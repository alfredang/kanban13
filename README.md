# IT PMO Kanban

A single-file Kanban board demo for an IT PMO team at a fictitious bank. Create tasks, filter them, and move cards between columns by drag-and-drop or a keyboard-friendly "Move" menu. All state lives in memory, so a refresh resets the board to its seed data.

**Live demo:** https://alfredang.github.io/kanban13/

## Features

- Board with columns per status, plus a summary strip counting all tasks
- Drag-and-drop and keyboard "Move ▸" fallback
- Filters (the summary strip is unaffected by filters)
- Add Task dialog with inline validation
- Inline "Delete? Yes / No" confirmation (no `alert()`/`confirm()`)
- Seed due dates computed relative to today, so some are always overdue
- Optional email notification on new tasks via FormSubmit

## Run locally

No build step. Open `index.html` in a browser.

To check JS syntax, extract the `<script>` block and run `node --check` on it (CI does this automatically).

## Tech and constraints

- Vanilla HTML/CSS/JS in one file (`index.html`); no frameworks, CDN, web fonts or images
- No persistence: no localStorage, sessionStorage, IndexedDB or cookies
- Not affiliated with, and does not imitate, any real bank's system

## Configuration

New-task notifications are sent with [FormSubmit](https://formsubmit.co). Set `FORMSUBMIT_ENDPOINT` at the top of the script in `index.html` to `https://formsubmit.co/ajax/<your-email>`. FormSubmit needs a one-time activation: the first submission sends a confirmation email that must be clicked.

With the default placeholder, new tasks still appear on the board but a "notification failed" warning toast is shown. When enabled, each new task's fields (ID, title, description, project, category, assignee, priority, due date, status) are sent to formsubmit.co.

## Deployment

GitHub Actions (`.github/workflows/deploy.yml`) syntax-checks the script and deploys `index.html` to GitHub Pages on every push to `main`.
