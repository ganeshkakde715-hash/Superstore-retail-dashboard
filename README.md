# Superstore-retail-dashboard
Superstore retail data analysis using Excel


# 📊 Superstore Sales & Profit Dashboard — Excel (Power Query, Power Pivot & DAX)

An end-to-end **Excel Business Intelligence dashboard** built on the classic *Superstore* dataset. Raw transactional data is cleaned with **Power Query**, modeled into a **star schema** with **Power Pivot**, measured with **DAX**, and visualized through an interactive **PivotChart dashboard** with **slicers** and **filters**.

![Dashboard Preview](dashboard_chart_preview.png)

---

## 📁 Repository Structure

```
Superstore-Excel-Dashboard/
│
├── Superstore_PowerQuery_DataModel.xlsx   # Main deliverable (workbook)
├── README.md                              # Project documentation (this file)
└── assets/
    └── dashboard_chart_preview.png        # Dashboard screenshot(s)
```

### Inside the workbook

| Sheet | Type | Purpose |
|---|---|---|
| `RawOrders`, `RawReturns`, `RawPeople` | Hidden staging sheets | Original, untouched source data loaded via Power Query |
| `FactOrders` | Fact table | Cleaned order-line transactions (query output) |
| `DimProducts` | Dimension | Product ID → Category → Sub-Category |
| `DimCustomers` | Dimension | Customer ID → Customer Name → Segment |
| `DimRegion` | Dimension | Region → Country → Regional Manager |
| `DimShipMode` | Dimension | Ship Mode lookup |
| `DimDate` / `DimShipDate` | Dimension | Date, Year, Quarter, Month, Weekday (Order Date & Ship Date) |
| `pivot work` | Staging | PivotTables that feed the dashboard charts |
| `Sheet3` | Staging | Supporting PivotTable/chart source |
| `Dashboard` | **Front-end** | Final interactive report — PivotCharts, slicers, KPI cards |

---

## 🎯 Business Questions Answered

This dashboard was built to answer real, stakeholder-style business questions:

1. What are **Total Sales**, **Total Profit**, and **Profit Margin** overall and by product **Category**?
2. Which **Category** (Furniture / Office Supplies / Technology) is the most profitable, and which drags margin down?
3. How do **Sales** and **Profit** vary across **Region** (Central, East, South, West) and **Regional Manager**?
4. How does performance change when filtered by **Category** and **Region** together (slicer-driven, cross-filtered view)?
5. What is the impact of **Discount** on **Profit**?
6. What share of orders are **Returned**, and does that concentrate in specific categories/regions?
7. How do **Sales** trend over time (Year / Quarter / Month) and by **Ship Mode**?

---

## 🔁 Workflow / Project Pipeline

```
Raw CSV/Source Data
      │
      ▼
1) POWER QUERY  → Clean, shape & transform (RawOrders, RawReturns, RawPeople)
      │            • Removed duplicates & blank rows
      │            • Fixed data types (dates, currency, text)
      │            • Split source into Fact + Dimension queries
      │            • Merged Returns flag into Orders (Returned column)
      ▼
2) DATA MODEL (Power Pivot) → Load queries into the Excel Data Model
      │            • Built a STAR SCHEMA:
      │              FactOrders (center) ↔ DimProducts, DimCustomers,
      │              DimRegion, DimShipMode, DimDate, DimShipDate
      │            • Defined relationships (1-to-many) between fact & dims
      ▼
3) DAX MEASURES → Wrote calculated measures on the Fact table
      │
      ▼
4) PIVOTTABLES → Summarized measures by Category / Region / Date
      │
      ▼
5) PIVOTCHARTS & DASHBOARD → Combo bar-line charts, KPI callouts
      │
      ▼
6) SLICERS & FILTERS → Region & Category slicers wired to every PivotTable
      │              for one-click, synchronized cross-filtering
      ▼
7) FINAL DASHBOARD → Single-page interactive report
```

---

## 🧱 Data Model

The workbook uses a **star schema** built entirely in the Excel Data Model (Power Pivot), rather than flat VLOOKUPs — enabling fast DAX aggregations over ~10,000 order lines.

**Fact table**
- `FactOrders` — Order ID, Order Date, Ship Date, Ship Mode, Customer ID, Segment, Region, Product ID, Category, Sub-Category, Sales, Quantity, Discount, Profit, Returned (~9,994 rows)

**Dimension tables**
- `DimProducts` — Product ID, Product Name, Category, Sub-Category (17 sub-categories / 3 categories)
- `DimCustomers` — Customer ID, Customer Name, Segment (~793 customers)
- `DimRegion` — Region, Country, Regional Manager (4 regions: Central, East, South, West)
- `DimShipMode` — Ship Mode lookup
- `DimDate` / `DimShipDate` — Date, Year, Quarter, Month Number, Month, Weekday (covers **2014–2018**)

```
                DimDate        DimShipDate
                    \             /
DimCustomers  ──┐     \         /
                 ├──  FactOrders  ──┬── DimProducts
DimShipMode  ────┘                 └── DimRegion
```

