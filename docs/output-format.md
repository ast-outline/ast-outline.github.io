# Output format

`ast-outline` has three output modes: **outline** (per-file, detailed),
**digest** (multi-file, compact), and **grep** (matches with
enclosing scope and a kind classification). All three are designed to
be **parseable cold by an AI agent** — no out-of-band reference doc
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

### Format presets (`--format`)

The four levels (`names`, `compact`, `default`, `wide`) trade detail
for density. Same source file rendered under each:

**`--format=names`** (alias `--oneline`) — one line per file, top-level
symbols only:

```text
src/Combat/
  Player.cs [medium]: Player, IDamageable
  Enemy.cs [tiny]: Enemy
```

Files with no public top-level symbols are hidden. Markdown surfaces
H1 headings, YAML surfaces top-level keys, CSS/SCSS surfaces flat
selectors. `[huge]` files emit a header-only line (no trailing `:`).

**`--format=compact`** — hierarchy without per-file counters, line
ranges, or blank paragraph breaks:

```text
# legend: name()=callable, : Base, …=inheritance
src/Combat/
  Player.cs [medium] (1247 lines, ~9,212 tokens)
    class Player : Entity, IDamageable
      TakeDamage(), Heal(), Die() [3 overloads]
      Update() [override], FixedUpdate() [override]
  Enemy.cs [tiny] (84 lines, ~620 tokens)
    class Enemy : Entity
      PatrolTo(), Attack() [async], Despawn()
```

Inheritance, decorators, modifiers, and size labels all survive —
they carry semantic weight. Files with no declarations are hidden
entirely (no `# no declarations` marker).

**`--format=default`** — the v0.8.x output, unchanged:

```text
# legend: name()=callable, name [kind]=non-callable, [N overloads]=N callables share name, L<a>-<b>=line range, : Base, …=inheritance
src/Combat/
  Player.cs [medium] (1247 lines, ~9,212 tokens, 1 types, 6 methods, 3 fields)
    class Player : Entity, IDamageable  L12-198
      TakeDamage(), Heal(), Die() [3 overloads]
      Update() [override], FixedUpdate() [override]
      cooldowns [property]

  Enemy.cs [tiny] (84 lines, ~620 tokens, 1 types, 3 methods)
    class Enemy : Entity  L8-72
      PatrolTo(), Attack() [async], Despawn()
```

**`--format=wide`** — default + private members + fields + uncapped
method lists (preset shortcut for `--include-private --include-fields
--max-members ∞`):

```text
# legend: …
src/Combat/
  Player.cs [medium] (1247 lines, ~9,212 tokens, 1 types, 6 methods, 3 fields)
    class Player : Entity, IDamageable  L12-198
      TakeDamage(), Heal(), Die() [3 overloads]
      Update() [override], FixedUpdate() [override], _refreshCooldowns()
      cooldowns [property], _maxHealth [field], _lastTickAt [field]
```

**Overrides** — explicit `--include-private`, `--include-fields`, and
`--max-members` win over the preset's defaults. `--format=wide --max-
members 5` truncates long member lists while keeping wide's private +
fields; `--oneline --include-private` adds `_private` symbols to the
names output.

---

## Grep format

`grep` returns matches **annotated with their enclosing class /
function** and a **kind classification**, so a single call gives the
agent enough scope to act without follow-up reads.

```text
# src/ast_outline/adapters/python.py (4 matches)

## imports
  > L24: from .base import count_parse_errors [import]

## matches
class PythonAdapter  L41-93
    def parse(self, path: Path) -> ParseResult  L45-93 [def]
        > L47: tree = _PARSER.parse(src)
        > L88: error_count=count_parse_errors(tree.root_node),

# 5 matches in comments/strings hidden — pass --include-noise to see
```

Anatomy:

| Element | Meaning |
| --- | --- |
| `# <path> (<N> match[es])` | File header. One per file with at least one match. Singular `match` for `N=1`. |
| `## imports` | Section header. Emitted only when imports contain a match. |
| `## matches` | Section header. Emitted only when non-import code contains a match. |
| `class …  L<a>-<b>` / `def …  L<a>-<b>` | Scope frame. Mirrors `outline` indentation — the enclosing class / function chain is reconstructed from the AST so the agent reads which class / method a match lives in without re-opening the file. |
| `> L<n>: <code>` | A match line. Indented one level deeper than its scope frame. Whitespace inside `<code>` is preserved verbatim from the source. |
| `[def]` | Trailing tag on the declaration line itself when the symbol is **defined** there (not just mentioned). |
| `[import]` | Trailing tag on a match inside an `import` / `use` / `using` / `require` statement — including multi-line block forms (Python `from x import (\n  Y,\n)`, Go `import (...)`, Rust `use foo::{...}`, etc., classified via AST, not line-prefix). |
| `[comment]` / `[string]` | Trailing tags for noise matches. **Only surface with `--include-noise`** (or `--kind comment` / `--kind string`, which auto-enables noise). |
| *(no tag)* | Calls and bare references. The line shape — identifier-followed-by-`(` or not — distinguishes them, so an explicit `[call]` / `[ref]` tag would be redundant. |

