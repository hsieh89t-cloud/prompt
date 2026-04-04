# CLAUDE.md — AI 個性化架構師

This file provides guidance for AI assistants working on this repository.

---

## Repository Overview

A single-file static web application — **AI 個性化架構師** (AI Personality Architecture Assistant) — powered by Perplexity Pro. The app guides users through a 12-dimension questionnaire to generate a personalized AI system prompt.

- **Language/UI:** Traditional Chinese (zh-TW)
- **Stack:** Pure HTML + embedded CSS — no JavaScript framework, no build system, no package manager
- **Single source file:** `index.html` (the entire application lives here)

---

## File Structure

```
/
└── index.html    ← Entire application: HTML structure + embedded CSS + (incomplete) JS
```

No other source files, config files, or dependencies exist.

---

## Known Issue: Truncated File

`index.html` is **incomplete**. It ends abruptly at line 301 mid-HTML, inside the `.result-section` div. The file is missing:

- Completion of the result section HTML (`<div class="result-subtitle">`, copy button, restart button, closing tags)
- All JavaScript logic (theme toggle, questionnaire flow, option rendering, progress tracking, result generation)
- Closing `</body>` and `</html>` tags

When working on this file, be aware the JavaScript section needs to be written or restored. The CSS design system (lines 12–237) and HTML skeleton (lines 239–301) are intact and should be preserved.

---

## Architecture

### CSS Design System (lines 12–237)

All visual tokens are CSS custom properties defined in `:root` / `[data-theme="light"]` and overridden in `[data-theme="dark"]`.

**Key token groups:**
- `--color-*` — Background, surface, text, primary, accent, semantic colors
- `--shadow-*` — Elevation shadows
- `--radius-*` — Border radius scale (sm → full)
- `--transition` — Standard easing (`180ms cubic-bezier(0.16,1,0.3,1)`)
- `--font-body` — `'Noto Sans TC', 'Inter', sans-serif`
- `--text-*` — Fluid font sizes via `clamp()`
- `--space-*` — Spacing scale (1 = 0.25rem … 16 = 4rem)

**Themes:** `data-theme="dark"` (default) / `data-theme="light"` on `<html>`. Toggle is handled by JS setting this attribute.

### HTML Structure (lines 239–301+)

```
.app-wrapper (max-width: 760px)
├── <header> .header
│   ├── .logo (icon + title + subtitle)
│   └── button.theme-toggle [data-theme-toggle]
├── .progress-section #progress-section
│   ├── .progress-label #progress-dim
│   ├── .progress-fraction #progress-frac
│   ├── .progress-fill #progress-fill  ← width % animated
│   └── .progress-steps #progress-steps ← step dots
├── .chat-container #chat-container
│   └── .message / .typing (AI typing indicator initially visible)
├── .options-section #options-section (hidden initially)
│   ├── .options-title #options-title
│   ├── .options-grid #options-grid ← option buttons injected here
│   └── .custom-input-section
│       ├── input.custom-input #custom-input
│       └── button.send-btn #send-btn
└── .result-section #result-section (hidden initially, shown on completion)
    └── [incomplete — needs result-subtitle, .code-block, copy/restart buttons]
```

### Questionnaire Flow (to be implemented/restored)

- 12 dimensions covering personality/style preferences
- Each dimension shows AI message + multiple-choice options (keyboard shortcuts 1–9)
- User can type a custom answer in `.custom-input` instead
- Progress bar and step dots update after each answer
- On completion, `.result-section` becomes visible with the generated system prompt

---

## Development Workflow

### Running Locally

No build step. Open `index.html` directly in a browser or serve it:

```bash
# Python
python3 -m http.server 8080

# Node
npx serve .
```

### Editing

Edit `index.html` directly. The file structure is:
1. `<head>` with meta tags and Google Fonts link (lines 1–11)
2. `<style>` block with all CSS (lines 12–237)
3. HTML body (lines 238–301+)
4. `<script>` block with all JS (missing — needs to be added before `</body>`)

### Testing

Manual browser testing only — no test framework. Check:
- Theme toggle (light ↔ dark)
- Questionnaire flow (all 12 dimensions)
- Keyboard shortcuts (number keys for options)
- Custom text input + submit
- Progress bar and step dot updates
- Result section display and copy button
- Responsive layout at ≤600px
- Reduced-motion behavior (`prefers-reduced-motion`)

---

## Coding Conventions

### CSS

- All CSS lives in the single `<style>` block — **no external stylesheets**
- CSS is minified (properties on one line per selector) — maintain this style
- Always use CSS variables (`var(--color-*)`, `var(--space-*)`, etc.) — never hardcode values
- Use `oklch()` color functions for opacity variants: `oklch(from var(--color-primary) l c h / 0.15)`
- Animations should always include a `@media(prefers-reduced-motion:reduce)` fallback

### HTML

- `lang="zh-TW"` on `<html>` — all UI text is Traditional Chinese
- Include `aria-label` on all icon-only interactive elements
- Use semantic elements (`<header>`, `<button>`, not `<div onclick>`)
- IDs are used for JS DOM queries; classes are used for styling

### JavaScript (when adding/restoring)

- No framework — vanilla JS only
- Place all JS in a single `<script>` tag just before `</body>`
- Theme toggle: toggle `data-theme` attribute on `<html>` between `"light"` and `"dark"`
- Keyboard shortcuts: listen on `document.keydown` for keys `1`–`9`
- Typing animation: show `.typing.visible` while simulating AI "thinking", then hide and inject `.message` bubble

### Content / Copy

- All user-facing text must be Traditional Chinese
- Maintain the existing tone: professional, clean, minimal
- The 12 questionnaire dimensions define AI personality axes (style, tone, depth, format, etc.)

---

## Deployment

Static file — deploy anywhere that serves HTML:
- GitHub Pages
- Netlify / Vercel (drag-and-drop or git integration)
- Any CDN or HTTP server

No environment variables, secrets, or server-side logic required. The Perplexity Pro integration is client-side (via the app's questionnaire logic feeding into the Perplexity interface).

---

## Git

- **Main branch:** `main`
- **Active development branch:** `claude/add-claude-documentation-ePVO5`
- All commits should target the active branch; do not push directly to `main` without a PR
- Commit messages should be descriptive (the project history uses plain English summaries)
