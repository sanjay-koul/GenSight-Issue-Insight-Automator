
# Phase 1 – Data Foundation (Technical Notes)

This document explains the Phase‑1 modules and their responsibilities for the repository **GenSight‑Issue‑Insight‑Automator**.

---

## 1) `src/data_loader.py`

**Key function:** `load_monthly_tracker(path: str) -> pandas.DataFrame`

**Responsibilities**

- **Sheet detection:** Accepts only monthly sheet names in the regex form `^[A-Z]{3}\d{4}$` (e.g., `DEC2025`). Non‑matching sheets are skipped.
- **Column normalization:** `_standardize_columns(df)` maps common header variations to a canonical set: `project, engineer, associate_id, associate_name, issue, start, end, status, request_id, remarks`. Unknown headers are lowercased and converted to snake_case.
- **Date coercion:** `_coerce_dates(df)` converts `start`/`end` to pandas datetimes (day‑first). It derives a normalized `date` column from `start` (fallback to `end`).
- **Metadata enrichment:** Adds `month`, `year`, and `month_label` (e.g., `DEC2025`) computed by `_parse_month_sheet` using a fixed `JAN…DEC` map.
- **Output schema:** Ensures the ordered columns:

  `['month','year','month_label','project','engineer','associate_id','associate_name','issue','start','end','status','request_id','remarks','date']`

**Notes**

- Trims string fields safely and tolerates blank rows.  
- Raises `ValueError` if no valid monthly sheets are found.  

---

## 2) `src/aggregator.py`

**Key functions**

- `classify_issue(text: str) -> str`  
  Rule‑based classifier using keyword lists for categories such as **Citrix**, **MFA**, **Endpoint**, **Access/Password**, **Network/VPN** (fallback: `Other`).

- `generate_monthly_summary(df: pandas.DataFrame) -> dict`

  Returns a dictionary with:
  - `by_month` *(DataFrame)*: counts by `month_label` (computed via `groupby`).
  - `by_status` *(dict)*: status distribution (normalized to title case).
  - `by_engineer` *(dict)*: workload by engineer (trimmed strings).
  - `by_issue_type` *(dict)*: frequency of rule‑based categories.
  - `daily` *(DataFrame)*: daily counts grouped by `month_label` and `date`.
  - `raw` *(DataFrame)*: the input with an added column `issue_type` for downstream phases.

- `filter_month(df: pandas.DataFrame, month_label: str) -> pandas.DataFrame`  
  Convenience slicer for a single month.

**Assumptions & behaviors**

- Requires the canonical columns created by the loader (especially `issue`, `status`, `engineer`, `date`, `month_label`).  
- Tolerates missing columns by creating empty series where appropriate (e.g., status/engineer normalization).  
- Daily counts exist only if `date` is present; otherwise returns an empty DataFrame with the expected headers.

---

## 3) Minimal demo usage (interactive prompt)

```python
from src.data_loader import load_monthly_tracker
from src.aggregator import generate_monthly_summary

df = load_monthly_tracker("EmblemHealth_Monthly_Productivity_Tracker.xlsx")
summary = generate_monthly_summary(df)

# Key outputs
print(summary["by_month"])       # Monthly totals
print(summary["by_issue_type"])  # Category distribution
print(summary["by_engineer"])    # Engineer workload
```

---

## 4) Operational guidance

- Keep the **same Excel filename** each month; add a new sheet named like `MONYYYY` (e.g., `MAR2026`).  
- Do **not** commit real Excel to the repo; add `*.xlsx` to `.gitignore`.  
- Phase‑1 dependencies are minimal: `pandas`, `openpyxl`.  
- When additional engineers appear in the data, `by_engineer` will automatically include them.

---

## 5) Extension points (future phases)

- **Phase 2:** consume `summary["by_month"]`, `summary["daily"]`, and `summary["by_engineer"]` to build charts (matplotlib/seaborn) and export to PPT/PDF.  
- **Phase 3:** feed `summary["raw"]` and aggregated tables into a GenAI narrative module.

