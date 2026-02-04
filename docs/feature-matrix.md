# Feature Matrix

**Product:** Legal Document Intelligence Platform
**Last Updated:** February 4, 2026

---

## Legend

| Symbol | Meaning |
|--------|---------|
| BUILT | Implemented and tested |
| PLANNED | In roadmap, not yet built |
| DEFERRED | Considered but not prioritized |
| FREE | Available in all tiers |
| PAID | Available in paid tiers only |
| ENTERPRISE | Available in enterprise tier only |

---

## Core Features

### Document Ingestion

| Feature | Status | Tier | Details |
|---------|--------|------|---------|
| PDF upload (text-based) | BUILT | FREE | Standard text PDF parsing |
| PDF upload (scanned/image) | BUILT | FREE | Claude Vision OCR extraction |
| Image upload (PNG, JPG) | BUILT | FREE | Direct image text extraction |
| Text file upload (TXT, MD) | BUILT | FREE | Plain text ingestion |
| HTML upload | BUILT | FREE | HTML content extraction |
| CSV upload | BUILT | FREE | Structured data ingestion |
| Drag-and-drop upload UI | BUILT | FREE | Browser-based file upload |
| Upload progress tracking | BUILT | FREE | Real-time stage progress via SSE |
| Duplicate detection | BUILT | FREE | SHA-256 content hash |
| Batch upload (multiple files) | PLANNED | PAID | Upload and process multiple files at once |
| DOCX/PPTX support | PLANNED | PAID | Microsoft Office format parsing |
| Email (.eml/.msg) ingestion | DEFERRED | PAID | Email content + attachment processing |
| OCR for handwritten text | DEFERRED | ENTERPRISE | Handwriting recognition |

### Document Processing

| Feature | Status | Tier | Details |
|---------|--------|------|---------|
| Legal-aware chunking | BUILT | FREE | Article/Section/Clause/Schedule detection |
| Contextual enrichment | BUILT | FREE | Claude adds context prefix per chunk |
| Entity extraction | BUILT | FREE | Parties, dates, clauses, legal concepts |
| Relationship extraction | BUILT | FREE | Cross-references, definitions, obligations |
| Dual embedding generation | BUILT | FREE | Voyage AI (1024d) + BAAI fallback (384d) |
| Table extraction | BUILT | FREE | Via Claude Vision rendering |
| Section hierarchy preservation | BUILT | FREE | Nested headings tracked in metadata |
| Custom chunking rules | PLANNED | ENTERPRISE | User-defined split points per document type |
| Language detection | DEFERRED | PAID | Auto-detect document language |
| Multi-language support | DEFERRED | ENTERPRISE | Non-English document processing |

### Search & Retrieval

| Feature | Status | Tier | Details |
|---------|--------|------|---------|
| Vector similarity search | BUILT | FREE | Qdrant COSINE distance search |
| BM25 keyword search | BUILT | FREE | Synonym-expanded keyword matching |
| Knowledge graph traversal | BUILT | FREE | Neo4j 3-hop multi-hop queries |
| Hybrid search (3-channel RRF) | BUILT | FREE | Vector + Synonym + Graph fusion |
| Cross-encoder reranking | BUILT | FREE | ms-marco-MiniLM result refinement |
| Query classification | BUILT | FREE | Auto-detect query type for optimal strategy |
| Query enrichment | BUILT | FREE | Synonym and concept expansion |
| Tenant-scoped search | BUILT | FREE | Results filtered by tenant_id |
| Document-scoped search | BUILT | FREE | Search within specific documents |
| Saved searches | PLANNED | PAID | Save and re-run frequent queries |
| Search analytics | PLANNED | ENTERPRISE | Query patterns and usage metrics |

### Question Answering

| Feature | Status | Tier | Details |
|---------|--------|------|---------|
| Natural language Q&A | BUILT | FREE | Free-text questions about uploaded documents |
| Streaming answers (SSE) | BUILT | FREE | Real-time token-by-token display |
| Citation extraction | BUILT | FREE | [Source N] mapped to document/page |
| Grounding validation | BUILT | FREE | Confidence score per answer |
| Hallucination risk rating | BUILT | FREE | LOW/MEDIUM/HIGH risk classification |
| Multi-turn conversations | BUILT | FREE | Context preserved across questions |
| Conversation history | BUILT | FREE | Browse and resume past conversations |
| Source chunk viewer | BUILT | FREE | View exact text that supports each citation |
| Answer regeneration | BUILT | FREE | Re-generate if grounding is low |
| Answer export (copy/share) | PLANNED | FREE | Copy answer with citations |
| Answer comparison | PLANNED | PAID | Compare answers from different document sets |

### Knowledge Graph

| Feature | Status | Tier | Details |
|---------|--------|------|---------|
| Entity storage | BUILT | FREE | Parties, concepts, dates, clauses |
| Relationship mapping | BUILT | FREE | MENTIONS, REFERENCES, DEFINED_IN |
| Multi-hop traversal (3 hops) | BUILT | FREE | Follow entity relationships across documents |
| Tenant isolation | BUILT | FREE | Graph nodes scoped per tenant |
| Graph visualization | PLANNED | PAID | Interactive entity relationship diagram |
| Custom entity types | PLANNED | ENTERPRISE | Define domain-specific entity schemas |
| Graph export | PLANNED | ENTERPRISE | Export graph data (JSON-LD, RDF) |

---

## Production Agent Features (Phase 3)

