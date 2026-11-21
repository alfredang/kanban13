# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-file IT PMO Kanban demo for a fictitious bank: everything lives in `index.html` (CSS in one `<style>`, JS in one `<script>`). There is no build, package manager, linter or test suite. Run it by opening `index.html` in a browser; check JS syntax by extracting the script and running `node --check` on it.

## Hard constraints (from the original spec)

- Vanilla HTML/CSS/JS only. No frameworks, CDN, web fonts or image files. Icons are Unicode or inline SVG.
- No persistence: never use localStorage, sessionStorage, IndexedDB or cookies. Refresh resets the board to seed data (the UI says so).
- No `!important`, no `alert()`/`confirm()` (use inline errors and the inline "Delete? Yes / No" confirm).
- Do not use real UOB logos or trademarks, or imitate an official UOB system. The wordmark is the neutral text "IT PMO". Task IDs (`UOB-ITPM-####`) and the email subject prefix follow the spec literally.
- FormSubmit AJAX is the only backend. The user's email may appear only in `FORMSUBMIT_ENDPOINT`. `notifyNewTask()` must stay wrapped so a failure never breaks the board (it shows a warning toast and keeps the card).

## Architecture

- **Single state object:** `state = { tasks: [], filters: {} }`. The UI is re-rendered from it by `renderBoard()` and `renderSummary()`, which rebuild the DOM from escaped HTML strings. Always pass user-supplied text through `escapeHtml()`.
- **Transient UI state** lives in a separate `ui` object (open Move panel, delete-confirm id, dragging id, `focusSelector`). Because every render replaces the DOM, `ui.focusSelector` is how keyboard focus is restored afterwards.
- **Event delegation:** all card interactions (click, dragstart/dragend/dragover/dragleave/drop, keydown) are bound once on `#board`. Don't attach listeners to individual cards.
- **Mutations go through** `addTask`, `moveTask` and `deleteTask`, which update `state.tasks` and re-render. Drag-and-drop and the keyboard "Move ▸" fallback both call `moveTask`.
- **Filters vs summary:** the board columns use filtered tasks, but the summary strip counts all tasks.
- **Dates:** compare ISO `YYYY-MM-DD` strings built from local time (`toISODate`), not UTC, to avoid timezone shifts. Seed due dates are computed relative to today so some are always overdue.
- **Add Task form:** a `<dialog>` opened with `showModal()`. `validateTask` produces inline errors. On submit the flow is: validate, optimistic `addTask`, reset form, success toast, then `notifyNewTask` with the button disabled and showing "Sending…".
- The script is divided into numbered commented sections (CONFIG, STATE, UTILITIES, SEED, FILTERING, RENDERING, ACTIONS, BOARD EVENTS, TOASTS, FORMSUBMIT, FORM, DIALOG HELPERS, INIT). Keep new code in the matching section.

## Configuration

Change `FORMSUBMIT_ENDPOINT` at the top of the script (currently the placeholder `YOUR_EMAIL@example.com`). FormSubmit needs a one-time activation: the first submission triggers a confirmation email that must be clicked. With the placeholder, new tasks still appear but show the "email notification failed" warning toast. An HTML comment above the `<script>` documents this.
