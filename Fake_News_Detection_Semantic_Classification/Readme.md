# Fake News Detection using Word2Vec and Machine Learning

## Project Overview

This project develops a Natural Language Processing (NLP) pipeline for classifying news articles as **true** or **fake** using semantic text representations and supervised machine learning.

The project combines text preprocessing, linguistic feature extraction, exploratory data analysis, pretrained Word2Vec embeddings, and multiple classification algorithms.

Three machine learning models are evaluated:

- Logistic Regression
- Decision Tree
- Random Forest

The best validation result among the evaluated models was achieved by **Logistic Regression**, with an F1-score of **92.35%** and accuracy of **92.68%**.

---

## Problem Statement

The project investigates whether machine learning models can distinguish between true and fake news articles based on patterns in their textual content.

The task is formulated as binary classification:

```
0 → Fake News
1 → True News
```

The complete workflow is:

```
True.csv + Fake.csv
        |
        v
Data Preparation
        |
        v
Text Cleaning
        |
        v
POS Tagging + Lemmatization
        |
        v
Exploratory Data Analysis
        |
        v
Word2Vec Embeddings
        |
        v
Supervised Classification
        |
        v
Model Evaluation
```

---

## Objectives

- Combine true and fake news datasets into a single modeling dataset.
- Create binary labels for classification.
- Clean and normalize news article text.
- Apply POS tagging and noun-focused lemmatization.
- Explore linguistic differences between true and fake news.
- Generate word, unigram, bigram, and trigram frequency analyses.
- Represent articles using pretrained Word2Vec embeddings.
- Train multiple supervised classification models.
- Compare model performance using accuracy, precision, recall, and F1-score.
- Identify the best-performing model on the validation dataset.

---

## Dataset

The project uses two CSV files:

- `True.csv`
- `Fake.csv`

Each dataset contains:

- `title`
- `text`
- `date`

A `news_label` column is added during preprocessing.

### Labels

| Label | Meaning |
|---|---|
| 0 | Fake News |
| 1 | True News |

### Dataset Statistics

| Dataset | Articles |
|---|---|
| True News | 21,417 |
| Fake News | 23,523 |
| Combined | 44,940 |
| After removing missing title/text | 44,919 |

The final modeling dataset contains 44,919 articles.

---

## Data Preparation

The true and fake datasets are concatenated into a single DataFrame.

Rows with missing title or text values are removed because these fields provide the primary textual information used for classification.

The date column is retained during the initial data preparation but is not used as a modeling feature.

After preprocessing the dataset contains:

- 44,919 articles

---

## Text Preprocessing

The raw article title and body are combined into a single `news_text` field.

The cleaning pipeline performs the following operations:

- Convert text to lowercase.
- Remove text inside square brackets.
- Remove punctuation.
- Remove words containing numbers.
- Normalize extra whitespace.

Example:

```
Raw Article
    ↓
Lowercase
    ↓
Remove [bracketed text]
    ↓
Remove punctuation
    ↓
Remove words containing numbers
    ↓
Normalized text
```

---

## POS Tagging and Lemmatization

After cleaning, the project applies NLTK tokenization and Part-of-Speech (POS) tagging.

Only the following noun tags are retained:

- `NN` — singular noun
- `NNS` — plural noun

English stopwords are removed, and the remaining nouns are lemmatized using WordNet.

For example, related word forms can be reduced to a common base form.

The resulting text is stored in:

```
lemmatized_text
```

This representation is subsequently used for Word2Vec feature extraction.

---

## Exploratory Data Analysis

EDA is performed on the training dataset to investigate differences between the two classes.

### Text Length Analysis

Character lengths are calculated for:

- cleaned news text
- lemmatized news text

Histograms are used to compare their distributions.

This provides insight into how linguistic preprocessing changes the size and structure of the articles.

### Word Frequency Analysis

The project generates word clouds showing frequently occurring terms in:

- true news
- fake news

The analysis also identifies the most frequent words separately for both classes.

### N-gram Analysis

The project calculates frequent:

- Unigrams
- Bigrams
- Trigrams

for true and fake news separately.

This helps identify recurring lexical patterns and multi-word expressions within the dataset.

The observed patterns are dataset-specific and should not be interpreted as universal indicators of whether a news article is factually true or false.

---

## Train-Validation Split

The final dataset is divided using a stratified 70/30 split:

```python
train_test_split(
    test_size=0.3,
    random_state=9,
    stratify=df_clean['news_label']
)
```

