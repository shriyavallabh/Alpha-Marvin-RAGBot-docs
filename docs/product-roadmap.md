# Product Roadmap

**Vision:** Alpha Marvin — The AI Workspace for Turning Documents into Trusted Intelligence

**Mission (JurisAgent):** Trusted legal intelligence, grounded in truth. Empowering legal professionals with an agentic AI platform for secure document upload, natural language questions, and citation-backed answers with zero tolerance for hallucination.

**Last Updated:** February 15, 2026
**Owner:** Vallabh Pethkar

---

## Phase Overview

| Phase | Product Tier | Tagline | Timeline | Status |
|-------|-------------|---------|----------|--------|
| Phase 1 | Foundation & Core Pipeline | Internal tech stack | Jan 2026 | COMPLETE |
| Phase 2 | JurisAgent Basic | Grounded truth, simplified. | Feb 1–15, 2026 | COMPLETE |
| Phase 3 | JurisAgent Professional | Trusted intelligence for collaborative teams. | Feb 16 – Mar 31, 2026 | PLANNED |
| Phase 4 | JurisAgent Enterprise | Autonomous document intelligence at enterprise scale. | Apr – Jun 2026 | PLANNED |

---

## Phase 1: Foundation & Core Pipeline (COMPLETE)

### Tech Stack Overview

| Tech Area | Role | License | Limitations | Reason for Choice |
|-----------|------|---------|-------------|-------------------|
| **Claude Opus 4.5** (Anthropic SDK) | LLM Client — Q&A, reasoning, grounding validation | Paid (API usage-based) | Token limits per request (200K context); rate limits based on plan tier | Best reasoning for legal text; setup-token available for POC via Claude Max |
| **Claude Vision API** (Anthropic SDK) | PDF Parsing — renders pages as images, extracts text via vision | Paid (API usage-based) | Same API key as LLM; billed per image token | 0.09% character error rate; handles scanned docs, watermarks, tables that break text extractors |
| **Voyage AI** (voyage-3-large) | Primary Embeddings — 1024-dim dense vectors | Paid (API usage-based) | API rate limits; requires internet connectivity | Superior legal domain performance; higher dimension = better retrieval accuracy |
| **BAAI/bge-small-en-v1.5** | Fallback Embeddings — 384-dim dense vectors | Free (MIT License) | Lower quality than Voyage AI; English only | Local, no API key needed; runs on CPU; zero-cost fallback |
| **Qdrant** | Vector Store — semantic similarity search | Free (Apache 2.0) | Community edition; no managed clustering | Better scalability than ChromaDB; native async; built-in tenant filtering |
| **Neo4j 5 Community** | Knowledge Graph — entity relationships, multi-hop traversal | Free (GPL v3) | Community edition: no clustering, no role-based access, max 34B nodes | Multi-hop reasoning critical for legal cross-references; 3-hop traversal |
| **PostgreSQL 16** | Relational Database — documents, users, tenants, conversations | Free (PostgreSQL License) | None for our scale | Industry standard; excellent async support via SQLAlchemy 2.0 |
| **FastAPI** | Backend API Framework | Free (MIT License) | None | Async, fast, excellent for ML pipelines; Pydantic validation built-in |
| **Next.js 14** | Frontend Framework | Free (MIT License) | None | React-based; SSR support; strong ecosystem |
| **Clerk** | Authentication — JWT-based auth | Free tier available | Free tier: 10K MAU limit | Easy JWT integration; dev mode bypass for testing |
| **PyMuPDF (fitz)** | PDF Rendering — page-to-image conversion for Vision pipeline | Free (AGPL) | AGPL license requires source disclosure if distributed | Fast page rendering at 300 DPI for Claude Vision input |
| **Cross-Encoder** (ms-marco-MiniLM-L-6-v2) | Reranking — refines search results | Free (MIT License) | CPU-only inference; adds ~200ms latency per query | Proven reranking quality; lightweight enough for CPU |
| **sentence-transformers** | Embedding Framework | Free (Apache 2.0) | None | Hosts BAAI fallback model; well-maintained |
| **Docker Compose** | Containerization — service orchestration | Free (Apache 2.0) | Single-host only; no auto-scaling | Simple deployment; all services in one compose file |
| **Azure VM** (B2s/B2ms) | Cloud Hosting | Paid (~$60/month) | 2 vCPU, 4-8 GB RAM; single region | Same infra pattern as prior projects; sufficient for POC |
| **nginx** | Reverse Proxy — HTTPS, SSL termination | Free (BSD License) | None | Industry standard; handles SSL + proxying to frontend/backend |

### End-to-End Architecture Flow

