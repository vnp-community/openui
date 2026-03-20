# OpenUI Frontend Code Structure

## 1. Directory Overview (`src/`)

```text
src/
├── api/             # API service layer (LLM & Session)
├── components/      # UI Components
│   ├── ui/          # Design system primitives (Shadcn/Radix)
│   └── ...          # Feature-specific components (Chat, Prompt, etc.)
├── hooks/           # Custom React hooks (logic reuse)
├── lib/             # Utility libraries & configurations
├── pages/           # Page-level containers (e.g., AI interface)
├── state/           # Jotai atoms & state management
│   └── atoms/       # Domain-specific state definitions
├── mocks/           # MSW mock definitions for testing
├── __tests__/       # Global test suite
└── main.tsx         # Application entry point
```

## 2. Module Responsibilities

### 2.1. API Layer (`src/api/`)
- `openui.ts`: Handles session management, component sharing, and WebAuthn (authentication).
- `openai.ts`: Manages communication with LLM providers (streaming, prompts).
- `models.ts`: Defines available LLM models and their capabilities (e.g., vision support).

### 2.2. Components (`src/components/`)
- **Feature Components**:
    - `Chat.tsx`: Coordinates the preview, prompt, and sidebar interaction.
    - `HTMLAnnotator.tsx`: Handles the interactive UI preview and element annotation.
    - `Prompt.tsx`: Manages user input and image uploads for the LLM.
- **Support Components**:
    - `History.tsx` & `Versions.tsx`: Manage collapsible sidebars for session and iteration lists.
    - `CodeEditor.tsx`: Integrates Monaco Editor for manual code changes.
- **UI Primitives (`ui/`)**: Low-level, reusable components like `Button`, `Dialog`, and `Input`.

### 2.3. State Management (`src/state/`)
Uses **Jotai** for a flat, atomic state model.
- `atoms/history.ts`: Typed state for session history and individual item wrappers.
- `atoms/ui.ts`: Manages UI state (rendering progress, errors, active version).
- `index.ts`: Exports atoms and configures persistence (e.g., `atomWithStorage`).

### 2.4. Utilities (`src/lib/`)
- `html.ts`: Logic for wrapping and cleaning generated HTML/Tailwind code.
- `markdown.ts`: Safely handles LLM output parsing.
- `themes.ts`: Configuration for different UI generated styles (Shadcn, Tailwind, etc.).
- `utils.ts`: Common helpers (formatting, file downloads, image resizing).

### 2.5. Custom Hooks (`src/hooks/`)
- `useMediaQuery`: Reactively handles responsive breakpoints.
- `useVersion`: Synchronizes the active version between the workspace and the sidebar.

## 3. Core Patterns
- **Atomic Components**: Separating design system primitives from feature-specific logic.
- **Persistent State**: User settings and session history are stored locally via Jotai.
- **Programmatic Navigation**: Uses `useNavigate` for handling session life-cycles (e.g., redirecting to `/new` after deletion).

## 4. External Libraries

### 4.1. Core Framework & Routing
- **React 18**: Core UI library.
- **React Router 6**: Client-side routing.
- **Vite**: Modern build tool and dev server.

### 4.2. State Management & Data Fetching
- **Jotai**: Main atomic state management library.
- **Jotai MiniDB**: Local storage persistency for complex objects.
- **TanStack Query (v5)**: Asynchronous state and API synchronization.

### 4.3. UI & Styling
- **Tailwind CSS**: Utility-first CSS framework.
- **Radix UI Primitives**: Accessible, headless UI components (Dialog, Dropdown, Slider, etc.).
- **Lucide React & Radix Icons**: Comprehensive icon sets.
- **Tailwind Merge / CVA**: Utilities for managing conditional Tailwind classes.

### 4.4. Editor & Content
- **Monaco Editor**: High-performance code editor core.
- **React Markdown / Unified / Remark**: Toolchain for parsing and rendering markdown and LLM outputs.
- **React Syntax Highlighter**: Code snippet styling in the chat.

### 4.5. Specialized Utilities
- **i18next**: Internationalization framework.
- **WebAuthn (Native)**: Used for passwordless authentication.
- **Jotai DevTools**: State inspection during development.
- **MSW (Mock Service Worker)**: API mocking for testing and local development.
