
# Real Estate Market & Sales Performance Analytics (Power BI)

An end-to-end Power BI reporting solution that tracks Danish housing market dynamics, pricing trends, and transaction performance across regions, property types, and economic indicators.

---

## 📌 Project Overview

This project analyzes historical residential property transactions to evaluate property valuations, sales velocity, and regional market distribution. It incorporates macroeconomic indicators—such as inflation, nominal interest rates, and bond yields—to assess their relationship with housing transactions and pricing behavior.

* **Format:** Power BI Project (`.pbip`)
* **Data Warehouse:** Google BigQuery (`project-756ca98a-b0d2-452c-b8b`)
* **Storage Mode:** Import Mode

---

## 📊 Dashboard Breakdown

### 1. House Market Overview
* **Key Metrics:** High-level monitoring of sales volume, units sold in the latest quarter, and trailing 12-month transaction values.
* **Regional Dynamics:** Tracks price momentum via **Median Sales Price Change** across regions (Jutland, Fyn & Islands, Zealand, Bornholm).
* **Offer vs. Purchase Analysis:** Scatter plot evaluating negotiation margins between listed offer prices and final transaction prices.
* **Sales Type Growth:** Year-over-Year (YoY) transaction growth segmented by sales category (Auction, Regular Sale, Other Sale, Family Sale).

### 2. Sales Performance & Key Influencers
* **Regional Sales Distribution:** Total aggregate transaction values per territory.
* **AI Key Influencers:** Machine learning visual pinpointing the primary property attributes (e.g., property age, square footage) driving upward or downward shifts in purchase prices.
* **Efficiency Ratios:** Offer-to-square-meter ratios broken down by transaction category.
* **Regional Unit Pricing:** Donut chart breakdown of average price per square meter (`sqm_price`).

### 3. Macroeconomic & Property Segment Analysis
* **Property Type Comparison:** Comparisons across Farm, Apartment, Townhouse, Villa, and Summerhouse segments.
* **Economic Drivers:** Tracks average annual inflation rate (`dk_ann_infl_rate%`), nominal interest rates, and mortgage credit bond yields against property valuations.
* **Pricing Density:** Combined bar-and-line analysis contrasting overall living space (`sqm`) against square-meter valuations across housing types.

---

## 📐 Data Model & Architecture

### Fact Table: `Housing`
Contains granular transaction records ingested from Google BigQuery:
* **Attributes:** `house_id`, `house_type`, `sales_type`, `year_build`, `no_rooms`, `sqm`, `sqm_price`
* **Location Hierarchy:** `region`, `area`, `city`, `zip_code`, `address`
* **Financials:** `purchase_price`, `Offer Price`, `%_change_between_offer_and_purchase`
* **Macroeconomics:** `nom_interest_rate%`, `dk_ann_infl_rate%`, `yield_on_mortgage_credit_bonds%`
* **Calculated Columns:** 
  * `Offer Price`: `(100 * Housing[purchase_price]) / (100 - Housing[%_change_between_offer_and_purchase])`
  * `Age`: `ABS(YEAR('Housing'[date].[Date]) - 'Housing'[year_build])

### Calendar & Time Intelligence
* **`LocalDateTable`:** Automatic calendar generated dynamically from `MIN('Housing'[date])` through `MAX('Housing'[date])`, structured into standard hierarchies (`Year`, `Quarter`, `Month`, `Day`).

---

## 🧮 Key DAX Measures

### Year-over-Year (YoY) Sales Growth
```dax
YOY_Sales_Growth = 
VAR CurrYearSales =
    CALCULATE(
        SUM('Housing'[purchase_price]),
        YEAR('Housing'[date]) = YEAR(MAX('Housing'[date]))
    )
VAR PrevYearSales = 
    CALCULATE(
        SUM('Housing'[purchase_price]),
        YEAR('Housing'[date]) = YEAR(MAX('Housing'[date])) - 1
    )
RETURN
    IF(PrevYearSales <> 0, (CurrYearSales - PrevYearSales) / PrevYearSales, BLANK())
```

### Median Sales Price Change
```dax
Median Sales Price Change = 
VAR CurrMedianPrice = 
    MEDIANX(
        FILTER('Housing', YEAR('Housing'[date].[Date]) = YEAR(MAX('Housing'[date].[Date]))),
        'Housing'[purchase_price]
    )
VAR PrevMedianPrice = 
    MEDIANX(
        FILTER('Housing', YEAR('Housing'[date].[Date]) = YEAR(MAX('Housing'[date].[Date])) - 1),
        'Housing'[purchase_price]
    )
RETURN
    IF(PrevMedianPrice <> 0, (CurrMedianPrice - PrevMedianPrice) / PrevMedianPrice, BLANK())
```

### Trailing 12-Month Sales
```dax
Last 12 Month Sales = 
CALCULATE(
    SUM('Housing'[purchase_price]),
    DATESINPERIOD('Housing'[date], MAX('Housing'[date]), -12, MONTH)
)
```

### Regional Aggregation & Unit Economics
```dax
// Sales isolated by region
Sales By Region = 
CALCULATE(SUM('Housing'[purchase_price]), ALLEXCEPT('Housing', 'Housing'[region]))

// Offer Price to SQM Ratio
Offer To SQM Ration = 
DIVIDE(SUM('Housing'[Offer Price]), SUM('Housing'[sqm]))

```
---

## 📊 Dashboard Previews
![House Market Overview](<Housing Project Visuals/Page 1.png>)
![Sales Performance](<Housing Project Visuals/Page 2.png>)
![Macroeconomic Analysis](<Housing Project Visuals/Page 3.png>)

---

## 🚀 How to Open and Run

1. Clone or download this repository to your local machine.
2. Open Power BI Desktop (ensure **PBIP format preview** is enabled in *Options > Preview Features*).
3. Open the `Housing Project.pbix` file.
