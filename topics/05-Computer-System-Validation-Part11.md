# Topic 05: Computer System Validation & Part 11

## Overview
CSV/CSA methodology, 21 CFR Part 11 electronic records/signatures, audit trails, and validation architecture for GxP systems.

---

### Q1: Compare traditional Computer System Validation (CSV) with the FDA's newer Computer Software Assurance (CSA) approach. How does this change architecture/validation planning?

**A:**
**Traditional CSV:** Document-heavy, exhaustive scripted testing of every function regardless of risk level — every requirement gets a written test case with screenshots, driving significant validation timeline/cost, often disproportionate to actual patient-safety/data-integrity risk.

**CSA (FDA draft guidance, 2022):** Risk-based approach explicitly encouraging sponsors to focus rigorous testing effort on high-risk functions (those directly impacting patient safety, product quality, or data integrity) while using lighter-touch approaches (unscripted testing, leveraging vendor testing evidence, ad hoc/exploratory testing) for low-risk functions.

**Architectural/process implications:**
1. **Risk assessment must happen earlier and more rigorously:** Before validation planning, the architect/QA team must categorize each system function by risk (high/medium/low) — this becomes the primary driver of validation approach, not a formality
2. **Leverage vendor documentation more heavily:** For low-risk, standard/configured (not customized) functionality, CSA explicitly supports relying on vendor testing evidence rather than sponsor-side re-testing everything
3. **Faster validation cycles:** Shifting effort away from low-risk scripted testing toward high-risk focused testing can meaningfully compress validation timelines — important for architects designing systems that must be validated and live before study start
4. **Documentation still required, but proportionate:** CSA doesn't eliminate documentation, but explicitly discourages "testing to test" — architects should design test plans with clear risk-rationale, not default to maximal documentation as a defensive posture

**Practical note:** CSA is guidance, not a binding regulation, and adoption maturity varies by sponsor/inspector — architects should be prepared to defend risk-based decisions if challenged in inspection, with clear documented rationale for why a function was deemed lower risk.

### Q2: What are the core 21 CFR Part 11 requirements for electronic records and signatures, and how do you architect a system to meet them?

**A:**
**Core Part 11 requirements:**
1. **Validation:** System must be validated to ensure accuracy, reliability, and consistent intended performance
2. **Audit trails:** Secure, computer-generated, time-stamped audit trails that independently record operator entries/actions creating, modifying, or deleting electronic records — must not be able to be turned off by users, and must retain original data (not overwrite)
3. **Access controls:** Limit system access to authorized individuals; unique user IDs (no shared logins) with periodic access review
4. **Electronic signature requirements:** Must be uniquely linked to a signer, include signer's printed name/date/time/meaning of the signature (e.g., "approved," "reviewed"), and be non-transferable
5. **Record retention and retrieval:** Records must remain accurate, complete, and readily retrievable throughout the required retention period (which can span decades for trial records)

**Architecture translation:**
- Database schema must separate audit trail storage from editable record storage (append-only audit log, immutable once written)
- Authentication architecture: enforce strong password policy or MFA, unique credentials, automatic session timeout
- Signature workflow: capture signature meaning as a required field at time of signing (not inferred later), with cryptographic or database-enforced linkage between signature event and specific record version
- Legacy system migration: when moving data between systems (e.g., EDC vendor switch), audit trail continuity/legacy audit trail accessibility must be preserved or the historical record integrity is compromised

### Q3: How would you design an audit trail review process for a large multi-site trial without it becoming operationally unmanageable?

**A:**
**Problem:** Manually reviewing every audit trail entry across a large multi-site trial (potentially millions of data change events) is not feasible, but regulatory expectation (and good practice) is that audit trails are actually reviewed, not just retained.

**Risk-based design approach:**
1. **Define review triggers, not blanket review:** Focus audit trail review on high-risk data changes — critical/key data points (primary endpoint, eligibility-determining fields, safety data) reviewed with higher scrutiny/frequency than administrative field changes
2. **Automated anomaly detection:** Build statistical/rule-based flags for unusual patterns — e.g., a single user making unusually high volumes of changes, changes made outside expected business hours, or changes to locked/frozen data — routing only flagged events for human review
3. **Risk-proportionate review cadence:** High-risk fields reviewed continuously/near-real-time (supporting centralized monitoring); lower-risk fields reviewed at defined milestones (e.g., pre-database-lock)
4. **Role-based review responsibility:** Data management reviews data-entry-pattern anomalies; clinical/medical monitors review safety-data-specific audit trail changes; this distributes review burden according to expertise rather than centralizing all review in one overloaded function
5. **Documentation of the review strategy itself:** The risk-based audit trail review approach must itself be documented and justified in the Data Management Plan/Monitoring Plan, since inspectors will ask not just "did you review" but "what was your rationale for the review scope"

### Q4–Q16: (Representative additional topics)
- Vendor system validation/qualification (SaaS/cloud vendor GxP assessment)
- Legacy system decommissioning and data archival validation
- Change control processes for live-study system modifications
- Part 11 vs. EU Annex 11 vs. other regional electronic records requirements
- Validation documentation structure (URS, FRS, IQ/OQ/PQ, traceability matrix)
- Cloud/SaaS shared responsibility model for validation obligations
- Automated testing frameworks for GxP system regression testing
- Data migration validation strategy between eClinical system versions
- Inspection readiness for computerized systems (FDA BIMO inspections)
- Role of Quality Assurance vs. IT vs. Clinical Operations in CSV governance
- Configuration vs. customization distinction and its validation effort implications
- Periodic review/revalidation triggers for long-running trial systems

---

## Summary
CSV/CSA and Part 11 compliance are not bureaucratic overhead but the architectural backbone ensuring trial data can be trusted by regulators, sponsors, and ultimately patients. Modern risk-based approaches (CSA) reward architects who can rigorously justify proportionate validation effort rather than defaulting to maximal documentation.