| Feature | Status | Tier | Details |
|---------|--------|------|---------|
| Document summary generation | PLANNED | PAID | 1-page exec summary from 200-page doc |
| Cross-document comparison | PLANNED | PAID | Side-by-side clause analysis |
| Structured data extraction | PLANNED | PAID | Extract to table/CSV/JSON |
| Due diligence report generation | PLANNED | ENTERPRISE | Templated DD reports |
| Compliance gap analysis | PLANNED | ENTERPRISE | Check docs against regulations |
| Communication drafting | PLANNED | ENTERPRISE | Letters/emails referencing doc clauses |
| Contract redlining | PLANNED | ENTERPRISE | Suggested edits with tracked changes |
| Playbook/template system | PLANNED | PAID | Reusable workflow templates |
| Batch processing | PLANNED | ENTERPRISE | Process questions across entire corpus |

---

## User Interface

| Feature | Status | Tier | Details |
|---------|--------|------|---------|
| Chat interface (Claude.ai style) | BUILT | FREE | Warm off-white theme, centered chat |
| Document upload with drag-drop | BUILT | FREE | Progress indicators |
| Document management sidebar | BUILT | FREE | List, delete uploaded documents |
| Source viewer | BUILT | FREE | View cited chunks |
| Confidence indicator | BUILT | FREE | Visual hallucination risk display |
| Markdown rendering | BUILT | FREE | Formatted answers with headers, lists |
| Conversation sidebar | BUILT | FREE | Browse conversation history |
| Settings page | BUILT | FREE | User preferences |
| Admin panel | BUILT | ENTERPRISE | System stats, user management |
| Dark mode | PLANNED | FREE | Dark theme option |
| Artifact viewer (reports, tables) | PLANNED | PAID | Display generated documents in chat |
| PDF annotation viewer | PLANNED | PAID | Highlight cited sections in original PDF |
| Mobile responsive | PLANNED | FREE | Responsive layout for mobile browsers |

---

## Infrastructure & Operations

| Feature | Status | Tier | Details |
|---------|--------|------|---------|
| Docker Compose deployment | BUILT | FREE | PostgreSQL + Qdrant + Neo4j |
| Azure VM deployment scripts | BUILT | FREE | Ubuntu 24.04 setup |
| HTTPS via nginx | BUILT | FREE | SSL termination, reverse proxy |
| Health check endpoint | BUILT | FREE | /api/health service status |
| Clerk JWT authentication | BUILT | FREE | Token-based auth |
| Multi-tenant data isolation | BUILT | FREE | DB + Vector + Graph isolation |
| CI/CD pipeline | BUILT | FREE | Automated testing and deployment |
| Structured logging | BUILT | FREE | JSON-formatted application logs |
| Error handling middleware | BUILT | FREE | Consistent error responses |
| API rate limiting | PLANNED | PAID | Per-tenant request limits |
| Usage metering | PLANNED | PAID | Track queries, documents, tokens |
| Audit logging | PLANNED | ENTERPRISE | Compliance-grade activity logs |
| SSO/SAML authentication | PLANNED | ENTERPRISE | Enterprise identity providers |
| Role-based access control | PLANNED | ENTERPRISE | Admin, Attorney, Paralegal, Viewer |
| Multi-region deployment | DEFERRED | ENTERPRISE | Geographic redundancy |
| Backup and disaster recovery | PLANNED | ENTERPRISE | Automated backups, point-in-time recovery |

---

## Integration & API

| Feature | Status | Tier | Details |
|---------|--------|------|---------|
| REST API (14 endpoints) | BUILT | FREE | Full CRUD for documents, queries, conversations |
| SSE streaming | BUILT | FREE | Real-time answer and progress streaming |
| Webhook notifications | PLANNED | PAID | Document processing and query events |
| Python SDK | PLANNED | PAID | Client library for API integration |
| Word/Office add-in | DEFERRED | ENTERPRISE | Query from within Microsoft Word |
| Email integration | DEFERRED | ENTERPRISE | Process documents from email |
| DMS connectors | DEFERRED | ENTERPRISE | iManage, NetDocuments integration |
| Zapier/Make integration | DEFERRED | PAID | No-code workflow connections |

---

## Testing & Quality

| Feature | Status | Tier | Details |
|---------|--------|------|---------|
| Unit tests (pytest) | BUILT | N/A | 42/42 passing |
| BDD tests (pytest-bdd) | BUILT | N/A | Feature-level testing |
| API endpoint tests | BUILT | N/A | Full endpoint coverage |
| 1000-question evaluation suite | PLANNED | N/A | Accuracy/relevance/citation metrics |
| Automated evaluation runner | BUILT | N/A | Batch processing with metrics |
| LLM call mocking | BUILT | N/A | Tests never hit real API |

---

## Pricing Tier Summary (Proposed)

| Tier | Target User | Included | Estimated Price |
|------|------------|----------|-----------------|
| **Free / Self-Hosted** | Solo practitioners, developers | Core Q&A, upload, search, graph, grounding | Infrastructure cost only (~$60/mo) |
| **Paid** | Small firms, teams (2-10 users) | Free + production agents, batch, analytics, SDK | TBD (target: $49-99/user/mo) |
| **Enterprise** | Large firms, corporations | Paid + SSO, RBAC, audit logs, custom entities, DMS | TBD (custom pricing) |

*Note: Pricing model (per user, per document, or flat rate) is a pending decision.*
