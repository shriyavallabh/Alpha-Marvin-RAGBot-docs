# Investor Pitch Deck

**Product:** JurisAgent by Alpha Marvin
**Last Updated:** February 15, 2026

This page contains the content for each slide of the investor pitch deck. Use this as the source material for designing presentation slides (PowerPoint, Keynote, Google Slides, or Figma).

---

## Slide 1: Cover

**JurisAgent by Alpha Marvin**

The AI Workspace for Turning Legal Documents into Trusted Intelligence

*"The only legal AI with per-answer hallucination detection"*

---

## Slide 2: The Problem

### Lawyers Cannot Trust AI — And It's Costing Them

**The manual way is expensive:**

- Attorneys spend 60–70% of their time reading documents
- A single contract review: 8–12 hours at $300–500/hour
- Due diligence for a mid-size M&A: 500–2,000 attorney hours

**Existing AI tools are unreliable:**

- Lexis+ AI hallucination rate: **17%**
- Westlaw AI hallucination rate: **34%**
- GPT-4 without RAG: **58–82%**

**The consequences are real:**

- **120+** court cases involving AI-fabricated citations
- **128** lawyers sanctioned for AI hallucinations
- Law firms now ban or heavily restrict AI use

**The result:** $3B+ is being spent on legal AI, but trust is broken.

---

## Slide 3: The Solution

### JurisAgent — Legal AI You Can Verify

Upload documents. Ask questions. Get answers you can trust.

**Three things no competitor does:**

1. **Grounding Validation** — Every answer gets a confidence badge (GREEN / YELLOW / RED) with hallucination risk scoring. Not a black box.

2. **Knowledge Graph** — Neo4j entity graph connects parties, clauses, dates, and definitions across documents. Enables multi-hop reasoning that vector search alone cannot do.

3. **Self-Hosted Option** — Deploy on your own infrastructure. Data never leaves your network. The only legal AI tool offering this.

---

## Slide 4: How It Works

### Three Steps to Trusted Answers

**Step 1: Upload**
Drag and drop legal documents (PDF, scanned PDFs, images, text files). The 9-stage pipeline automatically parses, chunks, embeds, and builds a knowledge graph.

**Step 2: Ask**
Type natural language questions. The system searches across 3 channels (vector similarity, synonym expansion, knowledge graph traversal), retrieves the most relevant passages, and generates a cited answer.

**Step 3: Verify**
Every answer shows:

- Inline citations ([Source 1], [Source 2]) linked to exact document sections
- Expandable source viewer with original text
- Confidence badge: GREEN (high confidence), YELLOW (verify some claims), RED (consult original documents)

---

## Slide 5: Product Demo

### What Users See

*(Include screenshots or a short video walkthrough)*

**Key screens:**

1. **Chat Interface** — Clean, focused Q&A with streaming answers
2. **Citation Pills** — Click any [Source N] to see the exact supporting text
3. **Confidence Badge** — GREEN/YELLOW/RED indicator on every answer
4. **Pipeline Stepper** — Real-time progress during document processing (9 stages)
5. **Source Viewer** — Side panel with original document chunks, relevance scores, and page numbers
6. **Admin Dashboard** — Document counts, chunk counts, conversations, user management

---

## Slide 6: Why It's Better

### Benchmark Results — 9 Rounds of Rigorous Evaluation

| Metric | JurisAgent | Harvey AI | CoCounsel | Lexis+ AI |
|--------|-----------|-----------|-----------|-----------|
| Hallucination rate | **0%** | ~5% | ~10% | 17% |
| Answer quality (composite) | **4.48/5** | N/A | N/A | N/A |
| Citation quality | **4.78/5** | N/A | N/A | N/A |
| Grounding validation | **Yes (per-answer)** | Unspecified | No | No |
| Self-hosted option | **Yes** | No | No | No |
| Knowledge graph | **Yes (Neo4j)** | No | No | No |
| Pricing | **~$60/mo infra** | Enterprise only | $110–400/user | $99–250/user |

**How we tested:** 1,308 questions generated from 71 real legal documents. Evaluated by an LLM judge on factual accuracy, completeness, citation quality, and confidence calibration. 9 iterative improvement rounds from 85% pass / 3.72 composite to 100% pass / 4.48 composite.

---

## Slide 7: Market Opportunity

### $10.8B Market by 2030

| Year | Legal AI Market Size | Growth |
|------|---------------------|--------|
| 2024 | $1.5B | — |
| 2025 | $3B+ | 2x |
| 2030 (projected) | $10.8B | ~30% CAGR |

**Adoption is accelerating:**

