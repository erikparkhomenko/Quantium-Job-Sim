# Quantium Virtual Internship — Retail Analytics

A two-part data analytics project completed as part of the [Quantium Virtual Internship](https://www.theforage.com/) on Forage. The analysis covers chip product transaction data for a major Australian retailer, progressing from exploratory data analysis through to experimentation and uplift testing.

---

## Project Structure

```
├── Task_1/
│   ├── Task_1.ipynb          # Cleaning & EDA notebook
│   └── Task_1.html           # Exported HTML report
├── Task_2/
│   ├── Task_2.ipynb          # Experimentation & uplift testing notebook
│   └── Task_2.html           # Exported HTML report
├── purchase_behaviour.csv    # Customer loyalty card data
└── transaction_data.xlsx     # Chip product transaction data
```

---

## Task 1 — Data Cleaning & Exploratory Data Analysis

### Overview

Cleaned and merged two datasets: transaction records and customer purchase behaviour. Then conducted exploratory analysis to surface insights about customer segments, brand preferences, and sales trends.

### Data Cleaning Highlights

- **Transaction data:** Extracted `WEIGHT` from product name strings; used a brand lookup dictionary (21 canonical brands) to separate `BRAND` from `FLAVOUR`; standardised flavour names using abbreviation dictionaries built with LLM assistance; converted date column from Excel serial integers to `datetime`; removed two outlier rows with `PROD_QTY = 200`.
- **Purchase data:** Converted `LIFESTAGE` and `PREMIUM_CUSTOMER` to categorical types; confirmed no missing values across either dataset.
- **Merged dataset:** 264,836 rows × 12 columns; joined on `LYLTY_CARD_NBR`.

### Key Findings

| Area | Finding |
|---|---|
| **Top segment by revenue** | Older Families (Budget) — over $168k in sales |
| **Basket size driver** | Life stage, not spending tier; Older Families Premium reach 2.00 units/txn |
| **Dominant brand** | Kettle (~20–23% share across all 21 segments) |

<img width="1790" height="755" alt="image" src="https://github.com/user-attachments/assets/33c4718d-0ac0-4d7c-9ad1-d80aafc10e07" />

| **Flavour landscape** | Highly fragmented; top 10 flavours account for only ~32–33% of sales |
| **Mainstream pricing** | Young & Midage Mainstream customers pay significantly more per unit ($4.05 vs $3.69, t = 40.83, p ≈ 0) |

<img width="1185" height="490" alt="image" src="https://github.com/user-attachments/assets/bd198fc6-901c-4000-991e-ffd4143dd5e4" />

| **Temporal spike** | Sharp January 2019 sales spike across all tiers — likely seasonal/promotional |

<img width="1589" height="590" alt="image" src="https://github.com/user-attachments/assets/58c59bfe-5a64-425c-86de-a03086591bfb" />

| **Missing date** | Christmas Day 2018 — likely store closure |
| **Young Singles/Couples Mainstream affinity** | Over-index on Tyrrells (1.24), Twisties (1.23), Kettle (1.20); prefer large sharing bags (270g, 380g) |

<img width="1186" height="590" alt="image" src="https://github.com/user-attachments/assets/d45ad18c-6c04-49fc-9ad6-f3d7d648dbce" />

### Libraries

`pandas`, `numpy`, `matplotlib`, `seaborn`, `scipy`, `re`, `thefuzz`

---

## Task 2 — Experimentation & Uplift Testing

### Overview

Evaluated a store trial conducted across stores 77, 86, and 88 (trial period: February–June 2019). Identified the best control store for each trial store using a composite similarity score, then tested whether trial period performance was statistically significantly different from the control.

### Methodology

1. **Aggregation** — Monthly store-level metrics: `MONTH_SALES`, `NUM_CUSTOMERS`, `TRANS_CUST`, `SALES_CUST`.
2. **Control store selection** — Composite score (weighted 50/50) of:
   - Pearson correlation across `MONTH_SALES` and `NUM_CUSTOMERS` in the pre-trial period
   - Standardised magnitude distance (inverted so 1 = most similar)
3. **Scaling** — Control store values scaled to the trial store's pre-trial mean ratio to ensure comparability.
4. **Significance testing** — t-test using pre-trial standard deviation of percentage differences as the null distribution; t-critical = 1.895 (df = 7, α = 0.05).

### Control Store Assignments

| Trial Store | Control Store | Composite Score |
|---|---|---|
| 77 | 233 | 0.9735 |
| 86 | 155 | 0.9549 |
| 88 | 237 | 0.8140 |

<img width="1584" height="492" alt="image" src="https://github.com/user-attachments/assets/e18d919c-c995-41c6-ad10-710bee6502b3" />


### Results Summary

**Store 77 (control: 233)** — Clear positive effect. Sales significant in 4 trial months; customer numbers significant in 3 months. Uplift driven by new customer acquisition.

**Store 88 (control: 237)** — Strong result comparable to Store 77. Sales significant in 4 months; customers in 3. Supports broader rollout.

**Store 86 (control: 155)** — Mixed result. Customer numbers significantly higher in most months, but sales significance limited to February and March. Possible promotional pricing dampening revenue impact — follow-up with Category Manager recommended.

### Libraries

`pandas`, `numpy`, `matplotlib`, `seaborn`, `scipy`

---

## Setup

```bash
# Clone the repo
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>

# Install dependencies
pip install pandas numpy matplotlib seaborn scipy thefuzz openpyxl
```

Run notebooks in order: `Task_1.ipynb` → `Task_2.ipynb`.

> **Note:** Task 2 imports the merged dataset directly from a Forage CDN URL, so no local file is required for that step.

---

## Notes

- Brand standardisation and flavour abbreviation dictionaries were built with LLM assistance (Claude Sonnet, DeepSeek-V3) and manually reviewed.
- The Data Wrangler extension (VS Code / Jupyter) was used for initial column-level profiling.
