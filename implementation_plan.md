# Implementation Plan: MPLADS AI Monitor (Smart India Hackathon Prototype)

An AI-powered monitoring, risk detection, and decision-support web platform for the **Members of Parliament Local Area Development Scheme (MPLADS)**, built to government/public-sector design standards for the Smart India Hackathon.

## System Overview & Design Philosophy

The application directly addresses the problem statement:
- **Platform Name**: **MPLADS AI Monitor**
- **Tagline**: *AI-Powered Monitoring, Risk Detection & Decision Support for MPLADS*
- **Role-Based Adaptation**: Ministry (National level), State Nodal Authority (State level), District Authority (District/work level), Member of Parliament (Constituency level).
- **Public-Sector Visual Language**: Deep navy (`#0A2540`, `#0F172A`), Indian government slate blue (`#1E3A8A`, `#1D4ED8`), warm alert saffron/amber (`#D97706`), clean white cards, high information density, crisp typography, and standard audit banners.
- **Responsible AI & Governance**: Strict compliance with public administration ethics—no claims of "fraud confirmed"; all alerts framed as *"Potential anomaly detected"*, *"Requires verification"*, *"Unusual pattern detected"*, *"Potential compliance deviation"*. Prominently displays `DEMO MODE — Synthetic Data`.

---

## User Review Required

> [!IMPORTANT]
> - Node.js v24.19.0 and npm 11.17.0 were installed in user scope and are ready for Vite + React + TypeScript initialization.
> - The application is entirely self-contained: no external API keys or paid third-party services are required. All AI risk calculations, statistical anomaly detections, and predictions run client-side using deterministic, explainable algorithms on a rich dataset of 120+ synthetic MPLADS projects with 15+ deliberate anomaly cases.
> - Leaflet with OpenStreetMap tiles (and an offline SVG fallback) will power the Geographic Intelligence module for reliable operation both online and offline.

---

## Proposed Architecture & File Structure

```
BFU/
├── index.html
├── package.json
├── tsconfig.json
├── vite.config.ts
├── tailwind.config.js
├── postcss.config.js
├── src/
│   ├── main.tsx
│   ├── App.tsx
│   ├── index.css
│   ├── types/
│   │   └── index.ts                 # Project, Payment, Alert, Compliance, Anomaly, Role types
│   ├── data/
│   │   ├── projectsData.ts          # 120+ realistic Indian MPLADS projects with planted anomalies
│   │   ├── paymentsData.ts          # Hundreds of payment transaction records
│   │   ├── complianceRules.ts       # 10 official MPLADS guideline-inspired rules
│   │   └── agenciesData.ts          # Implementing agencies benchmark data
│   ├── services/
│   │   ├── riskEngine.ts            # Explainable weighted scoring (0-100) & 7 risk indicators
│   │   ├── anomalyDetection.ts      # Statistical rules (Z-score, cost overrun, velocity, duplicates)
│   │   ├── predictiveEngine.ts      # Delay probability, budget overrun risk, utilization forecast
│   │   ├── storageService.ts        # LocalStorage persistence for alert triage & investigations
│   │   └── exportService.ts         # CSV exporter & print report formatter
│   ├── context/
│   │   └── AppContext.tsx           # Global state: active role, filters, selected project, alerts, search
│   ├── components/
│   │   ├── common/
│   │   │   ├── Header.tsx           # National emblem branding, role switcher, global search, alerts counter
│   │   │   ├── Sidebar.tsx          # 10 navigation links with active indicators & role badges
│   │   │   ├── RiskBadge.tsx        # Color-coded badges (Low, Medium, High, Critical)
│   │   │   ├── StatusBadge.tsx      # Project status badges (Completed, Ongoing, Delayed, etc.)
│   │   │   ├── MetricCard.tsx       # KPI stat cards with period-over-period delta indicators
│   │   │   ├── ExplainableRiskBar.tsx# Visual component breakdown of risk score contribution
│   │   │   └── AIAssistantDrawer.tsx# Contextual AI Decision-Support assistant answering key queries
│   │   ├── ProjectDetailModal.tsx   # Comprehensive drill-down modal (info, financials, 8-stage timeline, payments, AI explanation, actions)
│   │   └── FutureMLArchitectureModal.tsx # Full system architecture diagram for judges
│   └── pages/
│       ├── OverviewPage.tsx         # National KPI cards, trend charts, donut status, risk bars, state rankings, priority attention table
│       ├── ProjectMonitoringPage.tsx# 17-column data table with multi-filters, sorting, search, pagination, CSV export
│       ├── FundAnalyticsPage.tsx    # Sanctions vs Releases vs Expenditures, monthly trends, category charts, anomaly highlights
│       ├── AIRiskEnginePage.tsx     # Methodology, transparent weights adjustment, score distribution, indicator breakdown
│       ├── AlertsPage.tsx           # 8 alert categories, severity filters, workflow actions (Acknowledge, Assign, Escalate, False Positive, Resolve)
│       ├── ComplianceMonitorPage.tsx# 10-rule compliance matrix, compliance score (0-100), rule failure diagnostics
│       ├── GeographicPage.tsx       # Interactive India map with risk markers, cluster filters, district drilldown popup
│       ├── PredictiveInsightsPage.tsx# Delay probability, cost overrun risk, 3/6/12 month utilization forecasts
│       ├── ReportsPage.tsx          # 6 downloadable report templates, filterable preview, CSV export, print-friendly executive view
│       └── ModelHealthPage.tsx      # Ingestion pipeline, dataset quality metrics, ML model health, security & governance controls
```

