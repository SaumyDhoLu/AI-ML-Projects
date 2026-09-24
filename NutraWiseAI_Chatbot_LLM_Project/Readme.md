# NutraWiseAI: Personalized Nutraceutical Assistant

## Project Overview

NutraWiseAI is a Generative AI application designed to provide personalized, safety-oriented information about nutraceuticals and supplements.

The application combines:

- Google Gemini 2.5 Flash
- User health profiles
- Short-term conversation memory
- A local SQLite supplement knowledge base
- Optional PubMed literature search
- Prompt-based safety and interaction guidance

The goal is to demonstrate how an LLM can be combined with structured user information, external scientific literature, and application-level logic to create a domain-focused AI assistant.

This is an educational AI application and is not intended to provide medical diagnosis or treatment recommendations.

---

## Problem Statement

People often have questions about supplements, including:

- What is a supplement commonly used for?
- What are potential side effects?
- Could a supplement interact with a medication?
- What research exists on a particular supplement?
- Is a supplement relevant to a particular health goal?

A general-purpose LLM may not have the user's health context or a direct connection to current scientific literature.

NutraWiseAI explores a more structured approach by combining:

```
User Health Profile
        +
Conversation Context
        +
Supplement Knowledge
        +
Optional PubMed Evidence
        +
Gemini LLM
        |
        v
Personalized Response
```

---

## System Architecture

```
                         +------------------+
                         |       User       |
                         +--------+---------+
                                  |
                                  v
                       +---------------------+
                       | Health Profile Setup |
                       +----------+----------+
                                  |
                    +-------------+-------------+
                    |                           |
                    v                           v
          +-------------------+       +-------------------+
          | Conversation      |       | PubMed Scientific |
          | History            |       | Evidence Search   |
          +---------+---------+       +---------+---------+
                    |                           |
                    +-------------+-------------+
                                  |
                                  v
                     +--------------------------+
                     | Prompt Construction      |
                     |                          |
                     | Profile + History +      |
                     | Evidence + User Query    |
                     +------------+-------------+
                                  |
                                  v
                       +-----------------------+
                       | Gemini 2.5 Flash      |
                       +-----------+-----------+
                                   |
                                   v
                          Personalized Answer
```

A local SQLite database is also initialized to store structured supplement information.

---

## Key Features

- Personalized health profile creation
- Supplement-focused conversational AI
- Gemini 2.5 Flash integration
- Short-term conversation memory
- Local SQLite supplement database
- Structured supplement metadata
- Optional PubMed research search
- PubMed article metadata and links
- Safety-focused prompt instructions
- Medication-interaction awareness
- Allergy and health-condition context
- Interactive command-line interface
- API-key handling through environment/Colab secrets

---

## 1. User Health Profile

NutraWiseAI provides an interactive profile setup process.

The profile is represented using a Python dataclass:

```
UserHealthProfile
```

The profile stores:

- Age
- Gender
- Health Conditions
- Medications
- Health Goals
- Allergies
- Activity Level

### Health Conditions

The application provides predefined options including:

- Type 1 Diabetes
- Type 2 Diabetes
- Hypertension
- Heart Disease
- Asthma
- Arthritis
- High Cholesterol
- Depression/Anxiety
- Obesity
- Thyroid Disorders
- Osteoporosis

A custom "Other" option is also available.

### Medications

Predefined medication categories include:

- Blood pressure medication
- Diabetes medication
- Cholesterol medication
- Pain relievers
- Antidepressants
- Asthma inhalers
- Blood thinners
- Thyroid medication
- Birth control pills
- Vitamins/supplements

### Health Goals

Users can select goals such as:

- Weight loss
- Weight gain
- Muscle building
- Cardiovascular health
- Diabetes management
- Blood pressure management
- Stress reduction
- Sleep improvement
- Energy
- Immune support

### Allergies

The profile can capture common allergies including:

- Peanuts
- Tree nuts
- Shellfish
- Fish
- Dairy
- Eggs
- Soy
- Wheat/gluten
- Sesame
- Food additives

---

## 2. Structured Supplement Database

The application creates a local SQLite database:

```
advanced_nutriwise.db
```

with a `supplements` table.

### Database Schema

