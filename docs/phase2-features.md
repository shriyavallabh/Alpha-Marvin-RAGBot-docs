# JurisAgent Basic — Feature Guide

**Product:** Alpha Marvin / JurisAgent
**Phase:** Phase 2 — JurisAgent Basic
**Tagline:** Grounded truth, simplified.
**Last Updated:** February 15, 2026
**Owner:** Vallabh Pethkar

This document describes each Phase 2 feature in business language: what it does, how it works, what it depends on, and what it cannot do.

!!! note "At a Glance"
    12 features — all built and operational. Document upload with 9-stage AI pipeline,
    natural language Q&A, citation-backed answers, hallucination prevention with
    GREEN/YELLOW/RED confidence scoring, admin dashboard, conversation history,
    OCR text cleanup, auto document filtering, Bluebook citation formatting, and
    analysis playbooks (contract review, due diligence, regulatory analysis).
    Validated through 9 rounds of benchmark evaluation — 100% pass rate, 4.48/5 composite score.

---

## 1. Secure Document Upload & Encrypted Storage

### What It Does

Allows Admin users to upload legal PDF documents through a drag-and-drop interface. Documents are processed through a 9-stage AI pipeline that extracts text, understands structure, and makes the content searchable via natural language.

### Process Flow

<div markdown="block" style="max-width:740px;margin:1.5rem auto;font-family:'Inter',-apple-system,BlinkMacSystemFont,sans-serif;">
<div style="background:#ffffff;border-radius:16px;padding:24px;border:1px solid #e8e8e8;">
<div style="text-align:center;font-weight:700;font-size:14px;color:#1a1a1a;margin-bottom:16px;">9-Stage AI Ingestion Pipeline</div>
<div style="display:flex;align-items:center;justify-content:center;flex-wrap:wrap;gap:4px;">
<div style="background:#E8726A;color:white;padding:6px 14px;border-radius:20px;font-size:11px;font-weight:600;white-space:nowrap;">1. Parse</div>
<span style="color:#d1d5db;font-size:14px;">&#x2192;</span>
<div style="background:#E8726A;color:white;padding:6px 14px;border-radius:20px;font-size:11px;font-weight:600;white-space:nowrap;">2. Dedup</div>
<span style="color:#d1d5db;font-size:14px;">&#x2192;</span>
<div style="background:#E8726A;color:white;padding:6px 14px;border-radius:20px;font-size:11px;font-weight:600;white-space:nowrap;">3. Chunk</div>
<span style="color:#d1d5db;font-size:14px;">&#x2192;</span>
<div style="background:#E8726A;color:white;padding:6px 14px;border-radius:20px;font-size:11px;font-weight:600;white-space:nowrap;">4. Enrich</div>
<span style="color:#d1d5db;font-size:14px;">&#x2192;</span>
<div style="background:#E8726A;color:white;padding:6px 14px;border-radius:20px;font-size:11px;font-weight:600;white-space:nowrap;">5. Extract</div>
<span style="color:#d1d5db;font-size:14px;">&#x2192;</span>
<div style="background:#E8726A;color:white;padding:6px 14px;border-radius:20px;font-size:11px;font-weight:600;white-space:nowrap;">6. Embed</div>
<span style="color:#d1d5db;font-size:14px;">&#x2192;</span>
<div style="background:#E8726A;color:white;padding:6px 14px;border-radius:20px;font-size:11px;font-weight:600;white-space:nowrap;">7. Vector Store</div>
<span style="color:#d1d5db;font-size:14px;">&#x2192;</span>
<div style="background:#E8726A;color:white;padding:6px 14px;border-radius:20px;font-size:11px;font-weight:600;white-space:nowrap;">8. Database</div>
<span style="color:#d1d5db;font-size:14px;">&#x2192;</span>
<div style="background:#E8726A;color:white;padding:6px 14px;border-radius:20px;font-size:11px;font-weight:600;white-space:nowrap;">9. Knowledge Graph</div>
</div>
<div style="text-align:center;margin-top:12px;font-size:12px;color:#6b7280;">~15 seconds per page for scanned PDFs &nbsp;|&nbsp; Text PDFs process in under 1 second per page</div>
</div>
</div>

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

