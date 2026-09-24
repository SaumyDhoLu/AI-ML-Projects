# Lending Club Loan Risk Analysis

## Project Overview

This project performs an exploratory data analysis (EDA) of Lending Club loan data to identify patterns and borrower characteristics associated with loan charge-offs.

The analysis focuses on understanding how variables such as:

- Loan purpose
- Loan amount
- Debt-to-income ratio
- Annual income
- Interest rate
- Home ownership
- Verification status
- Borrower location

relate to loan outcomes.

The primary business objective is to identify segments that show different historical charge-off rates and provide data-driven observations that could support credit-risk analysis.

This is an **EDA and business analytics project**. It does not build a machine learning model or produce individual borrower default predictions.

---

## Business Problem

Lending institutions need to understand the characteristics associated with borrowers who fail to repay their loans.

The objective of this analysis is to answer questions such as:

- Which loan purposes have higher historical charge-off rates?
- Does loan size relate to charge-off rates?
- How does debt-to-income ratio vary across loan outcomes?
- Is annual income associated with loan performance?
- Does interest rate correspond to different charge-off rates?
- Does home ownership show differences in historical charge-off rates?
- Does verification status provide useful risk segmentation?
- Are there strong correlations among the financial variables?

The analysis uses historical loan outcomes to identify patterns rather than predicting the outcome of a new borrower.

---

## Dataset

The project uses the Lending Club dataset:

```
loan.csv
```

### Original Dataset

| Property | Value |
|---|---|
| Rows | 39,717 |
| Columns | 111 |

The dataset contains borrower, loan, credit, repayment, and loan-status information.

Examples of important variables include:

- `loan_amnt`
- `funded_amnt`
- `funded_amnt_inv`
- `term`
- `int_rate`
- `installment`
- `grade`
- `emp_length`
- `home_ownership`
- `annual_inc`
- `verification_status`
- `loan_status`
- `purpose`
- `addr_state`
- `dti`
- `delinq_2yrs`
- `inq_last_6mths`
- `revol_bal`
- `revol_util`
- `total_acc`
- `total_pymnt`
- `recoveries`

---

## Data Cleaning

The notebook follows a structured data-cleaning workflow before performing the analysis.

### 1. Missing-Value Analysis

Columns with more than 30% missing values are removed.

This removes 58 columns from the original dataset.

```python
loan = loan.dropna(
    axis=1,
    thresh=len(loan) * 0.70
)
```

After this step:

- 39,717 rows
- 53 columns

remain.

### 2. Removing Low-Information Columns

Additional columns are removed when they provide little value for the planned EDA.

These include:

- `pymnt_plan`
- `application_type`
- `policy_code`
- `initial_list_status`
- `tax_liens`
- `chargeoff_within_12_mths`
- `collections_12_mths_ex_med`
- `delinq_amnt`
- `acc_now_delinq`
- `url`
- `emp_title`
- `sub_grade`

The resulting dataset contains:

- 39,717 rows
- 41 columns

### 3. Data Type Standardization

Several variables are converted into appropriate numerical or date formats.

**Interest Rate**

The `%` symbol is removed and the column is converted to numeric:

```
"10.65%" → 10.65
```

**Revolving Utilization**

The `%` symbol is removed from `revol_util`.

**Loan Term**

The text "months" is removed:

```
"36 months" → 36
```

**Date Columns**

The following fields are converted to datetime:

- `issue_d`
- `earliest_cr_line`
- `last_pymnt_d`
- `last_credit_pull_d`

**Employment Length**

Employment duration is converted from categorical strings to numeric years.

Example:

```
< 1 year  → 0
1 year    → 1
2 years   → 2
...
10+ years → 10
```

### Handling Remaining Missing Values

After column-level cleaning, records with missing values in:

- `emp_length`
- `pub_rec_bankruptcies`

are removed.

The record count changes from:

```
39,717
```

to:

```
37,945
```

This represents approximately 4.46% of the records.

### Loan Status Filtering

The original loan-status distribution contains:

| Loan Status | Count |
|---|---|
| Fully Paid | 31,566 |
| Charged Off | 5,281 |
| Current | 1,098 |

The "Current" category is removed because it does not represent a completed loan outcome for the analysis.

The primary comparison therefore becomes:

```
Fully Paid
vs.
Charged Off
```

### Derived Features

Two time-based variables are created from the issue date:

- `issue_month`
- `issue_year`

These features provide additional temporal information for potential analysis.

The notebook subsequently focuses primarily on borrower and loan characteristics.

---

## Outlier Analysis

Box plots are used to investigate potential outliers in numerical variables.

Variables examined include:

