# Data Cleaning & Preparation – Methodology Notes

This document explains the data-cleaning and preparation steps performed in
`Data_Cleaning.ipynb`. The notebook was written with assistance from Cursor’s AI
but reflects my own design choices and analytical intent. This file exists to
make the workflow transparent and reproducible.

---

## 1. Objective

The purpose of the data-cleaning notebook is to take **raw U.S. waste data**
(and supporting economic datasets) and convert them into **analysis-ready
tables** suitable for:

- time-series plotting,
- regression-based growth estimation (linear and exponential),
- downstream fiscal and distributional analysis.

The cleaning phase is intentionally separated from modeling to ensure that
all transformations are explicit and auditable.

---

## 2. High-Level Workflow

The notebook follows a linear pipeline:

1. Import raw data from source files
2. Remove formatting artifacts (headers, notes, footers)
3. Standardize column names and data types
4. Subset to the relevant analytical window
5. Create helper variables for regression and plotting
6. Export or pass forward clean DataFrames

Each section of the notebook is demarcated with comment blocks to make the
workflow readable and modular.

---

## 3. File Ingestion & Setup

### Libraries
The notebook relies on a standard scientific Python stack:

- `pandas` for data manipulation
- `numpy` for numerical operations
- `matplotlib` for plotting
- `re` for light text cleaning and pattern matching

### Helper Functions
Reusable helper functions are defined early in the notebook to:

- clean numeric strings (e.g., remove commas or footnotes),
- standardize column labels,
- safely coerce values to numeric types.

This avoids repeated inline cleaning logic and makes assumptions explicit.

---

## 4. Raw Data Structure

The primary dataset used in this notebook is U.S. municipal solid waste data
(e.g., EPA MSW tables). These files typically include:

- non-data header rows,
- explanatory notes embedded in cells,
- numeric values stored as strings.

As a result, the raw files cannot be used directly for analysis.

---

## 5. Table Extraction & Cleaning

Key steps performed include:

- Identifying the correct table or sheet containing total waste figures
- Dropping non-data rows at the top and bottom of the file
- Selecting only the rows corresponding to aggregate totals
- Renaming columns to clear, consistent identifiers

For example:
- `Year` → `year`
- `Total MSW Generated` → `total_msw_generated`

This standardization is necessary for downstream merging and regression.

---

## 6. Type Standardization & Validation

After extraction, the notebook explicitly enforces data types:

- `year` is converted to an integer
- waste quantities are coerced to floats
- invalid or non-parsable entries are set to `NaN`

Basic validation checks are performed informally by:
- inspecting `df.head()` / `df.tail()`,
- confirming expected year ranges,
- verifying monotonicity where appropriate.

---

## 7. Subsetting the Analysis Window

The cleaned dataset is filtered to a consistent time window to avoid mixing
structural breaks or incomparable reporting regimes.

Typical operations include:
```python
df = df[df["year"] >= 2018]
```

This ensures compatibility with later regression and extrapolation steps.

---

## 8. Construction of Modeling Variables

To support regression analysis, the notebook constructs:

- a numeric time index (`t`) for trend estimation,
- the dependent variable vector (`y`) for modeling,
- log-transformed versions of waste data for exponential fits.

These transformations are done *after* cleaning, and used solely for the purposes of plotting to avoid contaminating the raw data.

---

## 9. Regression Preparation (Preview)

Although full modeling may occur in a separate notebook, this file prepares
the data for:

- linear trend estimation,
- exponential growth estimation via log-linear regression.

At this stage, no interpretation is imposed; the goal is only to ensure the
inputs are well-formed.

---

## 10. Design Philosophy

A few guiding principles behind this notebook:

- **Separation of concerns:** cleaning first, modeling later
- **Minimal manual intervention:** automated cleaning whenever possible
- **Transparency:** all assumptions visible in code or comments
- **Reproducibility:** rerunning the notebook reproduces the same clean output
