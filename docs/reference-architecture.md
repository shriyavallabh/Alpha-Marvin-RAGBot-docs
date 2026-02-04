# Reference Architecture

**Product:** Legal Document Intelligence Platform
**Last Updated:** February 4, 2026

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
    │  Stage 1: PDF Parsing     │  │  Step 1: Query Classification│
    │    └─ Claude Vision OCR   │  │    └─ SIMPLE | MULTI_HOP    │
    │                           │  │       ENTITY | COMPARATIVE   │
    │  Stage 2: Deduplication   │  │       OPEN_ENDED            │
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
  ┌─────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
  │  Parse   │───►│  Dedup   │───►│  Chunk   │───►│  Enrich  │
  │ (Vision) │    │ (SHA-256)│    │ (Legal)  │    │ (Claude) │
  └─────────┘    └──────────┘    └──────────┘    └──────────┘
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
| Reranking | Cross-Encoder | ms-marco-MiniLM | Result refinement |
| Containerization | Docker Compose | latest | Service orchestration |
| Cloud | Azure VM | B2s/B2ms | Hosting |
