# Semantic Spotter - Project
# BYOP - Code Review Buddy
# Author - Saumy DhoLu

An AI-powered code analysis tool that combines static analysis with intelligent LLM-based review using LangChain and Google's Gemini API. This tool provides comprehensive, actionable feedback on code quality, security vulnerabilities, and performance optimization opportunities.

![Python Version](https://img.shields.io/badge/python-3.8%2B-blue)
![License](https://img.shields.io/badge/license-Educational-green)
![Status](https://img.shields.io/badge/status-Active-success)

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
  - [Basic Usage](#basic-usage)
  - [Advanced Usage](#advanced-usage)
  - [Analysis Tools](#analysis-tools)
- [Project Structure](#project-structure)
- [Supported Languages](#supported-languages)
- [Output Formats](#output-formats)
- [Configuration Options](#configuration-options)
- [Knowledge Base](#knowledge-base)
- [Troubleshooting](#troubleshooting)
- [Limitations](#limitations)
- [Contributing](#contributing)
- [License](#license)

## Overview

Code Review Buddy addresses the challenge of maintaining code quality in fast-paced development environments. It combines:

- **Static Analysis**: Fast AST parsing, cyclomatic complexity calculation, and pattern detection
- **AI-Powered Review**: Three specialized agents (Quality, Security, Performance) using Google's Gemini API
- **Knowledge-Grounded Analysis**: Reviews based on established coding standards (PEP 8, OWASP guidelines)
- **Multiple Output Formats**: HTML dashboards, detailed markdown reports, and machine-readable JSON

### Why LangChain?

LangChain provides the ideal framework for this project because:

1. **Structured AI Integration**: PromptTemplates and Chains enable systematic, reproducible analysis
2. **Multiple Analysis Agents**: Clean separation of concerns (quality, security, performance)
3. **Knowledge Base Grounding**: External coding standards integrated through structured prompts
4. **Flexible Output Processing**: Structured parsing of AI responses into actionable formats

## Features

### Core Capabilities

-  **Deep Code Analysis**: AST parsing, structural analysis, complexity metrics
-  **Security Review**: OWASP-based vulnerability detection, injection flaw identification
-  **Performance Analysis**: Algorithmic complexity evaluation, optimization recommendations
-  **Quality Assessment**: PEP 8 compliance, best practices, maintainability scoring
-  **AI-Powered Insights**: Context-aware recommendations from Gemini API
-  **Multiple Report Formats**: HTML dashboards, markdown reports, JSON summaries
-  **Medium-Detail Summaries**: Quick insights without information overload

### File Upload Methods

1. **Individual Files**: Direct file path input
2. **ZIP Archives**: Batch processing of compressed files
3. **GitHub Repositories**: Clone and analyze entire repos
4. **Directory Scanning**: Recursive analysis of project folders

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                  USER INTERFACE                     │
│          (File Upload + Analysis Options)           │
└─────────────────┬───────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────┐
│              FILE HANDLER LAYER                     │
│  • Process uploads  • Validate files                │
│  • Extract archives • Scan directories              │
└─────────────────┬───────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────┐
│          STATIC ANALYSIS ENGINE                     │
│  • AST Parsing (Python)                             │
│  • Complexity Calculation                           │
│  • Pattern Detection                                │
└─────────────────┬───────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────┐
│           AI ANALYSIS LAYER                         │
│          (LangChain + Gemini)                       │
│  ┌────────────────────────────────────┐             │
│  │  Quality Agent  (PEP 8, practices) │             │
│  │  Security Agent (OWASP, vulns)     │             │
│  │  Performance Agent (optimization)   │            │
│  └────────────────────────────────────┘             │
└─────────────────┬───────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────┐
│        REPORT GENERATION & STORAGE                  │
│  • Executive summaries  • Detailed reports          │
│  • HTML dashboards     • JSON exports               │
└─────────────────────────────────────────────────────┘
```

## Prerequisites

- **Python**: 3.8 or higher
- **Gemini API Key**: Get one from [Google AI Studio](https://makersuite.google.com/app/apikey)
- **Git**: Required for cloning GitHub repositories (optional)
- **Internet Connection**: For API calls and GitHub cloning

## Installation

### Step 1: Clone or Download

```bash
git clone <your-repository-url>
cd code-review-buddy
```

### Step 2: Install Dependencies

```bash
pip install langchain langchain-google-genai gitpython pygments tabulate colorama pandas
```

**Required Packages:**
- `langchain` - AI orchestration framework
- `langchain-google-genai` - Gemini API integration
- `gitpython` - Git repository handling
- `pygments` - Syntax highlighting
- `tabulate` - Table formatting
- `colorama` - Terminal colors
- `pandas` - Data handling and timestamps

### Step 3: Verify Installation

```python
import langchain
import langchain_google_genai
print(" All packages installed successfully!")
```

## Configuration

### API Key Setup

Create a file named `GEMINI_API_KEY.txt` in the project directory:

```bash
echo "your-actual-gemini-api-key-here" > GEMINI_API_KEY.txt
```

 **Important**: Add `GEMINI_API_KEY.txt` to your `.gitignore`:

```bash
echo "GEMINI_API_KEY.txt" >> .gitignore
```

### Knowledge Base Setup

Ensure `knowledge_base.txt` exists in the project directory with your coding standards. The file should follow this format:

```
# Section: Category Name

Rule: Rule Name
Description: Detailed explanation of the rule
Example: Code example (optional)
Bad: Anti-pattern example (optional)
Good: Correct pattern example (optional)
```

### Configuration Options

Edit the `Config` class in the notebook to customize:

```python
class Config:
    # File size limits
    MAX_FILE_SIZE = 2048 * 2048      # 2MB per file
    MAX_TOTAL_SIZE = 20 * 2048 * 2048  # 40MB total
    
    # Analysis thresholds
    MAX_LINE_LENGTH = 200
    MAX_COMPLEXITY = 20
    MAX_NESTING_DEPTH = 8
```

## Usage

### Basic Usage

**Option 1 - Quick Start (Recommended for first-time users):**

```python
report, results = main()
```

This single command will:
1. Display a welcome screen
2. Prompt you to choose upload method
3. Run comprehensive analysis
4. Generate all reports
5. Display results in multiple formats

**Option 2 - Step-by-Step Control:**

```python
# Initialize the application
app = CodeReviewBuddy()

# Run analysis
report, results = app.run_analysis()
```

### Upload Methods

When prompted, choose from:

1. **Individual Files**: Enter file paths one per line
   ```
   /path/to/your/script.py
   /path/to/another/file.py
   ```

2. **ZIP Archive**: Provide path to ZIP file
   ```
   /path/to/your/code.zip
   ```

3. **GitHub Repository**: Enter repository URL
   ```
   https://github.com/username/repository
   ```

4. **Directory Scan**: Provide directory path
   ```
   /path/to/your/project
   ```

### Analysis Tools

After running the main analysis, use these helper functions:

#### Get Medium-Detail Summaries

```python
summaries = get_file_summaries(results, app)
```

Generates 300-500 word summaries highlighting critical issues without overwhelming detail.

#### Focus on Security

```python
security_report = get_security_focus(results)
```

Extracts all security-related findings across analyzed files.

#### Get Quick Fixes

```python
fixes = get_quick_fixes(results)
```

Lists low-effort, high-value improvements you can implement immediately.

#### View Detailed Reports

```python
view_detailed_reports()
```

Lists all generated markdown and JSON files with access instructions.

#### Access Individual File Data

```python
# Get specific file analysis
file_analysis = results['your_file.py']

# Access specific analysis types
print(file_analysis['quality_analysis'])
print(file_analysis['security_analysis'])
print(file_analysis['performance_analysis'])
```

## Project Structure

```
code-review-buddy/
│
├── Code_Review_Buddy.ipynb    # Main application notebook
├── GEMINI_API_KEY.txt         # Your API key (don't commit!)
├── knowledge_base.txt         # Coding standards database
├── README.md                  # This file
│
├── code_review_reports/       # Generated reports (auto-created)
│   ├── analysis_summary_*.json
│   ├── *_quality_*.md
│   ├── *_security_*.md
│   └── *_performance_*.md
│
└── test_files/                # Sample files for testing (optional)
```

## Supported Languages

### Full Analysis (AST + AI + Metrics)
- **Python** (.py, .ipynb)

### Metrics + AI Analysis
- JavaScript (.js, .jsx)
- TypeScript (.ts)
- Java (.java)
- C++ (.cpp, .c)
- C# (.cs)
- PHP (.php)
- Ruby (.rb)
- Go (.go)
- Rust (.rs)

## Output Formats

### 1. HTML Dashboard

Interactive dashboard displayed in the notebook with:
- Overall quality score (0-100)
- Issue counts by severity
- Key findings summary
- Quick wins section

### 2. Markdown Reports (Detailed)

Generated in `code_review_reports/` directory:

- `FILENAME_quality_TIMESTAMP.md` - Complete quality analysis
- `FILENAME_security_TIMESTAMP.md` - Security vulnerability analysis
- `FILENAME_performance_TIMESTAMP.md` - Performance optimization suggestions

### 3. JSON Summary

Machine-readable report with complete analysis data:

```json
{
  "metadata": {
    "generated_at": "2025-09-30 00:18:07",
    "total_files_analyzed": 3,
    "total_lines_of_code": 255
  },
  "executive_summary": {
    "overall_score": 90,
    "total_issues_found": 1,
    "severity_breakdown": {...}
  },
  "files": {...}
}
```

### Understanding Results

#### Quality Score Scale
- **80-100**: Excellent - production ready
- **60-79**: Good - minor improvements needed
- **40-59**: Needs Work - multiple issues
- **0-39**: Critical - serious problems

#### Issue Severity Levels
- **Critical**: Security vulnerabilities, data loss risks
- **High**: Bugs, major performance issues
- **Medium**: Code quality, maintainability
- **Low**: Style, minor improvements

## Configuration Options

### Modify Analysis Thresholds

```python
class Config:
    MAX_LINE_LENGTH = 200        # Flag lines longer than this
    MAX_COMPLEXITY = 20          # Flag functions exceeding complexity
    MAX_NESTING_DEPTH = 8        # Flag deeply nested code
```

### Customize File Filters

```python
SUPPORTED_EXTENSIONS = {
    '.py': 'python',
    '.js': 'javascript',
    # Add more extensions as needed
}
```

### Adjust AI Parameters

```python
llm = ChatGoogleGenerativeAI(
    model="gemini-2.5-pro",
    temperature=0.1,              # Lower = more deterministic
    max_output_tokens=4096,       # Maximum response length
    timeout=60                    # API timeout in seconds
)
```

## Knowledge Base

The `knowledge_base.txt` file contains coding standards used to ground AI analysis. You can customize it to match your team's standards.

### Adding New Rules

```
# Section: Your Category

Rule: Your Rule Name
Description: Detailed explanation
Example: def good_example(): pass
Bad: def bad_example(): ...
Good: def better_example(): ...
Exception: When this rule doesn't apply
```

### Default Categories

- **Clarity, Readability, and PEP 8**
- **Correctness and Logic**
- **Pythonic Code and Best Practices**
- **Security**
- **Performance**
- **Testing and Code Structure**

## Troubleshooting

### Common Issues

**"API key file not found"**
```bash
# Solution: Create the API key file
echo "your-api-key" > GEMINI_API_KEY.txt
```

**"Analysis failed after X attempts"**
- Check internet connection
- Verify API key is valid and has credits
- Try with a smaller file first
- Check Gemini API status

**"No files to analyze"**
- Verify file extensions are supported
- Check file paths are correct
- Ensure files aren't in ignored directories

**Memory Issues**
- Reduce `MAX_TOTAL_SIZE` in Config
- Analyze files in smaller batches
- Close other memory-intensive applications

**Slow Analysis**
- Each file requires 3 API calls (quality, security, performance)
- Large files take longer to analyze
- Network speed affects GitHub cloning
- Consider analyzing fewer files at once

### Debug Mode

To enable verbose logging:

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## Limitations

1. **Python Focus**: Full static analysis only for Python; other languages get metrics + AI analysis
2. **API Dependent**: Requires internet and valid Gemini API key
3. **File Size Limits**: Files over 2MB are skipped by default
4. **Rate Limits**: Gemini API has usage quotas
5. **Context Length**: Very long files (>2000 lines) are truncated for AI analysis
6. **Privacy**: Code is sent to Gemini API for analysis

## Best Practices

### For Accurate Analysis

1. **Clean Code First**: Remove obvious syntax errors before analysis
2. **Meaningful Names**: Use descriptive file and variable names
3. **Modular Code**: Smaller, focused files get better analysis
4. **Comments**: Well-commented code helps AI understand intent
5. **Test Files**: Start with a single file before analyzing entire projects

### For Performance

1. **Batch Wisely**: Don't analyze too many files at once
2. **Use Summaries**: Get quick insights before diving into detailed reports
3. **Filter Files**: Exclude test files, generated code, and dependencies
4. **Cache Results**: Save JSON reports for future reference

### Security Considerations

1. **Sensitive Code**: Be cautious analyzing code with secrets or proprietary logic
2. **API Keys**: Never commit `GEMINI_API_KEY.txt` to version control
3. **Public Repos**: Be aware when analyzing public repositories
4. **Review Reports**: Check reports before sharing with others

## Contributing

This project is for educational purposes. If you'd like to contribute:

### Areas for Improvement

1. **Language Support**: Add full AST analysis for JavaScript, Java, etc.
2. **Caching**: Implement caching to avoid re-analyzing unchanged files
3. **CI/CD Integration**: Create GitHub Actions workflow
4. **Custom Rules**: Build UI for managing knowledge base rules
5. **IDE Plugins**: Develop plugins for VS Code, PyCharm, etc.

### Development Setup

```bash
# Clone repository
git clone <your-repo>
cd code-review-buddy

# Install development dependencies
pip install -r requirements-dev.txt

# Run tests
pytest tests/
```

## License

This project is for educational purposes as part of AI/ML coursework. Use responsibly and at your own discretion.

## Acknowledgments

Built with:
- **LangChain** - AI application framework
- **Google Gemini** - Large language model
- **Python AST** - Abstract syntax tree parsing
- Various open-source libraries

## Support

For issues, questions, or suggestions:
1. Check the [Troubleshooting](#troubleshooting) section
2. Review Gemini API documentation at https://docs.gemini.com
3. Consult LangChain docs at https://docs.langchain.com

---

**Happy Code Reviewing!**

*Remember: This tool assists review, it doesn't replace human judgment. Always verify critical findings yourself.*

