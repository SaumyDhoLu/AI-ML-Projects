# Telecom Customer Churn Prediction

## Project Overview

This project develops a machine learning solution for predicting customer churn in the telecommunications industry.

The objective is to identify customers who are likely to churn based on historical customer behavior, recharge activity, revenue patterns, call usage, roaming activity, data usage, and other customer-level attributes.

The project follows a complete machine learning workflow:

- Data understanding and cleaning
- Missing-value analysis
- Feature engineering
- Exploratory data analysis
- Outlier treatment
- Feature scaling
- Feature selection
- Dimensionality reduction using PCA
- Baseline Logistic Regression
- Hyperparameter tuning
- XGBoost modelling
- Classification threshold optimization
- Random Forest analysis
- SHAP-based model interpretation
- Kaggle submission
- Business-oriented churn insights

The project was developed as part of a telecom churn prediction Kaggle case study.

---

## Business Problem

Customer acquisition is generally more expensive than retaining existing customers.

For a telecom operator, identifying customers who are showing behavioral patterns associated with churn can help the business prioritize retention efforts.

The machine learning objective is therefore:

> **Predict whether a customer is likely to churn using historical telecom usage and recharge behavior.**

The model uses customer activity from multiple months to capture changes in engagement and usage behavior before churn.

---

## Dataset

The project uses separate training and unseen test datasets.

### Dataset dimensions

| Dataset | Rows | Columns |
|---|---:|---:|
| Training data | 69,999 | 172 |
| Unseen test data | 30,000 | 171 |
| Sample submission | 30,000 | 2 |
| Data dictionary | 36 | 2 |

The training dataset contains the target variable:

```
churn_probability
```

The final modelling task treats this target as a binary classification problem:

```
0 → Non-churn
1 → Churn
```

---

## Feature Categories

The dataset contains a wide range of telecom customer behavior variables.

Major feature groups include:

### Revenue and ARPU

Examples:

```
arpu_6
arpu_7
arpu_8
```

These represent customer revenue/activity levels across different months.

### Recharge Behavior

Examples:

```
total_rech_num_6
total_rech_num_7
total_rech_num_8

total_rech_amt_6
total_rech_amt_7
total_rech_amt_8

max_rech_amt_6
max_rech_amt_7
max_rech_amt_8

last_day_rch_amt_6
last_day_rch_amt_7
last_day_rch_amt_8
```

### Call Usage

Examples:

```
total_og_mou_6
total_og_mou_7
total_og_mou_8

total_ic_mou_6
total_ic_mou_7
total_ic_mou_8

onnet_mou_6
onnet_mou_7
onnet_mou_8

offnet_mou_6
offnet_mou_7
offnet_mou_8
```

### Roaming Activity

Examples:

```
roam_ic_mou_6
roam_ic_mou_7
roam_ic_mou_8

roam_og_mou_6
roam_og_mou_7
roam_og_mou_8
```

### Data Usage

Examples include:

```
vol_2g_mb_6
vol_2g_mb_7
vol_2g_mb_8

vol_3g_mb_6
vol_3g_mb_7
vol_3g_mb_8
```

The monthly structure allows the model to capture changes in customer behavior over time.

---

## Machine Learning Workflow

```
Raw Telecom Dataset
        |
        v
Data Understanding
        |
        v
Data Quality Analysis
        |
        v
Missing-Value Analysis
        |
        v
Feature Engineering
        |
        v
Exploratory Data Analysis
        |
        v
Outlier Treatment
        |
        v
Feature Scaling
        |
        v
Feature Selection
        |
        +--------------------+
        |                    |
        v                    v
       PCA              Selected Features
        |                    |
        v                    v
Logistic Regression     Tree Models
                             |
                             v
                         XGBoost
                             |
                             v
                  Hyperparameter Tuning
                             |
                             v
                    Threshold Optimization
                             |
                             v
                    Model Interpretation
                             |
                             v
                    Kaggle Submission
```

---

## Data Preparation

### Train / Validation Split

The training data is divided using an 80/20 split:

```python
train_test_split(
    X,
    y,
    test_size=0.2,
    random_state=9
)
```

