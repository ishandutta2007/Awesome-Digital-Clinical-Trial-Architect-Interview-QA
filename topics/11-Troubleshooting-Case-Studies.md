# Topic 11: Troubleshooting & Case Studies

## Overview
Debugging real-world eClinical system failures, data discrepancies, and applied problem-solving scenarios.

---

### Q1: Mid-study, sites report that randomization assignments in the RTSM occasionally don't match what's reflected in the EDC's treatment arm field, causing confusion during monitoring visits. Walk through your troubleshooting approach.

**A:**
**Systematic troubleshooting framework:**
1. **Scope the discrepancy:** Is this affecting all sites or specific ones? All subjects or a subset? Since a specific point in time, or throughout the study? Scoping quickly distinguishes a systemic integration bug from a site-specific process error
2. **Check integration timing/latency:** RTSM-to-EDC integration often occurs via scheduled batch sync (e.g., every 15 minutes) rather than real-time — verify whether the discrepancy is transient (resolves after sync latency) or persistent (indicates an actual integration failure)
3. **Verify the integration mapping logic:** Check whether recent RTSM or EDC configuration changes (e.g., a new stratification factor, an arm relabeling) broke a previously working field mapping
4. **Check for manual override conflicts:** Determine if a user manually edited the EDC treatment arm field (which should typically be read-only/system-populated from RTSM) — a broken permission/configuration allowing manual edits is a common root cause
5. **Reproduce in a test/UAT environment:** Attempt to replicate the specific discrepancy pattern in a non-production environment before making production changes, to avoid compounding the issue
6. **Assess blinding impact:** Critically, determine whether this discrepancy has *already* caused any unblinding exposure (e.g., did a monitor or site user see two conflicting arm values that could reveal blinded information) — this may trigger separate reporting obligations independent of the technical fix

**Resolution and prevention:** Implement automated reconciliation monitoring (Topic 02, Q3) so RTSM-EDC mismatches are flagged automatically going forward rather than discovered reactively by confused site staff, and conduct a formal root cause analysis/CAPA given the patient-safety and blinding-integrity implications of randomization data discrepancies.

### Q2: Case study — Three weeks before database lock, the SDTM conformance validation reveals over 500 errors in the AE domain, largely due to inconsistent adverse event term coding across sites. How do you triage and resolve this under time pressure?

**A:**
**Triage approach:**
1. **Categorize error severity:** Separate true data errors (miscoded terms affecting safety analysis) from structural/cosmetic conformance issues (e.g., minor variable formatting) — not all 500 errors carry equal urgency or risk
2. **Root cause analysis:** Investigate why coding inconsistency wasn't caught earlier — likely candidates: MedDRA coding wasn't being applied consistently in near-real-time during the study (a process gap), or dictionary version mismatches across sites/time periods
3. **Prioritize by impact on primary/key secondary safety endpoints:** Errors affecting SAEs or endpoints central to the safety narrative get immediate senior medical/safety review priority over lower-impact administrative fields
4. **Parallelize remediation:** Split the error list across the data management/medical coding team by domain/error-type category, with clear ownership and tracking rather than sequential processing
5. **Implement targeted, documented fixes:** Each correction must go through the standard query/data correction process (not ad hoc database edits) to preserve audit trail integrity, even under time pressure — cutting corners here creates larger inspection risk than the timeline pressure itself

**Prevention for future studies:** This scenario is a strong argument for implementing the incremental/ongoing SDTM validation pipeline described in Topic 04, Q2 — the architectural lesson is that waiting until near-lock to run conformance validation is itself the root cause of the fire drill, regardless of the specific coding inconsistency trigger.

### Q3–Q15: (Representative additional topics)
- Debugging a wearable device data gap affecting primary endpoint completeness
- Investigating a suspected data fabrication flag from centralized statistical monitoring
- Troubleshooting eConsent version mismatch across sites after a late protocol amendment
- Root-causing an audit trail gap discovered during internal QA review
- Resolving a vendor API outage affecting real-time RTSM drug supply visibility
- Investigating patient complaints about DCT mobile app usability affecting compliance
- Debugging FHIR-to-EDC integration failures causing incomplete medical history import
- Troubleshooting a Part 11 e-signature validation failure blocking site data submission
- Handling a cybersecurity incident affecting a subset of trial participant data
- Root-causing inconsistent unit conversions between central and local lab data
- Investigating an unexpected spike in protocol deviations at a specific site
- Case study: recovering from an EDC vendor's unplanned extended system outage

---

## Summary
Real-world digital trial architecture problem-solving requires systematic triage balancing patient safety, data integrity, regulatory risk, and operational timeline pressure — strong candidates demonstrate structured root-cause methodology, not just technical fixes.