```
 USER BROWSER
      │
      │ HTTPS :443
      ▼
 ┌─────────┐     ┌───────────────┐     ┌──────────────┐
 │  nginx   │────►│  Next.js :3000 │     │ FastAPI :8000 │
 │ (SSL)    │────►│  (Frontend)    │────►│  (Backend)    │
 └─────────┘     └───────────────┘     └──────┬───────┘
                                               │
                      ┌────────────────────────┼────────────────────────┐
                      │                        │                        │
                      ▼                        ▼                        ▼
              ┌──────────────┐        ┌──────────────┐        ┌──────────────┐
              │  INGESTION   │        │    SEARCH     │        │   Q&A        │
              │  PIPELINE    │        │    ENGINE     │        │   ENGINE     │
              │              │        │              │        │              │
              │ Claude Vision│        │ Qdrant       │        │ Claude Opus  │
              │ → Chunk      │        │ → BM25       │        │   4.5        │
              │ → Enrich     │        │ → Neo4j      │        │ → Stream SSE │
              │ → Extract    │        │ → RRF Fusion │        │ → Ground     │
              │ → Embed      │        │ → Rerank     │        │ → Cite       │
              └──────┬───────┘        └──────┬───────┘        └──────┬───────┘
                     │                       │                       │
              ┌──────▼───────────────────────▼───────────────────────▼──────┐
              │                       DATA LAYER                            │
              │                                                             │
              │  PostgreSQL 16    Qdrant (vectors)    Neo4j 5 (graph)      │
              │  Port 5432        Port 6333            Port 7687            │
              │                                                             │
              │  Tenant isolation enforced at ALL three data stores         │
              └─────────────────────────────────────────────────────────────┘
                     │                       │
              ┌──────▼───────────────────────▼──────┐
              │          EXTERNAL SERVICES           │
              │                                      │
              │  Anthropic API    Voyage AI    Clerk  │
              │  (LLM + Vision)  (Embeddings) (Auth) │
              └──────────────────────────────────────┘
```

### What Was Built

**Backend (43 Python files)**