Result:

```
Training samples:   55,999
Validation samples: 14,000
```

### Missing-Value Analysis

Missing-value percentages were analyzed across the available features.

Features with more than 40% missing values were excluded from the selected modelling feature set.

The resulting selected feature matrix contains:

```
61 features
```

The project also explores different imputation strategies during different modelling stages.

These include:

- Constant-value imputation
- Median imputation

For the final XGBoost pipeline, median imputation is used.

### Date Feature Engineering

Several telecom activity columns contain dates.

The notebook processes:

```
last_date_of_month_6
last_date_of_month_7
last_date_of_month_8

date_of_last_rech_6
date_of_last_rech_7
date_of_last_rech_8
```

The workflow:

- Converts the columns to datetime.
- Handles invalid/missing dates.
- Extracts the day component.
- Uses the resulting numeric features during modelling.

---

## Exploratory Data Analysis

The notebook performs extensive exploratory analysis including:

- Univariate analysis
- Bivariate analysis
- Multivariate analysis
- Correlation analysis
- Distribution plots
- Boxplots
- Missing-value visualization
- Feature correlation heatmaps
- Principal Component visualization

The analysis focuses particularly on customer engagement, recharge behavior, call activity, and changes across months.

---

## Outlier Treatment

Outliers are investigated using distribution analysis and boxplots.

The notebook applies outlier capping based on standard deviation for selected numerical variables.

This reduces the impact of extreme observations while retaining the corresponding customer records.

---

## Feature Scaling

StandardScaler is used to standardize numerical features.

Scaling is particularly important for modelling approaches such as:

- Logistic Regression
- PCA

Tree-based models such as XGBoost do not fundamentally require feature scaling, although the final pipeline in this project includes a scaling step.

---

## PCA Analysis

Principal Component Analysis is explored as a dimensionality-reduction technique.

The project investigates:

- Explained variance
- Principal component distributions
- Relationships between principal components and churn
- Random Forest importance across PCA components

A Logistic Regression model is subsequently trained using the first seven PCA components in one of the modelling experiments.

---

## Baseline Model — Logistic Regression

The first major modelling approach uses Logistic Regression.

The initial PCA-based model achieves approximately:

```
Training Accuracy: 90.28%
```

A preprocessing pipeline is then constructed:

```
Imputation
    ↓
Standard Scaling
    ↓
PCA
    ↓
Logistic Regression
```

The pipeline uses:

```
PCA Components: 10
Logistic Regression
max_iter: 1000
```

### Baseline Validation Results

The baseline pipeline produces:

```
Validation Accuracy: 90.18%
```

Confusion matrix:

```
[[12462,   116],
 [ 1259,   163]]
```

The corresponding churn-class recall is approximately:

```
11.46%
```

This highlights an important characteristic of the dataset:

Overall accuracy alone does not adequately describe performance when churn is the minority class.

The model initially identifies the majority non-churn class much more effectively than the churn class.

---

## Logistic Regression Hyperparameter Tuning

The project then uses:

- GridSearchCV
- RandomizedSearchCV

The search explores:

- C
- Penalty
- Solver

The GridSearchCV result reported:

```
Best C: 10
Penalty: L1
Solver: liblinear

Cross-validation F1:
0.2014
```

The RandomizedSearchCV result reported:

```
C: 10
Penalty: L2
Solver: liblinear

Cross-validation F1:
0.2004
```

---

## Classification Threshold Optimization

Because the dataset is imbalanced, the default probability threshold of 0.5 is not necessarily optimal for identifying churn cases.

The project evaluates multiple thresholds.

For the Logistic Regression experiment:

```
Best threshold: 0.20
F1 score: 0.4888
```

This demonstrates how classification threshold selection can substantially change minority-class performance.

---

## XGBoost Model

The project then moves to XGBoost to capture nonlinear relationships and interactions between telecom behavioral variables.

The XGBoost pipeline contains:

```
Median Imputation
        ↓
Standard Scaling
        ↓
XGBoost Classifier
```

### XGBoost Hyperparameter Optimization

