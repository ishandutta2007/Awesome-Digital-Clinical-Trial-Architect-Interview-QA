# Topic 03: Decentralized Trial Design

## Overview
DCT models, telehealth integration, home health visits, and hybrid architecture supporting patient-centric trial delivery.

---

### Q1: What are the main decentralized clinical trial (DCT) models, and how does system architecture differ across them?

**A:**
- **Fully decentralized (site-less):** All visits conducted remotely (telehealth, home health nursing, direct-to-patient drug shipment); no traditional brick-and-mortar site. Architecture requires robust telehealth integration, DtP logistics tracking, and remote consent — highest complexity, typically reserved for trials with simple assessments and strong safety profiles.
- **Hybrid/site-supported DCT:** Combination of in-person site visits (for procedures requiring physical presence, e.g., imaging, infusion) and remote visits for routine follow-up. Architecture must support flexible visit-type switching within a single subject's schedule, with the EDC/CTMS tracking visit modality per encounter.
- **Site-centric with DCT-enabling tools:** Traditional site-based trial augmented with ePRO, wearables, or eConsent to reduce burden without removing the site as hub. Lowest architectural complexity — primarily additive tooling rather than a redesigned visit model.

**Architectural implication:** The DCT model chosen should be protocol-driven (based on assessment complexity, population, and regulatory risk tolerance), not vendor-driven — architects should push back on "decentralize everything" defaults when procedures genuinely require in-person, controlled conditions (e.g., first-dose monitoring for a novel biologic).

### Q2: How do you architect a direct-to-patient (DtP) investigational product shipment and chain-of-custody tracking system?

**A:**
**Core requirements:**
1. **Temperature-controlled logistics integration:** Real-time cold-chain monitoring (IoT temperature loggers) with automated excursion alerts feeding into the RTSM/drug supply system
2. **Chain-of-custody documentation:** Every handoff (depot → courier → patient) must be timestamped and attributable, satisfying GCP source documentation requirements as rigorously as an in-site dispensing log
3. **Patient identity verification at delivery:** Courier or site coordinator confirms correct recipient (often via ID check or signature capture integrated with the eConsent/subject ID system) — critical for controlled substances or high-risk investigational products
4. **RTSM synchronization:** Shipment triggers (dispensation events) must sync with RTSM inventory in near-real-time to prevent stock-outs or over-shipment, and to maintain accurate blinding-safe inventory visibility
5. **Regulatory/customs handling for international trials:** Architecture must account for cross-border import/export documentation, particularly for controlled substances, with country-specific customs holds factored into supply chain buffer calculations
6. **Return/destruction tracking:** Used/unused product return logistics from patient homes require the same documentation rigor as site-based drug accountability

**Risk consideration:** DtP shipment removes the pharmacist/site verification checkpoint present in traditional dispensing — architecture must compensate with additional automated verification (barcode scanning at each step, automated dose-count reconciliation) to maintain equivalent data integrity.

### Q3: What are the informed consent architecture requirements for a fully remote eConsent process, and how do you ensure regulatory acceptance across jurisdictions?

**A:**
**Core architectural requirements:**
1. **Comprehension verification:** Interactive elements (embedded quizzes, teach-back prompts) rather than passive document display — FDA/EMA guidance increasingly expects demonstrated comprehension, not just presentation
2. **Identity verification:** Remote identity proofing (knowledge-based authentication, video verification, or government ID matching) proportionate to trial risk level
3. **Legally valid e-signature:** Must comply with 21 CFR Part 11 (US) and eIDAS (EU) — architecture must capture signature with appropriate authentication strength (simple electronic signature vs. advanced/qualified signature depending on jurisdiction and risk)
4. **Version control:** Every consent version (including amendments) must be tracked per-subject, with re-consent workflows triggered automatically on protocol amendments affecting the informed consent form (ICF)
5. **Multi-language support with certified translation linkage:** Each translated ICF version must be traceable to its approved source version and IRB/EC approval record
6. **Witness/LAR (legally authorized representative) workflows:** For subjects unable to independently consent, architecture must support remote witnessing that satisfies site-specific IRB requirements, which vary significantly

**Cross-jurisdictional challenge:** eConsent regulatory acceptance is not uniform — some IRBs/ECs still require a hybrid model (remote presentation + live investigator discussion via video) rather than fully asynchronous consent. Architecture should be configurable per-site/per-region rather than assuming a single global eConsent workflow will satisfy all reviewing bodies.

### Q4–Q16: (Representative additional topics)
- Telehealth platform integration and video visit documentation requirements
- Home health nursing vendor integration and data capture standardization
- Local lab vs. central lab architecture trade-offs in DCT designs
- Connected device provisioning and patient onboarding/tech support architecture
- DCT patient engagement app design principles (usability for diverse populations)
- Protocol deviation tracking unique to remote visit models
- Site-of-record designation in fully decentralized trials (regulatory accountability)
- DCT feasibility assessment frameworks (which protocols are DCT-appropriate)
- Caregiver/care-partner data entry roles and system permissions
- Rural/low-connectivity patient accommodation architecture (offline data capture/sync)
- DCT budget modeling differences vs. traditional site-based trials
- Emergency unblinding procedures in a fully remote/DCT context
- Investigator oversight requirements when procedures are performed by home health vendors

---

## Summary
DCT architecture must balance patient convenience against the same GCP rigor expected of traditional site-based trials — every "decentralized" convenience feature needs an equivalent or stronger data integrity and oversight mechanism than what it replaces.
