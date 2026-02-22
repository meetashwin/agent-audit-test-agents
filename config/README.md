# Financial Advisor – Google ADK YAML Config

A fully declarative, multi-agent financial advisory workflow built with the
[Google Agent Development Kit (ADK)](https://google.github.io/adk-docs/)
Agent Config feature (Python ≥ v1.11.0, experimental).

---

## Project Structure

```
financial_advisor/
├── root_agent.yaml       # Coordinator – orchestrates the four sub-agents
├── data_analyst.yaml     # Step 1 – Market analysis via Google Search
├── trading_analyst.yaml  # Step 2 – Trading strategy development
├── execution_analyst.yaml# Step 3 – Trade execution planning
├── risk_analyst.yaml     # Step 4 – Risk evaluation
├── .env                  # Environment variables (not committed)
└── README.md
```

---

## Prerequisites

| Requirement | Version |
|---|---|
| Python | 3.10+ |
| google-adk | ≥ 1.11.0 |

Install ADK:
```bash
pip install google-adk
```

---

## Configuration

Copy `.env.example` to `.env` and fill in your credentials.

**Option A – Google AI Studio (Gemini API Key)**
```
GOOGLE_GENAI_USE_VERTEXAI=0
GOOGLE_API_KEY=<your-Google-Gemini-api-key>
```

**Option B – Google Cloud Vertex AI**
```
GOOGLE_GENAI_USE_VERTEXAI=1
GOOGLE_CLOUD_PROJECT=<your-gcp-project-id>
GOOGLE_CLOUD_LOCATION=us-central1
# Only needed for Agent Engine deployment:
GOOGLE_CLOUD_STORAGE_BUCKET=<your-gcs-bucket>
```

Then authenticate:
```bash
gcloud auth application-default login
gcloud auth application-default set-quota-project $GOOGLE_CLOUD_PROJECT
```

---

## Running the Agent

From inside the `financial_advisor/` directory:

```bash
# Interactive web UI
adk web

# Terminal chat
adk run financial_advisor

# Headless API server
adk api_server
```

---

## Agent Workflow

```
User
 │
 ▼
Financial Coordinator  (root_agent.yaml)
 │
 ├─ Step 1 ──► Data Analyst        → market_data_analysis_output
 │              └─ [google_search]
 │
 ├─ Step 2 ──► Trading Analyst     → proposed_trading_strategies_output
 │
 ├─ Step 3 ──► Execution Analyst   → execution_plan_output
 │
 └─ Step 4 ──► Risk Analyst        → final_risk_assessment_output
```

---

## Deploying to Vertex AI Agent Engine

```bash
pip install google-adk[agent-engines]

adk deploy agent_engine \
  --project=$GOOGLE_CLOUD_PROJECT \
  --region=$GOOGLE_CLOUD_LOCATION \
  --staging_bucket=gs://$GOOGLE_CLOUD_STORAGE_BUCKET \
  financial_advisor
```

---

## Known ADK Limitations (as of v1.11+)

- Only **Gemini models** are supported in YAML config mode.
- Only a subset of ADK tools work: `google_search`, `load_artifacts`,
  `url_context`, `exit_loop`, `get_user_choice`, etc.
- `LangGraphAgent` and `A2aAgent` types are not yet supported.

---

> ⚠️ **Disclaimer:** All outputs from this agent system are for educational
> and informational purposes only. They do not constitute financial advice.
> Always consult a qualified financial advisor before making investment decisions.