RandomizedSearchCV is used to search across a broad hyperparameter space.

Parameters include:

- n_estimators
- max_depth
- learning_rate
- subsample
- colsample_bytree
- gamma
- min_child_weight
- scale_pos_weight
- reg_alpha
- reg_lambda

The search performs:

```
100 random parameter configurations
5-fold cross-validation
```

for a total of:

```
500 model fits
```

### Best XGBoost Configuration

The reported best configuration was:

```
n_estimators       = 109
max_depth          = 8
learning_rate      = 0.02006
subsample          = 0.91063
colsample_bytree   = 0.62424
gamma              = 0.22318
min_child_weight   = 2
scale_pos_weight   = 2
reg_alpha          = 0.49398
reg_lambda         = 0.75464
```

The best cross-validation accuracy reported by the notebook was:

```
94.25%
```

### XGBoost Threshold Optimization

After training the tuned XGBoost model, prediction probabilities are evaluated across multiple classification thresholds.

The selected validation threshold was:

```
0.60
```

The resulting validation accuracy was:

```
94.46%
```

### XGBoost Validation Performance

At the selected threshold, the notebook reports:

| Metric | Non-Churn | Churn |
|---|---|---|
| Precision | 0.96 | 0.78 |
| Recall | 0.98 | 0.63 |
| F1 Score | 0.97 | 0.70 |

Overall:

```
Accuracy: 94.46%
Macro F1: approximately 0.83
```

The confusion matrix reported in the notebook is:

```
True Negative: 12,328
False Positive:   250
False Negative:   525
True Positive:    897
```

This provides a much more informative view of performance than accuracy alone.

---

## Kaggle Submission

The tuned XGBoost model was used to generate predictions for the 30,000-row unseen test dataset.

The submission generated by the notebook was:

```
submission_xgb_randomsearch_0.94464.csv
```

The notebook reports the following Kaggle leaderboard result:

```
Kaggle Accuracy: 0.94576
```

The distinction between validation performance and leaderboard performance is important:

```
Cross-validation accuracy: 0.9425
Validation accuracy:       0.9446
Kaggle score:              0.94576
```

The Kaggle score is an external evaluation on the competition's unseen evaluation data.

---

## Model Interpretability

A major part of the project goes beyond prediction and investigates which customer behaviors influence churn predictions.

Two approaches are used:

- XGBoost feature importance
- SHAP analysis

### XGBoost Feature Importance

The feature-importance analysis identifies several usage and recharge variables as highly influential.

The most prominent feature reported by the notebook is:

```
total_ic_mou_8
```

representing total incoming minutes of usage during month 8.

Other important variables include:

```
total_og_mou_8
roam_og_mou_8
last_day_rch_amt_8
```

A notable pattern is that many highly influential features come from the most recent months.

This suggests that recent customer behavior contains substantial predictive information for the model.

### SHAP Analysis

SHAP is used to investigate how individual features influence the XGBoost predictions.

Important variables identified in the notebook include:

```
last_day_rch_amt_8
last_day_rch_amt_7
total_rech_num_8
total_ic_mou_8
total_og_mou_7
onnet_mou_6
roam_og_mou_8
roam_ic_mou_6
```

The SHAP analysis helps move from:

```
"What features are important?"
```

toward:

```
"How does the value of a feature influence the model's churn prediction?"
```

---

## Behavioral Patterns Identified

The model interpretation suggests several recurring patterns.

### Recharge Behavior

Lower recent recharge activity is associated with higher predicted churn risk in the model's SHAP analysis.

Relevant variables include:

```
last_day_rch_amt_8
last_day_rch_amt_7
total_rech_num_8
```

### Call Usage

Recent incoming and outgoing call activity is highly influential.

Examples:

```
total_ic_mou_8
total_og_mou_7
onnet_mou_6
```

Lower usage values frequently push the model toward higher churn predictions in the SHAP analysis.

### Recent-Month Behavior

A large proportion of important variables come from months 7 and 8.

This indicates that recent behavioral information is particularly important to the trained model.

---

## Random Forest Churn Analysis

A separate Random Forest experiment is also used to investigate the selected features.

