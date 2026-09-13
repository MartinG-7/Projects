# Digital Payment & Transaction Analytics (Power BI)

A Power BI analytical reporting solution built to evaluate monthly digital payment trajectories, monitor transactional liquidity and customer remaining balances, and dissect multi-currency financial activity across geographic hubs and banking partners.

---

## 📌 Project Overview

This dashboard provides detailed tracking of digital transaction volumes, velocity, and post-transaction account liquidity[cite: 20]. It features extensive slicing across banking endpoints, geographic clusters, demographic cohorts, device types, and payment mechanisms to uncover seasonal peaks, cash flow trends, and regional spending behavior.

* **Format:** Power BI Project (`.pbix`)
* **Storage Mode:** Import Mode
* **Data Source:** Excel Data Ingestion (`UPI+Transactions.xlsx`) processed via Power Query transformations

---

## 📊 Dashboard Views & Visual Breakdown

Both report pages integrate a comprehensive top filter pane enabling granular slicing across:
* **Banking Routes:** `BankNameSent`, `BankNameReceived`
* **Demographics & Location:** `City`, `Gender`, `Age Group`
* **Transaction Profiling:** `DeviceType`, `MerchantName`, `PaymentMethod`, `Purpose`, `TransactionType`

---

### 1. Monthly Transaction Volume Trends (Year 2024)
Focuses on chronological payment trends and volume velocity throughout the calendar year.

* **Interactive Switcher Buttons:** Provides immediate toggling between chart types and financial metrics:
  * `Line Chart Amount`
  * `Column Chart Amount`
  * `Line Chart Balance`
  * `Column Chart Balance`
* **Seasonal Spend Tracking:** Displays a high-contrast area/line visual tracking total transaction volume month-over-month (peaking at ~1,707K in May and showing subsequent recovery to 1,691K in October).

![Transactions By Month](<Project Visuals/1.png>)

---

### 2. Multi-Currency Regional Liquidity Matrix
A structured operational matrix mapping out transactional throughput and post-transaction remaining balances across major regional markets and currencies[cite: 20].

* **Regional Currencies Covered:**
  * **Burgas** (GBP)
  * **Plovdiv** (EUR)
  * **Sofia** (USD)
  * **Varna** (INR)
* **Liquidity Heatmap:** Evaluates monthly transacted `Amount` alongside end-of-period `RemainingBalance` to evaluate consumer liquidity and capital retention across distinct city centers.

![Regional Liquidity Matrix](<Project Visuals/2.png>)

---

## 📐 Data Model & Transformations

### Fact Table: `UPI Transactions`
Transformed via Power Query M steps to split combined timestamps into dedicated date and time objects, standardize string encodings, and convert account references:

* **Transaction Identity & Timing:** `TransactionID`, `TransactionDate`, `TransactionTime`
* **Financial Ledger:** `Amount`, `RemainingBalance`, `Currency`
* **Parties Involved:** `BankNameSent`, `BankNameReceived`, `CustomerAccountNumber`, `MerchantName`, `MerchantAccountNumber`
* **Demographic & Contextual Metadata:** `City`, `Gender`, `CustomerAge`, `DeviceType`, `PaymentMethod`, `PaymentMode`, `Purpose`, `TransactionType`, `Status`
* **Calculated Demographic Tiering:**
  ```dax
  Age Group = 
  IF('UPI Transactions'[CustomerAge] <= 25, "A1",
      IF('UPI Transactions'[CustomerAge] <= 35, "A2", "A3")
  )
  ```

### Calendar & Time Intelligence
* **`LocalDateTable`:** Automatic calendar generated dynamically across the span of `MIN('UPI Transactions'[TransactionDate])` to `MAX('UPI Transactions'[TransactionDate])`, providing structured hierarchy levels (`Year`, `Quarter`, `Month`, `Day`) with explicit chronological sorting.

---

## 🚀 How to View and Run

1. Clone or download this repository as a `.zip` archive and extract the project files locally.
2. Launch **Power BI Desktop** (verify that **Power BI Project (.pbip)** format is active under *File > Options and settings > Options > Preview features*).
3. Open `Payment_Interface_Analysis_Project.pbix`.
