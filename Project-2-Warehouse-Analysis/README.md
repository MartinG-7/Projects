# Warehouse Supply & Demand Optimization Analytics (Power BI)

A Power BI analytical solution designed to evaluate warehouse inventory dynamics, calculate supply shortfalls, and measure the direct financial impact (realized profit vs. stockout-driven loss) across product portfolios.

---

## 📌 Project Overview

This reporting project evaluates business inventory health by pairing product-level daily demand against stock availability. The objective is to identify critical supply chain deficits, track inventory fulfillment consistency, and quantify uncaptured revenue versus profitable transactions.

* **Format:** Power BI Project (`.pbip`) via `Warehouse_Analysis_Project.Report`
* **Storage Mode:** Import Mode
* **Data Pipeline & Architecture:**
  * The core relational schema and table structure were initially created in **Microsoft SQL Server**.
  * The production ingestion was then layered onto **MySQL** (`PROD` database, querying `new_table`), preserving identical column schemas, data types, and transactional records.

---

## 🖼️ Dashboard Showcase

### 1. Demand & Availability Overview
Focuses on operational volume metrics, daily transaction velocity, and overall inventory coverage.

* **Average Demand per Day:** Tracks consumer pull volume across active operational days.
* **Average Availability per Day:** Monitors standard stock replenishment rates.
* **Total Supply Shortage:** Cumulative units where customer demand exceeded warehouse fulfillment capability.

![Operational KPIs](images/1.jpg)

### 2. Financial Impact & Business Optimization
Quantifies inventory performance in terms of profitability and lost revenue due to stockouts.

* **Total Profit:** Realized financial gains calculated from successful unit availability matching or exceeding demand.
* **Total Loss:** Financial valuation of unmet demand (stockouts multiplied by unit pricing).
* **Average Daily Loss:** Trailing day-over-day financial loss run-rate to assess operational friction.

![Financial KPIs](images/2.jpg)

---

## 📐 Data Model & Architecture

### Fact Table: `Demand/Availability Data`
Ingested from the production database schema:
* `Order_Date_DD_MM_YYYY` (Date hierarchy anchor)
* `Product_ID` (SKU identifier)
* `Product_Name` (Catalog title)
* `Availability` (Stock units on hand)
* `Demand` (Total incoming order quantity)
* `Unit_Price` (Sales price per product unit)
* **Calculated Column:**
  * `Loss/Profit`: Measures unit discrepancy directly:
    ```dax
    Loss/Profit = 'Demand/Availability Data'[Availability] - 'Demand/Availability Data'[Demand]
    ```

### Calendar & Time Intelligence
* **`LocalDateTable`:** Automatic calendar generated dynamically across the span of `MIN('Demand/Availability Data'[Order_Date_DD_MM_YYYY])` to `MAX('Demand/Availability Data'[Order_Date_DD_MM_YYYY])`, providing structured hierarchy levels (`Year`, `Quarter`, `Month`, `Day`).

---

## 🧮 Key DAX Measures

### Supply & Demand Aggregations
```dax
Total Number Of Days = 
DISTINCTCOUNT('Demand/Availability Data'[Order_Date_DD_MM_YYYY].[Date])

Total Demand = 
SUM('Demand/Availability Data'[Demand])

Total Availability = 
SUM('Demand/Availability Data'[Availability])

Average Demand Per Day = 
DIVIDE('Measures Table'[Total Demand], 'Measures Table'[Total Number Of Days])

Average Availability = 
DIVIDE('Measures Table'[Total Availability], 'Measures Table'[Total Number Of Days])

Total Supply Shortage = 
'Measures Table'[Total Demand] - 'Measures Table'[Total Availability]
