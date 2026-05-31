---
description: >-
  Complete ast-outline CLI reference: the outline, digest, show, setup-prompt
  and prompt subcommands, every flag and option, with examples.
---

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
`import`, Lua `require(...)` inside a function body). See
[Output format → Conditional / dynamic imports](output-format.md#conditional-dynamic-imports).

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

### `--format=<level>` / `--oneline`

Four detail levels matching four agent task archetypes:

| Level | Per-file shape | Use case |
| --- | --- | --- |
| `names` | one line per file — `name.py [size]: A, B, C` (top-level symbols only) | repo orientation: pick a file to drill into |
| `compact` | hierarchy minus per-file counters, `L<a>-<b>` line ranges, blank lines, `# no declarations` markers | module-structure questions |
| `default` | full hierarchy (v0.8.x output) | surgical `Read --offset N` workflow |
| `wide` | default + private members + fields + uncapped methods | one-file deep dives |

`--oneline` is an alias for `--format=names` (`git log --oneline` style).
Default is `default` — back-compat with every existing skill that parses
digest output.

```bash
ast-outline digest src/                       # default
ast-outline digest src/ --oneline             # broad scan, 1 line/file
ast-outline digest src/ --format=compact      # tight hierarchy
ast-outline digest src/Foo.cs --format=wide   # everything for one file
```

`names` extends to non-code languages: markdown surfaces top-level
H1 headings, single-doc YAML surfaces top-level keys, multi-doc YAML
surfaces per-doc separator captions (`--- doc 1 of 3 — ConfigMap …`),
CSS/SCSS surfaces the flat selector list, HTML surfaces page
landmarks (top-level body children — `header.site-nav`, `main`,
`section#hero`, `form#newsletter` — `html` / `head` / `body` chrome
is hidden as it's identical on every page). `[huge]` files (>100k tokens)
collapse to a header-only line with no symbol list — same as their
default-format behavior.

**Preset overrides** — explicit `--include-private`, `--include-fields`,
and `--max-members` win over the preset's defaults (kubectl-style
silent override). Useful for fine-tuning without learning a new flag:

```bash
ast-outline digest src/ --format=wide --max-members 5   # wide, but cap lists
ast-outline digest src/ --oneline --include-private     # names + private syms
```

`--imports` composes with every format including `names`, adding an
indented `imports: …` line per file. Files filtered to zero visible
symbols (e.g. all-private modules under `names`) are hidden; if every
file in the batch collapses, an explicit `# note: all files hidden …`
replaces the empty stream.

### `--imports`

Same as for `outline` — adds an `imports:` line per file.

---

## `ast-outline show <file|dir|glob> <symbol…>`

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
dot-separated key path (`spec.containers[0].image`). For CSS / SCSS /
HTML, the symbol is a CSS-selector token (`.btn-primary`, `#hero`,
`section#hero`, `[rel=stylesheet]`) — same vocabulary across the three
adapters.

For Markdown headings, inline-markdown decoration — backticks
(`` ` ``), asterisks (`*`), underscores (`_`), tildes (`~`) — is
stripped from both the heading title and the query before comparing,
so ``` `useState` — when to reach for it ``` resolves whether the
query keeps the backticks or drops them. Numbered prefixes (`1.`,
`4.2`) are also tolerated: querying the meaningful core of a numbered
heading works the same as passing the full `outline`-printed form.
The guiding rule is *anything `outline` prints under a `##` header
should resolve as a symbol in `show`* — regardless of how the agent
normalized the title.

`show` matches **keys**, not value text — use `grep`/`rg` for free-text
search inside values.

### Directory / glob target — find + show in one call

Point `show` at a **directory** (or a quoted **glob**) instead of a
file and it locates the symbol's definition(s) under that scope itself,
then prints the body — no separate `grep <symbol> <dir> --kind def` to
find the file first. This collapses the most common two-call pattern
into one:

```bash
# Old: two calls
ast-outline grep MailSpec Assets/Scripts/App/Mail --kind def   # find the file
ast-outline show Assets/Scripts/App/Mail/MailSpec.cs MailSpec  # then read it

# New: one call — directory
ast-outline show Assets/Scripts/App/Mail MailSpec

# New: one call — quoted glob (quote it so the shell keeps `**` literal)
ast-outline show "Assets/Scripts/**/*.cs" MailSpec
```

The outcome depends on how many definitions carry that name:

- **One definition** → its body, preceded by a note naming where it
  was found:

  ```text
  # note: found 'MailSpec' (class) in Assets/Scripts/App/Mail/MailSpec.cs
  # Assets/Scripts/App/Mail/MailSpec.cs:10-42  App.Mail.MailSpec  (class)
  ...body...
  ```

- **Several definitions** (same name across files) → **all** bodies are
  printed (a directory `show` is still a `show`), preceded by a count
  note; each body header carries its own path:

  ```text
  # note: 3 definitions of 'MailSpec' across 3 files — all shown below
  ```

- **No match** → a `# note: symbol not found` line, plus a
  `# hint: did you mean: …?` suggestion when a close name exists (the
  same edit-distance recovery `grep` uses). Exit code is still **0**.
- **A glob that matches no files** → `# note: no files match glob: …`
  (exit 0). A plain non-glob path that doesn't exist still gets the
  precise `# note: file not found` — the glob branch only triggers on a
  path carrying `*`, `?`, or `[`.

A **directory** search reuses `grep`'s file collection, so it **honors
`.gitignore` / `.ignore`** by default; pass `--no-ignore` to search
ignored folders or `--exclude GLOB` to prune extra paths (these two
flags bite only on a directory target — a single-file `show` reads
exactly the file given). A **glob** is expanded literally — it shows
exactly the files the pattern matches, with **no** ignore-filtering
(you already narrowed via the pattern), so `--no-ignore` / `--exclude`
are no-ops for a glob target. All other `show` flags (`--signature`,
`--no-doc`, `--view`, `--json`) apply to the located file(s). A plain
file target is unchanged.

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

## `ast-outline grep <pattern> <paths…>` — structural code search

A code-search command that returns matches **annotated with their
enclosing class/function** and a **kind classification** (`[def]`,
`[import]`; calls and refs render untagged because the line shape —
identifier-followed-by-`(` or not — makes them obvious). Comments and
string literals are filtered out by default.

```bash
ast-outline grep User.save src/
```

Sample output:

```
# src/payments/processor.py (4 matches)

## imports
  > L3: from .models import User [import]

## matches
class PaymentProcessor  L12-180
    def commit(self, tx)  L45-78
        > L48: user.save()
        > L67: user.save()
    def refund(self, tx)  L90-120
        > L98: user.save()
```

**Why a separate command — `grep` / `rg` already exist.**
Regular `grep` returns flat `path:line:content` rows. To answer
"where is `User.save` actually used in this codebase" an agent has to
run the grep, then **open every interesting file** and read enough
surrounding lines to understand which class / function contains each
match. `ast-outline grep` collapses that into one call: scope and
kind are in the output, no follow-up reads needed to learn "this is
the `commit` method in `PaymentProcessor`".

For an LLM agent, the single most expensive thing after a `grep` is
the cascade of follow-up `Read` calls. This command is built so the
*first call* contains enough structural information that the agent
can usually decide what to do next — invoke `show` on a specific
method, plan a refactor, or conclude the symbol is unused — without
re-reading source files.

### Multi-pattern via `-e` (POSIX `grep` / `rg` style)

```bash
ast-outline grep User.save -e User.load -e User.delete src/
```

Patterns combine via OR-alternation in one walk — saves N startup
costs vs running grep three times. The first pattern is positional;
extras come via `-e/--expression` (repeatable).

You can also use `-e` for the **first** pattern (no positional at all)
— matches POSIX `grep -e PAT PATH` and `rg -e PAT PATH` muscle memory:

```bash
ast-outline grep -e User.save src/                   # POSIX-style
ast-outline grep -e User.save -e User.load src/      # all via -e
ast-outline grep --expression=User.save src/         # equals form
```

### Filter by classification: `--kind`

Eliminates the most common post-filter step ("show me only
definitions of X" / "only call sites").

```bash
ast-outline grep --kind def User src/                # only definitions
ast-outline grep --kind def,call save src/           # def + call (skip refs/imports)
ast-outline grep --kind import User src/             # where User is imported
```

Accepts `def`, `call`, `ref`, `import`, `comment`, `string`. Both
repeated (`--kind def --kind call`) and comma-separated
(`--kind def,call`) forms work. `comment` / `string` auto-enable
`--include-noise` — they'd otherwise be filtered out before the kind
filter ever sees them.

### POSIX-style flags

Familiar from `grep` / `rg`, all operate on the AST-aware base so
counts and file lists exclude docstring noise:

| Flag | Behavior |
|------|----------|
| `-w` / `--word` | Whole-word match (`\b...\b` boundaries — `save` no longer matches `save_user` / `_save`) |
| `-l` / `--files-with-matches` | Output paths only — for "where does X exist" exists-checks |
| `-c` / `--count` | `path:N` per file, skips zero-count files |
| `-m N` / `--max-count N` | Cap visible matches per file. Truncated files get a `# truncated — N more...` footer so partial results are never silent |
| `-i` / `--case-insensitive` | Case-insensitive match |
| `--regex` | Treat patterns as regular expressions instead of literal substrings |

### Regex auto-detect

Patterns containing unambiguous regex syntax (`\|`, `\d`, `\w`, `\s`,
`\b`, `(?:`, bare `|`) auto-promote to regex with a `# note:`
documenting the promotion. `\|` is normalized to `|` before compile
(BRE → ERE — Python's `re` reads `\|` as a literal pipe, opposite of
grep). Ambiguous metachars (`.`, `*`, `+`, `?`, `[`, `^`, `$`) never
auto-promote — they have legitimate literal interpretations in code
— but emit a `# hint:` on zero matches suggesting `--regex`.

### Empty-result recovery

An empty `grep` is the most expensive miss for an agent — the usual
next move is a blind retry (reword the pattern, drop a flag, or
abandon the tool for raw `rg` / file reads). Three heuristics make the
*first* call land, or hand back enough to make the second call
correct rather than another guess. None of them fire under explicit
`--regex` (you mean exactly what you typed), and each prints a
`# note:` / `# hint:` line — never a non-zero exit.

**Leading definition-keyword stripping.** Agents habitually paste the
source keyword they'd *write* in front of a symbol. As a literal
substring that misbehaves — on `public enum ItemSoundFamily {` the
match starts on `enum`, not the name, so it classifies as a `ref` and
a `--kind def` narrow drops it. When a single literal pattern is
`<keyword> Identifier`, the keyword is stripped, the identifier is
searched, and (if you gave no explicit `--kind`) the search auto-narrows
to `def`:

```bash
ast-outline grep "enum ItemSoundFamily" src/   # ≡ grep ItemSoundFamily --kind def
# note: searched 'ItemSoundFamily' as a definition (stripped leading 'enum')
```

The recognized keywords are owned by each language adapter
(`class` / `struct` / `enum` / `interface` / `trait` / `record` /
`def` / `fn` / `func` / `function` / `type` / `impl` / `mod` /
`namespace` / `protocol` / `extension` / `object` / `union` / …); the
union across adapters is what `grep` matches against, so a new adapter
extends it for free. Only the single-pattern form is handled — with
multiple `-e` patterns the auto narrow-to-`def` would be ambiguous.

**`--kind` mismatch breakdown.** When a `--kind` narrow yields zero but
the pattern *did* match under other kinds, the kinds that were excluded
are reported so the retry is exact instead of a guess:

```bash
ast-outline grep EditorPrefs src/ --kind call
# note: no matches for 'EditorPrefs'
# hint: --kind call excluded 3 matches (3 ref) — retry with --kind call,ref or drop --kind
```

(`EditorPrefs.GetString(...)` is a `ref` — the dot after the name — not
a `call`; the bare "no matches" used to hide that.)

**Did-you-mean by edit distance.** On a true no-match for a plain
identifier, `grep` collects the declaration names in scope and surfaces
the closest real symbol(s) — catching plural/singular slips and typos
that otherwise drive a chain of blind retries:

```bash
ast-outline grep MissSortPiles src/
# note: no matches for 'MissSortPiles'
# hint: did you mean: MissSortPile (class), MissSortPileGroup (class)?
```

Names that share no structure with any real symbol produce no
suggestion (no false leads), and the lookup is bounded so it never
turns a no-match into a long stall.

### Other flags

| Flag | Behavior |
|------|----------|
| `--include-noise` | Include matches inside comments / strings (filtered by default) |
| `--no-ignore` | Disable `.gitignore` / `.ignore` filtering |
| `--exclude GLOB` | Skip paths matching gitwildmatch GLOB (repeatable; `.gitignore` syntax; `!` negates; anchored at project root; applies even with `--no-ignore`). See the `--exclude` section under [Directory walks](#-exclude-glob-narrow-the-walk-inline). |

### Multi-line / block-form imports

Tree-sitter knows where each import statement starts and ends, so
inner symbols inside multi-line forms classify correctly as
`[import]` — not `[ref]` or `[string]`:

| Language | Example |
|----------|---------|
| Go       | `import (\n  "fmt"\n  "strings"\n)` |
| Python   | `from foo import (\n  Bar,\n  Baz,\n)` (top-level + inside if/try AND inside function/class bodies) |
| TypeScript | `import {\n  A,\n  B\n} from './mod'` |
| Rust     | `use foo::{\n  Bar,\n  Baz,\n}` |
| PHP      | `use App\{\n  Foo,\n  Bar,\n}` |
| Scala    | `import foo.{\n  A,\n  B\n}` |

For C++, the AST distinguishes `using namespace std;` /
`using std::vector;` (imports — bring names into scope) from
`using my_int = int;` (type alias — declaration, NOT import). C# 10+
`global using` is also recognized as `[import]`.

Not a replacement for ripgrep on non-symbol patterns (TODO comments,
log strings, free-text in docs) — fall back to `rg` for those. This
command is built for code-symbol queries.

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

## JSON output

Every structural command — `outline`, `digest`, `grep`, `show` —
accepts `--json`, which swaps the text output for a single JSON
document. The text format stays the default (it is deliberately
token-dense for LLM agents and free to change between releases);
`--json` exists for **programmatic** consumers — editor plugins, CI
gates, scripts — that need a stable, parseable contract.

```bash
ast-outline outline src/ --json
ast-outline digest src/ --json | jq '.summary'
ast-outline grep User.save src/ --json
ast-outline show Player.cs TakeDamage --json
```

Every document is wrapped in a fixed envelope and carries a
`schema_version` so a consumer can detect a breaking change:

```json
{
  "tool": "ast-outline",
  "schema_version": 1,
  "command": "digest",
  "root": "src",
  "notes": [],
  "summary": { "files": 12, "types": 30, "methods": 120, "fields": 40 },
  "files": [ /* one object per parsed file */ ]
}
```

**`--json` is a pure encoding switch.** It changes *how* the output is
serialized, not *what* it contains — the convention `rg --json`,
`kubectl -o json`, and `eslint --format json` all follow. So the
content-filtering flags apply to JSON exactly as they apply to the text
output:

| Flag | Command | Effect on JSON |
| --- | --- | --- |
| `--no-private` / `--no-fields` / `--no-docs` / `--no-attrs` | outline | filter the `declarations` tree |
| `--include-private` / `--include-fields` | digest | filter the `declarations` tree |
| `--view` / `--no-doc` | show | trim each match's `source` field |
| `--no-ignore` / `--exclude` / `--glob`, grep query flags | all | select which files / matches — apply as always |

Flags that pick a *text layout* have no JSON equivalent (JSON has no
layout) and do not affect `--json` output: `--no-lines` and `--imports`
(outline), the `--format` preset's layout dimension and `--max-members`
cap (digest), `-l` / `-c` (grep). A `--format` preset's *content*
settings still apply — so `--format=wide --json` differs from
`--format=default --json` (wide adds private members + fields), while
`--format=names`/`compact`/`default` produce identical JSON because
they share the same content and differ only in text layout.

Because `digest` itself defaults to a public-API map (private members
and fields hidden), `digest --json` does too — pass `--include-private`
/ `--include-fields` (or `--format=wide`) for the complete tree.

**Errors stay valid JSON.** A user-facing failure (path not found, bad
argument, unsupported extension) is emitted as an `error` object
instead of a `# note:` line, so stdout is always parseable — the
exit-0 contract is preserved:

```json
{
  "tool": "ast-outline",
  "schema_version": 1,
  "command": "outline",
  "error": { "notes": ["path not found: does/not/exist"] }
}
```

A zero-result search (`grep` with no matches, a file with no
declarations) is a valid empty document, **not** an error.

**`show` over a directory or glob** uses a slightly different envelope:
the top-level locator is split across two always-present fields —
`directory` (set for a directory target) and `glob` (set for a glob
target), exactly one non-empty — instead of `file`, and because a
single query can resolve to definitions in several files, **each match
carries its own `file`** field. A not-found symbol is an entry with an
empty `matches` list; the did-you-mean suggestion rides in the
top-level `notes`:

```json
{
  "tool": "ast-outline",
  "schema_version": 1,
  "command": "show",
  "directory": "Assets/Scripts/App/Mail",
  "glob": "",
  "notes": [],
  "results": [
    {
      "query": "MailSpec",
      "matches": [
        { "qualified_name": "App.Mail.MailSpec", "kind": "class",
          "file": "Assets/Scripts/App/Mail/MailSpec.cs", "source": "..." }
      ]
    }
  ]
}
```

For a glob target the same shape applies with `directory` empty and
`glob` carrying the pattern (e.g. `"glob": "Assets/Scripts/**/*.cs"`).

See [Output format → JSON output](output-format.md#json-output) for
the full per-field schema.

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
hardcoded list), pass `--no-ignore`. It works on `outline`,
`digest`, and `grep`:

```bash
ast-outline digest node_modules/our-fork --no-ignore
ast-outline outline .venv/lib/python3.12/site-packages/somepkg --no-ignore
```

When set, the walker only filters by supported extension —
`.gitignore`, `.ignore`, and the hardcoded defaults are all
disabled. The `# note: ignored …` line is also omitted (since
nothing the auto-filter would have caught was filtered).

`--no-ignore` and `--exclude` (below) target different axes:
`--no-ignore` silences the **automatic** filter; `--exclude` is the
**user's explicit voice** and keeps applying even under `--no-ignore`.
Both useful together — e.g. `--no-ignore --exclude secret/` walks
every dir except the one you named.

### `--exclude <glob>` — narrow the walk inline

When you want to keep `.gitignore` / `.ignore` honored but add a few
patterns just for this call — without touching files — use
`--exclude GLOB` (`.gitignore`-syntax, repeatable). Works on
`outline`, `digest`, and `grep`:

```bash
ast-outline digest src/ --exclude tests/ --exclude '*.gen.*'
ast-outline outline src/ --exclude vendor/
ast-outline grep User.save src/ --exclude tests/
```

- **Repeatable** — pass the flag multiple times to add patterns; they
  combine additively, same as if you'd added several lines to a
  `.gitignore`.
- **`.gitignore` syntax** — full gitwildmatch grammar: directory
  patterns with trailing `/`, file globs (`*.gen.py`), `**` for
  any-depth, and `!pattern` for negation.
- **Anchored at the project root**, like a top-level `.gitignore` —
  `--exclude src/generated/` resolves identically no matter where you
  invoke from, because patterns aren't reinterpreted against cwd.
- **Survives `--no-ignore`** — explicit user intent isn't silenced by
  the auto-filter switch.
- **Layers above the auto-filter** — a bare `--exclude '!node_modules/'`
  re-includes a default-filtered dir on a single line (the three-line
  git escape idiom isn't needed because `--exclude` sits one frame
  higher than `.gitignore` defaults).
- **Explicit file inputs bypass it** — same rule as `.gitignore`:
  pointing at a file is explicit intent, the file is processed.
- **Bad pattern → `# note:` line** — malformed gitwildmatch
  (`!` alone, trailing backslash) surfaces as
  `# note: invalid --exclude pattern: …` with `rc=0`, honoring the
  LLM-friendly contract.

When `--exclude` contributes to ignored-dir pruning, the existing
`# note: ignored …` line widens its source list — so an agent
debugging "where did my folder go" sees its own flag named:

```text
# note: ignored 3 dirs (__pycache__, generated, tests) via .gitignore/.ignore + defaults + --exclude — pass --no-ignore to disable
```

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
