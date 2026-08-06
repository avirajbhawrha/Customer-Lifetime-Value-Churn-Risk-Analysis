# Customer Churn Risk Analysis

An end-to-end churn analysis project built on customer, subscription, and support-ticket data stored in a SQLite database. The project covers SQL-based data extraction, data cleaning, feature engineering, KPI computation, and visualization using Python (Pandas, NumPy, Matplotlib, Seaborn).

## Objective

To identify **why and where customers are churning**, quantify the business impact (revenue at risk, retention rate), and flag customers by churn risk level using a rule-based score, so that the business can prioritize retention efforts.

## Tech Stack

| Tool / Library | Purpose |
|---|---|
| **SQLite3** | Source database for customer, subscription, and support data |
| **Pandas** | Data extraction (`read_sql`), cleaning, merging, aggregation |
| **NumPy** | Conditional logic (`np.where`, `np.select`) for feature creation |
| **Matplotlib** | Trend and comparison bar/line charts |
| **Seaborn** | Correlation heatmap, pairplot, categorical plot |

## Data Source

Data was pulled directly from a SQLite database (`customer_churn.db`) containing three tables:

| Table | Rows | Description |
|---|---|---|
| `db_customer` | 21 | Customer demographics — ID, name, country, state, gender, date of birth, interests, pincode |
| `db_subscription` | 21 | Subscription lifecycle — plan type, contract type, start/renewal/cancellation dates, monthly charges, CLTV, churn score |
| `db_support` | 9 | Support interactions — complaint date, escalation flag, CSAT score, comments |

Tables were queried using `sqlite_master` to list all tables, then `PRAGMA table_info()` to inspect each table's schema before loading into DataFrames.

## 1. Data Cleaning

### Customer Table
| Step | Action |
|---|---|
| Rename | `name` → `customer_name` |
| Drop columns | `interests` (only 4/21 non-null) and `pincode` (0/21 non-null) removed as unusable |
| Data type fix | `dob` converted from object → `datetime64` |
| Standardization | Gender values normalized — `Men` → `Male`, `Women` → `Female` |
| Missing values | `country` had 3 missing values; filled using a **state → country mapping** built from the rows that already had a country value |

### Subscription Table
- `subscription_start_date`, `renewal_date`, and `cancellation_date` converted from object → `datetime64`.

### Support Table
| Step | Action |
|---|---|
| Drop columns | `col_1` (fully null) and `comment` (mostly null/unstructured text) removed |
| Data type fix | `complaint_date` converted to `datetime64` |
| Duplicate handling | Customers with multiple complaints were consolidated: a `customer_count` column captured total complaints per customer, then the table was de-duplicated to keep only the **most recent complaint per customer** (9 rows → 7 unique customers) |

## 2. Feature Engineering

| Feature | Logic | Purpose |
|---|---|---|
| `churn_flag` | `1` if `cancellation_date` is present, else `0` | Binary churn label |
| **Merged dataset** | `subscription` LEFT JOIN `customer` LEFT JOIN `support` on `customerid` | Single analysis-ready table (21 rows × 21 columns), exported to `exported_churn_data.csv` |
| `Tenure_days` | If churned: `cancellation_date − subscription_start_date`; if active: `today − subscription_start_date` | Customer lifetime/duration in days |
| `churn_risk` | Bucketed from `churn_score`: `< 50` → **low**, `50–69` → **med**, `≥ 70` → **high** | Simple rule-based risk segmentation |

## 3. Key Business Metrics (KPIs)

| Metric | Value |
|---|---|
| Churn Rate | **28.57%** |
| Retention Rate | **71.43%** |
| Average Revenue Per User (ARPU) | **18.85** |
| Average Customer Tenure | **1,511 days** |
| Revenue at Risk (from churned users) | **73.94** |
| Escalation Rate | **19.05%** |
| Avg. Complaints per User | **0.43** |
| Correlation: Escalations vs. Churn | **0.47** (moderate positive) |

### Churn Rate by Plan Type
| Plan Type | Churn Rate |
|---|---|
| Basic | 60.00% |
| Standard | 22.22% |
| Premium | 14.29% |

### Churn Rate by Acquisition (Subscription) Type
| Subscription Type | Churn Rate | Total Revenue | Users |
|---|---|---|---|
| Referral | 83.33% | 74.94 | 6 |
| Paid | 16.67% | 174.94 | 6 |
| Organic | 0.00% | 145.91 | 9 |

### Churn Rate by State (sample)
| State | Churn Rate | Total Revenue | Users |
|---|---|---|---|
| Karnataka | 100.00% | 20.98 | 2 |
| Meghalaya | 66.67% | 42.97 | 3 |
| Telangana | 50.00% | 30.98 | 2 |
| Delhi | 25.00% | 52.96 | 4 |
| Maharashtra, Nagaland, Rajasthan, Uttar Pradesh, Kathmandu | 0.00% | — | — |

## 4. Data Visualization

**Matplotlib**
- Monthly Churn Trend — line chart of churned customers over time (time-series KPI)
- Churn Rate by Plan Type — bar chart
- Churn Rate by State — bar chart
- Churn Rate by Monthly Charges — bar chart

**Seaborn**
- Categorical features (`plan_type`, `contract_type`, `churn_risk`) were ordinally encoded and `escalations` mapped to 0/1 to enable correlation analysis
- **Heatmap** — correlation matrix across `plan_type`, `contract_type`, `churn_score`, `churn_flag`, `churn_risk`, `escalations`
- **Pairplot** — pairwise relationships across the encoded features
- **Catplot** — `monthly_charges` by `plan_type`, split by `gender`, faceted by `churn_risk`

## 5. Pivot Table Summary

A pivot on `plan_type` summarizing:
- `monthly_charges` → sum
- `customerid` → unique count
- `churn_flag` → mean (i.e., churn rate)

## 6. Bonus: SQL-in-Python Practice

A separate practice section demonstrates core SQL operations executed from Pandas against a small toy SQLite database (`test_database.sqlite`):
- `CREATE TABLE` for a `users` table (`first_name`, `country`, `budget`)
- `INSERT INTO` with multiple rows
- `SELECT * FROM users` via `pd.read_sql`
- `GROUP BY` aggregation (`SUM(budget)` per country)

## Key Insights

- **Basic plan users churn the most (60%)** — far higher than Standard (22%) or Premium (14%), suggesting entry-tier customers are the highest priority for retention efforts.
- **Referral-acquired customers churn at 83%**, dramatically higher than Organic (0%) or Paid (17%) — referral sign-ups may not be as committed or well-matched to the product.
- **Escalations are moderately correlated with churn (0.47)**, meaning customers who escalate a complaint are meaningfully more likely to leave — an early-warning signal for retention teams.
- Roughly **29% of the customer base has churned**, putting **~74 (revenue units) at risk**, concentrated in specific states (Karnataka, Meghalaya, Telangana) and the Basic plan tier.

## Project Files

| File | Description |
|---|---|
| `Churn_Risk_Analysis_.ipynb` | Main analysis notebook (this project) |
| `customer_churn.db` | Source SQLite database (customer, subscription, support tables) |
| `exported_churn_data.csv` | Cleaned, merged, analysis-ready dataset exported from the notebook |

## How to Run

1. Place `customer_churn.db` in the same directory as the notebook.
2. Install dependencies: `pip install pandas numpy matplotlib seaborn`
3. Run all cells in `Churn_Risk_Analysis_.ipynb` sequentially — data loads from SQLite, gets cleaned, merged, feature-engineered, and visualized in order.
