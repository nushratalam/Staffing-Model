# Behavioral Health Staffing Model
### A portfolio project in public-sector workforce analytics

> *An end-to-end worked example of how to translate operational workload data into defensible FTE requirements for a complex 24/7 health-services organization.*

![Status](https://img.shields.io/badge/status-portfolio-blue)
![Tools](https://img.shields.io/badge/tools-Power%20BI%20%7C%20Excel%20%7C%20Python%20%7C%20Plotly%20%7C%20PPTX-6B46C1)
![Scope](https://img.shields.io/badge/scope-staffing%20analytics-0C3A4A)

---

![2025 FTE need by role and facility — Power BI dashboard page 1](images/staffing_model_overview.png)

<sub>**Figure 1 — Operating baseline.** Power BI view of the 2025 staffing-model output: 269 total FTE required against 250 current, a +18.83 gap. Split 167 KCCF / 102 MRJC with per-role breakdown and facility stacking.</sub>

![2026 FTE need by scenario — Power BI dashboard page 2](images/staffing_model_scenarios.png)

<sub>**Figure 2 — 2026 initiatives.** Incremental FTE required for each forward-looking initiative, with a KCCF/MRJC split and a waterfall showing cumulative build-up. Interactive filter selects individual scenarios or the combined 2026 end-state.</sub>

---

## Overview

This repository contains a complete, reproducible staffing-model workflow built for a correctional-health context: **a synthetic operating dataset, an analytics dashboard, a formula-driven Excel staffing model with facility splits, and a leadership walkthrough presentation.**

The project answers a specific analytic question — *"given 2025 workload, how many FTE does each workforce group actually need, by facility?"* — and shows the full thinking behind that answer, not just the final number.

It was built as a portfolio artifact for senior business analyst work in public-sector and healthcare staffing. The domain is behavioral-health services in a two-jail system (King County Correctional Facility and Maleng Regional Justice Center), but the method applies to any organization that needs to link workload to staffing defensibly — hospital systems, public-health agencies, court systems, contact centers, or any 24/7 operation with multiple role groups and bargaining units.

---

## Why this project exists

Staffing plans in large public-sector organizations often collapse into one of two failure modes:

- **Percentage-based allocation** — *"nursing gets 42% of the headcount because it always has"* — which cannot defend itself against a budget question.
- **Anecdotal escalation** — *"we need more psychiatric providers because wait times are long"* — which is directionally right but cannot quantify how many more.

A proper staffing model avoids both. It connects each FTE to a specific workload activity and a defensible time standard, so that when leadership or a budget office asks *"why do you need 12 more nurses,"* the answer is a table, not an opinion.

This repository demonstrates that full workflow on a realistic problem.

---

## What's in the repo

```
├── images/
│   ├── staffing_model_overview.png          # Power BI — 2025 baseline view
│   └── staffing_model_scenarios.png         # Power BI — 2026 scenarios view
├── data/
│   └── JHS_Synthetic_Dataset_2025.xlsx      # 730-row daily fact table + workforce, equity, MOUD rollups
├── model/
│   └── JHS_Staffing_Model_V2.xlsx           # 414 live formulas, 8 sheets, facility-split FTE calc
├── dashboard/
│   └── JHS_Staffing_Dashboard_2025.html     # Standalone interactive web dashboard (Plotly + editorial typography)
├── briefing/
│   └── JHS_Staffing_Model_Briefing_Report_V2.docx    # Methodology, dataset spec, audit mapping
├── presentation/
│   └── JHS_Staffing_Model_Walkthrough.pptx  # 13-slide leadership walkthrough
└── README.md
```

### 1. Synthetic operating dataset (`data/`)

A 7-sheet Excel workbook covering January 1 through December 31, 2025, at daily grain by facility — 730 rows in the main fact table, plus workforce, equity-stratified, and MOUD-specific rollups.

**Calibrated against published public-sector baselines** so that every aggregate in the dataset lands within a few percent of a real-world target:
- Average daily population ≈ 1,411 (target 1,412)
- Annual bookings ≈ 17,956 (target 17,564)
- Behavioral health medication order within 2 days of booking: 65% → 74% trend through the year
- Median psychiatric clinic wait: 40 days → 28 days, crossing the 30-day service standard in Q4
- Release-supply rate: 18% overall (target 17%), with the audit-documented SUD vs non-SUD split preserved
- Long-acting injectable monthly volumes overlaid against real published totals for validation

Behavioral patterns are intentionally built into the data — staffing vacancies close through the year, wait times decline as staffing recovers, equity gaps narrow but persist — so the dataset tells a story a model can actually learn from.

### 2. Analytics dashboard (`dashboard/`)

A fully self-contained HTML file — no server, no build step, opens in any browser — with six tabbed views:

- **Overview** — full-year operating KPIs in one glance
- **Wait Times** — trend against the 30-day standard, first-appointment completion split by patient housing type
- **Equity** — BH-order timeliness by race/ethnicity, with a wait-time gap visible and narrowing through the year
- **MOUD Expansion** — synthetic LAI volumes overlaid against published real-world totals for calibration transparency
- **Release Meds** — SUD vs non-SUD supply rates, after-hours exposure
- **Workforce** — vacancy trends and premium-labor burn by role group

Design direction: editorial, government-credible. Typography pairs Fraunces (display serif), DM Sans (body), and JetBrains Mono (tabular numeric). Not a generic BI template.

### 3. Staffing model (`model/`)

The analytic core. An 8-sheet Excel workbook with **414 live formulas** that translates the 2025 workload volumes into FTE requirements for each of eight role groups, calculated independently for each facility.

**Method (transparent and editable):**

| Calc type | Formula |
|---|---|
| Per-encounter activity | `FTE = (Annual Volume × Minutes per Unit) ÷ Productive Minutes per FTE` |
| Per-position activity | `FTE = Positions × Coverage Factor` |

Productive minutes per FTE = **2,080 annual hours × (1 − 20% non-productive) × 60 = 99,840 minutes**.

**Output** (combined facilities):

| Role Group | Required FTE | KCCF | MRJC |
|---|---:|---:|---:|
| Nursing Services | 119.1 | 68.6 | 50.4 |
| Social Services | 54.1 | 33.9 | 20.2 |
| Psychiatric Services | 20.5 | 12.8 | 7.7 |
| Health Info & Clinical Apps | 17.0 | 11.9 | 5.1 |
| Medical & Dental Providers | 13.9 | 7.9 | 5.9 |
| Pharmacy Services | 18.9 | 10.8 | 8.1 |
| Clinical Operations | 15.0 | 10.4 | 4.6 |
| Administration | 10.4 | 10.4 | 0.0 |
| **Total** | **268.8** | **166.7** | **102.1** |

Against a 250-FTE current baseline, the model identifies a gap of ~19 FTE at current service standards — heaviest on Nursing (med-pass volume plus 24/7 coverage) and Social Services (full org-chart team structure, not only release-planning encounter work).

**Scenario sheet** prices the incremental FTE impact of nine forward-looking initiatives independently, from "tightened wait-time standard" through "1115 Waiver go-live" to "all 2026 initiatives combined." Every scenario is KCCF / MRJC split.

### 4. Briefing report (`briefing/`)

A formal briefing document covering the analytic framework, calibration anchors, dataset specification (100+ columns with definitions), and a mapping between dataset columns and the twelve concurred-with audit recommendations that anchor the work.

### 5. Leadership walkthrough (`presentation/`)

A 13-slide deck in an editorial dark-blue / teal palette, structured as a 15-minute presentation:

- Context and problem statement (3 slides)
- Method — principle, formula, inputs (3 slides)
- Results — headline, role breakdown, facility split (3 slides)
- Scenarios and limitations (3 slides)
- Close (1 slide)

Built for a budget-committee or leadership audience that needs to understand the model's logic quickly without technical background.

---

## Tooling

Everything in this repo was produced with open or widely available tools:

- **Power BI** for the interactive staffing-model dashboard shown at the top of this README (two pages: 2025 operating baseline and 2026 scenario view)
- **Python** (pandas, openpyxl, numpy) for the synthetic dataset and staffing model
- **Excel formulas** (SUMIF, cross-sheet references, editable assumption cells with blue-on-yellow convention) for the live-recalculating model

No proprietary BI tools beyond Power BI itself, which is widely available in public-sector environments. The entire project could be rebuilt or extended by anyone with a Python environment, Excel, and a browser.

---

## What this demonstrates

If you are reviewing this as part of a hiring process or portfolio evaluation, here are the competencies this project is intended to evidence:

- **Staffing and workforce analytics** — activity-based FTE modeling, coverage-factor math, scenario design
- **Data engineering** — realistic synthetic data generation with calibration validation against known baselines
- **BI and visualization** — dashboard design, chart selection, editorial typography, accessibility-conscious color palette
- **Public-sector context** — familiarity with audit-driven deliverables, equity stratification as a first-class modeling requirement, and the reality of cross-agency dependencies
- **Communication** — writing and design for a non-technical audience, with the discipline to admit what the model does *not* know
- **Reproducibility** — every number is traceable to an input; every input is editable; every calculation is transparent

The last item matters most. A staffing model that a leadership team cannot open, read, and pressure-test is a model they cannot defend. Everything here is built to be opened, read, and pressure-tested.

---

## Caveats

- **All data is synthetic.** Generated with a fixed NumPy random seed against published public-sector baselines, for demonstration purposes only. It is not operational data, does not represent real patients or real staff, and should not be used for any decision that affects real people.
- **Time standards are notional benchmarks.** Before this model could feed a real budget submission, every per-unit minute value must be validated with the responsible role-group lead. The infrastructure supports that conversation; it does not replace it.
- **Out of scope:** dollar-cost modeling, shift-level scheduling, and individual performance analysis. Those are related but separate problems.

---

## Contact

Questions or feedback welcome — open an issue, or reach out via the link on my GitHub profile.

---

<sub>Repository last updated April 2026. All artifacts can be regenerated from the Python scripts and supporting skills in the build process.</sub>
