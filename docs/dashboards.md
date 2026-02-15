# Dashboard Inventory

**Product:** Alpha Marvin / JurisAgent
**Last Updated:** February 15, 2026
**Owner:** Alpha Marvin Team

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

<div markdown="block" style="max-width:740px;margin:1.5rem auto;font-family:'Inter',-apple-system,BlinkMacSystemFont,sans-serif;">
<div style="border-radius:16px;overflow:hidden;box-shadow:0 8px 32px rgba(0,0,0,0.08);border:1px solid #e8e8e8;">

<!-- Sidebar + Dashboard layout -->
<div style="display:flex;">

<!-- Mini sidebar -->
<div style="width:200px;background:#2d2d3f;padding:20px 14px;flex-shrink:0;">
<div style="display:flex;align-items:center;gap:8px;margin-bottom:24px;">
<div style="width:28px;height:28px;border-radius:50%;background:#E8726A;display:flex;align-items:center;justify-content:center;"><span style="color:white;font-size:12px;font-weight:700;">=</span></div>
<span style="color:#ffffff;font-weight:600;font-size:14px;">JurisAgent</span>
</div>
<div style="color:#ffffff;font-size:13px;padding:8px 10px;border-radius:8px;background:rgba(255,255,255,0.08);margin-bottom:3px;display:flex;align-items:center;gap:8px;"><span style="width:18px;height:18px;border-radius:5px;background:rgba(255,255,255,0.12);display:inline-flex;align-items:center;justify-content:center;font-size:9px;color:#ccc;">&#9638;</span> Dashboard</div>
<div style="color:#8b8fa3;font-size:13px;padding:8px 10px;margin-bottom:3px;display:flex;align-items:center;gap:8px;"><span style="width:18px;height:18px;border-radius:5px;display:inline-flex;align-items:center;justify-content:center;font-size:9px;color:#8b8fa3;">&#9675;</span> Ask JurisAgent</div>
<div style="color:#8b8fa3;font-size:13px;padding:8px 10px;margin-bottom:3px;display:flex;align-items:center;gap:8px;"><span style="width:18px;height:18px;border-radius:5px;display:inline-flex;align-items:center;justify-content:center;font-size:9px;color:#8b8fa3;">&#9633;</span> Knowledge Base</div>
<div style="color:#8b8fa3;font-size:13px;padding:8px 10px;margin-bottom:3px;display:flex;align-items:center;gap:8px;"><span style="width:18px;height:18px;border-radius:5px;display:inline-flex;align-items:center;justify-content:center;font-size:9px;color:#8b8fa3;">&#9881;</span> Settings</div>
</div>

<!-- Dashboard content -->
<div style="flex:1;background:#fafafa;padding:24px;">
<div style="font-size:18px;font-weight:700;color:#1a1a1a;margin-bottom:2px;">Dashboard</div>
<div style="font-size:13px;color:#6b7280;margin-bottom:20px;">Your legal knowledge at your fingertips</div>

<!-- 4 stat cards in a row (matching alphamarvin.com layout) -->
<div style="display:grid;grid-template-columns:repeat(4,1fr);gap:12px;margin-bottom:12px;">

<div style="background:#ffffff;border-radius:12px;padding:16px;border:1px solid #e8e8e8;">
<div style="display:flex;align-items:center;gap:6px;margin-bottom:10px;">
<span style="width:20px;height:20px;border-radius:6px;background:rgba(232,114,106,0.1);display:inline-flex;align-items:center;justify-content:center;font-size:10px;color:#E8726A;">&#9634;</span>
<span style="font-size:11px;color:#6b7280;">Documents Indexed</span>
</div>
<div style="font-size:28px;font-weight:800;color:#1a1a1a;margin-bottom:2px;">84</div>
<div style="font-size:11px;color:#22c55e;">+12 this month</div>
</div>

<div style="background:#ffffff;border-radius:12px;padding:16px;border:1px solid #e8e8e8;">
<div style="display:flex;align-items:center;gap:6px;margin-bottom:10px;">
<span style="width:20px;height:20px;border-radius:6px;background:rgba(232,114,106,0.1);display:inline-flex;align-items:center;justify-content:center;font-size:10px;color:#E8726A;">&#9643;</span>
<span style="font-size:11px;color:#6b7280;">Chunks Processed</span>
</div>
<div style="font-size:28px;font-weight:800;color:#1a1a1a;margin-bottom:2px;">5,369</div>
<div style="font-size:11px;color:#6b7280;">Across all documents</div>
</div>

<div style="background:#ffffff;border-radius:12px;padding:16px;border:1px solid #e8e8e8;">
<div style="display:flex;align-items:center;gap:6px;margin-bottom:10px;">
<span style="width:20px;height:20px;border-radius:6px;background:rgba(232,114,106,0.1);display:inline-flex;align-items:center;justify-content:center;font-size:10px;color:#E8726A;">&#9673;</span>
<span style="font-size:11px;color:#6b7280;">Conversations</span>
</div>
<div style="font-size:28px;font-weight:800;color:#1a1a1a;margin-bottom:2px;">127</div>
<div style="font-size:11px;color:#22c55e;">Active</div>
</div>

