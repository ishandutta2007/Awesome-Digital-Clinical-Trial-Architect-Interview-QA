# Topic 06: Wearables & Remote Monitoring

## Overview
Connected devices, sensor data pipelines, eCOA/ePRO systems, and data quality management for continuous remote patient monitoring.

---

### Q1: What are the key architectural considerations when integrating a consumer-grade wearable (e.g., a smartwatch) vs. a medical-grade device into a clinical trial data pipeline?

**A:**
**Consumer-grade wearables:**
- Pro: Lower cost, higher patient familiarity/compliance (patients may already own one), broader population reach
- Con: Algorithms are often proprietary "black boxes" without published validation against clinical gold standards; firmware updates can silently change measurement algorithms mid-study without sponsor notification/control; data export APIs may change or be deprecated by the manufacturer without trial-specific support commitments

**Medical-grade devices:**
- Pro: Regulatory-cleared (FDA 510(k)/CE marked) with documented accuracy/precision specifications; typically more stable firmware/software lifecycle with vendor support agreements
- Con: Higher cost, potentially lower patient compliance (bulkier, less familiar), more limited real-world validation across broad populations vs. widely-used consumer devices

**Architecture implications:**
1. **Firmware/algorithm version locking:** For consumer devices, contractually or technically pin the firmware version used for the duration of the trial where possible, and document any forced updates as a protocol deviation risk requiring analysis
2. **Independent validation requirement:** For novel endpoints derived from a device (especially consumer-grade), a validation sub-study or literature-based justification is typically required to support regulatory acceptance of the derived endpoint
3. **Data pipeline resilience:** Build abstraction layers so a device API change doesn't require re-architecting the entire pipeline — isolate vendor-specific integration code from downstream processing logic

### Q2: Design a data pipeline for continuous wearable sensor data (e.g., accelerometer-derived activity data) from device to analysis-ready dataset. What quality checks are essential?

**A:**
**Pipeline stages:**
```
Device (raw sensor sampling, e.g., 50-100 Hz accelerometer)
  → Local device/app buffering (handles connectivity gaps)
  → Secure transmission (encrypted, typically via patient's smartphone app as gateway)
  → Cloud ingestion layer (raw data landing zone, immutable storage)
  → Signal processing/feature extraction (e.g., step count, activity classification algorithms)
  → Data quality/completeness flagging
  → Aggregation to protocol-specified endpoint (e.g., daily step count, weekly average)
  → SDTM-aligned domain mapping (or custom domain for novel endpoints)
  → Statistical analysis dataset
```

**Essential quality checks:**
1. **Wear-time compliance:** Distinguish "device off body" from "no activity" — a flat accelerometer signal could mean either, and misclassifying non-wear as low-activity biases results
2. **Data completeness thresholds:** Pre-specify minimum wear-time per day (e.g., ≥10 hours) for a day's data to be considered valid/included in analysis — inconsistent completeness across subjects can bias aggregate endpoints
3. **Signal quality/artifact detection:** Flag implausible values (e.g., heart rate readings outside physiological range) for exclusion or query
4. **Timestamp integrity:** Detect and correct for device clock drift, especially important for multi-day continuous recordings correlated with other time-stamped clinical events
5. **Battery/connectivity gap documentation:** Distinguish protocol-relevant missingness (patient didn't wear device) from technical missingness (connectivity failure) — these may need different handling in the statistical analysis plan (informative vs. non-informative missingness)

### Q3: How do you handle the regulatory distinction between an exploratory wearable-derived endpoint and one intended to support a labeling claim?

**A:**
**Exploratory endpoints:** Lower regulatory bar — device/algorithm doesn't necessarily need formal analytical validation before use; data can inform future trial design or hypothesis generation without requiring the same rigor as a primary/key secondary endpoint

**Labeling-claim-supporting endpoints (e.g., a digital endpoint intended to support an efficacy claim):** Requires substantially more rigor, per FDA's digital health technology (DHT) guidance:
1. **Verification:** Does the device accurately measure the intended physical parameter? (technical/engineering validation)
2. **Analytical validation:** Does the algorithm correctly process the raw sensor data into the intended measurement/metric?
3. **Clinical validation:** Does the resulting measurement meaningfully capture the clinical concept of interest for the target population and context of use?

**Architectural implication:** The intended regulatory use of an endpoint should be decided *before* device/algorithm selection, since it determines the validation evidence package the architect must plan to generate and document — retrofitting clinical validation onto an exploratory-grade pipeline after the fact is far more costly than planning appropriately from trial design.

### Q4–Q15: (Representative additional topics)
- ePRO/eCOA instrument electronic migration and equivalence testing (paper-to-electronic)
- Multi-device data harmonization when different sites use different wearable models
- Patient engagement/compliance strategies to maximize wearable wear-time
- Battery life/charging logistics architecture for extended remote monitoring
- Bluetooth/connectivity architecture for home-based device data transmission
- Data ownership and patient access rights to their own wearable-generated data
- Sensor-derived digital biomarker qualification pathways (FDA DDT program)
- Handling device recalls/malfunctions mid-study
- Sample size/statistical power implications of continuous vs. episodic endpoint data
- Cross-cultural/accessibility considerations in wearable device selection

---

## Summary
Wearable/remote monitoring architecture must rigorously distinguish exploratory from label-supporting use cases, build resilient pipelines that separate raw signal from derived features, and treat data completeness/quality as a first-class design concern rather than a downstream statistics problem.
