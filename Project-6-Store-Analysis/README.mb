# Retail Store Sales, Promotion & Dual-Calendar Analytics (Power BI)

An enterprise multi-page Power BI analytical solution built on a classic star schema architecture with role-playing date dimensions to track retail store performance, promotional campaign effectiveness, profitability margins, and product catalog distributions.

---

## 📌 Project Overview

This dashboard provides end-to-end operational visibility across retail sales channels, product hierarchies, promotional discounts, and geographic customer bases across India. It implements an advanced dual-calendar modeling pattern that allows business users to compare two independent date ranges side-by-side using active and inactive DAX relationship overrides (`USERELATIONSHIP`).

* **Architecture:** Star Schema (1 Fact Table, 3 Master Dimensions, 2 Independent Role-Playing Date Dimensions)
* **Format:** Power BI Project / Desktop Model (`Innovate` Theme applied)
* **Storage Mode:** Import Mode
* **Data Pipeline:** Microsoft Excel (`Store+Data.xlsx`) transformed via multi-step Power Query (M) joins, calculated pricing additions, and automated margin logic.

---

## 📊 Dashboard Views & Architecture

The report is structured into five core analytical views:

### 1. Executive Sales, Geography & Promotions Overview
A high-level cockpit monitoring overarching revenue, regional distribution, and promotional campaign drag.

* **KPI Card:** Tracks cumulative order volume (3.51K Total Orders).
* **Sales by City Map:** Geospatial bubble distribution highlighting revenue hubs across major metropolitan markets in India (e.g., New Delhi, Mumbai, Bengaluru).
* **Promotional Discount Impact:** Bar chart breaking down average discount values surrendered across marketing campaigns (`Weekend Flash Sale` yielding ~23K average discount, followed by `Clearance Sale` and `Summer Sale`).
* **Profit vs. Net Sales Correlation:** Scatter visual illustrating fixed 10% operating margin linear scaling across net revenues.
* **Chronological Trend Ribbon:** Trailing multi-year run-rate tracking total daily/weekly sales spikes from 2020 through 2024.

![Executive Sales & Geography](<Project Visuals/1.png>)

---

### 2. Product Catalog Pareto & Ranking Analysis
Isolates top-performing SKUs against long-tail inventory to evaluate inventory turnover and product line margins.

