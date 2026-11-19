# Change log: dashboard UI history (archived working notes)

> **Read this first.** This file is the team's working log from the build (7 Oct to 15 Nov 2026), kept for history only.
> The "Do these by hand" steps and the "If something is wrong" table below were instructions for the team at the time.
> They are **done or superseded** in the handover version (`dashboard/`). Specifically:
> - Error bars were **not** added. The three mortality chart subtitles now say "Hover over a bar to see its 95% confidence interval" (changed 16 Nov 2026).
> - The v2 files **have** been opened, checked and saved in Power BI Desktop.
> - The theme file is now `dashboard/RAHSPR_theme.json` (was `RAHSPR_theme_UI_v1.json`).
> - The REDCap token has been removed from the project files.
>
> The full, current description of the dashboard is in the S2-15 Handover Documentation (Word).

---

# SCI Dashboard — UI and Aesthetics v1 (SCIDA-146)

Hans's copy of Abrar's project (`csi_capstone.zip`, 5 Oct 2026), changed on 7 Oct 2026.
The data model (`SCI_Dashboard.SemanticModel`) is not changed. Only the look of the report.

> 🔒 `SCI_Dashboard.SemanticModel/definition/expressions.tmdl` holds the REDCap API token.
> Do not post this folder or zip anywhere public.

## Open it (Windows, Power BI Desktop)

1. Unzip, then open `SCI_Dashboard.pbip`
2. **View → Themes → Browse for themes → `RAHSPR_theme_UI_v1.json`** (in this folder)
3. Save. Power BI copies the theme into the project

Without step 2 the colour and layout changes still show, but the page background, the white
boxes and the table style do not.

## What changed

### Colour by meaning (one colour per kind of measure)

| Kind | Colour | Charts |
|---|---|---|
| Clinical: mortality, length of stay, ICU, surgery | `#334F8F` navy | Mortality by year, Median stay by age band, In-hospital mortality, Average stay, Had an ICU stay, Had surgery |
| Cost | `#148D7A` teal | Average cost per admission, Top 5 DRGs by cost, Top 5 operations by cost |
| Data quality | `#3F4652` grey | How death is recorded, Key fields filled in |

Before: navy and teal were mixed. "Had surgery" was teal like cost, and "Top 5 DRGs by cost"
was navy like mortality.

The navy and teal are Abrar's colours, adjusted slightly so they pass a colour-blind and
contrast check (`#26407A` → `#334F8F`, `#2E8B7A` → `#148D7A`). Amber stays only for the
MOCK DATA warning.

Charts with 2 or more series (Complications; Cost by year and part) use the theme palette:
navy, orange, teal, violet, in that order.

### Layout

- Page background light grey; charts, cards and tables in white boxes with a thin border
- The headline sentence under the filters has no box, so it reads as text
- **Age groups page:** the filters now sit in the same place as on the other pages. The
  Age band slot is empty there, because age is already on the charts
- **Cost page:** the note at the bottom gets the "How to read this page" heading like the
  other pages. The Cost by year chart is 15 px shorter to make room
- **Data completeness page:** "What this means" renamed "How to read this page"
- Tables: navy header row, light grey banding, horizontal lines only

### Not changed (waiting for Abrar's reply to my feedback, 6 Oct)

Page titles ("SCI patients aged 60+"), the Aboriginal or TSI filter, the two age
groupings, the "Had surgery" label, average cost per admission. Also not yet: an `n<5` rule
and an empty-filter message (planned for v2, SCIDA-147).

---

## Round 2 — 7 Oct, afternoon (items 1, 2 and 6)

This folder (`csi_capstone_UI_v1_r2`) = everything above, plus:

### 1. Uncertainty shown on the charts

- **Mortality page:** "In-hospital mortality by year" is now **"Deaths by year of admission"**,
  shown as counts. There are too few deaths in each year for a reliable rate. The rate and
  the number of admissions are in the tooltip
- **Age groups page, "In-hospital mortality":** a subtitle explains how to read the 95%
  interval. The tooltip now shows "Deaths / admissions", for example `5 / 354`
- New text measures in the `Admissions` table: `Deaths of admissions` and `Overview headline`

### 2. New first page: Overview

