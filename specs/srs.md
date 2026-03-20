# Software Requirement Specification (SRS): OpenUI

## 1. Introduction
This Software Requirement Specification (SRS) provides a comprehensive description of the functional and non-functional requirements for the OpenUI platform. It serves as a technical baseline for development, testing, and project management.

## 2. Overall Description

### 2.1. Product Perspective
OpenUI is a standalone web application designed to integrate with various LLM providers (OpenAI, Groq, Anthropic, Ollama) via standard REST APIs.

### 2.2. Product Functions
- Natural language to UI code generation.
- Image-to-UI structure mapping.
- Live preview and interactive annotation.
- Multi-framework code export (React, Vue, HTML).
- Session history and versioning.

### 2.3. User Classes and Characteristics
- **Developers**: Require clean, semantic code and framework flexibility.
- **Designers**: Require visual fidelity and fast iteration loops.
- **Stakeholders**: Require prototypes for validation.

## 3. System Features

### 3.1. Prompt Processing & UI Generation
- **REQ-1**: The system shall accept text prompts up to 4000 characters.
- **REQ-2**: The system shall allow image uploads (PNG/JPG) for vision-based generation.
- **REQ-3**: The system shall support streaming chunks of code to provide real-time feedback.

**Data Flow (Generation):**
1. **Input**: User enters text in `Prompt.tsx` or uploads an image via `FileReader`.
2. **Preprocessing**: Images are resized via `lib/utils/resizeImage` and stored in `state/imageDB`.
3. **API Call**: `Prompt.tsx` calls `api/openai/createOrRefine` with the prompt, model settings (Jotai atoms), and optional base64 image data.
4. **Streaming**: `createOrRefine` uses the OpenAI SDK with `stream: true`. Each chunk is passed back via a callback.
5. **State Update**: `Prompt.tsx` updates `liveMarkdown` state, which is then parsed by `lib/markdown/parseMarkdown`.
6. **Output**: Resulting HTML/JS is stored in the `historyAtomFamily` and emitted to the `HTMLAnnotator.tsx` iframe via `postMessage`.

### 3.2. Preview & Annotation
- **REQ-4**: The system shall render HTML/Tailwind/JS in a sandboxed iframe.
- **REQ-5**: The system shall implement an "Annotation Mode" where clicking a DOM element captures its selector and metadata for refinement prompts.

**Data Flow (Annotation):**
1. **Trigger**: User enables "Inspector" mode in `HTMLAnnotator.tsx`, which sends a `toggle-inspector` message to the sandboxed iframe.
2. **Interaction**: User clicks a DOM element within the iframe.
3. **Capture**: The iframe script identifies the element's CSS selector and HTML structure.
4. **Communication**: Iframe sends a `message` event back to the parent window containing the selector and comment context.
5. **State Integration**: Parent window updates `commentsAtom` and triggers a `streamResponse` in `Prompt.tsx` with `annotatedHTML` included in the request context.

### 3.3. Persistence & Management
- **REQ-6**: The system shall persist session history in indexedDB (via MiniDB).
- **REQ-7**: The system shall maintain an immutable list of versions for each session.
- **REQ-8**: The system shall provide a sharing mechanism that generates a public read-only URL.

**Data Flow (Persistence):**
1. **Write**: Any change to `setItem` (via `historyAtomFamily`) triggers the `useSaveHistory` hook.
2. **Storage**: Data is serialized and stored in `indexedDB` for sessions and `localStorage` for smaller metadata.
3. **Read**: On application load or navigation to `/ai/:id`, the `historyAtomFamily` hydratest state from the local databases.
4. **Sharing**: `ShareDialog.tsx` sends the current session state to the backend `/v1/share` endpoint to receive a persistent CID/URL.

## 4. External Interface Requirements

### 4.1. User Interfaces
- **Responsiveness**: The UI must be fully functional on viewports from 375px (mobile) to 2560px+ (desktop).
- **Theme**: Support for dynamic CSS variable switching for Light/Dark modes.

### 4.2. API Interfaces
- **Backend API**: FastAPI-based REST API providing endpoints for:
    - `/v1/chat/completions`: Aggregated model proxying (SSE).
    - `/v1/share`: Persistence of compressed (Gzip) payloads to AWS S3.
    - `/v1/models`: Dynamic discovery of available cloud and local LLMs.
- **Authentication**: 
    - **OAuth**: Integration with GitHub SSO via `fastapi-sso`.
    - **Passkey**: Integration with WebAuthn API for hardware-backed security.

## 5. Non-Functional Requirements

### 5.1. Performance
- **Latency**: Initial UI skeleton should render in < 500ms.
- **Streaming**: Code streaming should maintain a minimum rate of 20 tokens/sec.

### 5.2. Security
- **Authentication**: Must support GitHub SSO as the primary OAuth provider.
- **Data Protection**: All shared components must be compressed and stored in private S3 buckets with controlled access.
- **Usage Control**: Implement daily token quotas (default 700k) per user via `Usage` tracking in the database.
- **Passkeys**: Native support for WebAuthn/Passkey registration and login.

### 5.3. Reliability
- **Recovery**: The system must auto-save drafts to local storage in case of network failure.

## 6. Other Requirements
- **I18n**: Support for internationalization via `i18next`.
- **Framework Support**: Code generation must strictly adhere to the selected framework's syntax (React JSX vs. Vue SFC).
