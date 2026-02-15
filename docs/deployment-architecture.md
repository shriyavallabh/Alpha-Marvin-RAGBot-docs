# SaaS Deployment Architecture

**Product:** Alpha Marvin / JurisAgent
**Deployment Model:** SaaS (Cloud-Hosted)
**Last Updated:** February 15, 2026
**Owner:** Vallabh Pethkar

---

## Decision: SaaS for Beta

**Chosen Model:** SaaS (cloud-hosted, single-tenant Azure VM)

**Rationale:**

1. **Fastest path to demo** — PM and beta testers access via URL + login, zero setup on their side
2. **Single deployment to maintain** — One VM, one set of services, one place to debug
3. **Centralized updates** — Bug fixes and features ship instantly, no customer-side coordination
4. **Cost-effective for beta** — One Azure VM (~$60/month) serves both developers (India + US)
5. **Self-hosted remains an option** — Docker Compose already exists; private hosting can be offered later without rearchitecting

**What this means for users:** They open a URL, sign in with Clerk, and start uploading documents. No installation, no Docker, no API keys.

---

## Deployment Diagram

```
                        SAAS DEPLOYMENT ARCHITECTURE
                        ============================

    ┌─────────────────────────────────────────────────────────────┐
    │                     USER DEVICES                            │
    │                                                             │
    │   [Developer - India]          [Developer - US]             │
    │   [PM - Review Access]         [Beta Testers]               │
    │         │                           │                       │
    └─────────┼───────────────────────────┼───────────────────────┘
              │         HTTPS :443        │
              └─────────────┬─────────────┘
                            │
    ┌───────────────────────▼───────────────────────────────────┐
    │                    AZURE VM (B2ms)                         │
    │                    Ubuntu 24.04 LTS                        │
    │                    2 vCPU / 8 GB RAM                       │
    │                                                           │
    │  ┌────────────────────────────────────────────────────┐   │
    │  │              nginx (reverse proxy)                  │   │
    │  │              SSL termination, HTTPS :443            │   │
    │  │              Let's Encrypt certificate              │   │
    │  └──────────┬─────────────────────┬───────────────────┘   │
    │             │                     │                       │
    │     /       │              /api   │                       │
    │             ▼                     ▼                       │
    │  ┌──────────────────┐  ┌──────────────────┐              │
    │  │  Next.js :3000   │  │  FastAPI :8000   │              │
    │  │  (Frontend)      │  │  (Backend API)   │              │
    │  │                  │  │                  │              │
    │  │  systemd:        │  │  systemd:        │              │
    │  │  ragbot-frontend │  │  ragbot-backend  │              │
    │  └──────────────────┘  └────────┬─────────┘              │
    │                                 │                        │
    │  ┌──────────────────────────────┼────────────────────┐   │
    │  │            DOCKER COMPOSE SERVICES                │   │
    │  │                              │                    │   │
    │  │  ┌───────────┐  ┌───────────▼──┐  ┌───────────┐  │   │
    │  │  │PostgreSQL │  │   Qdrant     │  │  Neo4j    │  │   │
    │  │  │  :5432    │  │   :6333      │  │  :7687    │  │   │
    │  │  │           │  │              │  │           │  │   │
    │  │  │ Users     │  │ Vectors      │  │ Entities  │  │   │
    │  │  │ Docs      │  │ (1024-dim)   │  │ Relations │  │   │
    │  │  │ Convos    │  │ Voyage AI    │  │ Graph     │  │   │
    │  │  │ Invites   │  │ embeddings   │  │ Traversal │  │   │
    │  │  │ Audit     │  │              │  │           │  │   │
    │  │  └───────────┘  └──────────────┘  └───────────┘  │   │
    │  └───────────────────────────────────────────────────┘   │
    │                                                           │
    │  ┌────────────────────────────────────────────────────┐   │
    │  │          anthropic-max-router :3456                 │   │
    │  │          (LLM proxy for Claude Max token)          │   │
    │  │          systemd: anthropic-router                  │   │
    │  └────────────────────────────────────────────────────┘   │
    │                                                           │
    └───────────────────────────────────────────────────────────┘
                            │
              ┌─────────────┼─────────────┐
              │             │             │
              ▼             ▼             ▼
    ┌──────────────┐ ┌───────────┐ ┌───────────┐
    │ Anthropic API│ │ Voyage AI │ │   Clerk   │
    │ Claude Opus  │ │ Embeddings│ │   Auth    │
    │ + Vision     │ │ 1024-dim  │ │   JWT     │
    └──────────────┘ └───────────┘ └───────────┘
       EXTERNAL SERVICES (API calls over HTTPS)
```

