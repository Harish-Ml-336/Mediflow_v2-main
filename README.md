# MediFlow AI — Hospital Intelligence Platform

> **ML-powered discharge prediction, emergency admission forecasting, and intelligent OP patient priority scoring — all running locally in your browser with zero dependencies.**

---

## Table of Contents

- [Overview](#overview)
- [Quick Start](#quick-start)
- [Features](#features)
- [Pages & Modules](#pages--modules)
- [OP Priority Scoring Algorithm](#op-priority-scoring-algorithm)
- [Dataset Management](#dataset-management)
- [CSV Format Reference](#csv-format-reference)
- [Built-in Sample Data](#built-in-sample-data)
- [How localStorage Works](#how-localstorage-works)
- [Technology Stack](#technology-stack)
- [Folder Structure](#folder-structure)
- [Known Limitations](#known-limitations)
- [Future Enhancements](#future-enhancements)

---

## Overview

MediFlow AI is a fully self-contained, single-file hospital intelligence web application. It simulates a real-world clinical decision support system that:

- Predicts **discharge times** for inpatients using EHR data patterns
- Forecasts **emergency admission surges** using 24-hour and 7-day ML models
- Ranks **outpatient (OP) patients by admission priority** using a multi-factor ML scoring engine
- Optimises **bed, ICU, and OR allocation** to cut wait times by 15% and push utilisation above 85%
- Stores all patient data **persistently in the browser's localStorage** — no server required
- Supports full **CSV import and export** of patient datasets

There is **no installation, no backend, no internet connection required**. Just open `index.html`.

---

## Quick Start

```bash
# 1. Unzip the downloaded file
unzip mediflow-ai-v2.zip

# 2. Open in browser
open mediflow-v2/index.html
# or double-click index.html in your file explorer
```

That's it. The app loads with **22 pre-seeded patients** (12 inpatients + 10 outpatients) and is immediately usable.

---

## Features

| Feature | Description |
|---|---|
| 🗄️ **Persistent Dataset** | All patient data saved to `localStorage` — survives page refresh and browser close |
| 📄 **CSV Import** | Drag-and-drop or file-picker import of any CSV matching the schema |
| ⬇️ **CSV Export** | Export full dataset as `mediflow_patients.csv` at any time |
| 🚨 **OP Priority Queue** | ML-scored ranking of outpatients by admission urgency (P1–P4) |
| ➕ **Add Patients** | Form to add new IP or OP patients with full EHR fields |
| ✏️ **Edit Records** | Inline editing of any patient record with automatic score recalculation |
| 🗑️ **Delete Records** | Remove individual patients or clear the entire dataset |
| 📊 **Live Charts** | Canvas-drawn charts for flow, utilisation, discharge timelines, forecasts |
| 🔬 **Predictions Page** | Discharge ETA cards for all inpatients with ML confidence scores |
| 📈 **Analytics Page** | Risk distribution, unit census, wait time comparisons, age analysis |
| 🔍 **Search & Filter** | Filter patients by risk level, unit, type (IP/OP), and free-text search |
| 🖱️ **Click-to-View EHR** | Click any patient row to open a full detail modal with vitals + history |
| ✅ **Admit OP Patients** | One-click admission of OP patients converts them to inpatients |
| 🕒 **Live Clock** | Real-time timestamp in the navigation bar |
| 💾 **Auto-Save Indicator** | Visual confirmation every time data is written to localStorage |

---

## Pages & Modules

### 1. 🏥 Dashboard
The main overview screen. Shows:
- KPI strip: average utilisation, total patients, critical count, OP waiting, ML accuracy
- **Live unit census** with occupancy bars for General Ward, ICU, Emergency, Neurology, and Maternity
- **24-hour flow chart** — predicted admissions vs discharges
- **Utilisation bar chart** — current occupancy by unit with 85% target line
- **Active alerts** — auto-generated based on real dataset values (critical patients, ICU near capacity, OP backlog)

### 2. 🚨 OP Priority Queue
The core clinical feature. All outpatients (type = `OP`) are ranked by their ML priority score from highest to lowest. Each card shows:
- **Rank number and priority tier** (P1 Critical / P2 Urgent / P3 Semi-urgent / P4 Routine)
- Patient name, ID, age, gender, wait time
- Chief complaint, diagnosis, comorbidities
- Live vital signs as badges
- All scoring factors that contributed to the score
- **Priority score out of 100** with a progress bar
- **Admit button** — instantly converts the OP patient to an inpatient in General Ward

### 3. 👤 Patient Records
Full EHR table for all patients with:
- Searchable by name, ID, diagnosis, unit, or complaint
- Filterable by risk level, unit, and IP/OP type
- Sortable by any column (click table headers)
- Inline **Edit** and **Delete** buttons per row
- Click any row to open the full **EHR Detail Modal** with vitals, labs, scoring breakdown, and clinical notes

### 4. 📊 Dataset Manager
The data control centre:
- **Drag-and-drop CSV import** with full parsing and upsert logic (existing IDs are updated, new IDs are added)
- **Quick Add Patient form** with all EHR fields — priority score is calculated automatically on save
- **Dataset table** showing all records with edit/delete actions
- **Download sample CSV** button for the correct schema
- **Export current data** button
- **Clear all** button (with confirmation)

### 5. 🤖 Predictions
ML prediction outputs:
- **Discharge timeline chart** — predicted vs actual discharge counts per hour
- **7-day admission forecast chart** — with confidence bands
- **Model accuracy donut chart** — 94.1% correct, 4.2% near-miss, 1.7% error
- **ML insights panel** — auto-generated from dataset (P1 count, P2 count, model tips)
- **Discharge prediction cards** — one per inpatient with ETA, risk badge, and confidence score

### 6. 📈 Analytics
Performance metrics derived from the live dataset:
- KPI strip: avg utilisation, avg LOS, avg OP wait, ML accuracy, critical count, OP count
- **Risk distribution donut** — breakdown by critical/high/medium/low
- **Patients by unit** horizontal bar chart
- **Wait time before vs after optimisation** comparison chart
- **Age distribution** histogram

---

## OP Priority Scoring Algorithm

Each outpatient is scored out of **100 points** across six clinical domains. Higher score = more urgent = admit first.

```
Total Score = Vitals + Complaint + Comorbidities + Wait Time + Labs + Age Risk
```

### Factor Breakdown

| Factor | Max Points | Key Rules |
|---|---|---|
| **Vital Signs** | 25 | BP ≥180 or ≤90 (+8), HR ≥120 or ≤50 (+7), SpO₂ ≤90% (+7), extreme temp (+3) |
| **Chief Complaint** | 20 | Chest pain / cardiac / stroke / dyspnea / fitting (+18), abdominal pain / fever / headache (+10), other (+4) |
| **Comorbidities** | 20 | CAD/CHF (+6), COPD/Asthma (+4), Diabetes (+4), CKD (+4), Hypertension (+3), Anticoagulation (+3), Cancer (+5) — capped at 20 |
| **Wait Duration** | 15 | ≥6h (+15), ≥3h (+10), ≥1h (+6), <1h (+2) |
| **Lab Values** | 12 | Blood sugar ≥300 or ≤60 (+4), WBC ≥15 or ≤3 (+3), Hb ≤8 (+3), Creatinine ≥2.5 (+3) |
| **Age Risk** | 8 | Age ≥80 (+8), ≥70 (+6), ≥65 (+4), ≤5 yrs (+7), ≤12 yrs (+4) |

### Priority Tiers

| Tier | Score Range | Colour | Action |
|---|---|---|---|
| **P1 Critical** | 80–100 | 🔴 Red | Admit immediately |
| **P2 Urgent** | 60–79 | 🟠 Orange | Admit within 2 hours |
| **P3 Semi-urgent** | 40–59 | 🟡 Yellow | Admit same day |
| **P4 Routine** | 0–39 | 🟢 Green | Schedule next available slot |

> **Risk override:** If a patient's `risk` field is explicitly set to `critical` or `high`, the score is floored at 80 or 60 respectively, ensuring they are never under-ranked.

---

## Dataset Management

### Adding Patients

**Via the form (Dataset tab):**
Fill in the Quick Add Patient form. All vitals and lab fields are optional but improve scoring accuracy. The priority score is calculated automatically when you click **Add Patient**.

**Via CSV import:**
Drag a CSV file onto the drop zone or click to browse. The system will:
- Parse all rows using the column headers
- Calculate priority scores for each patient
- Upsert records (update if ID exists, add if new)
- Save to localStorage immediately

### Editing Patients
Click the **✏ Edit** button on any row in the Patients or Dataset page. After editing vitals or complaints, the priority score recalculates automatically on save.

### Deleting Patients
Click the **🗑 Delete** button on any row. You will be prompted to confirm. To remove all data, use **Clear All** in the Dataset tab.

### Recalculating Scores
Click **🔄 Recalculate Priority** on the OP Priority page to re-run the ML scoring algorithm on the entire dataset. This is useful after bulk CSV imports or manual edits.

---

## CSV Format Reference

When importing a CSV, the first row must be headers. Column names are case-insensitive and spaces are converted to underscores automatically.

```csv
id,name,age,gender,type,unit,diagnosis,bp_sys,bp_dia,hr,spo2,temp,rr,wbc,hb,creatinine,blood_sugar,comorbidities,chief_complaint,wait_hours,admitted_date,risk,notes
```

### Column Definitions

| Column | Type | Required | Description |
|---|---|---|---|
| `id` | string | Yes | Unique patient ID (e.g. PT-001, OP-042) |
| `name` | string | Yes | Full patient name |
| `age` | integer | Yes | Patient age in years |
| `gender` | string | No | Male / Female / Other |
| `type` | string | Yes | `IP` (inpatient) or `OP` (outpatient) |
| `unit` | string | Yes | General Ward / ICU / Emergency / Neurology / Maternity / OP |
| `diagnosis` | string | Yes | Primary diagnosis |
| `bp_sys` | number | Recommended | Systolic blood pressure (mmHg) |
| `bp_dia` | number | Recommended | Diastolic blood pressure (mmHg) |
| `hr` | number | Recommended | Heart rate (beats/min) |
| `spo2` | number | Recommended | Oxygen saturation (%) |
| `temp` | number | Recommended | Temperature (°C) |
| `rr` | number | No | Respiratory rate (breaths/min) |
| `wbc` | number | No | White blood cell count (×10³/μL) |
| `hb` | number | No | Hemoglobin (g/dL) |
| `creatinine` | number | No | Serum creatinine (mg/dL) |
| `blood_sugar` | number | No | Blood glucose (mg/dL) |
| `comorbidities` | string | Recommended | Comma-separated list (e.g. "Diabetes, Hypertension") |
| `chief_complaint` | string | Recommended | Primary presenting complaint |
| `wait_hours` | number | OP only | Hours the patient has been waiting (used in scoring) |
| `admitted_date` | string | IP only | Admission date (YYYY-MM-DD) |
| `risk` | string | No | Pre-set risk: critical / high / medium / low (overrides calculated risk if provided) |
| `notes` | string | No | Free-text clinical notes |

### Example Row

```csv
OP-100,John Smith,68,Male,OP,OP,Chest Pain Evaluation,158,96,108,93,37.2,21,12.1,11.0,1.5,188,"Diabetes,Hypertension,Previous MI","Chest tightness radiating to left arm",3.5,,high,ECG changes noted
```

> **Tip:** Use the **Download Sample CSV** button in the Dataset tab to get a correctly formatted template.

---

## Built-in Sample Data

The application ships with 22 pre-loaded patient records seeded on first launch:

### Inpatients (IP) — 12 patients

| ID | Name | Unit | Diagnosis | Risk |
|---|---|---|---|---|
| PT-001 | Margaret Chen | General Ward | Pneumonia | High |
| PT-002 | Robert Okafor | ICU | Cardiac Arrest - ROSC | Critical |
| PT-003 | Aisha Patel | Maternity | Active Labour | Low |
| PT-004 | James Whitmore | Neurology | Ischaemic Stroke - TIA | High |
| PT-005 | Fatima Al-Hassan | Emergency | Acute Appendicitis | Medium |
| PT-006 | Liu Wei | General Ward | Femur Fracture Post-Op | Low |
| PT-007 | Sandra Johnson | ICU | Sepsis - Abdominal Source | Critical |
| PT-008 | Carlos Mendez | General Ward | Appendectomy Post-Op | Low |
| PT-009 | Elena Novak | Neurology | Epilepsy Management | Medium |
| PT-010 | Omar Sharif | General Ward | COPD Exacerbation | High |
| PT-011 | Priya Sharma | Maternity | C-Section Recovery | Low |
| PT-012 | Michael Torres | ICU | Post CABG Surgery | High |

### Outpatients (OP) — 10 patients

| ID | Name | Chief Complaint | Expected Priority |
|---|---|---|---|
| OP-001 | Ravi Kumar | Chest pain radiating to left arm | P1 |
| OP-010 | Dorothy Evans | Worsening breathlessness, ankle swelling | P1 |
| OP-002 | Sunita Rao | Sudden shortness of breath, wheezing | P2 |
| OP-003 | David Osei | Fall, confusion, head laceration | P2 |
| OP-005 | Peter Nguyen | Sudden onset confusion | P2 |
| OP-008 | Lakshmi Devi | Vomiting, polydipsia, high glucose (DKA) | P2 |
| OP-004 | Anjali Singh | Severe RUQ pain, vomiting | P3 |
| OP-007 | Thomas Wright | Severe headache, very high BP | P3 |
| OP-009 | Ahmed Hassan | Severe flank pain, haematuria | P3 |
| OP-006 | Maria Garcia | Severe migraine with aura | P4 |

---

## How localStorage Works

All patient data is stored in your browser's **localStorage** under the key `mediflow_patients_v3`.

```
localStorage["mediflow_patients_v3"] = JSON array of patient objects
```

- Data **persists** across page refreshes, browser restarts, and computer reboots
- Data is **browser and origin specific** — data in Chrome won't appear in Firefox
- Data is **private to your device** — nothing is sent to any server
- Typical localStorage limit is **5MB** — sufficient for several thousand patient records
- A **"✓ Saved"** indicator flashes in the top-right corner after every write operation
- To fully reset the app, click **Clear All** in the Dataset tab, or run `localStorage.removeItem('mediflow_patients_v3')` in the browser console

---

## Technology Stack

| Layer | Technology |
|---|---|
| **UI Framework** | Vanilla HTML5 / CSS3 / JavaScript (ES2020+) — no frameworks |
| **Charts** | HTML5 Canvas API — fully custom, no chart libraries |
| **Data Persistence** | Browser `localStorage` API |
| **Fonts** | Google Fonts — Fraunces (display), Outfit (UI), JetBrains Mono (data) |
| **ML / Scoring** | Rule-based weighted scoring engine (simulated ML) |
| **CSV Parsing** | Custom CSV parser (handles quoted fields with commas) |
| **Build Tools** | None — single static HTML file, no bundler required |
| **Dependencies** | None — fully self-contained |

---

## Folder Structure

```
mediflow-v2/
├── index.html        ← Entire application (HTML + CSS + JS in one file)
└── README.md         ← This file
```

All styles, scripts, and data are embedded in `index.html`. This design ensures:
- No CORS issues when opening as a local file
- No broken relative paths
- Easy distribution — just share the single file

---

## Known Limitations

- **localStorage is browser-bound** — data does not sync across devices or browsers. Use CSV export/import to transfer data.
- **ML scoring is rule-based** — the priority algorithm uses a weighted clinical ruleset, not a trained neural network. Scores are clinically informed approximations, not medical diagnoses.
- **Charts are static after page load** — charts render on tab switch. If you resize the browser window significantly, switch to another tab and back to redraw.
- **No user authentication** — this is a demonstration platform. Do not store real patient data in a browser application without appropriate security controls.
- **localStorage limit** — browsers typically cap localStorage at 5MB. For very large datasets (5,000+ patients), prefer CSV export and reimport subsets.
- **Offline only** — the app requires an internet connection only on first load to fetch Google Fonts. All other functionality works offline.

---

## Future Enhancements

- [ ] Real ML model integration via TensorFlow.js or ONNX Runtime
- [ ] IndexedDB backend for larger datasets (no 5MB cap)
- [ ] Multi-user support with session management
- [ ] PDF export of priority queue and patient reports
- [ ] Real-time WebSocket integration with hospital HIS/EMR systems
- [ ] FHIR R4 data import support
- [ ] Dark/light theme toggle
- [ ] Mobile-optimised responsive layout
- [ ] Audit log — track all admissions and data changes with timestamps
- [ ] Push notifications for P1 patient alerts

---

## License

This project is provided for educational and demonstration purposes. Not intended for use in actual clinical decision-making without appropriate validation, regulatory approval, and integration with certified medical systems.

---

*MediFlow AI — Hospital Intelligence Platform · ML Model v4.2 · Built February 2025*
