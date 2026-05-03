# AGENTS.md — ast-outline.github.io (website repo)

Notes for Claude Code / Cursor / other coding agents working in this
repo. The user (Dmitrii Zaitsev / dim-s) maintains **two related
repositories** locally — keep both in mind.

## The two repos

| Role | GitHub | Local path |
| --- | --- | --- |
| **CLI tool source (the actual `ast-outline`)** | [ast-outline/ast-outline](https://github.com/ast-outline/ast-outline) | `/Users/dmitrijzajcev/dev/tools/code-outline/` *(historical folder name; the project was renamed but the local folder stayed)* |
| **This repo — docs website source** | [ast-outline/ast-outline.github.io](https://github.com/ast-outline/ast-outline.github.io) | `/Users/dmitrijzajcev/dev/tools/ast-outline.github.io/` |

This repo is **only the website** — Markdown sources + MkDocs Material
config + a deploy workflow. No tool code lives here.

Published at <https://ast-outline.github.io/> via GitHub Pages (org-level
convention: repo name matches `<orgname>.github.io` exactly, so it serves
the bare root URL).

## What lives where

```
docs/              All Markdown sources for the site
  index.md         Landing page
  commands.md      CLI reference — mirrors the tool's actual commands
  agents.md        LLM-agent integration — embeds the `ast-outline prompt` snippet
  output-format.md Digest format spec — must stay in sync with src/ast_outline/core.py in the code repo
  about.md         License & attribution
  history.md       Project history / release timeline

mkdocs.yml         Theme, nav, markdown extensions (Material)
.github/workflows/
  deploy.yml       Build + deploy on push to main (no path filter — entire repo IS the site)

LICENSE            CC BY 4.0 full canonical text — applies to ALL content here
NOTICE             Attribution + ast-outline(TM) trademark statement
README.md          Repo purpose + local preview commands
```

## When the tool changes, this repo may need updating

Edit the corresponding page in `docs/` and push:

| Tool change (in `../code-outline/`) | Page to update here |
| --- | --- |
| New / changed CLI subcommand or flag | `docs/commands.md` |
| New language adapter | `docs/index.md` (supported-languages table) + `docs/output-format.md` if format quirks |
| Digest format / marker / legend change | `docs/output-format.md` |
| LLM-agent prompt snippet change in `src/ast_outline/_prompt.py` | `docs/agents.md` (the `??? quote` block) |
| New release / version | `docs/history.md` (timeline) |
| License / trademark changes | `docs/about.md` + `LICENSE` / `NOTICE` here |

## Local preview

```bash
# One-shot, no install
uvx --with mkdocs-material mkdocs serve   # http://127.0.0.1:8000

# Strict build (matches CI)
uvx --with mkdocs-material mkdocs build --strict
```

Site auto-rebuilds on every push to `main`. First deploy after repo
creation requires enabling Pages in GitHub UI: Settings → Pages →
Source: **GitHub Actions** (not "Deploy from a branch").

## License

All content in this repo is under **CC BY 4.0** (`LICENSE`). Reuse
of any non-trivial portion requires visible attribution. The
`ast-outline™` trademark is held by Dmitrii Zaitsev separately —
CC BY 4.0 does not grant trademark rights.

## Don't do

- Don't add tool code here. The CLI source belongs in
  `../code-outline/` (GitHub: `ast-outline/ast-outline`).
- Don't rename this repo. The name `ast-outline.github.io` is what
  makes GitHub serve this at the bare root URL — any other name and
  Pages serves under `/<name>/` instead.
- Don't remove `LICENSE`, `NOTICE`, or the trademark notice in the
  footer (`mkdocs.yml` `copyright:` field). They establish the
  attribution and trademark posture against forks like
  `aeroxy/ast-outline`.
- Don't break the cross-link in `docs/about.md` to the tool repo —
  that's the canonical "where does the actual code live" pointer for
  visitors landing on the docs site.