- `loan_amnt`
- `funded_amnt`
- `funded_amnt_inv`
- `installment`
- `annual_inc`
- `dti`
- `delinq_2yrs`
- `inq_last_6mths`
- `revol_bal`
- `revol_util`
- `total_acc`
- `total_pymnt`
- `total_pymnt_inv`
- `recoveries`
- `last_pymnt_amnt`

An IQR-based filtering function is then applied using a threshold of:

```
1.5 × IQR
```

The filtering is applied sequentially across the selected variables.

### Dataset After Outlier Filtering

The resulting dataset contains:

- 20,129 rows
- 43 columns

This means the outlier-filtering stage substantially reduces the analysis population.

Because sequential IQR filtering can remove legitimate extreme observations as well as statistical outliers, the final results should be interpreted as findings within this filtered analytical dataset.

---

## Charge-Off Ratio

For the categorical risk analysis, the notebook calculates:

```
Charged-Off Ratio =
Charged Off
-----------------------------
Charged Off + Fully Paid
× 100
```

This measure is used to compare historical loan outcomes across borrower and loan segments.

For example:

```python
Chargedoff_Ratio = (
    Charged Off /
    (Charged Off + Fully Paid)
) * 100
```

This is a historical segment-level ratio, not an individual default probability.

---

## Exploratory Data Analysis

The project uses:

- Univariate analysis
- Segmented univariate analysis
- Bivariate analysis
- Box plots
- Histograms
- Count plots
- Bar charts
- Correlation heatmaps

The analysis focuses on variables that can provide meaningful insight into historical loan performance.

### 1. Home Ownership

Home ownership categories include:

- MORTGAGE
- RENT
- OWN
- OTHER

The observed charge-off ratios in the filtered dataset are:

| Home Ownership | Charged-Off Ratio |
|---|---|
| MORTGAGE | 4.28% |
| OTHER | 3.85% |
| OWN | 3.73% |
| RENT | 3.57% |

The analysis shows relatively similar historical charge-off ratios across the main home-ownership categories.

This suggests that home ownership alone does not create a large separation in this particular filtered dataset.

### 2. Loan Purpose

Loan purpose is analyzed using both loan counts and historical charge-off ratios.

The highest observed charge-off ratios include:

| Purpose | Charged-Off Ratio |
|---|---|
| Small Business | 6.93% |
| Vacation | 6.22% |
| Medical | 4.83% |
| Debt Consolidation | 4.49% |
| Other | 4.49% |

The lowest observed ratios in the analyzed data include:

| Purpose | Charged-Off Ratio |
|---|---|
| House | 1.33% |
| Car | 1.80% |
| Major Purchase | 2.31% |

These results indicate differences in historical charge-off rates across loan purposes.

The results should be interpreted as associations in the dataset rather than evidence that loan purpose itself causes default.

### 3. Verification Status

The analysis compares:

- Verified
- Source Verified
- Not Verified

Observed charge-off ratios:

| Verification Status | Charged-Off Ratio |
|---|---|
| Verified | 5.46% |
| Not Verified | 3.40% |
| Source Verified | 3.30% |

Within this dataset, the verified category has the highest historical charge-off ratio.

This does not imply that verification causes higher default rates. Verification status may be related to other borrower or loan characteristics.

### 4. Loan Amount

Loan amounts are analyzed using both distributions and custom buckets.

**Loan Amount Buckets**

```
0 - 5K
5K - 10K
10K - 15K
15K - above
```

Observed charge-off ratios:

| Loan Amount | Charged-Off Ratio |
|---|---|
| 15K and above | 9.06% |
| 10K - 15K | 4.05% |
| 5K - 10K | 3.08% |
| 0 - 5K | 3.01% |

Within the filtered dataset, larger loan amounts show higher historical charge-off ratios.

This is an observed relationship and does not establish that increasing the loan amount independently causes higher default risk.

### 5. Debt-to-Income Ratio

DTI is analyzed both continuously and using buckets.

**DTI Buckets**

```
Very Low    <= 8%
Low         8 - 12%
Moderate    12 - 16%
High        16 - 20%
Very High   > 20%
```

Observed charge-off ratios:

| DTI Bucket | Charged-Off Ratio |
|---|---|
| Very High (>20%) | 5.05% |
| Moderate (12-16%) | 4.66% |
| High (16-20%) | 4.47% |
| Low (8-12%) | 3.11% |
| Very Low (<=8%) | 2.47% |

The filtered dataset shows higher historical charge-off ratios in several higher-DTI groups.

The strongest observed ratio is in the >20% DTI bucket.

### 6. Annual Income

Annual income is analyzed using distribution plots and income buckets.

**Income Buckets**

```
0 - 40K
40K - 50K
50K - 60K
60K - 70K
70K - 80K
80K and above
```

Observed charge-off ratios:

