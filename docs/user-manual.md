# User Manual — Beta v0.1.0

**Product:** Alpha Marvin / JurisAgent
**Audience:** Beta testers (JurisAgent Basic)
**Last Updated:** February 4, 2026

---

## Table of Contents

1. [Getting Started](#getting-started)
2. [Uploading Documents](#uploading-documents)
3. [Asking Questions](#asking-questions)
4. [Understanding Answers](#understanding-answers)
5. [Managing Documents](#managing-documents)
6. [Conversations](#conversations)
7. [Interpreting Confidence Scores](#interpreting-confidence-scores)
8. [Tips for Better Results](#tips-for-better-results)
9. [Known Limitations](#known-limitations)
10. [Reporting Issues](#reporting-issues)

---

## Getting Started

### Accessing the Platform

1. Open your browser and navigate to the platform URL (provided separately)
2. Log in with your credentials (provided during beta onboarding)
3. You will land on the Chat page — the main interface for asking questions

### Interface Layout

```
┌──────────────────────────────────────────────────────┐
│  ┌────────────┐  ┌─────────────────────────────────┐ │
│  │            │  │                                 │ │
│  │ Sidebar    │  │     Chat Area                   │ │
│  │            │  │                                 │ │
│  │ • Convos   │  │  [Answer with citations]        │ │
│  │ • History  │  │  [Source viewer]                 │ │
│  │            │  │  [Confidence indicator]          │ │
│  │            │  │                                 │ │
│  │            │  │                                 │ │
│  │            │  ├─────────────────────────────────┤ │
│  │            │  │  [Type your question here...]   │ │
│  └────────────┘  └─────────────────────────────────┘ │
└──────────────────────────────────────────────────────┘
```

- **Sidebar:** Browse your conversations and uploaded documents
- **Chat Area:** View answers with citations and source references
- **Input Bar:** Type your questions (fixed at the bottom)

---

## Uploading Documents

### Supported Formats

| Format | Extension | Notes |
|--------|-----------|-------|
| PDF (text-based) | .pdf | Standard text PDFs |
| PDF (scanned) | .pdf | Image-based PDFs processed via Claude Vision |
| Images | .png, .jpg | Direct image text extraction |
| Text files | .txt, .md | Plain text documents |
| HTML | .html | Web page content |
| CSV | .csv | Structured tabular data |

### How to Upload

1. Navigate to the **Documents** page (sidebar or top navigation)
2. **Drag and drop** your file onto the upload area, or click to browse
3. The upload progress will show each processing stage:

```
Stage 1/9: Parsing document...          ✓
Stage 2/9: Checking for duplicates...   ✓
Stage 3/9: Chunking text...             ✓
Stage 4/9: Enriching chunks...          ✓
Stage 5/9: Extracting entities...       ✓
Stage 6/9: Generating embeddings...     ✓
Stage 7/9: Storing vectors...           ✓
Stage 8/9: Saving to database...        ✓
Stage 9/9: Building knowledge graph...  ✓
```

4. Once complete, the document appears in your document list
5. You can now ask questions about the document

### Upload Tips

- **File size:** There is no strict file size limit, but larger documents (500+ pages) will take longer to process
- **Multiple documents:** Upload multiple documents to enable cross-document queries
- **Duplicate detection:** If you upload the same document twice, the system will detect it and skip reprocessing

---

## Asking Questions

### How to Ask

1. Go to the **Chat** page
2. Type your question in the input bar at the bottom
3. Press Enter or click Send
4. The answer will stream in real-time, appearing token by token

### Question Types That Work Well

| Type | Example | What Happens |
|------|---------|-------------|
| **Simple lookup** | "What is the effective date of this agreement?" | Searches for date entities and returns with citation |
| **Multi-hop** | "Which parties are bound by the confidentiality clause, and what are their obligations?" | Traverses knowledge graph across entity relationships |
| **Entity lookup** | "Who are the signatories?" | Searches entity nodes in knowledge graph |
| **Comparative** | "How do the termination clauses differ between Section 5 and Section 8?" | Retrieves both sections and presents differences |
| **Summarization** | "Summarize the key obligations of the licensee." | Retrieves all relevant chunks and synthesizes |
| **Cross-reference** | "What does 'Confidential Information' mean as defined in Article 1?" | Follows definition cross-references in graph |
| **Not in document** | "What is the patent filing deadline?" | Responds: "The provided documents do not contain sufficient information to answer this question." |

### Follow-Up Questions

The system maintains conversation context. You can ask follow-up questions without repeating context:

```
You: What are the payment terms?
AI:  [answer about payment terms from Section 3]

You: What happens if payment is late?
AI:  [answer about late payment penalties, using conversation context]
```

---

## Understanding Answers

### Answer Structure

Each answer includes:

1. **Main Answer** — The response text, formatted in Markdown
2. **Citations** — References like [Source 1], [Source 2] pointing to specific document sections
3. **Sources** — The actual text chunks that support the answer
4. **Confidence Score** — How well the answer is grounded in the documents
5. **Hallucination Risk** — Rating of potential inaccuracy

### Example Answer

```
Based on the Master Services Agreement, the termination provisions
are as follows:

Either party may terminate this Agreement upon thirty (30) days'
written notice to the other party [Source 1]. In the event of a
material breach, the non-breaching party may terminate immediately
upon written notice if the breach is not cured within fifteen (15)
business days [Source 2].

Upon termination, all confidential information must be returned or
destroyed within ten (10) days [Source 3].

──────────────────────────────
Sources:
[Source 1] MSA_AcmeCorp.pdf, Page 12, Section 7.1
[Source 2] MSA_AcmeCorp.pdf, Page 12, Section 7.2
[Source 3] MSA_AcmeCorp.pdf, Page 14, Section 8.3

Confidence: 0.94  |  Hallucination Risk: LOW  |  Grounded: Yes
```

### Viewing Source Chunks

Click on any [Source N] reference to expand and view the exact text from the document that supports that part of the answer. This allows you to verify the answer against the original document.

---

## Managing Documents

### Viewing Uploaded Documents

- Navigate to **Documents** in the sidebar
- See all uploaded documents with:
  - Document name
  - Upload date
  - Number of chunks
  - Processing status

### Deleting Documents

1. Click the delete icon next to a document
2. Confirm the deletion
3. This removes the document, all its chunks, vector embeddings, and knowledge graph entries
4. Previous answers referencing this document will still be visible in conversation history, but the sources will no longer be available

### Viewing Document Chunks

Click on a document to see its individual chunks — the text segments that were created during processing. Each chunk shows:
- The text content
- Page number(s)
- Section heading (if detected)
- Whether it contains a legal definition

---

## Conversations

### Starting a New Conversation

- Click **New Conversation** in the sidebar
- Each conversation maintains its own context and history

### Resuming a Conversation

- Click on any previous conversation in the sidebar
- The full message history is loaded
- Continue asking follow-up questions with full context

### Deleting a Conversation

- Click the delete icon next to a conversation
- All messages in that conversation are permanently removed

---

## Interpreting Confidence Scores

### Confidence Score (0.0 – 1.0)

| Range | Meaning | Action |
|-------|---------|--------|
| 0.90 – 1.00 | High confidence — answer is well-supported by sources | Use with confidence; verify citations for critical decisions |
| 0.70 – 0.89 | Moderate confidence — most of the answer is supported | Review the sources; some parts may be inferred rather than directly stated |
| 0.50 – 0.69 | Low confidence — answer may not be fully supported | Treat with caution; manually verify against original documents |
| Below 0.50 | Very low confidence — answer is likely unreliable | Do not rely on this answer; consult the original documents directly |

### Hallucination Risk Rating

| Rating | Meaning |
|--------|---------|
| **LOW** | Answer is well-grounded in the source documents |
| **MEDIUM** | Some claims may not be directly supported; review recommended |
| **HIGH** | Significant risk of unsupported claims; manual verification required |

### What Happens When Risk Is High

If the system detects high hallucination risk:
1. The answer is automatically regenerated with a stricter prompt
2. The system may attempt up to 3 regeneration cycles
3. If grounding remains low after 3 attempts, the answer is returned with a prominent warning

### Important Disclaimer

**This platform is an AI assistant, not a substitute for legal judgment.** Always verify AI-generated answers against the original documents before relying on them for legal decisions. The confidence score indicates grounding quality, not legal correctness.

---

## Tips for Better Results

### 1. Be Specific
- Instead of: "Tell me about the contract"
- Try: "What are the termination notice periods in the Master Services Agreement?"

### 2. Reference Document Sections
- "What does Section 4.2(a) say about confidentiality?"
- The system can find specific sections more accurately when referenced

### 3. Ask One Question at a Time
- Multi-part questions can reduce answer quality
- Instead of: "What are the payment terms and termination clauses and IP ownership provisions?"
- Try: Ask each as a separate question

### 4. Use Follow-Up Questions
- The system remembers conversation context
- "What are the obligations?" → "Are there any exceptions to those obligations?"

### 5. Upload Related Documents Together
- Cross-document queries work best when all relevant documents are uploaded
- Example: Upload both the MSA and all related SOWs to ask about obligations across them

### 6. Check the Sources
- Always click through to the source chunks for critical answers
- The source viewer shows the exact text that supports the answer

### 7. Watch the Confidence Score
- High confidence (>0.9) means the answer is well-grounded
- Low confidence (<0.7) means you should verify manually
- The system will warn you about high hallucination risk

---

## Known Limitations (Beta)

1. **Processing time:** Large documents (200+ pages) may take several minutes to process through all 9 pipeline stages
2. **Concurrent uploads:** Upload one document at a time for best results
3. **Language:** English documents only in beta
4. **Document generation:** The beta is Q&A only; document generation features are coming in a future release
5. **Export:** Answers cannot be exported to PDF/DOCX in beta; use copy/paste
6. **Mobile:** The UI is designed for desktop browsers; mobile support is limited
7. **Session timeout:** Long-idle sessions may require re-login
8. **Scanned PDFs:** While supported, very low-quality scans may produce lower-accuracy extraction

---

## Reporting Issues

### What to Report

- Incorrect answers (especially if confidence score was high)
- Missing citations or broken source links
- Upload failures or stuck processing
- UI bugs or display issues
- Slow performance (note the question and approximate response time)

### How to Report

1. **Screenshot** the issue if possible
2. **Note** the conversation ID (visible in the sidebar)
3. **Note** the question you asked
4. **Send** to the development team via the agreed-upon channel

### What Helps Us Most

- The exact question you asked
- What you expected the answer to be
- What the system actually answered
- The confidence score and hallucination risk rating
- The document(s) involved

---

## Glossary

| Term | Definition |
|------|-----------|
| **Chunk** | A segment of text extracted from a document, typically 300–800 words |
| **Citation** | A reference like [Source 1] linking an answer claim to a specific document section |
| **Confidence Score** | A 0.0–1.0 rating of how well the answer is supported by source documents |
| **Grounding** | The process of verifying that an answer is based on actual document content |
| **Hallucination** | When an AI generates information not present in the source documents |
| **Knowledge Graph** | A network of entities (people, companies, clauses) and their relationships extracted from documents |
| **Multi-hop Query** | A question that requires following relationships across multiple entities or documents |
| **RRF Fusion** | Reciprocal Rank Fusion — the mathematical method used to combine results from multiple search channels |
| **SSE Streaming** | Server-Sent Events — the technology that enables real-time, token-by-token answer display |
| **Tenant** | An isolated workspace; your data is separate from other users' data |
