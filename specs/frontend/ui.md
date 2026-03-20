# OpenUI Frontend UI Design Document

## 1. Design Philosophy
OpenUI follows a modern, clean, and functional aesthetic, prioritizing clarity in the UI generation workspace. It leverages a "neutral with accents" palette to ensure the generated UI components remain the focal point.

## 2. Visual Identity

### 2.1. Typography
- **Primary Font**: [Source Sans 3](https://fonts.google.com/specimen/Source+Sans+3)
- **Scale**: Uses standard Tailwind typography scales for headings and body text.
- **Weight**: Varies from Extra Light (200) to Black (900).

### 2.2. Color Palette (HSL-based)
The design uses HSL variables for easy theme switching (Dark/Light).

| Variable | Light Value (Example) | Dark Value (Example) | Description |
| :--- | :--- | :--- | :--- |
| `--background` | `0 0% 100%` | `240 10% 3.9%` | Main page background. |
| `--foreground` | `240 10% 3.9%` | `0 0% 98%` | Primary text color. |
| `--primary` | `186 80% 41%` | `186 80% 41%` | Brand accent (Teal-like). |
| `--secondary` | `240 4.8% 95.9%` | `240 3.7% 15.9%` | Sidebar/Muted backgrounds. |
| `--border` | `240 5.9% 90%` | `240 3.7% 15.9%` | Divider and border lines. |

### 2.3. Dark Mode
Implemented via a `.dark` class on the root element. State is persisted in local storage and can sync with system preferences.

## 3. Component System (Atomic Design)
The UI is built using a composition of atomic primitives, largely based on **Shadcn UI** patterns and **Radix UI** headless components.

### 3.1. Foundation (`src/components/ui`)
- **Action**: `Button`, `DropdownMenu`, `Select`, `Switch`.
- **Input**: `Input`, `Textarea`, `Slider`, `Checkbox`, `Label`.
- **Feedback**: `Tooltip`, `HoverCard`, `Popover`.
- **Layout**: `Avatar`, `Dialog`, `Sheet`.

### 3.2. Layout Patterns
- **Three-Column Grid**: Flexible layout with collapsible left (History) and right (Versions) sidebars.
- **Mobile Safe Container**: Custom CSS utility (`.mobile-safe-container`) using `env(safe-area-inset-*)` to handle notches and rounded corners on mobile devices.
- **Responsive Wrapper**: `.code-responsive-wrapper` uses a grid-based SVG background to provide context for UI previews.

## 4. Interaction & Motion

### 4.1. Keyframes & Animations
Managed via Tailwind extensions:
- **Slide-in/out**: Smooth transitions for sidebars (`translateX`).
- **Wiggle-Zoom**: Micro-animation for attention-grabbing elements.
- **Accordion**: Vertical expansion/contraction for collapsible sections.

### 4.2. Responsive Design
- **Breakpoints**: Standard Tailwind breakpoints (`sm`, `md`, `lg`, `xl`).
- **Sidebar Behavior**: Sidebars typically transition from persistent grid columns on large screens to absolute-positioned overlays on smaller screens.

## 5. Implementation Notes
- **Utility-First**: Extensive use of Tailwind CSS for styling.
- **Class Merging**: Uses `clsx` and `tailwind-merge` for robust conditional styling.
- **Icons**: Standardized on `Lucide React` and `Radix Icons`.