What the dashboard answers · 5 key numbers · mortality with its 95% CI in one sentence ·
who is included · what the dashboard cannot tell you · buttons to every page, with a
one-line guide to each. It has the same 60+ page filter as the other pages.

### 6. "Requested by" subtitles

| Chart | Requested by |
|---|---|
| Deaths by year of admission (Mortality) | Nashwa Najib (as 30-day mortality) |
| Mortality by age band (Mortality) | Dr Ryan Dorrian |
| In-hospital mortality (Age groups) | Dr Ryan Dorrian |
| Conditions coded in admissions that ended in death | Nashwa Najib |
| Top 5 DRGs by total cost | Nashwa Najib |

Source: the "Requested:" tags in the team wireframe v0.6 and the chart list of 25 Sep.

### Do these by hand in Power BI (about 5 minutes)

1. **Theme** (if not done yet): View → Themes → Browse for themes →
   `RAHSPR_theme_UI_v1.json`
2. **Error bars** — Age groups page, "In-hospital mortality" chart:
   select the chart → **Analytics** pane (magnifying glass) → **Error bars** → On →
   Upper bound: `Mortality CI upper %` · Lower bound: `Mortality CI lower %`.
   Bar colour dark grey `#1F2430`, width 2
3. **Optional — "5 / 354" on the bars:** Format → Data labels → if your version has a
   **Detail** option, add the measure `Deaths of admissions`. If not, skip: the tooltip
   already shows it
4. **Overview page buttons:** if the page-button visual shows an error, delete it and use
   Insert → Buttons → Navigator → **Page navigator** in the same place
   (top of the right column)
5. Check every page, then save

---

## Round 3 — 7 Oct, afternoon: Surgeon view and Researcher view

This folder (`csi_capstone_UI_v1_r3`) = rounds 1 and 2, plus:

**Two audiences**, following Scoping Document p.8 "Role-based access views" and the team
wireframe v0.6 (Surgeon view = a clinical summary; Researcher view = the full analysis).
Here they are separate pages, not separate permissions.

### Page 1: "Surgeon view" (replaces the Overview page)

| Row | What | Why a surgeon needs it |
|---|---|---|
| KPIs | Admissions · In-hospital mortality · Median length of stay · Had an ICU stay · Average cost per admission. Under each, one grey line of context (patients; deaths and 95% CI; average stay; ICU denominator; "fee-based estimate") | The overall picture in five numbers, each with what it is based on |
| Patients like yours | Table: age band × frailty (HFRS) band, with admissions, deaths, mortality, 95% CI, median stay, ICU % | The surgeon finds the row that matches the patient in front of them |
| Which complications are coded | Admissions per complication group | What goes wrong, in this cohort |
| Mortality by age band / by frailty band | Two charts, each with its 95% interval (error bars by hand, below) | Shows at a glance that the groups cannot be told apart yet |

No filters on this page. The 60+ page filter stays.

### Researcher pages

- "RESEARCHER VIEW" label next to MOCK DATA on all 5 pages
- "Requested by Nashwa Najib" corrected to **"Requested by Dr Nashwa Najib"** (3 charts)
- The "Requested by" labels are on the researcher pages only, not on the surgeon page

### Do these by hand in Power BI (about 10 minutes)

1. **Theme** — this is a new copy, so import it again: View → Themes → Browse for themes →
   `RAHSPR_theme_UI_v1.json`
2. **Error bars on 3 charts** — Surgeon view: "In-hospital mortality by age band" and
   "... by frailty (HFRS) band"; Age groups: "In-hospital mortality".
   Select the chart → **Analytics** pane → **Error bars** → On → Upper bound:
   `Mortality CI upper %` · Lower bound: `Mortality CI lower %` · colour `#1F2430`, width 2
3. **"Researcher view" button** on the Surgeon view: Insert → Buttons → Blank. Format →
   Button text: `Researcher view →`. Action: On · Type: **Page navigation** · Destination:
   **Mortality**. Put it at the top right, left of MOCK DATA
4. Check all 6 pages, then save

---

## Round 4 — 7 Oct, evening: filters for surgeons, and buttons between the two views

This folder (`csi_capstone_UI_v1_r4`) = rounds 1 to 3, plus Hans's feedback on round 3.

### Surgeon view

