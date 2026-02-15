# Full User Journey

**Product:** Alpha Marvin / JurisAgent
**Last Updated:** February 15, 2026
**Owner:** Vallabh Pethkar

This document maps the complete journey for both user types — from first contact to daily usage.

---

## Admin Journey

### Phase 1: Initial Setup (One-Time)

```
┌────────────────────────────────────────────────────────────────┐
│                                                                │
│  STEP 1: SIGN UP                                               │
│  ────────────────                                              │
│  Admin navigates to JurisAgent URL                             │
│  └─► Clerk sign-up page                                        │
│  └─► Creates account (email + password or SSO)                 │
│  └─► Role set to "admin" in Clerk metadata                     │
│  └─► Redirected to main dashboard                              │
│                                                                │
│  STEP 2: FIRST DOCUMENT UPLOAD                                 │
│  ────────────────────────────                                  │
│  Admin sees empty workspace                                    │
│  └─► Clicks upload or drags PDF onto upload zone               │
│  └─► Progress indicator shows during 9-stage pipeline:         │
│      ┌──────────────────────────────────────────────┐          │
│      │ Parse (Claude Vision) ────────────► 15s/page │          │
│      │ Deduplicate ──────────────────────► <1s      │          │
│      │ Chunk (legal-aware splitting) ────► <1s      │          │
│      │ Enrich (contextual via Claude) ───► 3s/chunk │          │
│      │ Extract (entities + relations) ───► 3s/chunk │          │
│      │ Embed (Voyage AI, 1024-dim) ──────► <1s      │          │
│      │ Store in Qdrant ──────────────────► <1s      │          │
│      │ Save to PostgreSQL ───────────────► <1s      │          │
│      │ Build Knowledge Graph (Neo4j) ────► <1s      │          │
│      └──────────────────────────────────────────────┘          │
│  └─► Document appears in sidebar when complete                 │
│  └─► Admin can upload multiple documents in sequence           │
│                                                                │
│  STEP 3: VERIFY SYSTEM WORKS                                   │
│  ───────────────────────────                                   │
│  Admin asks a test question about uploaded document             │
│  └─► Receives streamed answer with citations                   │
│  └─► Clicks [Source N] to verify source chunks                 │
│  └─► Checks confidence indicator (GREEN = good)                │
│  └─► System is ready for users                                 │
│                                                                │
└────────────────────────────────────────────────────────────────┘
```

### Phase 2: User Onboarding

```
┌────────────────────────────────────────────────────────────────┐
│                                                                │
│  STEP 4: INVITE USERS                                          │
│  ────────────────────                                          │
│  Admin navigates to Admin Dashboard (/admin)                   │
│  └─► Views current system stats (doc count, user count, etc.)  │
│  └─► Creates invite:                                           │
│      • Enters user's email address                             │
│      • Selects role: "member" (normal) or "admin"              │
│      • System generates unique invite token (7-day expiry)     │
│  └─► Admin copies invite link                                  │
│  └─► Sends link to user via email/Slack/etc.                   │
│                                                                │
│  STEP 5: MONITOR ACCEPTANCE                                    │
│  ────────────────────────                                      │
│  Admin checks Admin Dashboard                                  │
│  └─► Sees pending invites (not yet accepted)                   │
│  └─► User count increases when invite is accepted              │
│                                                                │
└────────────────────────────────────────────────────────────────┘
```

### Phase 3: Ongoing Administration

```
┌────────────────────────────────────────────────────────────────┐
│                                                                │
│  STEP 6: UPLOAD MORE DOCUMENTS                                 │
│  ─────────────────────────────                                 │
│  As the team's needs evolve:                                   │
│  └─► Admin uploads additional contracts, regulations, memos    │
│  └─► New documents automatically available to all users        │
│  └─► Knowledge graph grows with new entities and relations     │
│                                                                │
│  STEP 7: MONITOR SYSTEM HEALTH                                 │
│  ────────────────────────────                                  │
│  Admin periodically checks:                                    │
│  └─► Admin Dashboard for stats                                 │
│  └─► Conversation history to understand what users are asking  │
│  └─► Identifies gaps in document coverage based on queries     │
│                                                                │
│  STEP 8: MANAGE WORKSPACE                                      │
│  ────────────────────────                                      │
│  └─► Invite additional users as team grows                     │
│  └─► Review and respond to user feedback                       │
│  └─► Upload documents to address common query gaps             │
│                                                                │
└────────────────────────────────────────────────────────────────┘
```

---

## Normal User Journey

### Phase 1: Onboarding

