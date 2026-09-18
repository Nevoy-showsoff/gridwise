# Gridwise Energy Optimizer

A FastAPI microservice that optimizes 24-hour battery and grid power dispatch for campus energy systems. The system translates natural language operator notes into structured actionable directives via LLM, solves a linear programming model for cost minimization, and verifies system invariant guardrails on solver output.

**🔗 Live Demo:** [https://gridwise-bup.up.railway.app/](https://gridwise-bup.up.railway.app/)


**📖 API Docs (Swagger UI):** [https://gridwise-bup.up.railway.app/docs](https://gridwise-bup.up.railway.app/docs)

---

## Key Features

- **LLM Directive Parsing** — Extracts structured directives (`solar_reduction`, `minimum_battery_reserve`, `no_charge_window`, `no_discharge_window`, `max_grid_window`, `no_op`) from free-text operational notes using the Groq API.

- **Linear Programming Solver** — Generates optimal 24-hour dispatch schedules to minimize energy import costs in BDT under battery and grid constraints.

- **Guardrail & Invariant Validation** — Applies pre-optimization directive validation and post-optimization physical balance checks (energy balance, capacity constraints, end-of-day battery neutrality).

- **Containerized Deployment** — Ready-to-use Docker environment running FastAPI via Uvicorn.

---

## Project Architecture

| File | Description |
|---|---|
| `main.py` | API endpoint definitions (`/health`, `/optimize-energy`), validation flow, and error handling. |
| `llm_interpreter.py` | Integrates with Groq (`qwen/qwen3.8-27b`) to convert text notes into structured JSON schema directives. |
| `guardrails.py` | Validates LLM directive types, parameter ranges, and formatting. |
| `orchestrator.py` | Validates directive sequences across multiple operational notes. |
| `optimizer.py` | Linear programming solver logic for hourly power dispatch. |
| `final_validator.py` | Post-solver invariant verifier checking power flow balances and battery transitions. |
| `schemas.py` | Pydantic data schemas for API requests, responses, and directive parameters. |
| `Dockerfile` | Docker container configuration. |

---

## Getting Started

### Configuration

1. Clone the repository and navigate into the project directory.
2. Create a `.env` file in the root folder with your Groq API key:

```env
GROQ_API_KEY=your_api_key_here
```

### Local Setup

1. Install dependencies:

```bash
pip install -r requirements.txt
```

2. Run the application:

```bash
uvicorn main:app --host 0.0.0.0 --port 8000
```

### Docker Setup

Build and launch the application container:

```bash
docker build -t gridwise-optimizer .
docker run -p 8000:8000 --env-file .env gridwise-optimizer
```

---

## API Endpoints

- **`GET /health`** — Returns service health status (`{"status": "ok"}`).

- **`POST /optimize-energy`** — Accepts a scenario containing 24 hourly demand/solar/tariff profiles, battery specifications, and operator notes, returning an optimized dispatch plan.
