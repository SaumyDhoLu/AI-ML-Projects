# Healthcare AI RAG System

## Project Overview

This project implements an end-to-end **Retrieval-Augmented Generation (RAG) system for healthcare research**.

The system retrieves relevant medical research papers from a large PubMed-based dataset, re-ranks the retrieved evidence, and uses Google Gemini to generate a grounded response.

Instead of asking a Large Language Model to answer a healthcare question entirely from its pretrained knowledge, the system follows a retrieval-first architecture:

```
User Query
    |
    v
Medical Text Preprocessing
    |
    v
PubMedBERT Embedding
    |
    v
FAISS Vector Search
    |
    v
Cross-Encoder Re-ranking
    |
    v
Relevant Research Papers
    |
    v
Gemini 2.5 Pro
    |
    v
Evidence-Grounded Response
```

The project demonstrates practical implementation of semantic retrieval, vector databases, re-ranking, prompt engineering, source grounding, and LLM-based response generation.

---

## Problem Statement

Medical research is distributed across a large volume of scientific literature. Finding relevant evidence for a specific clinical question can require searching through many documents and manually synthesizing the results.

The goal of this project is to build a system that can:

- understand a healthcare question,
- retrieve relevant medical literature,
- rank the retrieved evidence,
- provide the retrieved research as context to an LLM,
- generate a structured response,
- cite the retrieved sources,
- acknowledge limitations when the available evidence does not fully answer the query.

The system is designed as an educational RAG implementation and is not intended to replace clinical decision-making.

---

## Key Features

- Medical-domain text preprocessing
- Medical abbreviation expansion
- Multiple chunking strategies
- Sentence-boundary-aware semantic chunking
- PubMedBERT-based document embeddings
- 768-dimensional vector representations
- FAISS vector similarity search
- Cross-encoder re-ranking
- Query caching
- Gemini 2.5 Pro generation
- Evidence-grounded prompting
- Source/reference generation
- Interactive medical question answering
- Structured evaluation across three queries
- Screenshot-ready retrieval and generation outputs

---

## Dataset

The system uses a dataset of 60,380 medical research papers.

Each paper contains information such as:

- PMID
- Title
- Abstract
- Authors
- Journal
- Publication date
- URL
- Full text
- Word count
- Medical condition
- Medical condition category

### Dataset Statistics

| Property | Value |
|---|---|
| Research papers | 60,380 |
| Medical categories | 22 |
| Average abstract length | ~262 words |
| Missing author values | 202 |
| Missing publication dates | 325 |

The publication dates in the loaded dataset range from March 2019 to June 2026.

---

## System Architecture

The project is organized into three primary layers.

### 1. Embedding Layer

Responsible for converting medical research text into numerical vector representations.

Components:

- Medical text preprocessing
- Medical abbreviation expansion
- Document chunking
- PubMedBERT embeddings

### 2. Search Layer

Responsible for finding and ranking relevant research.

Components:

- FAISS vector index
- Cosine-similarity retrieval
- Cross-encoder re-ranking
- Query caching

### 3. Generation Layer

Responsible for converting retrieved research into a readable response.

Components:

- Gemini 2.5 Pro
- Evidence-grounded prompt
- Source references
- Medical safety disclaimer

---

## 1. Medical Text Preprocessing

Before embedding the research papers, the project performs domain-specific text preprocessing.

A custom `MedicalTextProcessor` handles:

- whitespace normalization
- special-character normalization
- medical abbreviation expansion
- title processing
- abstract processing
- full-text processing

### Medical Abbreviation Expansion

The system contains a curated dictionary for common medical abbreviations.

Examples include:

```
MI     → myocardial infarction
DM     → diabetes mellitus
T2DM   → type 2 diabetes mellitus
HTN    → hypertension
CHF    → congestive heart failure
COPD   → chronic obstructive pulmonary disease
CAD    → coronary artery disease
CVD    → cardiovascular disease
HbA1c  → hemoglobin A1c
BP     → blood pressure
HR     → heart rate
BMI    → body mass index
ICU    → intensive care unit
ER     → emergency room
IV     → intravenous
SSRI   → selective serotonin reuptake inhibitor
```

The original abbreviation is retained alongside the expanded form to improve matching during retrieval.

---

## 2. Searchable Document Construction

The project combines processed:

```
Title + Abstract
```

into a `searchable_content` field.

This provides a compact representation of each research paper for the retrieval pipeline while retaining the original full-text information separately.

---