| Annual Income | Charged-Off Ratio |
|---|---|
| 50K - 60K | 5.17% |
| 0 - 40K | 4.49% |
| 60K - 70K | 3.68% |
| 70K - 80K | 3.54% |
| 40K - 50K | 3.47% |
| 80K and above | 2.04% |

The highest observed ratio occurs in the 50K - 60K group, while the 80K+ group has the lowest observed ratio.

The relationship is not strictly monotonic across all income buckets, so the results should not be reduced to a simple "higher income always means lower risk" statement.

### 7. Interest Rate

Interest rates are analyzed using both distributions and custom buckets.

**Interest Rate Buckets**

```
Very Low   <= 8%
Low        8 - 11%
Moderate   11 - 13%
High       13 - 15%
Very High  > 15%
```

Observed charge-off ratios:

| Interest Rate | Charged-Off Ratio |
|---|---|
| Very High (>15%) | 7.94% |
| High (13-15%) | 4.92% |
| Moderate (11-13%) | 4.64% |
| Low (8-11%) | 2.94% |
| Very Low (<=8%) | 1.55% |

The analysis shows an increasing historical charge-off ratio across the interest-rate buckets, with the highest ratio in the >15% category.

Interest rate may also reflect other characteristics of the underlying loan and borrower, so this is an association rather than an independent causal effect.

### 8. Geographic Analysis

The `addr_state` variable is analyzed to compare historical charge-off ratios across U.S. states.

The notebook calculates:

- State
- Charged Off
- Fully Paid
- Charged-Off Ratio

Examples from the resulting analysis include:

| State | Charged-Off Ratio |
|---|---|
| TN | 14.29% |
| VT | 9.68% |
| MS | 7.69% |
| SD | 6.67% |
| NM | 6.38% |
| GA | 6.35% |
| WA | 6.33% |
| NV | 6.20% |

Some states have very small numbers of loans in the filtered dataset. Therefore, state-level percentages should be interpreted together with the underlying number of observations.

For example, a high percentage based on a small number of loans can be unstable and should not automatically be interpreted as a broad state-level risk characteristic.

---

## Correlation Analysis

A correlation heatmap is used to examine relationships among selected numerical variables.

Initial variables include:

- `loan_amnt`
- `funded_amnt`
- `funded_amnt_inv`
- `installment`
- `total_pymnt`
- `total_rec_prncp`
- `total_rec_int`
- `int_rate`
- `dti`
- `annual_inc`

Strong positive relationships include:

| Variable Pair | Correlation |
|---|---|
| Loan Amount / Installment | 0.93 |
| Loan Amount / Total Payment | 0.91 |
| Installment / Total Payment | 0.89 |
| Total Payment / Total Interest | 0.82 |
| Loan Amount / Total Interest | 0.71 |

The strong relationships between loan amount, installment, and payment-related variables are expected because these variables are mathematically or economically related.

The analysis therefore creates a refined correlation view containing:

- `loan_amnt`
- `installment`
- `total_pymnt`
- `total_rec_int`
- `int_rate`
- `dti`
- `annual_inc`

---

## Key Findings

Within the final filtered analytical dataset, several patterns stand out:

**Loan Amount**
Loans of 15K and above have the highest observed charge-off ratio among the defined loan-amount buckets.

**DTI**
The >20% DTI bucket has the highest observed charge-off ratio among the DTI groups.

**Interest Rate**
The >15% interest-rate bucket has the highest observed charge-off ratio.

**Income**
The 80K+ income group has the lowest observed charge-off ratio, while the 50K-60K group has the highest among the defined income buckets.

**Loan Purpose**
Small Business and Vacation have the highest observed charge-off ratios among the analyzed purposes.

**Verification**
The Verified category has a higher historical charge-off ratio than the Not Verified and Source Verified categories in this dataset.

**Home Ownership**
The main home-ownership categories show relatively similar historical charge-off ratios compared with some of the differences observed for loan amount, interest rate, and DTI.

---

## Business Interpretation

The analysis suggests that historical loan performance varies across several borrower and loan characteristics.

The variables showing notable segmentation in the final analysis include:

- Loan Amount
- Interest Rate
- Debt-to-Income Ratio
- Loan Purpose
- Annual Income
- Verification Status
- Geographic State

These variables could be useful inputs for a subsequent credit-risk modeling project.

However, the EDA alone cannot determine which variables are independently predictive after controlling for other factors.

---

## Methodology

The overall workflow is:

```
Raw Lending Club Dataset
          |
          v
Data Quality Checks
          |
          v
Missing-Value Analysis
          |
          v
Column Reduction
          |
          v
Data Type Standardization
          |
          v
Missing-Record Removal
          |
          v
Loan Status Filtering
          |
          v
Derived Features
          |
          v
Outlier Detection
          |
          v
IQR-Based Filtering
          |
          v
Univariate Analysis
          |
          v
Segmented Univariate Analysis
          |
          v
Bivariate Analysis
          |
          v
Correlation Analysis
          |
          v
Business Insights
```

