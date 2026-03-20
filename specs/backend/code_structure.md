# OpenUI Backend Code Structure

## 1. Directory Overview (`openui/`)

```text
openui/
├── api/             # (Note: Logic is currently consolidated in server.py)
├── assets/          # Static assets (SVGs, audio for placeholders)
├── db/              # Database layer
│   └── models.py    # Peewee models and migrations
├── eval/            # Evaluation & Dataset tools
│   ├── evaluate.py  # LLM quality evaluation
│   ├── scrape.py    # UI data collection
│   └── synthesize.py # Data synthesis for fine-tuning
├── tui/             # Text User Interface (experimental)
├── util/            # Shared utilities
│   ├── storage.py   # S3 & Gzip management
│   └── screenshots.py # UI capture logic
├── config.py        # App configuration & Envs
├── server.py        # FastAPI Entry point & Endpoints
├── session.py       # Session store implementation
├── openai.py        # OpenAI streaming logic
├── ollama.py        # Ollama streaming logic
├── litellm.py       # Multi-provider bridge logic
└── models.py        # Pydantic request/response models
```

## 2. Core Modules

### 2.1. API & Routing (`server.py`)
- **App Lifecycle**: Manages FastAPI initialization, middleware (CORS, Sessions), and lifespan events.
- **Endpoint Handlers**: Processes `/v1/chat/completions`, `/v1/share`, and session management.
- **Provider Switching**: Logic for routing prompts to OpenAI, Groq, Ollama, or LiteLLM.

### 2.2. Database & Persistence (`db/models.py`)
- **ORM**: Uses Peewee for SQLite interactions.
- **Migrations**: Automated schema versioning and `perform_migration` logic.
- **Usage Tracking**: Atomic updates to token consumption via `Usage.update_tokens`.

### 2.3. LLM Integration Layer
- **Generators**: Provider-specific modules (`openai.py`, `ollama.py`, `dummy.py`) that implement the streaming SSE protocol.
- **LiteLLM Bridge**: Dynamically maps environment keys (e.g., `GEMINI_API_KEY`, `ANTHROPIC_API_KEY`) to model configurations.

### 2.4. Evaluation & Fine-Tuning (`eval/`)
- **Tracing**: Deep integration with W&B Weave for capturing generation traces.
- **Synthesizers**: Scripts to convert scraped UI data into training datasets for LLM fine-tuning.

## 3. External Libraries

### 3.1. Core Service
- **FastAPI / Uvicorn**: Async web server and routing.
- **Peewee**: Lightweight ORM for SQLite.
- **Boto3**: AWS SDK for S3 component persistence.

### 3.2. LLM Clients
- **OpenAI / Ollama**: Official SDKs for cloud and local inference.
- **LiteLLM**: For standardized access to Claude, Gemini, and Mistral.

### 3.3. Utilities & Auth
- **ItsDangerous**: Cryptographic signing for session cookies.
- **FastAPI SSO**: OAuth2 implementation for GitHub login.
- **Tiktoken**: For accurate token counting across models.
- **Playwright**: Used in `eval/` for UI scraping and screenshotting.
