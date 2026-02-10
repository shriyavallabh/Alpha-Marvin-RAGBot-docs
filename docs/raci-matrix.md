# RACI Matrix

**Product:** Alpha Marvin / JurisAgent
**Last Updated:** February 4, 2026

**Legend:**
- **R** = Responsible (does the work)
- **A** = Accountable (owns the outcome)
- **C** = Consulted (provides input)
- **I** = Informed (kept in the loop)

---

## Roles

| Abbreviation | Role | Person |
|-------------|------|--------|
| DEV | Developer / Technical Lead | Vallabh Pethkar |
| PM | Product Manager / Senior Advisor | PM (30+ years experience) |
| USER | Beta Tester / End User | Beta participants |

---

## Model 1: SaaS (Cloud-Hosted) — RECOMMENDED

The platform is hosted on cloud infrastructure managed by the product team. Users access via browser.

| Activity | DEV | PM | USER |
|----------|-----|-----|------|
| **Infrastructure & Deployment** | | | |
| Provision cloud VM (Azure) | R/A | I | - |
| Configure HTTPS, DNS, nginx | R/A | I | - |
| Deploy Docker services | R/A | I | - |
| Monitor uptime and performance | R/A | C | I |
| Apply security patches | R/A | I | - |
| Scale infrastructure | R | A | I |
| **Application Development** | | | |
| Backend development (Python/FastAPI) | R/A | C | - |
| Frontend development (Next.js) | R/A | C | - |
| API design and implementation | R/A | C | - |
| Bug fixes and maintenance | R/A | I | I |
| **Data & AI Pipeline** | | | |
| LLM configuration (Claude Opus 4.5) | R/A | I | - |
| Embedding model selection/tuning | R/A | C | - |
| Chunking strategy optimization | R/A | C | - |
| Knowledge graph maintenance | R/A | I | - |
| Evaluation suite and quality metrics | R/A | C | - |
| **Product & Business** | | | |
| Product roadmap and priorities | R | A | C |
| Feature scoping and requirements | C | A | C |
| Pricing model decisions | C | A | I |
| Go-to-market strategy | C | A | I |
| User onboarding documentation | R | A | C |
| **User Operations** | | | |
| Document upload and management | I | I | R/A |
| Question asking and answer review | I | I | R/A |
| Feedback and bug reporting | I | I | R/A |
| Account management | R | I | A |
| **Security & Compliance** | | | |
| Data encryption (at rest, in transit) | R/A | C | I |
| Authentication system (Clerk JWT) | R/A | I | - |
| Tenant data isolation | R/A | C | - |
| API key management | R/A | I | - |
| Compliance certifications (future) | R | A | I |

### SaaS Pros
- Lowest barrier for users (just a URL + login)
- Centralized updates and bug fixes
- Single infrastructure to monitor
- Easier to demonstrate to PM

### SaaS Cons
- Data leaves the user's control (mitigated by tenant isolation + encryption)
- Cloud costs scale with usage
- Requires reliable internet connectivity

### SaaS Deployment — Detailed Timeline

Granular task-level breakdown for the SaaS beta deployment. For the full deployment architecture (diagrams, request lifecycle, cost breakdown), see [SaaS Deployment Architecture](deployment-architecture.md).

| Task | DEV | PM | Effort (hrs) | Target Date | Status |
|------|-----|-----|-------------|-------------|--------|
| Provision Azure VM (B2ms) | R/A | I | 2 | Jan 30 | DONE |
| Install Docker, Docker Compose | R/A | I | 1 | Jan 30 | DONE |
| Configure Docker Compose (PG, Qdrant, Neo4j) | R/A | I | 3 | Jan 31 | DONE |
| Deploy FastAPI backend (systemd) | R/A | I | 2 | Feb 1 | DONE |
| Deploy Next.js frontend (systemd) | R/A | I | 2 | Feb 1 | DONE |
| Configure nginx + Let's Encrypt SSL | R/A | I | 2 | Feb 2 | DONE |
| Integrate Clerk JWT authentication | R/A | I | 4 | Feb 3 | DONE |
| Configure anthropic-max-router proxy | R/A | I | 2 | Feb 3 | DONE |
| First document upload + query test | R/A | C | 2 | Feb 4 | DONE |
| Batch ingest 84 documents | R/A | I | 8 | Feb 5 | DONE |
| Generate 1308-question benchmark | R/A | I | 6 | Feb 5 | DONE |
| Run benchmark evaluation (API mode) | R/A | C | 4 | Feb 6 | DONE |
| Analyze benchmark results (LLM judge) | R/A | C | 4 | Feb 6 | DONE |
| Create documentation site (MkDocs) | R/A | C | 4 | Feb 4 | DONE |
| Write PM-facing docs (5 new docs) | R/A | C | 8 | Feb 7 | IN PROGRESS |
| Complete admin module (accept invite, user mgmt) | R/A | I | 14 | Feb 12 | PLANNED |
| PM review of live system + docs | C | R/A | 2 | Feb 9 | PLANNED |
| Bug fixes from PM feedback | R/A | C | 8 | Feb 13 | PLANNED |
| Performance testing | R/A | I | 4 | Feb 13 | PLANNED |
| Beta acceptance sign-off | C | R/A | 2 | Feb 15 | PLANNED |
| **Total** | | | **84** | | |

