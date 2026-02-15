# Use Case Catalog

**Product:** Alpha Marvin / JurisAgent
**Last Updated:** February 15, 2026

---

## Primary Vertical: Legal

### Use Case 1: Contract Review & Analysis

**Persona:** In-house counsel at a mid-size company
**Pain Point:** Reviewing 50+ vendor contracts annually; each takes 2–4 hours to read and extract key terms
**How It Works:**
1. Upload all vendor contracts to the platform
2. Ask: "What are the termination notice periods across all contracts?"
3. System searches all documents, extracts termination clauses, and presents a comparison
4. Follow up: "Which contracts auto-renew?" — system traverses knowledge graph for renewal entities

**Value:** Reduces contract review from hours to minutes per question. Knowledge graph enables cross-contract analysis that manual review cannot efficiently achieve.

**Documents:** MSAs, NDAs, SOWs, vendor agreements, licensing agreements

---

### Use Case 2: Regulatory Compliance Verification

**Persona:** Compliance officer at a financial services firm
**Pain Point:** Verifying that internal policies align with 500+ pages of federal regulations
**How It Works:**
1. Upload regulatory documents (GDPR, SOX, CCPA, industry-specific regulations)
2. Upload internal policy documents
3. Ask: "Does our data retention policy comply with GDPR Article 17?"
4. System retrieves relevant regulation text + internal policy sections, identifies gaps
5. Grounding validation ensures the answer is based strictly on uploaded documents

**Value:** Compliance gap analysis that previously required expensive outside counsel. Grounding validation prevents false compliance claims.

**Documents:** Federal/state regulations, internal policies, compliance manuals, audit reports

---

### Use Case 3: Due Diligence for M&A

**Persona:** Associate attorney at a law firm handling acquisitions
**Pain Point:** Reviewing 500+ documents in a virtual data room under tight deadlines
**How It Works:**
1. Batch upload data room documents
2. Ask: "Are there any change-of-control provisions that could block this acquisition?"
3. System searches across all documents for change-of-control, assignment restrictions, consent requirements
4. Knowledge graph links entities across documents (e.g., "Party X" appears in 12 contracts)
5. Ask: "What are all of Party X's obligations across these contracts?"

**Value:** Multi-hop graph queries find relationships that keyword search misses. Entity extraction creates a complete picture of obligations and relationships.

**Documents:** Share purchase agreements, shareholder agreements, employment contracts, IP assignments, real estate leases

---

### Use Case 4: Litigation Support & Case Law Research

**Persona:** Litigation attorney preparing for trial
**Pain Point:** Finding relevant precedents and organizing case evidence across thousands of pages
**How It Works:**
1. Upload case filings, depositions, expert reports, prior rulings
2. Ask: "What did the expert witness say about damages calculation?"
3. System retrieves specific deposition excerpts with page citations
4. Ask: "How does this ruling compare to the precedent in Smith v. Jones?"
5. Knowledge graph tracks relationships between parties, claims, and rulings

**Value:** Instant access to specific evidence with exact page citations. Eliminates hours of manual document searching.

**Documents:** Complaints, answers, motions, depositions, expert reports, court orders, case law

---

### Use Case 5: Legal Research for Solo Practitioners

**Persona:** Solo attorney or small firm (1–5 attorneys)
**Pain Point:** Cannot afford CoCounsel ($400/mo) or Lexis+ ($250/mo) for document analysis
**How It Works:**
1. Upload client documents and relevant statutes
2. Ask questions in natural language
3. Receive grounded answers with citations to specific document sections
4. Self-hosted option keeps client data on the attorney's own server

**Value:** Enterprise-grade legal AI at a fraction of the cost. Self-hosted option addresses bar association ethics rules on client data confidentiality.

**Documents:** Client contracts, court filings, statutes, regulations

---

## Secondary Vertical: Healthcare & Life Sciences

### Use Case 6: Clinical Trial Protocol Analysis

**Persona:** Regulatory affairs specialist at a pharma company
**Pain Point:** Reviewing 200+ page clinical trial protocols for compliance with FDA regulations
**How It Works:**
1. Upload clinical trial protocols and FDA guidance documents
2. Ask: "Does this protocol's informed consent section meet 21 CFR 50 requirements?"
3. System cross-references protocol sections with regulatory requirements
4. Knowledge graph links protocol elements to specific regulatory citations

**Value:** Reduces protocol review cycles and identifies compliance gaps before FDA submission.

**Documents:** Clinical trial protocols, FDA guidances, informed consent forms, IRB submissions

---

### Use Case 7: Healthcare Contract Management

