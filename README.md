# CV Converter - Documentation

	This repository is private due to contractual agreements with collaborating organizations.


**AI-Powered CV Template Conversion Tool**

Transform candidate CVs from any format into your standardized company template automatically. Process individual CVs or bulk convert up at once with consistent formatting and accuracy.

---

## Table of Contents

1. [Quick Start](#quick-start)
2. [Installation & Setup](#installation--setup)
3. [Testing](#testing)
4. [Configuration](#configuration)
5. [Template Guide](#template-guide)
6. [Code Documentation](#code-documentation)
7. [Customization](#customization)
8. [Troubleshooting](#troubleshooting)
9. [Product Overview](#product-overview)

---

## Quick Start

**Files Provided:**

- `cv_converter.py` - Main application
- `extraction.py` - AI extraction logic
- `utils.py` - Helper functions
- `requirements.txt` - Python dependencies (includes test tools)
- `runtime.txt` - Python version specification
- `.gitignore` - Git ignore rules
- `README.md` - Complete documentation (this file)
- `TESTING.md` - Test suite documentation
- `tests/` - Automated test suite (31 tests)
- `CV Template With Tokens.docx` - Template to upload in "Upload Company Template" field
- `Jane_Doe_Resume.docx` - Test Resume to upload in "Upload Candidate CV(s)" field

**Prerequisites:**

- Python 3.11
- Google Gemini API key
- Company CV template with tokens

---

## Installation & Setup

### 1. Install Python 3.11 (Ubuntu/WSL)

```bash
# Update package list
sudo apt update

# Install tools for adding extra repositories
sudo apt install software-properties-common -y

# Add the official "deadsnakes" repository for newer Python versions
sudo add-apt-repository ppa:deadsnakes/ppa -y

# Update again to see packages from new repository
sudo apt update

# Install Python 3.11 + venv module + dev headers
sudo apt install python3.11 python3.11-venv python3.11-dev -y

# Verify installation
python3.11 --version
```

### 2. Create and Activate Virtual Environment

```bash
# Create venv with Python 3.11
python3.11 -m venv venv

# Activate it [Note: always perform run or test commands inside the virtual environment]
source venv/bin/activate

# Verify Python version in venv
python --version  # Should show Python 3.11.x
```

### 3. Install Project Dependencies

```bash
# Upgrade pip
pip install --upgrade pip

# Install requirements
pip install -r requirements.txt
```

---

## Testing

This application includes a comprehensive test suite with **31 automated tests** ensuring code quality and reliability.

### Running Tests

**Prerequisite:** Install test dependencies (already in requirements.txt)

```bash
# Activate virtual environment first
source venv/bin/activate

# Install all dependencies including test tools
pip install -r requirements.txt
```

**Run all tests:**

```bash
pytest tests/ -v
```

**Run specific test category:**

```bash
pytest tests/test_formatting.py -v          # Formatting tests
pytest tests/test_extraction.py -v          # Data validation tests
pytest tests/test_template.py -v            # Template filling tests
pytest tests/test_critical_features.py -v   # Critical features
pytest tests/test_edge_cases.py -v          # Edge case handling
pytest tests/test_production_critical.py -v # Production scenarios
pytest tests/test_pipeline_integration.py -v # Full pipeline tests
```

**Quick test (no verbose output):**

```bash
pytest tests/ -q
```

### Test Coverage

- **31 tests total** with 100% pass rate
- Covers: Formatting, validation, template processing, edge cases, production scenarios
- See [TESTING.md](TESTING.md) for complete documentation

---

## Configuration

### Create Secrets File

```bash
# Create .streamlit directory
mkdir -p .streamlit

# Create secrets.toml
nano .streamlit/secrets.toml
```

**Add the following to `.streamlit/secrets.toml`:**

```toml
GEMINI_API_KEY = "your-api-key-here"
company_domain = "@yourcompany.com"
app_password = "YourSecurePassword123"
```

CTRL O; Enter; CTRL X to save and exit Nano editor.

**Configuration Options:**

| Key              | Description                             | Example           |
| ---------------- | --------------------------------------- | ----------------- |
| `GEMINI_API_KEY` | Google Gemini API key for AI extraction | `"AIzaSy..."`     |
| `company_domain` | Email domain for authentication         | `"@company.com"`  |
| `app_password`   | Shared password for team access         | `"SecurePass123"` |

### Run the Application

```bash
streamlit run cv_converter.py
```

Access the app at **http://localhost:8501**

Login with company email and upload your template to begin conversion.

---

## Template Guide

### Understanding Tokens

Your CV template uses **tokens** - placeholders wrapped in double curly braces like `{{CANDIDATE_NAME}}` that get replaced with actual data during conversion.

**How It Works:**

When someone uploads a CV, the system extracts information and fills tokens automatically:

| Token                | Becomes                     |
| -------------------- | --------------------------- |
| `{{CANDIDATE_NAME}}` | "Jane Doe"                  |
| `{{EXP1_COMPANY}}`   | "Formation Bio"             |
| `{{EXP1_RESP1}}`     | First responsibility bullet |

### Template Structure

**Current template supports:**

- **20 work experiences** (EXP1 through EXP20)
- **30 responsibilities per experience** (RESP1 through RESP30)
- **5 education entries** (EDU1 through EDU5)
- **10 certifications** (CERT1 through CERT10)

**Each experience includes:**

```
{{EXP1_ROLE}}        - Job title
{{EXP1_DURATION}}    - Date range
{{EXP1_COMPANY}}     - Company name
{{EXP1_LOCATION}}    - City, State
{{EXP1_RESP1}}       - First bullet point
{{EXP1_RESP2}}       - Second bullet point
...
{{EXP1_RESP30}}      - Last bullet point
```

### Modifying the Template

**Adding More Experiences:**

1. Copy an entire experience block (from `{{EXP1_ROLE}}` through all 30 responsibilities)
2. Increment the numbers (EXP21, EXP22, etc.)
3. Update `utils.py` - change `range(1, 21)` to `range(1, 22)` (or your new max)

**Adding More Responsibilities:**

1. Add `{{EXP1_RESP31}}` in your template
2. Update `utils.py` - change `range(1, 101)` to `range(1, 102)`

**Changing Formatting:**

Style the template however you want - the tokens will adopt whatever formatting surrounds them. Change fonts, colors, layouts, or add your company logo. Just keep the tokens in place.

### Automatic Cleanup

If a CV doesn't have enough data to fill all experiences, the system automatically removes empty sections. For example, if someone only has 8 jobs, experiences 9-20 and their bullet points disappear completely from the final document.

---

## Code Documentation

### System Architecture

The converter consists of three main modules:

```
cv_converter/
├── cv_converter.py      # Main app - authentication, UI, file handling
├── extraction.py        # AI extraction using Google Gemini
├── utils.py             # Helper functions for processing
├── tests/               # Automated test suite (31 tests)
│   ├── test_formatting.py
│   ├── test_extraction.py
│   ├── test_template.py
│   ├── test_critical_features.py
│   ├── test_edge_cases.py
│   ├── test_production_critical.py
│   └── test_pipeline_integration.py
└── .streamlit/
    └── secrets.toml     # Configuration file
```

### Module Breakdown

**1. utils.py - Helper Functions**

- Extracts text from PDF/DOCX files
- Formats dates consistently (converts "Sep-2015" to "SEP 2015")
- Fills the template with extracted data
- Removes empty sections automatically

**2. extraction.py - AI Processing**

- Uses Google Gemini AI to read CVs and extract structured data
- Identifies names, companies, roles, dates, responsibilities
- Handles tricky cases (multiple roles at one company, missing info)
- Returns everything in an organized format

**3. cv_converter.py - Main Application**

- Handles login/authentication
- Manages file uploads
- Coordinates the conversion process
- Generates downloadable files

### Adjusting Template Limits

In `utils.py`, locate the `fill_template()` function. Find and modify these ranges:

| Range           | Controls                     | Default |
| --------------- | ---------------------------- | ------- |
| `range(1, 21)`  | Max work experiences         | 20      |
| `range(1, 101)` | Max responsibilities per job | 100     |
| `range(1, 6)`   | Max education entries        | 5       |
| `range(1, 11)`  | Max certifications           | 10      |

**Example:** To support 25 experiences, change `range(1, 21)` to `range(1, 26)`.

---

## Customization

### Changing Fallback Text

In `utils.py`, find the `fill_template()` function and locate these lines:

```python
"Location Not Specified"      # Line ~235
"Dates Not Available"         # Line ~255
"Year Not Available"          # Line ~270
"Provider Not Specified"      # Line ~273
```

Change these to whatever text you prefer for missing data.

### Improving Extraction Quality

If the AI isn't extracting information correctly, edit the prompt in `extraction.py` (starting around line 15). You can:

- Add more examples for tricky CV formats
- Emphasize specific fields you need
- Adjust the extraction instructions
- Change AI model: modify `genai.GenerativeModel("gemini-flash-latest")` to `"gemini-pro"` for potentially better results

### Modifying Date Format

The system converts all dates to **"MMM YYYY"** format (e.g., "JAN 2023"). To change this, edit the `format_date()` function in `utils.py` (around line 45).

### Changing Authentication

To disable login or use different authentication, edit the `check_company_email()` function in `cv_converter.py` (around line 35).

### Session Timeout

Default session expires after **30 minutes**. To change this, find `check_session_timeout()` in `cv_converter.py` and modify:

```python
if elapsed.total_seconds() > 1800:  # Change 1800 to desired seconds
```

---

## Troubleshooting

### Login Issues

**Problem:** Cannot login or authentication fails

**Solutions:**

- ✓ Check that `.streamlit/secrets.toml` exists in the project folder
- ✓ Verify email domain format includes @ symbol (e.g., `"@company.com"`)
- ✓ Ensure `app_password` matches exactly (no extra spaces)
- ✓ Restart the Streamlit app after changing secrets

### Extraction Quality Issues

**Problem:** AI extracts incorrect or incomplete information

**Solutions:**

- ✓ Edit the AI prompt in `extraction.py` (line 15-200) to add more specific instructions
- ✓ Try a different AI model: `genai.GenerativeModel("gemini-pro")` for better accuracy
- ✓ Add examples of your specific CV format to the prompt
- ✓ Check that uploaded CVs are text-based (not scanned images)

### Template Not Filling Correctly

**Problem:** Tokens remain unfilled or data appears in wrong places

**Solutions:**

- ✓ Ensure token names in template EXACTLY match the code (case-sensitive)
- ✓ Check that curly braces are doubled: `{{TOKEN}}` not `{TOKEN}`
- ✓ Verify template is saved as `.docx` format (not `.doc`)
- ✓ Confirm no extra spaces inside tokens: `{{EXP1_ROLE}}` not `{{ EXP1_ROLE }}`

### Files Not Uploading

**Problem:** Cannot upload CV files

**Solutions:**

- ✓ PDF extraction works best with text-based PDFs (not scanned images)
- ✓ DOCX files must be standard Word format
- ✓ Check file isn't corrupted by opening it manually first
- ✓ File size limits depend on your hosting configuration

### Empty Sections Not Deleting

**Problem:** Unused experience sections remain in output

**Solutions:**

- ✓ The system only deletes complete experience blocks (EXP1-20)
- ✓ Individual fields like education/certs are replaced with fallback text
- ✓ Check that unused experience tokens are on separate lines in template
- ✓ Verify your template structure matches the expected format

---

## Product Overview

### What It Does

This tool converts candidate CVs from any format into your standardized company template. Upload one CV or 50 - the system reads each one, extracts all relevant information, and outputs formatted documents ready for review.

### The Process

1. **Upload** - HR uploads company template + candidate CVs
2. **Extract** - AI reads each CV and pulls out names, experience, education, etc.
3. **Format** - System fills template tokens with extracted data
4. **Clean** - Automatically removes unused sections (empty jobs, missing certs)
5. **Download** - Get individual formatted CVs or download all as ZIP

### What Gets Handled Automatically

| Feature                  | Description                                        |
| ------------------------ | -------------------------------------------------- |
| **Name formatting**      | Converts "JOHN DOE" to "John Doe"                  |
| **Date standardization** | All dates become "MMM YYYY" format                 |
| **Location extraction**  | Separates "Boston, MA" from company names          |
| **Multiple roles**       | Creates separate entries for roles at same company |
| **Missing information**  | Adds fallbacks like "Location Not Specified"       |
| **Empty sections**       | Removes completely from final document             |

### Benefits

- ✅ Process 50 CVs in minutes instead of hours
- ✅ Consistent formatting across all candidates
- ✅ No manual copy-pasting
- ✅ Secure access (company email authentication required)
- ✅ Preserves all candidate information accurately
- ✅ Automatic cleanup of unused template sections
- ✅ Batch download as ZIP for efficiency

### Customization Freedom

The template is yours - format it however you want. Change fonts, colors, layouts, add your logo. Just keep the tokens in place and the system will work perfectly. The AI adapts to your styling while maintaining consistency across all converted CVs.

---

## Support

For technical issues or questions:

1. Check the [Troubleshooting](#troubleshooting) section above
2. Review code comments in respective `.py` files
3. Verify your configuration in `.streamlit/secrets.toml`

**Remember:** Always test with a sample CV before bulk processing to ensure extraction quality meets your needs.

_




📊 AI-Powered CV Analyzer & Candidate Scoring Engine - app.py

The CV Analyzer is an AI-driven candidate evaluation system designed to assist recruiters in making faster, more objective, and data-informed hiring decisions. The platform analyzes candidate CVs against job descriptions to generate ATS compatibility scores, skill gap analysis, and automated hiring recommendations.

This system enables HR teams to process hundreds of applications in minutes instead of hours, dramatically improving recruitment efficiency and consistency.

 Core Capabilities

	•	ATS Compatibility Scoring
Evaluates how well a candidate’s CV aligns with job description requirements using AI-based semantic matching.
	•	Skill Gap Analysis
Identifies missing or underrepresented skills relative to job criteria and highlights development opportunities.
	•	Automated Hiring Recommendations
Generates structured insights to support shortlist, interview, or rejection decisions.
	•	Advanced NLP Information Extraction
Extracts work experience, education, skills, certifications, and achievements using modern natural language processing models.
	•	Multi-Format Support
Analyzes CVs from PDF, DOCX, and TXT formats with consistent accuracy.

Security & Compliance

	•	Role-based access control for recruiters and administrators
	•	Secure authentication and controlled data access
	•	Privacy-first handling of candidate information aligned with HR compliance standards

System Architecture

	•	Python-based AI processing engine
	•	RESTful API architecture for system integration
	•	Cloud deployment with scalable infrastructure
	•	99.9% uptime reliability

Business Impact

	•	Reduced recruiter screening workload by 80%
	•	Improved consistency and fairness in candidate evaluation
	•	Enabled HR teams to focus on strategic hiring and talent engagement
	•	Achieved 100% adoption across HR users within the first week

