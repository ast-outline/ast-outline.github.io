# Commands

`ast-outline` ships five subcommands: `outline` (default — run the
binary with file/directory arguments), `digest`, `show`,
`setup-prompt`, and `prompt`.

## `ast-outline <paths…>` — outline

Print structural signatures (classes, methods, fields, line ranges)
without method bodies. Typical output is **2–10× smaller** than the
source.

```bash
# One file
ast-outline path/to/Player.cs
ast-outline path/to/user_service.py

# Whole folder, recursively, by supported extension
ast-outline src/

# Multiple paths in one call (mixed languages OK)
ast-outline src/Combat src/Inventory src/Player.cs
```

Output rows are `[Attr] <modifiers> <kind> <Name> [: bases] L<start>-<end>`,
nested under their parent type. Every declaration carries a line range
so an agent can jump straight to the body.

### `--imports`

Add an `imports:` line to each file header listing the file's
`import` / `use` / `using` / `require` statements verbatim.

```bash
ast-outline --imports src/services/
```

Useful when the agent needs to know **which symbols a file pulls in**
or **where a referenced type lives**. Skip it for normal structural
reads — it adds one extra line per file.

A trailing `[+ N conditional includes]` on the imports line means
the file has more dependencies inside `if` / `try` / loop / function
bodies (PHP `require_once` in if-fallbacks, Python lazy `import` for
circular-deps avoidance, Rust fn-local `use`, Scala method-scoped
`import`). See [Output format → Conditional / dynamic imports](output-format.md#conditional-dynamic-imports).

---

## `ast-outline digest <paths…>`

Compact, language-agnostic **public API map** of a module or folder.
Ideal for a "first look" — fits a whole subsystem on one screen.

```bash
ast-outline digest src/Combat
ast-outline digest src/services src/handlers
```

Code-language output starts with a one-line legend:

```text
# legend: name()=callable, name [kind]=non-callable, [N overloads]=N callables share name, [deprecated]=obsolete, ...
```

The legend is **dynamic** — only entries whose token shape actually
appears in the rendered body are listed. A YAML- or markdown-only
batch emits no legend at all (their digests contain no callables,
kinds, markers, or inheritance), and code batches keep a legend
pruned to the subset of tokens that actually surfaces.

Each file gets a header with a **size label** (`[tiny]` / `[medium]` /
`[large]`) and a `[broken]` tag when parse errors clip the outline. See
[Output format](output-format.md) for the full reference.

### `--imports`

Same as for `outline` — adds an `imports:` line per file.

---

## `ast-outline show <file> <symbol…>`

Print the **full source** of one or more declarations from a file —
suffix-matched, AST-accurate.

```bash
# Single method
ast-outline show Player.cs TakeDamage

# Multiple at once
ast-outline show Player.cs TakeDamage Heal Die

# Whole type body (class / struct / interface / trait / enum)
ast-outline show Combat.cs Player

# Disambiguate same-name members across types
ast-outline show Player.cs Player.TakeDamage
```

**Symbol matching is suffix + case-sensitive for code**, **substring +
case-insensitive for Markdown headings**. For YAML, the symbol is a
dot-separated key path (`spec.containers[0].image`).

`show` matches **keys**, not value text — use `grep`/`rg` for free-text
search inside values.

### Header-only output: `--signature` / `--view signature`

When you already know the symbol name (typically after a `digest`) and
just want the **contract** — what it promises rather than how it's
implemented — pass `--signature` (or the long form `--view signature`).
The output drops the body and keeps only docs + attributes + the
signature line:

```bash
ast-outline show Player.cs TakeDamage --signature
# tests/fixtures/csharp/unity_behaviour.cs:34-39  Demo.Combat.HeroController.TakeDamage  (method)
# in: namespace Demo.Combat → public class HeroController : MonoBehaviour, IDamageable
/// <summary>Apply damage to the hero.</summary>
/// <param name="amount">HP to subtract.</param>
public void TakeDamage(int amount)
```

The mutex-grouped `--full` / `--view full` aliases keep the existing
body-extraction behavior; `--full` is the default, so omitting both
flags reproduces the original behavior. `--signature` and `--full`
cannot be combined — argparse rejects the pair as a `# note:` on
stdout (still exit 0, per the LLM-friendly invariant).

`--no-doc` composes with `--signature`: the doc-comment lines are
stripped, leaving the bare contract:

```bash
ast-outline show Player.cs TakeDamage --signature --no-doc
# tests/fixtures/csharp/unity_behaviour.cs:34-39  Demo.Combat.HeroController.TakeDamage  (method)
# in: namespace Demo.Combat → public class HeroController : MonoBehaviour, IDamageable
public void TakeDamage(int amount)
```

Doc placement matches `outline`: C# `///`, JSDoc, and Rust doc-comment
lines render **before** the signature; Python docstrings render
**after** the signature with one level of indent (mirroring how Python
docstrings live inside the method body).

---

## `ast-outline setup-prompt` — automatic install via your agent (recommended)

Print an install-time checklist for one-shot consumption by a coding
agent (Claude Code, Codex CLI, Gemini CLI, Cursor).

!!! success ":material-robot-happy-outline: One-line setup &mdash; paste into your agent"

    Works in Claude Code, Codex CLI, Gemini CLI, or Cursor.

    ```text { .ast-cta }
    Run `ast-outline setup-prompt` and follow its instructions.
    ```

    *Re-run after every `ast-outline` upgrade — the bundled snippet
    is refreshed in place, your local edits are preserved.*

The agent walks you through:

1. **Verify the CLI** — runs `ast-outline --version`. If missing,
   offers `uv tool install ast-outline` (recommended), `pipx install`,
   or `pip install` inside the active venv. Can install on your
   behalf with explicit consent.
2. **Update check** — best-effort PyPI lookup for a newer release;
   if found, surfaces the matching upgrade command
   (`uv tool upgrade` / `pipx upgrade` / `pip install -U`) — never
   auto-upgrades.
3. **Persistent-context file** — picks the right target for your
   tooling: `./AGENTS.md` is the cross-tool default (read by Codex
   CLI, Claude Code via `@AGENTS.md` import, Gemini CLI with
   `settings.json` config, and Cursor); `./CLAUDE.md` /
   `./GEMINI.md` for single-vendor users; or the matching
   `~/.<tool>/...` file for global scope. Appends the snippet
   wrapped in `<!-- ast-outline:start --> ... <!-- ast-outline:end -->`
   markers — re-runs upgrade the block in place. Diff-aware: if the
   existing block differs from the fresh canonical (CLI upgrade or
   manual edit), the agent shows the diff and asks before
   overwriting.
4. **Optional subagent patches** — looks for exploration-oriented
   subagent files under `.claude/agents/` / `.codex/agents/` /
   `.gemini/agents/` and inserts a small `## Tooling — ast-outline`
   block in each, with per-agent permission.

Cross-vendor universal — adapts to your shell (Unix /
PowerShell / `cmd.exe`), to the conversation's human language, and
to non-interactive harnesses (`codex exec`, `claude -p`, Gemini
headless, scheduled CI restrict execution to read-only checks plus
the AGENTS.md write).

```bash
# Manual pipe to clipboard if you want to read the checklist first:
ast-outline setup-prompt | pbcopy   # macOS
ast-outline setup-prompt | xclip -sel c   # Linux
```

The CLI does no file I/O itself — the active agent performs every
edit using its native tools, so each change is reviewable before it
lands.

---

## `ast-outline prompt` — manual install path

Print the canonical AI-agent prompt snippet. Pipe it into your
`AGENTS.md` / `CLAUDE.md` / `GEMINI.md` so the agent learns to use
`ast-outline` before reading whole files:

```bash
ast-outline prompt >> AGENTS.md
ast-outline prompt >> .claude/CLAUDE.md
ast-outline prompt | pbcopy   # macOS clipboard
```

This is the **manual** path — no agent involvement, you pick the
target file. For the automated equivalent that picks the right file,
runs version checks, and handles diff-aware upgrades, see
`setup-prompt` above. See [AI agents](agents.md) for the full snippet
and integration notes.

---

## Global flags

### `--version` / `-V`

```bash
ast-outline --version
# ast-outline X.Y.Z
# homepage: https://github.com/ast-outline/ast-outline
```

### `--help` / `-h` / `help <cmd>`

```bash
ast-outline help
ast-outline help show
ast-outline help digest
```

### `--lang <name>`

Force a specific adapter when the file extension is ambiguous or
non-standard. Mostly for fixtures and one-off pipelines:

```bash
ast-outline --lang python README.legacy
```

---

## Directory walks — what gets ignored

When you point `outline` or `digest` at a directory, the walk **respects
the project's `.gitignore` and `.ignore`** (including nested ones in
subdirs, with proper override semantics) plus a small hardcoded list
of universally non-source dirs. No flag, no config — it just works on
a fresh clone.

