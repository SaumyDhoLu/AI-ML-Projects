# Code Review Buddy

## AI-Powered Code Analysis and Review Assistant

Code Review Buddy is an AI-powered code analysis tool that combines **static code analysis with Large Language Model (LLM)-based review** to provide actionable feedback on software quality, security, performance, and maintainability.

The application uses **Python AST analysis** for structural inspection and **Google Gemini through LangChain** for contextual AI-powered analysis.

It supports analyzing individual files, ZIP archives, directories, and GitHub repositories and generates detailed reports in multiple formats.

---

## Overview

Traditional code review can require significant manual effort, especially when reviewing large numbers of files or identifying different categories of issues.

Code Review Buddy combines deterministic static analysis with AI-assisted analysis to provide a broader review workflow.

The system performs:

- Structural code analysis
- Cyclomatic complexity analysis
- Import analysis
- Function and class analysis
- Pattern detection
- Security analysis
- Performance analysis
- Code-quality analysis
- AI-generated recommendations
- Knowledge-base-grounded reviews
- Multi-format report generation

The goal is not to replace human code reviewers, but to provide an additional automated layer that can help identify potential issues and prioritize areas requiring attention.

---

## Key Features

### Static Code Analysis

For Python code, the system uses the Python Abstract Syntax Tree (AST) to analyze code structure.

The static analysis engine examines areas such as:

- Code complexity
- Functions
- Classes
- Imports
- Code structure
- Mutable default arguments
- Bare exception handling
- `eval()` usage
- Other detectable code patterns

---

### AI-Powered Code Review

The application uses Google Gemini through LangChain to perform contextual analysis.

Three specialized analysis areas are used:

#### Code Quality

Reviews code for:

- Coding standards
- Readability
- Maintainability
- Best practices
- Potential bugs
- Problematic patterns

#### Security

Looks for potential security concerns such as:

- Injection-related vulnerabilities
- Unsafe coding patterns
- Security risks
- Other issues identified through the configured security knowledge base

#### Performance

Examines code for:

- Inefficient patterns
- Potential algorithmic improvements
- Performance bottlenecks
- Optimization opportunities

---

## Knowledge-Grounded Analysis

Code Review Buddy uses an external `knowledge_base.txt` file to provide coding standards and review rules to the AI analysis layer.

The knowledge base includes categories such as:

- Clarity, Readability, and PEP 8
- Correctness and Logic
- Pythonic Code and Best Practices
- Security
- Performance
- Testing and Code Structure

This approach allows the review criteria to be maintained separately from the application logic and expanded with additional rules.

---

## Architecture

```text
┌─────────────────────────────────────────────────────┐
│                  USER INTERFACE                               │
│       File Upload + Analysis Options                          │
└──────────────────────┬──────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────┐
│                FILE HANDLER LAYER                             │
│  • Individual files                                           │
│  • ZIP archives                                               │
│  • GitHub repositories                                        │
│  • Directory scanning                                         │
└──────────────────────┬──────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────┐
│              STATIC ANALYSIS ENGINE                           │
│  • Python AST parsing                                         │
│  • Complexity calculation                                     │
│  • Structure analysis                                         │
│  • Pattern detection                                          │
└──────────────────────┬──────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────┐
│               AI ANALYSIS LAYER                               │
│              LangChain + Gemini                               │
│                                                               │
│  ┌───────────────┐ ┌───────────────┐ ┌───────────┐    │
│  │ Quality Agent    │ │ Security Agent. │ │Performance. │    │
│  │                  │ │                 │ │   Agent     │    │
│  └───────────────┘ └───────────────┘ └───────────┘    │
└──────────────────────┬──────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────┐
│          REPORT GENERATION & STORAGE                          │
│  • Executive summaries                                        │
│  • Detailed Markdown reports                                  │
│  • HTML dashboards                                            │
│  • JSON summaries                                             │
└─────────────────────────────────────────────────────┘
```

## Supported Input Methods

Code Review Buddy supports multiple ways of providing code for analysis.

1. **Individual Files**
   Provide one or more file paths for analysis.

2. **ZIP Archives**
   Upload a ZIP archive containing a project or collection of source files.

