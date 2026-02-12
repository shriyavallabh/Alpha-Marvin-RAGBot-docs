# Admin Module & Role-Based Workflows

**Product:** Alpha Marvin / JurisAgent
**Last Updated:** February 7, 2026
**Owner:** Vallabh Pethkar

---

## At a Glance

<div markdown="block" style="max-width:720px;margin:1.5rem auto;font-family:'Inter',-apple-system,sans-serif;">
<div style="display:grid;grid-template-columns:repeat(3,1fr);gap:16px;">

<!-- Card 1: User Management -->
<div style="background:#ffffff;border-radius:12px;padding:20px;box-shadow:0 1px 3px rgba(0,0,0,0.1);border-top:3px solid #E8726A;">
<div style="font-size:24px;margin-bottom:8px;">👥</div>
<div style="font-weight:700;font-size:14px;color:#1a1a1a;margin-bottom:10px;">User Management</div>
<div style="font-size:12px;color:#6b7280;line-height:1.7;">
• Add users via invite links<br>
• Assign roles (Admin / Member)<br>
• Deactivate user accounts<br>
• View user list &amp; status
</div>
</div>

<!-- Card 2: Document Management -->
<div style="background:#ffffff;border-radius:12px;padding:20px;box-shadow:0 1px 3px rgba(0,0,0,0.1);border-top:3px solid #E8726A;">
<div style="font-size:24px;margin-bottom:8px;">📄</div>
<div style="font-weight:700;font-size:14px;color:#1a1a1a;margin-bottom:10px;">Document Management</div>
<div style="font-size:12px;color:#6b7280;line-height:1.7;">
• Upload legal PDFs<br>
• Track processing status<br>
• Delete / archive documents<br>
• View 9-stage pipeline progress
</div>
</div>

<!-- Card 3: System Monitoring -->
<div style="background:#ffffff;border-radius:12px;padding:20px;box-shadow:0 1px 3px rgba(0,0,0,0.1);border-top:3px solid #E8726A;">
<div style="font-size:24px;margin-bottom:8px;">📊</div>
<div style="font-weight:700;font-size:14px;color:#1a1a1a;margin-bottom:10px;">System Monitoring</div>
<div style="font-size:12px;color:#6b7280;line-height:1.7;">
• View dashboard statistics<br>
• Monitor system health<br>
• Identify knowledge gaps<br>
• Track query patterns
</div>
</div>

</div>
</div>

---

## Two User Types

JurisAgent defines two distinct user roles with separate workflows:

| | Admin | Normal User (Member) |
|---|-------|---------------------|
| **Who** | Workspace owner, sets up the system | Legal professional, queries documents |
| **Primary actions** | Upload documents, invite users, monitor system | Ask questions, review answers, browse sources |
| **Access level** | Full system access + admin dashboard | Document query access only |
| **How they join** | First user / designated by org | Receives invite link from Admin |
| **Role in Clerk** | `public_metadata.role = "admin"` | `public_metadata.role = "member"` |

---

## Admin Complete Workflow

```
ADMIN JOURNEY
=============

┌─────────────────────────────────────────────────────────┐
│  PHASE 1: INITIAL SETUP (one-time)                      │
│                                                         │
│  1. Admin signs up via Clerk                            │
│     └─► Account created with role = "admin"             │
│                                                         │
│  2. Admin accesses JurisAgent dashboard                 │
│     └─► Sees empty workspace: 0 docs, 0 users          │
│                                                         │
│  3. Admin uploads legal documents                       │
│     └─► Drag-and-drop PDFs                              │
│     └─► 9-stage pipeline processes each document:       │
│         Parse → Dedup → Chunk → Enrich → Extract        │
│         → Embed → Vector Store → Database → Graph       │
│     └─► Documents appear in sidebar when complete       │
│                                                         │
└─────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────┐
│  PHASE 2: USER ONBOARDING                               │
│                                                         │
│  4. Admin opens Admin Dashboard (/admin)                │
│     └─► Views system stats (docs, chunks, vectors,      │
│         conversations, users)                           │
│                                                         │
│  5. Admin creates invite                                │
│     └─► Enters email + role (admin or member)           │
│     └─► System generates unique invite token            │
│     └─► Token valid for 7 days                          │
│     └─► Admin shares invite link with user              │
│                                                         │
└─────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────┐
│  PHASE 3: ONGOING ADMINISTRATION                        │
│                                                         │
│  6. Admin monitors system health                        │
│     └─► Admin Dashboard: document count, query volume   │
│     └─► Checks ingestion status for new uploads         │
│                                                         │
│  7. Admin uploads additional documents as needed        │
│     └─► Same drag-and-drop workflow                     │
│     └─► Documents added to existing corpus              │
│                                                         │
│  8. Admin reviews and manages workspace                 │
│     └─► Views conversation history                      │
│     └─► Uploads more docs based on user queries         │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## Normal User Complete Workflow

```
NORMAL USER JOURNEY
===================

