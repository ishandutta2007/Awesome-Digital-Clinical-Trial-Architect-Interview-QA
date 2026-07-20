# Topic 01: Clinical Trial Fundamentals

## Overview
GCP principles, trial phases, protocol design basics, and the operational context digital architects must design around.

---

### Q1: What is ICH E6(R2)/(R3) Good Clinical Practice, and why does it matter for system architecture decisions?

**A:** ICH GCP is the international ethical/scientific quality standard for designing, conducting, recording, and reporting trials involving human subjects. E6(R2) (2016 addendum) introduced explicit expectations around risk-based quality management and computerized systems; E6(R3) (finalized 2023–2025 rollout) further modernizes the guideline to be technology-agnostic and explicitly accommodate decentralized elements, real-world data, and diverse trial designs.

**Architectural implications:**
- Systems must support **attributable, legible, contemporaneous, original, accurate** (ALCOA) data capture — driving audit trail, timestamp, and e-signature requirements
- E6(R2)'s emphasis on risk-based approaches justifies architecting centralized statistical monitoring dashboards rather than 100% source data verification
- E6(R3)'s broader scope legitimizes DCT-native architecture (remote monitoring, telehealth) as compliant by design rather than as an exception requiring special justification

### Q2: Walk through the differences between Phase I, II, III, and IV trials, and how data architecture needs change across phases.

**A:**
- **Phase I (safety/PK, 20–100 subjects):** Intensive, high-frequency data capture (PK sampling, vitals) often in a single controlled unit — architecture favors real-time, high-resolution EDC with tight nursing/dosing integration; less need for distributed DCT infrastructure
- **Phase II (efficacy signal, 100–300 subjects):** Introduces biomarker/endpoint complexity — architecture must support integration with central labs, imaging cores, and possibly early ePRO instruments
- **Phase III (confirmatory, 300–3000+ subjects, multi-site/global):** Requires the full eClinical stack — EDC, RTSM, CTMS, eTMF — with multi-language/multi-region localization, and often DCT hybrid elements to support recruitment/retention at scale
- **Phase IV (post-marketing):** Frequently leverages real-world data (RWD) sources, registries, and claims data integration rather than purpose-built EDC — architecture shifts toward RWD ingestion pipelines and pharmacovigilance system integration

### Q3–Q16: (Representative additional topics)
- Informed consent process and eConsent regulatory acceptance criteria
- Protocol amendments and their downstream system reconfiguration impact
- Site selection/activation timelines and CTMS support requirements
- Investigator-initiated vs. sponsor-initiated trial architecture differences
- Adaptive trial designs and their real-time data/statistical system demands
- Blinding/unblinding architecture and RTSM randomization integrity
- Adverse event/serious adverse event (AE/SAE) reporting workflow and timelines
- Data Safety Monitoring Board (DSMB) data package generation requirements
- Master protocols (basket/umbrella/platform) and shared infrastructure design
- Site monitoring visit types (on-site, remote, centralized) and system support
- Trial budget/contract systems and their CTMS integration
- Study startup timeline compression via digital-first site activation
- Global vs. single-region trial architecture considerations
- Rescue/rare disease trial architecture with small, distributed populations

---

## Summary
A digital architect must translate GCP principles and phase-specific operational needs into concrete system requirements — architecture decisions should always trace back to a regulatory or operational rationale, not technology preference alone.
