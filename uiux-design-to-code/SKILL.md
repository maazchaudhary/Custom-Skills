---
name: uiux-design-to-code
description: >
  Converts UI/UX designs (images, wireframes, Figma screenshots, sketches, or text descriptions)
  into fully-structured, multi-file applications — including frontend, backend, database schema,
  API routes, config files, and everything needed to run the app. Use this skill whenever the user
  shares a design mockup, screenshot, wireframe, or says things like "build this", "code this design",
  "turn this into an app", "implement this UI", "make this design real", "convert this mockup",
  or "build the full stack for this". Also trigger when the user uploads any image of an interface
  and wants it built. This skill goes beyond just HTML — it scaffolds the entire project architecture.
---

# Design-to-Code: Full-Stack App Generator

Turn any UI/UX design — image, screenshot, wireframe, sketch, or text description — into a
complete, runnable multi-file application with frontend, backend, database, and config.

---

## Phase 1: Analyze the Design

Before writing a single line of code, study the design carefully:

**Visual inventory:**
- List every screen / page visible
- Identify all UI components (nav, forms, tables, cards, modals, etc.)
- Note color palette, typography style, spacing rhythm
- Spot interactive elements (buttons, inputs, dropdowns, toggles)

**Data inventory:**
- What entities exist? (users, products, posts, orders, etc.)
- What relationships exist between them?
- What data does each screen display or mutate?

**Feature inventory:**
- What actions can users take?
- Is auth required?
- Are there roles or permissions?
- Any real-time features, file uploads, search, filters?

**Tech signal:**
- Are there any tech hints in the design? (framework logos, file names, etc.)
- If not, pick sensible defaults (see stack selection below)

---

## Phase 2: Choose the Stack

Pick the stack that fits the complexity and hints from the design. When in doubt, use these defaults:

| Layer | Default Choice | Alternative |
|---|---|---|
| Frontend | React + Tailwind CSS | Plain HTML/CSS/JS for simple pages |
| Backend | Node.js + Express | Python/FastAPI if data-heavy |
| Database | SQLite (via better-sqlite3) | PostgreSQL schema if scale implied |
| Auth | JWT + bcrypt | Sessions for simpler apps |
| Package mgr | npm | — |

**Always tell the user your stack choice before generating files, in one sentence.**

---

## Phase 3: Plan the Architecture

Before generating files, output a brief architecture plan in this format:

```
📁 Project Structure
├── /frontend        → React app (or static HTML)
│   ├── src/
│   │   ├── components/   → reusable UI pieces
│   │   ├── pages/        → route-level views
│   │   ├── hooks/        → custom React hooks
│   │   └── api.js        → all fetch calls to backend
│   └── package.json
├── /backend         → Express API
│   ├── routes/      → one file per resource
│   ├── middleware/  → auth, error handling
│   ├── db/          → schema + seed
│   └── server.js
├── /shared          → types or constants used by both (optional)
├── .env.example     → env vars needed
├── README.md        → how to run
└── docker-compose.yml (if DB warranted)
```

Adjust the tree to match the actual design. Show only what you'll actually create.

---

## Phase 4: Generate All Files

Generate **every file** — don't stub, don't say "add your logic here". Each file should be
complete and functional.

### Order of generation

1. **`README.md`** — setup steps, env vars, how to run dev + prod
2. **`.env.example`** — all required environment variables with descriptions
3. **Database schema** — SQL `CREATE TABLE` statements or ORM models; include seed data if useful
4. **Backend entry point** — `server.js` or `main.py`; middleware, routes, DB connection
5. **Backend routes** — one file per resource (users, products, etc.); full CRUD where needed
6. **Auth logic** — registration, login, JWT issue/verify, middleware guard
7. **Frontend entry** — `index.html` or `main.jsx`; router setup
8. **Frontend pages** — one file per screen in the design
9. **Frontend components** — extracted reusable pieces
10. **API client** — centralized fetch wrapper with base URL + token attach
11. **Config files** — `package.json`, `vite.config.js`, `tailwind.config.js`, etc.

### File generation rules

- **No placeholders.** Write real logic, real SQL, real JSX. If you need a value you don't have, use a sensible default and note it in a comment.
- **Match the design precisely.** Colors, layout, component names, field names — mirror what's in the design.
- **Backend ↔ Frontend contract must match.** If the frontend calls `POST /api/users`, the backend must have that exact route.
- **DB schema must match the API.** Column names must match what the routes read/write.
- **Error handling everywhere.** Try/catch on async, proper HTTP status codes, friendly error messages.
- **Comments on non-obvious logic only.** Don't narrate every line — explain the why, not the what.

---

## Phase 5: Present the Output

After all files are generated:

1. **Show the file tree** (what was created)
2. **Show each file** with its path as a header, in the order from Phase 4
3. **End with a "How to run" block:**

```bash
# How to Run

## Backend
cd backend
npm install
cp .env.example .env   # fill in values
npm run dev

## Frontend
cd frontend
npm install
npm run dev
```

If there's a Docker Compose, show that too.

---

## Design Fidelity Guidelines

### Colors
Extract the exact colors from the design. If the design image has a blue button, use that exact
blue — don't substitute with a Tailwind default. Use CSS variables for the color palette:

```css
:root {
  --color-primary: #2563eb;   /* extracted from design */
  --color-bg: #f8fafc;
  --color-text: #1e293b;
}
```

### Typography
- Match font weight, size, and hierarchy from the design
- Use Google Fonts if a specific font is identifiable; otherwise use a close match
- Preserve heading/body contrast ratios

### Layout
- Recreate spacing, padding, grid structure as faithfully as possible
- Mobile responsiveness: if the design is desktop-only, still add basic responsive breakpoints
- Preserve alignment: left-aligned content stays left-aligned

### Components
Name components after what they represent in the design, not generic names:
- ✅ `ProductCard`, `OrderTable`, `NavSidebar`, `CheckoutForm`
- ❌ `Card1`, `Table`, `Nav`, `Form`

---

## Handling Incomplete Designs

If the design doesn't show all screens:

- **Infer CRUD screens** — if a design shows a list view, create a detail view and a create/edit form
- **Infer auth screens** — if the app has user data, add login + register pages
- **Infer empty states** — every list needs an empty state
- **Note what you inferred** — add a comment block at the top of inferred files: `// Inferred from design — not shown explicitly`

---

## Multi-Screen Designs

If the design has multiple screens/tabs:

- Create one page component per screen
- Wire them together with React Router (or equivalent)
- Maintain consistent nav/layout across pages
- Preserve any active/selected state visible in the design

---

## Quality Checklist (run mentally before finalizing)

- [ ] Every screen in the design has a corresponding page/component
- [ ] Every form has a corresponding API route and DB column
- [ ] Auth is gated where the design implies it (dashboard, profile, admin)
- [ ] All fetch calls match actual backend routes
- [ ] `.env.example` lists every `process.env.*` used in the code
- [ ] README has enough info for a stranger to run the app
- [ ] No `TODO` or `// implement this` stubs left behind