<div markdown="block" style="max-width:740px;margin:1.5rem auto;font-family:'Inter',-apple-system,BlinkMacSystemFont,sans-serif;">
<div style="border-radius:16px;overflow:hidden;box-shadow:0 8px 32px rgba(0,0,0,0.08);border:1px solid #e8e8e8;">

<div style="background:#fafafa;padding:20px;">

<!-- User message -->
<div style="display:flex;justify-content:flex-end;margin-bottom:16px;">
<div style="background:#E8726A;color:white;padding:10px 16px;border-radius:16px 16px 4px 16px;max-width:85%;font-size:13px;line-height:1.5;">What are the indemnification obligations in the TTG Agreement?</div>
</div>

<!-- AI response with avatar -->
<div style="display:flex;gap:10px;align-items:flex-start;">
<div style="width:28px;height:28px;border-radius:50%;background:#E8726A;flex-shrink:0;display:flex;align-items:center;justify-content:center;margin-top:2px;"><span style="color:white;font-size:12px;font-weight:700;">=</span></div>
<div style="background:#ffffff;padding:16px;border-radius:4px 16px 16px 16px;max-width:90%;font-size:13px;line-height:1.7;color:#1a1a1a;border:1px solid #f0f0f0;">
<div style="font-size:12px;color:#E8726A;font-weight:600;margin-bottom:8px;">JurisAgent</div>

<strong>Indemnification Obligations — TTG Agreement</strong><br><br>

<span style="display:inline-block;width:5px;height:5px;border-radius:50%;background:#E8726A;margin-right:6px;vertical-align:middle;"></span> <strong>General Indemnification (§8.2):</strong> Each party shall indemnify and hold harmless the other from third-party claims arising from material breach of representations or warranties.<br>
<span style="display:inline-block;width:5px;height:5px;border-radius:50%;background:#E8726A;margin-right:6px;vertical-align:middle;"></span> <strong>IP Indemnification (§8.3):</strong> Service Provider shall defend against third-party IP infringement claims related to delivered work product.<br>
<span style="display:inline-block;width:5px;height:5px;border-radius:50%;background:#E8726A;margin-right:6px;vertical-align:middle;"></span> <strong>Liability Cap (§8.5):</strong> Capped at total fees paid in preceding 12-month period, except for gross negligence.

<div style="margin-top:12px;display:flex;flex-wrap:wrap;gap:6px;">
<div style="padding:5px 10px;background:#f8f8f8;border-radius:6px;font-size:11px;color:#6b7280;display:inline-flex;align-items:center;gap:4px;">&#9634; Source: Legal &gt; QAMAR 003092 &gt; §8.2, Page 5</div>
<div style="padding:5px 10px;background:#f8f8f8;border-radius:6px;font-size:11px;color:#6b7280;display:inline-flex;align-items:center;gap:4px;">&#9634; Source: Legal &gt; QAMAR 003098 &gt; §8.3, Pages 4-5</div>
</div>

<div style="margin-top:10px;display:flex;align-items:center;gap:8px;">
<span style="font-size:11px;color:#6b7280;">Confidence</span>
<div style="flex:1;height:4px;background:#e5e7eb;border-radius:2px;overflow:hidden;max-width:120px;"><div style="width:92%;height:100%;background:#22c55e;border-radius:2px;"></div></div>
<span style="font-size:11px;color:#22c55e;font-weight:600;">92%</span>
</div>
</div>
</div>

</div>
</div>
<p style="text-align:center;color:#6b7280;font-size:13px;margin-top:10px;font-style:italic;">Natural language Q&A — source-cited answers with confidence scoring</p>
</div>

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
- Benchmark result: citation quality scored **4.78/5** in Round 9 LLM judge evaluation (improved from 2.95/5 through 9 rounds of iterative fixes)

---

## 4. Hallucination Prevention & Grounding Validation

### What It Does

Every answer is automatically checked against the source documents. The system assigns a confidence score and hallucination risk rating, displayed as a color-coded indicator so users know how much to trust each answer.