### Split Statistics

| Dataset | Samples |
|---|---|
| Training | 31,437 |
| Validation | 13,473 |

The stratified split maintains the class distribution between training and validation data.

---

## Feature Extraction with Word2Vec

To convert the textual data into numerical representations, the project uses the pretrained:

- Google News Word2Vec 300-dimensional model

The pretrained model contains approximately:

- 3,000,000 word vectors

Each word is represented as a 300-dimensional vector.

### Document Representation

Because machine learning classifiers require fixed-size numerical inputs, the project creates one vector for each article by averaging the Word2Vec vectors of the words present in the article.

Conceptually:

```
Article
   |
   +-- word1 → 300D vector
   +-- word2 → 300D vector
   +-- word3 → 300D vector
   +-- ...
   |
   v
Mean of word vectors
   |
   v
300-dimensional article representation
```

Words not present in the pretrained vocabulary are ignored. If no known words are available, a zero vector is returned.

### Resulting Feature Matrices

```
Training:   (31,437, 300)
Validation: (13,473, 300)
```

---

## Model Training

Three supervised machine learning algorithms are trained using the 300-dimensional Word2Vec document representations.

### 1. Logistic Regression

Configuration:

```python
LogisticRegression(
    max_iter=1000,
    random_state=9
)
```

### 2. Decision Tree

Configuration:

```python
DecisionTreeClassifier(
    random_state=9
)
```

### 3. Random Forest

Configuration:

```python
RandomForestClassifier(
    n_estimators=100,
    random_state=9
)
```

---

## Model Evaluation

The models are evaluated on the held-out validation set using:

- Accuracy
- Precision
- Recall
- F1-score

F1-score is particularly useful for comparing the balance between precision and recall.

---

## Results

### Model Comparison

| Model | Accuracy | Precision | Recall | F1-Score |
|---|---|---|---|---|
| Logistic Regression | 92.68% | 92.08% | 92.62% | 92.35% |
| Decision Tree | 84.84% | 85.08% | 82.72% | 83.89% |
| Random Forest | 92.36% | 93.06% | 90.74% | 91.88% |

For the reported validation split, Logistic Regression produced the highest accuracy and F1-score among the three evaluated models.

Random Forest achieved slightly higher precision, while Logistic Regression achieved higher recall and F1-score.

### Best Validation Result

The strongest reported validation result was obtained using:

```
Word2Vec embeddings
        +
Logistic Regression
```

Performance:

| Metric | Score |
|---|---|
| Accuracy | 92.68% |
| Precision | 92.08% |
| Recall | 92.62% |
| F1-Score | 92.35% |

The validation set contained 13,473 articles.

---

## Model Interpretation

The results demonstrate that averaged Word2Vec representations can provide useful semantic features for binary classification on this dataset.

Interestingly, the simpler Logistic Regression model performed slightly better than the Decision Tree and Random Forest models when using the same 300-dimensional document representations.

This suggests that the embedding space contains information that can be separated effectively using a linear classifier for this particular dataset and validation split.

This observation is specific to the evaluated experiment and does not establish that Logistic Regression will outperform the other models on different datasets.

---

## Project Architecture

```
                    +----------------+
                    |   True.csv     |
                    +-------+--------+
                            |
                    +-------v--------+
                    |                |
                    | Data           |
                    | Preparation    |
                    |                |
                    +-------+--------+
                            |
                    +-------v--------+
                    |                |
                    | Text Cleaning  |
                    | POS + Lemma    |
                    |                |
                    +-------+--------+
                            |
                    +-------v--------+
                    |                |
                    |     EDA        |
                    |                |
                    +-------+--------+
                            |
                    +-------v--------+
                    |                |
                    |   Word2Vec     |
                    |   Embeddings   |
                    |                |
                    +-------+--------+
                            |
              +-------------+-------------+
              |             |             |
              v             v             v
        Logistic       Decision       Random
       Regression        Tree         Forest
              |             |             |
              +-------------+-------------+
                            |
                    +-------v--------+
                    |   Validation   |
                    |   Evaluation   |
                    +----------------+
```

---

## Technologies Used

**Programming**
- Python
- Jupyter Notebook

**NLP**
- NLTK
- spaCy
- WordNet
- POS Tagging
- Lemmatization
- Word2Vec

**Machine Learning**
- Scikit-learn
- Logistic Regression
- Decision Tree
- Random Forest

**Data Processing**
- Pandas
- NumPy