---

## Docker Compose Service Map

| Service | Port | Image | Data Volume | Purpose |
|---------|------|-------|-------------|---------|
| PostgreSQL 16 | 5432 | `postgres:16-alpine` | `/var/lib/postgresql/data` | Relational data (users, docs, conversations, invites, audit) |
| Qdrant | 6333 | `qdrant/qdrant:latest` | `/qdrant/storage` | Vector embeddings (1024-dim Voyage AI) |
| Neo4j 5 | 7687 | `neo4j:5-community` | `/data`, `/logs` | Knowledge graph (entities, relationships) |

**Non-Docker services (systemd-managed):**

| Service | Port | Runtime | Unit File |
|---------|------|---------|-----------|
| FastAPI Backend | 8000 | Python 3.11 / uvicorn | `ragbot-backend.service` |
| Next.js Frontend | 3000 | Node.js 18 / next start | `ragbot-frontend.service` |
| Anthropic Router | 3456 | Node.js 18 | `anthropic-router.service` |
| nginx | 443/80 | System package | `nginx.service` |

---

## Request Lifecycle

A user asks a question — here is exactly what happens:

```
Step 1: AUTHENTICATION
──────────────────────
Browser → HTTPS :443 → nginx → FastAPI :8000
                                    │
                                    ▼
                          Clerk JWT Verification
                          Extract: user_id, tenant_id, role
                          (from public_metadata)
                                    │
                                    ▼
                          Tenant Isolation Gate
                          All DB queries scoped to tenant_id

Step 2: RETRIEVAL
─────────────────
                          Query Classification
                          (factual / analytical / procedural)
                                    │
                                    ▼
                          Query Enrichment via Claude
                          (expand legal terminology)
                                    │
                                    ▼
                    ┌───────────────┼───────────────┐
                    ▼               ▼               ▼
              Qdrant Vector   BM25 Keyword    Neo4j Graph
              Search          Search          Traversal
                    │               │               │
                    └───────────────┼───────────────┘
                                    │
                                    ▼
                          RRF Fusion (merge 3 channels)
                                    │
                                    ▼
                          Cross-Encoder Reranking
                          (ms-marco-MiniLM-L-6-v2)
                                    │
                                    ▼
                          Top-K Chunks Selected

Step 3: GENERATION
──────────────────
                          Claude Opus 4.5
                          (via anthropic-max-router :3456)
                                    │
                                    ▼
                          SSE Stream to Browser
                          Events: "sources" → "text" → "grounding"

Step 4: VALIDATION
──────────────────
                          Grounding Check
                          confidence: 0.0–1.0
                          hallucination_risk: LOW/MEDIUM/HIGH
                          is_grounded: true/false
                                    │
                                    ▼
                          Color-coded Confidence Badge
                          GREEN  (≥0.75+LOW or ≥0.80+MEDIUM)
                          YELLOW (≥0.50+LOW/MED or ≥0.65+HIGH)
                          RED    (everything else)
                          (Raw % not shown to user)
```

---

## SaaS RACI with Effort Estimates

