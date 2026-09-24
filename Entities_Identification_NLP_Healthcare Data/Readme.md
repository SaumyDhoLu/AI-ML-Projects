# Healthcare NLP: Disease and Treatment Entity Identification

## Project Overview

This project applies Natural Language Processing (NLP) to healthcare text to identify **disease** and **treatment** entities from medical sentences.

The task is formulated as a token-level sequence classification problem using three labels:

- `D` — Disease
- `T` — Treatment
- `O` — Other

A Conditional Random Field (CRF) model is trained using lexical, linguistic, and contextual token features. The predicted entities are then processed to create a structured disease-treatment mapping that can be queried using disease names.

The project demonstrates an end-to-end NLP workflow covering data preprocessing, feature engineering, sequence modeling, evaluation, entity extraction, and structured output generation.

---

## Problem Statement

Healthcare documents contain valuable information about diseases, treatments, procedures, and clinical observations, but this information is commonly embedded in unstructured text.

The objective of this project is to automatically identify disease and treatment mentions from medical text and convert the extracted information into a more structured representation.

The overall workflow is:

```text
Raw Token-Level Healthcare Data
            |
            v
Sentence Reconstruction
            |
            v
Feature Engineering
            |
            v
CRF Sequence Classification
            |
            v
Disease / Treatment Entity Extraction
            |
            v
Disease-Treatment Mapping
            |
            v
Query-Based Treatment Lookup
```

## Objectives

- Reconstruct complete sentences from token-level input files.
- Process corresponding token-level entity labels.
- Explore common concepts in the healthcare corpus.
- Engineer token-level and contextual features for sequence classification.
- Train a Conditional Random Field model for disease/treatment identification.
- Evaluate the model on a held-out test dataset.
- Extract predicted disease and treatment entities.
- Build a structured disease-treatment mapping.
- Support approximate disease-name matching for treatment lookup.

---

## Dataset

The notebook expects the healthcare dataset in four files:

- `train_sent`
- `train_label`
- `test_sent`
- `test_label`

The source data is stored one token per line, with blank lines separating sentences. The corresponding label files follow the same structure.

### Dataset Statistics

| Dataset | Sentences | Tokens |
|---|---|---|
| Training | 2,599 | 45,902 |
| Testing | 1,056 | 18,618 |

The labels are aligned token-by-token with the corresponding sentences.

---

## Data Preprocessing

The raw dataset stores individual tokens on separate lines rather than complete sentences.

A preprocessing function reconstructs the sentences by:

- Reading the token and label files simultaneously.
- Pairing each token with its corresponding label.
- Detecting blank lines as sentence boundaries.
- Grouping tokens into complete sentences.
- Maintaining the corresponding sequence of labels.

This produces:

```python
sentences = [
    [token1, token2, token3, ...],
    [token1, token2, token3, ...],
    ...
]

labels = [
    [label1, label2, label3, ...],
    [label1, label2, label3, ...],
    ...
]
```

A consistency check is also performed to verify that the number of extracted features and labels matches for every sentence.

---

## Exploratory NLP Analysis

The project uses spaCy to investigate common concepts in the healthcare corpus.

Tokens tagged as nouns or proper nouns are counted to identify frequently occurring concepts.

Examples from the corpus include:

| Concept | Frequency |
|---|---|
| patients | 507 |
| treatment | 304 |
| cancer | 211 |
| therapy | 177 |
| study | 174 |
| disease | 149 |
| cell | 142 |
| lung | 118 |
| chemotherapy | 91 |
| gene | 91 |
| surgery | 73 |

This provides an initial understanding of the vocabulary and terminology present in the medical dataset.

---

## Feature Engineering

The CRF model operates on token-level feature dictionaries.

For each token, the project extracts features including:

### Token Features

- Original token
- Lowercase token
- Prefix
- Suffix
- Word shape
- Capitalization
- Uppercase indicator
- Digit indicator
- Bias feature

### Context Features

Features from neighboring tokens are also included:

- Previous word
- Previous POS/tag information
- Previous word shape
- Next word
- Next POS/tag information
- Next word shape

### Sentence Boundary Features

- Beginning of sentence (BOS)
- End of sentence (EOS)

These features allow the CRF to use both the characteristics of an individual token and the surrounding context when predicting its entity label.

---

## Model

### Conditional Random Field

The project uses a Conditional Random Field for sequence labeling through sklearn-crfsuite.

Configuration used in the notebook:

```python
crf = sklearn_crfsuite.CRF(
    algorithm='lbfgs',
    c1=0.1,
    c2=0.1,
    max_iterations=100,
    all_possible_transitions=True
)
```

### Why CRF?

A CRF is suitable for token-level sequence labeling because predictions are made while considering relationships between neighboring labels.

For example, the model can use surrounding words and predicted label transitions when determining whether a token belongs to a disease, treatment, or neither category.

---

## Model Evaluation

The trained CRF model is evaluated against the manually provided labels in the test dataset.

### Overall Test Performance

| Metric | Score |
|---|---|
| Accuracy | 92.97% |
| Weighted F1 | 92.58% |
| Macro F1 | 78.25% |

### Class-Level Performance

| Entity Class | Precision | Recall | F1 Score | Support |
|---|---|---|---|---|
| Disease (D) | 0.8129 | 0.6621 | 0.7298 | 1,450 |
| Other (O) | 0.9451 | 0.9771 | 0.9608 | 16,127 |
| Treatment (T) | 0.7762 | 0.5696 | 0.6571 | 1,041 |

The results show that the model performs particularly well on the dominant O class, while disease and treatment identification remains a more challenging part of the task.

For this reason, both weighted and class-specific metrics are reported rather than relying only on overall accuracy.

---

## Disease and Treatment Extraction