All relationships are one-to-many from each Dim table into `FactOrders`, joined on their respective ID/date keys — the standard pattern for efficient DAX filtering and slicer propagation.

---

## 🧮 DAX Measures

Key measures written on the model (rather than worksheet formulas), so every PivotTable/Chart recalculates instantly as slicers change:

| Measure | Logic | Purpose |
|---|---|---|
| `Total sales` | `SUM(FactOrders[Sales])` | Aggregate revenue |
| `Total profit` | `SUM(FactOrders[Profit])` | Aggregate profit |
| `profit margine` | `DIVIDE([Total profit], [Total sales])` | Profit as a % of sales — safe division (no `#DIV/0!`) |

> Additional CALCULATE()/DIVIDE()-based measures are defined in the model for returns/quantity analysis — visible in the workbook via **Power Pivot → Manage → Measures** or Excel's **Name Manager**.

---

## 📈 Dashboard Features

- **KPI summary** — Total Sales, Total Profit, and Profit Margin at a glance
- **Category breakdown** — combo bar (Sales) + line (Profit) PivotChart across Furniture / Office Supplies / Technology
- **Region breakdown** — Sales & Profit by Region and Regional Manager
- **Interactive Slicers** — `Region` and `Category` slicers connected to multiple PivotTables/PivotCharts for synchronized, one-click filtering
- **Report Filters** — drill from Category → Sub-Category and Region → State/City
- **Dynamic titles & callouts** — text boxes bound to cell references so headline numbers update automatically
- **Clean single-page layout** — designed to be read like a management report, not a raw spreadsheet

---

## 🖼️ Screenshots

**Sales vs. Profit by Category (Dashboard PivotChart)**

![Sales and Profit by Category](dashboard_chart_preview.png)

> *Add further screenshots of the full `Dashboard` sheet, the Region slicer view, and the Power Pivot data model diagram (Data → Manage Data Model → Diagram View) to this folder and reference them above for a complete visual walkthrough.*

---

## 🔑 Key Findings

- **Total Sales: ₹2,297,200.86** | **Total Profit: ₹286,397.02** | **Overall Profit Margin: ~12.5%**
- **Technology** is the strongest category — ₹571,180 in sales at a **~17% margin**, the highest of the three.
- **Office Supplies** sells slightly less (₹513,531) but is still healthy at a **~16% margin**.
- **Furniture** generates comparable sales (₹533,709) but profit collapses to a **~3% margin** — the category is being sold at heavily discounted prices relative to its cost, making it the clearest opportunity for margin recovery (e.g., discount-policy review).
- Regional performance is uneven across the 4 regions (Central, East, South, West), each with its own Regional Manager — useful for territory-level accountability.
- A meaningful share of orders carry a `Returned` flag, tracked at the line-item level, allowing returns to be filtered out or analyzed separately from "clean" sales.

---

## 🛠️ Tools & Skills Demonstrated

| Skill | Where it's used |
|---|---|
| **Power Query** | Importing, cleaning, and transforming `RawOrders` / `RawReturns` / `RawPeople` into model-ready tables |
| **Power Pivot / Data Model** | Star-schema relationships between `FactOrders` and 6 dimension tables |
| **DAX** | `SUM`, `DIVIDE`, `CALCULATE`-based measures for Sales, Profit, and Margin |
| **PivotTables** | Category × Region summaries feeding the dashboard |
| **PivotCharts** | Combo bar + line charts (Sales vs. Profit) |
| **Slicers** | Region & Category slicers, multi-connected to PivotTables/Charts |
| **Filters** | Report/page-level filters for Sub-Category and geography drill-down |
| **Dashboard design** | Single-page, KPI-first layout suitable for stakeholder reporting |

---

## ▶️ How to Use

1. Download / clone this repository.
2. Open `Superstore_PowerQuery_DataModel.xlsx` in **Excel (Windows, with Power Pivot enabled)**.
3. If prompted, click **Enable Content** / **Enable Editing** to allow the Data Model connections to load.
4. Go to the **Dashboard** sheet.
5. Use the **Region** and **Category** slicers to filter the report — all charts and tables update together.
6. To inspect the model: **Data tab → Manage Data Model → Diagram View**, and **Power Pivot tab → Measures** to view/edit DAX formulas.
7. To refresh with new source data: **Data tab → Queries & Connections → Refresh All**.

---

## 📌 Dataset

Based on the well-known **Sample Superstore** dataset (US retail orders, 2014–2018) — commonly used for BI/analytics practice — covering Orders, Returns, and People (Regional Managers).

---

## 🙋 About

Built as a hands-on Excel BI project to demonstrate the full analyst workflow: **Extract (Power Query) → Model (Power Pivot) → Calculate (DAX) → Visualize (PivotCharts) → Interact (Slicers/Filters)** — the same pipeline used in Power BI, applied natively inside Excel.

Feedback and suggestions are welcome — feel free to open an issue or a pull request.
