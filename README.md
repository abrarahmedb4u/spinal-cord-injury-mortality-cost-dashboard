# SCI 60+ Dashboard (RAH Spinal Registry) – Team S2-15

A proof-of-concept Power BI dashboard for the Royal Adelaide Hospital spine service. It shows in-hospital mortality (with 95% confidence intervals), length of stay, ICU use, coded complications and a fee-based cost estimate for spinal cord injury patients aged 60 and over, read from the RAH Spinal Registry in REDCap (live API) or from a CSV export.

> **Mock data only.** Everything here was built and tested on de-identified synthetic data. Figures are not clinical findings.
>
> **Full documentation:** [`docs/S2-15_Handover_Documentation.pdf`](docs/S2-15_Handover_Documentation.pdf) (Word version alongside). This README is a short summary of it.

Project 2026-S2-15 · COMP6000/COMP6024 ICT Master Capstone · Adelaide University · Nov 2026

## What is in the dashboard

| Page | Purpose |
|---|---|
| Surgeon view | One-page clinical summary: 5 KPIs, "Patients like yours" table, coded complications, mortality by age and frailty |
| Mortality | In-hospital mortality, deaths by year, mortality by age band and frailty band with 95% CI |
| Age groups | Mortality, cost, stay, surgery and ICU by 10-year age group (under 60 as reference) |
| Deaths and conditions | ICD-10 codes in admissions that ended in death; how death is recorded |
| Cost | Fee-based cost estimate (MBS/PBS), top DRGs and operations, blood-test and same-day switches |
| Data completeness | How complete the key REDCap fields are |
| Compare | Two groups side by side, each with its own measure and filters |

## Quick start

You need Windows and **Power BI Desktop (October 2026 or later)**, which is free.

**Just to look at it:** download or clone this repository and open `dashboard/SCI_Dashboard.pbip`. It opens with the mock data already loaded, so no token or setup is needed.

**To refresh the data from the included CSV:**

1. In Power BI: **Home → Transform data → Edit parameters**.
2. Set `CSVPath` to the full path of `data/RAHSPR_mock_data.csv` on your computer (for example `C:\Users\you\Downloads\<repo>\data\RAHSPR_mock_data.csv`). Leave `DataSource` = `CSV file`.
3. Click **OK**, then **Apply changes** on the yellow bar.

**To connect to the live REDCap project (PID 4486):** ask the REDCap administrator for an API token and save it in a text file **outside** this folder (for example `C:\RAHSPR\redcap_token.txt`). Then set `REDCapTokenFile` to that file and `DataSource` = `REDCap API`, and apply changes.

Never put a token or a real data export inside this folder. `.gitignore` blocks `*token*.txt` and every `*.csv` except the mock dataset.

## Folder structure

```
.
├── README.md
├── .gitignore
├── data/
│   └── RAHSPR_mock_data.csv            Mock REDCap export (raw codes, 1,000 synthetic patients)
├── dashboard/
│   ├── SCI_Dashboard.pbip              Open this in Power BI Desktop
│   ├── RAHSPR_theme.json               Colour theme (already embedded)
│   ├── SCI_Dashboard.Report/           Pages and visuals (PBIR JSON), bookmarks, theme
│   └── SCI_Dashboard.SemanticModel/    Data model (TMDL)
│       ├── .pbi/cache.abf              Saved mock data, so the report opens filled
│       └── definition/
│           ├── expressions.tmdl        Parameters + REDCap API / CSV source (Power Query)
│           ├── relationships.tmdl
│           ├── model.tmdl
│           └── tables/*.tmdl           Power Query, columns and DAX measures per table
└── docs/
    ├── S2-15_Handover_Documentation.docx   Full documentation
    ├── CHANGELOG_UI.md                      UI build log (history)
    ├── data_dictionary/                     REDCap codebook (PID 4486)
    ├── wireframes/                          Wireframes v0.1, v0.3, v0.4
    └── project_records/                     Presentations and minutes (signed scoping document and UAT forms are in the client zip only)
```

## How it works

REDCap (read only) → Power Query (download, split by REDCap form into Patients, Admissions, Surgery, ICD and DRG tables, derive bands, flags and fee-based costs) → semantic model (52 DAX measures) → report (7 pages). No Python, R or custom visuals.

## Key limitations

- In-hospital mortality only: 61 of the 87 admissions aged 60+ whose patient has a death date carry the same default date (10 Sep 2026), and some admissions start after the recorded death, so 30-day and 1-year mortality cannot be calculated.
- Surgery, ICD and DRG rows link to an admission only for single-admission patients, because the CHARTGUID linking field is removed from the de-identified export.
- ASIA grade and Spinal Trauma Score are empty.
- Cost is a fee-based proxy (excludes bed, nursing and theatre costs).

## User acceptance testing

9 Nov 2026, three testers: 21/21 tasks completed without help (target 85%), all three accepted the prototype. The Compare page, multiple selection and "Clear all filters" were added afterwards at their request.

## Maintenance and future work

See sections 8 and 9 of the documentation: first-time setup, common tasks (updating fees, changing bands, adding measures), troubleshooting, the list of hard-coded values, known limitations and ranked recommendations.

## Team

Abrar Ahmed Quraishi (coordinator, data) · Ashwin Pradeep (data) · Debasish Saha Pranta (data) · Anisha Ushrat Aurchi (presentation) · Chenghan (Hans) Hsieh (presentation)

Client: Royal Adelaide Hospital Spine Service · Industry partner: Prof. Ashish Diwan · Academic supervisor: Jan Stanek
