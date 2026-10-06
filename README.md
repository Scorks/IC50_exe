<p align="center">
  <img src="assets/ic50.png" alt="IC50 logo" width="80" height="80"/>
</p>

<h1 align="center">
  Automated dose-response curve analysis using 4-parameter logistic regression
</h1>

<p align="center">
  <img src="https://img.shields.io/badge/version-2.0.0-blue" alt="Version 2.0.0"/>
  <img src="https://img.shields.io/badge/platform-Windows-0078D6" alt="Platform: Windows"/>
  <img src="https://img.shields.io/badge/readers-Tecan%20%7C%20Cytation%205-8A2BE2" alt="Readers: Tecan | Cytation 5"/>
</p>

---

## Setup

```
IC50 folder/
├── IC50.exe
├── datasets/
│   ├── config.xlsx            (optional; or configuration.xlsx)
│   └── <any name>.xlsx        (plate files; one or more)
└── results/                   (created automatically)
```

Plate files can have any name and any number of sheets. Tecan and Cytation 5
exports are detected automatically. For S/I/R interpretation and CLSI QC, each
sheet name must end in its strain code (e.g. `1601` or `DN1601`).

## Configuration File (Optional)

Enables parent mapping, S/I/R interpretation and CLSI QC. Headers go in the first
row. Sheet and column names are not case-sensitive, column order doesn't matter,
and extra columns are ignored. Missing sheets disable only the features that
depend on them.

| Sheet          | Required columns                                                       |
|----------------|------------------------------------------------------------------------|
| SAR            | Compound, Parent                                                       |
| classification | Code, Bacteria, Classification                                         |
| breakpoints    | Classification, Compound, Susceptible, Intermediate, Resistant, Notes (optional) |
| QCCLSI         | Code, Drug, QC Range; Strain and CLSI Code (optional)            |

**Breakpoints** are written like `≤ 4`, `8`, `≥ 16`; use `-` where a category
doesn't apply. Classification and compound names must match across sheets and
plate files.

**QCCLSI** (optional) lists the CLSI M100 QC range for each QC strain and drug,
one row per pair. Any strain code listed here is treated as a QC strain; add rows
to support new strains or drugs. Drug names must match the plate files.

| Strain                 | Code | CLSI Code | Drug          | QC Range        |
|------------------------|------|-----------|---------------|-----------------|
| Pseudomonas aeruginosa | 1534 | 27853     | Ceftazidime   | 1,2,4           |
| Pseudomonas aeruginosa | 1534 | 27853     | Ciprofloxacin | 0.12,0.25,0.5,1 |

QC ranges can be written as a list (`1,2,4`), a range (`0.25-1`), an upper limit
(`<0.5/95`, `≤0.5`), or a combination drug (`8/152-32/608`, judged on the first
component). Use `N/A` or leave blank where CLSI gives no range.

## Starting Concentrations (Optional)

By default, every row uses the concentrations in the plate header (e.g. 0.125 to
128). If some drugs were run from a lower starting concentration, add a column to
the right of that table:

| <>            | CTRL   | 0.125  | … | 128    |   | Actual Concentration Used |
|---------------|--------|--------|---|--------|---|---------------------------|
| Ceftazidime   | 0.0518 | 0.9872 | … | 0.0845 |   |                           |
| Ciprofloxacin | 0.0563 | 0.4016 | … | 0.0500 |   | 16                        |

- The header should be in the plate's header row and be called `Top conc` or `Starting
  concentration` (equivalent).
- Enter plain numbers (`16`, not `16 ug/mL`). Blank rows use the plate header.
- The column applies only to the table it sits next to; add it to each table
  that needs it.
- The row keeps the same two-fold series, shifted to the new top (16 gives
  0.015625 to 16).

Unrecognized headers or unreadable values are flagged before and after the run.

## Running

Run `IC50.exe` and choose the run mode (Standard or Comparison), log base, MIC
threshold(s) (default 0.085), S/I/R interpretation, and files. Press Enter to
accept defaults.

The configuration summary shows the settings, config sheets loaded, CLSI QC
status, and every table with adjusted starting concentrations. Press Enter to
start or `r` to restart. When the run finishes, a recap lists anything that needs
attention.

Terminal messages use status labels:

| Label     | Meaning                                                              |
|-----------|----------------------------------------------------------------------|
| `[ OK  ]` | Choice accepted or file saved                                        |
| `[WARN ]` | Minor issue; the run continues normally                              |
| `[ALERT]` | Result needs review (possible contamination, starting-concentration issue) |
| `[FAIL ]` | CLSI QC failure                                                      |
| `[ERROR]` | A table or row could not be processed, or invalid input              |

## Output

| File                                  | Contents                                                     |
|---------------------------------------|--------------------------------------------------------------|
| `results/<file>/<sheet>/<plate>/`     | Curve plot per compound and a text summary (`_SUMMARY.txt`)  |
| `analysis_summary.xlsx`               | All compounds: MIC, starting concentration, IC50, fit statistics, S/I/R, CLSI QC. *CLSI QC* tab lists QC strains only |
| `mic_threshold_comparison.xlsx`       | Comparison mode only: MIC, S/I/R and CLSI QC at each threshold, plus a *Run Info* tab |

Red rows in the summary mean no valid IC50 or R² below 0.90. In the comparison
file, `SIR_Consistent` shows whether S/I/R agrees across thresholds and
`QC_Consistent` whether the QC result does.
