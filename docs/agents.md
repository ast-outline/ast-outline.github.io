# AI agents

This is the primary use case for `ast-outline`. The agent learns about
the tool from a short snippet in your repo's `AGENTS.md` / `CLAUDE.md`
/ `GEMINI.md` (or whatever persistent-context file your CLI reads).
Two paths to get the snippet there.

## Automatic — `ast-outline setup-prompt` (recommended)

Inside Claude Code, Codex CLI, Gemini CLI, or Cursor, ask the agent:

> Run `ast-outline setup-prompt` and follow its instructions.

The agent reads a checklist from stdout and walks you through:

1. Verify `ast-outline` is installed (offers to install via
   `uv tool install` / `pipx` / `pip` if missing, with explicit
   consent — never silently).
2. Best-effort PyPI check for a newer release; surfaces the upgrade
   command if available — never auto-upgrades.
3. Pick the right target file: `./AGENTS.md` is the cross-tool
   default (covers Codex CLI, Claude Code via `@AGENTS.md` import,
   Gemini CLI with `settings.json` config, and Cursor); `./CLAUDE.md`
   / `./GEMINI.md` for single-vendor users; or the matching
   `~/.<tool>/...` file for global scope across all your projects.
4. Append the canonical snippet wrapped in
   `<!-- ast-outline:start --> ... <!-- ast-outline:end -->` markers.
   Diff-aware on re-run: if the existing block differs from the
   fresh canonical (CLI upgrade or your manual edit), the agent
   shows the diff and asks before overwriting.
5. Optionally patch existing exploration-oriented subagents in
   `.claude/agents/` / `.codex/agents/` / `.gemini/agents/`, with
   per-agent permission.

Re-run after every `pip install -U ast-outline` (or
`uv tool upgrade ast-outline`) to refresh the bundled snippet
without overwriting your local edits.

## Manual — `ast-outline prompt`

The same snippet, no agent involvement — pipe it where you want:

```bash
ast-outline prompt >> AGENTS.md
ast-outline prompt >> .claude/CLAUDE.md
ast-outline prompt | pbcopy   # macOS clipboard
```

Use this when you don't have a coding-agent CLI handy, or want full
control over file edits.

## The snippet itself

