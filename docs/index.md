---
hide:
  - navigation
  - toc
---

<div class="ast-hero" markdown>

# <span class="ast-hero__bracket">[</span>ast-outline<span class="ast-hero__bracket">]</span>{ .ast-hero__title }

<p class="ast-hero__tagline">
Fast, AST-based <strong>structural outline</strong> for source files —
classes, methods, signatures with line numbers, but <strong>no method bodies</strong>.
Built for LLM coding agents that should read the <em>shape</em> of a file
before reading the whole thing.
</p>

<div class="ast-hero__cta" markdown>

[:octicons-rocket-16: Install](#install){ .md-button .md-button--primary }
[:octicons-terminal-16: Commands](commands.md){ .md-button }
[:simple-github: GitHub](https://github.com/ast-outline/ast-outline){ .md-button }

</div>
</div>

[![Code: Apache 2.0](https://img.shields.io/badge/code-Apache%202.0-blue.svg)](https://github.com/ast-outline/ast-outline/blob/main/LICENSE)
[![Docs: CC BY 4.0](https://img.shields.io/badge/docs-CC%20BY%204.0-lightgrey.svg)](https://github.com/ast-outline/ast-outline/blob/main/LICENSE-DOCS)
[![PyPI](https://img.shields.io/pypi/v/ast-outline.svg)](https://pypi.org/project/ast-outline/)
[![Python: 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://pypi.org/project/ast-outline/)

---

## See it live

=== ":simple-python: Python"

    <pre class="ast-console"><span class="ast-prompt">$</span> ast-outline src/services/billing.py
    <span class="ast-comment"># src/services/billing.py (412 lines)</span>
    <span class="ast-keyword">class</span> <span class="ast-type">BillingService</span> : <span class="ast-type">BaseService</span>                            <span class="ast-line">L18-298</span>
      <span class="ast-method">__init__</span>(<span class="ast-keyword">self</span>, db, gateway)                            <span class="ast-line">L24-31</span>
      <span class="ast-method">create_invoice</span>(<span class="ast-keyword">self</span>, customer_id, items)               <span class="ast-line">L42-78</span>
      <span class="ast-method">capture_payment</span>(<span class="ast-keyword">self</span>, invoice_id) <span class="ast-marker">[async]</span>           <span class="ast-line">L82-118</span>
      <span class="ast-method">refund</span>(<span class="ast-keyword">self</span>, invoice_id, amount) <span class="ast-marker">[async]</span>            <span class="ast-line">L122-156</span>
      <span class="ast-method">_total</span>(<span class="ast-keyword">self</span>, items) <span class="ast-marker">[classmethod]</span>                    <span class="ast-line">L260-274</span>
      ...
    <span class="ast-keyword">def</span> <span class="ast-method">format_currency</span>(amount, currency=<span class="ast-keyword">'USD'</span>)             <span class="ast-line">L301-318</span>
    <span class="ast-keyword">def</span> <span class="ast-method">parse_amount</span>(raw)                                  <span class="ast-line">L321-342</span></pre>

=== ":simple-rust: Rust"

    <pre class="ast-console"><span class="ast-prompt">$</span> ast-outline src/parser.rs
    <span class="ast-comment"># src/parser.rs (847 lines)</span>
    <span class="ast-keyword">pub struct</span> <span class="ast-type">Parser</span>&lt;<span class="ast-type">'src</span>&gt;                                 <span class="ast-line">L24-512</span>
      <span class="ast-method">new</span>(source: &amp;<span class="ast-type">'src str</span>) -&gt; <span class="ast-type">Self</span>                       <span class="ast-line">L40-58</span>
      <span class="ast-method">parse</span>(&amp;<span class="ast-keyword">mut self</span>) -&gt; <span class="ast-type">Result</span>&lt;<span class="ast-type">Ast</span>, <span class="ast-type">ParseError</span>&gt;          <span class="ast-line">L62-198</span>
      <span class="ast-method">peek</span>(&amp;<span class="ast-keyword">self</span>) -&gt; <span class="ast-type">Option</span>&lt;<span class="ast-type">Token</span>&gt; <span class="ast-marker">[const]</span>             <span class="ast-line">L210-218</span>
      <span class="ast-method">advance</span>(&amp;<span class="ast-keyword">mut self</span>) <span class="ast-marker">[unsafe]</span>                          <span class="ast-line">L222-234</span>
    <span class="ast-keyword">pub trait</span> <span class="ast-type">Visitor</span> : <span class="ast-type">Send</span>, <span class="ast-type">Sync</span>                       <span class="ast-line">L520-580</span>
      <span class="ast-method">visit_expr</span>(&amp;<span class="ast-keyword">mut self</span>, expr: &amp;<span class="ast-type">Expr</span>)                  <span class="ast-line">L535-545</span>
      <span class="ast-method">visit_stmt</span>(&amp;<span class="ast-keyword">mut self</span>, stmt: &amp;<span class="ast-type">Stmt</span>)                  <span class="ast-line">L548-560</span></pre>

=== ":simple-typescript: TypeScript"

    <pre class="ast-console"><span class="ast-prompt">$</span> ast-outline src/components/UserCard.tsx
    <span class="ast-comment"># src/components/UserCard.tsx (218 lines)</span>
    <span class="ast-keyword">interface</span> <span class="ast-type">UserCardProps</span>                                 <span class="ast-line">L8-18</span>
      user: <span class="ast-type">User</span>
      onEdit: (id: <span class="ast-type">string</span>) =&gt; <span class="ast-type">void</span>
      compact?: <span class="ast-type">boolean</span>
    <span class="ast-keyword">export const</span> <span class="ast-method">UserCard</span> : <span class="ast-type">React.FC</span>&lt;<span class="ast-type">UserCardProps</span>&gt;        <span class="ast-line">L22-148</span>
    <span class="ast-keyword">function</span> <span class="ast-method">getInitials</span>(name: <span class="ast-type">string</span>)                       <span class="ast-line">L152-164</span>
    <span class="ast-keyword">async function</span> <span class="ast-method">fetchAvatar</span>(userId: <span class="ast-type">string</span>) <span class="ast-marker">[async]</span>     <span class="ast-line">L168-198</span></pre>

=== ":octicons-markdown-16: Markdown"

    <pre class="ast-console"><span class="ast-prompt">$</span> ast-outline ARCHITECTURE.md
    <span class="ast-comment"># ARCHITECTURE.md (542 lines)</span>
    <span class="ast-keyword">#</span> <span class="ast-type">Architecture</span>                                          <span class="ast-line">L1-1</span>
    <span class="ast-keyword">##</span> <span class="ast-type">Overview</span>                                              <span class="ast-line">L8-8</span>
    <span class="ast-keyword">##</span> <span class="ast-type">Module layout</span>                                         <span class="ast-line">L42-42</span>
    <span class="ast-keyword">###</span> <span class="ast-type">Adapters</span>                                             <span class="ast-line">L78-78</span>
    <span class="ast-keyword">###</span> <span class="ast-type">Renderers</span>                                            <span class="ast-line">L156-156</span>
    <span class="ast-keyword">###</span> <span class="ast-type">CLI</span>                                                  <span class="ast-line">L234-234</span>
    <span class="ast-keyword">##</span> <span class="ast-type">Data flow</span>                                             <span class="ast-line">L312-312</span>
    <span class="ast-keyword">##</span> <span class="ast-type">Performance</span>                                           <span class="ast-line">L420-420</span></pre>

---

## Why use `ast-outline`

Modern agentic coding tools (Claude Code, Cursor's agent mode, Aider,
Copilot Chat, custom CLI agents) explore codebases by reading files
directly — not via embeddings or vector search. Reliable, but on a
1000-line file the agent pays for **1000 lines of tokens** just to
answer *"what methods exist here?"*.

`ast-outline` is a **pre-reading layer**:

<div class="grid cards" markdown>

- :material-database-arrow-down: **5–10× fewer tokens**

    An outline replaces a full file read when the agent only needs
    structural understanding.

- :material-map-search: **Faster exploration**

    A whole module's public API fits on one screen — `digest src/`
    gives the map in one call.

- :material-target: **Precise navigation**

    Every declaration carries a line range (`L42-58`). The agent
    jumps straight to the body it needs.

- :material-tree-outline: **AST accuracy, not regex**

    `show` and inheritance rendering work on real syntax — no false
    positives from comments or string literals.

- :material-cloud-off-outline: **Zero infrastructure**

    No index, no cache, no embeddings, no network. Always fresh,
    invisible to the repo.

- :material-format-list-checks: **One tool, ten languages**

    C#, Python, TypeScript/JS, Java, Kotlin, Scala, Go, Rust,
    Markdown, YAML — same digest format, same legend.

</div>

---

## How it stacks up

<table class="ast-compare">
<thead>
<tr>
  <th>Need</th>
  <th>grep/rg</th>
  <th>ctags</th>
  <th>ast-grep</th>
  <th>ast-outline</th>
</tr>
</thead>
<tbody>
<tr>
  <td>"What's the public API of this module?"</td>
  <td class="no">no</td>
  <td class="meh">flat list</td>
  <td class="no">no</td>
  <td class="yes">yes</td>
</tr>
<tr>
  <td>"What methods does <code>Player</code> expose?"</td>
  <td class="no">noisy</td>
  <td class="meh">scattered</td>
  <td class="meh">need a query</td>
  <td class="yes">yes</td>
</tr>
<tr>
  <td>"Show me the body of <code>TakeDamage</code>"</td>
  <td class="no">manual</td>
  <td class="meh">jump-to-line</td>
  <td class="meh">need a query</td>
  <td class="yes">yes</td>
</tr>
<tr>
  <td>Inheritance / trait bases visible</td>
  <td class="no">no</td>
  <td class="no">no</td>
  <td class="meh">need a query</td>
  <td class="yes">yes</td>
</tr>
<tr>
  <td>Find a pattern (<code>if x &amp;&amp; y</code>)</td>
  <td class="meh">regex</td>
  <td class="no">no</td>
  <td class="yes">yes</td>
  <td class="no">overview only</td>
</tr>
<tr>
  <td>Fixed cost regardless of repo size</td>
  <td class="yes">yes</td>
  <td class="meh">build index</td>
  <td class="yes">yes</td>
  <td class="yes">yes</td>
</tr>
</tbody>
</table>

`ast-outline` and `ast-grep` are siblings: **`ast-grep` searches**
code structurally, **`ast-outline` overviews** it. They compose well
— use `digest` for the map, `ast-grep` for cross-file pattern hunts.

---

## Install { #install }

=== "uv (recommended)"

    ```bash
    uv tool install ast-outline
    ```

    Don't have [`uv`](https://docs.astral.sh/uv/)?

    ```bash
    curl -LsSf https://astral.sh/uv/install.sh | sh        # macOS / Linux
    powershell -c "irm https://astral.sh/uv/install.ps1 | iex"   # Windows
    ```

=== "pipx"

    ```bash
    pipx install ast-outline
    ```

=== "pip"

    ```bash
    pip install ast-outline
    ```

=== "from source"

    ```bash
    uv tool install git+https://github.com/ast-outline/ast-outline.git
    ```

After install:

```bash
ast-outline --version          # 0.6.1
ast-outline path/to/Player.cs  # outline one file
ast-outline digest src/        # whole-folder map
ast-outline help               # full reference
```

---

## Design philosophy

> **Stateless. No index, no cache, no embeddings, no network.**
> Parse on demand, print, exit.

Opposite of RAG-style codebase indexers (Cursor, Bloop, Continue,
the embedding-MCP crowd). LLM agents are sharp enough to chain
`ast-outline` with `grep`, `find`, `ast-grep` and other unix tools
and navigate real code fast — without reading whole files, and
without a local index earning its complexity.

---

## Supported languages

| Language   | Extensions |
| ---        | --- |
| C#         | `.cs` |
| Python     | `.py`, `.pyi` |
| TypeScript | `.ts`, `.tsx` |
| JavaScript | `.js`, `.jsx`, `.mjs`, `.cjs` *(via the TS grammar)* |
| Java       | `.java` |
| Kotlin     | `.kt`, `.kts` |
| Scala      | `.scala`, `.sc` |
| Go         | `.go` |
| Rust       | `.rs` |
| Markdown   | `.md`, `.markdown`, `.mdx`, `.mdown` |
| YAML       | `.yaml`, `.yml` |

Adding another language is a single new adapter file. See
[`src/ast_outline/adapters/`](https://github.com/ast-outline/ast-outline/tree/main/src/ast_outline/adapters).

---

## Read next

<div class="grid cards" markdown>

-   :material-console: **[Commands](commands.md)**

    Outline, digest, show, prompt — full CLI reference with examples.

-   :material-robot: **[LLM agents](agents.md)**

    How to wire `ast-outline` into Claude Code, Cursor, Aider, custom
    agents.

-   :material-format-list-bulleted: **[Output format](output-format.md)**

    Digest legend, marker tags, size labels, broken-tag handling.

-   :material-source-branch: **[GitHub](https://github.com/ast-outline/ast-outline)**

    Source, issues, releases, contribution guide.

</div>
