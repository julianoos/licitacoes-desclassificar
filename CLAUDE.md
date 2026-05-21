# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Licitações 2026** is a Portuguese-language single-page application (SPA) for managing procurement tenders and related documentation. The entire application is contained in a single `index.html` file (~4,156 lines) with embedded CSS and JavaScript.

**Deployed on**: Vercel  
**Type**: Web application (no build step)

## Architecture

The application is a client-side only SPA with the following structure:

### Core Components

**Authentication System** (lines 783-~900)
- Login, registration, password recovery flows
- Uses localStorage for session persistence
- SHA-256 password hashing via Web Crypto API

**Navigation & Sections** (data-section attributes)
- `acompanhamento` — Follow-up/monitoring view with tables and filters
- `calculadora` — Calculator functionality
- `editalreader` — Tender document reader (uses PDF.js, XLSX, Mammoth libraries)
- `juridico` — Legal section with forms and data entry
- `fornecedores` — Suppliers/vendors management

**UI Framework**
- Custom CSS with CSS variables for dark/light theme toggle
- Flex/grid layouts for responsive design
- External fonts: Google Sans, DM Sans, Inter
- External libraries (loaded via CDN):
  - pdf.js (3.11.174) — PDF processing
  - xlsx (0.18.5) — Excel file handling
  - mammoth (1.6.0) — Word document parsing

**Data Storage**
- All data persists via localStorage with key patterns like `key_userId`
- User data stored in `auth_users`, sessions in `auth_session`
- Search and filter state restoration between page reloads

## Development Workflow

### Making Changes

1. **Edit index.html** — All changes go into the single HTML file
2. **Test locally** — Open `index.html` in a browser or use a local server (no build step needed)
3. **Commit to git** — Use conventional commit messages in Portuguese
4. **Deploy to Vercel** — Run `vercel --prod --yes` to push to production

### Key CSS Sections to Know

- Root variables (lines ~16-30) define the dark/light theme colors
- `.grupo` and `.grupo-header` — Spacing for grouped content (keep compact: ≤16px and ≤8px respectively)
- `.ck-item-header` — Checklist item headers (≤7px margin)
- Responsive breakpoints at 700px and 450px for mobile layouts

### JavaScript Organization

Within the `<script>` tag (starting line 780):
- **AUTH** (lines 783+) — Authentication logic
- **SECTIONS** — Navigation and view rendering
- **DATA MANAGEMENT** — localStorage interaction
- **UI RENDERING** — DOM manipulation and event handlers

## Common Tasks

### Edit a section's UI or logic
Edit the relevant HTML/CSS/JavaScript directly in index.html. For example:
- Section markup: search for `data-section="sectionname"`
- Section styles: search for `.sectionname`
- Section logic: search for function names like `renderSectionname()`

### Add a new feature
1. Add HTML markup in the appropriate section
2. Add CSS rules (can be inline in `<style>` or grouped by section)
3. Add JavaScript logic in the `<script>` tag, organized by section comment headers

### Test authentication
Log in with test credentials stored in localStorage. Run this in the browser console to see users: `console.log(JSON.parse(localStorage.getItem('auth_users')))`

### Deploy changes
```
vercel --prod --yes
```

## Important Notes

- **No build step** — changes are immediate, just edit and refresh
- **localStorage is single-domain** — test in the deployed environment or localhost
- **External libraries loaded via CDN** — ensure internet access for PDF/Excel/Word processing to work
- **Responsive design** — test viewport changes at 700px and 450px breakpoints
- **Theme toggle** — CSS variables handle dark/light mode seamlessly

## Git Workflow

- Keep commits focused and use Portuguese commit messages
- Follow the pattern of recent commits (e.g., `feat: ...`, `fix: ...`)
- Always commit and push after changes (configured in memory)