Hardcoded fallback (applied alongside `.gitignore`, kicks in even when
the project has none):

| Category | Dirs |
| --- | --- |
| VCS metadata | `.git/`, `.svn/`, `.hg/` |
| Node | `node_modules/` |
| Python caches, venvs, build metadata | `__pycache__/`, `.venv/`, `venv/`, `.tox/`, `.mypy_cache/`, `.pytest_cache/`, `.ruff_cache/`, `.eggs/`, `*.egg-info/` |
| JVM | `.gradle/` |
| IDE / editor | `.idea/`, `.vs/`, `.vscode/`, `.cursor/`, `.zed/`, `.fleet/` |
| JS test infra & hooks | `__snapshots__/`, `.husky/` |
| JS framework build caches | `.next/`, `.nuxt/`, `.svelte-kit/`, `.turbo/`, `.parcel-cache/`, `.vite/` |
| Infra | `.terraform/` |

Names like `build/`, `bin/`, `dist/`, `target/`, `vendor/`, `out/`,
`obj/` are **not** in the hardcoded list — they're sometimes
legitimate source/data dirs across the wider ecosystem (Maven
`target/` vs. Rust `target/` vs. some project's actual targets folder;
`bin/` for shell scripts vs. .NET output; `vendor/` for Composer deps
vs. hand-curated Go vendoring the user wants parsed; `obj/` for C#
output vs. C `.o` files vs. data dirs holding `.obj` 3D models). We
delegate those to `.gitignore` per repo — if your project has them,
list them there (which you likely already do).

