# Autopsy AI

AI-powered software auditing platform. Performs code review, test generation, security scanning, dependency analysis, and more — like having a senior engineer, QA lead, and security analyst review every line.

### Features

| Module                 | What It Does                                                                    |
| ---------------------- | ------------------------------------------------------------------------------- |
| **Code Review**        | SonarQube-style analysis: complexity, duplication, naming, best practices       |
| **Test Generation**    | Auto-generates pytest/Jest/JUnit test suites with edge cases and security tests |
| **Security Audit**     | OWASP Top 10 scan with static regex + AI deep analysis, CWE mapping             |
| **Documentation**      | Generates README.md and TECHNICAL_DOCS.md from source code                      |
| **Repo Intelligence**  | Clones a GitHub repo, detects tech stack, frameworks, architecture patterns     |
| **Dependency Scanner** | CVE lookup via OSV.dev API for npm/PyPI/Go packages                             |
| **Branch Comparison**  | Diffs two branches and flags new bugs, security issues, removed validations     |
| **Health Dashboard**   | Overall code health score (0-100) with category breakdown                       |
| **QA Scanner**         | Maps source files to test files, finds missing/empty tests                      |
| **DAST Simulator**     | Discovers API endpoints and generates simulated attack scenarios                |
| **Code Analyzer**      | Dead code detection, cyclomatic complexity, duplication, import graphs          |
| **Package Checker**    | Checks PyPI/npm for outdated or deprecated packages                             |
| **PR Integration**     | Posts analysis results as GitHub PR comments                                    |
| **Report Export**      | Downloadable HTML report with radar chart                                       |
| **Webhook Automation** | Receives GitHub push/PR events to trigger analysis                              |

## Project Structure

```
Autopsy-ai/
├── backend/
│   ├── main.py               # FastAPI entry point
│   ├── ai_helper.py           # Multi-provider AI client (Gemini/OpenAI/Claude)
│   ├── requirements.txt
│   ├── .env.example           # Template for API keys
│   ├── utils/
│   │   ├── constants.py       # Shared constants
│   │   ├── git_ops.py         # Git clone/parse helpers
│   │   └── parsers.py         # JSON cleaning, manifest parsing
│   └── routes/
│       ├── reviewer.py        # POST /api/review/
│       ├── tester.py          # POST /api/test/
│       ├── bug_hunter.py      # POST /api/audit/
│       ├── documenter.py      # POST /api/document/
│       ├── repo_analyzer.py   # POST /api/repo/analyze
│       ├── dependency_scanner.py  # POST /api/dependencies/scan
│       ├── branch_comparator.py   # POST /api/branch/compare
│       ├── dashboard.py       # POST /api/dashboard/generate
│       ├── repo_qa_scanner.py # POST /api/qa/scan
│       ├── dast_simulator.py  # POST /api/dast/scan
│       ├── code_analyzer.py   # POST /api/analyze/*
│       ├── package_checker.py # POST /api/packages/check
│       ├── pr_integration.py  # POST /api/pr/comment
│       ├── report_export.py   # POST /api/report/html
│       └── webhook.py         # POST /api/webhook/github
├── frontend/
│   ├── index.html             # Single-page UI
│   └── app.js                 # Frontend logic
└── examples/
    ├── sample.py              # Intentionally bad Python (for testing)
    └── sample.js              # Intentionally bad JavaScript (for testing)
```

## Quick Start

### 1. Clone and set up Python

```bash
cd backend
python3 -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

### 2. Configure API key

Copy the example env file and add your key:

```bash
cp .env.example .env
```

Then edit `.env` and uncomment one provider:

```env
# Pick ONE (Gemini is free):
GEMINI_API_KEY=your_key_here
# OPENAI_API_KEY=your_key_here
# ANTHROPIC_API_KEY=your_key_here
```

Get a free Gemini key at: https://aistudio.google.com/app/apikey

#### GitHub Token (optional — needed for PR integration & private repos)

1. Go to https://github.com/settings/tokens → **Generate new token (classic)**
2. Select scopes: `repo`, `write:discussion`
3. Generate, copy, and add to `.env`:
   ```env
   GITHUB_TOKEN=ghp_xxxxxxxxxxxx
   ```

#### GitHub Webhook Secret (optional — needed for webhook automation)

1. Pick any random string (or generate one):
   ```bash
   openssl rand -hex 20
   ```
2. Add it to `.env`:
   ```env
   GITHUB_WEBHOOK_SECRET=your_generated_secret
   ```
3. In your GitHub repo → **Settings → Webhooks → Add webhook**
   - Payload URL: `https://your-server/api/webhook/github`
   - Content type: `application/json`
   - Secret: paste the same string from step 1
   - Events: select **Pushes** and **Pull requests**

### 3. Run

```bash
python main.py
```

Open http://localhost:8000 for the UI, or http://localhost:8000/docs for the interactive API docs.

## API Endpoints

| Method | Endpoint                     | Description                   |
| ------ | ---------------------------- | ----------------------------- |
| POST   | `/api/review/`               | Code quality review           |
| POST   | `/api/test/`                 | Test suite generation         |
| POST   | `/api/audit/`                | Security vulnerability scan   |
| POST   | `/api/document/`             | Documentation generation      |
| POST   | `/api/repo/analyze`          | Full repository intelligence  |
| POST   | `/api/repo/scan-files`       | Lightweight file scan (no AI) |
| POST   | `/api/dependencies/scan`     | Dependency CVE scan           |
| POST   | `/api/branch/compare`        | Branch diff analysis          |
| POST   | `/api/dashboard/generate`    | Code health dashboard         |
| POST   | `/api/qa/scan`               | Test coverage mapping         |
| POST   | `/api/dast/scan`             | Simulated DAST                |
| POST   | `/api/analyze/full-analysis` | Dead code + metrics + linting |
| POST   | `/api/analyze/dead-code`     | Unused imports/functions      |
| POST   | `/api/analyze/metrics`       | Complexity and duplication    |
| POST   | `/api/analyze/imports`       | Import dependency graph       |
| POST   | `/api/analyze/lint`          | Framework-specific linting    |
| POST   | `/api/packages/check`        | Outdated package detection    |
| POST   | `/api/pr/comment`            | Post results to GitHub PR     |
| POST   | `/api/report/html`           | Download HTML report          |
| POST   | `/api/webhook/github`        | GitHub webhook receiver       |
| GET    | `/health`                    | Server status                 |

## Tech Stack

| Technology               | Purpose                                      |
| ------------------------ | -------------------------------------------- |
| Python 3.10+             | Backend                                      |
| FastAPI                  | Async web framework with auto-generated docs |
| Uvicorn                  | ASGI server                                  |
| httpx                    | Async HTTP client                            |
| Pydantic                 | Request/response validation                  |
| Gemini / OpenAI / Claude | AI provider (configurable)                   |
| OSV.dev API              | Open-source vulnerability database           |
| HTML / CSS / JS          | Frontend (no framework)                      |
