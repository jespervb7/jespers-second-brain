# Copilot instructions for jespers-second-brain

## What this repo is

A personal "second brain" documentation site built with **MkDocs + Material theme**, deployed as a static site. It is content-first: almost every change is a Markdown file under `docs/`, not application code. The author is an Analytics Engineer, so most content leans toward Power BI, Tabular Editor, SQL, dbt/data modelling, and Git/dev workflow topics.

There is no application logic to build or test — `main.py` is a placeholder. The only "build" is `mkdocs build`/`mkdocs serve`.

## Repo layout

- `docs/` — all content, organized by type:
  - `guides/` — reference articles / how-tos for tools, Git, productivity
  - `sops/` — Standard Operating Procedures (step-by-step, repeatable). Use `docs/snippets/templates/SOPs.md` as the template for new SOPs — keep its numbered section structure (Purpose, Scope, Roles, Preconditions, Inputs/Outputs, Procedure, Decision Points, Exception Handling, Rollback, etc.), even if some sections are trimmed for a lightweight personal SOP.
  - `knowledge/` — durable notes/concepts (e.g. Tabular Editor, Power BI, Data Modelling)
  - `snippets/` — reusable code, prompts, and templates, split into subfolders per kind (`SQL/`, `TA C# scripts/`, `tmdl/`, `prompts/`, `templates/`)
  - `ideas/` — unpolished/backlog ideas, not final documentation
  - `workflows/` — the author's own process notes (planning, review, execution)
  - `errors/` — troubleshooting/recovery notes for specific errors encountered
  - `blog/` — learning logs, retrospectives, experiments
  - `tags.md`, `about.md`, `resources.md`, `index.md` — site-level pages
- `mkdocs.yml` — site config, theme, plugins, and the **nav tree** (see below)
- `pyproject.toml` / `uv.lock` — Python deps for MkDocs (managed with `uv`)
- `.authors.yml` — used by the `meta`/authors setup for attributing pages

## Conventions to follow when adding or editing content

1. **Every new page must be added to `mkdocs.yml`'s `nav:` section**, under the matching category, in a position consistent with existing entries. A page not in `nav` won't appear in the site navigation even though it builds.
2. **Match the existing folder-per-category pattern.** Put a new page in the folder matching its type (SOP → `docs/sops/`, reusable code → `docs/snippets/<kind>/`, etc.) rather than inventing new top-level folders.
3. **Filenames use natural, space-separated title case** (e.g. `Setup PBI trusted servers.md`, `Check NULL percentage.md`), matching the existing files in that folder — not kebab-case or snake_case.
4. **SOPs follow the template** in `docs/snippets/templates/SOPs.md`. Reuse its section numbering and headers rather than freeform structure.
5. **Use Material for MkDocs admonitions and extensions already enabled** in `mkdocs.yml` (`admonition`, `pymdownx.details`, `pymdownx.tabbed`, `pymdownx.superfences` incl. `mermaid` fences, `pymdownx.critic`, `pymdownx.keys`, `pymdownx.mark`, `attr_list`, etc.) instead of raw HTML where a Markdown extension already covers it. Example patterns are visible in `docs/index.md` (`!!! danger`, `:material-icon-name:`, `==highlight==`).
6. **Don't fabricate links or nav entries to pages that don't exist.** Several nav entries already point at empty/placeholder files (e.g. `docs/knowledge/Git commit prefixes.md`); don't propagate that pattern for new pages — write real content or leave the page out of `nav` until it has content.
7. **Tone/voice**: first-person, personal notes written for the author's own future reference — not marketing copy or generic tutorial prose. Keep it terse and practical.
8. **Never redistribute or restate this as externally-licensed content** — `docs/index.md` explicitly states the content isn't meant to be copied into other repos/sites; keep that notice intact if editing `index.md`.

## Local workflow

- Python 3.13, dependencies managed via `uv` (see `.python-version`, `uv.lock`).
- Preview the site locally: `uv run mkdocs serve`.
- Build static output: `uv run mkdocs build`.
- No test suite, linter, or CI pipeline is configured in this repo — validate changes by building/serving the site and checking the nav renders correctly.

## Commit messages

Follow the conventions in `docs/knowledge/Git commit prefixes.md` if populated; otherwise mirror the existing commit log style (short, imperative, occasionally prefixed with `Add:`/`Fix:`, e.g. "Add extra SQL Snippets", "Fix: small code snippet bug").
