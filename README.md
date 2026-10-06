<p align="center">
  <img src="assets/ic50.png" alt="IC50 logo" width="80" height="80"/>
</p>

<h1 align="center">
  Automated dose-response curve analysis using 4-parameter logistic regression
</h1>

<p align="center">
  <img src="https://img.shields.io/badge/version-2.1.0-blue" alt="Version 2.1.0"/>
  <img src="https://img.shields.io/badge/platform-Windows-0078D6" alt="Platform: Windows"/>
  <img src="https://img.shields.io/badge/status-stable-brightgreen" alt="Status: stable"/>
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
exports are detected automatically. For S/I/R interpretation, each sheet name must
end in its strain code (e.g. `1601` or `DN1601`).

## Configuration File (Optional)

Enables parent mapping and S/I/R interpretation. Requires three sheets, with
headers in the first row. Names are not case-sensitive, column order doesn't
matter, and extra columns are ignored.

| Sheet          | Required columns                                                       |
|----------------|------------------------------------------------------------------------|
| SAR            | Compound, Parent                                                       |
| classification | Code, Bacteria, Classification                                         |
| breakpoints    | Classification, Compound, Susceptible, Intermediate, Resistant, Notes (optional) |

Breakpoints are written like `≤ 4`, `8`, `≥ 16`; use `-` where a category doesn't
apply. Classification and compound names must match across sheets and plate files.
Missing sheets disable only the features that depend on them.

## Running

Run `IC50.exe` and choose the run mode (Standard or Comparison), log base, MIC
threshold(s) (default 0.085), S/I/R interpretation, and files. Press Enter to
accept defaults. Review the summary, then press Enter to start or `r` to restart.

## Output

| File                                  | Contents                                                     |
|---------------------------------------|--------------------------------------------------------------|
| `results/<file>/<sheet>/<plate>/`     | Curve plot per compound and a text summary (`_SUMMARY.txt`)  |
| `analysis_summary.xlsx`               | All compounds: MIC, IC50, fit statistics, S/I/R              |
| `mic_threshold_comparison.xlsx`       | Comparison mode only: MIC and S/I/R at each threshold        |

Red rows in the summary mean no valid IC50 or R² below 0.90. The Consistent
column in the comparison file shows whether S/I/R agrees across thresholds.

## Methods

- **MIC:** Lowest concentration with response below the threshold. Reported as
  `<` or `>` when outside the tested range. Growth above the MIC is flagged as
  possible contamination.
- **IC50 (absolute):** Concentration where the fitted curve crosses the midpoint
  between the fitted top and a fixed baseline of 0.045, i.e.
  Y = (top + 0.045) / 2, rather than the fitted bottom.
- **S/I/R:** At or below S is Susceptible, at or above R is Resistant, otherwise
  Intermediate. Censored MICs (`<`, `>`) that could fall in more than one
  category are reported as *Not determinable*; compounds without breakpoints as
  *No breakpoint*.