- id
- name
- category
- benefits
- dosage_range
- contraindications
- interactions
- side_effects
- best_time
- notes
- vegan_friendly
- evidence_level

### Included Supplements

The initial database contains 10 supplement records:

| Supplement | Category | Evidence Level |
|---|---|---|
| Vitamin D3 | Vitamin | Strong |
| Magnesium Glycinate | Mineral | Strong |
| Omega-3 (EPA/DHA) | Fatty Acid | Strong |
| Probiotics | Beneficial Bacteria | Moderate |
| Ashwagandha | Adaptogenic Herb | Moderate |
| B-Complex | Vitamin Complex | Strong |
| Iron (Ferrous Bisglycinate) | Mineral | Strong |
| Zinc (Picolinate) | Mineral | Strong |
| Turmeric/Curcumin | Anti-inflammatory Herb | Moderate |
| CoQ10 (Ubiquinol) | Antioxidant | Moderate |

The database also stores information such as contraindications, potential interactions, side effects, recommended timing, and notes.

### Implementation Note

The current notebook initializes and populates the SQLite database, but the response-generation method does not currently execute database queries to retrieve supplement records for the LLM prompt.

Therefore, the database should be viewed as a structured application data layer and an extension point for future retrieval-based functionality rather than as an active retrieval component in the current response pipeline.

---

## 3. Gemini LLM Integration

The core conversational model is:

```
Gemini 2.5 Flash
```

The application initializes the model through Google's Generative AI Python SDK.

```python
genai.GenerativeModel(
    'gemini-2.5-flash'
)
```

The model receives a dynamically constructed prompt containing relevant application context.

---

## 4. Prompt Engineering

The system prompt defines NutraWiseAI as a nutraceutical information assistant.

The prompt instructs the model to:

- provide safe and evidence-oriented information
- prioritize user safety
- highlight potential interactions
- recommend consultation with healthcare professionals
- use concise explanations
- use bullet points where appropriate

The final prompt combines:

```
System Instructions
        +
User Health Profile
        +
Recent Conversation History
        +
Optional Scientific Evidence
        +
Current User Message
```

This creates a more context-aware interaction than sending only the user's latest message to the LLM.

---

## 5. Personalized Responses

If a user profile exists, its information is added to the model prompt.

For example, the model can receive information about:

- Age
- Health conditions
- Medications
- Allergies
- Health goals
- Activity level

This allows the LLM to take the provided context into account when generating an answer.

The application therefore demonstrates a basic form of profile-conditioned LLM prompting.

---

## 6. Conversation Memory

The application maintains an in-memory conversation history:

```
self.conversation_history
```

For each completed interaction, the system stores:

- User message
- AI response

When generating a new response, the last four conversation turns are included in the prompt.

```
Current Query
     +
Previous 4 Turns
     |
     v
Gemini
```

This provides short-term conversational continuity.

The memory is session-based and is not implemented as a persistent user conversation database.

---

## 7. PubMed Scientific Evidence Search

NutraWiseAI integrates with the NCBI PubMed E-utilities API.

The `ScientificEvidenceSearcher` class performs the search.

### Retrieval Workflow

```
User Message
     |
     v
Check for research-related keywords
     |
     v
PubMed ESearch
     |
     v
PMIDs
     |
     v
PubMed ESummary
     |
     v
Article Metadata
     |
     v
Prompt Context
     |
     v
Gemini
```

### Trigger Keywords

The current implementation activates the PubMed search when the user message contains:

- research
- study
- evidence
- pubmed

### Default Retrieval

The search method returns up to:

- 3 articles

for a query.

Each returned article contains:

- PMID
- Title
- Authors
- Journal
- Publication date
- PubMed URL

---

## 8. Scientific Evidence in the Prompt

When PubMed results are available, the article information is appended to the Gemini prompt.

The context has the general form:

```
RECENT SCIENTIFIC EVIDENCE:

- Title: ...
  Link: https://pubmed.ncbi.nlm.nih.gov/...
```

This gives the LLM direct access to the titles and links of relevant PubMed articles.

The current implementation does not download and pass the full abstracts or article text into the Gemini context.

Therefore, this component is best described as PubMed-assisted evidence retrieval rather than a full document-grounded RAG pipeline.