- Corporate legal AI adoption: **23% (2024) to 52% (2025)** — doubled in one year
- **855+** generative AI products launched in legal tech (Dec 2025)
- Harvey AI raised **$760M** in 2025 alone, valued at **$8B**
- **64%** of in-house legal teams expect to reduce outside counsel dependency

### TAM / SAM / SOM

| Segment | Size | Rationale |
|---------|------|-----------|
| **TAM** | $10.8B | Global legal AI market (2030) |
| **SAM** | $2.1B | SMB law firms + in-house legal teams in US/UK/India (firms with 1–100 attorneys not locked into Westlaw/Lexis contracts) |
| **SOM** | $12M | 1% of SAM in Year 3 — 10,000 users at ~$100/mo ARPU |

---

## Slide 8: Business Model

### SaaS + Self-Hosted Licensing

| Revenue Stream | Model | Price Range |
|---------------|-------|-------------|
| **Cloud SaaS** | Per-user monthly subscription | $49–149/mo per user |
| **Self-Hosted License** | Annual license + support | $5K–25K/year per deployment |
| **Professional Services** | Custom setup, training, integrations | $150–250/hour |

**Unit Economics (Target):**

| Metric | Target |
|--------|--------|
| Blended ARPU | ~$100/mo |
| Infrastructure cost per user | $1–3/mo |
| Gross margin | 85%+ |
| CAC (SMB) | $500–1,000 |
| LTV (24-mo retention) | $2,400 |
| LTV:CAC ratio | 2.4–4.8x |

**Revenue milestones:**

| Timeline | Users | ARR |
|----------|-------|-----|
| Year 1 | 500 | $600K |
| Year 2 | 2,000 | $2.4M |
| Year 3 | 10,000 | $12M |

---

## Slide 9: Competition

### We Win Where Trust and Cost Matter

**Competitor landscape (2x2 matrix):**

```
                     HIGH ACCURACY
                          |
                  Harvey  |  JurisAgent
                  ($8B)   |  (us)
                          |
    EXPENSIVE ————————————+———————————— AFFORDABLE
                          |
                CoCounsel |  Spellbook
                Lexis+ AI |  (contracts only)
                          |
                     LOW ACCURACY
```

**Where we win:**

- Accuracy-sensitive use cases (grounding + confidence scores)
- Multi-hop legal queries (knowledge graph)
- Data-sensitive industries (self-hosted)
- Budget-conscious firms ($60/mo infra vs $250–400/mo per user)

**Where we don't compete (yet):**

- Firms locked into Westlaw/Lexis ecosystems (switching cost too high)
- Teams needing Word integration today (Phase 4 roadmap item)
- Litigation analytics (not our focus)

---

## Slide 10: Go-to-Market Strategy

### Land with Accuracy, Expand with Workflows

**Phase 1: Pilot Program (Months 1–6)**

- 10 law firms in free/discounted pilot
- Focus: Solo practitioners, 5–20 attorney firms
- Goal: Case studies, testimonials, product-market fit validation
- Channel: Direct outreach, legal tech meetups, bar association events

**Phase 2: Self-Serve Launch (Months 6–12)**

- Public launch with freemium tier (5 documents free)
- Content marketing: "Legal AI accuracy" thought leadership
- Partnerships: Legal tech consultants, bar associations, law school clinics

**Phase 3: Enterprise & Self-Hosted (Months 12–24)**

- Enterprise sales team (1–2 AEs)
- Self-hosted licensing for government and regulated industries
- Channel partnerships: legal practice management software vendors (Clio, PracticePanther)

### Ideal Customer Profile

| Segment | Size | Pain Point | Why Us |
|---------|------|-----------|--------|
| Solo practitioners | 1 attorney | Cannot afford CoCounsel/Lexis+ | $49/mo vs $250+/mo |
| Small firms | 5–20 attorneys | Need accurate AI without enterprise contracts | Accuracy + affordability |
| In-house legal | 3–15 attorneys | Reduce outside counsel spend | Self-serve document analysis |
| Government legal | Varies | Data sovereignty requirements | Self-hosted deployment |
| Compliance teams | 2–10 people | Regulatory document analysis | Playbooks + risk assessment |

---

## Slide 11: Technology Moat

### Five Layers of Defensibility

**1. Knowledge Graph (6+ months to build)**
Neo4j entity graph with legal-specific relationship extraction. Enables multi-hop reasoning across documents. No competitor in our price segment has this.

**2. Grounding Infrastructure (9 rounds of calibration)**
Per-answer confidence scoring with 1,308-question benchmark. The evaluation framework itself is a moat — you cannot claim accuracy without building equivalent testing infrastructure.