## 3. Chunking Strategy Comparison

Long research documents are divided into smaller chunks before embedding.

The project evaluates three chunking approaches on a sample of 1,000 papers.

### Comparison

| Strategy | Total Chunks | Avg. Characters | Avg. Words |
|---|---|---|---|
| Sentence-based | 6,675 | 310.6 | 44.5 |
| Fixed-length | 6,414 | 365.0 | 52.1 |
| Sentence-boundary-aware | 5,906 | 349.0 | 50.3 |

The selected strategy is the sentence-boundary-aware approach with a maximum chunk size.

It attempts to preserve complete sentence context while preventing chunks from becoming excessively large.

### Full Dataset

Applying the selected strategy to all 60,380 papers produced:

- 321,652 chunks

These chunks become the units used by the retrieval system.

---

## 4. Medical Embedding System

The project uses:

```
microsoft/BiomedNLP-PubMedBERT-base-uncased-abstract-fulltext
```

for medical-domain text representation.

PubMedBERT is based on a biomedical language model trained on medical/scientific text.

The notebook wraps the model for embedding generation using mean pooling.

### Embedding Dimensions

Each chunk is represented as:

- 768-dimensional vector

The complete embedding matrix contains:

```
321,652 × 768
```

Embeddings are normalized before being added to FAISS.

The generated embeddings are also saved locally as a NumPy file so they do not need to be regenerated every time the system is executed.

---

## 5. FAISS Vector Search

The search layer uses Facebook AI Similarity Search (FAISS).

The project creates:

```
faiss.IndexFlatIP
```

Because the document embeddings and query embeddings are normalized, inner-product search can be used as cosine-similarity retrieval.

### Retrieval Process

For each query:

```
User Query
    |
    v
Medical Text Processing
    |
    v
Query Embedding
    |
    v
FAISS Search
    |
    v
Top 20 Candidate Chunks
```

The initial retrieval stage returns up to 20 candidate chunks.

---

## 6. Cross-Encoder Re-ranking

The initial vector search is followed by a second-stage re-ranking process.

The project uses:

```
cross-encoder/ms-marco-MiniLM-L12-v2
```

The cross-encoder receives pairs consisting of:

```
(query, retrieved document chunk)
```

and generates a relevance score.

The candidates are sorted using these scores and the final top 5 results are retained.

```
FAISS
  |
  | Top 20
  v
Cross-Encoder
  |
  | Re-ranked
  v
Top 5 Evidence Chunks
```

This two-stage architecture separates:

- fast broad retrieval
- more detailed relevance scoring

---

## 7. Query Caching

The search layer maintains an in-memory query cache.

The cache key includes:

- query
- initial_k
- final_k

Repeated searches with the same parameters can therefore reuse previously retrieved results instead of performing the complete retrieval and re-ranking process again.

---

## 8. Medical Response Generation

After retrieval and re-ranking, the top research papers are passed to:

- Gemini 2.5 Pro

The generation layer uses a structured prompt containing:

- user query
- retrieved research
- paper title
- abstract
- journal
- evidence-grounding instructions
- citation requirements
- uncertainty instructions
- medical disclaimer

### Evidence-Grounded Prompting

The generation prompt instructs the model to:

- Synthesize the retrieved research.
- Base the answer primarily on the supplied evidence.
- Explicitly reference the research papers used.
- Acknowledge limitations in the evidence.
- Explain complex medical terminology clearly.
- Avoid inventing information that is not present in the retrieved context.
- Include an educational-use medical disclaimer.

This is intended to reduce unsupported generation and keep the answer connected to the retrieved literature.

---

## 9. Complete RAG Pipeline

The final system orchestrates all components through:

```
HealthcareRAGSystem
```

The complete execution flow is:

```
                    User Query
                        |
                        v
              Medical Text Processing
                        |
                        v
                  Query Embedding
                        |
                        v
                   FAISS Search
                        |
                        v
                Top 20 Candidates
                        |
                        v
              Cross-Encoder Ranking
                        |
                        v
                  Top 5 Results
                        |
                        v
               Evidence Construction
                        |
                        v
                 Gemini 2.5 Pro
                        |
                        v
              Generated Answer
                        |
                        v
              References + Disclaimer
```

---

## 10. Evaluation

The system was evaluated using three healthcare questions.

### Query 1
What are the latest treatment guidelines for type 2 diabetes management in primary care?

### Query 2
How should major depressive disorder be treated in elderly patients with multiple comorbidities?