### Priority order

Multiple ignore sources combine into one filter, with later sources
winning on conflict — same precedence ripgrep / fd / ast-grep use:

```text
hardcoded defaults  <  .gitignore  <  .ignore
       lowest                          highest
```

So if your `.gitignore` excludes `vendor/` but your `.ignore` has
`!vendor/`, `vendor/*`, `!vendor/our-fork/` — the `.ignore` wins for
the rescued subdir. Same idea works at the file level: a generated
`schema.gen.ts` you keep in git but want hidden from outline goes
into `.ignore` (not `.gitignore`).

### `.ignore` — search-tool convention

`.ignore` is the file `ripgrep` / `fd` / `ast-grep` use to mean
"hide from search/tool output without affecting git tracking."
Same syntax as `.gitignore`, just a different file. ast-outline
reads it the same way — root-level and nested in subdirs. If you
already have an `.ignore` set up for `rg`/`ag`, ast-outline picks
it up automatically.

### Nested `.gitignore` / `.ignore` files

Mirroring `git` itself, a `.gitignore` placed in a subdir applies
**only to that subtree**, with patterns resolved relative to the
subdir. Sibling subtrees aren't affected, and a deeper `.gitignore`
can override its parent's rule via `!` negation:

```text
# proj/.gitignore        — top-level rule
*.skip.py

# proj/keep/.gitignore   — local override for this subtree
!*.skip.py
```

`proj/drop.skip.py` is filtered, `proj/keep/rescued.skip.py` is kept.

### Un-ignoring a default-pruned dir (monorepo escape hatch)

If you need to scan something inside a dir we filter by default —
classic case: a hand-curated fork at `node_modules/our-fork/` you
want parsed alongside everything else — use git's standard three-line
idiom in your `.gitignore`:

```text
!node_modules/             # un-exclude the dir
node_modules/*             # re-exclude its top-level contents
!node_modules/our-fork/    # un-exclude the one subtree you care about
```

A bare `!node_modules/our-fork/` won't work on its own — neither for
us nor for git. That's intentional: git refuses to descend into a
parent that was excluded, so descendant negations have no effect
unless the parent is un-excluded first.

### The `# note:` line

When the walker prunes anything, the output starts with a one-line
note listing the **unique basenames** of pruned dirs (sorted, deduped
across nested occurrences, capped at 8 with a `… +N more` tail in
deep monorepos), with a hint about the escape hatch:

```text
# note: ignored 12 dirs (.git, .gradle, .idea, .next, .pytest_cache, .venv, __pycache__, node_modules) via .gitignore/.ignore + defaults — pass --no-ignore to disable
```

A clean directory (nothing to ignore) emits no note. File-level
gitignore matches are filtered silently — listing them tends to bloat
the note without giving the agent useful disambiguation.

Pointing at a file directly (`ast-outline show .venv/foo.py X`) bypasses
the walker entirely — explicit file paths always work, even if their
parent dir would have been pruned in a directory walk.

### `--no-ignore` — disable the whole filter

For the rare case when you want to outline a default-pruned subtree
without editing any ignore files (`.gitignore`/`.ignore`/the
hardcoded list), pass `--no-ignore`. It works on `outline` and
`digest`:

```bash
ast-outline digest node_modules/our-fork --no-ignore
ast-outline outline .venv/lib/python3.12/site-packages/somepkg --no-ignore
```

When set, the walker only filters by supported extension —
`.gitignore`, `.ignore`, and the hardcoded defaults are all
disabled. The `# note: ignored …` line is also omitted (since
nothing was filtered).

This is **the** escape hatch — there's no `--exclude`,
`--include`, or per-rule disable flags. One switch, on/off.

---

## Exit codes — the LLM-friendly contract

`ast-outline` **always exits `0` on handled errors** and prints a
short `# note: …` line on stdout (the channel the agent reads as the
response). Missing paths, unsupported extensions, parse failures,
symbol-not-found — all produce `# note: …` rather than a non-zero
exit:

```bash
ast-outline does/not/exist
# note: path not found: does/not/exist

ast-outline show Player.cs DoesNotExist
# note: no symbol matching DoesNotExist in Player.cs
```

This avoids the common failure mode where an agent treats `rc=1` as
"empty result" or aborts the chain. Genuine internal bugs still exit
non-zero.