??? quote "Snippet (copy-paste version)"

    ```markdown
    ## Code exploration — prefer `ast-outline` over full reads

    For `.cs`, `.cpp`, `.cc`, `.cxx`, `.h`, `.hpp`, `.hh`, `.py`,
    `.pyi`, `.ts`, `.tsx`, `.js`, `.jsx`, `.java`, `.kt`, `.kts`,
    `.scala`, `.sc`, `.go`, `.rs`, `.php`, `.phtml`, `.rb`, `.rake`,
    `.gemspec`, `.css`, `.scss`, `.sql`, `.md`, and `.yaml`/`.yml`
    files, read structure with `ast-outline` before opening the full
    file.

    Pick the smallest of these that answers your question — they're a
    broad-to-narrow menu, not a sequence; skip straight to `show` when
    you already know the symbol:

    1. **Unfamiliar directory** — `ast-outline digest <paths…>`: a
       one-page map of every file's types and public methods. Each
       file is tagged with a size label — `[tiny]` / `[medium]` /
       `[large]` / `[huge]` — plus `[broken]` if parse errors clipped
       the outline. `[huge]` files (≥100k tokens) collapse to
       header-only in the digest; call `ast-outline outline <path>` on
       them when you need the full structure.

    2. **File-level structure** — `ast-outline <paths…>`: signatures
       with line ranges, no bodies (2–10× smaller than a full read on
       non-trivial files). If the header carries `# WARNING: N parse
       errors`, the outline is incomplete — read the affected region
       directly.

    3. **One method / type / markdown heading / yaml key** —
       `ast-outline show <file> <Symbol>`. Suffix matching: `TakeDamage`
       picks one method; `User` returns the full body of a type — class,
       struct, interface, trait, enum (especially useful when a file
       holds several types); disambiguate with `Player.TakeDamage` if
       there's ambiguity. Multiple at once:
       `ast-outline show Player.cs TakeDamage Heal Die`. Markdown
       symbols are heading text, matched case-insensitive substring:
       `"installation"` hits `"2.1 Installation (macOS / Linux)"`.
       YAML symbols are dot-separated key paths
       (`spec.containers[0].image`) — `show` matches **keys**, not
       values; for free-text search inside values use `grep`.
       For css/scss, the symbol is a selector token (`.btn-primary`,
       `$var`) — pseudos and attribute filters are stripped, so
       `.btn-primary` finds the rule even when it carries `:hover`
       or nests in `.modal`.
       For sql, the symbol is a table or column name (`users`,
       `users.email`) — `show users` returns the table definition,
       `show users.email` returns one column line.
       Add `--signature` to any of the above to return header only
       (docs + attrs + signature, no body) — useful after `digest`,
       when you have the name and want the contract, not the
       implementation.

    Both `outline` and `digest` accept multiple paths in a single call
    (mix files and directories, mix languages). Both renderers append
    `: Base, Trait` inheritance to type headers, so you see the
    hierarchy without a separate query.

    When you need to know **what a file pulls in** or **where a
    referenced type lives**, pass `--imports` to `outline` / `digest`.
    Each file gets an extra `imports:` line listing its `import` /
    `use` / `using` statements verbatim — `from .core import X`,
    `use foo::Bar`, `import { X } from './foo'`, `use App\Foo`,
    `require_once 'config.php'`. Read that line, then call
    `outline` / `show` on the source file directly — no `grep`
    needed to find definitions. Skip `--imports` for routine
    structural reads — it adds one line per file.

    A trailing `[+ N conditional includes]` on the imports line means
    N more dependencies live inside `if` / `try` / loop / function
    bodies — read the file directly when you need the full
    dependency picture.

    Fall back to a full read only when `show`'s body isn't enough
    context. `ast-outline help` for the full flag list.
    ```

---

## Integration notes per agent

### Claude Code

`CLAUDE.md` (project-level) or `~/.claude/CLAUDE.md` (global) instructs
the **main agent**. To make Claude Code's isolated subagents (the
built-in `Explore`, anything in `.claude/agents/*.md`) use
`ast-outline`, override them: a subagent file at
`.claude/agents/Explore.md` (or `~/.claude/agents/Explore.md`) shadows
the built-in.

Subagents only see their own system prompt, not your `CLAUDE.md` — so
each subagent that should know about `ast-outline` needs the snippet
in its own file.

### Cursor

Add the snippet to `.cursor/rules/ast-outline.mdc` or to your global
"Rules for AI" in Cursor settings.

### Aider

Append to `CONVENTIONS.md` at the repo root, or pass via
`--read CONVENTIONS.md`.

### Codex / Copilot Chat / others

Most agents accept a project-root file named `AGENTS.md` or a system
prompt configurable via the agent's settings UI. The snippet is plain
Markdown — drop it wherever your agent reads instructions.

---

## What's NOT in the snippet (and why)

- **No "always run `ast-outline` before any read"** rule. The snippet
  steers the agent toward structural reads first, but full reads
  remain valid when needed (small files, body-level questions).
- **No examples of failure modes.** The agent learns from the tool's
  `# note: …` output contract on its own — no need to pre-train it.
- **No reference to specific file extensions you don't use.** If your
  project is pure Python, you can shorten the extension list. The
  generic version supports all adapters.

---

## Verifying the integration

Open the agent in a fresh session and ask:

> "What types live in `src/Combat`? Use `ast-outline` if available."

A correctly-wired agent will run `ast-outline digest src/Combat`,
return the structural map, and only then open specific files for
detail. If it goes straight to `Read` instead, the snippet didn't land
— check that the agent loaded the right config file.