### Query 3
What are the current evidence-based protocols for acute myocardial infarction management in emergency departments?

Each query retrieves five final documents and generates an LLM response.

### Evaluation Results

The results are recorded in:

```
evaluation_summary.csv
```

| Query | Documents | Avg. Similarity | Avg. Re-rank Score | Response Length | Processing Time |
|---|---|---|---|---|---|
| Type 2 diabetes | 5 | 0.9772 | 4.0049 | 3,466 chars | 17.88 sec |
| Depression in elderly | 5 | 0.9782 | 1.0378 | 4,974 chars | 25.26 sec |
| Acute myocardial infarction | 5 | 0.9822 | -1.8284 | 3,446 chars | 18.35 sec |

### Average Processing Time

Across the three evaluation queries:

- ~20.5 seconds per query

The reported similarity and re-ranking scores are retrieval-system measurements. They are not accuracy or medical correctness percentages.

---

## 11. Example Evaluation Output

The project generates screenshot-ready outputs showing both the retrieved research and the generated answer.

The results directory contains outputs such as:

```
results/
│
├── Query1_Diabetes_SearchResults.png
├── Query1_Diabetes_GeneratedAnswer.png
├── ...
└── evaluation_summary.csv
```

The screenshot outputs cover the three evaluation queries and show:

- retrieved research papers
- similarity/re-ranking scores
- research excerpts
- medical categories
- generated responses
- references

These provide visual evidence of the complete RAG pipeline operating from query to final response.

---

## 12. Interactive Querying

The notebook also implements an interactive command-line session.

Users can enter medical questions and receive RAG-generated responses.

Conceptually:

```
Your Query:
    |
    v
Healthcare RAG System
    |
    v
Retrieved Research
    |
    v
Generated Response
```

The session can be terminated with:

```
quit
```

---

## Technologies Used

**Programming**
- Python
- Jupyter Notebook

**Data Processing**
- Pandas
- NumPy

**NLP / Embeddings**
- Sentence Transformers
- PubMedBERT
- Medical text preprocessing

**Vector Search**
- FAISS

**Re-ranking**
- Cross-Encoder
- cross-encoder/ms-marco-MiniLM-L12-v2

**Generative AI**
- Google Gemini API
- Gemini 2.5 Pro
- Prompt engineering
- Retrieval-Augmented Generation

**Visualization / Notebook Tools**
- Matplotlib
- Seaborn
- IPyWidgets
- tqdm

---

## Project Structure

```
Healthcare AI RAG System/
│
├── Healthcare_AI_RAG_System_Final.ipynb
│
├── healthcare_papers_20250827_102302.csv
├── GEMINI_API_KEY.txt
│
├── healthcare_embeddings_pubmedbert_*.npy
│
├── results/
│   ├── Query1_Diabetes_SearchResults.png
│   ├── Query1_Diabetes_GeneratedAnswer.png
│   ├── ...
│   └── evaluation_summary.csv
│
└── README.md
```

`GEMINI_API_KEY.txt` should never be committed to GitHub. Store API credentials locally or through environment variables/secrets.

---

## How to Run

### 1. Install Dependencies

```bash
pip install pandas numpy sentence-transformers faiss-cpu \
google-generativeai scikit-learn matplotlib seaborn \
ipywidgets tqdm
```

For GPU-enabled FAISS or PyTorch configurations, installation may differ depending on the local environment.

### 2. Configure Gemini

Create a local file:

```
GEMINI_API_KEY.txt
```

and place the Gemini API key inside it.

Do not commit this file to a public repository.

### 3. Add the Healthcare Dataset

Place the medical research dataset expected by the notebook in the project directory:

```
healthcare_papers_20250827_102302.csv
```

### 4. Run the Notebook

Open:

```
Healthcare_AI_RAG_System_Final.ipynb
```

and execute the notebook sequentially.

The notebook will:

- Load the medical research dataset.
- Configure Gemini.
- Preprocess medical text.
- Compare chunking strategies.
- Create the selected chunks.
- Generate PubMedBERT embeddings.
- Build the FAISS index.
- Load the cross-encoder.
- Initialize the generation layer.
- Create the complete RAG system.
- Run the evaluation queries.
- Provide an interactive querying interface.

---

## Key Skills Demonstrated

### Generative AI
- Retrieval-Augmented Generation
- LLM integration
- Prompt engineering
- Evidence grounding
- Context construction
- Source citation