<div style="background:#ffffff;border-radius:12px;padding:16px;border:1px solid #e8e8e8;">
<div style="display:flex;align-items:center;gap:6px;margin-bottom:10px;">
<span style="width:20px;height:20px;border-radius:6px;background:rgba(232,114,106,0.1);display:inline-flex;align-items:center;justify-content:center;font-size:10px;color:#E8726A;">&#9775;</span>
<span style="font-size:11px;color:#6b7280;">Active Users</span>
</div>
<div style="font-size:28px;font-weight:800;color:#1a1a1a;margin-bottom:2px;">3</div>
<div style="font-size:11px;color:#6b7280;">2 Admin, 1 Member</div>
</div>

</div>

<!-- Answer Quality + Knowledge Insights row (matching alphamarvin.com) -->
<div style="display:grid;grid-template-columns:1fr 1fr;gap:12px;">

<div style="background:#ffffff;border-radius:12px;padding:16px;border:1px solid #e8e8e8;">
<div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:8px;">
<div style="display:flex;align-items:center;gap:6px;"><span style="font-size:12px;color:#E8726A;">&#10039;</span><span style="font-size:13px;font-weight:600;color:#1a1a1a;">Answer Quality</span></div>
<span style="font-size:11px;color:#22c55e;display:flex;align-items:center;gap:3px;"><span style="width:6px;height:6px;border-radius:50%;background:#22c55e;display:inline-block;"></span> Improving</span>
</div>
<div style="display:flex;align-items:baseline;gap:8px;margin-bottom:4px;">
<span style="font-size:22px;font-weight:800;color:#1a1a1a;">100%</span>
<span style="font-size:11px;color:#22c55e;">4.48/5 composite</span>
<span style="font-size:11px;color:#6b7280;">Round 9 evaluation</span>
</div>
<div style="font-size:11px;color:#6b7280;">50-question benchmark, 12 perfect scores</div>
</div>

<div style="background:#ffffff;border-radius:12px;padding:16px;border:1px solid #e8e8e8;">
<div style="display:flex;align-items:center;gap:6px;margin-bottom:8px;"><span style="font-size:12px;color:#E8726A;">&#10039;</span><span style="font-size:13px;font-weight:600;color:#1a1a1a;">Knowledge Insights</span></div>
<div style="font-size:11px;color:#6b7280;text-transform:uppercase;letter-spacing:0.5px;margin-bottom:6px;">Active by type</div>
<div style="display:flex;flex-wrap:wrap;gap:6px;margin-bottom:10px;">
<span style="font-size:11px;color:#4b5563;display:flex;align-items:center;gap:3px;"><span style="width:6px;height:6px;border-radius:50%;background:#E8726A;display:inline-block;"></span> Contracts (34)</span>
<span style="font-size:11px;color:#4b5563;display:flex;align-items:center;gap:3px;"><span style="width:6px;height:6px;border-radius:50%;background:#3b82f6;display:inline-block;"></span> Depositions (28)</span>
<span style="font-size:11px;color:#4b5563;display:flex;align-items:center;gap:3px;"><span style="width:6px;height:6px;border-radius:50%;background:#22c55e;display:inline-block;"></span> Policies (22)</span>
</div>
<div style="font-size:11px;color:#6b7280;text-transform:uppercase;letter-spacing:0.5px;margin-bottom:4px;">Knowledge gaps</div>
<div style="background:#fefce8;padding:6px 10px;border-radius:6px;font-size:11px;color:#a16207;"><span style="display:inline-block;width:6px;height:6px;border-radius:50%;background:#eab308;margin-right:4px;vertical-align:middle;"></span> Q3 compliance reports not yet indexed</div>
</div>

</div>

</div>
</div>
</div>
<p style="text-align:center;color:#6b7280;font-size:13px;margin-top:10px;font-style:italic;">Admin Dashboard — modeled on the Alpha Marvin platform dashboard</p>
</div>

### What It Does NOT Show

| Gap | Priority | Target | Status |
|-----|----------|--------|--------|
| Invite management (create, view pending) | HIGH | Feb 10 | Built (backend API) |
| User list with roles | MEDIUM | Feb 12 | Built (backend API) |
| Document ingestion status/progress | MEDIUM | Feb 12 | Built (SSE progress) |
| Query volume over time (chart) | LOW | Phase 3 | Planned |
| Storage usage breakdown | LOW | Phase 3 | Planned |
| System health indicators (service status) | LOW | Phase 3 | Planned |

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

