# Business Model & Unit Economics

**Product:** JurisAgent by Alpha Marvin
**Last Updated:** February 15, 2026

---

## Revenue Model

JurisAgent operates a **SaaS subscription model** with a **self-hosted licensing** tier for data-sensitive customers.

### Revenue Streams

| Stream | Model | Target Customer | Price Range |
|--------|-------|----------------|-------------|
| **Cloud SaaS** | Per-user monthly subscription | Solo practitioners, small/mid firms | $49–149/mo per user |
| **Self-Hosted License** | Annual license + support contract | Government, defense, healthcare, regulated industries | $5K–25K/year per deployment |
| **Professional Services** | One-time or hourly | Enterprise clients needing custom setup | $150–250/hour |

---

## Pricing Tiers

### Cloud SaaS

| Tier | Price | Documents | Users | Features |
|------|-------|-----------|-------|----------|
| **Free Trial** | $0 for 14 days | 5 documents | 1 | Full feature access, evaluation only |
| **Solo** | $49/mo | 50 documents | 1 | Q&A, citations, confidence scoring, standard support |
| **Professional** | $99/mo per user | 200 documents | Up to 10 | Everything in Solo + playbooks, cross-document comparison, priority support |
| **Team** | $149/mo per user | Unlimited | Up to 50 | Everything in Professional + admin dashboard, user management, API access |

### Self-Hosted License

| Tier | Annual License | Support | Includes |
|------|---------------|---------|----------|
| **Standard** | $5,000/year | Email support, business hours | Docker Compose deployment, all features, quarterly updates |
| **Premium** | $15,000/year | Dedicated support, SLA | Everything in Standard + deployment assistance, custom integrations, monthly updates |
| **Enterprise** | $25,000+/year | 24/7 support, named engineer | Everything in Premium + on-premise consulting, custom model fine-tuning, audit support |

---

## Pricing Rationale

### Why These Price Points

1. **Below CoCounsel ($110–400/mo) and Lexis+ ($99–250/mo)** — We compete on price in the SMB segment where these tools are perceived as too expensive.

2. **Above commodity AI wrappers ($10–30/mo)** — We justify premium pricing through provable accuracy (0% hallucination, 4.48/5 quality) and features competitors lack (knowledge graph, self-hosted).

3. **Self-hosted premium** — Government and regulated industries have budget for on-premise tools. $5K–25K/year is a fraction of what they would pay for a single attorney's time on manual review.

### Competitive Pricing Context

| Competitor | Entry Price | Full Price | What You Get |
|-----------|-----------|-----------|-------------|
| Harvey AI | Custom | Custom | Enterprise contracts, no public pricing |
| CoCounsel | $110/mo/user | $400/mo/user | Westlaw-integrated, limited to TR ecosystem |
| Lexis+ AI | $99/mo/user | $250/mo/user | Lexis-integrated, 17% hallucination rate |
| Spellbook | $179/mo/user | $179/mo/user | Contracts only |
| Superlegal | $999/mo | $999/mo | Human-in-the-loop, slow turnaround |
| **JurisAgent** | **$49/mo/user** | **$149/mo/user** | **0% hallucination, knowledge graph, self-hosted option** |

---

## Unit Economics

### Cost Structure (Per User, Cloud SaaS)

| Cost Component | Monthly Cost | Notes |
|---------------|-------------|-------|
| LLM API (Claude) | $0.50–2.00 | ~20 queries/day average, varies by question complexity |
| Embedding API (Voyage AI) | $0.05–0.10 | Query embeddings, minimal per-query cost |
| Vector DB (Qdrant) | $0.10–0.30 | Shared infrastructure, scales with document count |
| PostgreSQL | $0.05–0.10 | Shared database |
| Compute (Azure VM) | $0.50–1.00 | Shared across users, amortized |
| **Total COGS per user** | **$1.20–3.50** | |

### Margin Analysis

| Metric | Solo ($49) | Professional ($99) | Team ($149) |
|--------|-----------|-------------------|-------------|
| Revenue per user | $49 | $99 | $149 |
| COGS per user | ~$2 | ~$3 | ~$3.50 |
| Gross profit per user | $47 | $96 | $145.50 |
| **Gross margin** | **96%** | **97%** | **98%** |

*Note: Gross margins are high because LLM costs are low per-query and infrastructure is shared. The primary costs are human (engineering, sales, support) which are below the gross margin line.*

### Customer Acquisition Economics