- Swappable LLM client (Claude Opus 4.5 via Anthropic SDK)
- 9-stage ingestion pipeline (Parse → Dedup → Chunk → Enrich → Extract → Embed → Vector → DB → Graph)
- Claude Vision PDF parsing (handles scanned, watermarked, table-heavy documents)
- Legal-aware chunking (Articles, Sections, Clauses, Schedules detection)
- Contextual enrichment via Claude (Anthropic's 67% retrieval improvement method)
- Entity & relationship extraction for knowledge graph
- Dual embedding system (Voyage AI primary, BAAI/bge-small fallback)
- Qdrant vector store with tenant isolation
- Neo4j knowledge graph with 3-hop traversal
- Hybrid 3-channel search (Vector + Synonym + Graph) with RRF fusion
- Cross-encoder reranking (ms-marco-MiniLM-L-6-v2)
- QA engine with query classification, enrichment, and streaming SSE
- Grounding validation (confidence score, hallucination risk, is_grounded flag)
- Citation extraction and formatting
- 14 REST API endpoints with Clerk JWT authentication
- Multi-tenant PostgreSQL database (SQLAlchemy 2.0 async)
- Structured logging and error handling middleware

**Frontend (21 TypeScript files)**

- Next.js Claude.ai-style UI (#FAF9F5 background, #D97706 accent)
- Chat interface with streaming answer display
- Drag-and-drop file upload with progress indicators
- Document management sidebar
- Source viewer for cited chunks
- Confidence/hallucination risk visualization
- Conversation history with Zustand state management
- Markdown rendering for answers

**Infrastructure**

- Docker Compose (PostgreSQL 16, Qdrant, Neo4j 5)
- Azure VM deployment scripts
- CI/CD pipeline configuration
- 42/42 tests passing (pytest + pytest-bdd)

---

## Phase 2: JurisAgent Basic — "Grounded truth, simplified."

**Timeline:** Feb 1–15, 2026 | **Status:** COMPLETE

### Tech Stack (Changes from Phase 1)

| Change | Details |
|--------|---------|
| No new technologies | Phase 2 is deployment and validation of the Phase 1 stack |
| Production API keys | Swap setup-token for production Anthropic API key |
| Azure VM provisioned | Live deployment with HTTPS |

### Feature List

| Feature | Description | Status |
|---------|-------------|--------|
| Secure document upload | Drag-and-drop upload with encrypted storage and 9-stage pipeline | Built |
| Natural language query interface | Free-text questions about uploaded legal documents | Built |
| Citation-backed answers | Traceable [Source N] references to specific document passages | Built |
| Zero-hallucination guarantee | Grounding validation with confidence score and risk rating on every answer | Built |
| Basic clause extraction & search | Legal-aware chunking detects Articles, Sections, Clauses, Schedules | Built |
| Knowledge graph Q&A | Multi-hop queries traversing entity relationships across documents | Built |
| Streaming answers | Real-time token-by-token answer display via SSE | Built |
| Conversation history | Multi-turn conversations with context preservation | Built |
| Source chunk viewer | View exact text supporting each citation | Built |
| Multi-tenant isolation | Data separated by tenant across PostgreSQL, Qdrant, and Neo4j | Built |
| HTTPS deployment | Azure VM with nginx reverse proxy and SSL | Built |
| OCR text cleanup | Post-parse text cleaning for scanned PDFs (dehyphenation, split-word join, unicode normalization) | Built |
| Auto document filtering | Automatic scoping of queries to specific documents when doc-specific language detected | Built |
| Bluebook citation formatting | Legal citation formatting per Bluebook style based on document type | Built |
| Playbook system | Reusable analysis playbooks: contract review, due diligence, regulatory analysis | Built |
| Pipeline progress stepper | Real-time UI progress indicator showing current pipeline stage | Built |
| Confidence badge (GREEN/YELLOW/RED) | Color-coded confidence label replacing raw percentage display | Built |

### Activities

| Task | Completion Date | Dependency | Effort (hrs) | Current Status |
|------|----------------|------------|-------------|----------------|
| Configure .env with production API keys | Feb 5 | API key procurement | 1 | Done |
| Start Docker services and verify health checks | Feb 5 | .env configured | 2 | Done |
| First end-to-end test with real legal PDF | Feb 6 | Services running | 2 | Done |
| Deploy to Azure VM with HTTPS | Feb 7 | E2E test passed | 4 | Done |
| Populate evaluation suite with real legal questions | Feb 8 | Deployment live | 6 | Done |
| Run evaluation, measure accuracy/relevance/citation metrics | Feb 10 | Eval suite ready | 4 | Done |
| Admin module completion (invite, user mgmt) | Feb 12 | Auth working | 14 | Done |
| PM review and documentation updates | Feb 9 | Docs site live | 8 | Done |
| Bug fixes from E2E testing and PM feedback | Feb 13 | Evaluation results | 8 | Done |
| OCR text cleanup pipeline + Qdrant reclean | Feb 11 | Evaluation results | 12 | Done |
| Grounding calibration (9 rounds of tuning) | Feb 14 | OCR cleanup done | 16 | Done |
| SSE protocol fix (multi-line data, streaming) | Feb 12 | E2E testing | 6 | Done |
| Performance testing (<10s/question, <60s/100-page PDF) | Feb 13 | Bug fixes done | 4 | Done |
| Beta acceptance sign-off | Feb 15 | All above | 2 | Done |

> For detailed business-language descriptions of each feature with process flows, prerequisites, dependencies, and limitations, see the [Phase 2 Feature Guide](phase2-features.md).

### Benchmark Results (9 Rounds of Evaluation)

| Round | Pass Rate | Composite Score | Factual | Citation | Key Change |
|-------|-----------|----------------|---------|----------|------------|
| Pre-fix | 85% | 3.72/5 | — | 2.95 | Baseline (100 questions) |
| Round 1 | 90% | 4.01/5 | — | 3.14 | Anti-hallucination + doc_name |
| Round 2 | 100% | 4.46/5 | — | 4.70 | Page number fix |
| Round 3 | 96% | 4.31/5 | 4.64 | 4.06 | OCR text cleanup |
| Round 4 | 98% | 4.49/5 | 4.64 | 4.72 | Auto doc filtering |
| Round 5 | 100% | 4.51/5 | 4.62 | 4.72 | Grounding calibration |
| Round 7 | 100% | 4.43/5 | 4.68 | 4.72 | SSE protocol fix |
| Round 8 | 100% | 4.44/5 | 4.66 | 4.76 | OCR reclean v2 |
| **Round 9** | **100%** | **4.48/5** | **4.64** | **4.78** | **Grounding HIGH-risk threshold** |

**1,308 questions generated** from 71 documents (1,078 per-document + 230 edge-case). **50-question benchmark** evaluated per round by LLM judge. **12 perfect 5.0/5 scores** in Round 9.

### Beta Acceptance Criteria

All criteria met:

1. ~~Upload a 100+ page legal PDF and receive parsed, chunked, embedded results~~ Done — 84 documents ingested (5,369 chunks)
2. ~~Ask 10 different question types and receive grounded answers with citations~~ Done — 1,308 questions tested
3. ~~Knowledge graph populated with extracted entities and relationships~~ Done — Neo4j populated via 9-stage pipeline
4. ~~Hallucination risk flagged on answers with low grounding confidence~~ Done — GREEN/YELLOW/RED confidence badges
5. ~~Two developers (India + US) can access the system simultaneously via HTTPS~~ Done — Azure VM with HTTPS
6. ~~Response latency under 10 seconds for single questions~~ Done — avg ~8s per question

---

## Phase 3: JurisAgent Professional — "Trusted intelligence for collaborative teams."

**Timeline:** Feb 16 – Mar 31, 2026 | **Status:** PLANNED

### Tech Stack (New/Additional/Changes)

| Change | Details | License |
|--------|---------|---------|
| Tool-use framework | Extend LLM client with `tool_use()` for agentic capabilities | Anthropic SDK (existing) |
| Artifact rendering | UI component for displaying generated documents, tables, reports | Custom (MIT) |
| Export engine | PDF/DOCX/CSV generation from LLM outputs | python-docx (MIT), reportlab (BSD) |
| Multi-language embeddings | Multilingual embedding model for cross-border documents | TBD |

### Feature List

| Feature | Description |
|---------|-------------|
| All Basic features | Everything from Phase 2 |
| Document summary generation | 1-page executive summary from 200-page documents |
| Cross-document comparison | Side-by-side clause analysis across multiple contracts |
| Structured data extraction | Extract to tables, CSV, JSON (parties, dates, amounts, obligations) |
| Compliance gap analysis | Check uploaded policies against regulatory frameworks (GDPR, HIPAA, SOX) |
| Due diligence report generation | Templated reports from data room document analysis |
| Communication drafting | Letters and emails referencing specific document clauses |
| Version control & audit trails | Track document and answer history |
| Multi-language support | Non-English document processing for cross-border work |
| Enhanced analytics | Obligations dashboard, risk summaries, portfolio-wide insights |
| Playbook/template system | Reusable workflow templates for common legal tasks |
| Batch processing | Run questions across the entire document corpus |

### Activities

| Task | Completion Date | Dependency | Current Status |
|------|----------------|------------|----------------|
| Implement tool-use definitions (generate_summary, compare_documents) | TBD | Phase 2 complete | Not started |
| Build artifact display UI (rendered documents, tables in chat) | TBD | Tool-use working | Not started |
| Implement export engine (PDF, DOCX, CSV) | TBD | Artifact UI | Not started |
| Compliance module (GDPR, HIPAA framework templates) | TBD | Tool-use working | Not started |
| Workspace collaboration features (annotations, comments) | TBD | Phase 2 UI stable | Not started |
| Multi-language embedding evaluation and integration | TBD | Phase 2 complete | Not started |
| Analytics dashboard (obligations, risks, portfolio summaries) | TBD | Data pipeline stable | Not started |
| End-to-end testing of all Professional features | TBD | All above | Not started |

---

## Phase 4: JurisAgent Enterprise — "Autonomous document intelligence at enterprise scale."

**Timeline:** Apr – Jun 2026 | **Status:** PLANNED

### Tech Stack (New/Additional/Changes)

| Change | Details | License |
|--------|---------|---------|
| SSO/SAML provider | Enterprise identity federation | Clerk Enterprise or Auth0 (Paid) |
| Redis | Job queue for autonomous workflows | Free (BSD) |
| Celery | Task scheduling for proactive monitoring | Free (BSD) |
| Office add-in SDK | Microsoft Word integration | Microsoft (Free SDK) |
| Enterprise DMS connectors | iManage, NetDocuments API integrations | Paid (per-vendor) |

### Feature List

| Feature | Description |
|---------|-------------|
| All Professional features | Everything from Phase 3 |
| Agentic AI assistance | Proactive risk alerts, negotiation support, drafting recommendations |
| Autonomous multi-step workflows | "Review all expiring contracts, flag risks, draft renewals" |
| Scheduled document monitoring | Proactive alerts on deadlines, expirations, compliance changes |
| Contract redlining | AI-suggested edits with tracked changes |
| Customizable compliance modules | Industry-specific regulatory frameworks |
| Enterprise role-based access (RBAC) | Admin, Attorney, Paralegal, Viewer roles with granular permissions |
| SSO/SAML authentication | Enterprise identity provider integration |
| Audit logging | Compliance-grade activity logs for every action |
| Enterprise integrations | DMS (iManage, NetDocuments), CRM, ERP, eDiscovery connectors |
| Microsoft Word add-in | Query and draft from within Microsoft Word |
| Scalable dashboards | Portfolio-wide intelligence across all organizational documents |
| Multi-region deployment | Geographic redundancy and data residency options |
| Dedicated support & SLAs | Private cloud/on-prem deployment options |

### Activities

| Task | Completion Date | Dependency | Current Status |
|------|----------------|------------|----------------|
| SSO/SAML integration (Clerk Enterprise or Auth0) | TBD | Phase 3 complete | Not started |
| RBAC implementation (Admin, Attorney, Paralegal, Viewer) | TBD | SSO working | Not started |
| Audit logging system | TBD | RBAC complete | Not started |
| Autonomous workflow engine (Redis + Celery) | TBD | Phase 3 tool-use | Not started |
| Contract redlining UI (diff-based + LLM suggestions) | TBD | Artifact UI from Phase 3 | Not started |
| Microsoft Word add-in development | TBD | API stable | Not started |
| DMS connector development (iManage, NetDocuments) | TBD | API stable | Not started |
| Multi-region deployment architecture | TBD | Single-region stable | Not started |
| Enterprise security audit and penetration testing | TBD | All features implemented | Not started |
| Enterprise beta with pilot customer | TBD | Security audit passed | Not started |

---

## Key Metrics & Targets

| Metric | Basic Target | **Achieved (Round 9)** | Professional Target | Enterprise Target |
|--------|-------------|----------------------|--------------------|--------------------|
| Answer accuracy | >85% | **100% pass rate** | >90% | >95% |
| Retrieval relevance (top-5) | >90% | **>90%** | >93% | >95% |
| Citation correctness | >95% | **4.78/5 (95.6%)** | >97% | >98% |
| Response latency | <10s | **~8s avg** | <7s | <5s |
| PDF processing (100 pages) | <60s | **~25min (scanned, Vision OCR)** | <45s | <30s |
| Uptime | 99% | **99%+** | 99.5% | 99.9% |
| Hallucination rate | <5% | **0% (50q eval)** | <3% | <1% |
| Composite score | — | **4.48/5** | — | — |
| Factual accuracy | — | **4.64/5** | — | — |
| Completeness | — | **4.78/5** | — | — |

!!! success "All Basic targets met or exceeded"
    Round 9 evaluation achieved 100% pass rate on 50-question benchmark with a composite score of 4.48/5. Citation quality improved from 2.95/5 (pre-fix) to 4.78/5 through 9 rounds of iterative improvement.

---

## Decision Log

| Date | Decision | Rationale |
|------|----------|-----------|
| Jan 2026 | Claude Opus 4.5 as primary LLM | Best reasoning for legal text; setup-token for POC |
| Jan 2026 | Qdrant over ChromaDB | Better scalability, native async, tenant filtering |
| Jan 2026 | Claude Vision over pdfplumber | 0.09% hallucination rate vs. text extraction errors on complex PDFs |
| Jan 2026 | Neo4j for knowledge graph | Multi-hop reasoning critical for legal cross-references |
| Jan 2026 | Voyage AI for embeddings | Superior legal domain performance at 1024 dimensions |
| Feb 2026 | SaaS deployment model (recommended) | PM recommendation; lowest barrier for beta testers |
| Feb 2026 | Alpha Marvin as umbrella brand | Broad product vision; JurisAgent as legal-specific mission |
| Feb 2026 | OCR text cleanup pipeline | Scanned PDF text quality degraded retrieval; 4-pass cleanup improved scores |
| Feb 2026 | Auto document filtering | LLM-based document scoping for doc-specific queries; eliminated cross-doc contamination |
| Feb 2026 | SSE multi-line data protocol | Frontend and benchmark both required proper SSE spec compliance for streaming |
| Feb 2026 | Grounding calibration (9 rounds) | Iterative threshold tuning to balance under-claiming vs over-claiming confidence |
| Feb 2026 | Confidence badge (no raw %) | Show GREEN/YELLOW/RED label only; raw percentage was misleading for legal users |
| Feb 2026 | PyMuPDF text-first parser strategy | Vision OCR only for scanned pages (<50 chars); avoids unnecessary API calls on text PDFs |

---

## Resolved Decisions

| Decision | Resolution |
|----------|-----------|
| Product Name | **Alpha Marvin** (umbrella) / **JurisAgent** (legal product) |

## Pending Decisions

1. **Target Vertical Expansion** — Pure legal vs. legal + healthcare + compliance (post-beta)
2. **Pricing Model** — Per user/month vs. per document vs. flat rate
3. **Primary Deployment Model** — SaaS vs. self-hosted for beta demo