┌─────────────────────────────────────────────────────────┐
│  PHASE 1: ONBOARDING                                    │
│                                                         │
│  1. User receives invite link from Admin                │
│     └─► Link contains unique token                      │
│     └─► User clicks link → lands on /invite page        │
│                                                         │
│  2. User signs up via Clerk                             │
│     └─► Account created with role = "member"            │
│     └─► Automatically joined to workspace               │
│                                                         │
└─────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────┐
│  PHASE 2: FIRST QUERY SESSION                           │
│                                                         │
│  3. User sees chat interface                            │
│     └─► Document sidebar shows available docs           │
│     └─► Chat input at bottom of screen                  │
│                                                         │
│  4. User types a natural language question              │
│     └─► Example: "What are the termination clauses      │
│         in the NDA?"                                    │
│                                                         │
│  5. System retrieves and generates answer               │
│     └─► Searches across all ingested documents          │
│     └─► Streams answer token-by-token (SSE)             │
│     └─► Displays [Source N] citations inline            │
│     └─► Shows confidence indicator (GREEN/YELLOW/RED)   │
│                                                         │
│  6. User reviews sources                                │
│     └─► Clicks citation → source chunk viewer opens     │
│     └─► Sees exact text passage supporting the claim    │
│                                                         │
└─────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────┐
│  PHASE 3: DAILY USAGE                                   │
│                                                         │
│  7. User asks follow-up questions                       │
│     └─► Conversation context preserved                  │
│     └─► Can start new conversations                     │
│                                                         │
│  8. User browses conversation history                   │
│     └─► Past conversations listed in sidebar            │
│     └─► Click to revisit any previous Q&A               │
│                                                         │
│  9. User browses document list                          │
│     └─► Views all documents available for querying      │
│     └─► Sees document names, page counts                │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## Feature Availability by Role

| Feature | Admin | Normal User |
|---------|:-----:|:-----------:|
| **Document Operations** | | |
| Upload documents | Yes | No |
| View document list | Yes | Yes |
| Delete documents | Yes | No |
| **Query Operations** | | |
| Ask questions | Yes | Yes |
| View streamed answers | Yes | Yes |
| View source chunks | Yes | Yes |
| See confidence indicators | Yes | Yes |
| **Conversation** | | |
| Start new conversations | Yes | Yes |
| View conversation history | Yes | Yes (own only) |
| **Administration** | | |
| Access Admin Dashboard (/admin) | Yes | No (403) |
| View system stats | Yes | No |
| Create invite links | Yes | No |
| View pending invites | Yes | No |
| Manage users | Yes* | No |
| View audit logs | Yes* | No |

*Planned — not yet implemented.

---

## Admin ↔ Normal User Interaction

```
┌──────────────────┐                    ┌──────────────────┐
│                  │                    │                  │
│      ADMIN       │                    │   NORMAL USER    │
│                  │                    │                  │
│  1. Uploads docs │                    │                  │
│         │        │                    │                  │
│         ▼        │                    │                  │
│  2. Creates      │   Invite Link      │                  │
│     invite  ─────┼───────────────────►│  3. Accepts      │
│                  │                    │     invite       │
│                  │                    │         │        │
│                  │                    │         ▼        │
│                  │                    │  4. Asks          │
│                  │                    │     questions     │
│                  │                    │         │        │
│  5. Monitors     │                    │         ▼        │
│     usage via    │◄───── (indirect) ──│  5. Gets answers  │
│     dashboard    │                    │     with sources  │
│                  │                    │                  │
│  6. Uploads more │                    │  6. Reviews       │
│     docs based   │                    │     sources,      │
│     on queries   │                    │     asks more     │
│                  │                    │                  │
└──────────────────┘                    └──────────────────┘
```

---

## Current Implementation Status

### Built and Working

| Component | Location | Details |
|-----------|----------|---------|
| Admin stats endpoint | `backend/api/routes/admin.py` | `GET /api/admin/stats` — returns doc count, chunk count, vector count, conversation count, user count |
| Admin role check | `backend/api/clerk_auth.py` | Extracts role from Clerk JWT `public_metadata.role`; returns 403 for non-admins |
| Admin Dashboard UI | `frontend/src/app/admin/page.tsx` | 5 stat cards with icons, loading states, error handling |
| Invite creation | `backend/api/routes/invites.py` | `POST /api/invites` — email + role, generates token, 7-day expiry |
| List pending invites | `backend/api/routes/invites.py` | `GET /api/invites` — returns pending invites for tenant |
| Invite page (frontend) | `frontend/src/app/invite/page.tsx` | Token extraction, validation UI, "Accept" button (non-functional) |
| DB models | `backend/storage/models.py` | `User`, `Invite`, `AuditLog` models fully defined |

### Not Yet Built

| Component | Effort Estimate | Priority | Notes |
|-----------|----------------|----------|-------|
| Accept invite endpoint | 3 hrs | HIGH | `POST /api/invites/{token}/accept` — verify token, create user, update accepted_at |
| Frontend invite API functions | 2 hrs | HIGH | Add `createInvite()`, `listInvites()`, `acceptInvite()` to api.ts |
| Admin invite management UI | 4 hrs | HIGH | Create invites, view pending, copy link — on /admin page |
| User list endpoint | 3 hrs | MEDIUM | `GET /api/admin/users` — list workspace members |
| User role change endpoint | 2 hrs | MEDIUM | `PATCH /api/admin/users/{id}` — change role |
| User deactivation endpoint | 2 hrs | MEDIUM | `DELETE /api/admin/users/{id}` — soft deactivate |
| Audit log recording | 4 hrs | LOW | Middleware to log admin actions to AuditLog table |
| Audit log viewing endpoint | 2 hrs | LOW | `GET /api/admin/audit` — paginated audit log |
| User management UI | 6 hrs | MEDIUM | List users, change roles, deactivate — on /admin page |

**Total remaining effort: ~28 hours**
