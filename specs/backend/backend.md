# OpenUI Backend Technical Design & Architecture

## 1. System Overview
The OpenUI backend is a Python-based service responsible for proxying LLM requests, managing user sessions, persisting component history, and facilitating component sharing via cloud storage.

## 2. Tech Stack
- **Language**: Python 3.9+
- **Framework**: FastAPI (Asynchronous web framework)
- **Database**: SQLite with Peewee ORM
- **Storage**: AWS S3 (via Boto3) with Gzip compression
- **LLM Proxying**: LiteLLM, Ollama, and direct OpenAI SDK integrations
- **Auth**: GitHub SSO (OAuth2) and WebAuthn (Passkeys)
- **Monitoring**: Weights & Biases (W&B) Weave for tracing

## 3. Architecture

### 3.1. Database Schema (`openui/db/models.py`)
Uses Peewee ORM for a relational schema stored in SQLite (WAL mode).
- **User**: Stores basic GitHub/Passkey user profiles.
- **Credential**: WebAuthn credential data (public keys, sign counts).
- **Session**: Server-side session state linked to users.
- **Component**: Metadata and JSON data for generated UI components.
- **Vote**: User feedback (thumbs up/down) for specific components.
- **Usage**: Token consumption tracking per user per day.
- **SchemaMigration**: Tracks database versioning for automated migrations.

### 3.2. LLM Proxying & Abstraction
The backend provides a unified interface for different LLM providers via the `/v1/chat/completions` endpoint.
- **Internal Workers**: Specialized generators (`openai.py`, `ollama.py`, `dummy.py`) handle the specifics of streaming responses.
- **LiteLLM Bridge**: Dynamically generates configurations for Anthropic, Google (Gemini), Mistral, and Cohere based on environment variables.
- **Consumption Control**: `Usage` tracking enforces token quotas to prevent cost overruns in production.

### 3.3. Storage Logic (`openui/util/storage.py`)
- **Shared Components**: When a user shares a UI, the JSON payload is Gzipped and uploaded to S3.
- **Placeholder Assets**: The backend dynamically generates SVGs (`/openui/{name}.svg`) for placehold.co-like icons within generated UIs.

## 4. API Reference

| Endpoint | Method | Description |
| :--- | :--- | :--- |
| `/v1/chat/completions` | POST | Proxies streaming LLM requests. |
| `/v1/models` | GET | Aggregates available models from all providers. |
| `/v1/login` | GET | Initiates GitHub SSO redirect. |
| `/v1/share/{id}` | POST/GET | Uploads or retrieves shared UI components from S3. |
| `/v1/vote` | POST | Records user quality feedback. |
| `/v1/session` | GET/DELETE | Manages user session lifecycle. |

## 5. Security & Persistence
- **Session Management**: Starlette `SessionMiddleware` combined with a custom `DBSessionStore`.
- **Environment Driven**: Configuration uses `.env` files and standard environment variables for secret management.
- **Passkeys**: Native support for WebAuthn allows for passwordless login flows.

## 6. Development & Evaluation
- **Tracing**: Integrated with `weave` to capture inputs, outputs, and latency for LLM calls.
- **Local Dev**: Mock/Dummy generators and local SQLite support for easy development without cloud dependencies.