---

## 9. Interactive CLI

The application runs as an interactive command-line chatbot.

On launch, the user can optionally create a health profile.

The assistant then accepts natural-language questions such as:

```
What supplements can help with sleep?

Check interactions between ashwagandha and my medications.

Show me research on Vitamin D for immune support.
```

Special commands include:

- profile
- quit
- exit
- bye

---

## 10. Safety-Oriented Design

Because the application deals with health and supplement information, the prompt explicitly emphasizes safety.

The assistant is instructed to:

- highlight potential interactions
- prioritize safety
- recommend professional medical advice
- provide concise explanations

The application also displays a disclaimer when the chatbot starts:

```
I am an AI assistant. Always consult a healthcare professional for medical advice.
```

This is an application-level safety measure rather than a substitute for clinical validation.

---

## API Key Handling

The notebook is designed primarily for Google Colab.

API credentials are retrieved using Colab Secrets:

```python
userdata.get('GOOGLE_API_KEY')
userdata.get('PubMed_API_KEY')
```

The PubMed API key is optional.

Without a PubMed API key, the application can still attempt PubMed searches using the basic API rate limits.

The Gemini API key is required for the chatbot to operate.

---

## Technologies Used

**Generative AI**
- Google Gemini
- Gemini 2.5 Flash
- Prompt engineering
- Context-aware LLM generation

**NLP / AI Application Development**
- Natural-language conversational interface
- Profile-conditioned prompting
- Conversation memory
- External evidence retrieval

**Scientific Search**
- NCBI PubMed E-utilities
- PubMed ESearch
- PubMed ESummary

**Data Storage**
- SQLite
- Structured supplement metadata

**Programming**
- Python
- Dataclasses
- Object-oriented programming
- Requests
- Logging

---

## Project Structure

```
NutraWiseAI/
│
├── NutraWiseAI_Project_Code.ipynb
│
├── saved_models
│     ├──AMZN_bilstm1_model.h5
│     ├──AMZN_bilstm1_model.keras
│     ├──GOOGL_bilstm1_model.h5
│     ├──GOOGL_bilstm1_model.keras
│     ├── IBM_bilstm1_model.h5
│     ├── IBM_bilstm1_model.keras
│     ├──  MSFT_bilstm1_model.h5
│     ├──  MSFT_bilstm1_model.keras
│     ├──  multi_output_5d_bilstm_model.keras
│     └──  multioutput_bilstm.keras
│
└── README.md
```

The SQLite database is generated by the application when it is initialized.

API credentials should be supplied through environment variables or platform secrets and should not be committed to a public repository.

---

## How to Run

### 1. Install Dependencies

```bash
pip install google-generativeai requests pandas
```

If running in Google Colab, the notebook is designed to use the Colab Secrets system.

### 2. Configure Gemini API Key

Add the following secret:

```
GOOGLE_API_KEY
```

### 3. Configure PubMed API Key

Optionally add:

```
PubMed_API_KEY
```

A PubMed API key is recommended for higher API usage limits.

### 4. Run the Notebook

Open:

```
NutraWiseAI_Project_Code.ipynb
```

and execute the notebook sequentially.

The notebook will:

- Install/import dependencies.
- Load API credentials.
- Configure logging.
- Create the health-profile data structure.
- Initialize the interactive profile system.
- Initialize PubMed search.
- Create the SQLite supplement database.
- Initialize Gemini.
- Launch the interactive chatbot.

---

## End-to-End Workflow

```
                User
                 |
                 v
        Optional Health Profile
                 |
                 v
          Natural Language Query
                 |
        +--------+--------+
        |                 |
        v                 v
 Conversation       PubMed Search
   History          if requested
        |                 |
        +--------+--------+
                 |
                 v
          Prompt Construction
                 |
                 v
          Gemini 2.5 Flash
                 |
                 v
       Safety-Oriented Response
                 |
                 v
       Conversation History
```

---

## Key Skills Demonstrated

### Generative AI
- LLM API integration
- Gemini model integration
- Prompt engineering
- Context injection
- Conversational AI
- Domain-specific AI assistant design

### Application Development
- Object-oriented Python
- Dataclasses
- Interactive CLI design
- Error handling
- Logging
- Modular component design