- **Filters as tick boxes, not drop-downs:** Age band · Frailty (HFRS) band · Had surgery ·
  Gender. One click ticks or unticks a box. Nothing ticked means all patients. They filter this
  page only (they are not synced with the researcher pages). Same idea as the "Filter patients"
  panel in the team wireframe v0.6
- **"Researcher view →" button** at the top right. It opens Mortality. Built into the file, so
  it is no longer a by-hand step
- The grey context lines under the five numbers are taller, so the text is no longer cut off
- Everything below the filters moved down to make room. The table, the complications chart and
  the two mortality charts are a little shorter
- "Which complications are coded" has a shorter subtitle, so it is not cut off
- The description line now points to the Researcher view button instead of "the other tabs"

### Researcher view (the other 5 pages)

- **A row of tabs at the top:** Mortality · Age groups · Deaths and conditions · Cost · Data
  completeness. The current page is dark blue. **"← Surgeon view"** goes back
- The 5 pages are **hidden in view mode**. In the Power BI Service, the page list shows only the
  Surgeon view, and the researcher pages open from the buttons. Power BI Desktop still shows all
  6 tabs at the bottom (the hidden ones with an eye icon), because that is the editing view
- To make room for the tab row, the page title, the filters and the headline sentence moved down
  a little. The charts did not move

### Do these by hand in Power BI (about 10 minutes)

1. **Theme** — a new copy, so import it again: View → Themes → Browse for themes →
   `RAHSPR_theme_UI_v1.json`
2. **Error bars on 3 charts** (same as round 3): Surgeon view "In-hospital mortality by age
   band" and "... by frailty (HFRS) band"; Age groups "In-hospital mortality". Select the chart
   → **Analytics** pane → **Error bars** → On → Upper bound `Mortality CI upper %` · Lower bound
   `Mortality CI lower %` · colour `#1F2430`, width 2. **If you skip this, change the two Surgeon
   view subtitles**, because they say "Lines show the 95% confidence interval"
3. **Test the buttons.** In Power BI Desktop, **Ctrl + click** a button (a plain click only
   selects it). In the Service, a plain click works
4. **Test the tick boxes.** One click should tick a box, without Ctrl. If it needs Ctrl: select
   the filter → Format → Slicer settings → Selection → turn off "Multi-select with CTRL"
5. If a button shows an error: delete it, then Insert → Buttons → Blank → Action: On · Type:
   Page navigation, with the same text and destination
6. Check all 6 pages, then save

Note: the label of the table's total row follows the language of the Power BI that opens the
file. English Power BI shows "Total"; Power BI in Chinese shows "總計".

---

## UI v2 — 15 Nov: three changes from the UAT feedback (SCIDA-147)

This folder (`csi_capstone_UI_v2`) = rounds 1 to 4, plus three changes asked for at the UAT
on 9 Nov. The files were edited by hand on a Mac, where Power BI does not run. Every report
file passes Microsoft's published file format check, but **nothing here has been opened in
Power BI yet. Do the checks at the end of this section.**

### 1. "Clear all filters" button (all 7 pages)

- A white button at the right end of the filter row, on every page
- One click clears every filter on that page. Filters that are shared between the researcher
  pages are cleared on those pages too, because they are the same filter
- It uses Power BI's own "Clear all slicers" button action. No bookmark is needed

### 2. Multiple selection (the 5 researcher pages)

- Admission year, Age band, Gender, Funding, Aboriginal or TSI and Had surgery now take more
  than one value. Open the list and tick the boxes. No Ctrl key is needed
- 29 filters changed. The Surgeon view tick boxes already worked this way
- **Not changed, on purpose:** "Include blood tests" and "Apply same-day discount" on the Cost
  page. They are Yes/No switches. With both values ticked the cost would quietly fall back to "No"

### 3. New page: "Compare" (Dr Ryan's suggestion)

A seventh page, at the end of the tab row on every researcher page. It is hidden in view mode,
like the other researcher pages.