After generating predictions for the test sentences, the predicted D and T labels are converted into readable entity strings.

The project then constructs a dictionary with the general structure:

```
Disease -> Treatment(s)
```

The resulting output is exported as a CSV file containing:

```
Disease,Treatments
```

The generated output contains:

- 852 unique disease entries
- 688 unique treatment strings

Example entries include:

```
Acoustic Neuroma -> Stereotactic Radiosurgery
Acromegaly -> Long-Acting Octreotide (Sandostatin-Lar)
Viral Infections -> Interferon
Viremia -> Combination Therapy
Xerostomia -> Amifostine; Irradiation Therapy
```

The exact associations are derived from the predicted entities in the test corpus and should therefore be treated as an NLP extraction result rather than clinically validated treatment recommendations.

---

## Approximate Disease Matching

A query function is implemented to search the extracted disease dictionary.

The matching process uses:

- Case-insensitive comparison.
- Substring matching.
- Reverse substring matching.
- Shared-word matching when an exact/substring match is not found.

For example, the notebook queries:

```
hereditary retinoblastoma
```

and finds the related disease entry:

```
Retinoblastoma
```

with the extracted treatment:

```
Radiotherapy
```

This demonstrates how terminology variations can be handled when the exact query phrase is not present in the extracted dictionary.

---

## Output Artifacts

The project produces a structured disease-treatment output and a visual representation of the extracted mapping.

### CSV Output

```
disease_treatment_output.csv
```

Contains:

- Disease
- Treatments

### Visual Output

```
disease_treatment_table.png
```

The image presents the extracted disease-treatment mapping as a tabular output.

---

## Technologies Used

- Python
- Pandas
- spaCy
- scikit-learn
- sklearn-crfsuite
- CRF
- Matplotlib
- Natural Language Processing
- Sequence Classification
- Named Entity Recognition

---

## Project Structure

```
Entities Identification NLP Healthcare Data/
│
├── Assignment_NLP_Saumy_DhoLu.ipynb
│
└── results/
    ├── disease_treatment_output.csv
    └── disease_treatment_table.png
```

The exact dataset files should be present in the repository if the notebook is expected to run without modification.

---

## How to Run

### 1. Install Dependencies

```bash
pip install pandas matplotlib spacy scikit-learn sklearn-crfsuite pycrf
```

Install the spaCy English model:

```bash
python -m spacy download en_core_web_sm
```

### 2. Place the Dataset Files

Ensure the following files are available in the notebook's working directory:

- `train_sent`
- `train_label`
- `test_sent`
- `test_label`

### 3. Open the Notebook

```
Assignment_NLP_Saumy_DhoLu.ipynb
```

Run the notebook from top to bottom to:

- preprocess the data
- construct feature sequences
- train the CRF
- generate test predictions
- calculate evaluation metrics
- extract disease and treatment entities
- generate the disease-treatment mapping
- perform a sample disease lookup

---

## Key Skills Demonstrated

### Natural Language Processing
- Healthcare text processing
- Token-level classification
- Named Entity Recognition
- Part-of-speech analysis
- Linguistic feature engineering

### Machine Learning
- Conditional Random Fields
- Sequence modeling
- Regularization
- Model evaluation
- Classification metrics

### Data Processing
- Sentence reconstruction
- Token-label alignment
- Structured data generation
- CSV output generation

### Applied NLP
- Disease entity extraction
- Treatment entity extraction
- Disease-treatment mapping
- Approximate entity matching

---

## Project Highlights

- Processed 45,902 training tokens and 18,618 test tokens.
- Built a custom CRF-based healthcare entity extraction pipeline.
- Achieved 92.58% weighted F1 and 92.97% accuracy on the test token classification task.
- Reported separate disease and treatment class metrics rather than relying only on aggregate performance.
- Generated a structured mapping containing 852 extracted disease entries.
- Implemented approximate disease-name matching for downstream lookup.
- Produced both CSV and visual table outputs.

---

## Important Limitations

This project is an NLP/ML academic project and its outputs should not be interpreted as clinical guidance.

The disease-treatment mapping is generated from model predictions over the test corpus. It is not a medically validated knowledge base and should not be used to determine treatment for an individual patient.

The notebook also uses a relatively simple three-class labeling scheme (D, T, O) rather than a richer BIO/BILOU-style entity representation.

In addition, the disease-treatment association stage groups predicted diseases and treatments occurring within the same sentence. This is useful for demonstrating structured information extraction, but it does not establish a clinically validated relationship between every disease and treatment appearing together in a sentence.

---

## Future Improvements

Potential improvements include:

- Use BIO/BILOU entity tagging schemes.
- Improve spaCy linguistic feature extraction and validation.
- Add character-level features for medical terminology.
- Tune CRF hyperparameters using validation data.
- Perform entity-level rather than only token-level evaluation.
- Evaluate on an independent healthcare dataset.
- Improve disease-treatment relation extraction.
- Use domain-specific biomedical language models such as BioBERT or ClinicalBERT.
- Add normalization of medical entities to standardized terminology.
- Build a searchable interface for extracted entities.
- Add confidence or uncertainty information to predictions.
- Separate NLP extraction from medically validated knowledge sources.

---

## Academic Context

This project was developed as part of postgraduate Machine Learning and AI coursework and focuses on applying classical NLP and sequence modeling techniques to healthcare text.

The project demonstrates the progression from unstructured token-level medical data to machine-learning-based entity extraction and structured information retrieval.

---

## Disclaimer

This project is intended for educational and portfolio purposes.

The extracted disease-treatment relationships are model-generated NLP outputs and are not medical advice, diagnosis, or clinically validated treatment recommendations.

---

## Author

**Saumy DhoLu**

Machine Learning & AI Portfolio

GitHub: (https://github.com/SaumyDhoLu)

