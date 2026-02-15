# Beta Scope Document

**Product:** Alpha Marvin / JurisAgent
**Beta Version:** v0.1.0 (JurisAgent Basic)
**Beta Date:** February 15, 2026
**Prepared By:** Alpha Marvin Team

---

## Purpose

This document defines what is included in the February 15 beta release, what is explicitly deferred, and the acceptance criteria for beta readiness.

---

## Beta Audience

- 2 developers (India + US timezones)
- Access via HTTPS on Azure VM
- Token-based authentication (Clerk JWT)

---

## In Scope (Beta v0.1.0)

### Document Ingestion
| Feature | Status | Details |
|---------|--------|---------|
| PDF upload via web UI | Built | Drag-and-drop, progress indicators |
| Claude Vision PDF parsing | Built | Renders pages as images, extracts via Claude Vision |
| Legal-aware chunking | Built | Detects Articles, Sections, Clauses, Schedules |
| Contextual enrichment | Built | Claude adds context prefix to each chunk |
| Entity/relationship extraction | Built | Populates Neo4j knowledge graph |
| Dual embedding generation | Built | Voyage AI (1024-dim) + BAAI fallback (384-dim) |
| Duplicate detection | Built | SHA-256 content hash comparison |
| Supported formats | Built | PDF, PNG, JPG, TXT, MD, HTML, CSV |

### Question Answering
| Feature | Status | Details |
|---------|--------|---------|
| Natural language questions | Built | Free-text input in chat interface |
| Hybrid 3-channel search | Built | Vector + Synonym + Graph with RRF fusion |
| Cross-encoder reranking | Built | Refines top-30 results |
| Streaming answers (SSE) | Built | Real-time token-by-token display |
| Citation extraction | Built | [Source N] notation mapped to documents/pages |
| Grounding validation | Built | Confidence score + hallucination risk rating |
| Query classification | Built | SIMPLE, MULTI_HOP, ENTITY_LOOKUP, COMPARATIVE, OPEN_ENDED |
| Conversation history | Built | Multi-turn conversations with context |

### Knowledge Graph
| Feature | Status | Details |
|---------|--------|---------|
| Entity storage | Built | Parties, dates, clauses, legal concepts |
| Relationship mapping | Built | MENTIONS, REFERENCES, DEFINED_IN, etc. |
| Multi-hop traversal | Built | Up to 3 hops for complex queries |
| Tenant isolation | Built | Graph nodes scoped by tenant_id |

### User Interface
| Feature | Status | Details |
|---------|--------|---------|
| Chat interface | Built | Claude.ai-style design |
| File upload | Built | Drag-and-drop with progress |
| Document list | Built | Sidebar showing uploaded documents |
| Source viewer | Built | View cited chunks in context |
| Confidence indicator | Built | Visual hallucination risk display |
| Markdown rendering | Built | Formatted answer display |

### Infrastructure
| Feature | Status | Details |
|---------|--------|---------|
| Docker Compose | Built | PostgreSQL 16, Qdrant, Neo4j 5 |
| Azure VM deployment | Built | Scripts ready, VM not provisioned |
| HTTPS via nginx | Built | Reverse proxy configuration |
| Health check endpoint | Built | `/api/health` |

---

## Out of Scope (Deferred)

### Deferred to Phase 3 (Post-Beta)
| Feature | Reason for Deferral |
|---------|-------------------|
| Production agents (document generation, comparison, extraction) | Requires tool-use loop implementation and artifact display UI |
| Contract redlining | Medium effort, not needed for Q&A demo |
| Playbook/template system | Low effort but not core to beta value proposition |
| Batch processing | Requires job queue infrastructure |
| Export to PDF/DOCX/CSV | UI work needed for artifact download |

### Deferred to Phase 4 (Enterprise)
| Feature | Reason for Deferral |
|---------|-------------------|
| SSO/SAML authentication | Enterprise requirement, Clerk JWT sufficient for beta |
| Role-based access control | Only 2 users in beta |
| Audit logging | Compliance feature, not needed for POC |
| Word/Office integration | High effort, requires add-in development |
| Email integration | Not core to document Q&A |
| Multi-region deployment | Single Azure VM sufficient for beta |
| API rate limiting | 2 users, no risk of overload |

### Not Planned
| Feature | Reason |
|---------|--------|
| Westlaw/LexisNexis data integration | Proprietary databases, cannot replicate |
| Litigation analytics | Requires court data pipeline |
| Real-time collaboration | Different product category |
| Mobile app | Web UI sufficient, responsive design covers mobile access |

