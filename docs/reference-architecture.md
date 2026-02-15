# Reference Architecture

**Product:** Alpha Marvin / JurisAgent
**Last Updated:** February 15, 2026

---

## System Architecture Overview

```
                    LEGAL DOCUMENT INTELLIGENCE PLATFORM
                    ====================================

    ┌─────────────────────────────────────────────────────────────┐
    │                      USER LAYER                             │
    │                                                             │
    │  [Browser]  ──HTTPS──►  [nginx reverse proxy :443]          │
    │                              │                              │
    └──────────────────────────────┼──────────────────────────────┘
                                   │
    ┌──────────────────────────────┼──────────────────────────────┐
    │                      PRESENTATION LAYER                     │
    │                              │                              │
    │              ┌───────────────▼───────────────┐              │
    │              │     Next.js Frontend :3000     │              │
    │              │                               │              │
    │              │  • Chat Interface              │              │
    │              │  • Document Upload (drag/drop) │              │
    │              │  • Document Sidebar            │              │
    │              │  • Source Viewer                │              │
    │              │  • Confidence Indicators        │              │
    │              │  • Conversation History         │              │
    │              │                               │              │
    │              │  State: Zustand stores         │              │
    │              │  Style: Tailwind (Claude.ai)   │              │
    │              └───────────────┬───────────────┘              │
    │                              │ REST API                     │
    └──────────────────────────────┼──────────────────────────────┘
                                   │
    ┌──────────────────────────────┼──────────────────────────────┐
    │                      API LAYER                              │
    │                              │                              │
    │              ┌───────────────▼───────────────┐              │
    │              │    FastAPI Backend :8000       │              │
    │              │                               │              │
    │              │  Routes:                       │              │
    │              │  POST /api/ingest             │              │
    │              │  GET  /api/ingest/{id}/status  │              │
    │              │  POST /api/query              │              │
    │              │  GET  /api/documents          │              │
    │              │  DEL  /api/documents/{id}     │              │
    │              │  GET  /api/conversations      │              │
    │              │  POST /api/conversations      │              │
    │              │  GET  /api/health             │              │
    │              │  GET  /api/admin/stats        │              │
    │              │                               │              │
    │              │  Auth: Clerk JWT              │              │
    │              │  Middleware: CORS, logging     │              │
    │              └──────┬────────┬───────────────┘              │
    │                     │        │                              │
    └─────────────────────┼────────┼──────────────────────────────┘
                          │        │
         ┌────────────────┘        └────────────────┐
         │                                          │
    ┌────┼──────────────────────┐  ┌────────────────┼─────────────┐
    │    ▼  INGESTION PIPELINE  │  │    ▼  QA PIPELINE            │
    │                           │  │                              │
    │  Stage 1: PDF Parsing     │  │  Step 0: Document Filtering  │
    │    └─ Claude Vision OCR   │  │    └─ Auto-detect doc-      │
    │    └─ PyMuPDF text-first  │  │       specific queries      │
    │    └─ OCR Text Cleanup    │  │    └─ Scope search to doc   │
    │       (dehyphen, split-   │  │                              │
    │        word join, unicode)│  │  Step 1: Query Classification│
    │                           │  │    └─ SIMPLE | MULTI_HOP    │
    │  Stage 2: Deduplication   │  │       ENTITY | COMPARATIVE   │
    │    └─ SHA-256 hash check  │  │                              │
    │                           │  │  Step 2: Query Enrichment    │
    │  Stage 3: Chunking        │  │    └─ Synonym expansion     │
    │    └─ Legal-aware splits  │  │                              │
    │    └─ Article/Section/    │  │  Step 3: Hybrid Search       │
    │       Clause detection    │  │    ├─ Vector (Qdrant)       │
    │                           │  │    ├─ Synonym (expanded)    │
    │  Stage 4: Enrichment      │  │    └─ Graph (Neo4j 3-hop)  │
    │    └─ Claude context      │  │    └─ RRF Fusion (k=60)    │
    │       prefix generation   │  │                              │
    │                           │  │  Step 4: Reranking           │
    │  Stage 5: Extraction      │  │    └─ Cross-encoder         │
    │    └─ Entity extraction   │  │       (ms-marco-MiniLM)     │
    │    └─ Relationship mining │  │                              │
    │                           │  │  Step 5: Answer Generation   │
    │  Stage 6: Embedding       │  │    └─ Claude Opus 4.5       │
    │    └─ Voyage AI (1024d)   │  │    └─ SSE streaming         │
    │    └─ BAAI fallback(384d) │  │                              │
    │                           │  │  Step 6: Grounding           │
    │  Stage 7: Vector Store    │  │    └─ Confidence scoring    │
    │    └─ Qdrant upsert       │  │    └─ Hallucination risk    │
    │                           │  │    └─ Re-generate if low    │
    │  Stage 8: Database        │  │                              │
    │    └─ PostgreSQL store    │  │  Step 7: Citation Extract    │
    │                           │  │    └─ [Source N] mapping    │
    │  Stage 9: Knowledge Graph │  │                              │
    │    └─ Neo4j entities      │  └──────────────────────────────┘
    │    └─ Relationships       │
    │                           │
    └───────────────────────────┘

    ┌─────────────────────────────────────────────────────────────┐
    │                      DATA LAYER                             │
    │                                                             │
    │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
    │  │ PostgreSQL 16│  │   Qdrant     │  │   Neo4j 5    │     │
    │  │              │  │              │  │              │     │
    │  │ • Tenants    │  │ • Dense      │  │ • Entities   │     │
    │  │ • Users      │  │   vectors    │  │ • Relations  │     │
    │  │ • Documents  │  │   (1024-dim) │  │ • Chunks     │     │
    │  │ • Chunks     │  │ • COSINE     │  │ • Documents  │     │
    │  │ • Messages   │  │   distance   │  │ • 3-hop      │     │
    │  │ • Convos     │  │ • Tenant     │  │   traversal  │     │
    │  │              │  │   filtering  │  │ • Tenant     │     │
    │  │ Port: 5432   │  │ Port: 6333   │  │   isolation  │     │
    │  └──────────────┘  └──────────────┘  │ Port: 7687   │     │
    │                                      └──────────────┘     │
    └─────────────────────────────────────────────────────────────┘

    ┌─────────────────────────────────────────────────────────────┐
    │                    EXTERNAL SERVICES                        │
    │                                                             │
    │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
    │  │ Anthropic API│  │  Voyage AI   │  │  Clerk Auth  │     │
    │  │              │  │              │  │              │     │
    │  │ Claude Opus  │  │ voyage-3-    │  │ JWT          │     │
    │  │ 4.5          │  │ large        │  │ verification │     │
    │  │              │  │ (1024-dim)   │  │              │     │
    │  │ • Q&A        │  │              │  │ Dev mode:    │     │
    │  │ • Vision OCR │  │ Fallback:    │  │ bypass       │     │
    │  │ • Enrichment │  │ BAAI/bge-    │  │ available    │     │
    │  │ • Extraction │  │ small (local)│  │              │     │
    │  │ • Grounding  │  │ (384-dim)    │  │              │     │
    │  └──────────────┘  └──────────────┘  └──────────────┘     │
    │                                                             │
    └─────────────────────────────────────────────────────────────┘
```

