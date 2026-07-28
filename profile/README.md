# AgentCost

**Track, analyze, and optimize LLM costs in your AI applications.**

[AgentCost](https://agentcost.tech) is an open-source observability platform for LLM-powered applications. It automatically tracks every LLM call your AI agents make, calculates real-time costs, and provides actionable insights to reduce your AI infrastructure spending.

**[agentcost.tech](https://agentcost.tech)** · [Live demo, no signup](https://agentcost.tech/demo) · [Docs](https://agentcost.tech/docs/sdk) · [PyPI](https://pypi.org/project/agentcost/)

## The Problem

When building AI applications with OpenAI, Anthropic, Gemini, or LangChain, you have no visibility into:

- Which agents are costing you money
- Which LLM calls are happening under the hood
- How to optimize your prompts and model choices

AgentCost solves this with zero code changes.

## Features

- Zero-Friction Integration: Add 2 lines of code - your existing OpenAI, Anthropic, Gemini, and LangChain code works unchanged
- Real-Time Tracking: See every LLM call, token count, cost, and latency
- Multi-Agent Support: Track costs per agent in your multi-agent systems
- User Management: Secure user authentication and project-based access control
- Team Collaboration: Invite team members and manage project permissions
- Optimization Suggestions: Get AI-powered recommendations to reduce costs
- Analytics Dashboard: Beautiful charts and trends (React-based)
- Admin Platform: Comprehensive platform management and monitoring
- Feedback System: User feedback collection and management
- File Attachments: Secure file upload and management
- Self-Hostable: Run on your own infrastructure for data privacy

## Architecture

```
┌─────────────────────┐     ┌─────────────────────┐     ┌─────────────────────┐
│   Your AI           │     │   AgentCost SDK     │     │  AgentCost Backend  │
│   Application       │───▶│  (Python Package)   │────▶│    (FastAPI)        │
│                     │     │                     │     │                     │
│  - OpenAI           │     │  - Monkey patching  │     │  - Event ingestion  │
│  - Anthropic        │     │  - Token counting   │     │  - Analytics API    │
│  - Gemini           │     │  - Cost calculation │     │  - User management  │
│  - LangChain        │     │  - Batching         │     │  - Project system   │
└─────────────────────┘     └─────────────────────┘     │  - Admin platform   │
                                                        └─────────────────────┘
                                                                   │
                                                                   ▼
                                                    ┌───────────────────────────┐
                                                    │   AgentCost Dashboards    │
                                                    │                           │
                                                    │  User Dashboard (Next.js) │
                                                    │   - Cost overview         │
                                                    │   - Project management    │
                                                    │   - Team collaboration    │
                                                    │                           │
                                                    │  Admin Dashboard (Next.js)│
                                                    │   - Platform admin        │
                                                    │   - User management       │
                                                    │   - System monitoring     │
                                                    └───────────────────────────┘
```

## Quick Start

### 1. Install the SDK

```bash
pip install agentcost
# or from source:
pip install -e agentcost-sdk/
```

### 2. Add to Your Code (2 lines!)

```python
from agentcost import track_costs

# Initialize tracking
track_costs.init(
    api_key="your_api_key",
    project_id="my-project"
)

# Your existing code works unchanged!
# OpenAI
from openai import OpenAI
client = OpenAI()
response = client.chat.completions.create(model="gpt-4o", messages=[{"role": "user", "content": "Hello, world!"}]) # ← Automatically tracked!

# Anthropic
from anthropic import Anthropic
client = Anthropic()
message = client.messages.create(model="claude-3-5-sonnet-20241022", max_tokens=100, messages=[{"role": "user", "content": "Hello, world!"}]) # ← Automatically tracked!

# Gemini (Google Gen AI SDK)
from google import genai
client = genai.Client()
response = client.models.generate_content(model="gemini-2.5-flash", contents="Hello, world!") # ← Automatically tracked!

# LangChain
from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-4")
response = llm.invoke("Hello, world!") # ← Automatically tracked!
```

### 3. View Your Analytics

Start the backend and dashboard, then visit `http://localhost:3000` to see your costs.

## Repositories

AgentCost is not a monorepo — each component has its own repository:

| Repository | What it is |
| ---------- | ---------- |
| [agentcost-sdk](https://github.com/agentcost-ai/agentcost-sdk) | Python SDK, published to PyPI as `agentcost` |
| [agentcost-backend](https://github.com/agentcost-ai/agentcost-backend) | FastAPI backend — event ingestion, analytics, auth |
| [agentcost-dashboard](https://github.com/agentcost-ai/agentcost-dashboard) | Next.js dashboard and marketing site |
| [agentcost-admin](https://github.com/agentcost-ai/agentcost-admin) | Admin platform |

**Inside `agentcost-sdk`:**

```
agentcost/
├── tracker.py                 # Main entry point — track_costs.init()
├── interceptor.py             # LangChain monkey patching
├── openai_interceptor.py      # OpenAI monkey patching
├── anthropic_interceptor.py   # Anthropic monkey patching
├── gemini_interceptor.py      # Gemini (Google Gen AI) monkey patching
├── token_counter.py           # tiktoken fallback counting
├── cost_calculator.py         # Real-time cost calculation
├── batcher.py                 # Event batching (size + time)
├── http_client.py             # Backend communication
└── config.py                  # Settings + offline fallback pricing
```

**Inside `agentcost-backend`:** `app/main.py` (entry point), `app/routes/`
(API endpoints), `app/services/` (business logic), `app/models/` (Pydantic &
SQLAlchemy), plus `Dockerfile` and `docker-compose.yml`.

## Development Setup

### Prerequisites

- Python 3.10+
- Node.js 18+ (for dashboard)
- Docker (optional, for containerized deployment)

AgentCost is split across separate repositories — clone the ones you need.

### 1. Clone the repositories

```bash
mkdir AgentCost && cd AgentCost

git clone https://github.com/agentcost-ai/agentcost-sdk.git
git clone https://github.com/agentcost-ai/agentcost-backend.git
git clone https://github.com/agentcost-ai/agentcost-dashboard.git
```

### 2. Install the SDK and backend

```bash
# Create virtual environment
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate

pip install -e agentcost-sdk/
pip install -r agentcost-backend/requirements.txt
```

### 3. Configure environment

```bash
cp agentcost-backend/.env.example agentcost-backend/.env

# Defaults work for local development.
# For production, set SECRET_KEY and DATABASE_URL.
```

### 4. Run the backend

```bash
cd agentcost-backend
uvicorn app.main:app --reload --port 8000

# API available at http://localhost:8000
# Interactive docs at http://localhost:8000/docs
```

### 5. Run the dashboard

```bash
cd agentcost-dashboard
npm install
npm run dev

# Dashboard at http://localhost:3000
```

### 6. Run the demo

```bash
# From inside the SDK repo, in another terminal
cd agentcost-sdk
python demo_sdk.py
```

## How Costs Are Calculated

All costs are calculated in **real-time** using:

1. **Token Counting**: Uses the provider's own reported usage when the SDK returns it (including Gemini's `usage_metadata`), and falls back to `tiktoken` when it does not
2. **Model Pricing**: Up-to-date pricing for **3,500+ models** from all major providers
3. **Formula**:
   ```
   cost = (input_tokens / 1000 × input_price) + (output_tokens / 1000 × output_price)
   ```

### Supported Providers (3,500+ Models)

AgentCost supports pricing for models from:

| Provider | Examples |
| ---------------- | -------------------------------------------- |
| **OpenAI** | GPT-4, GPT-4-Turbo, GPT-4o, o1, o1-mini |
| **Anthropic** | Claude 3 Opus, Claude 3.5 Sonnet, Claude 4 |
| **Google** | Gemini 2.5 Pro/Flash, Gemini 2.0, Gemini 1.5 |
| **Mistral** | Mistral Small, Medium, Large |
| **DeepSeek** | DeepSeek Chat, DeepSeek Reasoner |
| **Groq** | Llama 3.x, Mixtral |
| **Cohere** | Command, Command-R, Command-R+ |
| **Together AI** | Meta Llama, Qwen, Phi |
| **AWS Bedrock** | All Bedrock-hosted models |
| **Azure OpenAI** | All Azure-hosted OpenAI models |
| **Perplexity** | pplx-\* models |
| **And 50+ more** | Replicate, Fireworks, Anyscale, etc. |

**Pricing is synced from [LiteLLM's pricing database](https://github.com/BerriAI/litellm)** which is continuously updated.

To sync pricing manually:

```bash
# Sync from LiteLLM (3500+ models)
curl -X POST http://localhost:8000/v1/pricing/sync/litellm

# Check sync status
curl http://localhost:8000/v1/pricing/sync/status
```

## Production Deployment

See the deployment guide in the
[backend repository](https://github.com/agentcost-ai/agentcost-backend) for
detailed instructions.

**Quick checklist:**

- [ ] Set `ENVIRONMENT=production`
- [ ] Set `SECRET_KEY` (use `python -c "import secrets; print(secrets.token_urlsafe(32))"`)
- [ ] Use PostgreSQL: `DATABASE_URL=postgresql+asyncpg://...`
- [ ] Configure CORS origins for your domain
- [ ] Set up HTTPS (nginx/Caddy)
- [ ] Remove `prototype.py` and test files

## Testing

```bash
# SDK tests
cd agentcost-sdk
pytest tests/ -v

# End-to-end test (requires backend running)
python test_e2e.py
```

## API Reference

### SDK

```python
# Initialize
track_costs.init(api_key="...", project_id="...", local_mode=False)

# Agent tagging
with track_costs.agent("my-agent"):
 llm.invoke("...")

# Add metadata
with track_costs.metadata(user_id="123", session_id="abc"):
 llm.invoke("...")

# Get local events (local_mode only)
events = track_costs.get_local_events()

# Manual flush
track_costs.flush()

# Shutdown
track_costs.shutdown()
```

### Backend API

| Endpoint | Method | Description |
| -------------------------- | ------ | ----------------------------- |
| `/v1/health` | GET | Health check |
| `/v1/projects` | POST | Create project |
| `/v1/projects/{id}` | GET | Get project |
| `/v1/events/batch` | POST | Ingest events |
| `/v1/analytics/overview` | GET | Cost overview |
| `/v1/analytics/agents` | GET | Per-agent stats |
| `/v1/analytics/models` | GET | Per-model stats |
| `/v1/analytics/timeseries` | GET | Time series data |
| `/v1/optimizations` | GET | Cost optimization suggestions |

## Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Run tests
5. Submit a pull request

## License

MIT License — see the [LICENSE](https://github.com/agentcost-ai/agentcost-sdk/blob/main/LICENSE)
in the SDK repository for details.

## Acknowledgments

- Built with [LangChain](https://langchain.com/)
- Token counting via [tiktoken](https://github.com/openai/tiktoken)
- Backend powered by [FastAPI](https://fastapi.tiangolo.com/)
- Dashboard built with [Next.js](https://nextjs.org/)