**3. 3-Channel Hybrid Search**
Vector + Synonym + Graph search with RRF fusion and cross-encoder reranking. Architecturally complex, requires tuning across all three channels.

**4. Legal OCR Pipeline**
Claude Vision + 4-pass text cleanup with legal-specific dictionary (700K+ words including inflections, 160 legal terms, 65 acronyms, 50 Latin phrases). Tuned over 84 real legal documents.

**5. Self-Hosted Architecture**
Docker Compose deployment on a single VM. Multi-tenant from day one with tenant isolation across PostgreSQL, Qdrant, and Neo4j. No competitor offers this.

---

## Slide 12: Roadmap

### What's Built, What's Next

| Phase | Status | Key Features |
|-------|--------|-------------|
| **Phase 1** — Foundation | COMPLETE | Document upload, Q&A engine, vector search, knowledge graph |
| **Phase 2** — Quality & Accuracy | COMPLETE | Grounding validation, OCR cleanup, auto doc filtering, playbooks, Bluebook citations, 9-round benchmark |
| **Phase 3** — Workflows (Next) | PLANNED | Agentic workflows, document generation, contract redlining, portfolio dashboard |
| **Phase 4** — Integrations | PLANNED | Word/Office integration, Slack/Teams, API marketplace, SSO |
| **Phase 5** — Scale | PLANNED | Multi-region deployment, enterprise admin, audit trail, usage analytics |

---

## Slide 13: Team

| Role | Name | What They Bring |
|------|------|----------------|
| **Founder & CTO** | Vallabh Pethkar | Full-stack engineer. Built the entire platform: backend (FastAPI/Python), frontend (Next.js/React), ML pipeline, knowledge graph, deployment infrastructure. |
| **Product Advisor** | Harish | Product strategy and legal tech domain expertise. Shaped the product roadmap and feature prioritization. |

### Key Hires (Post-Funding)

| Role | Priority | Why |
|------|----------|-----|
| Head of Sales / BD | HIGH | First customer acquisition; legal industry relationships |
| Legal Domain Expert | HIGH | Customer success, playbook design, domain credibility |
| Frontend Engineer | MEDIUM | UI/UX polish, Word integration, mobile |
| Backend Engineer | MEDIUM | Agentic workflows, document generation, scale |

---

## Slide 14: The Ask

### Seeking Pre-Seed / Seed Investment

**Use of Funds:**

| Category | % | Purpose |
|----------|---|---------|
| **Engineering** | 40% | 2 engineers, Phase 3 features (agentic workflows, document generation, Word plugin) |
| **Go-to-Market** | 30% | Sales hire, 10-firm pilot program, legal tech conferences, content marketing |
| **Infrastructure** | 15% | Production hosting, monitoring, SOC 2 Type I preparation |
| **Operations** | 15% | Legal, accounting, working capital |

**Key Milestones (12 months post-funding):**

| Milestone | Target |
|-----------|--------|
| Paying customers | 100+ |
| ARR | $120K+ |
| NPS | 50+ |
| Phase 3 features shipped | Agentic workflows, document generation |
| SOC 2 Type I | Achieved |
| Self-hosted enterprise deals | 2+ |

---

## Slide 15: Closing

### Why JurisAgent

1. **Only legal AI with per-answer grounding validation** — 0% hallucination rate, proven across 1,308 questions
2. **Knowledge graph for multi-hop reasoning** — answers questions that vector-only search cannot
3. **Self-hosted deployment** — the only legal AI tool offering data sovereignty
4. **85%+ gross margin SaaS** — serving a $10.8B market growing at 30% CAGR
5. **Working product** — not a prototype, not a pitch. A deployed, benchmarked, iteratively improved platform.

---

## Appendix: Slide Design Notes

### Recommended Design Direction

- **Color palette:** Warm amber (#D97706) as accent, dark navy text, white/off-white backgrounds
- **Font:** Inter (matches the product UI)
- **Tone:** Confident but not arrogant. Data-driven. Let the benchmarks speak.
- **Screenshots:** Include real product screenshots on slides 4, 5, and 6
- **Charts:** Use the benchmark progression chart (85% to 100% over 9 rounds) — it tells a compelling iteration story
- **Keep text minimal on slides** — this document has the full content; slides should use headlines + key numbers, with detail in speaker notes

### Key Numbers to Highlight

These are the numbers that should be large/bold on slides:

- **0%** hallucination rate
- **100%** pass rate
- **4.48/5** quality score
- **1,308** benchmark questions
- **$10.8B** market by 2030
- **52%** adoption rate
- **120+** court cases from AI hallucinations
- **$60/mo** infrastructure cost (vs $250–400/mo competitors)
