# Chip Sales Uplift Analysis

An end-to-end data analytics project analysing chip sales transactions to understand customer purchasing behaviour and evaluate the impact of a retail store trial.

---

## Project Structure

```
chips-sales-uplift-analysis/
│
├── EDA_annotated.ipynb         # Task 1: Exploratory Data Analysis
├── Quantium_Task2.ipynb        # Task 2: Experimentation & Uplift Testing
├── QVI_transaction_data.xlsx   # Raw transaction data (not included — see Data section)
├── QVI_purchase_behaviour.csv  # Customer loyalty profile data (not included)
├── QVI_data.csv                # Merged dataset output from Task 1 (not included)
├── images/                     # Screenshots used in this README
├── .gitignore
└── README.md
```

---

## Task 1 — Exploratory Data Analysis

### Overview
An end-to-end EDA on chip sales transaction data, aimed at understanding customer purchasing behaviour across different lifestages and customer segments to inform marketing and product strategy.

### Analysis Steps
1. **Data loading & initial inspection** — load transactions (Excel) and customer profiles (CSV), check shape and column types
2. **Data cleaning** — fix Excel serial date format, remove salsa products (non-chip items)
3. **Outlier detection & removal** — flag high-quantity transactions, identify and remove a wholesale/non-retail loyalty card (226000)
4. **Time-series analysis** — plot daily transaction counts, identify zero-transaction days (Christmas Day closure)
5. **Feature engineering** — extract `PACK_SIZE` from product name using regex; derive `BRAND` from first word; standardise brand name inconsistencies
6. **Customer dataset exploration** — inspect `LIFESTAGE` and `PREMIUM_CUSTOMER` distributions
7. **Dataset merging** — join transactions to customer profiles on loyalty card number; verify no unmatched records
8. **Segment-level analysis** — total sales, customer counts, and average units purchased by lifestage × premium tier
9. **Statistical price testing** — Welch's t-tests to compare price-per-unit across segments
10. **Target segment deep-dive** — brand and pack size affinity (lift analysis) for Mainstream Young Singles/Couples

---

### Transactions Over Time

<img width="1161" height="569" alt="image" src="https://github.com/user-attachments/assets/cccacaae-b19f-4f6d-8d18-4bd0a426b57c" />

---

### Total Chip Sales by Segment


<img width="1160" height="568" alt="image" src="https://github.com/user-attachments/assets/61650a82-946b-427f-8ff4-9f6df06d34fd" />

---

### Average Units per Customer by Segment

<img width="1163" height="576" alt="image" src="https://github.com/user-attachments/assets/8c9cccfd-692a-48ec-a08f-8228d657eece" />

---

### Brand Affinity — Mainstream Young Singles/Couples

<img width="974" height="486" alt="image" src="https://github.com/user-attachments/assets/f820141b-54c1-498b-91c3-378c403f300a" />


---

### Pack Size Affinity — Mainstream Young Singles/Couples


<img width="980" height="484" alt="image" src="https://github.com/user-attachments/assets/1ab09ea5-c787-4692-9e9b-97024056e94b" />

---

### Key Findings
- **Mainstream Young Singles/Couples** emerged as the highest-revenue segment for Young lifestages
- This segment over-indexes on premium brands (Tyrrells, Twisties, Doritos) with lift values above 1.20
- They strongly prefer **larger pack sizes** (270g, 380g, 330g), suggesting bulk or sharing purchase behaviour
- Mainstream customers pay significantly more per unit than Premium customers (t-test p < 0.001)
- Christmas Day (25 Dec 2018) had zero transactions across all stores

---

## Task 2 — Experimentation & Uplift Testing

### Overview
Evaluate whether a retail chip category trial (run across 3 stores, March–May 2019) caused a statistically significant uplift in sales and customer numbers, using matched control stores as the counterfactual.

### Methodology

**Control Store Selection** — for each trial store, every other store is scored on two dimensions using pre-trial data (Jul 2018 – Jan 2019):

| Dimension | Method |
|-----------|--------|
| Trend similarity | Pearson correlation of monthly totals |
| Level similarity | Standardised magnitude distance (0–1 scale) |

Scores are averaged equally across total sales and number of customers, and the highest-scoring store is selected as the control.

**Trial Assessment**
1. Scale the control store's metrics to match the trial store's pre-trial baseline
2. Compute monthly percentage differences between scaled control and trial
3. Derive standard deviation from the pre-trial period (8 months → 7 degrees of freedom)
4. Calculate t-statistics and compare against the 95th percentile of the t-distribution
5. Visualise results with 5%–95% confidence bands

### Control Stores Selected

| Trial Store | Control Store |
|-------------|---------------|
| 77 | 233 |
| 86 | 155 |
| 88 | 237 |

---

### Trial Store 77 — Sales & Customers vs Control

<img width="987" height="487" alt="image" src="https://github.com/user-attachments/assets/ede6beac-1f8d-422b-89ba-d0840581c72e" />


---

### Trial Store 86 — Sales & Customers vs Control

<img width="983" height="485" alt="image" src="https://github.com/user-attachments/assets/fbcad03e-e686-4afa-87c9-20e4946fcc41" />


---

### Trial Store 88 — Sales & Customers vs Control

<img width="980" height="486" alt="image" src="https://github.com/user-attachments/assets/51debdd3-d4fe-40f7-b759-68b7395794b7" />


---

### Results

| Trial Store | Sales Uplift Significant? | Customer Uplift Significant? |
|-------------|--------------------------|------------------------------|
| 77 | ✅ Yes (2/3 months) | ✅ Yes (2/3 months) |
| 86 | ❌ No | ✅ Yes (3/3 months) |
| 88 | ✅ Yes (2/3 months) | ✅ Yes (2/3 months) |

> **Store 86 note:** Customer numbers increased significantly but sales revenue did not. This may indicate promotional pricing during the trial — worth investigating with the Category Manager.

---

## Getting Started

### Prerequisites

```bash
pip install pandas numpy matplotlib scipy openpyxl
```

### Running the Notebooks

1. Clone the repository:
   ```bash
   git clone https://github.com/<your-username>/chips-sales-uplift-analysis.git
   cd chips-sales-uplift-analysis
   ```

2. Place the raw data files in the project root (see **Data** section below)

3. Run Task 1 first — it outputs `QVI_data.csv` which is used by Task 2:
   ```bash
   jupyter notebook EDA_annotated.ipynb
   ```

4. Then run Task 2:
   ```bash
   jupyter notebook Quantium_Task2.ipynb
   ```

---

## Data

 The expected files are:

| File | Description |
|------|-------------|
| `QVI_transaction_data.xlsx` | Individual chip purchase transactions (264,836 rows) |
| `QVI_purchase_behaviour.csv` | Customer loyalty card profiles — lifestage and premium tier (72,637 rows) |

**Transaction data columns:**

| Column | Description |
|--------|-------------|
| `DATE` | Transaction date (Excel serial format) |
| `STORE_NBR` | Store identifier |
| `LYLTY_CARD_NBR` | Loyalty card number (customer ID) |
| `TXN_ID` | Transaction identifier |
| `PROD_NBR` | Product number |
| `PROD_NAME` | Product name (used to extract brand and pack size) |
| `PROD_QTY` | Quantity of chips purchased |
| `TOT_SALES` | Total sales value |
