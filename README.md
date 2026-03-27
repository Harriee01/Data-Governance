# 📊 Data Governance Module

A structured collection of solved lab reports covering core concepts and practical implementations in Data Governance — built around real-world African and global business scenarios.

---

## 📁 Module Overview

This module applies data governance principles to realistic case studies: a Ghanaian health tech startup, a global e-commerce platform navigating multi-jurisdiction privacy law, and a fintech app with a flawed ML pipeline. Each lab is scenario-driven and requires identifying issues, assessing impact, and recommending actionable solutions.

---

## 🧪 Lab Reports

| # | Lab Title | Scenario | Key Domains | Status |
|---|-----------|----------|-------------|--------|
| 01 | [Data Quality Detective Challenge](#lab-01--data-quality-detective-challenge) | MedTrack Ghana — Patient Appointment Database | Data Quality, Operations, Billing | ✅ Solved |
| 02 | [Multi-Jurisdiction Compliance Challenge](#lab-02--multi-jurisdiction-compliance-challenge) | ShopGhana — Global Customer Deletion Requests | GDPR, Ghana DPA, CCPA/CPRA | ✅ Solved |
| 03 | [QuickLoan Mobile Ethical Data Review](#lab-03--quickloan-mobile-ethical-data-review) | QuickLoan Mobile — Flawed ML Loan Scoring Pipeline | Ethics, Algorithmic Bias, Compliance | ✅ Solved |

---

## 📝 Lab Summaries

---

### Lab 01 — Data Quality Detective Challenge

**Scenario:** MedTrack Ghana, a health tech startup, is experiencing SMS delivery failures, duplicate patient counts in reports, and billing errors — all traced back to a dirty patient appointment database.

**Dataset Fields:** `PatientID`, `PatientName`, `PhoneNumber`, `AppointmentDate`, `DoctorName`, `PaymentStatus`

#### Tasks Completed

**Task 1 — Quality Issues Identified (6 Dimensions)**

| Dimension | Violation Found | Example from Dataset |
|-----------|----------------|----------------------|
| **Accuracy** | Phone number is factually wrong | `P002` row 2: `244789012` (missing leading `0`) |
| **Completeness** | Required field is blank | `P004`: `PatientName` is empty |
| **Consistency** | Same data represented differently | `DoctorName`: `Dr. Osei` vs `dr. osei`; `PaymentStatus`: `Paid` vs `paid`; Date: `2025-10-15` vs `15/10/2025` |
| **Timeliness** | Date format mismatch hinting at stale/manual entry | `P003`: `10/16/2025` — ambiguous and non-standard format |
| **Validity** | Data doesn't follow defined format rules | `AppointmentDate` uses three different formats across records |
| **Uniqueness** | Duplicate patient records | `P001` (Kwame Mensah) appears twice; `P002` (Ama Serwa) appears twice with conflicting phone numbers |

**Task 2 — Business Impact Assessment**

- **SMS Failures (Operations):** Invalid and inconsistently formatted phone numbers (`244789012` missing the leading `0`) prevent the SMS gateway from routing reminders correctly.
- **Incorrect Reports (Clinical/Management):** Duplicate `PatientID` entries inflate patient counts, producing misleading dashboards and appointment load statistics.
- **Billing Failures (Finance):** Missing `PatientName` on `P004` and inconsistent `PaymentStatus` casing break automated billing workflows that depend on exact field matching.

**Task 3 — Top 3 Recommended Fixes**

1. **Phone Number Validation Rule**
   - *Solution:* Enforce a regex pattern (`^0[0-9]{9}$`) at the point of data entry; auto-prepend `0` where missing.
   - *Owner:* Backend/App Developer
   - *Verification:* Re-run SMS batch — delivery rate should reach ≥ 95%.

2. **Date Format Standardisation**
   - *Solution:* Enforce ISO 8601 (`YYYY-MM-DD`) via a database constraint or ingestion-layer transformation script.
   - *Owner:* Data Engineer
   - *Verification:* Zero date parsing errors in appointment scheduling and reporting pipelines.

3. **Duplicate Record Prevention**
   - *Solution:* Add a unique composite key constraint on `(PatientID, AppointmentDate)`; run a one-time deduplication script on existing records.
   - *Owner:* Database Administrator
   - *Verification:* Patient count reports match the expected unique patient registry.

**Task 4 — Biggest Risk of Poor Data Consistency (Developer Specialisation)**

> From a developer's perspective, the biggest risk of poor data consistency is **silent system failures**. When the same entity (e.g., a doctor or patient) is represented in multiple formats (`Dr. Osei` vs `dr. osei`), queries, joins, and conditional logic produce incorrect results without throwing errors. The system *appears* to work, but business logic — such as grouping appointments by doctor or triggering payment workflows — silently operates on incomplete or fragmented data. This erodes stakeholder trust in the system and makes bugs extremely difficult to trace.

---

### Lab 02 — Multi-Jurisdiction Compliance Challenge

**Scenario:** ShopGhana, a Ghanaian e-commerce platform with customers in Accra, Berlin, and Los Angeles, receives three simultaneous data deletion requests in one day — each governed by a different legal framework.

---

#### Customer A — Abena (Accra, Ghana)
**Applicable Law:** Ghana Data Protection Act, 2012 (Act 843)

| Question | Analysis |
|----------|----------|
| Legal right to deletion? | Yes. Act 843 grants data subjects the right to request erasure of personal data that is no longer necessary for its original purpose. |
| ShopGhana's obligations | Must acknowledge the request, verify identity, and process deletion of all personal data no longer needed for a legitimate business purpose. |
| Data that can be retained | Transaction records may be retained for statutory financial/tax compliance (typically 6 years under Ghana Revenue Authority requirements). |
| Response deadline | Act 843 does not specify an exact deadline but requires action within a **reasonable timeframe** — best practice is **21 days**. |

---

#### Customer B — Lukas (Berlin, Germany)
**Applicable Law:** EU General Data Protection Regulation (GDPR) — Article 17

| Question | Analysis |
|----------|----------|
| Legal right to erasure? | Yes. GDPR Article 17 grants the right to erasure ("right to be forgotten") where personal data is no longer necessary for the purpose it was collected. |
| Exemptions that apply | Minimal. The delivery is complete and there are no open disputes, so no Art. 17(3) exemption (legal obligation, legal claims) applies. Basic transaction records may be retained for German tax law (§147 AO — up to **10 years**). |
| Steps ShopGhana must take | (1) Verify Lukas's identity; (2) Erase all personal data from active systems; (3) Notify all third-party processors (logistics, payment providers) to delete his data; (4) Confirm erasure in writing. |
| Response deadline | **One calendar month** from receipt of request (Art. 12 GDPR); extendable by two additional months for complex cases with notice. |
| Consequence of missed deadline | Regulatory complaint to the relevant supervisory authority (e.g., Berlin's BlnBDI); potential fines up to **€20 million or 4% of global annual turnover**, whichever is higher (Art. 83 GDPR). |

---

#### Customer C — Maria (Los Angeles, California)
**Applicable Law:** CCPA / CPRA

| Question | Analysis |
|----------|----------|
| Rights under CCPA/CPRA | Right to deletion (§1798.105), right to opt-out of sale/sharing of personal information (§1798.120), right to know what data is collected, and right to non-discrimination for exercising these rights. |
| Can deletion be processed immediately? | **No.** Maria has an active return dispute filed 20 days ago. CCPA allows businesses to retain data necessary to complete a pending transaction or resolve an open dispute. Deletion should be deferred until the dispute is resolved. |
| Response to "stop selling" request | Must be honoured **immediately and independently** of the deletion request. ShopGhana must cease selling or sharing Maria's personal data with third parties right away and update downstream data-sharing agreements accordingly. |
| Response deadline | **45 calendar days** from receipt; extendable by an additional 45 days with written notice. |
| Required disclosure to Maria | ShopGhana must inform Maria: (1) what categories of personal data are held, (2) why deletion is deferred (active dispute), (3) confirmation that the opt-out of sale has been enacted, and (4) when deletion will be completed. |

---

**Cross-Jurisdiction Takeaway**

> Operating globally means ShopGhana must maintain a **jurisdiction-aware data subject request (DSR) workflow**. The same deletion request can have entirely different deadlines, exemptions, and consequences depending on the customer's location. A single unified process will not suffice — the company needs routing logic that applies the correct regulatory framework based on the data subject's residency.

---

### Lab 03 — QuickLoan Mobile Ethical Data Review

**Role:** Independent Data Governance Consultant  
**Scenario:** QuickLoan Mobile, a Ghanaian fintech startup, uses a fully automated ML loan-scoring model. An internal audit flagged excessive data collection, absence of consent management, data quality problems, and potential algorithmic bias.

---

#### Risk Classification

| Risk Area | Issue Identified | Severity |
|-----------|-----------------|----------|
| **Legal Compliance** | No explicit consent capture at onboarding — violates Ghana DPA Act 843 Section 17 (lawful basis for processing) | 🔴 Critical |
| **Data Minimisation** | App collects entire contact lists, call logs, and social data not required for credit scoring | 🔴 Critical |
| **Data Quality** | Customer records are incomplete and inconsistently formatted, degrading model input quality | 🟠 High |
| **Algorithmic Fairness** | ML model auto-approves/rejects without bias auditing — demographic groups may be disproportionately rejected | 🟠 High |
| **Data Retention** | No defined retention policy — personal data accumulated indefinitely beyond its collection purpose | 🟡 Medium |
| **Data Classification** | Sensitive financial and identity data is not classified, leading to insufficient access controls | 🟡 Medium |

---

#### Applied Governance Principles & Solutions

**1. Data Minimisation**
- *Problem:* Collecting contact lists and call logs goes far beyond what is necessary for a loan decision.
- *Solution:* Conduct a **data necessity audit** — map each data field collected to a specific, justified use in the scoring model. Remove all fields with no direct scoring relevance.
- *Owner:* Product Manager + Data Engineer
- *Ghana DPA Basis:* Act 843, Section 18 — data must be adequate, relevant, and not excessive.

**2. Consent Management**
- *Problem:* No explicit, informed consent is captured before data collection begins.
- *Solution:* Implement a **granular consent screen** at onboarding that clearly states what data is collected, why, and how long it will be kept. Consent must be freely given, specific, and revocable.
- *Owner:* Mobile Developer + Legal/Compliance Officer
- *Ghana DPA Basis:* Act 843, Section 17 — consent is a primary lawful basis for processing personal data.

**3. Data Quality at Ingestion**
- *Problem:* Incomplete and inconsistently formatted customer records corrupt ML model inputs, leading to unreliable scoring.
- *Solution:* Add **validation rules at the data ingestion layer** — enforce required fields, standardise formats (phone numbers, dates, names), and reject or flag records that fail quality checks before they reach the model.
- *Owner:* Data Engineer
- *Verification:* Model input data quality score should reach ≥ 98% completeness and format compliance.

**4. Algorithmic Bias Mitigation**
- *Problem:* The ML model makes automated approval decisions without any bias monitoring, risking unfair treatment of demographic groups (e.g., by region, gender, or occupation type).
- *Solution:* Implement a **fairness audit pipeline** that measures approval/rejection rates across demographic segments using metrics such as Demographic Parity and Equalized Odds. Establish a human review threshold for borderline cases.
- *Owner:* ML Engineer + Data Governance Officer
- *Ethical Basis:* Automated decisions that significantly affect individuals require explainability and fairness oversight.

**5. Data Retention Policy**
- *Solution:* Define retention schedules per data category (e.g., loan application data: 5 years post-closure; contact list data: delete immediately after scoring). Automate deletion via scheduled purge jobs.
- *Owner:* Data Governance Officer + DBA

---

#### Recommended Ethical Reporting Metric

**Metric: Loan Approval Rate Disparity Index (LARDI)**

> Track and publish (internally) the ratio of loan approval rates across demographic segments — by region, gender, and occupation type. A disparity ratio exceeding **1.2x** (i.e., one group is 20% more likely to be approved than another with comparable financial profiles) triggers a mandatory model review. This metric ensures transparency and accountability in automated decision-making and directly addresses the fairness risk flagged in the audit.

---

## 🛠️ Tools & Frameworks Referenced

- **Regulatory Frameworks:** Ghana Data Protection Act (Act 843), GDPR, CCPA/CPRA
- **Data Quality Dimensions:** Accuracy, Completeness, Consistency, Timeliness, Validity, Uniqueness
- **Governance Principles:** Data Minimisation, Consent Management, Data Classification, Retention Policies
- **Bias Metrics:** Demographic Parity, Equalized Odds
- **Scripting & Validation:** Python (regex validation, deduplication), SQL (constraint enforcement)

---

## 📂 Repository Structure

```
data-governance-module/
│
├── README.md
├── lab-01-data-quality-detective/
│   ├── report.md
│   ├── medtrack-sample-dataset.csv
│   └── validation-rules.py
├── lab-02-multijurisdiction-compliance/
│   ├── report.md
│   ├── dsr-workflow-diagram.png
│   └── jurisdiction-comparison-matrix.xlsx
└── lab-03-quickloan-ethical-review/
    ├── report.md
    ├── flawed-data-flow-diagram.png
    ├── risk-register.xlsx
    └── bias-audit-framework.md
```

---

## 📌 Notes

- All datasets used in labs are synthetic — no real patient, customer, or financial data was used.
- Lab reports follow a standard format: **Scenario → Issues Identified → Impact Assessment → Recommended Solutions → Takeaway**.
- Labs are scenario-independent and can be reviewed in any order.

---

