# AI/ML Projects Portfolio

A curated portfolio of Machine Learning, Deep Learning, NLP, Generative AI, Computer Vision, and Data Analytics projects developed by **Saumy DhoLu**.

This repository brings together academic projects, practical AI/ML implementations, generative AI applications, and competition-oriented machine learning work.

The projects cover the complete machine learning lifecycle — from data exploration and preprocessing to model development, evaluation, interpretation, and application.

---

## About This Portfolio

This repository demonstrates hands-on experience across multiple areas of Artificial Intelligence and Machine Learning, including:

- Classical Machine Learning
- Deep Learning
- Computer Vision
- Natural Language Processing
- Generative AI
- Large Language Models
- Retrieval-Augmented Generation
- Time-Series Forecasting
- GANs
- Recommendation and prediction systems
- Healthcare AI
- Telecom Analytics
- Model Explainability
- AI-powered applications

The projects use a combination of traditional statistical methods, neural networks, transformer/LLM-based systems, retrieval pipelines, and generative models.

---

## Projects

| # | Project | Domain | Main Techniques |
|---|---|---|---|
| 1 | [Bike Sharing Demand Prediction](#1-bike-sharing-demand-prediction) | Regression / Analytics | OLS, RFE, VIF, Statistical Diagnostics |
| 2 | [CNN Waste Segregation](#2-cnn-waste-segregation) | Computer Vision | CNN, VGG-style CNN, Inception-style CNN |
| 3 | [Code Review Buddy](#3-code-review-buddy) | Generative AI / Developer Tools | Gemini, AST, Static Analysis, LangChain |
| 4 | [Healthcare NLP — Entity Identification](#4-healthcare-nlp--entity-identification) | NLP / Healthcare | NLP, Entity Extraction, Semantic Analysis |
| 5 | [Fake News Detection](#5-fake-news-detection) | NLP / Classification | Text Classification, Semantic Features |
| 6 | [Healthcare AI RAG System](#6-healthcare-ai-rag-system) | Generative AI / Healthcare | RAG, Embeddings, Vector Search, LLM |
| 7 | [Lending Club Case Study](#7-lending-club-case-study) | Data Analytics / Finance | EDA, Risk Analysis, Statistical Analysis |
| 8 | [NutraWiseAI](#8-nutrawiseai) | LLM / Healthcare AI | Gemini, PubMed, SQLite, Prompt Engineering |
| 9 | [RNN Stock Price Prediction](#9-rnn-stock-price-prediction) | Time Series / Deep Learning | RNN, LSTM, GRU |
| 10 | [MRI Cross-Contrast Synthesis](#10-mri-cross-contrast-synthesis) | Generative AI / Medical Imaging | CycleGAN, U-Net, PatchGAN, SSIM |
| 11 | [Telecom Churn Prediction](#11-telecom-churn-prediction) | Machine Learning / Kaggle | XGBoost, Random Forest, PCA, SHAP |

---

## Project Categories

### Machine Learning & Data Analytics

Projects focused on structured/tabular data, statistical analysis, predictive modelling, and business insights.

- Bike Sharing Demand Prediction
- Lending Club Case Study
- Telecom Churn Prediction

### Deep Learning & Computer Vision

Projects involving neural networks and image-based learning.

- CNN Waste Segregation
- MRI Cross-Contrast Synthesis using CycleGAN

### Natural Language Processing

Projects involving text processing, classification, semantic analysis, and healthcare NLP.

- Healthcare Entity Identification
- Fake News Detection

### Generative AI & LLM Applications

Projects focused on Large Language Models, retrieval, AI assistants, and AI-powered developer tools.

- Code Review Buddy
- Healthcare AI RAG System
- NutraWiseAI

### Time-Series Deep Learning

Projects exploring sequential neural networks for financial time-series data.

- RNN Stock Price Prediction

---

## 1. Bike Sharing Demand Prediction

**Machine Learning | Regression | Statistical Modelling**

A multiple linear regression project focused on predicting daily bike-sharing demand and identifying variables associated with demand.

### Techniques

- Exploratory Data Analysis
- Data Cleaning
- Feature Selection
- Recursive Feature Elimination
- Variance Inflation Factor
- Multiple Linear Regression
- Residual Analysis
- Durbin-Watson Test
- Model Evaluation

### Key Result

The final regression model achieved approximately:

```
Training R²: 0.821
Testing R²:  0.798
```

The project also includes multicollinearity analysis and residual diagnostics.

---

## 2. CNN Waste Segregation

**Computer Vision | Deep Learning | Image Classification**

A multi-class image classification project for automated waste-category recognition using convolutional neural networks.

The project explores multiple CNN architectures and training strategies.

### Techniques

- CNN
- VGG-inspired architecture
- Inception-style architecture
- Data Augmentation
- Class Balancing
- Early Stopping
- Model Checkpointing
- Validation Monitoring

### Dataset

The project works with seven waste categories and standardized:

```
128 × 128 × 3
```

image inputs.

The selected CNN model achieved approximately 70% classification accuracy in the reported experiment.

Saved Keras model artifacts are included with the project.

---

## 3. Code Review Buddy

**Generative AI | Developer Tools | Static Analysis**

An AI-powered code review assistant combining deterministic static analysis with Large Language Model-based contextual analysis.

The system can analyze code from:

- Individual files
- ZIP archives
- Local directories
- GitHub repositories

### Key Capabilities

- Python AST analysis
- Code quality analysis
- Security analysis
- Performance analysis
- Complexity analysis
- AI-generated recommendations
- Knowledge-base grounding
- HTML reports
- Markdown reports
- JSON summaries

### Technologies

- Python
- Google Gemini
- LangChain
- Python AST
- Static Analysis

The project combines traditional program analysis with LLM reasoning rather than relying exclusively on an LLM.

---

## 4. Healthcare NLP — Entity Identification

**Natural Language Processing | Healthcare**

A healthcare-focused NLP project for identifying and extracting meaningful entities from medical text.

The project explores natural-language processing techniques for converting unstructured healthcare text into structured information.

### Focus Areas

- Text preprocessing
- Entity identification
- Semantic analysis
- Healthcare terminology
- NLP-based information extraction

Generated outputs and analysis artifacts are included in the project results.

---

## 5. Fake News Detection

**NLP | Text Classification | Semantic Analysis**

A Natural Language Processing project focused on distinguishing between different classes of news content using textual information.

### Techniques

- Text preprocessing
- Feature engineering
- Semantic representation
- Text classification
- Model evaluation

The project demonstrates the application of machine learning to a real-world text classification problem.

---

## 6. Healthcare AI RAG System

**Generative AI | RAG | Healthcare**

A Retrieval-Augmented Generation system designed to combine information retrieval with Large Language Model generation for healthcare-oriented question answering.

### Architecture

```
User Query
    |
    v
Document Retrieval
    |
    v
Relevant Context
    |
    v
LLM
    |
    v
Context-Aware Response
```

### Core Concepts

- Retrieval-Augmented Generation
- Embeddings
- Vector Search
- Document Processing
- Prompt Construction
- LLM Generation
- Healthcare Information Retrieval

The project demonstrates how retrieval can be used to provide an LLM with relevant external context rather than relying solely on its pretrained knowledge.

---

## 7. Lending Club Case Study

**Data Analytics | Finance | Risk Analysis**

An exploratory data-analysis project using Lending Club loan data to investigate factors associated with loan charge-offs.

The project focuses on understanding relationships between loan characteristics and observed repayment outcomes.

### Analysis Areas

- Missing-value analysis
- Data cleaning
- Outlier treatment
- Loan status analysis
- Charge-off ratios
- Home ownership
- Loan purpose
- Verification status
- Loan amount
- Debt-to-income ratio
- Annual income
- Interest rate
- Geographic analysis
- Correlation analysis

### Important Perspective

This project is an observational risk-analysis study, not a predictive machine learning model.

The identified relationships represent associations within the analyzed dataset and should not be interpreted as causal relationships.

---

## 8. NutraWiseAI

**Generative AI | LLM | Healthcare / Nutrition**

NutraWiseAI is a personalized supplement-information assistant combining an LLM with structured user profiles, a local supplement database, and optional PubMed evidence retrieval.

### Features

- Personalized health profile
- Supplement information
- Gemini-powered responses
- SQLite supplement database
- PubMed integration
- Evidence-oriented responses
- Conversation memory
- CLI interface

### Technologies

- Python
- Google Gemini
- Google Generative AI SDK
- SQLite
- PubMed E-utilities
- Dataclasses
- Prompt Engineering

The project demonstrates how LLM applications can combine structured information with external scientific resources.

---

## 9. RNN Stock Price Prediction

**Deep Learning | Time Series | Financial Data**

A sequence-modelling project exploring recurrent neural networks for stock-price prediction across:

- Amazon (AMZN)
- Alphabet (GOOGL)
- IBM
- Microsoft (MSFT)

### Models

- Simple RNN
- LSTM
- Bidirectional LSTM
- GRU
- Multi-output RNN

### Experiments

The project compares different:

- Sequence lengths
- Strides
- Network architectures
- Dropout values
- Batch sizes
- Training configurations

The strongest reported GRU experiment achieved an overall R² of approximately:

```
0.9961
```

The project also includes saved model artifacts.

### Important Note

The reported metrics are based on scaled target values in several experiments, and the notebook uses the test data repeatedly during model comparison. Therefore, the results should be interpreted as experimental modelling results rather than evidence of real-world trading performance.

---

## 10. MRI Cross-Contrast Synthesis

**Generative AI | Medical Imaging | GANs**

An unpaired image-to-image translation system based on CycleGAN for translating between T1-weighted and T2-weighted MRI contrasts.

### Translation Directions

- T1 → T2
- T2 → T1

### Architecture

The system contains:

- T1 → T2 Generator
- T2 → T1 Generator
- T1 PatchGAN Discriminator
- T2 PatchGAN Discriminator

The generators use U-Net-style encoder-decoder architectures with skip connections.

### Techniques

- CycleGAN
- U-Net
- PatchGAN
- Instance Normalization
- Adversarial Loss
- Cycle-Consistency Loss
- Identity Loss
- SSIM
- NIFTI Processing

### Reported Cycle SSIM

```
T1 → T2 → T1 : 0.8858
T2 → T1 → T2 : 0.8387
```

These are cycle-reconstruction SSIM measurements rather than paired image-translation accuracy.

The project also includes an external NIFTI inference pipeline using MRI data from the IXI dataset.

This is an experimental medical-imaging project and is not clinically validated.

---

## 11. Telecom Churn Prediction

**Machine Learning | XGBoost | Kaggle**

An end-to-end telecom customer churn classification project using a high-dimensional customer behavior dataset.

### Dataset

```
Training samples: 69,999
Features:         171
Selected features: 61
```

### Models

- Logistic Regression
- Random Forest
- XGBoost

### Techniques

- Exploratory Data Analysis
- Missing-Value Treatment
- Feature Engineering
- Outlier Treatment
- Standard Scaling
- PCA
- GridSearchCV
- RandomizedSearchCV
- Threshold Optimization
- SHAP
- Class Imbalance Analysis

### XGBoost Result

Reported validation accuracy:

```
94.46%
```

Reported Kaggle leaderboard score:

```
0.94576
```

The project also investigates churn-class precision, recall, F1 score, and the effect of classification thresholds.

**View Project →**

---

## Technology Stack

**Programming**
- Python
- SQL

**Data Analysis**
- Pandas
- NumPy
- Matplotlib
- Seaborn

**Machine Learning**
- Scikit-learn
- XGBoost
- PCA
- Random Forest
- Logistic Regression
- Linear Regression

**Deep Learning**
- TensorFlow
- Keras

**Computer Vision**
- CNNs
- U-Net
- PatchGAN
- CycleGAN
- Image preprocessing
- SSIM

**NLP**
- Text preprocessing
- Semantic analysis
- Entity identification
- Text classification

**Generative AI**
- Large Language Models
- Google Gemini
- LangChain
- Retrieval-Augmented Generation
- Prompt Engineering
- AI agents/applications

**Data & Retrieval**
- SQLite
- PubMed / NCBI E-utilities
- Vector search
- Embeddings

**Explainability**
- SHAP
- Feature importance
- Statistical diagnostics

**Development Environment**
- Jupyter Notebook
- Google Colab
- Git
- GitHub

---

## Skills Demonstrated

Across these projects, the portfolio demonstrates practical experience in:

### Data Preparation
- Data cleaning
- Missing-value treatment
- Outlier handling
- Feature engineering
- Feature selection
- Scaling
- Dimensionality reduction

### Exploratory Data Analysis
- Univariate analysis
- Bivariate analysis
- Correlation analysis
- Distribution analysis
- Visualization
- Statistical diagnostics

### Machine Learning
- Regression
- Binary classification
- Ensemble learning
- Gradient boosting
- Hyperparameter optimization
- Cross-validation
- Threshold optimization

### Deep Learning
- CNNs
- RNNs
- LSTMs
- GRUs
- GANs
- CycleGAN
- Encoder-decoder architectures

### NLP & Generative AI
- NLP pipelines
- Entity identification
- Semantic classification
- LLM applications
- Prompt engineering
- RAG
- External knowledge retrieval
- AI-assisted code analysis

### Model Evaluation
- R²
- Accuracy
- Precision
- Recall
- F1 Score
- RMSE
- MAE
- Confusion Matrix
- SSIM
- SHAP

### Engineering
- Model serialization
- Custom TensorFlow layers
- SQLite integration
- API integration
- Batch inference
- Report generation
- Git/GitHub project organization

---

## Portfolio Highlights

### Classical Machine Learning

The portfolio includes both statistical and machine-learning approaches, from multiple linear regression and VIF analysis to XGBoost and Random Forest.

### Deep Learning

Multiple neural-network architectures are implemented and compared, including CNNs, RNNs, LSTMs, GRUs, and GANs.

### Generative AI

The portfolio includes several practical GenAI applications rather than only model-training notebooks:

- Code Review Buddy
- Healthcare RAG
- NutraWiseAI

### Medical AI

Healthcare-oriented work spans multiple AI paradigms:

- Healthcare NLP
- Healthcare RAG
- Supplement-information LLM application
- MRI image translation using CycleGAN

### Model Interpretability

The projects also include techniques for understanding model behavior:

- SHAP
- Feature importance
- VIF
- Residual diagnostics
- Confusion matrices
- Threshold analysis

---

## Repository Structure

```
AI-ML-Projects/
│
├── Bike_Sharing_Assignment/
│
├── CNN Waste Segregation/
│
├── Code Review Buddy/
│
├── Entities Identification NLP Healthcare Data/
│
├── Fake News Detection Semantic Classification/
│
├── Healthcare AI RAG System/
│
├── LENDING CASE STUDY/
│
├── NutraWiseAI Chatbot LLM Project/
│
├── RNN Stock Price Prediction/
│
├── Style Transfer using GAN/
│
├── Telecom Churn Case Study/
│
└── README.md
```

Each project contains its own notebook/source files, supporting artifacts, and a dedicated README describing the implementation.

---

## How to Use This Repository

### Clone the Repository

```bash
git clone https://github.com/SaumyDhoLu/AI-ML-Projects.git
cd AI-ML-Projects
```

### Explore Individual Projects

Each project directory contains its own documentation.

Start with the project-specific README.md to understand:

- Problem statement
- Dataset
- Methodology
- Model architecture
- Results
- Technologies
- Limitations
- How to run the project

---

## Project Philosophy

The goal of this repository is not simply to collect model-training notebooks.

The projects demonstrate the progression from:

```
Data
  ↓
Understanding
  ↓
Preprocessing
  ↓
Feature Engineering
  ↓
Modelling
  ↓
Evaluation
  ↓
Interpretation
  ↓
Application
```

The portfolio also emphasizes understanding model limitations and distinguishing between:

- Experimental results
- Validation performance
- External evaluation
- Qualitative observations
- Model-derived associations
- Production-ready evidence

This distinction is particularly important for healthcare, financial, and predictive applications.

---

## Areas of Interest

The portfolio reflects a broad interest in:

- Machine Learning
- Deep Learning
- Generative AI
- Large Language Models
- Retrieval-Augmented Generation
- Computer Vision
- NLP
- Healthcare AI
- AI-powered applications
- Model Explainability
- Applied AI Engineering

---

## Author

**Saumy DhoLu**

AI/ML | Generative AI | Deep Learning | Applied AI

GitHub: [https://github.com/SaumyDhoLu](https://github.com/SaumyDhoLu)