---

## Data Flow: Document Ingestion

```
User uploads PDF
       │
       ▼
  ┌─────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
  │  Parse   │───►│  OCR     │───►│  Dedup   │───►│  Chunk   │───►│  Enrich  │
  │ (Vision/ │    │  Cleanup │    │ (SHA-256)│    │ (Legal)  │    │ (Claude) │
  │ PyMuPDF) │    │ (4-pass) │    │          │    │          │    │          │
  └─────────┘    └──────────┘    └──────────┘    └──────────┘    └──────────┘
                                                       │
       ┌───────────────────────────────────────────────┘
       ▼
  ┌─────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
  │ Extract  │───►│  Embed   │───►│  Store   │───►│  Graph   │
  │(Entities)│    │(Voyage)  │    │(Qdrant)  │    │ (Neo4j)  │
  └─────────┘    └──────────┘    └──────────┘    └──────────┘
                                       │               │
                                       ▼               ▼
                                  ┌──────────┐    ┌──────────┐
                                  │PostgreSQL│    │  Neo4j   │
                                  │(metadata)│    │ (graph)  │
                                  └──────────┘    └──────────┘
```

---

## Data Flow: Question Answering

```
User asks question
       │
       ▼
  ┌──────────┐    ┌──────────┐    ┌───────────────────────┐
  │ Classify  │───►│  Enrich  │───►│    Hybrid Search      │
  │  query    │    │  query   │    │                       │
  └──────────┘    └──────────┘    │  ┌─────┐ ┌─────────┐ │
                                  │  │Vector│ │ Synonym │ │
                                  │  │Search│ │ Search  │ │
                                  │  └──┬──┘ └────┬────┘ │
                                  │     │         │      │
                                  │  ┌──┴─────────┴──┐   │
                                  │  │  RRF Fusion   │   │
                                  │  └───────┬───────┘   │
                                  │          │           │
                                  │  ┌───────┴───────┐   │
                                  │  │  Graph Search │   │
                                  │  │  (Neo4j)      │   │
                                  │  └───────┬───────┘   │
                                  └──────────┼───────────┘
                                             │
                                             ▼
                                  ┌──────────────────┐
                                  │  Cross-Encoder   │
                                  │  Reranking       │
                                  └────────┬─────────┘
                                           │
                                           ▼
                                  ┌──────────────────┐
                                  │  Claude Opus 4.5 │
                                  │  Answer + Cite   │
                                  │  (SSE streaming) │
                                  └────────┬─────────┘
                                           │
                                           ▼
                                  ┌──────────────────┐
                                  │  Grounding       │
                                  │  Validation      │
                                  │                  │
                                  │  confidence: 0.92│
                                  │  risk: LOW       │
                                  │  grounded: true  │
                                  └────────┬─────────┘
                                           │
                                           ▼
                                  Answer + Citations
                                  + Confidence Score
                                  + Source Chunks
```