<div markdown="block" style="max-width:740px;margin:1.5rem auto;font-family:'Inter',-apple-system,BlinkMacSystemFont,sans-serif;">
<div style="background:#ffffff;border-radius:16px;padding:24px;border:1px solid #e8e8e8;">
<div style="text-align:center;font-weight:700;font-size:14px;color:#1a1a1a;margin-bottom:20px;">Confidence Scoring — Three Levels</div>
<div style="display:flex;gap:16px;justify-content:center;flex-wrap:wrap;">

<!-- GREEN -->
<div style="flex:1;min-width:190px;max-width:220px;background:#fafafa;border-radius:12px;padding:16px;border:1px solid #e8e8e8;">
<div style="display:flex;align-items:center;gap:8px;margin-bottom:10px;">
<span style="font-size:11px;color:#6b7280;">Confidence</span>
<div style="flex:1;height:4px;background:#e5e7eb;border-radius:2px;overflow:hidden;"><div style="width:92%;height:100%;background:#22c55e;border-radius:2px;"></div></div>
<span style="font-size:11px;color:#22c55e;font-weight:700;">92%</span>
</div>
<div style="font-size:13px;font-weight:600;color:#1a1a1a;margin-bottom:4px;">High Confidence</div>
<div style="font-size:11px;color:#6b7280;line-height:1.5;">Answer is well-grounded in source documents. Low hallucination risk.</div>
</div>

<!-- YELLOW -->
<div style="flex:1;min-width:190px;max-width:220px;background:#fafafa;border-radius:12px;padding:16px;border:1px solid #e8e8e8;">
<div style="display:flex;align-items:center;gap:8px;margin-bottom:10px;">
<span style="font-size:11px;color:#6b7280;">Confidence</span>
<div style="flex:1;height:4px;background:#e5e7eb;border-radius:2px;overflow:hidden;"><div style="width:50%;height:100%;background:#d97706;border-radius:2px;"></div></div>
<span style="font-size:11px;color:#d97706;font-weight:700;">50%</span>
</div>
<div style="font-size:13px;font-weight:600;color:#1a1a1a;margin-bottom:4px;">Moderate — Verify Sources</div>
<div style="font-size:11px;color:#6b7280;line-height:1.5;">Some claims may lack full support. Review cited sources carefully.</div>
</div>

<!-- RED -->
<div style="flex:1;min-width:190px;max-width:220px;background:#fafafa;border-radius:12px;padding:16px;border:1px solid #e8e8e8;">
<div style="display:flex;align-items:center;gap:8px;margin-bottom:10px;">
<span style="font-size:11px;color:#6b7280;">Confidence</span>
<div style="flex:1;height:4px;background:#e5e7eb;border-radius:2px;overflow:hidden;"><div style="width:23%;height:100%;background:#dc2626;border-radius:2px;"></div></div>
<span style="font-size:11px;color:#dc2626;font-weight:700;">23%</span>
</div>
<div style="font-size:13px;font-weight:600;color:#1a1a1a;margin-bottom:4px;">Low Confidence</div>
<div style="font-size:11px;color:#6b7280;line-height:1.5;">Insufficient source support. Verify all claims independently.</div>
</div>

</div>
</div>
</div>

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
Hallucination Risk assessed (LOW / MEDIUM / HIGH)
        │
        ▼
Classification (considers BOTH confidence + risk):
        │
        ├─► GREEN  (confidence >= 0.75 + LOW risk)
        │          (confidence >= 0.80 + MEDIUM risk)
        │   "High Confidence"
        │
        ├─► YELLOW (confidence >= 0.50 + LOW/MEDIUM risk)
        │          (confidence >= 0.65 + HIGH risk)
        │   "Moderate — Verify Sources"
        │
        └─► RED    (everything else)
            "Low Confidence — consult an attorney"
        │
        ▼
If HIGH risk detected:
        │  Answer is automatically regenerated with
        │  stricter prompt (up to 3 attempts)
        │
        ▼
Displayed to user as color-coded badge:
        │
        │  [GREEN]  High Confidence
        │  [YELLOW] Moderate — Verify Sources
        │  [RED]    Low Confidence — Attorney Review Recommended
        │
        │  (Raw percentage is NOT shown to avoid
        │   false precision for legal users)
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

---

---

## 9. OCR Text Cleanup

### What It Does

