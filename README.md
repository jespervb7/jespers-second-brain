# jespers-second-brain

This repository contains code for my website that has documentation about all kinds of things I have.

I build this site with [Zensical](https://zensical.org/) and manage dependencies with [uv](https://docs.astral.sh/uv/).

## Local development

```powershell
uv sync --locked
uv run zensical serve
```

I configure the site and navigation in `zensical.toml`. The Markdown content lives in `docs/`.

To check the whole site before publishing:

```powershell
uv run zensical build --clean --strict
```