### NLP
- Medical text preprocessing
- Medical terminology normalization
- Abbreviation expansion
- Biomedical embeddings
- Document chunking

### Information Retrieval
- Vector similarity search
- FAISS
- Cosine similarity
- Cross-encoder re-ranking
- Two-stage retrieval

### Machine Learning Engineering
- Modular class-based architecture
- Embedding pipelines
- Vector indexing
- Query caching
- Evaluation logging
- Reusable retrieval and generation components

### Healthcare AI
- Biomedical literature retrieval
- Medical research synthesis
- Evidence-aware response generation
- Safety-oriented prompting

---

## Project Highlights

- Processed 60,380 medical research papers.
- Organized the dataset into 22 medical condition categories.
- Generated 321,652 searchable text chunks.
- Created 768-dimensional biomedical embeddings.
- Built a FAISS vector index containing 321,652 vectors.
- Implemented two-stage retrieval using FAISS + Cross-Encoder re-ranking.
- Integrated Gemini 2.5 Pro for response generation.
- Added medical abbreviation expansion.
- Added query caching.
- Evaluated the system using three healthcare questions.
- Recorded retrieval and response-generation performance in evaluation_summary.csv.
- Generated screenshot-ready evidence of retrieval and generated responses.

---

## Limitations

This project is an educational RAG implementation and should not be treated as a clinical decision-support system.

### 1. Retrieval evaluation

The evaluation records similarity and re-ranking scores, but it does not contain a manually labeled relevance benchmark.

Therefore, the reported similarity scores cannot be interpreted as retrieval accuracy.

### 2. Source duplication

Multiple chunks from the same research paper can appear among the retrieved results.

A future version could apply source-level deduplication or diversity-aware retrieval to ensure that the final context contains a broader range of research papers.

### 3. Generic re-ranker

The Cross-Encoder used for re-ranking is:

```
cross-encoder/ms-marco-MiniLM-L12-v2
```

It is a general information-retrieval model rather than a biomedical-specific re-ranker.

A medical-domain cross-encoder could potentially provide better domain-specific relevance scoring.

### 4. Embedding model configuration

PubMedBERT is used with mean pooling to generate fixed-size embeddings. The embedding model has not been fine-tuned specifically for this retrieval dataset.

### 5. LLM generation

The generated answer depends on both the retrieved context and the Gemini model. Retrieval grounding can reduce unsupported generation, but it does not guarantee factual or clinical correctness.

### 6. Dataset coverage

The system can only retrieve information represented in its indexed research collection.

### 7. Clinical validation

The system has not undergone clinical validation or evaluation by medical professionals.

---

## Future Improvements

Potential improvements include:

- Biomedical-specific cross-encoder re-ranking.
- Source-level deduplication.
- Diversity-aware retrieval.
- Hybrid BM25 + vector retrieval.
- Metadata-aware filtering by publication date, journal, or condition.
- Retrieval evaluation using human-labeled relevance judgments.
- Recall@K, Precision@K, MRR, and nDCG evaluation.
- Medical-domain embedding fine-tuning.
- Citation verification.
- Better reference extraction and source linking.
- Automatic detection of outdated evidence.
- Confidence and uncertainty estimation.
- Conversation memory with controlled context management.
- Web/API-based PubMed refresh pipeline.
- Structured medical evidence extraction.
- Human-in-the-loop clinical review.

---

## Why This Project Matters

This project demonstrates the architecture behind a practical RAG application rather than simply integrating an LLM API.

The system combines several important components:

```
Domain-Specific NLP
       +
Biomedical Embeddings
       +
Vector Database / FAISS
       +
Re-ranking
       +
LLM Generation
       +
Prompt Engineering
       +
Evaluation
```

This makes the project particularly relevant to roles involving:

- Generative AI
- RAG systems
- NLP
- LLM applications
- AI engineering
- Information retrieval
- Healthcare AI

---

## Academic Context

This project was developed as part of postgraduate Machine Learning and AI coursework under a Build Your Own Project (BYOP) healthcare AI initiative.

The project focuses on implementing a complete RAG pipeline using biomedical research literature and a generative AI model.

---

## Disclaimer

This project is intended for educational and portfolio purposes.

The generated responses are based on retrieved research and an LLM-generated synthesis. They are not medical advice, diagnosis, or treatment recommendations and should not replace evaluation by a qualified healthcare professional.

---

## Author

**Saumy DhoLu**

Machine Learning & AI

GitHub: (https://github.com/SaumyDhoLu)