### Data and Knowledge Integration
- SQLite
- Structured supplement metadata
- External API integration
- PubMed search
- Scientific literature metadata

### Personalization
- Structured user profiles
- Health-condition context
- Medication context
- Allergy context
- Goal-based context
- Activity-level context
- Short-term conversational memory

---

## Project Highlights

- Built a domain-specific LLM assistant for nutraceutical questions.
- Integrated Gemini 2.5 Flash for conversational generation.
- Implemented structured health-profile collection.
- Captured health conditions, medications, allergies, goals, and activity level.
- Created a local SQLite database containing 10 supplement records.
- Stored structured information covering benefits, dosage ranges, contraindications, interactions, side effects, timing, and evidence level.
- Integrated PubMed's E-utilities API for optional scientific literature searches.
- Retrieves up to 3 PubMed articles for research-oriented queries.
- Maintains the most recent 4 conversation turns as prompt context.
- Added safety-oriented instructions and medical disclaimers.

---

## Limitations

This project should be considered an educational Generative AI application rather than a clinical decision-support system.

### 1. LLM-generated information

Gemini generates the final response and may produce incorrect or incomplete information.

### 2. PubMed retrieval is limited

The current implementation retrieves article metadata, primarily titles and links, rather than retrieving and grounding the response in full article abstracts or full text.

### 3. PubMed search is keyword-triggered

Scientific retrieval is activated only when the user's message contains one of the configured keywords:

- research
- study
- evidence
- pubmed

A question that does not contain these terms does not automatically trigger PubMed retrieval.

### 4. SQLite data is not currently used in response retrieval

The supplement database is initialized and populated, but the current `generate_ai_response()` method does not query the database before constructing the Gemini prompt.

A future implementation could retrieve relevant supplement records and explicitly ground responses in that structured information.

### 5. Short-term memory only

Only the last four conversation turns are included in the prompt. The conversation history is stored in memory and is lost when the application session ends.

### 6. No clinical validation

The system has not been clinically validated and should not be used for diagnosis, treatment selection, or medication changes.

### 7. Static supplement knowledge

The initial supplement database is manually defined in the application code. It is not automatically synchronized with a continuously updated scientific or regulatory database.

---

## Future Improvements

Potential extensions include:

- Implement actual SQLite retrieval during response generation.
- Build a proper supplement retrieval layer.
- Retrieve PubMed abstracts instead of only metadata.
- Introduce semantic search over scientific literature.
- Convert the system into a full RAG architecture.
- Add citation-aware generation.
- Add source-quality scoring.
- Use structured supplement evidence instead of relying primarily on LLM knowledge.
- Add persistent user profiles.
- Replace the CLI with a Streamlit or web interface.
- Add conversation persistence.
- Add supplement interaction checking using a structured knowledge base.
- Add dosage validation and unit normalization.
- Add contraindication matching against the user profile.
- Add automated evaluation datasets and response-quality metrics.
- Add guardrails for high-risk medical questions.
- Add source freshness and evidence-level tracking.

---

## Project Evolution

NutraWiseAI provides a foundation for a broader healthcare GenAI system.

The current architecture can be extended from:

```
LLM + Profile + PubMed Metadata
```

toward:

```
User Profile
      +
Structured Supplement Database
      +
PubMed Literature
      +
Semantic Retrieval
      +
Evidence Ranking
      +
LLM
      |
      v
Evidence-Grounded Personalized Assistant
```

This provides a clear path from a conversational LLM prototype toward a more robust retrieval-augmented healthcare application.

---

## Academic Context

This project was developed as part of postgraduate Machine Learning and AI coursework and explores the use of Generative AI for personalized nutraceutical information.

The project combines LLM integration, structured user profiling, local data storage, external scientific literature retrieval, and conversational prompting.

---

## Disclaimer

NutraWiseAI is an educational AI project.

The application does not provide medical diagnosis or treatment and should not be used to make decisions about medications, supplements, dosage, or medical conditions.

Users should consult a qualified healthcare professional before making healthcare-related decisions.

---

## Author

**Saumy DhoLu**

Machine Learning & AI

GitHub:(https://github.com/SaumyDhoLu)
