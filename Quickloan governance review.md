# QuickLoan Governance Review – Complete Submission

**Submitted To:** Governance Review Board
**Submitted By:** Data Governance & AI Ethics Team
**Date:** March 27, 2026

---

## Deliverable 1: QuickLoan Governance Review Card

| Section | Issue / Definition | Impact | Suggested Fix / Mitigation |
|---|---|---|---|
| **1. Data Quality Risk** | Inconsistent customer data from mobile forms—missing IDs, unstandardized phone numbers, incomplete KYC fields. | Leads to inaccurate ML scoring, higher default rates, and unreliable risk assessments. | Implement mandatory field validation at point of entry; apply standardized formatting rules; enforce automated KYC completeness checks before data enters preprocessing pipeline. |
| **2. Legal & Compliance Risk** | Mobile app collects excessive PII (contact lists, GPS, device logs) without explicit consent; no consent capture mechanism at API gateway. | Violates Ghana Data Protection Act, 2012 (Act 843)—specifically data minimization and lawful processing principles. High regulatory exposure to Data Protection Commission sanctions. | Enforce explicit opt‑in consent before collection; reduce data collection to strictly necessary fields (ID, income, transaction history); implement retention tagging and automated deletion schedules. |
| **Data Classification** | Sensitive – All customer financial, identity, behavioural, and device‑level data falls under this classification. | Classification triggers enhanced controls: encryption, access restriction, retention enforcement, and audit logging. | Apply classification tag at data ingestion; restrict access to authorized personnel; ensure encryption at rest and in transit; maintain classification register. |
| **3. Bias & Fairness Risk** | ML model uses proxy variables—geolocation clusters, contact graph patterns, device metadata—that may correlate with socioeconomic status and ethnicity. | Model may systematically under‑score or deny loans to certain demographic groups, leading to unfair discrimination and regulatory exposure. | Conduct quarterly bias audits using statistical parity metrics; implement model explainability (SHAP/LIME); monitor approval rates by demographic segments; retrain with fairness constraints when bias detected. |
| **Source of Bias** | Proxy variables (geolocation, contact graph, device metadata) correlate with protected attributes; lack of fairness testing in model development lifecycle. | | |

### 4. Storytelling / Reporting Recommendation

| | |
|---|---|
| **Metric to Monitor (name & definition)** | Demographic Parity Approval Ratio (DPAR) – For each demographic group, the ratio of the group's approval rate to the approval rate of the group with the highest observed approval rate. Formula: DPAR = (Approval Rate for Group X) / (Highest Approval Rate Across All Groups). |
| **Visualization Type** | Grouped Bar Chart – Displaying approval rates by demographic segment with a reference line at the target threshold (e.g., 0.80). |
| **Why It Matters (One sentence)** | This metric provides immediate visibility into whether any demographic group is systematically receiving fewer loan approvals, enabling proactive fairness interventions before regulatory or reputational harm occurs. |

---

## Deliverable 2: Corrected Data Flow Diagram (With Annotations)

### Original Diagram Flaws Identified

Based on the provided data flow diagram, the following critical flaws were identified:

| Step | Flaw |
|---|---|
| Step 1 (User Mobile App) | Excessive collection of PII—contact lists, GPS, device logs |
| Step 2–3 (API Gateway to Raw Data DB) | No consent capture mechanism |
| Step 3 (Raw Data DB 'AllEvents') | No classification or retention policy |
| Step 4 (Preprocessing Service) | No defined handling or validation rules |
| Step 7 (Decision Service) | No transparency or audit logging |
| Step 9 (Analytics DB) | Stores PII without masking or anonymization |

### Corrected Data Flow Diagram (With Annotations)
<img width="771" height="697" alt="QuickLoan -DataFlow" src="https://github.com/user-attachments/assets/18d24b4d-cd08-495f-8a8c-5ed714b24525" />

### Annotations (Numbered Corrections)