---

## Deployment Architecture

```
                    AZURE VM DEPLOYMENT
                    ===================

    Internet
       │
       │ HTTPS :443
       ▼
  ┌──────────────────────────────────────────┐
  │          Azure VM (Ubuntu 24.04)          │
  │                                          │
  │  ┌──────────────┐                        │
  │  │    nginx     │ reverse proxy          │
  │  │    :443      │ SSL termination        │
  │  └──────┬───────┘                        │
  │         │                                │
  │    ┌────┴────┐                           │
  │    │         │                           │
  │    ▼         ▼                           │
  │  ┌─────┐  ┌─────┐                       │
  │  │Next │  │Fast │                        │
  │  │ .js │  │ API │                        │
  │  │:3000│  │:8000│                        │
  │  └─────┘  └──┬──┘                        │
  │              │                           │
  │         Docker Compose                   │
  │    ┌─────┬───┴───┬─────┐                 │
  │    ▼     ▼       ▼     │                 │
  │  ┌────┐┌─────┐┌─────┐ │                 │
  │  │ PG ││Qdran││Neo4j│ │                 │
  │  │5432││ 6333││ 7687│ │                 │
  │  └────┘└─────┘└─────┘ │                 │
  │                        │                 │
  │  Volumes:              │                 │
  │  /data/postgres        │                 │
  │  /data/qdrant          │                 │
  │  /data/neo4j           │                 │
  └──────────────────────────────────────────┘

  Estimated monthly cost: ~$60 (B2s or B2ms VM)
```

---

## Multi-Tenant Data Isolation