The empty-section rule keeps output dense: a file whose only hits are
imports renders just the `## imports` block, no `## matches` header.

### Noise filter footer

When the default filter swallows matches inside comments / strings, a
trailer documents what was hidden so the agent never silently sees a
partial result set:

```text
# 5 matches in comments/strings hidden — pass --include-noise to see
```

Pass `--include-noise` to surface them with `[comment]` / `[string]`
tags inline. With `--kind comment` / `--kind string` this happens
automatically.

### Truncation footer (`-m` / `--max-count`)

When `-m N` caps per-file matches, every truncated file gets an
explicit footer — partial results are never silent:

```text
# truncated — 3 more matches in this file (raise --max-count to see)
```

`-c` (counts) and `-l` (paths-only) report the **capped** count,
matching POSIX `grep -c`.

### Alternate output shapes

`-l` and `-c` replace the structured form with one line per file:

```text
# -l (--files-with-matches) — paths only, zero-count files skipped
src/ast_outline/adapters/python.py
src/ast_outline/adapters/typescript.py

# -c (--count) — `path:N`, zero-count files skipped
src/ast_outline/adapters/python.py:4
src/ast_outline/adapters/typescript.py:6
```

Both operate on the AST-aware base, so counts and file lists exclude
docstring / comment noise (unless `--include-noise` is set).

### Empty result

```text
# note: no matches for 'pattern'
```

Printed to **stdout** with **exit code 0** — non-zero exits would
break parallel `bash` batches in agent harnesses, so user-facing
failures (no match, file not found, bad arg) stick to the
`rc=0` / `# note:` convention. Real internal crashes still propagate
normally.

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
**Ruby**, and **Lua** adapters. Java / Go / Kotlin / C# / TypeScript /
C++ leave the counter at `0` — their import grammars allow only
top-level imports. Lua is the latest entry: `require` is a runtime
function call, so anything inside a function body / `if` / loop is
counted but not listed; top-level `require "x"` / `require("x")` /
`local Y = require("x")` are all static.
Treat the count as a hint to read the file directly when you need
the full dependency picture.

---

## Lua adapter quirks

Lua has no classes, no namespace keyword, no visibility modifiers —
all module / class / public-API structure is convention over plain
tables. The adapter stays close to syntax instead of guessing
structure: each declaration is emitted at the file's top level with
the qualifier baked into the `name` (`M.foo`, `M:bar`,
`ns.deep.nested.helper`), flat like the Python adapter. No synthetic
`KIND_NAMESPACE` wrapping — agents read the file's `return X`
statement themselves.

| Source shape | Kind | Visibility |
| --- | --- | --- |
| `function foo()` (top-level global) | `function` | public |
| `local function foo()` | `function` | private |
| `function M.foo()` | `function` | public |
| `function M:bar()` *(colon = implicit `self`)* | **`method`** | public |
| `function M.__add()` / `M.__add = function() end` | **`operator`** | public |
| `M.foo = function() end` | `function` | public |
| `M.CONST = 42` | `field` | public |
| `local x = 1` | `field` | private |
| `local x = function() end` | `function` | private |
| `M._helper = function() end` *(underscore convention)* | `function` | private |
| `return { foo = function() end, V = 1 }` *(direct-return-table module)* | each field emitted at top level | per name |

**Metamethod set** (all → `KIND_OPERATOR`, regardless of declaration
shape): `__add`, `__sub`, `__mul`, `__div`, `__mod`, `__pow`,
`__unm`, `__idiv`, `__band`, `__bor`, `__bxor`, `__bnot`, `__shl`,
`__shr`, `__eq`, `__lt`, `__le`, `__concat`, `__len`, `__call`,
`__index`, `__newindex`, `__tostring`, `__metatable`, `__pairs`,
`__name`, `__close`, `__gc`, `__mode`. The underscore prefix is
part of a language-level protocol (Python-dunder analogue), NOT a
private-name convention — visibility stays public.

**Lua 5.4 attributes** `<const>` / `<close>` ride in
`Declaration.attrs` as source-true text.

**`require` imports** preserve source-true form. `require "x"`
(bare string-arg) stays bare; `require("x")` keeps its parens. The
`local Y = require("x")` shape — common Lua idiom — is recognised
as a static import (the WHOLE statement byte range goes into
`import_regions` so grep promotes the full line to `[import]`
despite the `local` prefix). Runtime `require` calls inside function
bodies / `if` / loops bump the conditional counter.

**Long-bracket comments** (`--[[ ]]`, `--[==[ ]==]` at any `=`
level) and **long-bracket strings** (`[[ ]]`, `[=[ ]=]`) span
multiple lines, so the line-prefix heuristics in `grep` can't
classify matches inside them. The adapter writes their byte ranges
into `noise_regions`, where the same noise-filter path used for
Python docstrings / multi-line strings handles them. Matches inside
classify as `[comment]` / `[string]` and disappear from the default
code view; `--include-noise` brings them back.

