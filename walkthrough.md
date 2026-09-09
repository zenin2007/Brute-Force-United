# Walkthrough: MPLADS AI Monitor (Smart India Hackathon)

**Platform**: **MPLADS AI Monitor**  
**Tagline**: *AI-Powered Monitoring, Risk Detection & Decision Support for MPLADS*  
**Environment**: React 19 + TypeScript + Vite + Tailwind CSS + Recharts + Leaflet + Lucide  
**Local Server**: `http://127.0.0.1:5173/` (Running and Verified)

---

## 1. Executive Summary

The **MPLADS AI Monitor** has been built as a serious, public-sector grade, decision-support web platform specifically addressing the Smart India Hackathon problem statement. 

In strict adherence to public administration ethics, the system operates as an **AI-assisted monitoring and early-warning decision support platform**. It never makes arbitrary accusations of fraud (using precise language such as *"Potential anomaly detected"*, *"Requires verification"*, *"Unusual pattern detected"*, *"Potential compliance deviation"*), ensuring that statutory audit and disciplinary authority remains with authorized government officials. Every page prominently displays the `DEMO MODE — Synthetic Data` badge.

---

## 2. Core Architecture & User Roles

### Role-Based Simulation
The system features a real-time role selector in the top navigation bar with dynamic dashboard re-scoping:
1. **Ministry (MoSPI)**: National-level strategic overview across all States, Union Territories, and Parliamentary constituencies.
2. **State Nodal Authority (e.g. Uttar Pradesh)**: State-wide monitoring of sanctioned works, releases, and district performance rankings.
3. **District Authority (e.g. Varanasi)**: District/block level execution, Measurement Book (MB) verification, and agency audits.
4. **Member of Parliament (e.g. Varanasi PC-77)**: Constituency works recommended, expenditure velocity, physical execution, and citizen welfare impact.

---

## 3. The 10 Functional Modules Built & Verified

| # | Module | Core Capabilities |
|---|--------|-------------------|
| 1 | **Overview Dashboard** | 8 primary KPI cards (Sanctioned, Expended, Utilization %, Completed, Delayed, Critical/High Risk, Anomalies), fund utilization trajectory line chart, project status donut chart, AI risk histogram, state performance ranking table, and **Priority Attention Required** table with 1-click drilldown. |
| 2 | **Project Monitoring** | 17-column comprehensive data table with real-time text search, multi-criteria filtering (State, District, Work Category, Status, Risk Level, Delay Duration), column sorting, pagination, and one-click **Export to CSV**. |
| 3 | **Fund Analytics** | Financial governance dashboard: Sanction vs Release vs Expenditure curves, monthly disbursement velocity, category-wise budget absorption, and an interactive **Progress Mismatch Scatter Matrix** with anomaly highlighting. |
| 4 | **AI Risk Engine** | Transparent explainable AI methodology: 7 weighted indicators (Financial, Cost Overrun, Delay, Payment Clustering, Duplicate Work, Utilization Gap, Compliance). Includes an **Interactive Weight Simulator** allowing officials to adjust sensitivities and see real-time recalculation across the dataset. |
| 5 | **Alerts & Investigations** | Audit triage center covering 8 anomaly categories. Allows officials to **Acknowledge**, **Assign**, **Escalate**, **Mark False Positive**, or **Resolve** alerts, with actions persisted in `localStorage`. |
| 6 | **Compliance Monitor** | Automated evaluation of 10 statutory rules under the official MPLADS Scheme Guidelines (e.g., 12-month rule, 10% cost escalation ceiling, non-duplication certification, geotagged milestone photos) with failure diagnostics. |
| 7 | **Geographic Intelligence** | Interactive India Map powered by Leaflet with color-coded risk markers (Red = Critical, Orange = High, Yellow = Medium, Green = Low). Clickable markers display popup summary cards with direct drill-down links. |
| 8 | **Predictive Insights** | Forward-looking ML forecasting simulation: predicted completion delay probabilities (%), estimated cost overrun risks (%), early-warning signals, and a 12-month fund utilization trajectory with a 95% confidence interval. |
| 9 | **Reports & Export** | Official government report generator offering 6 executive templates (Project Risk, Financial Overrun, Delayed Works, State Benchmarking, Compliance Audit, Agency Benchmarking). Includes table preview, CSV export, and a print-ready formal government layout (`window.print()`). |
| 10 | **Data & Model Health** | Technical pipeline telemetry covering the 7 ingestion stages (Ingestion -> Schema Validation -> Feature Engineering -> Anomaly Detection -> Risk Scoring -> Alert Generation -> Human Verification), data completeness metrics, and Security & Governance controls (RBAC, tamper-evident audit trail, AES-256 encryption). |

---

## 4. Key Interactive Modals & Decision Support Tools

1. **Comprehensive Project Detail Modal**:
   - Financial breakdown (Sanctioned, Estimated, Revised, Released, Spent, Remaining Balance, Utilization %).
   - **8-Stage Milestone Timeline**: *MP Recommendation -> Admin Sanction -> Technical Sanction -> Work Order -> Ground Execution -> Milestone Payments -> Project Completion -> Asset Geotagging & Handover*.
   - **Explainable AI Breakdown**: Horizontal visual bar chart showing the exact mathematical percentage contribution of each factor to the final Risk Score.
   - Payment voucher audit ledger with clearing statuses.
   - Official Recommended Actions.
