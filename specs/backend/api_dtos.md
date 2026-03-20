# OpenUI Backend API & DTO Specifications

## 1. API Endpoints

### 1.1. AI & Generation
| Endpoint | Method | Description | Request DTO | Response |
| :--- | :--- | :--- | :--- | :--- |
| `/v1/chat/completions` | POST | Proxy LLM requests with streaming response. | Raw JSON (OpenAI format) | SSE (text/event-stream) |
| `/v1/models` | GET | List all available models across providers. | None | JSON |

### 1.2. Authentication & Sessions
| Endpoint | Method | Description | Response |
| :--- | :--- | :--- | :--- |
| `/v1/login` | GET | Redirects to GitHub OAuth login. | RedirectResponse |
| `/v1/callback` | GET | GitHub OAuth callback handler. | RedirectResponse (with Session) |
| `/v1/session` | GET | Retrieve current session metadata. | `SessionData` (JSON) |
| `/v1/session` | DELETE | Log out and clear current session. | JSON (empty) |

### 1.3. Persistence & Sharing
| Endpoint | Method | Description | Request DTO | Response |
| :--- | :--- | :--- | :--- | :--- |
| `/v1/share/{id}` | POST | Upload component JSON to S3 cloud storage. | `ShareRequest` | `ShareRequest` |
| `/v1/share/{id}` | GET | Download shared component JSON from S3. | None | JSON |
| `/v1/vote` | POST | Record user feedback (up/down vote). | `VoteRequest` | `VoteRequest` |

### 1.4. Assets & Placeholders
| Endpoint | Method | Description | Response |
| :--- | :--- | :--- | :--- |
| `/openui/{name}.svg` | GET | Generate dynamic SVG placeholder icons. | `image/svg+xml` |
| `/openui/{name}.mp3` | GET | Serves placeholder audio assets. | `audio/mpeg` |

---

## 2. Data Transfer Objects (DTOs)

### 2.1. `ShareRequest`
Used for persisting components to S3 storage.
- **`prompt`** (`str`): The original text prompt used for generation.
- **`name`** (`str`): A human-readable name for the component.
- **`emoji`** (`str`): A representative emoji for the UI.
- **`html`** (`str`): The full generated HTML/JS payload.

### 2.2. `VoteRequest`
Used for capturing user quality training data.
- **`prompt`** (`str`): The original text prompt.
- **`name`** (`str`): Component name.
- **`emoji`** (`str`): Representative emoji.
- **`html`** (`str`): Generated code payload.
- **`vote`** (`bool`): `True` for thumbs up, `False` for thumbs down.

### 2.3. `SessionData`
Metadata returned describing the current authenticated user.
- **`username`** (`str`): GitHub username.
- **`email`** (`str`): User email address.
- **`token_count`** (`int`): Tokens consumed in the last 24 hours.
- **`max_tokens`** (`int`): Daily token quota (default: 700,000).
- **`model`** (`str`): Currently selected default model.