```
  Tenant A                          Tenant B
     │                                 │
     ▼                                 ▼
  ┌──────────────────┐    ┌──────────────────┐
  │ PostgreSQL       │    │ PostgreSQL       │
  │ WHERE tenant_id  │    │ WHERE tenant_id  │
  │ = 'tenant_a'     │    │ = 'tenant_b'     │
  └──────────────────┘    └──────────────────┘

  ┌──────────────────┐    ┌──────────────────┐
  │ Qdrant           │    │ Qdrant           │
  │ filter: {        │    │ filter: {        │
  │   tenant_id:     │    │   tenant_id:     │
  │   'tenant_a'     │    │   'tenant_b'     │
  │ }                │    │ }                │
  └──────────────────┘    └──────────────────┘

  ┌──────────────────┐    ┌──────────────────┐
  │ Neo4j            │    │ Neo4j            │
  │ MATCH (n)        │    │ MATCH (n)        │
  │ WHERE n.tenant_id│    │ WHERE n.tenant_id│
  │ = 'tenant_a'     │    │ = 'tenant_b'     │
  └──────────────────┘    └──────────────────┘

  All three data stores enforce tenant isolation.
  No cross-tenant data leakage is possible.
```

---

## Technology Stack Summary

| Layer | Technology | Version | Purpose |
|-------|-----------|---------|---------|
| Reverse Proxy | nginx | latest | HTTPS, SSL termination |
| Frontend | Next.js | 14 | React UI framework |
| State Management | Zustand | latest | Client-side state |
| Styling | Tailwind CSS | 3 | Claude.ai-inspired design |
| Backend | FastAPI | 0.104+ | Async Python API |
| Authentication | Clerk | latest | JWT-based auth |
| LLM | Claude Opus 4.5 | claude-opus-4-5-20251101 | Q&A, Vision OCR, enrichment |
| Embeddings (Primary) | Voyage AI | voyage-3-large | 1024-dim dense vectors |
| Embeddings (Fallback) | BAAI/bge-small | en-v1.5 | 384-dim local fallback |
| Vector Store | Qdrant | latest | Semantic similarity search |
| Database | PostgreSQL | 16 | Document metadata, users, tenants |
| ORM | SQLAlchemy | 2.0+ | Async database access |
| Knowledge Graph | Neo4j | 5 Community | Entity relationships, multi-hop |
| PDF Rendering | PyMuPDF | 1.23+ | Page-to-image conversion |
| OCR Text Cleanup | Custom (NLTK) | — | Dehyphenation, split-word join, unicode fix |
| Reranking | Cross-Encoder | ms-marco-MiniLM | Result refinement |
| Containerization | Docker Compose | latest | Service orchestration |
| LLM Proxy | anthropic-max-router | — | Claude Max token proxy (port 3456) |
| Cloud | Azure VM | B2s/B2ms | Hosting |

---

## Additional Modules (Built in Phase 2)

| Module | Location | Purpose |
|--------|----------|---------|
| OCR Text Cleaner | `backend/ingestion/text_cleaner.py` | 4-pass cleanup for scanned PDF text (dehyphenation, split-word join, char fixes, unicode normalization) |
| Auto Document Filter | `backend/qa/engine.py` | LLM-based detection and scoping of doc-specific queries |
| Legal Citations | `backend/qa/legal_citations.py` | Bluebook-style citation formatting based on document type |
| Playbooks | `backend/qa/playbooks/` | Structured analysis frameworks (contract review, due diligence, regulatory analysis) |
| Timeline Extraction | `backend/qa/timeline.py` | Extract dates, deadlines, and milestones from documents |
| Comparison Engine | `backend/qa/comparison.py` | Side-by-side clause comparison across documents |
| Risk Assessment | `backend/qa/risk_assessment.py` | Document risk analysis with structured output |
| Grounding Validator | `backend/qa/grounding.py` | Per-answer confidence scoring and hallucination risk rating |

### LLM Retry Mechanism

All LLM API calls include robust retry logic:

- **12 retries** with exponential backoff + jitter
- Parses `Retry-After` header from rate limit responses
- Maximum 180-second wait between retries
- Handles 429 (rate limit) and 500/502/503 (server error) responses