**Visualization**
- Matplotlib
- Seaborn
- WordCloud
- Plotly

**Embeddings**
- Google News pretrained Word2Vec
- 300-dimensional word vectors

---

## Project Structure

```
Fake News Detection/
│
└── Fake_News_Detection_Saumy_DhoLu.ipynb
```

The notebook also generates intermediate visualizations and analytical outputs during execution.

---

## How to Run

### 1. Install Dependencies

```bash
pip install numpy pandas matplotlib seaborn nltk spacy wordcloud scikit-learn gensim plotly
```

### 2. Install Required NLTK Resources

The notebook downloads the required resources programmatically, including:

```python
nltk.download('punkt')
nltk.download('averaged_perceptron_tagger')
nltk.download('wordnet')
nltk.download('stopwords')
```

### 3. Install the spaCy English Model

```bash
python -m spacy download en_core_web_sm
```

### 4. Prepare the Dataset

Place:

- `True.csv`
- `Fake.csv`

in the notebook's working directory.

### 5. Run the Notebook

Open:

```
Fake_News_Detection_Saumy_DhoLu.ipynb
```

and execute the notebook from top to bottom.

The Word2Vec Google News model is downloaded through gensim.downloader during execution.

---

## Key Skills Demonstrated

### NLP
- Text normalization
- Tokenization
- POS tagging
- Stopword removal
- Lemmatization
- N-gram analysis
- Word embeddings

### Machine Learning
- Binary classification
- Train-validation splitting
- Logistic Regression
- Decision Trees
- Random Forest
- Model comparison
- Classification metrics

### Feature Engineering
- Combining article title and body
- Linguistic preprocessing
- Semantic document representation
- Averaged Word2Vec embeddings

### Exploratory Data Analysis
- Text-length analysis
- Word frequency analysis
- Word clouds
- Unigrams
- Bigrams
- Trigrams

---

## Limitations

This project evaluates classification performance on the supplied labeled dataset and should not be interpreted as a general-purpose fact-checking system.

Important limitations include:

- The model learns patterns present in the training dataset rather than independently verifying claims.
- The validation set comes from the same overall dataset as the training data.
- Word2Vec document representations are created by averaging word vectors, which removes word order and some contextual information.
- Only noun POS tags (NN, NNS) are retained during the main lemmatization stage.
- The evaluation uses a single train-validation split.
- No external or temporally separated dataset is used to evaluate generalization.
- No claim-level fact verification or source credibility analysis is performed.

Therefore, the reported validation metrics should be interpreted as performance on this specific experimental setup rather than as evidence of real-world fact-checking accuracy.

---

## Future Improvements

Potential extensions include:

- Compare Word2Vec against TF-IDF and n-gram baselines.
- Use TF-IDF + Logistic Regression as a classical NLP baseline.
- Test pretrained contextual embeddings.
- Experiment with BERT-style transformer models.
- Preserve richer linguistic information instead of retaining only nouns.
- Tune classifier hyperparameters using cross-validation.
- Evaluate using a completely independent test dataset.
- Perform temporal validation to evaluate robustness against changing news topics.
- Add confusion matrices and error analysis.
- Investigate misclassified articles and identify recurring failure patterns.
- Incorporate source-level and metadata features.
- Develop claim-level fact verification rather than text-only classification.

---

## Project Takeaways

This project demonstrates an end-to-end NLP classification workflow:

```
Raw News Data
    ↓
Cleaning
    ↓
POS Tagging
    ↓
Lemmatization
    ↓
EDA
    ↓
Word2Vec
    ↓
Machine Learning
    ↓
Model Comparison
    ↓
Validation
```

The experiment shows that pretrained Word2Vec embeddings combined with conventional supervised machine learning can provide a strong baseline for binary news-text classification.

Among the three evaluated models, Logistic Regression achieved the highest validation F1-score of 92.35%.

---

## Academic Context

This project was developed as part of postgraduate Machine Learning and AI coursework and focuses on semantic text classification using pretrained word embeddings and supervised machine learning.

---

## Disclaimer

This project is intended for educational and portfolio purposes.

The classifier predicts whether an article belongs to the True or Fake class based on patterns learned from the supplied dataset. It does not independently verify the factual accuracy of claims contained in an article and should not be treated as a replacement for professional fact-checking or source verification.

---

## Author

**Saumy DhoLu**

Machine Learning & AI Portfolio

GitHub:(https://github.com/SaumyDhoLu)
