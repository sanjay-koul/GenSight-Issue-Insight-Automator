# GenSight-Issue-Insight-Automator
End‑to‑end automation project for processing monthly Excel‑based issue tracker data. Includes data loading, validation, normalization, summary generation, visualization, automated reporting, and AI‑assisted narrative insights. 

```Structured in three phases: Data Foundation → Visualization & Reporting → GenAI‑powered Narrative Insights.```

Phase 1 – Data Foundation
This repository contains the Phase 1 (Data Foundation) module of the GenSight‑Issue‑Insight‑Automator project.
Phase 1 focuses on clean data ingestion, validation, normalization, and summary generation from monthly Excel files.
It establishes the foundation required for all downstream reporting and analytics phases.

Demo Code

from src.data_loader import load_monthly_tracker
from src.aggregator import generate_monthly_summary

df = load_monthly_tracker("EmblemHealth_Monthly_Productivity_Tracker.xlsx")
summary = generate_monthly_summary(df)

print("\n=== MONTHLY TOTALS ===")
print(summary["by_month"])

print("\n=== ISSUE CATEGORIES ===")
print(summary["by_issue_type"])

print("\n=== ENGINEER WORKLOAD ===")
print(summary["by_engineer"])