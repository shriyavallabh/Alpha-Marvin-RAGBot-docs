# Product Roadmap

**Product:** Legal Document Intelligence Platform (working name — Alpha-Marvin-RAGBot)
**Last Updated:** February 4, 2026
**Owner:** Vallabh Pethkar

---

## Vision

An AI-powered document intelligence workspace that enables legal professionals to upload documents, ask natural language questions, and receive grounded, citation-backed answers — with zero tolerance for hallucination.

---

## Phase Overview

| Phase | Name | Timeline | Status |
|-------|------|----------|--------|
| Phase 1 | Foundation & Core Pipeline | Jan 2026 | COMPLETE |
| Phase 2 | Beta Release | Feb 1–15, 2026 | IN PROGRESS |
| Phase 3 | Production Agents | Feb 16 – Mar 31, 2026 | PLANNED |
| Phase 4 | Enterprise Features | Apr – Jun 2026 | PLANNED |

---

## Phase 1: Foundation & Core Pipeline (COMPLETE)

### Backend (43 Python files)
- [x] Swappable LLM client (Claude Opus 4.5 via Anthropic SDK)
- [x] 9-stage ingestion pipeline (Parse → Dedup → Chunk → Enrich → Extract → Embed → Vector → DB → Graph)
- [x] Claude Vision PDF parsing (handles scanned, watermarked, table-heavy documents)
- [x] Legal-aware chunking (Articles, Sections, Clauses, Schedules detection)
- [x] Contextual enrichment via Claude (Anthropic's 67% retrieval improvement method)
- [x] Entity & relationship extraction for knowledge graph
- [x] Dual embedding system (Voyage AI primary, BAAI/bge-small fallback)
- [x] Qdrant vector store with tenant isolation
- [x] Neo4j knowledge graph with 3-hop traversal
- [x] Hybrid 3-channel search (Vector + Synonym + Graph) with RRF fusion
- [x] Cross-encoder reranking (ms-marco-MiniLM-L-6-v2)
- [x] QA engine with query classification, enrichment, and streaming SSE
- [x] Grounding validation (confidence score, hallucination risk, is_grounded flag)
- [x] Citation extraction and formatting
- [x] 14 REST API endpoints with Clerk JWT authentication
- [x] Multi-tenant PostgreSQL database (SQLAlchemy 2.0 async)
- [x] Structured logging and error handling middleware

### Frontend (21 TypeScript files)
- [x] Next.js Claude.ai-style UI (#FAF9F5 background, #D97706 accent)
- [x] Chat interface with streaming answer display
- [x] Drag-and-drop file upload with progress indicators
- [x] Document management sidebar
- [x] Source viewer for cited chunks
- [x] Confidence/hallucination risk visualization
- [x] Conversation history with Zustand state management
- [x] Markdown rendering for answers

### Infrastructure
- [x] Docker Compose (PostgreSQL 16, Qdrant, Neo4j 5)
- [x] Azure VM deployment scripts
- [x] CI/CD pipeline configuration
- [x] 42/42 tests passing (pytest + pytest-bdd)

---

## Phase 2: Beta Release (Feb 1–15, 2026) — IN PROGRESS

### Technical Tasks
- [ ] Configure `.env` with production API keys
- [ ] Start Docker services and verify health checks
- [ ] First end-to-end test with real legal PDF
- [ ] Deploy to Azure VM with HTTPS (nginx reverse proxy)
- [ ] Populate evaluation suite with real legal questions
- [ ] Run evaluation, measure accuracy/relevance/citation metrics
- [ ] Bug fixes from E2E testing
- [ ] Performance testing (target: <10s per question, <60s per 100-page PDF)

### Business Artifacts
- [ ] Product roadmap (this document)
- [ ] Scope document with acceptance criteria
- [ ] RACI matrix for deployment models
- [ ] Market comparison and competitive analysis
- [ ] Reference architecture diagram
- [ ] Use case catalog
- [ ] Feature matrix (current vs. planned, free vs. paid)
- [ ] User manual for beta testers
- [ ] Differentiator analysis

### Beta Acceptance Criteria
1. Upload a 100+ page legal PDF and receive parsed, chunked, embedded results
2. Ask 10 different question types and receive grounded answers with citations
3. Knowledge graph populated with extracted entities and relationships
4. Hallucination risk flagged on answers with low grounding confidence
5. Two developers (India + US) can access the system simultaneously via HTTPS
6. Response latency under 10 seconds for single questions

---

## Phase 3: Production Agents (Feb 16 – Mar 31, 2026) — PLANNED

### Tier 2 Agent Capabilities (Tool-Use)
The LLM client already supports `tool_use()` and `atool_use()`. These tools extend the system from Q&A to an AI workspace:

| Priority | Tool | Description |
|----------|------|-------------|
| P0 | `generate_summary` | Document/multi-document summaries (1-page exec summary from 200-page regulation) |
| P0 | `compare_documents` | Side-by-side clause comparison across multiple contracts |
| P1 | `extract_structured_data` | Tables, CSV/JSON export of parties, dates, amounts |
| P1 | `generate_report` | Due diligence reports, compliance checks |
| P2 | `draft_communication` | Letters, emails referencing specific document clauses |

### Supporting Features
- [ ] Artifact display UI (rendered documents, tables, reports in chat)
- [ ] Export functionality (PDF, DOCX, CSV)
- [ ] Playbook/template system for common legal workflows
- [ ] Batch processing across document corpus

---

## Phase 4: Enterprise Features (Apr – Jun 2026) — PLANNED

### Tier 3 Workflow Agents
- [ ] Multi-step autonomous workflows (e.g., "Review all expiring contracts, flag risks, draft renewals")
- [ ] Scheduled/proactive document monitoring
- [ ] Contract redlining and revision tracking

### Enterprise Infrastructure
- [ ] SSO/SAML authentication
- [ ] Role-based access control (Admin, Attorney, Paralegal, Viewer)
- [ ] Audit logging for compliance
- [ ] API rate limiting and usage metering
- [ ] Multi-region deployment option

### Integrations
- [ ] Microsoft Word/Office add-in
- [ ] Email integration (Outlook, Gmail)
- [ ] Document management system connectors (iManage, NetDocuments)
- [ ] Webhook notifications for document processing events

---

## Key Metrics & Targets

| Metric | Beta Target | Production Target |
|--------|------------|-------------------|
| Answer accuracy | >85% | >92% |
| Retrieval relevance (top-5) | >90% | >95% |
| Citation correctness | >95% | >98% |
| Response latency | <10s | <5s |
| PDF processing (100 pages) | <60s | <30s |
| Uptime | 99% | 99.9% |
| Hallucination rate | <5% | <1% |

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

---

## Pending Decisions

1. **Product Name** — "Alpha-Marvin-RAGBot" is a working name; formal product name needed
2. **Target Vertical** — Pure legal vs. legal + healthcare + compliance
3. **Pricing Model** — Per user/month vs. per document vs. flat rate
4. **Primary Deployment Model** — SaaS vs. self-hosted for beta demo