When scanned PDFs are processed through Vision OCR, the extracted text can contain artifacts — split words, hyphenation errors, garbled characters, and unicode issues. The OCR text cleanup pipeline automatically fixes these before the text is chunked and embedded, improving retrieval accuracy.

### Process Flow

```
After PDF parsing (Stage 1), before deduplication (Stage 2):
        │
        ▼
Pass 1: DEHYPHENATION
        │  Rejoin words split by line-ending hyphens
        │  "termi-\nnation" → "termination"
        │
        ▼
Pass 2: SPLIT-WORD JOIN
        │  Sliding window (5→4→3→2 char fragments)
        │  Joins if result is a dictionary word
        │  "li abilities" → "liabilities"
        │  "termi nation" → "termination"
        │
        ▼
Pass 3: CHARACTER FIXES
        │  Common OCR character substitutions
        │  "rn" → "m", "cl" → "d", etc.
        │
        ▼
Pass 4: UNICODE NORMALIZATION
        │  Standardize smart quotes, dashes, whitespace
        │
        ▼
Cleaned text proceeds to deduplication and chunking
```

### Key Details

- Dictionary: NLTK words (~236K) + inflections (~700K total) + ~275 legal terms and Latin phrases
- Fragment threshold: 5+ characters (catches "ation", "ering", "trigg")
- Tiny non-standalone exception: 2-char fragments like "li" are not standalone words → allows joining "li abilities" → "liabilities"
- 31 unit tests validate the cleaner
- Reclean script (`scripts/reclean_qdrant.py`) can re-process existing Qdrant chunks

### Impact

After running the OCR reclean on existing data, 2,101 of 5,369 chunks (39.1%) had improved text quality, which directly contributed to better retrieval accuracy in benchmark evaluations.

---

## 10. Auto Document Filtering

### What It Does

When a user asks a question that references a specific document (e.g., "What are the termination clauses in the TTG Agreement?"), the system automatically detects this and scopes the search to that document only. This prevents cross-document contamination where facts from different documents get blended.

### Process Flow

```
User asks: "What are the liabilities in the ACE contract?"
        │
        ▼
Regex Detection
        │  Pattern matches: "in the ____ contract/agreement/document"
        │  Detects: doc-specific query
        │
        ▼
LLM Document Reference Extraction
        │  Claude extracts the document reference: "ACE contract"
        │
        ▼
Document Matching
        │  Embedding search against document titles
        │  + Title word-overlap scoring (70% title + 30% retrieval count)
        │  Threshold: 0.3 minimum match score
        │
        ▼
Scoped Search
        │  All retrieval channels (Vector, Synonym, Graph)
        │  are filtered to document_id of matched document
        │
        ▼
Answer references only the specified document
```

### Impact

Before auto document filtering, 2 of 50 benchmark questions failed due to cross-document contamination (mixing facts from Vol. I and Vol. II of depositions, or from different witness lists). After implementation, both passed.

---

## 11. Bluebook Citation Formatting

### What It Does

Citations in answers are automatically formatted according to Bluebook style — the standard citation format used in legal practice. The system detects the document type (contract, case law, regulation, deposition) and applies the appropriate citation format.

### Example

Instead of: `[Source 1: QAMAR_003092.pdf, Page 5]`
Produces: `[Source 1: QAMAR 003092, at 5]` (for legal documents)

---

## 12. Analysis Playbooks

### What It Does

Reusable analysis frameworks that apply structured analytical approaches to uploaded documents. Three playbooks are available:

| Playbook | Purpose | Key Outputs |
|----------|---------|-------------|
| **Contract Review** | Systematic contract analysis | Key terms, obligations, risks, missing clauses |
| **Due Diligence** | Data room document review | Red flags, compliance gaps, entity relationships |
| **Regulatory Analysis** | Regulatory compliance check | Applicable requirements, compliance status, gaps |

Playbooks extend the base Q&A engine with domain-specific prompts and structured output templates.

---

!!! warning "Data Scope: Unstructured Documents Only"
    JurisAgent processes **unstructured documents** — PDFs (text + scanned),
    images, and text files. Structured database connectivity (SQL tables,
    APIs, data warehouses) is a separate capability planned for Phase 2+.
    CSV files are supported but processed as text extraction.
