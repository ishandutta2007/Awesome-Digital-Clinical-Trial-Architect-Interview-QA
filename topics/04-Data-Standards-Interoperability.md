# Topic 04: Data Standards & Interoperability

## Overview
CDISC standards (CDASH, SDTM, ADaM), HL7 FHIR, and the transformation pipelines converting operational data into submission-ready formats.

---

### Q1: Explain the relationship between CDASH, SDTM, and ADaM, and where each fits in the data lifecycle.

**A:**
- **CDASH (Clinical Data Acquisition Standards Harmonization):** Defines standardized data *collection* fields — governs CRF/eCOA design at the point of data entry, ensuring consistent variable naming and structure from the start
- **SDTM (Study Data Tabulation Model):** The standardized format for *submitting* collected data to regulators — organizes data into standardized domains (DM for demographics, AE for adverse events, VS for vital signs, etc.) with a controlled structure regardless of source EDC vendor
- **ADaM (Analysis Data Model):** Derived, analysis-ready datasets built *from* SDTM, structured specifically to support statistical analysis and directly traceable back to SDTM source variables (traceability is an explicit ADaM requirement)

**Lifecycle flow:**
```
CRF/eCOA design (CDASH-aligned fields)
  → Raw EDC data collection
  → SDTM transformation (mapping raw fields to standardized domains)
  → ADaM derivation (analysis-ready datasets, e.g., ADSL subject-level, ADAE adverse events)
  → Statistical analysis / TLFs (tables, listings, figures)
  → Submission package (define.xml, SDTM+ADaM datasets, aCRF) to FDA/PMDA/etc.
```

**Architectural implication:** Designing CRFs with CDASH alignment from the start dramatically reduces downstream SDTM mapping effort and error risk — a digital architect should embed CDASH conformance checks into EDC build QC, not treat SDTM mapping as an afterthought handled solely by biostatistics/programming late in the study.

### Q2: How would you architect a pipeline that automatically validates SDTM conformance during the study (not just at database lock)?

**A:**
**Design approach:**
1. **Incremental extraction:** Schedule automated extracts from EDC (e.g., nightly or weekly) rather than waiting for final lock
2. **Automated SDTM mapping layer:** Apply a versioned, validated mapping specification (spec-driven, e.g., using a mapping tool or custom ETL) transforming raw EDC exports into draft SDTM domains
3. **Conformance validation:** Run automated checks against CDISC Define-XML rules and industry-standard validators (e.g., Pinnacle 21 / OpenCDISC rules) on each incremental extract — surfacing structural errors (invalid controlled terminology, missing required variables) early rather than at lock
4. **Trending dashboard:** Track conformance error counts over time per domain — a rising error trend in a specific domain (e.g., AE) flags a site training issue or CRF design flaw that can be corrected mid-study rather than discovered at database lock under time pressure
5. **Version control and change log:** Any mapping specification change must be documented and re-validated against previously processed data to ensure retrospective consistency

**Business value:** This shifts SDTM/submission-readiness left in the study timeline, reducing the historically common "database lock fire drill" where hundreds of mapping errors are discovered only during final submission dataset preparation, risking submission timeline delays.

### Q3: Explain how HL7 FHIR is being adopted in clinical trials context, and where it complements (rather than replaces) CDISC standards.

**A:** FHIR and CDISC serve different purposes and increasingly work together rather than compete:

- **CDISC standards (CDASH/SDTM/ADaM)** are optimized for regulatory submission — structured for statistical analysis and long-term archival in a stable, well-governed format
- **HL7 FHIR** is optimized for real-time healthcare interoperability — exchanging data between EHRs, wearables, and trial systems using RESTful APIs and standardized resources (Observation, Condition, MedicationStatement, etc.)

**Complementary use case — EHR-to-EDC integration:** A site's EHR exposes relevant clinical data (labs, medical history, concomitant medications) via FHIR APIs; this data is retrieved and mapped into the EDC's CDASH-aligned fields, reducing manual transcription and associated transcription error risk. The FHIR-to-CDASH mapping is the critical integration layer requiring careful specification (unit conversions, terminology mapping from SNOMED/LOINC used in EHRs to the controlled terminology expected in SDTM).

**CDISC's own FHIR initiative:** CDISC has developed FHIR-to-CDASH/SDTM implementation guides (e.g., via the Vulcan initiative with HL7) explicitly to standardize this bridge, reducing the need for each sponsor/vendor to build bespoke mapping logic.

**Architect's role:** Recognize that FHIR solves the "getting data out of clinical systems in real time" problem, while CDISC solves the "getting data into regulator-ready, analyzable format" problem — a modern trial architecture needs both, connected via a well-governed, validated mapping layer.

### Q4–Q17: (Representative additional topics)
- Controlled terminology (CT) management and versioning across a multi-year trial
- Define.xml generation and validation workflow
- SDTM domain design for non-standard/novel endpoints (e.g., wearable-derived continuous data domains)
- Legacy data conversion to current CDISC standards for pooled/integrated analyses
- TAUG (Therapeutic Area User Guides) application for indication-specific data standards
- Data standards governance committee structure and change control
- Cross-study data standardization for portfolio-level analytics
- USDM (Unified Study Definitions Model) and protocol digitization standards
- Real-world data (RWD) mapping to OMOP CDM vs. CDISC for hybrid RWE-trial designs
- Automated aCRF (annotated CRF) generation from CDASH-aligned build specs
- Data standard compliance in adaptive/platform trial designs with evolving domains
- International data standard variations (Japan's PMDA-specific requirements)

---

## Summary
Interoperability architecture requires fluency in both submission-oriented standards (CDISC) and real-time exchange standards (FHIR), plus disciplined governance to prevent standard drift across a multi-year trial lifecycle.
