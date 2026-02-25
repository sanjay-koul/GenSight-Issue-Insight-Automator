
# Phase 1 – Data Foundation (Manager Brief)

**Purpose**

Phase 1 builds a reliable data foundation. It reads our monthly Excel file (we keep the same filename and add a new month tab like `DEC2025`, `JAN2026`, `FEB2026`), cleans the content, and produces clear summaries we can trust.

**What the system does**

1. **Reads the Excel** – Automatically detects monthly sheets (e.g., `DEC2025`, `JAN2026`) and combines them.  
2. **Cleans & normalizes** – Fixes column name variations, standardizes dates, and ensures all key fields exist.  
3. **Creates summaries** – Monthly totals, issue categories (Citrix, Endpoint, MFA, etc.), engineer workload, and daily counts.  

**What we tested successfully**

- Loaded the full workbook and combined months (Dec–Jan–Feb in the current file).  
- Generated monthly totals and daily trends.  
- Produced workload by engineer (currently all rows are assigned to *Sanjay*, so the workload aggregates under one name).  

**Why it matters**

- We now have a **single, clean, consistent dataset** every month.  
- This is the **input for Phase 2** (charts/reports) and **Phase 3** (GenAI narrative).  

**One‑line summary**

> The system reads our monthly Excel file, cleans it, and gives us clear summaries for monthly totals, issue categories, and engineer workload.

**How we operate monthly**

- Add a new sheet to the same Excel file with the name `MONYYYY` (e.g., `MAR2026`).  
- Run the Phase‑1 program; it automatically includes the new month.  