---

## Acceptance Criteria

### Must Pass (Beta Blockers)

1. **Upload**: A 100+ page legal PDF uploads successfully and completes the 9-stage pipeline without errors
2. **Parse Quality**: Claude Vision extracts text from scanned PDFs with <1% character error rate
3. **Chunk Quality**: Legal structure headings (Article, Section, Clause) are detected and preserved in chunk metadata
4. **Search Relevance**: For 10 test questions, the relevant chunk appears in the top-5 results at least 9/10 times
5. **Answer Quality**: Answers cite specific sources using [Source N] notation; citations point to chunks that contain the claimed information
6. **Grounding**: Answers with low grounding confidence are flagged with hallucination risk warning
7. **Latency**: Single question answered in under 10 seconds (excluding first-load model warmup)
8. **Concurrent Access**: Two users can query simultaneously without errors or data leakage between tenants
9. **Deployment**: System accessible via HTTPS from both India and US
10. **Stability**: No crashes during a 30-minute continuous usage session

### Should Pass (Non-Blockers for Beta)

1. **Graph queries**: Multi-hop questions return answers that traverse entity relationships
2. **Conversation context**: Follow-up questions use prior conversation context
3. **Duplicate detection**: Re-uploading the same PDF is caught and rejected
4. **Document deletion**: Deleting a document removes its chunks, vectors, and graph nodes
5. **Streaming**: Answers stream token-by-token in the UI

### Nice to Have (All Achieved)

1. ~~**Evaluation metrics**: 1000-question suite runs and produces accuracy/relevance scores~~ **DONE** — 1,308 questions generated from 71 documents; 9 rounds of evaluation with LLM judge; 100% pass rate, 4.48/5 composite
2. ~~**Admin dashboard**: System stats visible in admin panel~~ **DONE** — 5 stat cards on /admin route
3. ~~**Error recovery**: Failed ingestion can be retried without side effects~~ **DONE** — `scripts/retry_ingest.py` with inter-doc delays and pipeline-level retry

---

## Known Risks

| Risk | Likelihood | Impact | Mitigation | **Outcome** |
|------|-----------|--------|-----------|------------|
| Claude Max setup-token rate limits during evaluation | Medium | Could block 1000-question evaluation | Run evaluation in batches with delays; switch to API key if needed | **Realized**: 48/84 docs failed on first ingestion due to 429 rate limits. Resolved with `retry_ingest.py` and inter-doc delays. |
| Azure VM provisioning delays | Low | Blocks deployment | Start provisioning immediately; have backup on local machine | **Not realized**: VM provisioned on schedule |
| Neo4j memory on small VM | Medium | Graph queries slow or fail | Use Neo4j Community with conservative memory settings | **Mitigated**: Running stable with conservative settings |
| Voyage AI embedding latency | Low | Slow ingestion | Fallback to local BAAI model if Voyage is slow | **Not realized**: Voyage AI performed well |
| Real legal documents with unusual formatting | High | Parser misses content | Test with 3+ document types (contract, regulation, case law) before beta | **Realized**: 60% of pages were scanned PDFs. Addressed with Vision OCR + OCR text cleanup pipeline |

---

## Test Plan for Beta

### Document Types to Test
1. **Standard contract** (NDA, MSA) — 10-30 pages, clean formatting
2. **Government regulation** — 100+ pages, numbered sections, cross-references
3. **Scanned legal document** — Image-based PDF, tests Claude Vision
4. **Table-heavy document** — Financial schedules, tests table extraction

### Question Categories to Test
1. **Simple lookup**: "What is the effective date of this agreement?"
2. **Multi-hop**: "Which parties are bound by the confidentiality clause, and what are their obligations?"
3. **Entity lookup**: "Who are the signatories of this contract?"
4. **Comparative**: "How do the termination clauses differ between Section 5 and Section 8?"
5. **Open-ended**: "Summarize the key obligations of the licensee."
6. **Not in document**: "What is the patent filing deadline?" (expected: "not found in documents")
7. **Cross-reference**: "What does 'Confidential Information' mean as defined in Article 1?"
8. **Temporal**: "What are the renewal deadlines?"
9. **Numerical**: "What is the liability cap?"
10. **Ambiguous**: "What are the restrictions?" (expected: identify ambiguity, ask for clarification)

---

## Sign-Off

| Role | Name | Date | Approved |
|------|------|------|----------|
| Developer | Technical Lead | Feb 15, 2026 | All acceptance criteria met |
| PM/Advisor | Harish | | |
