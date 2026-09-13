# Insurance Operations, Claims & Customer Sentiment Analytics (Power BI)

An end-to-end Power BI business intelligence and text-analytics solution for **Prism Insurance Pvt. Ltd.**, providing tracking across policy underwriting, claim settlements, and NLP-driven customer sentiment analysis.

---

## 📌 Project Overview

This multi-page analytical dashboard connects operational policy lifecycles with customer satisfaction metrics. It monitors macro underwriting health (total coverage vs. premium volume), claim settlement velocity, policy lapse/retention rates, and processes qualitative customer feedback using integrated Python NLP text analysis.

* **Format:** Power BI Project (`.pbix`)
* **Storage Mode:** Import Mode
* **Data Sources & Pipeline:**
  * **Policy & Claims DB:** Ingested from **Microsoft SQL Server** (`InsuranceDB.dbo.InsuranceData`).
  * **Customer Feedback:** Ingested from Excel (`Insurance+Customer+Feedback.xlsx`), then transformed via embedded **Python M scripting (`nltk.sentiment.vader`)** to compute automated compound sentiment scores.

---

## 📊 Dashboard Architecture & Views

### 1. Executive Operations & Claims Overview
Provides high-level KPI tracking, demographic volume splits, and claim settlement pipelines.

* **Executive Cards:** Displays global totals for Premium Amount (~5.97M), Total Underwritten Coverage (~600.33M), and Settled/Pending Claim Amounts (~16.90M).
* **Demographic Underwriting:** Gender volume breakdown (5K Female / 5K Male) and Premium Amount distribution segmented by product category (`Travel`, `Health`, `Auto`, `Life`, `Home`).
* **Portfolio Retention:** Donut chart evaluating `Active` (58.11%) vs. `Inactive` (41.89%) policies based on expiration thresholds.
* **Claims Pipeline:** Dissects claim volume and values by `ClaimStatus` (`Rejected`, `Settled`, `Pending`) across age demographics (`Adult`, `Elder`, `Young Adult`).

![Executive Operations Overview](<Project Visuals/1.png>)

---

### 2. Policy & Underwriting Ledger
A granular audit table allowing claims officers to review individual policy records, timeline milestones, and premium-to-coverage valuations across accounts.

* **Granular Audit Columns:** Tracks `PolicyNumber`, `CustomerID`, `ClaimNumber`, customer `Age`, `Gender`, `CoverageAmount`, and `PremiumAmount`.
* **Lifecycle Dual-Hierarchies:** Cross-examines chronological timelines between `PolicyStartDate` and `PolicyEndDate` with full year/quarter/month/day drill-downs.

![Underwriting Ledger](<Project Visuals/2.png>)

---

### 3. Customer Sentiment & Feedback NLP Analysis
Leverages natural language processing to extract customer perception, surface pain points, and classify feedback tiers.

* **Word Cloud Custom Visual:** Visually highlights recurring phrases in customer feedback (prominently featuring terms like *service*, *claims*, *policy*, *premiums*, *process*, and *support*).
* **Sentiment Scoring Matrix:** Individual feedback logs paired with computed compound sentiment scores ranging from 0.00 to 1.00.
* **Satisfaction Tiering:** Evaluates sentiment distribution across defined customer satisfaction cohorts:
  * **Good** (Score 0.60 – 0.79)
  * **Excellent** (Score ≥ 0.80)
  * **Needs Improvement** (Score < 0.60)

![Customer Sentiment Analysis](<Project Visuals/3.png>)

---

## 📐 Data Engineering & Transformations

### SQL Data Pipeline: `InsuranceData`
Ingested from SQL Server with automated M conditional logic:
* **Demographic Bins:**
  ```m
  Age Group = if [Age] <= 24 then "Young Adult" 
              else if [Age] <= 60 then "Adult" 
              else "Elder"
  ```[cite: 23]
* **Policy Status Logic:**
  ```m
  Active/Inactive = if [PolicyEndDate] <= #date(2024, 12, 10) then "Inactive" else "Active"
  ```[cite: 23]
* Duplicate rows are dynamically pruned via `Table.Distinct` to ensure clean reporting.

### Python NLP Integration: `Sheet1` (Feedback)
Automated sentiment extraction applied via Power Query's Python connector using NLTK's VADER (Valence Aware Dictionary and sEntiment Reasoner):

```python
import nltk
import pandas as pd
from nltk.sentiment.vader import SentimentIntensityAnalyzer

try:
    sia = SentimentIntensityAnalyzer()
except LookupError:
    nltk.download('vader_lexicon')
    sia = SentimentIntensityAnalyzer()

# Normalize compound polarity score between 0.0 and 1.0
dataset['Score sentiment'] = dataset['Feedback'].apply(
    lambda text: round((sia.polarity_scores(str(text))['compound'] + 1) / 2, 9)
)
```

---

## 🚀 How to View and Run

1. Clone or download this repository as a `.zip` archive and extract the project files locally.
2. Open the file in **Power BI Desktop**.
