# E-commerce Customer Segmentation & Revenue Risk Scoring

**Tools:** Python · Pandas · Matplotlib · Seaborn  
**Dataset:** UCI Online Retail II — 1.06M transactions · Dec 2009 to Dec 2011  
**Source:** [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/502/online+retail+ii)

---

## Business Problem

In e-commerce, not all customers are equal. A small group of high-value buyers typically drives the majority of revenue — but businesses often treat all customers the same way. This project answers three questions:

1. What does the revenue concentration across customers actually look like?
2. Which customers are slipping away — and how much revenue is at risk?
3. How can we segment customers into actionable groups without a machine learning model?

---

## Key Findings

| Metric | Value |
|---|---|
| Total revenue (2 years) | £17,743,429 |
| Unique customers analysed | 5,878 |
| UK revenue share | 83.0% |
| Repeat buyer rate | 72.4% |
| Top 20% of customers → revenue | 77.2% |
| **Revenue at risk (churn-flagged customers)** | **£2,340,625 (13.2%)** |

---

## Approach

### Notebook 1 — Data Cleaning
Combined two raw Excel sheets (1,067,371 rows) into a single clean dataset. Documented every removal decision with business justification:

- Separated 19,494 cancellation invoices (Invoice starting with "C") — saved separately for return rate analysis
- Dropped 243,007 rows with no Customer ID — anonymous transactions cannot be used for customer-level analysis
- Removed rows with zero or negative Quantity/Price (data entry errors)
- Engineered `TotalRevenue`, `YearMonth`, `DayOfWeek` columns
- **75.5% of raw rows retained** after cleaning

### Notebook 2 — Exploratory Data Analysis
Explored the revenue shape of the business before building any model. Five business questions answered:

- Revenue grew steadily over 2 years with a strong Nov 2011 seasonal peak
- UK accounts for 83% of revenue — the business is heavily concentrated in one market
- Top product (REGENCY CAKESTAND 3 TIER) contributed £286,486 alone
- **Top 20% of customers drive 77.2% of revenue** — classic Pareto concentration
- 72.4% of customers are repeat buyers, but one-time buyers are a meaningful retention opportunity

### Notebook 3 — RFM Segmentation & Revenue Risk Scoring
Built a full RFM model in pandas — no ML library used, fully explainable logic.

**RFM scoring:**
- Each customer scored 1–4 on Recency, Frequency, and Monetary using quartile splits (`pd.qcut`)
- Scores combined into a 3-digit RFM string (e.g. "444" = best possible customer)
- Mapped to 6 named business segments using explicit rules

**Segments:**

| Segment | Customers | Revenue |
|---|---|---|
| Champions | 1,814 | £13,463,547 |
| Need Attention | 1,104 | £2,180,761 |
| Lost | 1,595 | £721,832 |
| Loyal | 803 | £791,829 |
| At Risk | 226 | £454,415 |
| Promising | 336 | £131,045 |

**Churn risk scoring (rule-based):**  
Customers flagged High Risk if: `recency ≥ 90 days` AND `F_score ≥ 3` (previously high-frequency buyers who have gone quiet).

> **£2,340,625 in revenue is at risk from 927 customers who were previously high-frequency buyers but have not purchased in 90+ days.**

---

## Project Structure

```
online-retail-rfm/
├── data/
│   ├── online_retail_II.xlsx      ← raw source (UCI)
│   └── clean_retail.csv           ← cleaned output from notebook 1
├── notebooks/
│   ├── 01_cleaning.ipynb          ← data cleaning & preparation
│   ├── 02_eda.ipynb               ← exploratory data analysis
│   └── 03_rfm_scoring.ipynb       ← RFM segmentation & risk scoring
├── outputs/
│   ├── 01_cleaning_summary.png
│   ├── 02_monthly_revenue.png
│   ├── 02_revenue_by_country.png
│   ├── 02_top_products.png
│   ├── 02_pareto_curve.png
│   ├── 02_repeat_vs_onetime.png
│   ├── 03_segment_distribution.png
│   ├── 03_rfm_heatmap.png
│   ├── 03_churn_risk.png
│   └── rfm_segments.csv           ← final scored customer table
└── README.md
```

---

## How to Run

```bash
# Install dependencies
pip install pandas matplotlib seaborn openpyxl

# Run notebooks in order
jupyter notebook notebooks/01_cleaning.ipynb
jupyter notebook notebooks/02_eda.ipynb
jupyter notebook notebooks/03_rfm_scoring.ipynb
```

---

## Skills Demonstrated

- **Data cleaning** — real-world messy data, documented decisions, transparent impact tracking
- **Exploratory analysis** — business-question-led EDA, Pareto analysis, time series trends
- **RFM modelling** — quartile-based scoring built from scratch in pandas, no ML libraries
- **Business thinking** — translating data findings into revenue impact (£2.34M at risk)
- **Data storytelling** — charts designed to answer specific business questions, not generic plots