---

## Detailed Component Specifications

### 1. Data Model & Planted Anomaly Dataset (`src/data/`)
- **120+ synthetic projects** across 12 Indian states (Uttar Pradesh, Maharashtra, Bihar, Tamil Nadu, West Bengal, Rajasthan, Madhya Pradesh, Gujarat, Karnataka, Kerala, Odisha, Assam) and 35+ districts.
- **15+ deliberately planted anomaly cases** for judge demos:
  1. *Severe Utilization Mismatch*: e.g. "Road Construction Phase II, Varanasi" (Sanctioned: ₹45 Lakh, Expenditure: ₹41 Lakh [91%], Physical Progress: 38% -> flagged with Critical Risk 91).
  2. *Cost Overrun*: "Sub-District Hospital Oxygen Pipeline, Pune" (Estimated: ₹28 Lakh, Expenditure: ₹44.8 Lakh [+60%] without revised administrative sanction).
  3. *Chronic Delay*: "Community Health Sub-Centre, Gaya" (Expected completion 185 days ago, Progress stuck at 48%).
  4. *Potential Duplicate Work*: "Construction of Community Hall, Ward 4, Barmer" vs "Construction of Community Center, Ward 4, Barmer" (Similarity 94%, identical GPS coordinates and cost).
  5. *Abnormal Payment Clustered Spike*: 4 consecutive payments of ₹12 Lakh within 6 days to the same agency right before fiscal year-end.
  6. *High-Risk Implementing Agency*: Agency with 70% delayed projects and recurring voucher discrepancies.
  7. *Dormant Project*: 0% progress 14 months after sanction.

### 2. Explainable AI Risk Engine (`src/services/riskEngine.ts`)
Calculates a transparent `0–100` score using weighted indicators:
- **Financial Anomaly** (25% weight): Velocity spikes, single transaction caps, invoice irregularity.
- **Cost Deviation** (20% weight): Ratio of expenditure to initial sanctioned estimate.
- **Delay Risk** (15% weight): Ratio of elapsed time to expected duration vs physical progress.
- **Payment Anomaly** (15% weight): Rapid batch payouts, outlier amounts compared to category median.
- **Duplicate Similarity** (10% weight): Levenshtein/Jaccard text similarity + geo-distance proximity + category match.
- **Utilization Abnormality** (10% weight): Difference between financial progress % and physical progress %.
- **Compliance Deviation** (5% weight): Non-fulfillment of mandatory guidelines.
- **Explainability Output**: For any project, outputs an exact mathematical contribution bar chart and natural-language justifications (e.g. *"Financial progress exceeds physical progress by +53 percentage points"*).

### 3. Navigation & 10 Functional Modules
1. **Overview Dashboard**:
   - Top KPI cards: Total Projects (124), Total Sanctioned (₹342.8 Cr), Total Expenditure (₹238.4 Cr), Utilization (69.5%), Completed (52), Delayed (34), High-Risk (19), Potential Anomalies (23).
   - Recharts fund utilization timeline (Sanctioned vs Released vs Expended).
   - Donut charts for project status, risk distribution bar chart, state performance ranking table.
   - "Priority Attention Required" table with 1-click drill-down.
2. **Project Monitoring**:
   - Comprehensive table with columns: ID, State, District, Constituency, MP, Work Category, Implementing Agency, Sanctioned, Estimated, Spent, Utilization %, Dates, Status, Progress %, Risk Score, Risk Badge.
   - Instant search, multi-select dropdown filters (State, District, Category, Status, Risk Level), sorting, pagination, and CSV export.
3. **Fund Analytics**:
   - Financial breakdown: Allocation, Sanction, Release, Expenditure, Balance, Utilization %.
   - Monthly expenditure curves, State-wise fund absorption, Category-wise budget share, Sanction vs Expenditure scatter/bar analysis, Anomaly highlight tags.
