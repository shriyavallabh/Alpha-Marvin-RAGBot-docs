# JurisAgent Basic — Feature Guide

**Product:** Alpha Marvin / JurisAgent
**Phase:** Phase 2 — JurisAgent Basic
**Tagline:** Grounded truth, simplified.
**Last Updated:** February 7, 2026
**Owner:** Vallabh Pethkar

This document describes each Phase 2 feature in business language: what it does, how it works, what it depends on, and what it cannot do.

---

## 1. Secure Document Upload & Encrypted Storage

### What It Does

Allows Admin users to upload legal PDF documents through a drag-and-drop interface. Documents are processed through a 9-stage AI pipeline that extracts text, understands structure, and makes the content searchable via natural language.

### Process Flow

```
Admin drags PDF onto upload zone
        │
        ▼
File uploaded to server (encrypted in transit via HTTPS)
        │
        ▼
Stage 1: PARSE
        │  PDF pages rendered as images
        │  Claude Vision extracts text (handles scanned docs,
        │  watermarks, tables)
        │  Fallback: PyMuPDF text extraction for text-based PDFs
        │
        ▼
Stage 2: DEDUPLICATE
        │  Check if document already exists in the system
        │  Prevent duplicate processing
        │
        ▼
Stage 3: CHUNK
        │  Split document into retrievable sections
        │  Legal-aware splitting: detects Articles, Sections,
        │  Clauses, Schedules, Definitions
        │  Each chunk: 500-1500 characters with overlap
        │
        ▼
Stage 4: ENRICH
        │  Claude adds contextual summaries to each chunk
        │  Improves retrieval accuracy by 67% (Anthropic method)
        │
        ▼
Stage 5: EXTRACT
        │  Claude identifies entities (parties, dates, amounts)
        │  Identifies relationships (obligations, rights, conditions)
        │
        ▼
Stage 6: EMBED
        │  Voyage AI converts text to 1024-dimensional vectors
        │  Enables semantic similarity search
        │
        ▼
Stage 7: VECTOR STORE
        │  Vectors stored in Qdrant with tenant isolation
        │
        ▼
Stage 8: DATABASE
        │  Metadata saved to PostgreSQL
        │  (document name, page count, chunk count, timestamps)
        │
        ▼
Stage 9: KNOWLEDGE GRAPH
        │  Entities and relationships stored in Neo4j
        │  Enables multi-hop reasoning across documents
        │
        ▼
Document appears in sidebar — ready for queries
```

### Prerequisites

- User must have Admin role
- PDF file format (other formats not supported in Phase 2)
- Active internet connection (for Claude Vision and Voyage AI API calls)

### Dependencies

| Dependency | Purpose | What Happens If Unavailable |
|-----------|---------|----------------------------|
| Claude Vision API | Text extraction from PDF pages | Falls back to PyMuPDF text extraction (lower quality for scanned docs) |
| Voyage AI API | Embedding generation | Falls back to local BAAI/bge-small model (384-dim, lower retrieval quality) |
| Qdrant | Vector storage | Upload fails; user sees error |
| PostgreSQL | Metadata storage | Upload fails; user sees error |
| Neo4j | Knowledge graph | Graph stage skipped; document still queryable via vector search |

### Limitations

- PDF only (no Word, Excel, or image files)
- Maximum practical size: ~700 pages per document (very large scanned documents take hours)
- Scanned PDFs take significantly longer (~15 seconds per page for Vision OCR)
- No batch upload UI (one document at a time, though pipeline handles sequential uploads)
- No upload progress showing which of 9 stages is currently running

---

## 2. Natural Language Query Interface

### What It Does

Allows any authenticated user to ask questions about uploaded legal documents in plain English. No query syntax, no keyword matching — just type a question the way you would ask a colleague.

### Process Flow

```
User types question in chat input
        │
        ▼
Query Classification
        │  System categorizes the question type:
        │  • Factual ("What is the effective date?")
        │  • Analytical ("What are the risks in this contract?")
        │  • Procedural ("What steps are needed to terminate?")
        │  • Comparative ("How does clause A differ from clause B?")
        │
        ▼
Query Enrichment
        │  Claude expands legal terminology
        │  Example: "non-compete" → also searches for
        │  "restrictive covenant", "competition restriction"
        │
        ▼
3-Channel Search (parallel)
        │
        ├─► Vector Search (Qdrant)
        │   Semantic similarity against all document chunks
        │
        ├─► Keyword Search (BM25)
        │   Exact term matching for precise legal terms
        │
        └─► Graph Search (Neo4j)
            Multi-hop traversal of entity relationships
        │
        ▼
Result Fusion (RRF)
        │  Reciprocal Rank Fusion merges results from
        │  all 3 channels into a single ranked list
        │
        ▼
Reranking (Cross-Encoder)
        │  ms-marco-MiniLM-L-6-v2 re-scores results
        │  for final relevance ordering
        │
        ▼
Top chunks passed to Claude for answer generation
```

### Prerequisites

- At least one document must be uploaded and fully processed
- User must be authenticated (Admin or Member role)

### Dependencies

