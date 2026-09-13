# Loan Default Risk & Borrower Profile Analytics (Power BI)

An enterprise Power BI reporting solution designed to analyze consumer credit risk, monitor loan default probabilities, and dissect applicant demographics and underwriting profiles.

---

## 📌 Project Overview

This project provides end-to-end visibility into lending portfolio exposure, delinquency rates, and demographic distributions. It evaluates borrower risk profiles by analyzing credit scoring bins, debt-to-income (DTI) metrics, employment stability, and educational backgrounds across multi-year lending cycles.

* **Format:** Power BI Project (`.pbip`)
* **Storage Mode:** Import Mode
* **Data Pipeline & Architecture:**
  * **Data Source & Flow:** Data was sourced directly via a **Power BI Dataflow** running over an **On-Premises Data Gateway** connected to **Microsoft SQL Server**.
  * **ETL Pipeline:** The underlying table schema and entities (`Loan_default`) were extracted from Microsoft SQL Server, piped through the gateway into the cloud Dataflow, and ingested into Power BI Desktop via the Power Platform Dataflows connector (`PowerPlatform.Dataflows`).

---

## 📊 Dashboard Architecture & Visual Showcase

### 1. Loan Default & Overview
Focuses on macro lending volume, primary borrowing purposes, and default rate behaviors across time and employment classifications.

* **Loan Amount by Purpose:** Tracks exposure across categories (Home, Business, Education, Auto, Other).
* **Employment vs. Risk:** Contrasts average income across employment types against the corresponding default rate percentages.
* **Age Segment Allocation:** Evaluates average borrowed capital across defined age demographics.
* **Default Rate (%) by Year:** Multi-year trend line monitoring macro portfolio health.

![Loan Default & Overview](<Project Visuals/1.png>)

### 2. Applicant Demographics & Financial Profile
Drills into granular credit scoring tiers, dependent statuses, and marital attributes.

* **Median Loan Amount by Credit Category:** Evaluates underwriting exposure across High, Medium, Low, and Very Low credit tiers.
* **High-Credit Demographic Mix:** Donut chart evaluating loan distributions among prime credit borrowers segmented by age and marital status.
* **Obligation Analysis:** Column chart evaluating loan exposure among middle-aged borrowers based on mortgage and dependent status.
* **Volume by Education:** Loan distribution across degree classifications (Bachelor's, High School, Master's, PhD).

![Applicant Demographics & Financial Profile](<Project Visuals/2.png>)

### 3. Financial Risk Metrics
Advanced analytical tracking covering time intelligence, portfolio movements, and root-cause decomposition.

* **Year-over-Year (YoY) Change Metrics:** Tracks both total loan originations growth (%) and default rate movements (%) year-over-year.
* **Sankey Allocation:** Flow diagram mapping Year-to-Date (YTD) loan volume transitions across credit scoring tiers and marital profiles.
* **Decomposition Tree:** Root-cause visual breaking down total loan volume by income brackets and subsequent employment sectors.

![Financial Risk Metrics](<Project Visuals/3.png>)

---

## 📐 Data Model & Transformations

### Fact Table: `Loan_default`
Primary transactional records ingested from the Dataflow:
* **Borrower Attributes:** `LoanID`, `Age`, `Income`, `CreditScore`, `MonthsEmployed`, `NumCreditLines`, `DTIRatio`, `Education`, `EmploymentType`, `MaritalStatus`
* **Underwriting Data:** `LoanAmount`, `InterestRate`, `LoanTerm`, `LoanPurpose`, `HasMortgage`, `HasDependents`, `HasCoSigner`, `Default`
* **Calculated Categorical Bins:**
  * `Age Groups`: Categorizes borrowers into `Teen` (<=19), `Adults` (<=39), `Middle Age Adults` (<=59), and `Senior Citizens`.
  * `Credit Score Bins`: Segments ratings into `Very Low` (<=400), `Low` (<=450), `Medium` (<=650), and `High`.
  * `Income Bracket`: Bins salaries into `Low Income` (<30k), `Medium Income` (30k–60k), and `High Income` (>=60k).

---

## 🧮 Key DAX Measures

### Portfolio Risk & Default Rates
```dax
Default Rate By Employment Type = 
VAR totalrecords = COUNTROWS(ALL('Loan_default'))
VAR defaultcases = COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE()))
RETURN
CALCULATE(
    DIVIDE(defaultcases, totalrecords), 
    ALLEXCEPT('Loan_default', 'Loan_default'[EmploymentType])
) * 100

Defalut Rate By Year = 
VAR totalloans = CALCULATE(COUNTROWS('Loan_default'), ALLEXCEPT('Loan_default', 'Loan_default'[Year]))
VAR default = CALCULATE(COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE())), ALLEXCEPT('Loan_default', 'Loan_default'[Year]))
RETURN
DIVIDE(default, totalloans) * 100
```
### Time Intelligence & YoY Movements
```
YOY (Year On Year) Loan Amount Change = 
DIVIDE(
    CALCULATE(SUM('Loan_default'[LoanAmount]), 'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))) - 
    CALCULATE(SUM('Loan_default'[LoanAmount]), 'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])) - 1),
    CALCULATE(SUM('Loan_default'[LoanAmount]), 'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])) - 1),
    0
) * 100

YOY Default Loans Change = 
DIVIDE(
    CALCULATE(COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE())), 'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))) -
    CALCULATE(COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE())), 'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])) - 1),
    CALCULATE(COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE())), 'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])) - 1),
    0
) * 100
```
### Underwriting Aggregations
```
Average Loan By Age Group = 
AVERAGEX(VALUES('Loan_default'[Age Groups]), AVERAGE('Loan_default'[LoanAmount]))

Average Loan Amount (High Credit) = 
AVERAGEX(FILTER('Loan_default', 'Loan_default'[Credit Score Bins] = "High"), 'Loan_default'[LoanAmount])

YTD (Year To Date) Loan Amount = 
CALCULATE(
    SUM('Loan_default'[LoanAmount]), 
    DATESYTD('Loan_default'[Loan_Date_DD_MM_YYYY].[Date]), 
    ALLEXCEPT('Loan_default', 'Loan_default'[Credit Score Bins], 'Loan_default'[MaritalStatus])
)
```
---

## 🚀 How to Open and Run

1. Clone or download this repository as a `.zip` file and extract it.
2. Open Power BI Desktop (ensure **PBIP format preview** is enabled in *Options > Preview Features*).
3. Open `Warehouse_Analysis_Project.pbip`.
4. Data Refresh Notice: The semantic model queries a local production MySQL database. While full model definitions, DAX logic, and reports can be inspected locally, refreshing rows requires active credentials and a matching database host.
