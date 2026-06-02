# AI-Driven Educational Quiz Platform Generator

This system transforms user-specified subjects and knowledge points into a fully functional online quiz platform. Given a topic (e.g., "Python Basics: variables, loops, functions"), the system automatically generates a Flask API with question bank management, quiz generation via AI, auto-scoring, and grade statistics — along with an interactive frontend featuring quiz pages, result analysis, wrong-answer review, and an AI-generated knowledge mind map image. As users input different topics and generate questions, all questions accumulate in the question bank, creating a comprehensive mixed-topic quiz — the more topics you add, the richer and more diverse the quiz becomes.

> **Scope:** The core quiz flow (topic input → AI generation → quiz taking → auto-scoring) is fully implemented. The "Review Wrong" and "View Stats" sections on the frontend are UI placeholders.



## Quick Start

### Prerequisites

- `ai_in_se_cw.yml`
- `.env` – Contains the [APIFree](https://apifree.ai) API key (used for LLM calls).

### Setup

```bash
# 1. Clone and enter the project
cd DTS114TC-CW_2472426

# 2. Create conda environment from the provided file
conda env create -f ai_in_se_cw.yml
conda activate ai_in_se_cw

# 3. (If you don't have your own .env file) 
# Create .env file with your API key 
echo 'APIFREE_API_KEY=your-key-here' > .env
echo 'APIFREE_API_BASE=https://api.apifree.ai' >> .env

# 4. Launch Jupyter or open in VS Code
jupyter notebook
```

### Generate the Quiz Platform

1. Open `CW/Generator.ipynb` in Jupyter
2. Run all cells sequentially (Cell → Run All)
3. The notebook will:
   - Generate problem statement, personas, PRD, and user stories via AI
   - Generate UML diagrams (use case, class, sequence)
   - **Auto-generate a complete Flask API** (`CW/artifacts/app/flask/main.py`)
   - Generate an interactive quiz frontend with dark theme
   - Generate an AI knowledge mind map image
   - Generate Docker configuration, tests, and README

>   **！！ Important Notes ！！**
>
> 1. **API request failures** — Due to network instability, some API calls may fail with an error message. If a cell outputs an error starting with `APIFREE error`, or `Error:`, simply **re-run that cell** (and any dependent cells below it). It may take 1–3 retries before all requests succeed.
>
> 2. **Cells getting stuck** — Some cells (especially image generation in Step 5) may appear to hang. Check the cell's output area — if you see a polling counter , the cell is still running. Do not interrupt it; image generation can take up to 2 minutes.

### Run the Generated App

Ensure your terminal is running in the `ai_in_se_cw` conda environment. If not, run *(This command applies to conda users only.)*:
 ```bash
 conda activate ai_in_se_cw
 ```
 


**Method 1 — Docker (recommended for local testing)**

```bash
cd ./CW/artifacts/app

# Build & run (Need to start Docker Desktop first)
docker build -t quiz-platform:latest -f docker/Dockerfile .
docker run -p 5005:5005 quiz-platform:latest

# Or with docker-compose
docker-compose up --build
```


**Method 2 — Direct**

```bash
cd ./CW/artifacts/app/flask
pip install -r requirements.txt
python main.py
```


Then, open **http://127.0.0.1:5005** in your browser.

## API Endpoints

| Method | Route | Description |
|--------|-------|-------------|
| `GET` | `/health` | Health check — returns `{"status": "ok"}` |
| `POST` | `/generate` | Generate quiz questions via AI. Body: `{"topic": "...", "count": 5}` |
| `GET` | `/quiz?count=N` | Get current quiz questions (optional count filter) |
| `POST` | `/submit` | Submit answers for scoring. Body: `{"answers": [{"question_id": 1, "selected": "A"}]}` |
| `GET` | `/` | Quiz frontend page |



## File Structure

```
DTS114TC-CW_2472426/
├── .env                          # API keys (git-ignored)
├── .gitignore
├── ai_in_se_cw.yml               # Conda environment file
├── README.md                     # This file
├── .github/
│   └── workflows/
│       └── ci.yml                # CI/CD pipeline (GitHub Actions)
├── CW/
│   ├── __pycache__/              # Python bytecode cache (auto-generated)
│   ├── Generator.ipynb           # Core notebook — runs the AI pipeline
│   ├── utils.py                  # LLM client, helpers, PlantUML renderer
│   └── artifacts/                # Generated output (created by notebook)
│       ├── prd.md                # Product Requirements Document
│       ├── user_stories.json     # User stories (JSON)
│       ├── diagrams/             # PlantUML diagrams (PNG)
│       └── app/
│           ├── flask/
│           │   ├── main.py       # Generated Flask API
│           │   ├── index.html    # Quiz frontend (Bootstrap 5, dark theme)
│           │   ├── requirements.txt
│           │   ├── tests.py      # Basic API tests
│           │   ├── .env          # API key copy for Flask
│           │   └── static/
│           │       └── mindmap.png  # AI-generated mind map
│           ├── docker/
│           │   ├── Dockerfile
│           │   └── .dockerignore
│           └── docker-compose.yml
```



## CI Pipeline

This project includes a GitHub Actions workflow at `.github/workflows/ci.yml`.

### Workflow Triggers

| Trigger | When |
|---------|------|
| `push` to `main` | On every push (ignoring `.md` changes) |
| `pull_request` to `main` | On every PR |
| `workflow_dispatch` | Manual trigger from Actions tab |

### Pipeline Jobs

The workflow runs 4 independent jobs in parallel:

```
Push / PR / Manual
       │
       ├── Code Style           flake8 + markdown lint
       ├── Security Scan        grep hardcoded secrets
       ├── Notebook Structure   JSON validity + phase headers + execution
       └── Environment & Imports  conda setup + utils import test
```

| Job | Checks | Dependencies |
|-----|--------|-------------|
| **Code Style** | • flake8 on `CW/utils.py` (non-blocking)<br>• markdownlint on `README.md` (non-blocking) | System Python + npx only |
| **Security Scan** | • Grep for hardcoded API keys, secrets, tokens in source files | No Python needed |
| **Notebook Structure** | • Valid JSON with code + markdown cells<br>• Inception, Construction, Operation phase headers present<br>• Code cells executed and have outputs | System Python only |
| **Environment & Imports** | • `CW.utils` imports resolve without errors<br>• Conda environment exportable | Full conda environment |


## License


## License

This project is created for academic coursework (DTS114TC, XJTLU).
