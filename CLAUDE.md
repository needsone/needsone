# CLAUDE.md

## Project Overview

**Button Tracker** is a mobile-first Progressive Web App (PWA) for tracking two types of button presses ("Court"/Short and "Long") with timestamps, duration tracking, statistics, and CSV export. The entire application is a single self-contained HTML file with no external dependencies.

- **Primary language:** French (UI labels and text)
- **Tech stack:** Vanilla HTML5, CSS3, JavaScript (no frameworks, no build tools, no npm)
- **Target platform:** iOS Safari (PWA), also works in any modern browser

## Repository Structure

```
needsone/
├── README.md      # GitHub profile README (not project documentation)
├── index.html     # The entire application (HTML + CSS + JS, ~1400 lines)
└── CLAUDE.md      # This file
```

This is a **single-file application**. All markup, styles, and logic live in `index.html`.

## Architecture

### Code Organization (within index.html)

The file is structured in three sections:

1. **CSS** (lines 10-705): Embedded in `<style>` tag
   - CSS custom properties for theming (`:root` and `.light-theme`)
   - Mobile-first responsive layout (max-width 500px)
   - iOS safe-area-inset support for notched devices
   - Three view layouts: main, stats, timer
   - Settings modal (bottom sheet pattern)

2. **HTML** (lines 707-844): Four UI sections inside `.container`
   - `#mainView` — Two tracker buttons (Court/Long), last click info, undo
   - `#statsView` — Time-filtered breakdown, timeline with delete, CSV export
   - `#timerView` — Stopwatch display with large stop button
   - `#settingsModal` — Bottom sheet with toggle switches

3. **JavaScript** (lines 846-1413): Embedded in `<script>` tag
   - **Data layer:** `getData()`, `saveData()`, `getSettings()`, `saveSettings()` using localStorage
   - **Core actions:** `addPress(type)`, `startTimer()`, `stopTimer()`, `undoLast()`, `deleteEntry(timestamp)`, `clearData()`
   - **UI rendering:** `updateUI()`, `updateStatsBreakdown(filter)`, `updateTimeline()`
   - **Utilities:** `showToast()`, `createRipple()`, `exportToCSV()`, formatting functions
   - **Event listeners:** Touch-first handlers with click fallbacks for iOS compatibility

### Data Model

All data persists in `localStorage` under two keys:

- `buttonTrackerData` — `{ presses: [{ type: "short"|"long", timestamp: ISO string, duration?: number }] }`
- `buttonTrackerSettings` — `{ lightTheme: bool, vibration: bool, sound: bool, confirmDelete: bool }`

### Key Patterns

- **Touch-first event handling:** All interactive elements use `touchstart` with `preventDefault()` plus a `click` fallback guarded by a `touched` flag, to avoid double-firing on iOS
- **View switching:** Done by toggling `.hidden` / `.active` CSS classes on view containers
- **Theme system:** CSS custom properties swapped via `.light-theme` class on `<body>`
- **Feedback:** Haptic (`navigator.vibrate`), audio (Web Audio API oscillators), visual (ripple animations, toast notifications)

## Development Workflow

### Running Locally

No build step is needed. Open `index.html` directly in a browser or serve it with any static file server:

```bash
# Using Python
python3 -m http.server 8000

# Using Node.js (if available)
npx serve .
```

### Making Changes

Since everything is in one file, edits are straightforward:
- **Styling:** Modify the `<style>` block (lines 10-705)
- **Layout/markup:** Modify HTML between `<body>` tags (lines 707-844)
- **Logic:** Modify the `<script>` block (lines 846-1413)

### Testing

There is no automated test suite. Verify changes manually in a browser, preferably iOS Safari or Chrome DevTools mobile emulation. Key areas to test:
- Button press recording (both Court and Long)
- Timer start/stop and duration recording
- Stats view with all three filter modes (Heure/Jour/Mois)
- Timeline entry deletion
- Undo functionality
- CSV export
- Settings toggles (theme, vibration, sound, confirm delete)
- Touch interactions on mobile

### No Build/Lint/CI

- No `package.json`, no npm dependencies
- No linter or formatter configured
- No CI/CD pipeline
- No `.gitignore` (none needed — no build artifacts or dependencies)

## Conventions

- **Language:** All user-facing strings are in **French** (e.g., "Court", "Long", "Statistiques", "Paramètres")
- **Date/time formatting:** Uses `fr-FR` locale throughout
- **Coding style:** Vanilla JS with DOM manipulation, no classes or modules
- **CSS:** Uses custom properties for all theme colors; naming follows `--bg-*`, `--text-*`, `--accent-*` pattern
- **Commit messages:** Mix of English and French (see git log); no enforced convention

## Important Notes for AI Assistants

- The application has **zero external dependencies** — do not introduce any frameworks, libraries, or build tooling unless explicitly asked
- Keep everything in the **single `index.html` file** — do not split into separate CSS/JS files unless explicitly asked
- All UI text must remain in **French**
- Touch event handling is intentionally duplicated (touchstart + click fallback) for **iOS compatibility** — do not simplify this pattern
- The `README.md` is a **GitHub profile README**, not project documentation — do not modify it for project docs
- Data is stored in `localStorage` only — there is no backend or API
