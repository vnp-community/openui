# OpenUI Frontend Technical Design and Architecture

## 1. System Overview
OpenUI is a web application that leverages AI to generate and preview user interfaces. It provides a collaborative environment for designers and developers to iterate on UI components using natural language prompts.

## 2. Tech Stack
- **Framework**: [React 18](https://reactjs.org/)
- **Build Tool**: [Vite](https://vitejs.dev/)
- **State Management**: [Jotai](https://jotai.org/) (Atomic state) & [TanStack Query](https://tanstack.com/query/latest) (Server state)
- **Styling**: [Tailwind CSS](https://tailwindcss.com/) & [Radix UI](https://www.radix-ui.com/) (Headless components)
- **Editor**: [Monaco Editor](https://microsoft.github.io/monaco-editor/)
- **Icons**: [Lucide React](https://lucide.dev/)
- **Routing**: [React Router 6](https://reactrouter.com/)
- **Localization**: [i18next](https://www.i18next.com/)
- **Testing**: [Vitest](https://vitest.dev/), [Playwright](https://playwright.dev/), [MSW](https://mswjs.io/)

## 3. Architecture

### 3.1. Directory Structure (`src/`)
- `api/`: API service layer using `fetch`.
- `components/`: UI components, including `ui/` for Radix-based primitives.
- `hooks/`: Custom React hooks.
- `layout/`: Layout components (e.g., `Scaffold`).
- `lib/`: Utility libraries and configurations (e.g., `i18n`, `utils`).
- `pages/`: Page-level components.
- `state/`: Jotai atoms for global and persistent state.

### 3.2. State Management
OpenUI uses **Jotai** for fine-grained atomic state management.
- **Persistent State**: Managed via `atomWithStorage` for user preferences (dark mode, facets).
- **Local Database**: Uses `jotai-minidb` for larger datasets like image history.
- **Atom Families**: Used for managing collections of items (e.g., `historyAtomFamily`).

### 3.3. Routing
The application uses `createBrowserRouter` with specific routes:
- `/`: Automatically redirects to `/ai`.
- `/ai`: Main AI generation interface.
- `/ai/:id`: View a specific history item.
- `/ai/shared/:id`: View a shared UI component.

## 4. Key Features

### 4.1. AI Integration
The application communicates with backend LLM services (OpenAI, Groq, Ollama) via the `/v1/` API endpoints. It supports streaming responses for real-time UI generation.

### 4.2. Code Editor & Preview
- **Editor**: Integrated Monaco Editor for manual adjustments to the generated HTML/Tailwind code.
- **Preview**: Real-time rendering of the generated code within a sandboxed environment.
- **Annotation**: `HtmlAnnotator` allows users to interact with specific parts of the generated UI for refinements.

### 4.3. Authentication
Uses **WebAuthn** (Passkeys) for a passwordless authentication experience.
- `src/api/openui.ts` handles the `register` and `auth` flows using `navigator.credentials`.

## 5. Tooling & DX
- **Vite**: Provides fast HMR and optimized builds.
- **Vercel**: Configuration included for easy deployment.
- **Quality Control**:
    - **Linting**: ESLint with a strict configuration.
    - **Formatting**: Prettier with Tailwind plugin.
    - **Styles**: Stylelint for CSS maintenance.
    - **Tests**: Comprehensive suite including unit (Vitest) and E2E (Playwright).

## 6. Routing and Navigation

### 6.1. Route Configuration
The application uses `react-router-dom` with a history-based router.

| Path | Component | Description |
| :--- | :--- | :--- |
| `/` | `Navigate` | Redirects directly to `/ai`. |
| `/ai` | `AI` | The main interface. Routes to `/ai/new` if no ID is provided. |
| `/ai/:id` | `AI` | Loads a specific UI session from local history. |
| `/ai/shared/:id` | `AI` | Read-only view for a shared UI component. |

### 6.2. Navigation Flows
Navigation is handled both declaratively (via `<Link>`) and programmatically (via `useNavigate`).

- **New Session**: Triggered by clicking the "New UI" button or the application Logo. Navigates to `/ai/new`.
- **History Access**: Selecting an item in the `History` sidebar navigates to `/ai/:id` and closes the sidebar on mobile/small screens.
- **Deletion**: After deleting a history item from the sidebar dropdown, the app automatically navigates back to `/ai/new`.
- **Sharing**: Sharing an item provides a URL pointing to `/ai/shared/:id`, which the recipient can use to view the UI without authentication or local history.

## 7. Screens and Structure

### 6.1. Main AI Interface (`/ai/:id`)
The primary workspace for UI generation and iteration. It uses a flexible grid layout with collapsible sidebars.

- **Top Navigation (`NavBar`)**: Provides branding, the current UI's title (emoji + name), global actions (Settings, New UI), and user profile access.
- **History Sidebar (Left)**: A collapsible panel listing previous sessions, grouped by date (Today, Previous 7/30 days).
- **Central Workspace (`Chat`)**:
    - **Header**: Displays current version information, the full prompt used, and actions like Download and Version Toggle.
    - **Preview Area (`HTMLAnnotator`)**: Renders the generated UI. Users can click elements to annotate or request specific changes.
    - **Input Section (`Prompt`)**: A persistent input area at the bottom for natural language instructions, including support for image uploads for vision-capable models.
- **Versions Sidebar (Right)**: A collapsible panel showing thumbnails/previews of all iterations for the current UI, allowing quick rollback or comparison.

### 6.2. Shared UI View (`/ai/shared/:id`)
A read-only mode for viewing shared components. It reuses the central workspace but disables editing actions and persistent state modifications.

### 6.3. Overlays and Modals
- **Settings Dialog**: Managed via `radix-ui/react-dialog`. Configures:
    - Target Framework (HTML, React, Vue, etc.)
    - UI Theme (e.g., Shadcn, Tailwind)
    - LLM Model selection (OpenAI, Groq, Ollama)
- **Share Dialog**: Generates a shareable URL for the current UI version.
- **Authentication**: Managed via a persistent `Register` overlay that handles WebAuthn flows.