| Dependency | Purpose | What Happens If Unavailable |
|-----------|---------|----------------------------|
| Qdrant | Vector search | Search fails; user sees error |
| Neo4j | Graph traversal | Graph channel skipped; vector + keyword still work |
| Claude API | Query enrichment + answer generation | Query fails; user sees error |

### Limitations

- Questions must relate to uploaded documents — the system does not use outside knowledge
- Very broad questions ("tell me everything") produce lower-quality answers than specific ones
- Cross-document questions work but are limited by context window (~200K tokens)
- No support for non-English queries in Phase 2

---

## 3. Citation-Backed Answers

### What It Does

Every answer includes traceable citations pointing to specific passages in the source documents. Users can verify any claim by clicking the citation to see the exact text.

### Process Flow

```
Claude generates answer using retrieved chunks
        │
        ▼
Citations embedded inline as [Source N]
        │  Each [Source N] maps to a specific chunk
        │  from a specific document and page range
        │
        ▼
Answer displayed with clickable citations
        │
        ▼
User clicks [Source 1]
        │
        ▼
Source Viewer panel opens
        │  Shows: document name, page number(s),
        │  exact text passage that supports the claim
        │
        ▼
User verifies the citation is accurate
```

### Prerequisites

- A question must be asked and answered (citations are part of answers)
- Source chunks must exist in the vector store

### Dependencies

| Dependency | Purpose | What Happens If Unavailable |
|-----------|---------|----------------------------|
| Claude API | Generates citations inline with answer | No answer = no citations |
| PostgreSQL | Stores chunk-to-document mapping | Source viewer cannot look up original document |

### Limitations

- Citations reference chunks, not exact page numbers (a chunk may span pages 4-5)
- Citation quality depends on chunking — if a clause was split across chunks, the citation may show only part of it
- No deep-link to a specific page in the original PDF (text-based source viewer only)
- Benchmark result: citation quality scored 2.95/5 in LLM judge evaluation — this is an active improvement area

---

## 4. Hallucination Prevention & Grounding Validation

### What It Does

Every answer is automatically checked against the source documents. The system assigns a confidence score and hallucination risk rating, displayed as a color-coded indicator so users know how much to trust each answer.

### Process Flow

```
Claude generates answer
        │
        ▼
Grounding Validation
        │  System checks: does every claim in the answer
        │  have support in the retrieved source chunks?
        │
        ▼
Confidence Score calculated (0.0 to 1.0)
        │
        ▼
Classification:
        │
        ├─► GREEN  (confidence >= 0.7)
        │   "High confidence — answer is well-grounded"
        │
        ├─► YELLOW (confidence >= 0.4)
        │   "Medium confidence — some claims may lack
        │    full source support"
        │
        └─► RED    (confidence < 0.4)
            "Low confidence — answer may contain
             unsupported claims, verify carefully"
        │
        ▼
Displayed to user as:
        │
        │  Confidence: ████████░░ HIGH (0.92)  [GREEN]
        │  Hallucination Risk: LOW
        │  Grounded: Yes
```

### Prerequisites

- An answer must be generated (grounding is a post-generation step)
- Source chunks must be available for comparison

### Dependencies

| Dependency | Purpose | What Happens If Unavailable |
|-----------|---------|----------------------------|
| Claude API | Performs grounding check | Grounding skipped; answer shown without confidence score |

### Limitations

- Grounding checks add ~2-3 seconds to response time
- The confidence score is an estimate, not a guarantee — a GREEN answer can still contain subtle inaccuracies
- Grounding validates against retrieved chunks, not the full document — if the relevant passage was not retrieved, grounding cannot catch the gap
- Does not detect errors of omission (important information that should have been mentioned but was not)

---

## 5. Clause Extraction & Search

### What It Does

The system understands legal document structure. When documents are ingested, the chunking engine detects and preserves legal structural elements — Articles, Sections, Clauses, Schedules, Definitions. Users can search for specific clauses by name or content.

### Process Flow

```
During document ingestion:
        │
        ▼
Legal Structure Detection
        │  Regex + heuristic patterns identify:
        │  • "Article 1", "Article II"
        │  • "Section 3.2", "Section 3.2(a)"
        │  • "Clause 5", "Sub-clause (i)"
        │  • "Schedule A", "Exhibit B"
        │  • "Definitions" sections
        │
        ▼
Structure-Aware Chunking
        │  Chunk boundaries align with legal structure
        │  Section headings preserved as chunk metadata
        │  Cross-references noted ("see Section 4.2(a)")
        │
        ▼
Metadata stored with each chunk:
        │  • Document name
        │  • Page numbers
        │  • Section heading
        │  • Chunk index
        │
        ▼
During query:
        │
        ▼
User asks: "What does Section 5.1 say about termination?"
        │
        ▼
Search prioritizes chunks tagged with "Section 5.1"
        │
        ▼
Answer references specific clauses with [Source N] links
```

### Prerequisites

- Documents must be in a structured format (contracts, regulations, policies)
- Ingestion pipeline must have completed successfully

### Dependencies