The initial Random Forest produced:

```
Accuracy: 92.24%
```

However, churn-class recall was only:

```
0.36
```

This motivated an additional class-imbalance experiment.

### Handling Class Imbalance

A balanced Random Forest is trained using:

```python
class_weight="balanced"
```

The classification threshold is then adjusted to improve churn detection.

At a threshold of:

```
0.11
```

the notebook reports:

| Metric | Non-Churn | Churn |
|---|---|---|
| Precision | 0.98 | 0.50 |
| Recall | 0.91 | 0.85 |
| F1 Score | 0.94 | 0.63 |

Overall accuracy:

```
90.00%
```

This experiment demonstrates an important business trade-off:

Increasing sensitivity to churn cases can substantially reduce overall accuracy.

Therefore, the appropriate threshold depends on the business cost of false positives versus missed churners.

---

## Results Summary

| Experiment | Key Result |
|---|---|
| PCA + Logistic Regression | ~90.18% validation accuracy |
| Logistic Regression threshold tuning | F1 ≈ 0.489 |
| XGBoost CV | 94.25% |
| XGBoost validation | 94.46% accuracy |
| XGBoost churn precision | 78.20% |
| XGBoost churn recall | 63.08% |
| XGBoost churn F1 | 69.83% |
| Kaggle leaderboard | 94.576% |
| Balanced Random Forest | 85% churn recall at threshold 0.11 |

The Kaggle score is reported from the competition submission documented in the notebook.

---

## Results Folder

The repository contains a dedicated results directory with outputs generated during the project.

The results include artifacts such as:

- Exploratory data analysis visualizations
- Feature importance plots
- SHAP visualizations
- Confusion matrices
- Model evaluation outputs
- Threshold analysis
- Prediction results
- Kaggle submission/result artifacts
- Highest-scoring result artifact

These outputs provide visual and numerical evidence supporting the modelling workflow documented in the notebook.

---

## Key Business Insights

The analysis indicates several customer behaviors that are strongly associated with the model's churn predictions.

### 1. Recent recharge activity

Declining recharge amounts and recharge frequency are important signals.

### 2. Recent call usage

Changes in incoming and outgoing call activity are strongly represented among important features.

### 3. Recent customer engagement

Variables from the latest months, particularly months 7 and 8, frequently appear among important predictors.

### 4. Roaming behavior

Roaming-related variables also contribute to the model's predictions.

These observations represent model-derived associations, not proof that any individual behavior causes churn.

---

## Potential Business Applications

A telecom company could use a churn prediction system as part of a broader retention workflow.

Potential applications include:

- Prioritizing customers for retention campaigns
- Monitoring declining recharge activity
- Identifying sudden reductions in usage
- Creating customer risk segments
- Triggering targeted retention offers
- Supporting customer-service outreach
- Monitoring changes in churn risk over time

Any real-world deployment would require additional validation, calibration, monitoring, and business-cost analysis.

---

## Technical Challenges Addressed

This project covers several common challenges in real-world tabular machine learning:

**High-dimensional data**
The original dataset contains 171 predictors before feature selection.

**Missing values**
Many telecom usage variables contain missing observations.

**Class imbalance**
Churn cases are substantially less frequent than non-churn cases.

**Outliers**
Telecom usage and recharge variables contain extreme values.

**Feature redundancy**
Multiple monthly measurements of related variables create strong correlations.

**Threshold selection**
The classification threshold materially changes precision, recall, and F1.

**Model interpretability**
SHAP and feature-importance analysis are used to understand model behavior.

---

## Technologies Used

- Python
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Scikit-learn
- XGBoost
- SHAP
- Jupyter Notebook
- Kaggle

---

## Project Structure

```
Telecom Churn Case Study/
│
├── SaumyDhoLu_TC_casestudy.ipynb
│
├── RESULTS/
│
└── README.md
```

The exact result filenames may vary depending on the committed repository version.

---

## How to Run

### 1. Clone the repository

```bash
git clone https://github.com/SaumyDhoLu/AI-ML-Projects.git
```

### 2. Navigate to the project