3. **GitHub Repositories**
   Provide a GitHub repository URL and allow the application to clone and analyze the repository.

4. **Directory Scanning**
   Provide a local directory for recursive code analysis.

---

## Supported Languages

### Full Static Analysis

**Python:**
- `.py`
- `.ipynb`

Python files can receive AST-based structural analysis in addition to AI analysis.

### Metrics + AI Analysis

The application also supports:

- JavaScript
- JSX
- TypeScript
- Java
- C
- C++
- C#
- PHP
- Ruby
- Go
- Rust

The depth of static analysis depends on the language.

---

## Report Generation

Code Review Buddy generates multiple types of output.

### HTML Dashboard

The application can display an interactive-style dashboard containing:

- Overall code-quality score
- Issue counts
- Severity breakdown
- Key findings
- Quick wins

### Markdown Reports

Detailed reports are generated for individual analysis categories.

Example:

```
code_review_reports/
├── filename_quality_TIMESTAMP.md
├── filename_security_TIMESTAMP.md
└── filename_performance_TIMESTAMP.md
```

### JSON Reports

Machine-readable JSON summaries contain structured information about:

- Metadata
- Files analyzed
- Lines of code
- Overall score
- Issues
- Severity
- Detailed analysis

This makes the output easier to consume programmatically.

---

## Analysis Workflow

A typical analysis follows this pipeline:

```
Input Code
    │
    ▼
File Detection & Validation
    │
    ▼
Language Identification
    │
    ├───────────────┐
    ▼               ▼
Static Analysis   AI Analysis
    │               │
    │          ┌────┼────┐
    │          ▼    ▼    ▼
    │       Quality Security Performance
    │          │    │    │
    └──────────┴────┴────┘
               │
               ▼
       Report Generation
               │
       ┌───────┼────────┐
       ▼       ▼        ▼
      HTML   Markdown   JSON
```

---

## Configuration

The application provides configurable limits and analysis thresholds.

Example configuration:

```python
class Config:

    MAX_FILE_SIZE = 2048 * 2048
    MAX_TOTAL_SIZE = 20 * 2048 * 2048

    MAX_LINE_LENGTH = 200
    MAX_COMPLEXITY = 20
    MAX_NESTING_DEPTH = 8
```

These values control:

- Maximum individual file size
- Maximum total input size
- Maximum line length
- Complexity threshold
- Nesting-depth threshold

---

## AI Configuration

The project uses Google's Gemini model through LangChain.

The current notebook configures:

```python
llm = ChatGoogleGenerativeAI(
    model="gemini-2.5-pro",
    google_api_key=GEMINI_API_KEY,
    temperature=0.1,
    max_output_tokens=4096,
    timeout=60
)
```

The low temperature is intended to make review responses more consistent and deterministic.

---

## Setup

### Requirements

- Python 3.8+
- Google Gemini API key
- Git
- Internet connection for Gemini API calls and GitHub repository cloning

### Installation

Clone the repository containing the project and navigate to the project directory.

```bash
git clone <repository-url>
cd Code_Review_Buddy
```

Install the required packages:

```bash
pip install langchain langchain-google-genai gitpython pygments tabulate colorama pandas
```

### API Key Configuration

Create a local file named:

```
GEMINI_API_KEY.txt
```

and place your Gemini API key inside it.

Example:

```
your-gemini-api-key
```

### Security Warning

Never commit your API key to GitHub.

Add the following to `.gitignore`:

```
GEMINI_API_KEY.txt
```

The application reads the key locally when the notebook starts.

### Knowledge Base Configuration

The application expects:

```
knowledge_base.txt
```

The knowledge base contains the rules used to ground the AI code review.

A rule follows a structure such as:

```
# Section: Category Name

Rule: Rule Name
Description: Detailed explanation of the rule
Example: Code example
Bad: Anti-pattern example
Good: Correct pattern example
```

The knowledge base can be extended with additional coding standards and review rules.

---

## Usage

### Quick Start

After initializing the application:

```python
report, results = main()
```

The application will:

- Initialize Code Review Buddy.
- Present the available input methods.
- Process the selected code.
- Perform static analysis where supported.
- Run AI-powered quality, security, and performance analysis.
- Generate reports.
- Return the report and analysis results.

