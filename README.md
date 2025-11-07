# 🚀 Véridion AI - Quality Orchestrator

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.104+-green.svg)](https://fastapi.tiangolo.com/)
[![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4-orange.svg)](https://openai.com/)

> **AI-Driven Verification & Quality Orchestration Platform**  
> Seamlessly infusing Generative AI across the Software Development Life Cycle (SDLC) and Software Testing Life Cycle (STLC)

---

## 📋 Overview

**Véridion** is a cutting-edge Low-Code/No-Code (LCNC) platform that revolutionizes software quality assurance by leveraging advanced AI to automate and enhance every stage of the testing lifecycle - from requirement synthesis to bug triaging.

### ✨ Key Features

- 🤖 **AI-Powered Requirement Synthesis** - Transform unstructured input into clear user stories and acceptance criteria
- 🧪 **Intelligent Test Case Generation** - Automatically create comprehensive test suites (Positive, Negative, Boundary, Edge cases)
- 💻 **Automated Script Development** - Generate production-ready automation scripts (Playwright, Cypress, Selenium)
- 🔍 **Smart Bug Triaging** - AI-driven root cause analysis and automated bug report creation
- 📊 **Executive Analytics** - Real-time quality metrics and predictive insights
- 🔗 **CI/CD Integration** - Seamless integration with GitHub, GitLab, Jira, and Azure DevOps

---

## 🏗️ Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                   VÉRIDION AI ORCHESTRATOR                    │
├──────────────────────────────────────────────────────────────┤
│  Input Layer → AI Processing → Automation → Execution → AI   │
└──────────────────────────────────────────────────────────────┘
```

**Workflow Stages:**
1. **Requirement Synthesis** - Unstructured input → Structured requirements
2. **Test Case Design** - Requirements → Comprehensive test cases
3. **Script Generation** - Test cases → Executable automation scripts
4. **Test Execution** - Scripts → CI/CD pipeline execution
5. **Bug Triaging** - Failures → AI-analyzed bug reports
6. **Analytics & Reporting** - Results → Executive insights

---

## 🚀 Quick Start

### Prerequisites

- Python 3.10 or higher
- PostgreSQL 14+
- MongoDB 6+
- Redis 7+
- OpenAI API Key
- Docker & Docker Compose (optional)

### Installation

#### Option 1: Using Scripts (Recommended)

1.  **Initialize git (Windows):**
    Run the `000_init.bat` file.

2.  **Create a virtual environment (Windows):**
    Run the `001_env.bat` file.

3.  **Activate the virtual environment (Windows):**
    Run the `002_activate.bat` file.

4.  **Install dependencies:**
    Run the `003_setup.bat` file. This will install all the packages listed in `requirements.txt`.

5.  **Deactivate the virtual environment (Windows):**
    Run the `008_deactivate.bat` file.

## Usage

1.  **Run the main application (Windows):**
    Run the `004_run.bat` file.

    [Provide instructions on how to use your application.]

## Batch Files (Windows)

This project includes the following batch files to help with common development tasks on Windows:

* `000_init.bat`: Initialized git and also usn and pwd config setup also done.
* `001_env.bat`: Creates a virtual environment named `venv`.
* `002_activate.bat`: Activates the `venv` virtual environment.
* `003_setup.bat`: Installs the Python packages listed in `requirements.txt` using `pip`.
* `004_run.bat`: Executes the main Python script (`main.py`).
* `005_run_test.bat`: Executes the pytest  scripts (`test_main.py`).
* `005_run_code_cov.bat`: Executes the code coverage pytest  scripts (`test_main.py`).
* `008_deactivate.bat`: Deactivates the currently active virtual environment.

## Contributing

[Explain how others can contribute to your project.]

## License

[Specify the project license, if any.]
