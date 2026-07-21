# Contributing

This is [Jesper's](https://github.com/jespervb7) personal knowledge base — a solo-maintained collection of notes, SOPs, and snippets he wants to keep track of. It isn't run as an open-source project looking for feature contributions, and content decisions (what to document, how to phrase it, what stays a draft) are made by the author alone.

That said, corrections are genuinely welcome:

- **Typos, broken links, factual errors, outdated info** — open an issue using the "Content correction" template, or send a small PR directly.
- **"This SOP/snippet no longer works"** — open an issue with what you tried and what happened; see [SECURITY.md](SECURITY.md) instead if it's a security issue.
- **New content ideas** — open an issue using the "Content suggestion" template. These may sit in the backlog for a while; this is a personal project maintained in spare time.

## If you're sending a PR

- Keep it small and scoped to one fix/addition.
- Follow the existing conventions in [copilot-instructions.md](copilot-instructions.md): pages live under `docs/<category>/`, filenames use natural space-separated title case, and every new page must be added to `nav:` in `mkdocs.yml` or it won't appear on the site.
- No build/test suite is required to pass beyond the site building cleanly — verify locally with `uv run mkdocs build --strict` before opening the PR.

There's no formal review SLA — this repo has one maintainer, so expect turnaround to vary.
