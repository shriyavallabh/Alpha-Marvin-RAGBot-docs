# Dashboard Inventory

**Product:** Alpha Marvin / JurisAgent
**Last Updated:** February 7, 2026
**Owner:** Vallabh Pethkar

This document provides an honest accounting of what dashboard capabilities exist today, what they do not yet show, and when gaps are planned to be addressed.

!!! note "At a Glance"
    3 dashboards fully built (Admin Stats, Document List, Conversation History).
    1 partial (Document Insights — query-driven today, standalone dashboard in Phase 3).

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

<div markdown="block" style="max-width:720px;margin:1.5rem auto;font-family:'Inter',-apple-system,sans-serif;">
<div style="border-radius:12px;overflow:hidden;box-shadow:0 4px 24px rgba(0,0,0,0.12);border:1px solid #e5e7eb;">

<!-- Header -->
<div style="background:#1a1f36;padding:14px 20px;display:flex;align-items:center;justify-content:space-between;">
<div style="display:flex;align-items:center;gap:10px;">
<span style="color:#E8726A;font-weight:700;font-size:15px;">⚖ JurisAgent</span>
<span style="color:#94a3b8;font-size:13px;">/ Admin Dashboard</span>
</div>
<span style="background:rgba(232,114,106,0.15);color:#E8726A;padding:3px 10px;border-radius:12px;font-size:11px;font-weight:600;">Admin</span>
</div>

<div style="background:#f8f9fa;padding:24px;">

<!-- Row 1: 3 cards -->
<div style="display:grid;grid-template-columns:repeat(3,1fr);gap:14px;margin-bottom:14px;">

<div style="background:#ffffff;border-radius:10px;padding:20px;box-shadow:0 1px 3px rgba(0,0,0,0.06);text-align:center;">
<div style="font-size:28px;margin-bottom:4px;">📄</div>
<div style="font-size:28px;font-weight:800;color:#1a1a1a;">84</div>
<div style="font-size:12px;color:#6b7280;margin-top:2px;">Total Documents</div>
</div>

<div style="background:#ffffff;border-radius:10px;padding:20px;box-shadow:0 1px 3px rgba(0,0,0,0.06);text-align:center;">
<div style="font-size:28px;margin-bottom:4px;">🔷</div>
<div style="font-size:28px;font-weight:800;color:#1a1a1a;">5,369</div>
<div style="font-size:12px;color:#6b7280;margin-top:2px;">Total Chunks</div>
</div>

<div style="background:#ffffff;border-radius:10px;padding:20px;box-shadow:0 1px 3px rgba(0,0,0,0.06);text-align:center;">
<div style="font-size:28px;margin-bottom:4px;">🗄</div>
<div style="font-size:28px;font-weight:800;color:#1a1a1a;">5,369</div>
<div style="font-size:12px;color:#6b7280;margin-top:2px;">Vectors Stored</div>
</div>

</div>

<!-- Row 2: 2 cards -->
<div style="display:grid;grid-template-columns:repeat(2,1fr);gap:14px;">

<div style="background:#ffffff;border-radius:10px;padding:20px;box-shadow:0 1px 3px rgba(0,0,0,0.06);text-align:center;">
<div style="font-size:28px;margin-bottom:4px;">💬</div>
<div style="font-size:28px;font-weight:800;color:#1a1a1a;">127</div>
<div style="font-size:12px;color:#6b7280;margin-top:2px;">Conversations</div>
</div>

<div style="background:#ffffff;border-radius:10px;padding:20px;box-shadow:0 1px 3px rgba(0,0,0,0.06);text-align:center;">
<div style="font-size:28px;margin-bottom:4px;">👥</div>
<div style="font-size:28px;font-weight:800;color:#1a1a1a;">3</div>
<div style="font-size:12px;color:#6b7280;margin-top:2px;">Active Users</div>
</div>

</div>

</div>
</div>
<p style="text-align:center;color:#6b7280;font-size:13px;margin-top:8px;font-style:italic;">Admin Statistics Dashboard — live data from the JurisAgent deployment</p>
</div>

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

<div markdown="block" style="max-width:720px;margin:1.5rem auto;font-family:'Inter',-apple-system,sans-serif;">
<div style="border-radius:12px;overflow:hidden;box-shadow:0 4px 24px rgba(0,0,0,0.12);border:1px solid #e5e7eb;">