```
┌────────────────────────────────────────────────────────────────┐
│                                                                │
│  STEP 1: RECEIVE INVITE                                        │
│  ──────────────────────                                        │
│  User receives invite link from Admin                          │
│  └─► Link format: https://jurisagent.com/invite?token=abc123  │
│  └─► User clicks link                                          │
│                                                                │
│  STEP 2: SIGN UP                                               │
│  ────────────────                                              │
│  User lands on invite acceptance page                          │
│  └─► Clicks "Accept Invitation"                                │
│  └─► Clerk sign-up flow (email + password or SSO)              │
│  └─► Account created with role = "member"                      │
│  └─► Automatically associated with Admin's tenant              │
│  └─► Redirected to main chat interface                         │
│                                                                │
└────────────────────────────────────────────────────────────────┘
```

### Phase 2: First Query Session

<div markdown="block" style="max-width:740px;margin:1.5rem auto;font-family:'Inter',-apple-system,BlinkMacSystemFont,sans-serif;">
<div style="border-radius:16px;overflow:hidden;box-shadow:0 8px 32px rgba(0,0,0,0.08);border:1px solid #e8e8e8;">
<div style="display:flex;min-height:320px;">

<!-- Sidebar -->
<div style="width:200px;background:#2d2d3f;padding:20px 14px;flex-shrink:0;">
<div style="display:flex;align-items:center;gap:8px;margin-bottom:24px;">
<div style="width:28px;height:28px;border-radius:50%;background:#E8726A;display:flex;align-items:center;justify-content:center;"><span style="color:white;font-size:12px;font-weight:700;">=</span></div>
<span style="color:#ffffff;font-weight:600;font-size:14px;">JurisAgent</span>
</div>
<div style="color:#8b8fa3;font-size:11px;text-transform:uppercase;letter-spacing:1px;margin-bottom:10px;">Documents</div>
<div style="display:flex;align-items:center;gap:6px;margin-bottom:6px;"><span style="width:6px;height:6px;border-radius:50%;background:#E8726A;display:inline-block;"></span><span style="color:#b4b8c8;font-size:12px;">NDA_AcmeCorp.pdf</span></div>
<div style="display:flex;align-items:center;gap:6px;margin-bottom:6px;"><span style="width:6px;height:6px;border-radius:50%;background:#22c55e;display:inline-block;"></span><span style="color:#b4b8c8;font-size:12px;">Employment_Agmt.pdf</span></div>
<div style="display:flex;align-items:center;gap:6px;margin-bottom:6px;"><span style="width:6px;height:6px;border-radius:50%;background:#3b82f6;display:inline-block;"></span><span style="color:#b4b8c8;font-size:12px;">Vendor_Contract.pdf</span></div>
<div style="display:flex;align-items:center;gap:6px;margin-bottom:6px;"><span style="width:6px;height:6px;border-radius:50%;background:#a855f7;display:inline-block;"></span><span style="color:#b4b8c8;font-size:12px;">Policy_Handbook.pdf</span></div>
<div style="border-top:1px solid rgba(255,255,255,0.08);margin:16px 0;"></div>
<div style="color:#8b8fa3;font-size:11px;text-transform:uppercase;letter-spacing:1px;margin-bottom:8px;">History</div>
<div style="color:#555770;font-size:12px;font-style:italic;">No conversations yet</div>
</div>

<!-- Main area - empty state -->
<div style="flex:1;background:#fafafa;padding:28px;display:flex;flex-direction:column;align-items:center;justify-content:center;">
<div style="text-align:center;margin-bottom:24px;">
<div style="width:48px;height:48px;border-radius:50%;background:rgba(232,114,106,0.1);display:flex;align-items:center;justify-content:center;margin:0 auto 14px;"><span style="color:#E8726A;font-size:22px;font-weight:700;">=</span></div>
<div style="font-size:16px;font-weight:600;color:#1a1a1a;margin-bottom:4px;">Ask about your legal documents</div>
<div style="font-size:13px;color:#6b7280;">Get grounded, citation-backed answers from your uploaded documents</div>
</div>
<div style="display:flex;flex-wrap:wrap;gap:8px;justify-content:center;max-width:420px;">
<div style="background:#ffffff;border:1px solid #e8e8e8;border-radius:10px;padding:8px 14px;font-size:11px;color:#4b5563;">What are the key obligations?</div>
<div style="background:#ffffff;border:1px solid #e8e8e8;border-radius:10px;padding:8px 14px;font-size:11px;color:#4b5563;">Summarize this contract</div>
<div style="background:#ffffff;border:1px solid #e8e8e8;border-radius:10px;padding:8px 14px;font-size:11px;color:#4b5563;">Find termination clauses</div>
<div style="background:#ffffff;border:1px solid #e8e8e8;border-radius:10px;padding:8px 14px;font-size:11px;color:#4b5563;">Compare indemnification terms</div>
</div>
<div style="margin-top:20px;width:100%;max-width:420px;background:#ffffff;border:1px solid #e5e7eb;border-radius:12px;padding:10px 14px;font-size:13px;color:#9ca3af;display:flex;align-items:center;justify-content:space-between;">
<span>Ask anything about your documents...</span>
<div style="width:28px;height:28px;border-radius:50%;background:#E8726A;display:flex;align-items:center;justify-content:center;"><span style="color:white;font-size:12px;">&#x27A4;</span></div>
</div>
</div>