```bash
cd AI-ML-Projects
cd "Telecom Churn Case Study"
```

### 3. Install dependencies

```bash
pip install pandas numpy matplotlib seaborn scikit-learn xgboost shap missingno
```

### 4. Launch Jupyter Notebook

```bash
jupyter notebook
```

Open:

```
SaumyDhoLu_TC_casestudy.ipynb
```

Make sure the required dataset files are available in the expected working directory before running the notebook.

---

## End-to-End Skills Demonstrated

This project demonstrates experience with:

- Supervised Machine Learning
- Binary Classification
- Telecom Analytics
- Customer Churn Prediction
- Exploratory Data Analysis
- Feature Engineering
- Missing-Value Treatment
- Outlier Handling
- Feature Scaling
- PCA
- Logistic Regression
- Random Forest
- XGBoost
- Hyperparameter Optimization
- GridSearchCV
- RandomizedSearchCV
- Cross-Validation
- Classification Threshold Optimization
- Precision / Recall Analysis
- F1 Score
- Confusion Matrix
- SHAP
- Model Interpretability
- Kaggle Competition Workflow
- Business Insight Generation

---

## Important Modeling Considerations

The reported results should be interpreted in the context of the notebook's experimental workflow.

**Threshold tuning**
The validation/test split is used to select classification thresholds. Therefore, the resulting validation metrics should not be interpreted as an untouched final holdout evaluation.

**Kaggle evaluation**
The Kaggle score is an external competition result and is separate from the local validation metrics.

**Class imbalance**
The relatively high overall accuracy partly reflects the strong performance on the majority non-churn class.

For practical churn detection, recall, precision, F1, and the business cost of false positives and false negatives are also important.

**Model interpretation**
Feature importance and SHAP values describe associations learned by the model. They do not establish causal relationships between customer behavior and churn.

---

## Future Improvements
 
Possible improvements include:
 
- Use a strictly separated validation set for threshold selection.
- Preserve an untouched final test set for unbiased evaluation.
- Optimize directly for business-relevant metrics rather than accuracy alone.
- Compare XGBoost with LightGBM and CatBoost.
- Explore calibrated probability outputs.
- Use cost-sensitive learning based on the financial impact of churn.
- Investigate temporal feature engineering across months.
- Create explicit month-over-month change features.
- Evaluate precision-recall AUC.
- Perform systematic class-imbalance experiments.
- Add model calibration analysis.
- Develop a reusable inference pipeline.
- Monitor model drift using future customer cohorts.
- Build a retention-prioritization layer on top of churn predictions.
---
 
## Project Highlights
 
- Worked with 69,999 telecom customers and a high-dimensional feature space.
- Reduced the modelling feature space to 61 selected variables.
- Performed extensive EDA and preprocessing.
- Compared dimensionality-reduction and tree-based approaches.
- Built Logistic Regression, Random Forest, and XGBoost models.
- Used GridSearchCV and RandomizedSearchCV for model optimization.
- Performed classification threshold tuning.
- Used XGBoost with class weighting through scale_pos_weight.
- Achieved 94.46% validation accuracy with the tuned XGBoost model.
- Achieved a reported 94.576% Kaggle leaderboard accuracy.
- Used SHAP for model interpretation.
- Investigated class imbalance and churn-class recall.
- Generated a dedicated collection of result visualizations and evaluation artifacts.
---
 
## Academic Context
 
This project was developed as a telecom customer churn prediction case study and Kaggle competition project.
 
It demonstrates an end-to-end machine learning workflow, from raw customer data and exploratory analysis through feature engineering, model selection, hyperparameter optimization, model interpretation, and external competition evaluation.
 
---
 
## Disclaimer
 
This project is an academic and competition-oriented machine learning implementation.
 
Model predictions represent statistical estimates learned from the available dataset and should not be treated as deterministic evidence that a particular customer will churn.
 
Any production deployment would require additional validation, probability calibration, fairness analysis, monitoring, data-governance controls, and business-cost evaluation.
 
---
 
## Author
 
**Saumy DhoLu**
 
AI/ML
 
GitHub:(https://github.com/SaumyDhoLu)
