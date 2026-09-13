# Enterprise Power BI Analytics Portfolio

A curated collection of production-grade Power BI business intelligence projects demonstrating end-to-end data modeling, ETL engineering, advanced DAX, and interactive UI/UX design.

---

## 📂 Repository Structure & Project Index

| Project Directory | Domain | Core Tech Stack | Data Architecture Highlights |
| :--- | :--- | :--- | :--- |
| **[Project 1: Housing Market Analytics](./Project-1-Housing-Market)** | Real Estate | Google BigQuery, PBIP, TMDL | Direct warehouse ingestion, macroeconomic tracking, spatial pricing analysis |
| **[Project 2: Warehouse Optimization](./Warehouse-Optimization)** | Supply Chain & Logistics | MySQL, MS SQL Server, PBIP | Daily demand vs. stock availability, inventory stockout valuation, margin shortfall modeling |
| **[Project 3: Loan Default Risk](./Loan-Default-Risk)** | Financial Services & Credit | Power BI Dataflow, On-Premises Data Gateway, MS SQL Server, PBIP | Credit scoring bins, demographic stratification, DTI ratio analysis, YoY default rate tracking|
| **[Project 4: Digital Payment Analytics](./Digital-Payment-Analytics)** | FinTech & Banking | Excel, PBIP, Power Query (M) | Multi-currency matrix across regional banking hubs, monthly velocity tracking, customer liquidity analysis |
| **[Project 5: Prism Insurance Analytics](./Prism-Insurance-Analytics)** | Insurance & Claims | MS SQL Server, Excel, Python (NLTK VADER), PBIX | Automated underwriting pipeline, claims lifecycle auditing, NLP text sentiment scoring |
| **[Project 6: Retail Store Analytics](./Retail-Store-Analytics)** | Retail & E-Commerce | Excel, PBIX, Power Query (M) | Star schema modeling, catalog Pareto analysis, role-playing dual-calendar comparison via `USERELATIONSHIP` |

---

## 🛠️ Technical Competencies Demonstrated

* **Data Modeling & Architecture:** Designing normalized star schemas, implementing role-playing dimensions, handling active/inactive relationships, and managing row-level surrogate keys.
* **Advanced DAX Formulation:** Utilizing time intelligence (`DATESYTD`, YoY shifts), dynamic filtering context (`CALCULATE`, `ALLEXCEPT`, `ALL`), mathematical division controls, and conditional table iterators (`SUMX`, `AVERAGEX`).
* **Enterprise ETL & Integration:** 
  * Connecting to cloud warehouses (Google BigQuery) and relational engines (MySQL, Microsoft SQL Server).
  * Managing Power Platform Dataflows over On-Premises Data Gateways.
  * Transforming nested datasets and running embedded Python scripts (`nltk.sentiment.vader`) directly inside Power Query.
* **Modern Fabric/Developer Workflows:** Structuring repositories with Power BI Project (`.pbip`) artifacts and Tabular Model Definition Language (`.tmdl`) files for Git versioning and collaborative code review.

---

## 🚀 Getting Started & Local Inspection

1. Click the green **`<> Code`** button at the top right of this page and select **Download ZIP** (or clone the repository using `git clone`).
2. Extract the archive onto your local workstation.
3. Navigate to any individual project subfolder to access its dedicated report file (`.pbix`) and comprehensive technical documentation.
4. **Offline Viewing Notice:** All report visuals, calculated measures, schema layouts, and data models can be inspected directly in Power BI Desktop without needing active credentials for external data sources (Google BigQuery, local SQL databases, or enterprise gateways).