</div>
</div>
<p style="text-align:center;color:#6b7280;font-size:13px;margin-top:10px;font-style:italic;">JurisAgent empty state — suggested queries help users get started</p>
</div>

```
┌────────────────────────────────────────────────────────────────┐
│                                                                │
│  STEP 3: EXPLORE THE INTERFACE                                 │
│  ────────────────────────────                                  │
│  User sees the Claude.ai-style chat interface                  │
│                                                                │
│  STEP 4: ASK FIRST QUESTION                                    │
│  ──────────────────────────                                    │
│  User types: "What are the termination clauses in the NDA?"    │
│  └─► System searches across all ingested documents             │
│                                                                │
│  STEP 5: RECEIVE ANSWER                                        │
│  ──────────────────────                                        │
│  Answer streams token-by-token:                                │
│                                                                │
│  ┌──────────────────────────────────────────────┐             │
│  │ Based on the NDA document, there are three   │             │
│  │ termination clauses:                         │             │
│  │                                              │             │
│  │ 1. Termination for Convenience [Source 1]    │             │
│  │    Either party may terminate with 30 days   │             │
│  │    written notice...                         │             │
│  │                                              │             │
│  │ 2. Termination for Cause [Source 2]          │             │
│  │    Immediate termination upon material       │             │
│  │    breach...                                 │             │
│  │                                              │             │
│  │ Confidence: ████████░░ HIGH (0.92)  [GREEN]  │             │
│  └──────────────────────────────────────────────┘             │
│                                                                │
│  STEP 6: VERIFY SOURCES                                        │
│  ──────────────────────                                        │
│  User clicks [Source 1]                                        │
│  └─► Source viewer panel opens                                 │
│  └─► Shows exact text passage from the PDF                     │
│  └─► User confirms the citation is accurate                    │
│                                                                │
└────────────────────────────────────────────────────────────────┘
```

### Phase 3: Daily Usage Pattern

```
┌────────────────────────────────────────────────────────────────┐
│                                                                │
│  STEP 7: ONGOING Q&A                                           │
│  ────────────────────                                          │
│  User returns daily to query documents:                        │
│                                                                │
│  Common query patterns:                                        │
│  └─► "What obligations does Party A have under Section 5?"     │
│  └─► "Compare the indemnification clauses in Contract A vs B"  │
│  └─► "What are the key dates and deadlines?"                   │
│  └─► "Is there a non-compete provision?"                       │
│  └─► "Summarize the force majeure clause"                      │
│                                                                │
│  STEP 8: USE CONVERSATION HISTORY                              │
│  ────────────────────────────────                              │
│  User browses past conversations in sidebar                    │
│  └─► Click any past conversation to revisit                    │
│  └─► Full Q&A thread restored with all citations               │
│  └─► Continue asking follow-up questions                       │
│                                                                │
│  STEP 9: BROWSE DOCUMENTS                                      │
│  ────────────────────────                                      │
│  User reviews available document library                       │
│  └─► Sees all documents uploaded by Admin                      │
│  └─► Can scope queries to specific documents if needed         │
│                                                                │
└────────────────────────────────────────────────────────────────┘
```

---

## Admin ↔ Normal User Interaction Diagram

```
    TIME
     │
     │   ADMIN                              NORMAL USER
     │   ─────                              ───────────
     │
     │   Sign up ◄──── (first user)
     │       │
     │   Upload docs
     │       │
     │   Verify Q&A works
     │       │
     │   Create invite ──────────────────► Receive invite link
     │       │                                    │
     │       │                              Click link + sign up
     │       │                                    │
     │   See user count                     Ask first question
     │   increase on                              │
     │   dashboard                          Get answer + sources
     │       │                                    │
     │       │                              Verify citations
     │       │                                    │
     │   Monitor queries ◄── (indirect) ──  Ask more questions
     │       │                                    │
     │   Upload more docs                   Use daily for research
     │   based on query gaps                      │
     │       │                              Browse history
     │   Invite more users                        │
     │       │                              Share findings
     │       ▼                              with team
     │   Ongoing                                  │
     │   administration                     Ongoing usage
     │                                            │
     ▼                                            ▼
```

---

## Key Touchpoints Between Roles

| Touchpoint | Admin Action | User Experience |
|------------|-------------|-----------------|
| **Invite** | Creates invite with email + role | Receives link, clicks to join |
| **Documents** | Uploads PDFs to workspace | Sees documents appear in sidebar |
| **Coverage gaps** | Monitors what users are asking | Asks questions that surface gaps |
| **Quality** | Checks confidence scores in dashboard | Sees GREEN/YELLOW/RED on answers |
| **Growth** | Invites additional team members | New colleagues join same workspace |
