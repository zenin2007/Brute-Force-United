# SOURCE OF REFERENCE: MPLADS AI MONITOR
## Comprehensive Technical, Policy & Demonstration Reference Manual
**Smart India Hackathon Prototype** • Ministry of Statistics and Programme Implementation (MoSPI)

---

## 1. Official Government Policy & Scheme References

This platform's business logic, data models, rules, and milestone workflows are grounded in official Indian public financial administration frameworks:

| Government Document / System | Issuing Authority | Key Applied Provisions in Platform |
|------------------------------|-------------------|------------------------------------|
| **Revised MPLADS Guidelines (2023)** | Ministry of Statistics & Programme Implementation (MoSPI) | • Permissible vs Non-permissible work eligibility categories.<br>• ₹5 Crore annual MP entitlement structure.<br>• Mandatory 75-day work commencement deadline.<br>• Citizen display board requirement at project sites.<br>• Administrative sanction vs Technical sanction workflows. |
| **e-Sakshi Portal (MPLADS Revamp)** | MoSPI & National Informatics Centre (NIC) | • Paperless workflow from MP recommendation to completion.<br>• Milestone tracking data structure simulated in `src/types/index.ts`.<br>• Real-time financial ledger integration simulated in `src/data/paymentsData.ts`. |
| **Public Financial Management System (PFMS) & SNA** | Department of Expenditure, Ministry of Finance | • Single Nodal Account (SNA) fund release model.<br>• Just-in-time disbursement rule (Rule-03): subsequent installments withheld until >=80% utilization of previous tranche certified.<br>• Direct vendor settlement without intermediate parking accounts. |
| **Bhuvan MPLADS Geoportal** | National Remote Sensing Centre (NRSC), ISRO | • Mobile-based stage-wise geotagged photograph verification.<br>• Latitude / Longitude precision capture for physical public asset verification.<br>• Rule-05: Geotagged Milestone Verification check. |
| **CAG Performance Audits on MPLADS** | Comptroller and Auditor General of India (CAG) | • Identification of dormant works (&gt;12 months without progress).<br>• Identification of unapproved cost overruns exceeding 10% ceiling.<br>• Identification of duplicate work allocations between MPLADS and state schemes. |
| **General Financial Rules (GFR) 2017** | Ministry of Finance, Govt of India | • Rule 130–141: Scrutiny of Detailed Project Reports (DPR), Measurement Book (MB) verification before running bills, and tender award transparency. |

---

## 2. Mathematical & Algorithmic Reference

The AI Risk Engine operates deterministically and explainably without black-box opacity. Below are the governing mathematical formulas:

### A. Overall Risk Score Formula (Weighted Multi-Factor Ensemble)
$$\text{Risk Score} = \sum_{i=1}^{7} (W_i \times S_i) \quad \in [0, 100]$$

