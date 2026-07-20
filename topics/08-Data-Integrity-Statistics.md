# Topic 08: Data Integrity & Statistics

## Overview
ALCOA+ principles, missing data handling, and the statistical considerations digital architects must understand to design defensible data pipelines.

---

### Q1: Explain the ALCOA+ framework and how each principle maps to a specific architectural control.

**A:**
- **Attributable:** Every data point traceable to who/what generated it → unique user IDs, device serial number logging, no shared credentials
- **Legible:** Data must be readable/understandable throughout retention period → standardized formats, avoiding proprietary formats without long-term readability guarantees, migration planning for format obsolescence
- **Contemporaneous:** Recorded at time of observation → real-time timestamping at point of entry (not batch-entered later), device-level timestamp capture for wearables
- **Original:** First recording or certified true copy preserved → immutable raw data landing zones, no overwriting of source data even during transformation pipelines
- **Accurate:** Free from error, reflects what actually happened → validation rules, range checks, edit checks at point of entry
- **Complete:** All data including repeats/reanalysis retained → no silent deletion of "bad" data points; corrections via documented amendment, not erasure
- **Consistent:** Data internally consistent, chronologically ordered → cross-field validation logic (e.g., AE onset date can't precede informed consent date)
- **Enduring:** Available/retrievable for the full required retention period (often 15-25+ years) → architecture must plan for long-term archival independent of any single vendor's continued existence
- **Available:** Accessible for review/audit when needed → indexed, searchable archival rather than opaque cold storage that's technically retained but practically unusable

**Architectural takeaway:** ALCOA+ is not just a data management policy document — each principle should map to a concrete, testable system control that can be demonstrated during an inspection, not just asserted in an SOP.

### Q2: How should a digital architect approach missing data by design, versus leaving it entirely to biostatistics to handle post-hoc?

**A:** While the statistical *analysis* of missing data (multiple imputation, mixed models for repeated measures, tipping-point sensitivity analyses) is biostatistics' domain, the *architecture* substantially determines what missing data patterns even occur and whether they're informative or non-informative — this is squarely the architect's responsibility to get right upfront.

**Design-level missing data prevention/characterization:**
1. **Distinguish missing data types at capture time:** System should allow explicit "not done" / "not applicable" / "unknown" codes distinct from a blank field — a blank field with no reason code makes it impossible to later distinguish a data entry oversight from a clinically meaningful "test not performed because patient too unwell"
2. **Real-time completeness dashboards:** Surfacing missingness patterns during the study (not just at lock) allows operational intervention (site retraining, protocol clarification) before missingness becomes unfixably baked into the dataset
3. **Skip logic and conditional field design:** Poorly designed conditional CRF logic can create structural missingness (a field never displayed due to a logic error looks identical to a site simply not completing it) — rigorous UAT of skip logic is a data-integrity control, not just a UX nicety
4. **Wearable/continuous data non-wear vs. missing distinction:** As discussed in Topic 06, architecture must capture wear-time/device-status metadata alongside the raw signal to allow biostatistics to correctly classify missingness mechanism (MCAR/MAR/MNAR) rather than guessing post-hoc

**Collaboration model:** Architect and biostatistics should jointly review the Data Management Plan and Statistical Analysis Plan during study design specifically to align on what missingness metadata the system must capture to support the planned analysis approach — this conversation is far cheaper before study start than after database lock reveals the needed metadata was never captured.

### Q3–Q15: (Representative additional topics)
- Query management workflow design and its impact on data integrity timelines
- Source data verification (SDV) architecture in a risk-based monitoring model
- Handling protocol deviations vs. data queries as distinct system objects
- Blinding integrity in data listings/reports accessible to unblinded vs. blinded roles
- Statistical analysis plan (SAP) alignment with database design decisions
- Interim analysis data cut architecture and firewall procedures
- Double data entry vs. single entry with edit checks trade-offs
- Data reconciliation between EDC and external data (labs, imaging, safety database)
- Derived variable/data transformation documentation and traceability
- Handling data from terminated/withdrawn subjects in the analysis dataset architecture
- Central lab vs. local lab data harmonization for pooled analysis
- Sample size re-estimation architecture support for adaptive designs

---

## Summary
Data integrity is fundamentally an architectural discipline — statistical validity of the eventual trial result depends heavily on decisions made at data-capture design time, long before biostatistics ever sees the locked dataset.