---

## Technologies Used

**Programming**
- Python
- Jupyter Notebook

**Data Analysis**
- Pandas
- NumPy

**Visualization**
- Matplotlib
- Seaborn

**Statistical / Analytical Techniques**
- Descriptive statistics
- Missing-value analysis
- Outlier analysis
- IQR filtering
- Group-by aggregation
- Segmented analysis
- Correlation analysis
- Ratio analysis

---

## Project Structure

```
LENDING CASE STUDY/
│
├── Lending_Club_Casestudy_Notebook.ipynb
├── loan.csv
└── README.md
```

The notebook generates the visualizations used for the analysis during execution.

---

## How to Run

### 1. Install Dependencies

```bash
pip install pandas numpy matplotlib seaborn
```

### 2. Add the Dataset

Place the Lending Club dataset in the notebook working directory:

```
loan.csv
```

### 3. Open the Notebook

```
Lending_Club_Casestudy_Notebook.ipynb
```

### 4. Run the Notebook

Execute the notebook sequentially to reproduce:

- data cleaning
- missing-value analysis
- feature preparation
- outlier analysis
- univariate analysis
- bivariate analysis
- charge-off ratio calculations
- correlation analysis
- visualizations

---

## Important Analytical Considerations

### Outlier Filtering

The notebook applies sequential IQR filtering across multiple numerical variables.

The dataset decreases from:

```
36,847 rows
```

before outlier filtering to:

```
20,129 rows
```

after filtering.

This is a substantial reduction and can affect the resulting distributions and charge-off ratios.

A future version could compare:

- results before outlier removal
- results after outlier removal
- robust statistical methods
- winsorization
- variable-specific outlier treatment

rather than relying on a single global filtering strategy.

### Association vs Causation

The observed relationships do not establish causal relationships.

For example, the observation that higher interest-rate loans have higher historical charge-off ratios does not mean that interest rate alone causes default.

Multiple borrower and loan characteristics can interact with one another.

### Small-Sample Segments

Some state and purpose categories contain relatively few observations.

A high charge-off percentage based on a small number of loans can be statistically unstable.

Therefore, segment-level ratios should be interpreted together with the number of loans represented by each segment.

---

## Future Improvements

Potential extensions include:

- Build a binary classification model for loan default risk.
- Use `loan_status` as the target variable.
- Compare Logistic Regression, Random Forest, XGBoost, and other classifiers.
- Perform proper train/test splitting before modeling.
- Encode categorical variables using appropriate techniques.
- Handle class imbalance.
- Use cross-validation.
- Evaluate ROC-AUC, PR-AUC, precision, recall, and F1-score.
- Perform feature importance and model interpretability analysis.
- Compare model performance with and without aggressive outlier filtering.
- Use probability calibration for risk scoring.
- Perform statistical significance testing for important segment differences.
- Build a borrower-level risk scoring interface.
- Investigate temporal validation to reduce leakage from future information.

---

## Key Skills Demonstrated

### Data Cleaning
- Missing-value analysis
- Column filtering
- Data type conversion
- Invalid-value handling
- Date parsing
- Record-level filtering

### Exploratory Data Analysis
- Univariate analysis
- Segmented analysis
- Bivariate analysis
- Distribution analysis
- Correlation analysis
- Outlier detection

### Business Analytics
- Risk segmentation
- Charge-off rate analysis
- Borrower profiling
- Loan-purpose analysis
- Financial variable analysis
- Geographic analysis

### Visualization
- Histograms
- Box plots
- Count plots
- Bar charts
- Correlation heatmaps

---

## Project Takeaways

This project demonstrates how raw lending data can be transformed into a structured analytical dataset and investigated through multiple levels of exploratory analysis.

The workflow moves from:

```
Raw Financial Data
       ↓
Data Cleaning
       ↓
Data Quality Validation
       ↓
Feature Preparation
       ↓
Outlier Analysis
       ↓
Risk Segmentation
       ↓
Charge-Off Analysis
       ↓
Correlation Analysis
       ↓
Business Insights
```

The resulting analysis identifies several borrower and loan segments with different historical charge-off rates and provides a foundation for a future predictive credit-risk modeling system.

---

## Academic Context

This project was developed as part of postgraduate Machine Learning and AI coursework and focuses on exploratory data analysis and business problem solving using financial lending data.

---

## Disclaimer

This project is an educational data-analysis exercise.

The historical relationships identified in the dataset should not be interpreted as financial advice, individual credit decisions, or causal conclusions about borrower behavior.

---

## Author

**Saumy DhoLu**

Machine Learning & AI Portfolio

GitHub:(https://github.com/SaumyDhoLu)