---

## Model 2: Private Hosting (Self-Hosted)

The customer deploys the platform on their own infrastructure. Product team provides the software and support.

| Activity | DEV | PM | USER |
|----------|-----|-----|------|
| **Infrastructure & Deployment** | | | |
| Provision VM / bare metal | C | I | R/A |
| Install Docker and dependencies | C | I | R/A |
| Configure HTTPS, DNS, firewall | C | I | R/A |
| Deploy Docker services | C | I | R/A |
| Monitor uptime and performance | C | I | R/A |
| Apply security patches (OS level) | I | I | R/A |
| Apply application updates | R | I | A |
| **Application Development** | | | |
| Backend development | R/A | C | - |
| Frontend development | R/A | C | - |
| Release packaging and versioning | R/A | I | I |
| Installation documentation | R/A | C | I |
| **Data & AI Pipeline** | | | |
| LLM API key provisioning | C | I | R/A |
| Embedding model setup | R | I | A |
| Data stays on customer infrastructure | - | I | R/A |
| **Product & Business** | | | |
| Product roadmap | R | A | C |
| License management | C | A | I |
| Support SLA definition | C | A | I |
| **Security & Compliance** | | | |
| Network security | I | I | R/A |
| Data governance | C | I | R/A |
| Compliance certifications | C | A | R |

### Private Hosting Pros
- Full data sovereignty (critical for regulated industries)
- No dependency on vendor infrastructure
- Customer controls access and security

### Private Hosting Cons
- Customer needs DevOps expertise
- Slower updates (manual deployment)
- Higher support burden for product team
- Each deployment is a unique environment to debug

---

## Model 3: API-Only

The platform is offered as an API service. Customers build their own UI or integrate into existing systems.

| Activity | DEV | PM | USER |
|----------|-----|-----|------|
| **Infrastructure & Deployment** | | | |
| Host API infrastructure | R/A | I | - |
| API gateway and rate limiting | R/A | C | I |
| API documentation | R/A | C | I |
| SDK/client library development | R/A | C | I |
| **Application Development** | | | |
| Backend API development | R/A | C | - |
| Frontend development | - | - | R/A |
| API versioning and backwards compatibility | R/A | C | I |
| **Data & AI Pipeline** | | | |
| Pipeline as a service | R/A | I | I |
| Customer document isolation | R/A | C | I |
| **Product & Business** | | | |
| API pricing (per call / per document) | C | A | I |
| Developer relations | C | A | I |
| API usage analytics | R/A | C | I |
| **Integration** | | | |
| Customer UI development | C | I | R/A |
| Customer system integration | C | I | R/A |
| Webhook configuration | R | I | A |

### API-Only Pros
- Maximum flexibility for customers
- Scales independently of UI decisions
- Lower surface area to maintain (no frontend)
- Appeals to technical buyers who want integration

### API-Only Cons
- Customer must build their own UI
- Higher integration effort for customer
- Harder to demo (no visual product)
- Support requests are more technical

---

## Recommended Approach for Beta

**Primary: SaaS (Model 1)**

Rationale:
1. Fastest path to a working demo (Feb 15 deadline)
2. PM can access and review without any setup
3. Both developers can access from India and US
4. Single deployment to maintain during POC
5. Easiest to demonstrate to potential customers

**Secondary: Private Hosting documentation (Model 2)**

Rationale:
1. "Self-hostable" is a key differentiator vs. Harvey/CoCounsel/Lexis+
2. Docker Compose setup already exists
3. Documentation effort is low
4. Appeals to regulated industries (law firms, government)

**Deferred: API-Only (Model 3)**

Rationale:
1. Requires API gateway, rate limiting, SDK development
2. No visual demo for PM review
3. Better suited for Phase 4 when product is mature

---

## Review Cadence

| Milestone | Frequency | Participants |
|-----------|-----------|-------------|
| Pre-Beta (now – Feb 15) | Bi-weekly | DEV + PM |
| Beta (Feb 15 – Mar 15) | Weekly | DEV + PM + USERs |
| Post-Beta / Phase 3 | Bi-weekly | DEV + PM |
| Pre-Launch | Weekly | DEV + PM + stakeholders |
