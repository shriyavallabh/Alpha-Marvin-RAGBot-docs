# Alpha-Marvin-RAGBot API Specification

**Last Updated:** February 15, 2026

## Base URL
- Development: `http://localhost:8000/api`
- Production: `https://yourdomain.com/api`

## Authentication
All endpoints (except `/health`) require a Bearer JWT from Clerk.
In development mode, auth is bypassed with a default dev user.

## Endpoints (21 routes)

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
- Returns: Server-Sent Events with stage progress updates

### Ask Question (SSE)
`POST /api/query`
- Auth: Required
- Body: `{ "question": "string", "conversation_id": "uuid|null", "stream": true }`
- Returns: Server-Sent Events:
    - `event: sources` — `{ "sources": [...] }` (retrieved chunks)
    - `event: text` — answer text tokens (streamed)
    - `event: grounding` — `{ "confidence": 0.0-1.0, "hallucination_risk": "LOW|MEDIUM|HIGH", "is_grounded": bool, "confidence_level": "GREEN|YELLOW|RED" }`

### List Documents
`GET /api/documents`
- Auth: Required (tenant-scoped)
- Returns: Array of document objects

### Delete Document
`DELETE /api/documents/{document_id}`
- Auth: Required
- Deletes document, chunks, vectors, and graph nodes

### Document Chunks
`GET /api/documents/{document_id}/chunks`
- Auth: Required
- Returns: Array of chunk objects with content preview

### Document Timeline
`GET /api/documents/{document_id}/timeline`
- Auth: Required
- Returns: Extracted dates, deadlines, and milestones from the document

### Document Risk Assessment
`POST /api/documents/{document_id}/risk-assessment`
- Auth: Required
- Returns: Structured risk analysis of the document

### Cross-Document Comparison
`POST /api/compare`
- Auth: Required
- Body: `{ "document_ids": ["uuid", "uuid"], "aspect": "string" }`
- Returns: Side-by-side clause comparison

### Conversations
- `GET /api/conversations` — List conversations (tenant-scoped)
- `POST /api/conversations` — Create conversation `{ "title": "string" }`
- `GET /api/conversations/{id}/messages` — Get messages
- `DELETE /api/conversations/{id}` — Delete conversation

### Admin
- `GET /api/admin/stats` — System statistics (admin only): doc count, chunk count, vector count, conversation count, user count
- `GET /api/user/me` — Current user info

### Invites
- `POST /api/invites` — Create invite (admin only) `{ "email": "string", "role": "member" }`
- `GET /api/invites` — List pending invites

### Playbooks
- `GET /api/playbooks` — List available playbooks (contract review, due diligence, regulatory analysis)
- `POST /api/playbooks/{playbook_id}/run` — Run a playbook against a document
- `GET /api/playbooks/runs/{run_id}` — Get playbook run results