**Persona:** Hospital contract administrator
**Pain Point:** Managing hundreds of physician contracts, vendor agreements, and payer contracts
**How It Works:**
1. Upload all hospital contracts
2. Ask: "Which physician contracts expire in Q2 2026?"
3. Ask: "What are the non-compete radius restrictions across all physician contracts?"
4. Knowledge graph enables temporal queries and cross-contract comparison

**Value:** Centralized contract intelligence replaces spreadsheet tracking.

**Documents:** Physician employment agreements, vendor contracts, payer contracts, BAAs

---

## Secondary Vertical: Financial Services & Compliance

### Use Case 8: Investment Document Analysis

**Persona:** Fund administrator or compliance analyst
**Pain Point:** Reviewing PPMs, subscription agreements, and regulatory filings
**How It Works:**
1. Upload fund documents (PPM, LPA, subscription agreements)
2. Ask: "What are the management fee structures across all funds?"
3. Ask: "What are the investor eligibility requirements?"
4. System extracts financial terms and presents structured comparisons

**Value:** Structured extraction of financial terms from unstructured legal documents.

**Documents:** PPMs, LPAs, subscription agreements, side letters, regulatory filings

---

### Use Case 9: Insurance Policy Analysis

**Persona:** Insurance claims adjuster or underwriter
**Pain Point:** Comparing coverage terms across multiple policies for a single claim
**How It Works:**
1. Upload insurance policies (primary, excess, umbrella)
2. Ask: "What is the total coverage limit for cyber liability across all policies?"
3. Ask: "Are there any exclusions that could apply to this claim?"
4. Knowledge graph maps coverage layers and exclusion relationships

**Value:** Instant coverage analysis across policy stacks.

**Documents:** Insurance policies, endorsements, certificates of insurance, claims files

---

## Secondary Vertical: Government & Public Sector

### Use Case 10: Government Contract Administration

**Persona:** Government procurement officer or contractor
**Pain Point:** Navigating FAR/DFARS clauses across multiple contract vehicles
**How It Works:**
1. Upload government contracts and FAR/DFARS clause references
2. Ask: "Which contracts require cybersecurity incident reporting?"
3. Ask: "What are the ITAR restrictions across our defense contracts?"
4. System identifies relevant clauses and cross-references regulatory requirements

**Value:** Government contracts contain hundreds of incorporated clauses by reference. The knowledge graph resolves these references automatically.

**Documents:** Government contracts, task orders, FAR/DFARS clauses, security requirements

---

## Cross-Vertical Use Cases

### Use Case 11: Board & Governance Document Review

**Applies to:** Any industry with corporate governance requirements
**Documents:** Board minutes, corporate bylaws, shareholder agreements, resolutions
**Key questions:** "What was decided about the dividend policy in the last 3 board meetings?" / "What approvals are needed for this transaction under our bylaws?"

### Use Case 12: HR & Employment Document Analysis

**Applies to:** Any company with 50+ employees
**Documents:** Employment agreements, handbooks, policies, benefit plans
**Key questions:** "What are the severance terms across all executive contracts?" / "Does our handbook comply with the new state leave law?"

### Use Case 13: Intellectual Property Portfolio Management

**Applies to:** Technology, pharma, manufacturing companies
**Documents:** Patent applications, license agreements, assignment agreements, IP policies
**Key questions:** "Which patents are licensed to third parties?" / "What are the royalty obligations across all IP licenses?"

---

## Use Case Priority Matrix

| Priority | Use Case | Vertical | Beta Ready | Revenue Potential |
|----------|----------|----------|-----------|-------------------|
| P0 | Contract Review & Analysis | Legal | Yes | High |
| P0 | Regulatory Compliance | Legal/Financial | Yes | High |
| P1 | Due Diligence | Legal | Yes | High |
| P1 | Litigation Support | Legal | Yes | Medium |
| P1 | Solo Practitioner Research | Legal | Yes | Medium (volume) |
| P2 | Healthcare Contracts | Healthcare | Yes (same pipeline) | Medium |
| P2 | Insurance Policy Analysis | Financial | Yes (same pipeline) | Medium |
| P2 | Government Contracts | Government | Yes (same pipeline) | High |
| P3 | Clinical Trial Protocols | Healthcare | Needs domain tuning | High |
| P3 | Investment Documents | Financial | Needs domain tuning | High |

---

## Key Insight

The platform's architecture is **vertical-agnostic by design**. The same ingestion pipeline, knowledge graph, hybrid search, and grounding validation work across all document types. Domain specialization comes from:

1. **Chunking rules** — legal-aware chunking already handles Articles/Sections/Clauses; similar rules can be added for clinical trial sections or insurance policy endorsements
2. **System prompts** — the QA system prompt can be customized per vertical
3. **Entity schemas** — the knowledge graph schema can be extended for domain-specific entity types

Beta focuses on legal documents. Post-beta expansion to other verticals requires minimal code changes — primarily prompt tuning and entity schema additions.
