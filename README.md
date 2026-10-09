# 🛡️ PromptGuard — Continuous Integration (CI) for LLM Behavior

[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://python.org)
[![Ollama](https://img.shields.io/badge/Inference-Ollama-000000?style=for-the-badge)](https://ollama.ai)
[![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white)](.github/workflows/ci.yml)
[![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)

**PromptGuard** is an opinionated, pragmatic framework that establishes **Continuous Integration (CI) for LLM behavior**. It tests prompts across multiple model configurations, evaluates semantic drift, verifies tone invariants, and catches safety regressions before they reach production.

---

## 📌 Why PromptGuard?

In standard software engineering, CI/CD pipelines run unit tests on every pull request. In LLM applications, prompt modifications often cause **silent breaking changes**:
- A prompt tweak intended to fix one edge case subtly breaks 5 other workflows.
- Updating temperature or switching model checkpoints causes safety guardrail regressions.
- Conversational tone degrades without explicit syntax errors.

**PromptGuard treats prompts like code**: write test cases, define expectations, test across temperature and model matrices, and fail CI builds when behavior regresses.

---

## 🧩 Architectural Overview

```
                      +-----------------------------+
                      |     Prompt Test Suites      |
                      |   (prompts/*.yaml files)    |
                      +-----------------------------+
                                     |
                                     v
                      +-----------------------------+
                      |     Matrix Configurations   |
                      |       (configs.json)        |
                      | e.g. Llama-3.2 (T=0.2,0.7,1)|
                      +-----------------------------+
                                     |
                                     v
                      +-----------------------------+
                      |      PromptGuard Runner     |
                      |          (cli.py)           |
                      +-----------------------------+
                                     |
               +---------------------+---------------------+
               |                     |                     |
               v                     v                     v
     [ Semantic Diff ]        [ Tone Diff ]        [ Safety Diff ]
   Embeddings & Cosine      Formality & Style     Policy violations,
       Similarity               Stability         harm & jailbreaks
               |                     |                     |
               +---------------------+---------------------+
                                     |
                                     v
                      +-----------------------------+
                      |   CI Regression Report      |
                      |   PASS / FAIL status code   |
                      +-----------------------------+
```

### Core Differential Analyzers (`diff/`):
* **Semantic Diff (`diff/semantic_diff.py`):** Measures semantic deviation between actual model outputs and expected response criteria.
* **Tone Diff (`diff/tone_diff.py`):** Ensures brand persona and stylistic tone (e.g. professional, concise, empathetic) remain stable across temperature changes.
* **Safety Diff (`diff/safety_diff.py`):** Validates that safety guardrails are intact, flagging toxic outputs, policy breaches, or adversarial jailbreak vulnerabilities.

---

## 📁 Repository Structure

```text
PromptGuard/
├── cli.py                     # Command-line CI runner entry point
├── configs.json               # Model matrix configurations (models & temperatures)
├── prompts/                   # Test suite directory containing YAML test files
├── diff/                      # Differential evaluation engines
│   ├── semantic_diff.py       # Semantic drift detector
│   ├── tone_diff.py           # Persona and tone consistency validator
│   └── safety_diff.py         # Safety & alignment regression checker
├── runners/                   # Model execution drivers (Ollama / Local APIs)
│   └── run_models.py
├── report/                    # Test results formatter and markdown reporter
├── .github/workflows/ci.yml   # Automated GitHub Actions workflow
├── LICENSE                    # MIT License
└── README.md
```

---

## 🚀 Getting Started

### 1. Installation
Clone the repository and install dependencies:

```bash
git clone https://github.com/Tarunjit45/PromptGuard.git
cd PromptGuard

pip install -r requirements.txt
```

### 2. Configure Model Matrix (`configs.json`)
Specify the models and parameters to evaluate against:

```json
[
  { "model": "llama3.2:1b", "temperature": 0.2 },
  { "model": "llama3.2:1b", "temperature": 0.7 },
  { "model": "llama3.2:1b", "temperature": 1.0 }
]
```

### 3. Run the CI Test Suite
Execute PromptGuard against your test suite directory:

```bash
python cli.py --suite ./prompts --configs configs.json
```

If any prompt fails semantic, tone, or safety regression thresholds, PromptGuard outputs a detailed diagnostic table and exits with a non-zero code to block the pull request.

---

## 📄 License
This project is licensed under the [MIT License](LICENSE).
