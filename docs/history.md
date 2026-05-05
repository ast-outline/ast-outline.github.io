# Project history

A timeline of releases and notable events. The full record lives in
[`git log`](https://github.com/ast-outline/ast-outline/commits/main) and
the [GitHub release page](https://github.com/ast-outline/ast-outline/releases).

## 2026

### May

| Date | Event |
| --- | --- |
| **2026-05-05** | **v0.6.8**: directory walks now respect `.gitignore` and `.ignore` (root + nested, with deepest-wins override semantics including `!` negation) and prune a hardcoded list of universally non-source dirs out of the box (`.git`, `node_modules`, `__pycache__`, `.venv`, `.tox`, `.next`, `.gradle`, `.idea`, `.vscode`, `.cursor`, `.zed`, `.fleet`, `.vs`, `*.egg-info/`, …). Conflict-prone names (`build/`, `bin/`, `dist/`, `target/`, `vendor/`, `out/`, `obj/`) are intentionally NOT in the hardcoded list — those go through `.gitignore` per-repo. When the walker filters anything, output starts with `# note: ignored N dirs (basename1, basename2, …) via .gitignore/.ignore + defaults — pass --no-ignore to disable` so the agent sees what got skipped and learns the escape hatch. New `--no-ignore` flag disables the entire filter pipeline (one switch — no per-rule combinatorics like ripgrep's six). New `pathspec>=0.12` dependency (MPL-2.0, Apache-2.0 compatible). |
| **2026-05-04** | **v0.6.7**: `ast-outline digest` legend is now **dynamic** — only entries whose token shape actually appears in the rendered body are listed. YAML- and markdown-only digests (whose body contains no callables, kinds, markers, or inheritance) emit no legend at all; code batches keep a legend pruned to the subset of tokens that actually surface. The omission rule also drops the legend in the rare case where only `L<a>-<b>` would fire (e.g. a code batch of pure marker classes with no members) — a one-entry legend documenting line ranges is more overhead than insight when the suffix shape is already obvious from the body. Drops ~200 bytes of noise from yaml/md digests and saves prompt budget when digest output is piped into LLM context. |
| **2026-05-04** | **v0.6.6**: `ast-outline show` gains a depth dial — `--signature` (and the long form `--view signature`) returns header only (docs + attributes + the signature line, no method body), with the mutex-grouped `--full` / `--view full` aliases preserving the existing body-extraction behavior as the default. Closes the gap between `digest` (just symbol names) and `show` (full body) for the post-digest "I have the name, I want the contract, not the implementation" workflow — and removes the temptation for agents to pipe `show` through `head -80` to peek at signatures of large methods. Doc placement matches `outline`: `///` / JSDoc / Rust before the signature; Python docstrings after with +1 indent. Composes with `--no-doc` for a bare contract line. New public helper `core.render_signature_view(match)` and `SymbolMatch.decl` back-reference. Agent-prompt snippet (English canonical + RU/ZH README copies + docs-site `agents.md`) gets a closing line on step 3 documenting the flag as a modifier across every `show` form. |
| **2026-05-03** | **v0.6.5**: `ast-outline prompt` snippet (and its README copies) reworded to be explicitly cross-vendor — Claude Opus 4.7 / Sonnet 4.6 / Haiku 4.5 **and** OpenAI GPT-5.x (5.3-codex / 5.4 / 5.5). The broad-to-narrow command list lead-in changed from "Stop at the step that answers the question" to a menu/decision-tree framing, avoiding GPT-5.5's process-prescription antipattern while keeping the same scope guard for Claude's literal models. Module docstring of `_prompt.py` now codifies the cross-vendor invariants future edits must preserve. |
| **2026-05-03** | **v0.6.4**: `outline` and `digest` no longer produce empty stdout when **every** file in a batch fails to parse. Per-file `# note: parse error in <path>: <err>` lines now go to stdout (matching the existing convention for user-facing failures), so an LLM agent reading stdout sees what happened instead of `(no output)`. Detailed `# WARN` lines still go to stderr for humans; partial-failure batches keep their existing behavior. |
| **2026-05-03** | **v0.6.3**: `conditional_imports_count` extended to **Python**, **Rust**, **Scala** — same `[+ N conditional includes]` marker now flags lazy `import` inside fn / class bodies (Python), `use` inside `fn` / closures (Rust), and method-scoped `import` (Scala). |
| **2026-05-03** | **v0.6.2**: **PHP language adapter** (`.php`, `.phtml`, `.phps`, `.php8`) targeting modern PHP 8.x and the 7.4 LTS line — namespaces, classes (`abstract` / `final` / `readonly`), interfaces, traits, PHP 8.1 enums, magic ctor / dtor, PHP 8.0 ctor property promotion, multi-variable properties, PHP 8.3 typed class constants, PHP 8.0 `#[Attr]` attributes, `use` (incl. grouped) and top-level `include` / `require`. Verified on real WordPress core (no parse errors on files up to 291 KB). New common-IR field `ParseResult.conditional_imports_count` — renderers append `[+ N conditional includes]` to the imports line when the file has dependencies that aren't statically listed. |
| **2026-05-03** | **v0.6.1**: PyPI metadata refresh after the GitHub Organization transfer (no code changes). |
| **2026-05-03** | **Repository transferred** from `dim-s/ast-outline` to the [`ast-outline`](https://github.com/ast-outline) GitHub Organization. Old `dim-s/ast-outline` URLs continue to redirect. Copyright remains with Dmitrii Zaitsev (dim-s); the org is hosting infrastructure, not a new copyright holder. |
| **2026-05-03** | **v0.6.0**: relicense from MIT to **Apache License 2.0**. Documentation separately licensed under **CC BY 4.0**. The previous MIT text is retained in `LICENSE-MIT` for compatibility with downstream forks of the 0.5.x tree. |
| **2026-05-02** | First publish to **PyPI** as [`ast-outline`](https://pypi.org/project/ast-outline/). v0.4.2 / v0.4.3 / v0.5.0 (`code-outline` CLI alias dropped) / v0.5.1 (`implements` command dropped — outline/digest already render `: Base`) / v0.5.2 (`--imports` flag) / v0.5.3 (`--version` flag). |
| **2026-05-01** | **v0.4.0**: digest method markers (`[async]` / `[unsafe]` / `[const]` / `[suspend]` / `[static]` / `[abstract]` / `[override]` / `[classmethod]` / `[property]`); type modifiers, attrs, and `[deprecated]` tag. |

### April

| Date | Event |
| --- | --- |
| **2026-04-30** | YAML adapter; per-file size labels + token estimate in digest headers; **Rust adapter**. |
| **2026-04-28** | `# note: …` LLM-friendly error contract on stdout with `rc=0`; substring matching for Markdown headings. |
| **2026-04-25** | Go adapter. |
| **2026-04-24** | Scala adapter. **Renamed `code-outline` → `ast-outline` (v0.3.0).** GitHub repo renamed to `dim-s/ast-outline`. |
| **2026-04-23** | Kotlin adapter; `prompt` subcommand. |
| **2026-04-22** | **Repository created** on GitHub as `dim-s/code-outline`. First public commit, v0.2.0b0. Russian and Chinese READMEs added; TypeScript / JavaScript adapter shipped same day. |

---

## Priority date

The **2026-04-22** date matters: it's the first public commit and the
anchor for trademark priority. It's recorded in
[`NOTICE`](https://github.com/ast-outline/ast-outline/blob/main/NOTICE)
and frozen in PyPI release metadata, so it can't be backdated by
downstream forks or copies.
