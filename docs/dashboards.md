# Dashboard Inventory

**Product:** Alpha Marvin / JurisAgent
**Last Updated:** February 7, 2026
**Owner:** Vallabh Pethkar

This document provides an honest accounting of what dashboard capabilities exist today, what they do not yet show, and when gaps are planned to be addressed.

---

## 1. Admin Statistics Dashboard

**Status:** BUILT
**Route:** `/admin`
**Access:** Admin role only (403 for members)

### What It Shows Today

Five stat cards displayed in a responsive grid:

| Stat | Icon | Source |
|------|------|--------|
| Total Documents | FileText | Count of documents in PostgreSQL |
| Total Chunks | Layers | Count of chunks in PostgreSQL |
| Vectors Stored | Database | Count of vectors in Qdrant collection |
| Conversations | MessageSquare | Count of conversations in PostgreSQL |
| Users | Users | Count of users in PostgreSQL |

All counts are scoped to the current tenant (tenant_id filtering).

### What It Does NOT Show

| Gap | Priority | Target |
|-----|----------|--------|
| Invite management (create, view pending) | HIGH | Feb 10 |
| User list with roles | MEDIUM | Feb 12 |
| Document ingestion status/progress | MEDIUM | Feb 12 |
| Query volume over time (chart) | LOW | Phase 3 |
| Storage usage breakdown | LOW | Phase 3 |
| System health indicators (service status) | LOW | Phase 3 |

### Planned Enhancement

The Admin Dashboard will be expanded to include invite management and a user list. This turns it from a read-only stats view into an actionable admin console.

---

## 2. Document List

**Status:** BUILT
**Route:** Sidebar on main chat page + `/documents` (if navigated directly)
**Access:** All authenticated users

### What It Shows Today

| Element | Details |
|---------|---------|
| Document names | Displayed in sidebar, sorted by upload date |
| Upload status | Shows processing state during ingestion |
| Document count | Total number visible in list |
| Click-to-query | Selecting a document scopes questions to it |

Upload interface (Admin only):
- Drag-and-drop file upload
- Progress indicator during ingestion
- Success/error feedback

### What It Does NOT Show

| Gap | Priority | Target |
|-----|----------|--------|
| Page count per document | LOW | Phase 3 |
| File size | LOW | Phase 3 |
| Upload date/time | MEDIUM | Feb 12 |
| Ingestion pipeline stage (which of 9 stages) | LOW | Phase 3 |
| Document type detection (contract, case law, regulation) | LOW | Phase 3 |
| Chunk count per document | LOW | Phase 3 |
| Delete confirmation dialog | MEDIUM | Feb 12 |

---

## 3. Conversation History & Query Archive

**Status:** BUILT
**Route:** Left sidebar on main chat page
**Access:** All authenticated users (scoped to own conversations)

### What It Shows Today

| Element | Details |
|---------|---------|
| Conversation list | Past conversations listed by title/first question |
| Click to revisit | Full Q&A thread restored on click |
| New conversation button | Start fresh context |
| Active conversation highlight | Current conversation visually indicated |

Each conversation preserves:
- All questions asked
- All streamed answers with citations
- Source references
- Confidence/grounding indicators

### What It Does NOT Show

| Gap | Priority | Target |
|-----|----------|--------|
| Search across past conversations | MEDIUM | Phase 3 |
| Date/time stamps on conversations | LOW | Phase 3 |
| Export conversation to PDF/DOCX | LOW | Phase 3 |
| Conversation sharing between users | LOW | Phase 4 |
| Conversation analytics (questions asked, avg confidence) | LOW | Phase 3 |
| Conversation deletion | MEDIUM | Feb 12 |

---

## 4. Document Insights

**Status:** PARTIAL
**Route:** Available through Q&A responses (not a standalone dashboard)
**Access:** All authenticated users

### What Exists Today

Per-document analysis capabilities available through the Q&A engine:

| Capability | How It Works | Status |
|------------|-------------|--------|
| Risk assessment | Ask about risks in a specific document; Claude analyzes with citations | BUILT |
| Timeline extraction | Ask about dates, deadlines, milestones; structured response | BUILT |
| Cross-document comparison | Ask to compare clauses across two documents | BUILT |
| Entity relationships | Knowledge graph shows parties, obligations, references | BUILT |
| Confidence scoring | Every answer rated GREEN/YELLOW/RED | BUILT |

These work as **query-driven insights** — the user asks a question and gets an analyzed response.

### What Does NOT Exist

| Gap | Description | Priority | Target |
|-----|-------------|----------|--------|
| Portfolio dashboard | Aggregate view across all documents (total obligations, upcoming deadlines, risk summary) | MEDIUM | Phase 3 |
| Auto-generated risk report | Automatic risk scan on upload without user prompt | LOW | Phase 3 |
| Obligation tracker | Dashboard of all obligations extracted across documents | LOW | Phase 3 |
| Deadline calendar | Visual calendar of extracted dates and deadlines | LOW | Phase 3 |
| Document health score | Per-document completeness/risk rating on the document list | LOW | Phase 3 |

### Current vs Planned

```
TODAY (Query-Driven)                 PLANNED (Dashboard, Phase 3)
====================                 ===========================

User asks: "What are               Portfolio Insights Dashboard:
the risks in Contract A?"          ┌─────────────────────────┐
        │                          │ 12 Contracts Analyzed   │
        ▼                          │ 3 High-Risk Clauses     │
Claude analyzes + cites            │ 7 Upcoming Deadlines    │
        │                          │ 24 Obligations Tracked  │
        ▼                          │                         │
Answer with sources +              │ [Risk Summary Chart]    │
GREEN confidence                   │ [Deadline Calendar]     │
                                   │ [Obligation Table]      │
                                   └─────────────────────────┘
                                   Auto-generated, always
                                   visible, no prompt needed
```

---

## Summary: Built vs Planned

| Dashboard | Status | Actionable Today? | Next Enhancement |
|-----------|--------|:-----------------:|------------------|
| Admin Statistics | BUILT | View-only (5 metrics) | Add invite management, user list (Feb 10-12) |
| Document List | BUILT | Yes (upload, browse, select) | Add metadata display (Feb 12) |
| Conversation History | BUILT | Yes (browse, revisit) | Add search, deletion (Phase 3) |
| Document Insights | PARTIAL | Query-driven only | Portfolio dashboard (Phase 3) |
