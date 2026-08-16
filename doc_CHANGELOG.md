# Project Changelog, Global Superstore BI Dashboard

A summary of the key data preparation and design decisions behind this dashboard, with the reasoning for each. (A full, granular working log was kept during development; this is the distilled, public facing version.)

---

## Data Preparation

| Change | Reason |
|---|---|
| Converted Order Date / Ship Date from text to Date type | Text dates can't be sorted, filtered, or trended correctly, required for the line chart, date slicer, and any time based analysis. |
| Left Postal Code nulls unedited (documented, not "fixed") | Nulls are structural, only the US market records postal codes in this dataset. Filling or deleting them would fabricate data. |
| Converted Postal Code from Number to Text | It's an identifier, not a quantity, as a Number, Power BI would allow meaningless aggregation (summing postal codes) and could silently drop leading zeros. |
| Verified zero duplicates, two independent methods | Cross checked via Python (pandas) and Power Query's full dataset column profiling, both agreed: 51,290 rows, 0 duplicate Row IDs, 0 duplicate full rows. |

## Dashboard Build

- **5 KPI cards** built as explicit DAX measures (not dragged raw fields), so every visual reusing "Total Sales" or "Total Profit" draws from a single, reusable definition rather than an implicit per visual calculation.
- **DISTINCTCOUNT** used for Total Orders (not COUNT or SUM), each Order ID repeats across multiple product line rows, so a plain row count would overstate order volume.
- **DIVIDE()** used for Average Sales and Profit Margin instead of `/`, avoids divide by zero errors when filters narrow the dataset to nothing, and builds on top of existing measures rather than recalculating from scratch.
- **9 visualisations** total (exceeding the 8 visual minimum): 2 bar charts, 2 column charts, 1 line chart, 1 donut chart, 1 filled map, 1 matrix, each chosen to answer one specific business question, not to fill a quota.
- **2 slicers** (Region, Order Date) for genuine interactivity, letting a manager filter to their own region or time window without rebuilding anything.

## QA Lesson Worth Noting

Mid build, all KPI cards briefly showed numbers roughly half their true value. Root cause: a leftover page level filter and a cross highlight from a clicked chart element, both silently filter every other visual on the page without an obvious warning. This was caught only because the dashboard's totals were being checked against an independently calculated (Python) source of truth, rather than assumed correct. Both issues were cleared and all totals reconciled before the dashboard was finalised.

## Design

- Colour palette: consistent Sales/Profit colour coding applied across every chart that shows both metrics, dark charcoal teal background with gold/teal/green accents, chosen to keep the dashboard scannable at a glance rather than requiring the viewer to re learn a legend on every chart.
