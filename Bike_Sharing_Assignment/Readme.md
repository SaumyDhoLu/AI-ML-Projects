# Bike Sharing Demand Prediction

## Project Overview

This project focuses on predicting daily bike-sharing demand using **Multiple Linear Regression**.

The analysis was performed for a fictional bike-sharing company, **BoomBikes**, with the objective of understanding the factors that influence daily bike demand and building an interpretable predictive model.

The project combines exploratory data analysis, feature engineering, statistical analysis, feature selection, multicollinearity analysis, residual diagnostics, and model evaluation.

---

## Business Problem

BoomBikes, a US-based bike-sharing provider, experienced a decline in revenue during the COVID-19 pandemic.

To support recovery and future planning, the company wants to understand:

- Which factors influence daily bike demand?
- How do weather conditions affect demand?
- How does seasonality influence demand?
- How do working days, holidays, and other temporal factors affect usage?
- Can daily bike demand be predicted using a statistical machine learning model?

Understanding these relationships can help the business improve operational planning, resource allocation, and demand forecasting.

---

## Objective

The primary objective is to develop a **Multiple Linear Regression model** that can:

1. Predict daily bike-sharing demand.
2. Identify important factors influencing demand.
3. Understand the direction and strength of relationships between demand and explanatory variables.
4. Provide interpretable insights that can support business decision-making.

The target variable for prediction is **`cnt`**, representing the total number of bike rentals.

---

## Dataset

The analysis uses the `day.csv` dataset containing daily bike-sharing information.

The dataset contains information related to:

- Date and time-related variables
- Season
- Weather conditions
- Temperature
- Humidity
- Wind speed
- Working days
- Holidays
- Registered users
- Casual users
- Total bike rentals

### Important Data Preparation

Variables such as `casual` and `registered` were excluded from the predictive modelling because they are components of the total rental count and would introduce direct information about the target variable.

`instant` was also removed because it acts as an index rather than a meaningful explanatory feature.

Highly related temperature variables were also examined to reduce redundancy and multicollinearity.

---

## Approach

The project follows the following workflow:

### 1. Data Understanding

- Loaded the dataset using Pandas.
- Examined dimensions, data types, and summary statistics.
- Checked for missing values and duplicate records.
- Investigated categorical and numerical variables.

### 2. Exploratory Data Analysis

Visual analysis was performed to understand relationships between bike demand and variables such as:

- Season
- Weather
- Temperature
- Humidity
- Wind speed
- Working days
- Holidays
- Year and month
- Weekdays

Both univariate and bivariate analysis were used to identify patterns and relationships.

### 3. Data Preparation

The dataset was prepared for modelling by:

- Removing variables that could cause target leakage.
- Handling categorical variables through encoding.
- Creating/adjusting relevant categorical representations.
- Examining highly correlated variables.
- Preparing numerical variables for modelling.

### 4. Train-Test Split

The dataset was divided into training and testing sets using a **70:30 split** with a fixed random state to ensure reproducibility.

### 5. Feature Selection

Feature selection was explored using:

- Recursive Feature Elimination (RFE)
- Statistical significance
- Backward elimination
- Variance Inflation Factor (VIF)

VIF analysis was used to identify and reduce multicollinearity among explanatory variables.

### 6. Multiple Linear Regression

An Ordinary Least Squares (OLS) regression model was developed using `statsmodels`.

The model was evaluated using statistical measures such as:

- R-squared
- Adjusted R-squared
- Coefficients
- p-values
- F-statistic

### 7. Residual Analysis

The model assumptions were investigated using:

- Residual distribution
- Residual vs fitted-value analysis
- Normality assessment
- Homoscedasticity assessment
- Durbin-Watson statistic
- CCPR plots

These diagnostics were used to assess whether the fitted regression model was reasonably consistent with the assumptions of linear regression.

### 8. Test Set Evaluation

The final model was used to generate predictions on unseen test data.

Model performance was evaluated using:

- R-squared
- Adjusted R-squared
- RMSE

---

## Model Performance

The final model achieved approximately:

| Metric | Training Set | Test Set |
|---|---:|---:|
| R² | 0.821 | 0.798 |
| Adjusted R² | 0.818 | 0.790 |

The relatively small difference between the training and test R² values indicates that the model maintains reasonably consistent performance on unseen data.

> Note: The modelling workflow includes scaling of the target variable, so RMSE values from the notebook should be interpreted in the scaled target space rather than directly as the number of bikes.

---

## Model Diagnostics

The regression model was subjected to multiple diagnostic checks.

### Multicollinearity

Variance Inflation Factor (VIF) was used to identify highly correlated explanatory variables.

Variables were iteratively evaluated and removed where appropriate to improve the stability and interpretability of the regression model.

### Residual Analysis

Residual plots and statistical diagnostics were used to examine:

- Linearity
- Normality
- Homoscedasticity
- Independence of errors

The final model produced a Durbin-Watson statistic of approximately **2.0**, which is consistent with relatively low residual autocorrelation.

---

## Key Findings

The analysis indicates that bike demand is influenced by a combination of:

- Temperature
- Weather conditions
- Seasonality
- Year
- Working-day characteristics
- Other temporal factors

Among the model variables, temperature showed a strong positive relationship with bike demand, while adverse weather conditions were associated with lower demand.

The analysis demonstrates that bike-sharing demand is influenced by both **environmental conditions and temporal patterns**, making these variables useful for demand forecasting and operational planning.

---

## Technologies Used

- Python
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Statsmodels
- Scikit-learn
- SciPy
- Jupyter Notebook

### Main Techniques

- Exploratory Data Analysis
- Data Cleaning
- Feature Engineering
- Categorical Encoding
- Multiple Linear Regression
- Recursive Feature Elimination
- Backward Elimination
- Variance Inflation Factor (VIF)
- Residual Analysis
- Statistical Model Evaluation

---

## Project Structure

```text
Bike_Sharing_Assignment/
│
├── Saumy_Bike_Sharing.ipynb
├── README.md