**What's NOT done in v1:**

- **Luau (`.luau`, Roblox's typed dialect)** — vanilla
  `tree-sitter-lua` produces ERROR nodes on Luau's type
  annotations (`x: number`, `function f<T>(): U`, `export type`).
  Symbol semantics are the same, so the v0.9.1+ path is a
  suffix-dispatch branch on a separate `tree-sitter-luau` parser.
- **`setmetatable`-based inheritance.** No syntactic anchor — only
  convention (`Child.__index = Parent`, `setmetatable(Child,
  {__index = Parent})`, `class("Foo", Bar)` via middleclass /
  30log). v1 leaves `bases = []`. The `__index` assignment lands
  as `KIND_OPERATOR`, so the relationship is still surfaced; the
  agent reads it directly.

---

## TypeScript / JavaScript callback blocks

Modern TS/JS expresses a lot of structure through *function calls that
take a callback* rather than language-level declarations: test suites
(`describe` / `it` / `test`), Pinia setup-stores (`defineStore`), and
any in-house DSL of the same shape. The adapter recognises these as
`block` declarations and descends into the callback body — without
this, a test file outlines as empty and a setup-store dumps its whole
callback into one signature line.

The rule is **structural, not a hard-coded list of framework names** —
a call is a block when (1) its callee is a plain identifier, (2) its
last argument is a function literal, and (3) its first argument is a
string-literal label. The label names the block.

```text
# initialState.spec.ts [tiny] (75 lines, ~478 tokens)
describe('Testing: initial state')                          L7-74
    const useCounter = defineStore('counter', {…})          L8-15
    function factory(options?: TestingOptions)              L29-39
    it('can set an initial state')                          L41-49
    it('can provide objects')                               L51-60
```

In `digest` a top-level block renders like a type — a `block <label>`
header with member tokens for the nested cases:

```text
  initialState.spec.ts [tiny] (75 lines, ~478 tokens)
    block Testing: initial state  L7-74
      factory(), 'can set an initial state' [block], 'can provide objects' [block]
```

Block labels are quoted in digest member tokens and in `--format=names`
— a test description is free text and may contain commas, which the
comma-separated token list would otherwise mis-split. `show` extracts a
block by its label (`show file.spec.ts 'can provide objects'`), and
`grep` shows blocks in match scope chains (`describe('...') > it('...')`).

**Body elision.** A field whose value embeds a function or method body
renders with the body collapsed to `{…}` — the outline shows the
declaration's shape, never implementation code:

```text
const incrementDay = action((date: string) => {…})          L33-39
```

**Not recognised** — a pure shape rule cannot avoid these: a
member-expression callee (`QUnit.test(...)`, Playwright
`test.describe(...)`) is excluded the same way `arr.map` / `el.on` are,
so bare-global test frameworks (vitest / jest / mocha / jasmine / ava /
tape / node:test) work but `QUnit.`-namespaced calls do not; a bare
`addEventListener('x', fn)` is structurally identical to `it('x', fn)`
and is treated as a block.

---

## Ruby callback blocks

Ruby expresses a large amount of structure through *method calls that
take a block* rather than through `def` / `class` / `module`: RSpec
suites (`describe` / `context` / `it`), Rake tasks (`task :build do`),
route maps (`namespace :admin do` / `resources :users do`),
`ActiveSupport::Concern`'s `concerning`, and any in-house DSL of the
same shape. The adapter recognises these as `block` declarations and
descends into the block body — without it an RSpec spec file outlines
as "no declarations".

The rule is **structural, not a hard-coded list of framework names** —
a block-bearing `call` (`do...end` or `{...}`) becomes a block when
(1) its callee is a plain identifier and (2) its first argument is a
string or symbol label. A *constant* first argument is intentionally
not a label: `assert_raises(ArgumentError) { ... }` is structurally
identical to `describe User do`, so a constant is too weak a
discriminator.

```text
# user_spec.rb [tiny] (22 lines, ~114 tokens)
let(:user)                                       L4
describe "#full_name"                            L6-16
    it "joins first and last"                    L7-9
    context "when last name is blank"            L11-15
        it "returns just the first name"         L12-14
```

**Transparent descent.** A block-bearing call whose callee is *not* a
plain identifier — a member call such as `RSpec.describe User do` (the
modern RSpec entry point) or `Rails.application.routes.draw do` — is
not itself a block, but its body is still walked so the
plain-identifier blocks nested inside it surface. Without this a modern
`RSpec.describe` spec file would still outline as empty.

In `digest` a block renders like a type — a `block <label>` header with
member tokens for the nested cases, labels quoted:

```text
  user_spec.rb [tiny] (22 lines, ~114 tokens)
    block #full_name  L6-16
      'joins first and last' [block], 'when last name is blank' [block]
```

`show` extracts a block by its label (`show user_spec.rb '#full_name'`)
and `grep` shows blocks in match scope chains — the same as for the
TypeScript adapter above.

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