<div markdown="block" style="max-width:740px;margin:1.5rem auto;font-family:'Inter',-apple-system,BlinkMacSystemFont,sans-serif;">
<div style="border-radius:16px;overflow:hidden;border:1px solid #e8e8e8;box-shadow:0 8px 32px rgba(0,0,0,0.08);opacity:0.92;">

<div style="background:#fafafa;padding:24px;">
<div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:20px;">
<div style="font-size:18px;font-weight:700;color:#1a1a1a;">Portfolio Insights Dashboard</div>
<span style="background:rgba(217,119,6,0.1);color:#d97706;padding:4px 12px;border-radius:20px;font-size:11px;font-weight:600;">Phase 3 — Planned</span>
</div>

<!-- Stat cards -->
<div style="display:grid;grid-template-columns:repeat(4,1fr);gap:12px;margin-bottom:16px;">
<div style="background:#ffffff;border-radius:12px;padding:16px;border:1px dashed #d1d5db;">
<div style="display:flex;align-items:center;gap:6px;margin-bottom:8px;"><span style="width:20px;height:20px;border-radius:6px;background:rgba(232,114,106,0.1);display:inline-flex;align-items:center;justify-content:center;font-size:10px;color:#E8726A;">&#9634;</span><span style="font-size:11px;color:#6b7280;">Contracts</span></div>
<div style="font-size:24px;font-weight:800;color:#1a1a1a;">12</div>
</div>
<div style="background:#ffffff;border-radius:12px;padding:16px;border:1px dashed #d1d5db;">
<div style="display:flex;align-items:center;gap:6px;margin-bottom:8px;"><span style="width:20px;height:20px;border-radius:6px;background:rgba(220,38,38,0.1);display:inline-flex;align-items:center;justify-content:center;font-size:10px;color:#dc2626;">&#9888;</span><span style="font-size:11px;color:#6b7280;">High-Risk</span></div>
<div style="font-size:24px;font-weight:800;color:#dc2626;">3</div>
</div>
<div style="background:#ffffff;border-radius:12px;padding:16px;border:1px dashed #d1d5db;">
<div style="display:flex;align-items:center;gap:6px;margin-bottom:8px;"><span style="width:20px;height:20px;border-radius:6px;background:rgba(217,119,6,0.1);display:inline-flex;align-items:center;justify-content:center;font-size:10px;color:#d97706;">&#9201;</span><span style="font-size:11px;color:#6b7280;">Deadlines</span></div>
<div style="font-size:24px;font-weight:800;color:#d97706;">7</div>
</div>
<div style="background:#ffffff;border-radius:12px;padding:16px;border:1px dashed #d1d5db;">
<div style="display:flex;align-items:center;gap:6px;margin-bottom:8px;"><span style="width:20px;height:20px;border-radius:6px;background:rgba(232,114,106,0.1);display:inline-flex;align-items:center;justify-content:center;font-size:10px;color:#E8726A;">&#9745;</span><span style="font-size:11px;color:#6b7280;">Obligations</span></div>
<div style="font-size:24px;font-weight:800;color:#1a1a1a;">24</div>
</div>
</div>

<!-- Placeholder areas -->
<div style="display:grid;grid-template-columns:1fr 1fr;gap:12px;margin-bottom:12px;">
<div style="border:2px dashed #d1d5db;border-radius:12px;padding:28px;text-align:center;background:#ffffff;">
<div style="font-size:13px;color:#9ca3af;font-weight:600;">Risk Summary Chart</div>
<div style="font-size:11px;color:#d1d5db;margin-top:4px;">Coming in Phase 3</div>
</div>
<div style="border:2px dashed #d1d5db;border-radius:12px;padding:28px;text-align:center;background:#ffffff;">
<div style="font-size:13px;color:#9ca3af;font-weight:600;">Deadline Calendar</div>
<div style="font-size:11px;color:#d1d5db;margin-top:4px;">Coming in Phase 3</div>
</div>
</div>
<div style="border:2px dashed #d1d5db;border-radius:12px;padding:28px;text-align:center;background:#ffffff;">
<div style="font-size:13px;color:#9ca3af;font-weight:600;">Obligation Tracking Table</div>
<div style="font-size:11px;color:#d1d5db;margin-top:4px;">Coming in Phase 3</div>
</div>

</div>
</div>
<p style="text-align:center;color:#6b7280;font-size:13px;margin-top:10px;font-style:italic;">Portfolio Insights Dashboard — planned for Phase 3, auto-generated without user prompts</p>
</div>

---

## Summary: Built vs Planned

| Dashboard | Status | Actionable Today? | Next Enhancement |
|-----------|--------|:-----------------:|------------------|
| Admin Statistics | BUILT | View-only (5 metrics) | Add invite management, user list (Feb 10-12) |
| Document List | BUILT | Yes (upload, browse, select) | Add metadata display (Feb 12) |
| Conversation History | BUILT | Yes (browse, revisit) | Add search, deletion (Phase 3) |
| Document Insights | PARTIAL | Query-driven only | Portfolio dashboard (Phase 3) |