<!-- Header -->
<div style="background:#1a1f36;padding:14px 20px;display:flex;align-items:center;justify-content:space-between;">
<div style="display:flex;align-items:center;gap:10px;">
<span style="color:#ffffff;font-weight:700;font-size:15px;">📊 Portfolio Insights Dashboard</span>
</div>
<span style="background:rgba(217,119,6,0.15);color:#d97706;padding:3px 10px;border-radius:12px;font-size:11px;font-weight:600;">Phase 3 — Planned</span>
</div>

<div style="background:#f8f9fa;padding:24px;">

<!-- Stat cards -->
<div style="display:grid;grid-template-columns:repeat(4,1fr);gap:12px;margin-bottom:20px;">
<div style="background:#ffffff;border-radius:10px;padding:16px;box-shadow:0 1px 3px rgba(0,0,0,0.06);text-align:center;border:1px dashed #d1d5db;">
<div style="font-size:24px;font-weight:800;color:#1a1a1a;">12</div>
<div style="font-size:11px;color:#6b7280;margin-top:2px;">Contracts Analyzed</div>
</div>
<div style="background:#ffffff;border-radius:10px;padding:16px;box-shadow:0 1px 3px rgba(0,0,0,0.06);text-align:center;border:1px dashed #d1d5db;">
<div style="font-size:24px;font-weight:800;color:#dc2626;">3</div>
<div style="font-size:11px;color:#6b7280;margin-top:2px;">High-Risk Clauses</div>
</div>
<div style="background:#ffffff;border-radius:10px;padding:16px;box-shadow:0 1px 3px rgba(0,0,0,0.06);text-align:center;border:1px dashed #d1d5db;">
<div style="font-size:24px;font-weight:800;color:#d97706;">7</div>
<div style="font-size:11px;color:#6b7280;margin-top:2px;">Upcoming Deadlines</div>
</div>
<div style="background:#ffffff;border-radius:10px;padding:16px;box-shadow:0 1px 3px rgba(0,0,0,0.06);text-align:center;border:1px dashed #d1d5db;">
<div style="font-size:24px;font-weight:800;color:#1a1a1a;">24</div>
<div style="font-size:11px;color:#6b7280;margin-top:2px;">Obligations Tracked</div>
</div>
</div>

<!-- Placeholder areas -->
<div style="display:grid;grid-template-columns:1fr 1fr;gap:14px;margin-bottom:14px;">
<div style="border:2px dashed #d1d5db;border-radius:10px;padding:32px;text-align:center;background:#ffffff;">
<div style="font-size:24px;margin-bottom:8px;opacity:0.4;">📈</div>
<div style="font-size:13px;color:#9ca3af;font-weight:600;">Risk Summary Chart</div>
<div style="font-size:11px;color:#d1d5db;margin-top:4px;">Coming in Phase 3</div>
</div>
<div style="border:2px dashed #d1d5db;border-radius:10px;padding:32px;text-align:center;background:#ffffff;">
<div style="font-size:24px;margin-bottom:8px;opacity:0.4;">📅</div>
<div style="font-size:13px;color:#9ca3af;font-weight:600;">Deadline Calendar</div>
<div style="font-size:11px;color:#d1d5db;margin-top:4px;">Coming in Phase 3</div>
</div>
</div>

<div style="border:2px dashed #d1d5db;border-radius:10px;padding:32px;text-align:center;background:#ffffff;">
<div style="font-size:24px;margin-bottom:8px;opacity:0.4;">📋</div>
<div style="font-size:13px;color:#9ca3af;font-weight:600;">Obligation Tracking Table</div>
<div style="font-size:11px;color:#d1d5db;margin-top:4px;">Coming in Phase 3</div>
</div>

</div>
</div>
<p style="text-align:center;color:#6b7280;font-size:13px;margin-top:8px;font-style:italic;">Portfolio Insights Dashboard — planned for Phase 3, auto-generated without user prompts</p>
</div>

---

## Summary: Built vs Planned

| Dashboard | Status | Actionable Today? | Next Enhancement |
|-----------|--------|:-----------------:|------------------|
| Admin Statistics | BUILT | View-only (5 metrics) | Add invite management, user list (Feb 10-12) |
| Document List | BUILT | Yes (upload, browse, select) | Add metadata display (Feb 12) |
| Conversation History | BUILT | Yes (browse, revisit) | Add search, deletion (Phase 3) |
| Document Insights | PARTIAL | Query-driven only | Portfolio dashboard (Phase 3) |