| Part | What it does |
|---|---|
| Left side, right side | Each side is one group of patients. Each side has a **"Measure to show"** list (one choice) and six filters of its own (multiple choice): Admission year, Age band, Gender, Funding, Aboriginal or TSI, Had surgery |
| Measures | In-hospital mortality % · Admissions (n) · Deaths (n) · Median length of stay (days) · % with ICU stay · Average cost per admission. Both sides start on mortality |
| Under the filters | The number for that group, a grey line with admissions, deaths and the 95% confidence interval, and a table by year of admission |
| Chart at the bottom | Both sides by year of admission. **Blue = left side, orange = right side.** The line above the chart names the measure on each side |
| "Bar chart" / "Line chart" buttons | Switch the chart. Line view: two lines. Bar view: blue bars and an orange line, because Power BI cannot put two sets of bars on two different axes |
| Two axes | The left side reads on the left axis and the right side on the right axis, so a percentage and a dollar figure can share the chart |

Decisions (Hans, 15 Nov): the chart runs by admission year; two axes; no pie chart (a pie of
rates or averages is not valid); no blood test switch on this page, so cost uses the Cost
page starting values (blood tests left out, no same-day discount).

The page text says that a gap between the two sides is not a finding, and that two lines
moving together do not show a cause. The numbers are too small for that.

#### How it works (for the next team)

- The filters on the two sides must not affect each other. So each side has its own small
  lists that are **not related to any table**: `Compare Left Year`, `Compare Left Age band`,
  `Compare Left Gender`, `Compare Left Funding`, `Compare Left Aboriginal or TSI`,
  `Compare Left Had surgery`, and the same six for `Right`
- `Compare Left metric` and `Compare Right metric` hold the list of measures and the new
  measures: `Left value`, `Left admissions`, `Left deaths`, `Left value text`,
  `Left group summary`, `Left label` (and the same for `Right`), plus `Compare chart label`
- Each measure takes one of Abrar's existing measures and applies that side's ticked values
  with `TREATAS`. Nothing ticked means all values. `KEEPFILTERS` keeps the year on the chart axis
- `Left value` and `Right value` change their number format with the picked measure
  (`formatStringDefinition`, a dynamic format string)
- **None of the existing tables or measures were edited.** 14 new tables, 14 lines added to
  `model.tmdl`
- The Bar and Line buttons use two bookmarks in `SCI_Dashboard.Report/definition/bookmarks/`.
  They only show or hide the two charts. They do not touch the filters

### Do these by hand in Power BI (about 15 minutes)

1. **Open `SCI_Dashboard.pbip`.** If a yellow bar says the model has changes or tables need a
   refresh, press **Refresh now**. The new tables are calculated from data already in the
   file, so this should not need REDCap
2. **Theme** — a new copy, so import it again: View → Themes → Browse for themes →
   `RAHSPR_theme_UI_v1.json`
3. **Error bars on 3 charts** (same as round 4, step 2)
4. **Clear button:** tick some filters on each page, then Ctrl + click "Clear all filters".
   Every filter should go back to "All"
5. **Multiple selection:** on Mortality, open Gender and tick two values without Ctrl
6. **Compare page:**
   - Both "Measure to show" lists should show one picked measure. If one shows "All", pick a measure once
   - Tick Gender = Female on the left only. Only the left number, table and blue series should change
   - Ctrl + click "Line chart", then "Bar chart". The chart should switch, and the filters should stay
   - Set the right side to "Average cost per admission". The right axis should show dollars
7. Check all 7 pages, then save

### If something is wrong

| What you see | What to do |
|---|---|
| The file does not open, and the error names `formatStringDefinition` | In `Compare Left metric.tmdl` and `Compare Right metric.tmdl`, delete the one line that starts with `formatStringDefinition`. The chart still works; the axis numbers are just plain |
| The file does not open, with another error about a `Compare ...` table | Send Hans the error text. To open the file without the Compare page: delete the 14 `Compare ...` files in `SemanticModel/definition/tables/`, the 14 `ref table 'Compare ...'` lines in `model.tmdl`, and the page folder `e6compare00000000001` plus its line in `pages.json` |
| Both charts show on top of each other on the Compare page | Ctrl + click "Bar chart" once. If it stays: View → Selection pane → hide the line chart, then View → Bookmarks → right-click "Compare: bar chart" → Update |
| A filter still needs Ctrl | Select it → Format → Slicer settings → Selection → turn off "Multi-select with CTRL" |
| The line view shows one axis only | Select the line chart → Format → turn on the secondary y-axis |
