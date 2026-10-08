# loan-default-risk-analysis.zip
Bank Loan Default Risk Analysis

SQL (MySQL) | Python (Pandas, scikit-learn) | Power BI

An end-to-end analysis of 38,577 LendingClub loans (2007-2011) to find out which borrower and loan characteristics predict default, so a lender can price and approve loans more carefully.

<img width="428" height="240" alt="image" src="https://github.com/user-attachments/assets/ea41fe2a-c7a9-49f7-a4b8-9882c84588d7" />


Business problem

When a borrower stops repaying (a "charged-off" loan), the lender loses money. Roughly 1 in 7 loans in this dataset defaulted. The goal: find the segments that carry the most risk and show them in a dashboard a credit-risk team could use.

Key findings
Insight	Result
Overall default rate	14.6% (5,627 of 38,577 loans)
Loan grade	Default rate climbs from 6.0% (Grade A) to 33.8% (Grade G), about 5.6x
Loan purpose	Small business loans default at 27.1%, versus about 10% for major purchases, weddings and cars
Income	Low-income borrowers (<40k) default at 18.0% versus 11.2% for high-income (>80k)
Debt-to-income (DTI)	Default rate rises from 12.6% (DTI 0-10) to 16.7% (DTI 20-30)
Loan term	60-month loans default at 25.3% versus 11.1% for 36-month loans
Strongest numeric signal	Interest rate (correlation with default: 0.21)

Portfolio: $426.2M in total loan amount, 11.9% average interest rate.

Dashboard

Interactive Power BI dashboard 
 with a grade slicer, KPI cards (Total Loans, Total Amount, Default Rate %, Avg Interest Rate) and risk views by purpose, grade, income bracket and DTI bracket. A PDF export is in dashboard/.

Techniques used: DAX measures (Default Rate Pct = AVERAGE(default) * 100), calculated columns for income and DTI brackets, conditional formatting for risk levels.

Tech stack
Python: Pandas for cleaning and analysis, Matplotlib and Seaborn for charts, scikit-learn for the model
MySQL: table design, import, and GROUP BY / CASE WHEN analysis queries
Power BI: data modelling, DAX, dashboard design
Project workflow
Clean (Python): kept 17 relevant columns, kept only loans with a known outcome (Fully Paid / Charged Off), converted percent text to numbers, handled missing values, created the binary default target.
Query (MySQL): default rates by grade, purpose, income bracket, DTI bracket and term (sql/).
Explore and model (Python): correlation analysis and a logistic regression to predict default (python/loan_analysis.py).
Visualise (Power BI): interactive dashboard for risk segmentation.
Predictive model

A logistic regression predicts default from loan amount, interest rate, income, DTI, grade, open accounts and revolving utilisation.

Model	Accuracy	Recall	Precision
Baseline	85.8%	0.1%	20.0%
Class-weighted	61.3%	63.8%	21.2%

Why accuracy is misleading here: only about 15% of loans default, so a model that predicts "no default" for almost everyone scores 85.8% accuracy while catching almost no defaulters. I switched to class_weight='balanced' and prioritised recall, because missing a defaulter costs far more than a false alarm. The trade-off is low precision (about 1 in 5 flagged loans actually defaults), so this is a screening baseline, not a production model.

Repository structure
loan-default-risk-analysis/
├── README.md
├── requirements.txt
├── data/
│   └── loan_cleaned.csv
├── sql/
│   ├── 01_create_table.sql
│   └── 02_analysis_queries.sql
├── python/
│   └── loan_analysis.py
├── notebooks/
│   └── loan_default_analysis.ipynb
├── dashboard/
│   ├── loan_risk_dashboard.pbix
│   └── loan_risk_dashboard.pdf
├── images/
└── docs/
    └── data_dictionary.md
How to run
bash
git clone https://github.com/<your-username>/loan-default-risk-analysis.git
cd loan-default-risk-analysis
pip install -r requirements.txt
python python/loan_analysis.py

For SQL: run sql/01_create_table.sql in MySQL Workbench, import data/loan_cleaned.csv with the Table Data Import Wizard, then run sql/02_analysis_queries.sql. To view the dashboard, open the .pbix file in Power BI Desktop.

Limitations
Grade is partly circular. LendingClub assigns grade (and interest rate) using its own risk model, so those two variables reflect risk the lender already measured.
Correlation is not causation. The findings describe patterns, not proven causes.
Narrow time window. The data covers 2007-2011, including the financial crisis, so patterns may not hold today.
Simple model. Logistic regression is a baseline; precision is low.
Missing values. 50 rows have no revol_util; they are excluded from the model and were skipped in the MySQL import (38,527 rows there versus 38,577 in the CSV).
Public, anonymised data used for practice, not data from a real company.
Next steps
Try tree-based models (Random Forest, XGBoost) and tune the decision threshold
Add a precision-recall curve and a time-based train/test split
Add loan term and employment length to the dashboard
Data source

LendingClub loan data (2007-2011), publicly available on Kaggle.

Author

Ashitosh Sahare | www.linkedin.com/in/
ashitosh-sahare-5238a0315
 | ashitoshsahare.com
