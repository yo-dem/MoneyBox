# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

MoneyBox is a **zero-dependency, single-file** personal net worth tracker. The entire app lives in `money-box.html` (~3000 lines). There is no build system, no package manager, no test suite, and no linter. To run the app, open `money-box.html` in a browser.

## Architecture

Everything is inside one IIFE in `money-box.html`:

- **Lines 8–1225**: CSS — 5 themes (`light`, `dark`, `ocean`, `forest`, `sunset`) controlled via `data-theme` on `<html>`. All colors use CSS custom properties.
- **Lines 1227–1416**: HTML — header, toolbar, transaction table, 3-panel summary grid, modals.
- **Lines 1418–3051**: JavaScript — all logic in a single IIFE.

### JavaScript Structure

**State** — single object at the top of the IIFE:
```js
state = { income: [], causali_custom: [], chartCurrencyOrder: [], statsCurrencyOrder: [] }
```

**Persistence** — `saveToStorage()` / `loadFromStorage()` → `localStorage` key `patrimonio_tracker`. Saved on every mutation.

**Rendering** — orchestrated by `render()`, which calls:
- `renderTable()` — transaction list with filtering
- `renderPatrimonio()` — total net worth (per currency)
- `renderChart()` — Chart.js pie charts (Chart.js v4.4.1 from CDN)
- `renderBreakdown()` — category breakdown with percentage bars
- `renderStats()` — per-currency statistics

**i18n** — `STRINGS` object with 6 languages (IT, EN, RU, ES, FR, DE); stored in `localStorage` key `patrimonio_tracker_lang`. DOM elements use `data-i18n-*` attributes; updated by `applyTranslations()`.

**Key constants** (top of script):
- `STORAGE_KEY`, `THEME_KEY`, `LANG_KEY` — localStorage keys
- `DEFAULT_CAUSALI` — built-in categories (`["Risparmio", "Stipendio"]`)
- `CURRENCIES` — `[EUR, USD, GBP, CHF, JPY]`
- `PALETTE` — 15 colors for category charts
- `THEMES`, `LANGS` — available theme/language IDs

### Patterns to Follow

- **No XSS**: Always use `escapeHtml()` when inserting user data into HTML strings.
- **FLIP animation**: Used for drag-to-reorder of currency/stats groups; preserve the pattern when touching those lists.
- **Amount parsing**: `parseAmount()` handles both `.` and `,` as decimal separators.
- **Multi-currency**: Amounts are stored with their currency; aggregation happens at render time, not at storage time.
- **Add/Edit flow**: The same income modal handles both; distinguish by whether an `editIndex` is set in modal state.