2. **AI Monitoring Assistant Drawer**:
   - Slide-out decision support assistant providing instant contextual answers over the dataset:
     * *"Which projects require immediate attention?"*
     * *"Which states have the highest risk exposure?"*
     * *"Where are cost overruns increasing without revised sanction?"*
     * *"Which projects show financial vs physical progress mismatch?"*
     * *"Which implementing agencies exhibit recurring anomalies?"*
3. **Future Production ML Architecture Modal**:
   - End-to-end production architecture flowchart detailing how the system scales to 100,000+ national works using Apache Kafka, PostgreSQL + PostGIS, Isolation Forest, Sentence-BERT, XGBoost, and human-in-the-loop retraining.
4. **Role Selection Landing Modal**:
   - Accessible via login or the top bar to launch directly into Ministry, State, District, or MP mode.

---

## 5. Deliberately Planted Demo Anomaly Cases

The dataset includes **124 realistic Indian MPLADS projects** across 12 States, 35+ Districts, 40+ MPs, and multiple implementing agencies, with 14 prominent anomaly cases engineered for hackathon judge presentations:

1. **`MPLADS-UP-VAR-001` (Shivpur CC Road, Varanasi)**:
   - **Sanctioned**: ₹ 48 Lakh | **Expenditure**: ₹ 43.8 Lakh (91.2%) | **Physical Progress**: 38%
   - **Risk Score**: 91 (Critical)
   - **Anomaly**: Severe utilization gap (+53.2%), overdue by 142 days, clustered payments.
2. **`MPLADS-MH-PUN-004` (Hospital Oxygen Line, Pune)**:
   - **Sanctioned**: ₹ 28 Lakh | **Expenditure**: ₹ 44.8 Lakh (+60% overrun)
   - **Risk Score**: 88 (Critical)
   - **Anomaly**: Breach of statutory 10% cost escalation cap without revised administrative sanction.
3. **`MPLADS-RJ-BAR-015` vs `MPLADS-RJ-BAR-016` (Community Hall, Barmer)**:
   - **Anomaly**: 94.2% semantic and geospatial duplicate work match located 120m apart with matching ₹35 Lakh budgets across rural (DRDA) and urban municipal heads.
4. **`MPLADS-WB-KOL-022` (Solar Street Lights, Kolkata)**:
   - **Anomaly**: Payment clustering: 4 disbursements totaling ₹32 Lakh executed in 5 days (March 26-30) right before fiscal year end.
5. **`MPLADS-UP-LKO-031` (Smart Classrooms, Lucknow)**:
   - **Anomaly**: Dormant project: ₹12 Lakh mobilization advance released 14 months ago with zero (0%) physical delivery reported.

---

## 6. Hackathon Demonstration Flow (5–7 Minutes)

When demonstrating this prototype to hackathon judges, follow this step-by-step storyline:

```
[Step 1: National Overview]
- Open http://127.0.0.1:5173/ as Ministry Official.
- Point out the national KPIs (124 works, ₹342.8 Cr sanctioned, 69.5% utilization).
- Highlight the "DEMO MODE — Synthetic Data" banner and explain the responsible AI philosophy.

[Step 2: AI Anomaly Discovery]
- Direct the judges' attention to the "Priority Attention Required" table on Overview.
- Show that the algorithm automatically flagged 19 high-risk and critical works.

[Step 3: Drill-Down into Critical Project]
- Click "Inspect" on MPLADS-UP-VAR-001 (Shivpur CC Road, Varanasi).
- Show the 91 Risk Score and switch to the "AI Risk Breakdown" tab to show the exact mathematical contribution bars (Utilization gap + delay + payment anomaly).
- Show the 8-stage visual milestone timeline and the payment ledger.
- Highlight the official recommended verification actions. Close the modal.

[Step 4: Role-Based Switching]
- Open the Role Switcher in the header and switch to "District Authority (Varanasi)".
- Show how the overview and table immediately re-scope to Varanasi district works only. Switch back to Ministry.

[Step 5: Alerts & Triage Workflow]
- Navigate to "Alerts & Investigations".
- Show the 8 anomaly categories. Click "Acknowledge" on the first alert and persist notes.
- Point out that the action is saved locally and logged in the audit trail.

[Step 6: Geographic Intelligence]
- Navigate to "Geographic Intelligence".
- Show the interactive India map with red, orange, and green risk markers. Click a red marker to open the popup card.

[Step 7: AI Decision Assistant & Predictive Insights]
- Click "AI Decision Assistant" in the header to open the slide-out drawer.
- Click "Which projects show financial vs physical progress mismatch?" to reveal instant analytical answers with clickable project cards.
- Navigate to "Predictive Insights" to show the 12-month ARIMA utilization forecast and early-warning delay probabilities.

[Step 8: Reports & Future Roadmap]
- Navigate to "Reports & Export" to show the formal government print-ready preview and CSV export.
- Click "ML Architecture" in the header to show the production roadmap to the judges.
```

---

## 7. Verification & Build Results

- **TypeScript Compilation**: Clean build (`tsc -b && vite build`) with zero errors.
- **Production Asset Bundle**:
  * `dist/index.html`: 1.39 kB
  * `dist/assets/index-D4BGp0Ya.css`: 48.30 kB
  * `dist/assets/index-CB0gbkH1.js`: 999.84 kB
- **Local Dev Server**: Verified listening on `http://127.0.0.1:5173/` (HTTP 200).
- **Self-Contained**: 100% functional without external API keys or paid cloud subscriptions.
