# Construction Financials Analytics Dashboard

Power BI financials dashboard for construction cost data — star schema, KPI cards, trend charts, and detail drill-down.

## Overview
![Overview](https://github.com/AdamLumbley/construction-financials-analytics/blob/main/Construction%20-%20Financials%20-%20Overview%202.png?raw=true)
KPI cards (Total Costs, Budgeted, Actual, Variance, Variance %), monthly variance trend, and breakdowns by subcontractor and project.

## Trends
![Trends](https://github.com/AdamLumbley/construction-financials-analytics/blob/main/Construction%20-%20Financials%20-%20Trends.png?raw=true)
Budgeted vs. Actual cost over time, and Actual cost broken down by Cost Type (Labor, Material, Equipment, Subcontract).

## Detail
![Detail](https://github.com/AdamLumbley/construction-financials-analytics/blob/main/Construction%20-%20Financials%20-%20Details.png?raw=true)
Full transaction-level table for drill-down and audit, filterable by the same slicers used across all pages.

---

## Data quality & modeling notes

- Preserved nulls in Actual Cost rather than defaulting to zero, since a blank value represents work not yet invoiced — zero-filling would falsely understate cost
- Found and merged a duplicate subcontractor entry ("Basalt Concrete LLC" vs "L.L.C.") during profiling, before it could split cost totals across two fake entities
- Kept City and State as separate columns (rather than dropping State) to future-proof the model in case the company expands beyond Idaho
- Retained "Subcontractor" as the precise term throughout, rather than the more common but less accurate "Contractor," since the data represents subcontracted trade work, not general contracting
- Handled months with no transactions (e.g., a single project active in only two non-consecutive months) by forcing the Variance measure to return 0 instead of blank, so the trend line shows an honest flat period instead of a misleading straight-line jump between sparse data points

## Sample DAX

```dax
Total Variance = [Total Budgeted Amount] - [Total Actual Cost]

Variance % = DIVIDE([Total Variance], [Total Budgeted Amount])

Dim_Date = 
ADDCOLUMNS(
    CALENDAR(MIN(Fact_Cost[Transaction Date]), MAX(Fact_Cost[Transaction Date])),
    "Year", YEAR([Date]),
    "Quarter", "Q" & FORMAT([Date], "Q"),
    "Month", FORMAT([Date], "MMMM"),
    "MonthNum", MONTH([Date]),
    "Year-Month", FORMAT([Date], "MMM YYYY")
)
```
