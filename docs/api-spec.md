# Alpha-Marvin-RAGBot API Specification

## Base URL
- Development: `http://localhost:8000/api`
- Production: `https://yourdomain.com/api`

## Authentication
All endpoints (except `/health`) require a Bearer JWT from Clerk.
In development mode, auth is bypassed with a default dev user.

## Endpoints

### Health
`GET /api/health`
- No auth required
- Returns: `{ "status": "healthy", "service": "alpha-marvin-ragbot", "version": "0.1.0" }`

### Document Upload
`POST /api/ingest`
- Auth: Required
- Body: `multipart/form-data` with `file` field
- Accepted: PDF, TXT, MD, HTML, CSV, PNG, JPG (max 100 MB)
- Returns: `{ "document_id": "uuid", "status": "complete|duplicate|failed", "title": "...", "page_count": N, "chunk_count": N }`

### Ingestion Status (SSE)
`GET /api/ingest/{document_id}/status`
- Auth: Required
- Returns: Server-Sent Events with progress updates

### Ask Question
`POST /api/query`
- Auth: Required
- Body: `{ "question": "string", "conversation_id": "uuid|null", "stream": false }`
- Returns: `{ "answer": "...", "sources": [...], "citations": [...], "confidence": 0.0-1.0, "hallucination_risk": "LOW|MEDIUM|HIGH", "is_grounded": bool }`

### List Documents
`GET /api/documents`
- Auth: Required (tenant-scoped)
- Returns: Array of document objects

### Delete Document
`DELETE /api/documents/{document_id}`
- Auth: Required
- Deletes document, chunks, and vectors

### Document Chunks
`GET /api/documents/{document_id}/chunks`
- Auth: Required
- Returns: Array of chunk objects with content preview

### Conversations
- `GET /api/conversations` — List conversations
- `POST /api/conversations` — Create conversation `{ "title": "string" }`
- `GET /api/conversations/{id}/messages` — Get messages
- `DELETE /api/conversations/{id}` — Delete conversation

### Admin
- `GET /api/admin/stats` — System statistics (admin only)
- `GET /api/user/me` — Current user info

### Invites
- `POST /api/invites` — Create invite (admin only) `{ "email": "string", "role": "member" }`
- `GET /api/invites` — List pending invites