| Annotation | Step | Correction | Explanation |
|---|---|---|---|
| 1| Step 1 – User Mobile App | Data minimization implemented – Removed collection of contact lists, GPS, and device logs. App now collects only essential fields: ID, income, and transaction history. | Why necessary: Excessive PII collection violates the data minimization principle under Ghana Data Protection Act, 2012 (Act 843) and creates unnecessary regulatory exposure. |
| 2 | Step 2–3 – API Gateway to Raw Data DB | Consent capture added – Explicit opt‑in consent required before data is transmitted to Raw Data DB. Consent timestamp and version logged. | Why necessary: Act 843 requires lawful basis for processing. Without documented consent, all data collection is unlawful and subject to enforcement action. |
| 3 | Step 3 – Raw Data DB 'AllEvents' | Classification and retention policy added – Data tagged as Sensitive with automated retention schedule. Deletion triggered upon expiration. | Why necessary: Untagged data with no retention controls creates indefinite storage of sensitive information, increasing breach risk and violating storage limitation principles. |
| 4 | Step 4 – Preprocessing Service | Data quality validation added – Mandatory field validation, format standardization, and completeness checks. Incomplete records rejected and logged. | Why necessary: Without quality gates, incomplete KYC data flows into ML model, causing inaccurate scoring and increased default rates. |
| 5 | Step 7 – Decision Service | Audit logging added – Every decision logs input features, confidence score, reason codes, and timestamp. Logs retained for minimum 3 years. | Why necessary: Without traceability, fairness audits are impossible, and customers cannot receive explanations for adverse decisions—a transparency expectation under ethical AI frameworks. |
| 6 | Step 9 – Analytics DB | Data masking implemented – Personal identifiers replaced with hashed tokens. Analytics performed on anonymized datasets only. | Why necessary: Storing unmasked PII in analytics environments creates unnecessary breach risk and violates purpose limitation principles. |
| 7 | Step 10 – Third‑Party Partner Integration | Pseudonymization + contractual controls added – Partners receive only masked data governed by Data Processing Agreements (DPAs) with strict usage restrictions. | Why necessary: Uncontrolled sharing of PII with third parties creates significant regulatory exposure and violates data subject rights under Act 843. |

---

## Deliverable 3: Summary of Review Process (200–300 Words)

### How I Used Data Lifecycle and Classification Principles to Identify Risks

I structured my review around the Data Lifecycle stages: Collection, Storage, Processing, Sharing, and Retention. At each stage, I applied data classification principles to determine sensitivity and required controls. Starting with the mobile app (Collection), I classified all customer PII—financial, identity, behavioural, and device metadata—as Sensitive. This immediately flagged the excessive collection of contact lists, GPS, and device logs as a violation of data minimization under Ghana's Data Protection Act, 2012 (Act 843). Moving to Storage, I identified the Raw Data DB lacked classification tags or retention policies—a critical gap for sensitive data. During Processing, I noted the absence of validation rules, allowing poor‑quality KYC data to flow into the ML model, degrading accuracy. At the Output stage, the Decision Service had no logging mechanism, making fairness audits impossible. Finally, during Sharing, the Analytics DB stored unmasked PII, and third‑party integrations lacked contractual controls—both high‑risk exposures under Act 843.

### How My Proposed Metric Ensures Ethical and Transparent Governance

The Demographic Parity Approval Ratio (DPAR) ensures ethical governance by making fairness measurable and visible. It compares approval rates across demographic groups, flagging any group whose approval rate falls significantly below the highest‑approved group. This metric is visualized as a grouped bar chart, enabling executive stakeholders to immediately identify potential disparate impact. By monitoring DPAR monthly, QuickLoan can detect bias early, investigate root causes, and retrain models with fairness constraints before regulatory scrutiny or reputational damage occurs. The metric transforms fairness from an abstract principle into an accountable, data‑driven governance practice.
