# MPLADS AI Monitor
### AI-Powered Monitoring, Risk Detection & Decision Support for MPLADS
**Smart India Hackathon Prototype** • Ministry of Statistics and Programme Implementation (MoSPI)

---

## 🏛️ Project Overview

**MPLADS AI Monitor** is a production-style, decision-support web platform engineered for monitoring the **Members of Parliament Local Area Development Scheme (MPLADS)**. 

The platform employs advanced analytics and explainable AI risk scoring to monitor sanctions, expenditure velocity, milestones, contractor payments, and statutory compliance across thousands of developmental works.

### Key Principles:
- **Decision-Support & Early Warning**: Never makes unsubstantiated accusations of fraud. Flagged projects use precise statutory terminology: *"Potential anomaly detected"*, *"Requires verification"*, *"Unusual pattern detected"*, *"Potential compliance deviation"*.
- **Role-Based Simulation**: Real-time contextual switching between **Ministry (MoSPI)** (National), **State Nodal Authority** (State), **District Authority** (District/Block), and **Member of Parliament** (Constituency).
- **Public-Sector Visual Language**: Government-grade analytics styling, clean cards, high information density, and interactive data visualizations.
- **100% Self-Contained**: Operates immediately without external API keys or paid cloud subscriptions.

---

## 🚀 Quick Start

### 1. Prerequisites
- Node.js (v18+) & npm

### 2. Run the Development Server
```bash
npm run dev
```
Open your browser at **[http://localhost:5173/](http://localhost:5173/)**

### 3. Build for Production
```bash
npm run build
```

---

## 📑 Core Documentation & References

| Document | Description |
|----------|-------------|
| 📘 **[Source of Reference Manual](file:///c:/Users/ftshu/OneDrive/Documents/BFU/REFERENCES.md)** | Comprehensive manual: Official government policies (MoSPI, PFMS, SNA, Bhuvan), mathematical formulas, planted anomaly cheat sheet, compliance rules reference, and judge defense FAQ. |
| 📋 **[Walkthrough Guide](file:///C:/Users/ftshu/.gemini/antigravity-ide/brain/561acb91-6bc0-4b5d-a5fc-536f2ca7b749/walkthrough.md)** | Step-by-step 5–7 minute presentation flow for hackathon judges, module breakdown, and verification results. |

---

## 📊 10 Main Dashboard Modules

1. **Overview Dashboard**: Primary national monitoring hub with 8 KPI cards, fund utilization trend, project status donut, risk histogram, state performance table, and **Priority Attention Required** table.
2. **Project Monitoring**: 17-column table with multi-criteria filtering (State, District, Category, Status, Risk, Delay), search, sorting, pagination, and CSV export.
3. **Fund Analytics**: Sanction vs Release vs Expenditure velocity, monthly curves, category breakdowns, and an interactive **Progress Mismatch Scatter Matrix**.
4. **AI Risk Engine**: Transparent 7-factor weighted scoring (0–100) with an interactive **Risk Weight Simulator**.
5. **Alerts & Investigations**: Triage center for 8 anomaly categories with persistent workflow actions (*Acknowledge*, *Assign*, *Escalate*, *Mark False Positive*, *Resolve*) in `localStorage`.
6. **Compliance Monitor**: Automated evaluation against 10 statutory rules under the official MPLADS Scheme Guidelines.
7. **Geographic Intelligence**: Interactive India map with color-coded risk markers, popups, and drilldown links.
8. **Predictive Insights**: Forward-looking ML simulations forecasting delay probabilities, cost overrun risks, and 12-month ARIMA utilization curves.
9. **Reports & Export**: 6 executive report templates, preview tables, CSV export, and print-ready formal layout (`window.print()`).
10. **Data & Model Health**: Ingestion pipeline telemetry across 7 processing stages, data completeness score (94.2/100), and Security & Governance controls.

---

## 🛠️ Technology Stack

- **Frontend**: React 19, TypeScript, Vite 8, Tailwind CSS v4
- **Charts & Maps**: Recharts, Leaflet, React-Leaflet, Lucide React
- **Persistence**: Browser LocalStorage
- **Dataset**: 124 synthetic projects, 340+ payment transactions, 14 planted anomaly test cases