| Factor ($i$) | Indicator | Default Weight ($W_i$) | Raw Score Function ($S_i$) | File Reference |
|---|---|---|---|---|
| 1 | **Financial Anomaly** | 0.25 (25%) | Disproportional velocity, voucher irregularities, single-draw spikes. | [`riskEngine.ts`](file:///c:/Users/ftshu/OneDrive/Documents/BFU/src/services/riskEngine.ts) |
| 2 | **Cost Deviation** | 0.20 (20%) | Cost escalation ratio beyond initial technical estimate. | [`calculateCostDeviation`](file:///c:/Users/ftshu/OneDrive/Documents/BFU/src/services/riskEngine.ts#L17-L30) |
| 3 | **Delay Risk** | 0.15 (15%) | Overdue days scaled against physical progress. | [`calculateDelayRisk`](file:///c:/Users/ftshu/OneDrive/Documents/BFU/src/services/riskEngine.ts#L48-L62) |
| 4 | **Payment Anomaly** | 0.15 (15%) | Clustered disbursements within narrow calendar windows. | [`detectPaymentClustering`](file:///c:/Users/ftshu/OneDrive/Documents/BFU/src/services/anomalyDetection.ts#L76-L103) |
| 5 | **Duplicate Work** | 0.10 (10%) | Semantic + geospatial proximity match against other works. | [`calculateTextSimilarity`](file:///c:/Users/ftshu/OneDrive/Documents/BFU/src/services/anomalyDetection.ts#L25-L71) |
| 6 | **Utilization Gap** | 0.10 (10%) | Financial drawal % minus certified physical progress %. | [`calculateUtilizationGap`](file:///c:/Users/ftshu/OneDrive/Documents/BFU/src/services/riskEngine.ts#L33-L45) |
| 7 | **Compliance Deviation** | 0.05 (5%) | Non-fulfillment of 10 statutory guideline checks. | [`COMPLIANCE_RULES`](file:///c:/Users/ftshu/OneDrive/Documents/BFU/src/data/complianceRules.ts) |

### B. Specific Detection Formulations

#### 1. Cost Overrun Deviation
$$\text{Overrun \%} = \frac{\text{Expenditure} - \max(\text{Sanctioned}, \text{Revised})}{\max(\text{Sanctioned}, \text{Revised})} \times 100$$
- If $\text{Overrun \%} \le 0$: Score $= (\text{Expenditure}/\text{Sanctioned}) \times 20$ (Normal baseline)
- If $\text{Overrun \%} > 0$: Score $= 20 + \min(80, \text{Overrun \%} \times 1.6)$
- **Statutory Threshold**: Escalations $>10\%$ require fresh administrative sanction (MPLADS Rule-06).

#### 2. Progress Mismatch (Utilization Gap)
$$\Delta_{\text{Progress}} = \text{Financial Utilization \%} - \text{Certified Physical Progress \%}$$
- If $\Delta_{\text{Progress}} \le 10\%$: Score $= 10$ (Within normal working capital advance)
- If $10\% < \Delta_{\text{Progress}} \le 25\%$: Score $= 35$ (Observation)
- If $25\% < \Delta_{\text{Progress}} \le 40\%$: Score $= 65$ (Warning)
- If $\Delta_{\text{Progress}} > 40\%$: Score $= \min(100, 65 + (\Delta_{\text{Progress}} - 40) \times 1.5)$ (**Critical Flag**)

#### 3. Payment Clustering Algorithm (Fiscal Year-End Rush Detector)
Given chronologically sorted payments $P = [p_1, p_2, \dots, p_n]$:
$$\text{Window} = \text{Date}(p_{i+k-1}) - \text{Date}(p_i) \le 7 \text{ days}, \quad \text{where } k \ge 3$$
$$\text{If } \sum_{j=i}^{i+k-1} \text{Amount}(p_j) \ge 0.50 \times \text{Sanctioned Cost} \implies \textbf{Flagged (Score 95)}$$

#### 4. Semantic & Geospatial Duplicate Work Metric
$$\text{Similarity Score} = 0.60 \times J(T_1, T_2) + 0.20 \times L(T_1, T_2) + 0.20 \times C(\text{Cost}_1, \text{Cost}_2)$$
where:
- $J(T_1, T_2) = \frac{|W(T_1) \cap W(T_2)|}{|W(T_1) \cup W(T_2)|} \times 100$ (Jaccard Word Token Overlap)
- $L(T_1, T_2)$ = Levenshtein Distance Ratio: $\frac{\max(|T_1|, |T_2|) - \text{dist}(T_1, T_2)}{\max(|T_1|, |T_2|)} \times 100$
- $C(\text{Cost}_1, \text{Cost}_2) = \max\left(0, 100 - \frac{|\text{Cost}_1 - \text{Cost}_2|}{\text{Cost}_1} \times 100\right)$
- **Geospatial Filter**: Distance calculated via Haversine formula:
$$d = 2R \arcsin\left(\sqrt{\sin^2\left(\frac{\Delta\phi}{2}\right) + \cos(\phi_1)\cos(\phi_2)\sin^2\left(\frac{\Delta\lambda}{2}\right)}\right) \le 1.0\text{ km}$$

---

## 3. Planted Anomaly Cases Demo Cheat Sheet

Use this reference table to immediately navigate to specific planted demo cases during judge evaluations:

| Project ID | Project Name | State / District | Planted Anomaly Profile | Key Indicators | Expected Action |
|------------|--------------|------------------|-------------------------|----------------|-----------------|
| **`MPLADS-UP-VAR-001`** | CC Road and Covered Drain - Shivpur Sector 3 | Uttar Pradesh / Varanasi | **Extreme Utilization Gap** | • Spent: 91.2% (₹43.8L)<br>• Physical Done: 38%<br>• Delay: 142 days<br>• **Risk: 91 (Critical)** | Direct District Quality Monitor (DQM) for independent MB inspection; freeze SNA release. |
| **`MPLADS-MH-PUN-004`** | Hospital Oxygen Pipeline - Baramati | Maharashtra / Pune | **Cost Escalation Breach** | • Sanction: ₹28.0L<br>• Spent: ₹44.8L (**+60% overrun**)<br>• No revised sanction<br>• **Risk: 88 (Critical)** | Enforce Rule-06; audit rate contracts against state GeM portal benchmarks. |
| **`MPLADS-RJ-BAR-015`** | Community Hall - Ward 4, Balotra | Rajasthan / Barmer | **Potential Duplicate Work** | • **94.2% match** with `MPLADS-RJ-BAR-016`<br>• Distance: 120m<br>• Identical ₹35L budget<br>• **Risk: 89 (Critical)** | Field check land revenue khasra numbers; verify whether single hall is billed under dual heads. |
| **`MPLADS-WB-KOL-022`** | 250 Solar Street Lights - Ward 62 | West Bengal / Kolkata | **Year-End Payment Clustering** | • 4 vouchers within 5 days (Mar 26-30)<br>• 100% disbursed vs 52% installed<br>• **Risk: 84 (Critical)** | Audit physical light pole count; examine vendor warranty challans. |
| **`MPLADS-BR-GAY-009`** | Primary Health Sub-Centre - Tekari | Bihar / Gaya | **Chronic Project Stagnation** | • Overdue by 185 days<br>• Progress stalled at 42%<br>• Zero site logs for 90 days<br>• **Risk: 86 (Critical)** | Contractual default review; recover unspent balance from agency escrow. |
| **`MPLADS-MP-BHP-028`** | Deep Tube Well & Solar Pump - Berasia | Madhya Pradesh / Bhopal | **High-Risk Agency Benchmark** | • Agency (CRWC) has 75% delay rate<br>• 8 active payment anomalies<br>• Missing water lab test<br>• **Risk: 85 (Critical)** | Restrict awarding new MPLADS tenders to CRWC pending audit. |
| **`MPLADS-UP-LKO-031`** | Smart Classrooms in 8 Inter Colleges | Uttar Pradesh / Lucknow | **Dormant Work Anomaly** | • ₹12L advance paid 14 months ago<br>• **0% physical progress delivered**<br>• Rule-04 violation<br>• **Risk: 86 (Critical)** | Demand delivery verification of interactive panels from DIOS; invoke bank guarantee. |
| **`MPLADS-AS-KAM-065`** | Flood Protection Sluice Gate - Hajo | Assam / Kamrup | **Cost Overrun + Inspection Gap** | • +43.6% cost escalation<br>• Overdue 131 days<br>• Missing core sample test<br>• **Risk: 89 (Critical)** | Order structural core audit by State Engineering Research Institute. |
| **`MPLADS-TN-CHE-037`** | Stormwater Desilting - Madipakkam | Tamil Nadu / Chennai | **Monatorium Payment Anomaly** | • Spent ₹82L vs ₹60L sanction (+47.5%)<br>• Payments issued during monsoon ban<br>• **Risk: 87 (Critical)** | Special audit by State Principal Accountant General (PAG). |
| **`MPLADS-KA-BLR-043`** | RO Water Purification Network - Dasarahalli | Karnataka / Bengaluru Urban | **Missing Geotag & Quality Cert** | • 89% spent vs 45% operational<br>• NABL water lab cert missing<br>• Missing Bhuvan geotags<br>• **Risk: 83 (Critical)** | Withhold final tranche until NABL lab cert is verified on portal. |

---

## 4. The 10 MPLADS Statutory Compliance Rules Reference

Configured in [`complianceRules.ts`](file:///c:/Users/ftshu/OneDrive/Documents/BFU/src/data/complianceRules.ts) and evaluated live on the **Compliance Monitor Page**:

```
RULE-01: Administrative & Technical Sanction Validity
         Authority: MPLADS Guidelines Para 3.1 & 3.2
         Mandatory: YES | Weight: 15%
         Criterion: Formal administrative approval and technical estimate scrutiny must precede fund drawal.

RULE-02: MPLADS Eligible Work Category
         Authority: MPLADS Guidelines Annexure-I (Permissible Works) & Annexure-II (Prohibited Works)
         Mandatory: YES | Weight: 15%
         Criterion: Commercial assets, places of worship, and private properties strictly barred.

RULE-03: Utilization Certificate (UC) Compliance
         Authority: Ministry of Finance SNA Circular / MPLADS Para 4.3
         Mandatory: YES | Weight: 15%
         Criterion: Subsequent tranche withheld until >=80% utilization of prior release certified.

RULE-04: Mandatory Execution Timeline (12-Month Rule)
         Authority: MPLADS Guidelines Para 3.14
         Mandatory: NO  | Weight: 10%
         Criterion: Works must commence within 75 days and complete within 12 months unless extended by DM.

RULE-05: Geotagged Milestone Verification
         Authority: ISRO Bhuvan MPLADS Geoportal Mandate
         Mandatory: YES | Weight: 10%
         Criterion: Running bills must feature timestamped, geotagged on-site photographs.

RULE-06: Cost Escalation Clearance (<10% Limit)
         Authority: GFR 2017 & MPLADS Guidelines Para 3.9
         Mandatory: YES | Weight: 10%
         Criterion: Expenditure exceeding 10% of estimate requires fresh administrative sanction.

RULE-07: Non-Duplication Scheme Clearance
         Authority: MPLADS Guidelines Para 2.4
         Mandatory: NO  | Weight: 10%
         Criterion: District Collector must certify work is not co-funded under PMGSY, JJM, or state budget.

RULE-08: Public Information Board at Site
         Authority: Transparency Mandate / MPLADS Para 3.23
         Mandatory: NO  | Weight: 5%
         Criterion: Citizen display board with MP Name, Cost, and Agency must be installed at site.

RULE-09: Final Asset Registry & Handover Certificate
         Authority: MPLADS Guidelines Para 5.1
         Mandatory: YES | Weight: 5%
         Criterion: Asset must be entered in District Public Asset Register and handed over to user department.

RULE-10: Voucher Audit & Single Treasury Account (SNA)
         Authority: Department of Expenditure SNA Directives
         Mandatory: YES | Weight: 5%
         Criterion: All vendor payments routed through designated Single Nodal Account with valid GSTIN vouchers.
```

---

## 5. Technical Codebase & Symbol Index

```
c:\Users\ftshu\OneDrive\Documents\BFU\
├── index.html                           # App shell, fonts, Leaflet CSS & government meta tags
├── package.json                         # Dependencies: React 19, Recharts, Leaflet, Tailwind v4
├── tsconfig.app.json                    # Compiler flags optimized for browser execution
├── vite.config.ts                       # Vite 8 config with Tailwind v4 & React plugins
└── src/
    ├── main.tsx                         # Entry point
    ├── App.tsx                          # Main application layout, tab router & global dialogs
    ├── index.css                        # Tailwind v4 import, scrollbars, Leaflet & print styles
    ├── types/
    │   └── index.ts                     # Core TypeScript interfaces (Project, Payment, Alert, Role, Rule)
    ├── data/
    │   ├── projectsData.ts              # 124 synthetic projects + 14 planted anomaly test cases
    │   ├── paymentsData.ts              # 340+ payment transactions with voucher numbers
    │   ├── complianceRules.ts           # 10 official statutory compliance rules
    │   └── agenciesData.ts              # Implementing agencies performance benchmarks
    ├── services/
    │   ├── riskEngine.ts                # 7-factor weighted scoring & explainable contribution calculator
    │   ├── anomalyDetection.ts          # Statistical Z-scores, clustering, Levenshtein & Haversine formulas
    │   ├── predictiveEngine.ts          # Completion delay probability, cost overrun risk & ARIMA forecast
    │   ├── storageService.ts            # LocalStorage persistence for alert triage & weight customization
    │   └── exportService.ts             # CSV exporter with UTF-8 BOM & Indian Rupee currency formatters
    ├── context/
    │   └── AppContext.tsx               # Global React context: Role switching, projects, alerts, search
    ├── components/
    │   ├── common/
    │   │   ├── Header.tsx               # Govt disclaimer banner, role dropdown, search, alert counter
    │   │   ├── Sidebar.tsx              # 10 functional modules navigation with active badges
    │   │   ├── MetricCard.tsx           # High-density KPI cards with period-over-period trend delta
    │   │   ├── RiskBadge.tsx            # Color-coded risk badges (Critical, High, Medium, Low)
    │   │   ├── StatusBadge.tsx          # Execution status badges (Ongoing, Completed, Delayed, At Risk)
    │   │   ├── ExplainableRiskBar.tsx   # Stacked horizontal factor contribution visualizer
    │   │   └── AIAssistantDrawer.tsx    # Contextual Q&A drawer answering key analytical questions
    │   ├── ProjectDetailModal.tsx       # 8-stage timeline, financials, explainable bar, payment ledger
    │   ├── FutureMLArchitectureModal.tsx# Scalable production ML pipeline blueprint for judges
    │   └── LoginModal.tsx               # Role landing screen for Ministry, State, District, and MP
    └── pages/
        ├── OverviewPage.tsx             # National/State KPI cards, trend line, donut, state rankings
        ├── ProjectMonitoringPage.tsx    # 17-column table with multi-filters, sorting, pagination, CSV
        ├── FundAnalyticsPage.tsx        # Sanction vs Release vs Spend, monthly curves, progress gap scatter
        ├── AIRiskEnginePage.tsx         # Transparent methodology, interactive weight sliders, histogram
        ├── AlertsPage.tsx               # 8 alert categories, severity filters, workflow triage dialogs
        ├── ComplianceMonitorPage.tsx    # 10-rule compliance matrix with failure diagnostics table
        ├── GeographicPage.tsx           # Interactive India Leaflet map with risk markers & popups
        ├── PredictiveInsightsPage.tsx   # Delay probability, overrun risk, 12-month ARIMA utilization forecast
        ├── ReportsPage.tsx              # 6 report templates, preview tables, CSV export, print layout
        └── ModelHealthPage.tsx          # Pipeline telemetry (7 stages), data quality, security controls
```

---

## 6. Hackathon Presentation & Judge Defense FAQ

### Q1: "Does your system automatically prove fraud or accuse contractors?"
> **Answer**: No. In strict accordance with government ethics, this is an **AI-assisted monitoring and early-warning decision support system**. The system computes statistical anomalies and flags *"Potential anomalies requiring verification"*. Final decisions and field inspections remain under the statutory jurisdiction of authorized government officials (District Magistrates and State Nodal Authorities).

### Q2: "How does the system calculate risk scores without an external ML API?"
> **Answer**: The system uses a modular, explainable multi-factor weighted ensemble (7 indicators: Financial anomaly, Cost overrun, Delay days, Payment clustering, Duplicate similarity, Utilization gap, and Compliance). Each calculation is deterministic and transparent—demonstrated live in the **AI Risk Engine Weight Simulator**, where officials can inspect and adjust factor weights.

### Q3: "How does this prototype scale into real government infrastructure?"
> **Answer**: Click the **ML Architecture** button in the header. The system is designed with clean API boundaries. The frontend interfaces directly map to production microservices using Apache Kafka for data ingestion, PostgreSQL with PostGIS for geospatial analysis, Isolation Forest for payment anomalies, Sentence-BERT for duplicate work detection, and XGBoost for milestone delay forecasting.

### Q4: "What happens when an official marks an alert as 'False Positive'?"
> **Answer**: The action is persisted in local state with the officer's audit rationale. In the production architecture (Stage 05 of the ML Roadmap), these false-positive annotations flow into an automated MLflow retraining pipeline to continuously calibrate anomaly detection thresholds.

---

*Document prepared for the Smart India Hackathon Evaluation • MPLADS AI Monitor Platform*
