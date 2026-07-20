# Topic 02: eClinical Systems Architecture

## Overview
EDC, CTMS, eTMF, RTSM, and the integration patterns that connect them into a coherent trial technology ecosystem.

---

### Q1: Describe the core eClinical systems in a modern trial stack and how they integrate.

**A:**
- **EDC (Electronic Data Capture):** Central repository for clinical data entered by sites (CRFs); source of truth for efficacy/safety data
- **CTMS (Clinical Trial Management System):** Operational backbone — site management, monitoring visit tracking, enrollment tracking, budgets/payments
- **eTMF (electronic Trial Master File):** Regulatory document repository (protocols, IRB approvals, monitoring reports) — must maintain inspection-ready completeness
- **RTSM (Randomization and Trial Supply Management, aka IRT/IWRS):** Manages randomization, blinding, and drug supply/inventory logistics
- **eCOA/ePRO:** Captures patient- and clinician-reported outcomes electronically
- **Safety database (e.g., Argus, ArisGlobal):** Pharmacovigilance case processing for AE/SAE

**Integration architecture:**
```
Site/Patient Data Entry → EDC ⇄ RTSM (randomization arm visibility control)
                              ⇄ Safety DB (AE reconciliation)
                              ⇄ eCOA/ePRO (patient-reported data merge)
                CTMS ⇄ EDC (enrollment/visit status sync)
                eTMF ⇄ CTMS (document completeness tracking by milestone)
                        ↓
              Data warehouse / CDISC SDTM transformation → Biostatistics/submission
```

**Key integration principle:** Avoid duplicate data entry — each data element should have one authoritative source system, with other systems consuming via API/interface rather than re-entry, which is both an efficiency and a data-integrity (single source of truth) imperative.

### Q2: What are the trade-offs between a single-vendor unified eClinical platform vs. best-of-breed integrated systems?

**A:**
**Unified platform (single vendor, e.g., Veeva Vault, Medidata Rave suite):**
- Pro: Native integration reduces interface build/validation burden; single support relationship; consistent UX reduces site training burden
- Con: Vendor lock-in; may lack best-in-class functionality for specific modules (e.g., their eCOA may be weaker than a specialized vendor); harder to swap underperforming components

**Best-of-breed (separate specialized vendors integrated via APIs/middleware):**
- Pro: Best functionality per module; flexibility to swap vendors; can negotiate independently
- Con: Integration complexity — each interface requires its own validation, and failure points multiply; higher long-term maintenance burden; potential data synchronization/timing issues across systems with different update cadences

**Practical decision framework:** For smaller/mid-size sponsors or standard trial designs, unified platforms reduce validation overhead and time-to-start. For large sponsors with complex/novel trial designs (e.g., heavy DCT with many sensor integrations), best-of-breed with a dedicated integration/middleware layer (and strong API governance) often wins on capability, provided the organization has mature integration engineering capacity.

### Q3: How would you design the data flow and conflict-resolution logic when the same data point (e.g., a vital sign) can originate from both a site-entered EDC field and a connected wearable device?

**A:**
**Design considerations:**
1. **Define source of truth per field, not per system:** Explicitly document in the Data Management Plan (DMP) which system is authoritative for each variable — e.g., wearable-derived heart rate is authoritative for continuous monitoring endpoints, but site-entered vitals remain authoritative for protocol-specified visit assessments
2. **Reconciliation windows:** If both sources exist for overlapping data, define a reconciliation process (e.g., automated flag if wearable and site-entered values diverge beyond a clinical threshold) routed to data management for query
3. **Timestamp harmonization:** Wearables often use device-local time; EDC entries use site-entered time — must normalize to a single time standard (UTC + site timezone metadata) to enable accurate cross-referencing
4. **Audit trail preservation:** Both original data points must be retained (not overwritten) per ALCOA+ principles, even after reconciliation — the resolved/derived value should reference its source and any query resolution
5. **Statistical Analysis Plan (SAP) alignment:** Biostatistics must pre-specify (before unblinding) which source is used for the primary endpoint if multiple sources exist, to avoid post-hoc source-selection bias

### Q4–Q17: (Representative additional topics)
- CDASH-aligned CRF design principles
- Edit check/query management architecture in EDC systems
- RTSM randomization algorithm types (block, stratified, minimization) and system config
- Site payment/CTMS financial system integration
- eTMF completeness metrics and inspection-readiness dashboards
- API/middleware architecture patterns for eClinical integration (point-to-point vs. hub-and-spoke)
- Data migration strategy when switching EDC vendors mid-study
- Vendor selection/evaluation criteria for eClinical systems (RFP process)
- Multi-language/localization architecture for global trials
- System uptime/SLA requirements for patient-facing DCT applications
- Version control and change management for live-study system configurations
- Interactive Response Technology (IRT) fail-safe/downtime procedures
- Master data management across the eClinical ecosystem
- Study build timeline optimization via reusable system templates/libraries

---

## Summary
eClinical architecture requires balancing integration complexity, vendor risk, data integrity, and operational efficiency. A strong architect designs for a single source of truth per data element and builds explicit reconciliation logic wherever data can originate from multiple systems.