* **Revenue Drivers:** Top 5 products by net revenue (led by Apple iPhone 14 at 21.4M, Apple MacBook Air at 19.6M, and Sony Bravia 55" TV at 19.4M).
* **Volume Drivers:** Top 5 products by units moved (Apple iPhone 14, Raymond Suit, Fossil Smartwatch, Zara Casual Shirt).
* **Underperforming Segments:** Pinpoints bottom 5 products by revenue and unit volume (e.g., Colgate Toothpaste at 0.02M, Dove Soap, Nivea Body Lotion, Tupperware)
![Product Performance](<Project Visuals/2.png>)

---

### 3. Dual-Calendar Comparative Overview (Grouped)
Leverages `Date Table 1` and `Date Table 2` to benchmark performance across two independent time horizons within unified grouped visuals.

* **Side-by-Side Barmetric Comparison:**
  * **Total Sales:** Compares `Sales 1` vs. `Sales 2` (e.g., 122M baseline).
  * **Total Profit:** Benchmarks `Profit 1` vs. `Profit 2` (12.2M baseline).
  * **Total Quantity Sold:** Benchmarks `Quantity 1` vs. `Quantity 2` (7.1K units moved).

![Dual Calendar Grouped Analysis](<Project Visuals/3.png>)

---

### 4. Dual-Calendar Split Window Horizon Analysis
Presents isolated horizontal split tiles for Date Filter 1 (e.g., custom window 1/1/2020 – 3/21/2023) against Date Filter 2 (full historical period 1/1/2020 – 1/1/2024).

* Enables instant variance auditing for Net Sales (97M vs. 122M), Profitability (9.7M vs. 12.2M), and Quantity Sold (5.6K vs. 7.1K units).

![Dual Calendar Split View](<Project Visuals/4.png>)

---

### 5. Detailed Transactional & Customer Audit Ledger
A granular audit table equipped with interactive slicers for `Date`, `Customer Name`, `Product Name`, and `Promotion Name`[cite: 25, 26, 27, 28].

* Displays line-item order details: `CustomerID`, `Date (dd/mm/yyyy)`, `Discount Percentage`, `Discount Value`, `Net Sales`, `Price Per Unit`, `OrderID`, `Product ID`, `Profit`, `PromotionID`, `Total Sales`, and `Units Sold`[cite: 28].

![Detailed Transaction Ledger](<Project Visuals/5.png>)

---

## 📐 Data Model & Star Schema Architecture

The analytical model is structured around a classic **Star Schema**, positioning a single transactional fact table at the center while establishing one-to-many relationships out to master dimension entities and dual calendar systems:

* **Central Fact Table (`Fact Table`):** Anchors the relational model and contains transactional sales records at the order-line granularity. It maintains foreign key references linking outward to dimension tables (`CustomerID`, `Product ID`, `PromotionID`, and `Date (dd/mm/yyyy)`). Key numeric metrics stored within this table include `Units Sold`, `Price Per Unit`, `Total Sales`, `Discount Percentage`, `Discount Value`, `Net Sales`, and an automated 10% operating margin metric `Profit`.
* **Customer Master Dimension (`Dim Customers`):** Relates to the fact table through a one-to-many relationship using `Customer ID`. It supplies descriptive customer profile attributes and geographic data, including `Customer Name`, `City`, `State`, `Pincode`, `EmailID`, and `Phone Number`.
* **Product Catalog Dimension (`Dim Product`):** Connects to the central fact table via `ProductID` to `Product ID` in a one-to-many relationship. It enriches each transaction with merchandise attributes including `Product Name`, `Product Line`, and the baseline catalog price `Price Per Unit (INR)`.
* **Campaign & Promotion Dimension (`Dim Promotion`):** Maps to the fact table across `PromotionID` in a one-to-many relationship. It captures marketing campaign details such as `Promotion Name`, `Ad Type`, `Coupon Code`, `Price Reduction Type`, and the assigned markdown rate `Percentage` (spanning 10% to 70%).
* **Primary Date Dimension (`Date Table 1`):** Serves as the active, default calendar dimension generated through `CALENDARAUTO()`. It maintains an active relationship with `Date (dd/mm/yyyy)` in the fact table to control chronological navigation, default slicer behavior, and standard time-series visual filtering.
* **Role-Playing Date Dimension (`Date Table 2`):** Operates as an independent, secondary calendar dimension built with `CALENDARAUTO()`. Instead of using an active relationship, it maintains an inactive link to `Date (dd/mm/yyyy)` that is invoked on demand within DAX expressions using `USERELATIONSHIP`. This configuration enables side-by-side horizon auditing between two independent date selections without filter conflicts.

---

## 🧮 Key DAX Measures

Measures reside in `Measures Table` and utilize `USERELATIONSHIP` to activate role-playing date logic across `Date Table 2` while overriding `Date Table 1` filter contexts:

```dax
// Evaluates Net Sales through the secondary date dimension
Sum of Net Sales = 
CALCULATE(
    SUM('Fact Table'[Net Sales]),
    ALL('Date Table 1'),
    USERELATIONSHIP('Date Table 2'[Date], 'Fact Table'[Date (dd/mm/yyyy)])
)

// Cumulative Profit calculated over secondary date context
Total Profit = 
CALCULATE(
    SUM('Fact Table'[Profit]),
    ALL('Date Table 1'),
    USERELATIONSHIP('Date Table 2'[Date], 'Fact Table'[Date (dd/mm/yyyy)])
)

// Unit volume sold across Date Table 2
Quantity Sold = 
CALCULATE(
    SUM('Fact Table'[Units Sold]),
    ALL('Date Table 1'),
    USERELATIONSHIP('Date Table 2'[Date], 'Fact Table'[Date (dd/mm/yyyy)])
)
```

---

## 🚀 How to View and Run

1. Clone or download this repository as a `.zip` archive and extract it locally.
2. Open the file in **Power BI Desktop**.
3. **Data Refresh Notice:** Ingestion paths are mapped to local file storage (`Store+Data.xlsx`)[cite: 25, 26, 27, 28]. To refresh live rows, adjust the source file path under **Power Query > Data Source Settings**[cite: 25, 26, 27, 28]. All visual configurations, custom color palettes, DAX logic, and table relationships can be fully inspected offline without an active refresh.
