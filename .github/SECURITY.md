# Security Policy

This repository publishes a static documentation site (MkDocs + Material, deployed to GitHub Pages). It has no backend, no user accounts, no forms, and doesn't collect visitor data — so the realistic attack surface is narrow. Relevant reports still welcome:

- A vulnerable dependency pinned in `pyproject.toml` / `uv.lock` (e.g. a CVE in `mkdocs-material` or `mkdocs-glightbox`)
- A secret, credential, or token accidentally committed to the repo history
- A GitHub Actions workflow (`.github/workflows/`) that could be abused to run untrusted code or exfiltrate the `contents: write` token used for `gh-deploy`
- Content injected into the published site that wasn't authored here (e.g. via a compromised dependency at build time)

## Reporting

Please use GitHub's private vulnerability reporting for this repo — go to the **Security** tab → **Report a vulnerability** — rather than opening a public issue, so any real exposure (leaked secret, exploitable workflow) isn't disclosed before it's fixed.

This is a solo-maintained personal project, so response time isn't guaranteed, but reports will be looked at and addressed as soon as reasonably possible.

## Scope

Out of scope: the content/opinions expressed in the documentation itself (factual corrections belong in a regular issue/PR per [CONTRIBUTING.md](CONTRIBUTING.md), not here), and third-party services this site links out to.
