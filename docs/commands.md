# Commands

`ast-outline` ships four subcommands. The default — running the binary
with file/directory arguments — is `outline`.

## `ast-outline <paths…>` — outline

Print structural signatures (classes, methods, fields, line ranges)
without method bodies. Typical output is **5–10× smaller** than the
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

Output starts with a one-line legend:

```text
# legend: name()=callable, name [kind]=non-callable, [N overloads]=N callables share name, [deprecated]=obsolete, ...
```

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

## `ast-outline prompt`

Print the canonical LLM-agent prompt snippet. Pipe it into your
`CLAUDE.md` / `AGENTS.md` so the agent learns to use `ast-outline`
before reading whole files:

```bash
ast-outline prompt >> AGENTS.md
ast-outline prompt >> .claude/CLAUDE.md
ast-outline prompt | pbcopy   # macOS clipboard
```

See [LLM agents](agents.md) for the full snippet and integration notes.

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
