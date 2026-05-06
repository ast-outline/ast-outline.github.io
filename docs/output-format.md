# Output format

`ast-outline` has two output modes: **outline** (per-file, detailed)
and **digest** (multi-file, compact). Both are designed to be
**parseable cold by an AI agent** — no out-of-band reference doc
needed.

## Outline format

```text
# path/to/Player.cs [medium] (1247 lines, ~3,400 tokens, 2 types, 8 methods)
class Player : Entity, IDamageable                        L12-340
  public int Health                                       L18-18
  public int Speed                                        L19-19
  public Player(int initialHealth)                        L25-32
  public void TakeDamage(int amount)                      L42-58
  public void Heal(int amount)                            L60-72
  private void Die()                                      L74-89
  ...
```

Each row is:

```
<indent> [Attr] <modifiers> <kind> <Name> [: bases] L<start>-<end>
```

Method bodies are **omitted**. The line range points at the body —
agents can `show` it or `Read` that exact slice.

---

## Digest format

`digest` is denser: file headers + (when needed) a one-line legend +
collapsed callables.

```text
# legend: name()=callable, name [kind]=non-callable, [N overloads]=N callables share name, [deprecated]=obsolete, ...

# src/Combat/Player.cs [medium] (1247 lines)
class Player : Entity, IDamageable
  Health, Speed, Mana
  TakeDamage(), Heal(), Die() [3×]
  Update() [override], FixedUpdate() [override]
  cooldowns [property]

# src/Combat/Enemy.cs [tiny]
class Enemy : Entity
  PatrolTo(), Attack() [async], Despawn()
```

The legend is **dynamic** — only entries whose token shape actually
appears in the rendered body are listed. A code batch with overloads
and `@deprecated` members gets the full legend; a batch using only
free functions skips `[N overloads]` and `[deprecated]`. A YAML- or
markdown-only batch (whose digest contains no callables, kinds,
markers, or inheritance) emits no legend at all — there is nothing
non-obvious to explain. The legend is one line, intentionally — easy
to scan once and drop into a prompt.

---

## Size labels

Both **outline** and **digest** stamp each file with a categorical
size label based on **outline output size**:

| Label | Approximate range | Meaning |
| --- | --- | --- |
| `[tiny]` | < ~500 tokens | Outline is roughly the same size as the source — `Read` directly is fine. |
| `[medium]` | ~500–5000 tokens | Outline meaningfully compresses — prefer it. |
| `[large]` | ~5000–100k tokens | Outline output itself can run long; prefer `digest`, then `show` on specific symbols. |
| `[huge]` | 100k+ tokens | In `digest`, the file collapses to its header line only — call `ast-outline outline <path>` to expand. |

These labels are about **how much you save with the outline**, not the
file's intrinsic complexity. A large generated file might still be
`[large]` even though no human writes it.

`[tiny]` / `[medium]` / `[large]` are purely descriptive — they don't
change what gets rendered, just inform the agent's choice between
`Read` / `outline` / `show`. **`[huge]` is the one label that also
changes behavior**, but only in `digest`: the body is suppressed so
a directory full of generated SDKs / vendored mega-files doesn't
bloat the output. The full `(N lines, ~N tokens, N types, N methods,
N fields)` counters in parens still appear, so the agent can size up
the file from the digest alone. `outline` and `show` are unaffected
— when the agent explicitly opens one file by path, they get the full
structure regardless of size. The digest legend gets one extra clause
whenever a huge file is in the batch:

```
# legend: [huge]=body omitted (use `ast-outline outline <path>`)
```

Before / after on a directory of 50 huge TypeScript checker.ts copies
(51 232 lines each, ~742 k tokens):

| | Lines of digest output |
| --- | --- |
| Before `[huge]` collapse | 1 602 |
| After `[huge]` collapse | 52 |

A 31× reduction without losing any decision-relevant info — every
collapsed file still carries lines, tokens, and per-kind counters in
its header.

---

## Method-level marker tags

Modifiers that are interesting to an agent surface as bracketed tags
after the method name:

| Tag | Meaning |
| --- | --- |
| `[async]` | `async` / `suspend` callable |
| `[unsafe]` | Rust `unsafe fn` |
| `[const]` | Rust / C# `const fn` / `constexpr` |
| `[static]` | Static method or function (varies per language) |
| `[abstract]` | Abstract / un-implemented |
| `[override]` | Overrides a base / parent |
| `[classmethod]` | Python `@classmethod` |
| `[property]` | Python `@property`, C# property accessor, JVM property |
| `[deprecated]` | Marked deprecated / obsolete |
| `[N×]` | N adjacent callables share the name (overloads) |

Type-level tags (`[deprecated]`, modifier prefixes like `sealed`,
`abstract`, `partial`) appear **before** the kind keyword.

---

## Inheritance

Both renderers append `: Base, Trait` to type headers when the AST
gives bases / interfaces / traits — so the agent sees the hierarchy
without a separate query.

Examples:

```text
class Player : Entity, IDamageable
trait Drawable : Component
struct Vec3 : Copy, Clone
class TimerService : ITimerService, IDisposable
```

For Rust, `impl Trait for Foo` blocks **regroup under the target type**
and add `Trait` to the bases list — there are no synthetic
`impl_Foo` shadows polluting the outline.

---

## Imports (`--imports`)

When `--imports` is passed, each file header gets an extra line:

```text
# src/Combat/Player.cs [medium] (1247 lines)
imports: using UnityEngine; using System.Linq; using Combat.Damage;
class Player : Entity, IDamageable
  ...
```

The import line is **verbatim** from the source — no normalization,
no de-aliasing. An agent looking at `IDamageable` can grep the
imports for where it lives.

### Conditional / dynamic imports

Some languages allow imports outside the file's static top level —
e.g. PHP `require_once` inside an `if` / `else` fallback chain (the
WordPress `wp-load.php` shape), Python lazy `import` inside a
function body to break circular deps, Rust `use` inside a `fn`,
Scala `import` inside a method.

Listing those as if they were regular top-level imports would
mislead an agent into thinking they always load. We don't list them
— but we count them, and append a compact marker to the imports
line:

```text
# wp-load.php [medium] (106 lines)
imports: [+ 6 conditional includes]
  # no declarations
```

When the file also has static imports, the marker trails them:

```text
# wp-includes/functions.php [large] (9285 lines)
imports: require ABSPATH . WPINC . '/option.php' [+ 6 conditional includes]
  ...
```

Currently surfaced by the **PHP**, **Python**, **Rust**, **Scala**,
and **Ruby** adapters. Java / Go / Kotlin / C# / TypeScript / C++
leave the counter at `0` — their import grammars allow only
top-level imports.
Treat the count as a hint to read the file directly when you need
the full dependency picture.

---

## Errors and broken outlines

When tree-sitter recovers from syntax errors, the outline is kept
but a warning surfaces. In `outline`:

```text
# path/to/Player.cs [medium] (1247 lines, ~3,400 tokens, 1 types, 4 methods)
# WARNING: 3 parse errors — output may be incomplete
class Player : Entity                                     L12-340
  ...
```

In `digest`, the file gets a `[broken]` tag:

```text
# src/Combat/Player.cs [medium] [broken]
  ...
```

When you see `[broken]`, treat the outline as best-effort and read
the affected region directly with `Read` / `show`.