### Programmatic Usage

The application can also be controlled directly:

```python
app = CodeReviewBuddy()

report, results = app.run_analysis()
```

Individual file analysis can then be accessed through:

```python
results["filename.py"]
```

### Additional Analysis Tools

**Medium-Detail Summaries**

```python
summaries = get_file_summaries(results, app)
```

Generates concise summaries containing:

- Important issues
- Recommendations
- Quick fixes
- Overall code-health assessment

**Security-Focused Review**

```python
security = get_security_focus(results)
```

Extracts security-related findings from the completed analysis.

**Quick Fixes**

```python
fixes = get_quick_fixes(results)
```

Extracts actionable improvements from the generated analysis.

**View Generated Reports**

```python
view_detailed_reports()
```

Lists available Markdown and JSON reports.

---

## Example Analysis Workflow

The project was tested using multiple sample code files, including examples covering:

- Web scraping
- Data processing
- User authentication

It also supports analyzing a ZIP archive containing multiple source files.

This demonstrates the ability to analyze both individual source files and grouped project inputs.

---

## Project Structure

```
Code_Review_Buddy/
│
├── Code_Review_Buddy.ipynb
├── knowledge_base.txt
├── README.md
│
├── code_review_reports/
│   ├── *.md
│   └── *.json
│
└── test_files/
    └── sample source files
```

### Main Components

| Component | Purpose |
|---|---|
| `Code_Review_Buddy.ipynb` | Main application and orchestration |
| `knowledge_base.txt` | Coding standards and review rules |
| `code_review_reports/` | Generated Markdown and JSON reports |
| `test_files/` | Sample code used for testing |

---

## Limitations

The current implementation has several practical limitations.

### API Dependency

AI-powered analysis requires:

- Internet access
- A valid Gemini API key
- Available API quota

### Privacy

Source code submitted for AI analysis is sent to the configured Gemini API.

Sensitive or proprietary code should therefore be reviewed according to the organization's privacy and data-handling requirements.

### File Size

The default configuration limits individual files to approximately 2 MB and total input size to approximately 40 MB.

### Long Files

Very large files may be truncated before being passed to the AI analysis layer because of context-length limitations.

### Python Static Analysis

AST-based structural analysis is primarily focused on Python.

Other supported languages receive the analysis supported by the corresponding processing pipeline rather than the same Python-specific AST checks.

---

## Future Improvements

Potential extensions for the project include:

- Full AST-based static analysis for additional programming languages.
- Result caching to avoid repeatedly analyzing unchanged files.
- GitHub Actions / CI integration.
- Custom rule management through a user interface.
- IDE integrations for VS Code and PyCharm.
- Improved automated testing.
- More sophisticated repository-level dependency analysis.
- Persistent analysis history and comparison between reviews.

---

## Technologies Used

**Programming**
- Python

**AI / LLM**
- Google Gemini
- LangChain

**Static Analysis**
- Python AST

**Supporting Libraries**
- Pandas
- Pygments
- Tabulate
- Colorama
- GitPython

**Output**
- HTML
- Markdown
- JSON

---

## Skills Demonstrated

This project demonstrates practical experience with:

- Generative AI application development
- LLM integration
- LangChain
- Prompt engineering
- AI agent-style architecture
- Knowledge-grounded AI analysis
- Static code analysis
- Python AST
- Software quality analysis
- Security analysis
- Performance analysis
- Structured report generation
- JSON-based outputs
- Repository/file processing
- API integration

---

## Why This Project Matters

Code Review Buddy demonstrates the combination of traditional software-engineering techniques and Generative AI rather than relying solely on an LLM.

The static-analysis layer provides deterministic code information, while the LLM layer adds contextual interpretation and recommendations.

This hybrid approach allows the project to demonstrate how AI can be integrated into an existing software-engineering workflow.

---

## Academic Context

This project was developed as part of a Build Your Own Project (BYOP) focused on applying Generative AI concepts to a practical software-engineering problem.

---

## Author

**Saumy DhoLu**

AI/ML | Generative AI | Machine Learning

GitHub: [https://github.com/SaumyDhoLu](https://github.com/SaumyDhoLu)
