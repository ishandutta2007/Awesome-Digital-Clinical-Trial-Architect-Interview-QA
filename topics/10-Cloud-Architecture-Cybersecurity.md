# Topic 10: Cloud Architecture & Cybersecurity

## Overview
Cloud infrastructure for GxP systems, HIPAA/GDPR compliance, and cybersecurity architecture protecting trial data and patient safety.

---

### Q1: What is the shared responsibility model for cloud-hosted GxP clinical trial systems, and how does it affect validation/compliance ownership?

**A:** In cloud computing (IaaS/PaaS/SaaS), security and compliance responsibilities are split between the cloud provider and the sponsor/vendor, with the split varying by service model:

- **IaaS (e.g., raw AWS/Azure compute):** Provider responsible for physical infrastructure/hypervisor security; customer responsible for OS, application, data, and access control layers — heaviest customer validation burden
- **PaaS:** Provider manages more of the underlying platform (OS, runtime); customer focuses on application-layer configuration and data
- **SaaS (e.g., a validated EDC vendor's cloud offering):** Vendor manages most infrastructure and application layers; customer (sponsor) responsible primarily for user access management, configuration choices, and appropriate use — but critically, the sponsor retains ultimate regulatory responsibility for GxP compliance regardless of what's delegated contractually

**Compliance ownership implications:**
1. **Sponsor cannot outsource accountability:** Even in a full SaaS model, FDA/EMA hold the sponsor accountable for data integrity — this necessitates vendor qualification/audit processes (reviewing vendor SOC 2 reports, conducting vendor audits) rather than blind trust
2. **Contractual clarity required:** Quality/technical agreements must explicitly define who performs which validation activities (e.g., vendor performs IQ/OQ on infrastructure changes, sponsor performs PQ/UAT validating the specific study configuration)
3. **Right-to-audit clauses:** Architecture/procurement should ensure contracts include audit rights, enabling the sponsor to verify vendor compliance claims rather than relying solely on self-attestation

### Q2: Design the cybersecurity architecture for a patient-facing DCT mobile application handling ePRO and connected device data. What are the key threat vectors and mitigations?

**A:**
**Key threat vectors:**
1. **Unauthorized access to patient data:** Mitigated via strong authentication (biometric/MFA on device), encrypted local storage, and session timeout policies
2. **Data interception in transit:** Mitigated via TLS 1.2+ enforced for all API communication, certificate pinning to prevent man-in-the-middle attacks
3. **Device loss/theft:** Mitigated via remote wipe capability, local data encryption at rest, and minimizing locally cached PHI (sync-and-clear patterns rather than persistent local storage where feasible)
4. **API/backend vulnerabilities:** Mitigated via regular penetration testing, API rate limiting, input validation/sanitization to prevent injection attacks, and a Web Application Firewall (WAF) in front of backend services
5. **Insider threat/over-privileged access:** Mitigated via role-based access control (RBAC) with least-privilege defaults, and comprehensive access logging feeding into the audit trail system
6. **Supply chain risk (third-party SDKs/libraries):** Mitigated via software composition analysis (SCA) scanning for known vulnerabilities in dependencies, and vendor security assessment before SDK integration (particularly relevant for wearable device SDKs)
7. **Social engineering targeting patients:** Mitigated via patient education materials on phishing risk, and app-level design that never asks patients to re-enter credentials via email links (reducing phishing attack surface)

**Architecture principle:** Security controls should be layered (defense in depth) — no single control failure should result in a full data breach; regular third-party penetration testing (not just internal review) should validate these layers before go-live and periodically throughout the trial.

### Q3: How would you architect data residency and cross-border transfer compliance for a global trial spanning US, EU, and Asia-Pacific sites?

**A:**
**Core challenge:** Different jurisdictions impose different data localization/transfer restrictions — GDPR restricts EU personal data transfer outside the EEA without adequate safeguards; some Asia-Pacific countries (e.g., China) have increasingly strict data localization requirements for health data.

**Architectural approaches:**
1. **Regional data residency architecture:** Deploy region-specific database instances (e.g., EU data stored in EU-based cloud regions) with a federated query/reporting layer that aggregates de-identified or appropriately safeguarded summary data centrally, rather than replicating raw identifiable data globally
2. **Standard Contractual Clauses (SCCs) / adequacy mechanisms:** For necessary cross-border transfers (e.g., central statistical monitoring requiring pooled data), implement legally-recognized transfer mechanisms and document the transfer impact assessment required post-Schrems II
3. **Pseudonymization at the point of cross-border transfer:** Where full raw data doesn't need to cross borders, transfer pseudonymized/coded data with re-identification keys retained locally in the origin region
4. **Country-specific legal review integration:** Architecture decisions (e.g., "can central monitoring statisticians in the US view raw EU site data") must be validated against current legal guidance per region — this is a collaborative architecture-legal function, not a purely technical decision
5. **Vendor infrastructure footprint alignment:** Select eClinical vendors with cloud infrastructure presence in required regions (e.g., EU-based hosting options) rather than assuming a single global instance will satisfy all regional requirements

### Q4–Q15: (Representative additional topics)
- Zero-trust architecture principles applied to clinical trial systems
- Incident response planning for a clinical trial data breach
- Encryption key management for long-term data retention (15-25+ years)
- Multi-factor authentication rollout for site users with varying technical literacy
- API security architecture for third-party eClinical system integrations
- Disaster recovery/business continuity planning for mission-critical trial systems
- Security architecture review process for new vendor onboarding
- Data loss prevention (DLP) controls for clinical trial data
- Cloud cost optimization without compromising validated infrastructure stability
- Vulnerability management/patching cadence for validated GxP systems (balancing security currency with change control rigor)

---

## Summary
Cloud and cybersecurity architecture for clinical trials must satisfy the dual mandate of technical security best practice and GxP regulatory accountability — sponsors cannot delegate away compliance responsibility even when infrastructure is fully outsourced.
