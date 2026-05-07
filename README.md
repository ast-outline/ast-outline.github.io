# ast-outline.github.io

Source for the **ast-outline documentation website** published at
<https://ast-outline.github.io/>.

The actual `ast-outline` tool — CLI, source code, releases, issue
tracker — lives in a separate repository:
**<https://github.com/ast-outline/ast-outline>**.

This repo holds only the website (Markdown sources + MkDocs Material
config). Pushing to `main` rebuilds and redeploys via GitHub Actions.

---

## Local preview

```bash
# One-shot, no install
uvx --with mkdocs-material mkdocs serve

# Or, with a permanent install
pip install mkdocs-material
mkdocs serve   # http://127.0.0.1:8000

# Strict build (matches CI)
mkdocs build --strict
```

## Layout

```
docs/              Markdown sources
  index.md         Landing page
  commands.md      CLI reference
  agents.md        AI-agent integration
  output-format.md Digest format reference
  about.md         License & attribution
  history.md       Project history

mkdocs.yml         MkDocs Material config
.github/workflows/
  deploy.yml       Build + deploy to Pages on push to main

LICENSE            CC BY 4.0 (full canonical text)
NOTICE             Attribution statement
```

## License

All content in this repository is licensed under the **Creative
Commons Attribution 4.0 International License** ([CC BY 4.0](LICENSE)).

Reuse of any non-trivial portion requires visible attribution. See
`LICENSE` and `NOTICE` for the canonical terms.

---

## Reporting issues

Issues with the **website itself** (typos, broken links, layout) —
[open an issue here](https://github.com/ast-outline/ast-outline.github.io/issues).

Issues with the **tool** (bugs, feature requests, CLI behavior) —
[open them in the main repo](https://github.com/ast-outline/ast-outline/issues).