| Dependency | Purpose | What Happens If Unavailable |
|-----------|---------|----------------------------|
| Chunking engine | Structure detection | Falls back to basic character-based splitting (lower precision) |
| Qdrant | Stores chunk metadata including section info | Search works but cannot filter by section |

### Limitations

- Works best with well-structured documents (numbered sections, clear headings)
- Unstructured documents (letters, memos, emails) may not have detectable clause structure
- Non-standard numbering schemes may not be detected (e.g., custom Roman numeral formats)
- Cannot reconstruct full document outline/table of contents from extracted structure

---

## 6. Admin Dashboard & Document Insights

### What It Does

Gives Admin users a high-level view of the workspace: how many documents are loaded, how many chunks and vectors exist, how many conversations have occurred, and how many users are in the system. Provides operational visibility without needing to dig into databases.

### Process Flow

```
Admin navigates to /admin
        │
        ▼
System checks role (must be "admin")
        │
        ├─► Non-admin: 403 Forbidden
        │
        └─► Admin: Dashboard loads
                │
                ▼
        5 stat cards displayed:
        ┌─────────────────────────────────────────────┐
        │                                             │
        │  [FileText]     [Layers]      [Database]    │
        │  12 Documents   847 Chunks    847 Vectors   │
        │                                             │
        │  [MessageSquare]              [Users]       │
        │  34 Conversations             3 Users       │
        │                                             │
        └─────────────────────────────────────────────┘
                │
                ▼
        All counts scoped to current tenant
```

### Prerequisites

- Admin role required
- At least one document uploaded (otherwise all stats show 0)

### Dependencies

| Dependency | Purpose | What Happens If Unavailable |
|-----------|---------|----------------------------|
| PostgreSQL | Document, chunk, conversation, user counts | Dashboard shows error |
| Qdrant | Vector count | Vector stat shows error; other stats still work |

### Limitations

- Read-only dashboard (no actions can be taken from this view yet)
- No historical trends (shows current state only, not changes over time)
- No invite management UI yet (invites must be created via API)
- No user management UI yet (cannot view/edit users from dashboard)
- See [Dashboards](dashboards.md) for detailed gap analysis

---

## 7. Conversation History & Query Archive

### What It Does

All questions and answers are saved automatically. Users can browse past conversations, revisit previous research sessions, and continue where they left off. Each conversation preserves the full thread of questions, answers, citations, and confidence scores.

### Process Flow

```
User asks a question
        │
        ▼
System creates conversation (if new)
or appends to existing conversation
        │
        ▼
Question + answer + citations + grounding
all saved to PostgreSQL
        │
        ▼
Conversation appears in sidebar history
        │
        ▼
Later: user clicks past conversation
        │
        ▼
Full Q&A thread restored
        │  • All questions shown
        │  • All answers with citations
        │  • Confidence indicators preserved
        │  • Source links still clickable
        │
        ▼
User can continue asking follow-up questions
in the same conversation context
```

### Prerequisites

- User must be authenticated
- At least one question must be asked to create a conversation

### Dependencies

| Dependency | Purpose | What Happens If Unavailable |
|-----------|---------|----------------------------|
| PostgreSQL | Stores conversation data | History not saved; current session still works |

### Limitations

- No search across past conversations (must scroll through list)
- No conversation export (PDF/DOCX) in Phase 2
- No conversation sharing between users
- No conversation deletion by users in Phase 2
- Conversations are scoped to the user who created them — other users cannot see them

---

## 8. Source Verification

### What It Does

When an answer cites a source, users can click the citation to see the exact text passage from the original document. This closes the trust loop: the user does not have to take the AI's word for it — they can read the source themselves.

### Process Flow

```
Answer displayed with [Source 1], [Source 2], etc.
        │
        ▼
User clicks [Source 1]
        │
        ▼
Source Viewer panel opens alongside answer
        │
        ▼
Panel shows:
┌──────────────────────────────────────────┐
│  SOURCE 1                                │
│                                          │
│  Document: NDA_AcmeCorp.pdf              │
│  Pages: 4-5                              │
│  Section: Article 3 — Confidentiality    │
│                                          │
│  "The Receiving Party agrees to hold     │
│  and maintain in strict confidence all   │
│  Confidential Information disclosed by   │
│  the Disclosing Party, and shall not,    │
│  without the prior written consent of    │
│  the Disclosing Party, disclose..."      │
│                                          │
└──────────────────────────────────────────┘
        │
        ▼
User reads original text and confirms
the AI's interpretation is accurate
```

### Prerequisites

- An answer with citations must exist
- The cited chunk must still be in the vector store

### Dependencies

| Dependency | Purpose | What Happens If Unavailable |
|-----------|---------|----------------------------|
| PostgreSQL | Maps chunk ID to document and page info | Source viewer shows error |
| Qdrant | Stores the actual chunk text | Chunk text unavailable |

### Limitations

- Shows extracted text, not the original PDF rendering (no images, formatting, or layout)
- Chunk text may be truncated if it spans a chunk boundary
- No ability to download or view the original PDF page
- Source viewer opens in a panel alongside the chat — not a full-page view