| Activity | DEV | PM | Effort (hrs) | Notes |
|----------|-----|-----|-------------|-------|
| **Infrastructure** | | | | |
| Provision Azure VM | R/A | I | 2 | Already done |
| Configure nginx + SSL | R/A | I | 2 | Already done |
| Docker Compose setup | R/A | I | 3 | Already done |
| Monitor uptime | R/A | C | Ongoing | Weekly check |
| **Security** | | | | |
| Clerk JWT integration | R/A | I | 4 | Already done |
| Tenant data isolation | R/A | C | 6 | Already done |
| API key rotation | R/A | I | 1 | As needed |
| **Deployment** | | | | |
| CI/CD pipeline | R/A | I | 4 | GitHub Actions |
| Zero-downtime deploys | R/A | I | 3 | Planned |
| Backup strategy | R/A | C | 4 | Planned |
| **Operations** | | | | |
| User onboarding (invites) | R | A | 2 | Admin sends invite link |
| Bug triage and fixes | R/A | C | Ongoing | Per-issue |
| Performance monitoring | R/A | I | 2 | Setup + ongoing |

---

## Timeline: SaaS Beta Deployment

| Task | Owner | Effort (hrs) | Target Date | Dependencies | Status |
|------|-------|-------------|-------------|--------------|--------|
| Azure VM provisioned | DEV | 2 | Jan 30 | None | DONE |
| Docker services running | DEV | 3 | Jan 31 | VM ready | DONE |
| Backend deployed | DEV | 4 | Feb 1 | Docker services | DONE |
| Frontend deployed | DEV | 3 | Feb 1 | Backend running | DONE |
| HTTPS + nginx configured | DEV | 2 | Feb 2 | Frontend deployed | DONE |
| Clerk auth integrated | DEV | 4 | Feb 3 | HTTPS working | DONE |
| First document ingested | DEV | 2 | Feb 4 | All services running | DONE |
| 84 documents ingested | DEV | 8 | Feb 5 | Ingestion pipeline | DONE |
| 1308-question benchmark generated | DEV | 6 | Feb 5 | Documents ingested | DONE |
| Benchmark evaluation (API mode) | DEV | 4 | Feb 6 | Benchmark ready | DONE |
| Admin module completion | DEV | 8 | Feb 10 | Auth working | DONE |
| Accept invite endpoint | DEV | 3 | Feb 10 | Invite model | DONE |
| User management endpoints | DEV | 6 | Feb 12 | Auth + DB models | DONE |
| PM review and feedback | PM | 2 | Feb 9 | Docs site live | DONE |
| Bug fixes from PM feedback | DEV | 8 | Feb 13 | PM review | DONE |
| OCR text cleanup + Qdrant reclean | DEV | 12 | Feb 11 | Evaluation results | DONE |
| Grounding calibration (9 rounds) | DEV | 16 | Feb 14 | OCR cleanup | DONE |
| SSE protocol fix | DEV | 6 | Feb 12 | E2E testing | DONE |
| Beta acceptance sign-off | PM | 2 | Feb 15 | All above | DONE |

---

## Cost Breakdown (Monthly)

| Item | Cost | Notes |
|------|------|-------|
| Azure VM (B2ms) | ~$60 | 2 vCPU, 8 GB RAM, 30 GB SSD |
| Anthropic API (Claude Opus 4.5) | ~$0* | Via Claude Max subscription (setup-token) |
| Voyage AI (Embeddings) | ~$5 | Pay-per-use, minimal for beta volume |
| Clerk (Authentication) | $0 | Free tier (10K MAU) |
| Domain + SSL | $0 | Let's Encrypt (free) |
| **Total (Beta)** | **~$65/month** | |

*Production swap to Anthropic API key will add usage-based LLM costs (~$15/1M input tokens for Opus).

---

## Security Considerations

| Concern | Mitigation |
|---------|-----------|
| Data in transit | HTTPS everywhere (TLS 1.3 via Let's Encrypt) |
| Data at rest | PostgreSQL on encrypted Azure disk; Qdrant/Neo4j on same disk |
| Authentication | Clerk JWT with RS256 verification; no password storage |
| Authorization | Role-based (admin/member) checked at every endpoint |
| Tenant isolation | All queries filtered by `tenant_id` across all 3 data stores |
| API keys | Stored in `.env` on VM, not in source control; rotated as needed |
| Network | Azure NSG restricts inbound to 443 (HTTPS) and 22 (SSH, key-only) |
| LLM data | Anthropic does not train on API data; Voyage AI same policy |
