# Customer-Lifetime-Value-Churn-Risk-Analysis
Analyzed customer behavior to predict Customer Lifetime Value (CLV) and identify churn risk using Python, SQL, and Machine Learning. Created Power BI dashboards to visualize customer segmentation, churn trends, and actionable insights for customer retention and revenue optimization.
# Customer Lifetime Value & Churn Risk Analysis 
An end-to-end data analytics project that identifies high-risk customers and quantifies revenue at risk by combining customer demographics, subscription, and support data. The project covers SQL-based data extraction, feature engineering, churn prediction modeling, and an interactive Power BI dashboard for retention strategy.
---
## Project Overview Customer churn directly impacts recurring revenue, and not all churn carries equal weight — losing a high-CLTV customer costs far more than losing a low-value one. This project analyzes a relational customer database to: - Identify the key drivers behind customer churn - Predict churn risk using a classification model - Segment customers by combining **Customer Lifetime Value (CLTV)** and **churn probability** - Prioritize retention efforts toward high-value, high-risk customers - Present findings through an interactive Power BI dashboard 
--- 
## Dataset The data is stored in a normalized **SQLite database** with three related tables, joined on customerid: | Table | Description | |---|---| | db_customer | Customer demographics — name, country, state, gender, date of birth, interests | | db_subscription | Subscription details — plan type, contract type, monthly charges, renewal/cancellation dates, cancellation reason, CLTV, churn score | | db_support | Support interactions — complaint dates, escalations, CSAT scores, comments | --- ## Tech Stack - **SQL** — data extraction and table joins from the relational database - **Python (Pandas, NumPy)** — data cleaning and feature engineering - **Scikit-learn** — churn classification model - **Power BI** — interactive dashboard and visualization - **SQLite** — source database --- ## Project Workflow 1. **Data Extraction** — Queried and joined customer, subscription, and support tables via SQL 2. **Data Cleaning** — Handled missing values, standardized date formats, resolved inconsistent categorical entries 3. **Feature Engineering** — Derived customer tenure, complaint counts, escalation flags, and CLTV bands 4. **Exploratory Data Analysis** — Examined churn patterns across contract type, plan tier, and support activity 5. **Churn Prediction Model** — Built a classification model to estimate churn probability per customer 6. **Customer Segmentation** — Combined CLTV and churn risk into priority tiers for retention targeting 7. **Dashboard** — Visualized churn rate by segment, revenue at risk, and support-to-churn correlation in Power BI --- ## Key Insights *(Update with your actual results once analysis is complete)* - Identified **X%** of customers contributing **Y%** of at-risk revenue - Achieved **X%** accuracy / **X** AUC score in predicting churn - Flagged **X** high-CLTV customers with elevated churn risk for retention targeting - Found that **[contract type / support escalations / etc.]** was the strongest predictor of churn --- ## Repository Structure
├── data/
│   └── customer_churn.db          # Source SQLite database
├── sql/
│   └── queries.sql                # Table joins and extraction queries
├── notebooks/
│   └── churn_analysis.ipynb       # EDA, feature engineering, and modeling
├── dashboard/
│   └── churn_dashboard.pbix       # Power BI dashboard file
├── README.md
└── requirements.txt
--- ## How to Run 1. Clone the repository
bash
   git clone https://github.com/<your-username>/customer-churn-analysis.git
   cd customer-churn-analysis
2. Install dependencies
bash
   pip install -r requirements.txt
3. Run the analysis notebook
bash
   jupyter notebook notebooks/churn_analysis.ipynb
4. Open dashboard/churn_dashboard.pbix in Power BI Desktop to explore the interactive dashboard --- ## Future Improvements - Incorporate additional behavioral/usage data for stronger predictive signal - Test ensemble models (Random Forest, XGBoost) for improved accuracy - Automate the SQL-to-dashboard pipeline for live churn monitoring ---