| Metric | SMB (Inbound) | Mid-Market (Outbound) | Enterprise (Self-Hosted) |
|--------|-------------|---------------------|------------------------|
| CAC | $300–500 | $1,000–2,500 | $5,000–10,000 |
| ARPU (monthly) | $49–99 | $99–149 | $1,000–2,000 |
| Payback period | 5–10 months | 10–17 months | 5–10 months |
| Expected retention | 18 months | 24 months | 36 months |
| LTV | $900–1,800 | $2,400–3,600 | $36,000–72,000 |
| **LTV:CAC** | **1.8–6.0x** | **1.4–3.6x** | **3.6–14.4x** |

*Target: LTV:CAC > 3x across all segments by Month 18.*

---

## TAM / SAM / SOM

### Total Addressable Market (TAM)

**$10.8B** — Global legal AI market by 2030 (projected at ~30% CAGR from $3B in 2025).

Includes: Document analysis, legal research, contract management, litigation analytics, compliance automation.

### Serviceable Addressable Market (SAM)

**$2.1B** — The subset we can realistically serve:

| Segment | Firms/Teams | Avg Users | Price Assumption | Annual Value |
|---------|------------|-----------|-----------------|-------------|
| US small law firms (2–20 attorneys) | 80,000 | 5 | $99/mo | $475M |
| US mid-size firms (20–100 attorneys) | 12,000 | 20 | $149/mo | $429M |
| US in-house legal teams | 25,000 | 5 | $99/mo | $149M |
| UK/EU law firms (SMB) | 40,000 | 5 | $99/mo | $238M |
| India law firms (growing market) | 30,000 | 3 | $49/mo | $53M |
| Government legal departments (US) | 5,000 | 10 | Self-hosted $15K/yr | $75M |
| Compliance teams (regulated industries) | 15,000 | 5 | $99/mo | $89M |
| **Total SAM** | | | | **~$1.5B** |

*Conservatively rounded to $2.1B including adjacent markets (legal education, legal process outsourcing, paralegal tools).*

### Serviceable Obtainable Market (SOM)

**Year 1: $600K** (500 users at ~$100/mo)
**Year 3: $12M** (10,000 users at ~$100/mo)

This represents less than 1% of SAM — achievable with a small team focused on the right segments.

---

## Revenue Projections

### Year 1 (Post-Launch)

| Quarter | New Users | Total Users | MRR | ARR Run Rate |
|---------|----------|-------------|-----|-------------|
| Q1 | 50 | 50 | $5K | $60K |
| Q2 | 100 | 150 | $15K | $180K |
| Q3 | 150 | 280 | $28K | $336K |
| Q4 | 220 | 500 | $50K | $600K |

*Assumptions: 10% monthly churn in Q1–Q2 (beta), improving to 5% by Q4. Blended ARPU $100/mo.*

### Year 2

| Quarter | New Users | Total Users | MRR | ARR Run Rate |
|---------|----------|-------------|-----|-------------|
| Q5 | 300 | 750 | $75K | $900K |
| Q6 | 350 | 1,000 | $100K | $1.2M |
| Q7 | 400 | 1,300 | $130K | $1.56M |
| Q8 | 500 | 2,000 | $200K | $2.4M |

### Year 3

| Target | Value |
|--------|-------|
| Total users | 10,000 |
| MRR | $1M |
| ARR | $12M |
| Self-hosted enterprise deals | 10+ |
| Gross margin | 88% |

---

## Key Assumptions & Risks

### Assumptions

| Assumption | Basis |
|-----------|-------|
| Legal AI adoption continues to grow | 23% to 52% in one year; no signs of slowing |
| SMB firms are underserved | No affordable option between free ChatGPT and $250/mo Lexis+ |
| Self-hosted is a genuine differentiator | Government/defense buyers confirm this in interviews |
| LLM costs continue to decrease | Claude API pricing has dropped 3x in 18 months |
| Accuracy is the primary buying criterion | Survey data: 78% of lawyers rank accuracy as #1 concern |

### Risks

| Risk | Mitigation |
|------|-----------|
| Big player launches affordable tier | Our knowledge graph + self-hosted are structural advantages they cannot easily replicate |
| LLM provider pricing increase | Swappable LLM client supports multiple providers; can switch to open-source models |
| Slow enterprise sales cycle | Focus on SMB self-serve first; enterprise is a bonus, not a dependency |
| Regulatory change (AI in legal) | Our grounding validation positions us well for any "AI transparency" requirements |
| Single-founder risk | Hiring plan addresses this; advisor involvement mitigates |