4. **AI Risk Engine Page**:
   - Interactive weight simulator where officials can adjust weights to see real-time score recalculation.
   - Score distribution histogram and methodology documentation.
5. **Alerts & Investigations**:
   - 8 alert types with badge severity (Critical, High, Medium, Low).
   - Triage action buttons: *Acknowledge*, *Assign to District Collector*, *Escalate to Ministry*, *Mark as False Positive*, *Resolve*. Persists state in browser `localStorage`.
6. **Compliance Monitor**:
   - 10 MPLADS guidelines checked per project.
   - Compliance score (0-100), rule failure diagnostics, filter by Non-Compliant / Warning / Compliant.
7. **Geographic Intelligence**:
   - Interactive India Map with Leaflet showing color-coded risk markers (Green/Yellow/Orange/Red).
   - Popup cards showing project summary, expenditure, progress %, risk score, and quick view button.
8. **Predictive Insights**:
   - Simulation of ML predictive inference: Delay Probability (%), Budget Overrun Risk (%), 3/6/12 Month Fund Utilization Forecasts. Labeled "Predictive Model — Prototype".
9. **Reports & Exports**:
   - 6 structured report types: Project Risk, Financial Anomaly, Delayed Works, State Performance, Compliance Audit, High-Risk Agency.
   - Filter, data preview, CSV export, and print-friendly executive view.
10. **Data & Model Health**:
    - Data ingestion pipeline monitor, data quality metrics (94.8% completeness, schema validation, duplicate detection).
    - ML model health status (version, inference latency, drift detection, false-positive feedback loop).
    - Conceptual Security & Governance section (RBAC, tamper-evident audit logging, data encryption).
    - End-to-end Future ML Architecture flowchart.

### 4. Interactive Project Detail Modal
- Complete drill-down screen for any clicked project:
  - Header with Project ID, Work Category, Risk Score, Status.
  - Key financial cards (Sanctioned, Estimated, Released, Spent, Balance, Utilization %).
  - 8-stage Visual Timeline: *Recommendation -> Admin Sanction -> Technical Sanction -> Work Order -> Execution -> Milestone Payments -> Completion -> Asset Geotagging*.
  - Explainable Risk Contribution breakdown chart.
  - Payment transactions ledger.
  - Rule compliance audit checklist.
  - Specific official Recommended Actions.

### 5. AI Monitoring Assistant (Contextual Q&A)
- Slide-out decision support drawer with pre-computed analytical responses based on the loaded dataset:
  - *"Which projects require immediate attention?"*
  - *"Which states have the highest risk exposure?"*
  - *"Where are cost overruns increasing?"*
  - *"Which projects show financial vs physical progress mismatch?"*
  - *"Which implementing agencies show unusual delay patterns?"*

---

## Verification Plan

### Automated Verification:
- Build and bundle test: `npm.cmd run build` to ensure TypeScript compilation without errors.
- Run local dev server: `npm.cmd run dev` on port 5173.

### Manual Verification via Browser Subagent:
- Validate that the dashboard loads with government styling and `DEMO MODE — Synthetic Data` badge.
- Test role switcher (Ministry -> State Nodal -> District -> MP) and observe dashboard scope filtering.
- Test search and filters in Project Monitoring table.
- Test project drill-down: click a critical project (e.g., "Road Construction Phase II") to verify the 8-stage milestone timeline, financial details, explainable risk bars, and recommended actions.
- Test Alerts page: click "Acknowledge", "Escalate", or "Resolve" and ensure status updates smoothly.
- Test Geographic Intelligence: verify Leaflet map loads, zoom and click markers to inspect popups.
- Test Reports page: verify CSV export and report preview.
- Test AI Monitoring Assistant drawer with quick questions.

---

## Hackathon Demonstration Flow (Ready for 5–7 Minute Presentation)
1. **Step 1**: National Overview (Ministry Role) -> Point out national KPIs, utilization rates, and risk distribution.
2. **Step 2**: AI Detection Highlight -> Show the 19 High-Risk / Critical projects flagged by the algorithm.
3. **Step 3**: Drill-down to Critical Project -> Open "Road Construction Phase II", show 91 Risk Score, explainable contribution (utilization gap + delay + payment clustering), 8-stage timeline, and recommended official action.
4. **Step 4**: Alerts Triage -> Transition to Alerts & Investigations, escalate the alert to District Collector.
5. **Step 5**: Geographic Intelligence -> Show high-risk clusters across India map.
6. **Step 6**: Predictive Insights & Fund Analytics -> Display predicted delay probabilities and forecast curves.
7. **Step 7**: Export Reports & Model Health -> Generate CSV report and display the ML architecture roadmap.
