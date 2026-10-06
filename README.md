# .NET Interview Prep Bank

A searchable, filterable Q&A reference for .NET developer interview prep, with code/SQL samples shown separately from each explanation. **Front end only, read-only** — there is no in-app editor or backend. All content lives in this repo as JSON files under `data/`, and the page fetches it at load time. This repo *is* the database: adding, editing, or removing a question means editing a file and pushing a commit.

Ships with **sample placeholder content** across 11 topics (2–3 questions each) so the app is fully functional out of the box — replace it with your real questions whenever you're ready, following the format below.

## How content is structured

```
data/
├── index.json              ← manifest: lists every topic file
└── topics/
    ├── csharp.json
    ├── csharp-advanced.json
    ├── sql.json
    ├── aspnet-mvc.json
    ├── web-api.json
    ├── dapper.json
    ├── ef-core.json
    ├── apis.json
    ├── swagger.json
    ├── azure-service.json
    ├── dto.json
    └── _example-template.json   ← starting point to copy, not registered in index.json
```

**`data/index.json`** lists every topic the app should load:

```json
{
  "version": 1,
  "topics": [
    { "id": "csharp", "label": "C#", "file": "topics/csharp.json" },
    { "id": "sql", "label": "SQL", "file": "topics/sql.json" }
  ]
}
```

**Each topic file** (e.g. `data/topics/sql.json`) is a flat array of questions:

```json
[
  {
    "id": "q1",
    "heading": "What is the difference between INNER JOIN and LEFT JOIN?",
    "parentHeading": null,
    "content": "INNER JOIN returns only matching rows... LEFT JOIN returns every row from the left table...",
    "code": "SELECT c.Name, o.OrderId\nFROM Customers c\nINNER JOIN Orders o ON o.CustomerId = c.Id;",
    "codeLanguage": "sql"
  }
]
```

Field notes:
- `id` only needs to be unique *within that file* — the app namespaces it internally as `<topicId>:<id>`.
- `heading` is the question text — what search and autocomplete match against first.
- `content` is the prose explanation. Use `\n` for line breaks, `-   ` for bullet-style lines.
- `code` is **optional**. When present, it renders in its own dark code card below the explanation, clearly separated from the prose — this is what the UI uses to keep "definition" and "sample code" visually distinct. Leave it `null` (or omit it) for purely conceptual questions with no natural code example.
- `codeLanguage` is a short label shown on the code card (`"csharp"`, `"sql"`, `"bash"`, `"http"`, etc.) — it's just a display badge, not a real syntax highlighter, so any short string works.
- `parentHeading` is optional, for clustering related questions under one scenario. `null` when not needed.

## Adding a new topic

1. Create a new file at `data/topics/<your-slug>.json` containing a JSON array (start with `[]`, or copy `data/topics/_example-template.json`).
2. Add an entry for it to `data/index.json`:
   ```json
   { "id": "your-slug", "label": "Your Topic Label", "file": "topics/your-slug.json" }
   ```
3. Commit and push to `main`.
4. If GitHub Pages is set up as described below, it redeploys automatically within a minute or two, and the new topic appears in the sidebar — no code changes needed.

## Adding a question to an existing topic

Open the relevant file under `data/topics/`, append an object to the array (give it a locally-unique `id`), commit, push. Same auto-deploy behavior.

## Editing or removing a question

Edit or delete the object in place in its topic file, commit, push. Git's own history is your version history — `git log -- data/topics/csharp.json` shows every past change to that file.

## Running locally

Because the app uses `fetch()` to load the JSON files, opening `index.html` directly from disk (a `file://` URL) will fail in most browsers. Serve the folder over HTTP instead:

```bash
npx serve .
# or
python3 -m http.server 8000
```

Then visit the printed `localhost` URL.

## Deploying to GitHub Pages

No build step is required — this is static HTML/CSS/JS.

1. Push this repo to GitHub (`git init`, `git remote add origin <url>`, `git add .`, `git commit -m "Initial commit"`, `git push -u origin main`).
2. In the repo, go to **Settings → Pages**.
3. Under "Build and deployment", set **Source** to "Deploy from a branch", branch `main`, folder `/ (root)`.
4. Save. The site publishes at `https://<your-username>.github.io/<repo-name>/` within a minute or two.
5. From then on, every push to `main` — including just adding a topic JSON file — triggers a redeploy automatically.

## What's intentionally *not* here

- **No admin UI / in-app editing** — all changes happen as commits.
- **No version history panel** — Git's own history replaces it.
- **No server or database** — the app is static files only; GitHub + GitHub Pages is the entire stack.
- Favorites (the ★ toggle) are the one piece of client-side state, stored in the visitor's own browser via `localStorage`. They're personal to that browser/device and aren't part of the content pipeline.
