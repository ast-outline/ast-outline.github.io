---
hide:
  - navigation
  - toc
---

<div class="ast-hero" markdown>

<h1 class="ast-hero__title"><span class="ast-hero__bracket">[</span>ast-outline<span class="ast-hero__bracket">]</span></h1>

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

<div class="ast-hero__stats">
<span><strong>11</strong> languages</span>
<span><strong>0</strong> runtime deps you write</span>
<span><strong>0</strong> indexes / caches</span>
<span><strong>5–10×</strong> token savings vs full reads</span>
<span><strong>~0.1s</strong> for 600+ tests</span>
</div>

</div>

[![Code: Apache 2.0](https://img.shields.io/badge/code-Apache%202.0-blue.svg)](https://github.com/ast-outline/ast-outline/blob/main/LICENSE)
[![Docs: CC BY 4.0](https://img.shields.io/badge/docs-CC%20BY%204.0-lightgrey.svg)](https://github.com/ast-outline/ast-outline/blob/main/LICENSE-DOCS)
[![PyPI](https://img.shields.io/pypi/v/ast-outline.svg)](https://pypi.org/project/ast-outline/)
[![Python: 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://pypi.org/project/ast-outline/)

---

## See it live

Real `ast-outline` output — including extracted **doc-comments**,
**type annotations**, **decorators / attributes**, and per-file stats
in the header. No method bodies, no fluff.

=== ":material-language-python: Python"

    <div class="ast-console" markdown="0">
    <div class="ast-console__bar"><span class="ast-console__dots"><span class="y"></span><span class="g"></span></span><span class="ast-console__title">cli.py — ast-outline</span></div>
    <div class="ast-console__cmd"><span class="ast-prompt">$</span> ast-outline src/ast_outline/cli.py</div>
    <pre class="ast-console__out"><span class="ast-comment"># src/ast_outline/cli.py (563 lines, ~5,326 tokens, 2 types, 12 methods, 6 fields)</span>
    <span class="ast-keyword">class</span> <span class="ast-type">_LLMArgumentParser</span>(argparse.ArgumentParser)  <span class="ast-line">L33-51</span>
        <span class="ast-doc">"""ArgumentParser that doesn't sys.exit on bad args.</span>
    <span class="ast-doc">    Default argparse behavior on bad arguments is to print to stderr</span>
    <span class="ast-doc">    and call sys.exit(2). For an LLM-facing CLI that breaks parallel</span>
    <span class="ast-doc">    bash chains in Claude Code. Instead we raise a sentinel exception</span>
    <span class="ast-doc">    that main() turns into a short # note: line on stdout..."""</span>
        <span class="ast-keyword">def</span> <span class="ast-method">error</span>(self, message: <span class="ast-type">str</span>) -&gt; <span class="ast-type">None</span>  <span class="ast-line">L43-44</span>
        <span class="ast-keyword">def</span> <span class="ast-method">exit</span>(self, status: <span class="ast-type">int</span> = 0, message: <span class="ast-type">str | None</span> = None) -&gt; <span class="ast-type">None</span>  <span class="ast-line">L46-51</span>

    <span class="ast-keyword">def</span> <span class="ast-method">main</span>(argv: <span class="ast-type">list[str] | None</span> = None) -&gt; <span class="ast-type">int</span>  <span class="ast-line">L58-132</span>
    <span class="ast-keyword">def</span> <span class="ast-method">_cmd_version</span>(_args) -&gt; <span class="ast-type">int</span>  <span class="ast-line">L135-146</span>
        <span class="ast-doc">"""Print version + authorship in the standard `tool x.y.z` form..."""</span>
    <span class="ast-keyword">def</span> <span class="ast-method">_cmd_prompt</span>(_args) -&gt; <span class="ast-type">int</span>  <span class="ast-line">L149-158</span>
        <span class="ast-doc">"""Print the canonical copy-paste LLM-agent prompt snippet verbatim."""</span></pre>
    </div>

=== ":material-language-typescript: TypeScript"

    <div class="ast-console" markdown="0">
    <div class="ast-console__bar"><span class="ast-console__dots"><span class="y"></span><span class="g"></span></span><span class="ast-console__title">storage_service.ts — ast-outline</span></div>
    <div class="ast-console__cmd"><span class="ast-prompt">$</span> ast-outline src/storage_service.ts</div>
    <pre class="ast-console__out"><span class="ast-comment"># src/storage_service.ts (60 lines, ~352 tokens, 3 types, 8 methods, 10 fields)</span>
    <span class="ast-keyword">const</span> <span class="ast-method">DB_NAME</span> = <span class="ast-string">"demo-db"</span>  <span class="ast-line">L5</span>
    <span class="ast-keyword">const</span> <span class="ast-method">DB_VERSION</span> = <span class="ast-string">1</span>  <span class="ast-line">L6</span>

    <span class="ast-keyword">interface</span> <span class="ast-type">DBSchema</span>  <span class="ast-line">L8-12</span>
        projects: <span class="ast-type">Project</span>  <span class="ast-line">L9</span>
        documents: <span class="ast-type">Document</span>  <span class="ast-line">L10</span>
        settings: <span class="ast-type">AppSettings</span>  <span class="ast-line">L11</span>

    <span class="ast-keyword">export class</span> <span class="ast-type">StorageService</span>  <span class="ast-line">L14-44</span>
        <span class="ast-keyword">private</span> db: <span class="ast-type">IDBDatabase | null</span>  <span class="ast-line">L15</span>
        <span class="ast-keyword">private</span> initPromise: <span class="ast-type">Promise&lt;void&gt; | null</span>  <span class="ast-line">L16</span>
        <span class="ast-keyword">async</span> <span class="ast-method">init</span>(): <span class="ast-type">Promise&lt;void&gt;</span>  <span class="ast-line">L18-22</span>
        <span class="ast-comment">// Generic CRUD</span>
        <span class="ast-keyword">private async</span> <span class="ast-method">getAll</span>&lt;<span class="ast-type">T</span>&gt;(storeName: <span class="ast-keyword">keyof</span> <span class="ast-type">DBSchema</span>): <span class="ast-type">Promise&lt;T[]&gt;</span>  <span class="ast-line">L29-31</span>
        <span class="ast-keyword">async</span> <span class="ast-method">getProject</span>(id: <span class="ast-type">string</span>): <span class="ast-type">Promise&lt;Project | null&gt;</span>  <span class="ast-line">L33-35</span>
        <span class="ast-keyword">async</span> <span class="ast-method">saveProject</span>(project: <span class="ast-type">Project</span>): <span class="ast-type">Promise&lt;void&gt;</span>  <span class="ast-line">L37-39</span>
        <span class="ast-keyword">protected</span> <span class="ast-method">log</span>(msg: <span class="ast-type">string</span>): <span class="ast-type">void</span>  <span class="ast-line">L41-43</span>

    <span class="ast-keyword">export const</span> <span class="ast-method">storage</span> = <span class="ast-keyword">new</span> <span class="ast-type">StorageService</span>()  <span class="ast-line">L58</span>
    <span class="ast-keyword">export const</span> <span class="ast-method">language</span> = <span class="ast-keyword">new</span> <span class="ast-type">LanguageService</span>()  <span class="ast-line">L59</span></pre>
    </div>

=== ":material-language-rust: Rust"

    <div class="ast-console" markdown="0">
    <div class="ast-console__bar"><span class="ast-console__dots"><span class="y"></span><span class="g"></span></span><span class="ast-console__title">user_service.rs — ast-outline</span></div>
    <div class="ast-console__cmd"><span class="ast-prompt">$</span> ast-outline src/user_service.rs</div>
    <pre class="ast-console__out"><span class="ast-comment"># src/user_service.rs (72 lines, ~396 tokens, 3 types, 9 methods, 6 fields)</span>
    <span class="ast-doc">/// Represents a registered user account.</span>
    <span class="ast-doc">///</span>
    <span class="ast-doc">/// Carries the public name visible to others plus the (private) raw</span>
    <span class="ast-doc">/// id used for storage indexing.</span>
    <span class="ast-attr">#[derive(Debug, Clone)]</span> <span class="ast-keyword">pub struct</span> <span class="ast-type">User</span>  <span class="ast-line">L10-47</span>
        <span class="ast-keyword">pub</span> name: <span class="ast-type">String</span>  <span class="ast-line">L11</span>
        <span class="ast-keyword">pub</span> email: <span class="ast-type">String</span>  <span class="ast-line">L12</span>
        id: <span class="ast-type">u64</span>  <span class="ast-line">L13</span>
        <span class="ast-doc">/// Constructor — assigns a fresh id at creation time.</span>
        <span class="ast-keyword">pub fn</span> <span class="ast-method">new</span>(name: <span class="ast-type">String</span>, email: <span class="ast-type">String</span>, id: <span class="ast-type">u64</span>) -&gt; <span class="ast-type">Self</span>  <span class="ast-line">L29-31</span>
        <span class="ast-doc">/// Read-only accessor for the raw id.</span>
        <span class="ast-keyword">pub fn</span> <span class="ast-method">raw_id</span>(&amp;self) -&gt; <span class="ast-type">u64</span>  <span class="ast-line">L34-36</span>

    <span class="ast-doc">/// Trait describing anything that can be addressed by a unique id.</span>
    <span class="ast-keyword">pub trait</span> <span class="ast-type">HasId</span>  <span class="ast-line">L17-20</span>
        <span class="ast-doc">/// Numeric identifier — must be stable for the lifetime of the value.</span>
        <span class="ast-keyword">fn</span> <span class="ast-method">id</span>(&amp;self) -&gt; <span class="ast-type">u64</span>  <span class="ast-line">L19</span>

    <span class="ast-doc">/// Service that owns a registry of users keyed by id.</span>
    <span class="ast-keyword">pub struct</span> <span class="ast-type">UserService</span>  <span class="ast-line">L23-63</span>
        users: <span class="ast-type">HashMap&lt;u64, User&gt;</span>  <span class="ast-line">L24</span>
        <span class="ast-keyword">pub fn</span> <span class="ast-method">register</span>(&amp;<span class="ast-keyword">mut</span> self, user: <span class="ast-type">User</span>)  <span class="ast-line">L56-58</span>
        <span class="ast-keyword">pub fn</span> <span class="ast-method">lookup</span>(&amp;self, id: <span class="ast-type">u64</span>) -&gt; <span class="ast-type">Option&lt;&amp;User&gt;</span>  <span class="ast-line">L60-62</span>

    <span class="ast-keyword">pub const</span> <span class="ast-method">MAX_USERS</span>: <span class="ast-type">u32</span> = <span class="ast-string">10_000</span>  <span class="ast-line">L70</span></pre>
    </div>

=== ":material-language-java: Java"

    <div class="ast-console" markdown="0">
    <div class="ast-console__bar"><span class="ast-console__dots"><span class="y"></span><span class="g"></span></span><span class="ast-console__title">UserService.java — ast-outline</span></div>
    <div class="ast-console__cmd"><span class="ast-prompt">$</span> ast-outline src/main/java/com/example/UserService.java</div>
    <pre class="ast-console__out"><span class="ast-comment"># UserService.java (64 lines, ~352 tokens, 3 types, 9 methods, 5 fields)</span>
    <span class="ast-keyword">namespace</span> <span class="ast-type">com.example.demo.service</span>
        <span class="ast-doc">/**</span>
    <span class="ast-doc">     * Service layer for user accounts.</span>
    <span class="ast-doc">     *</span>
    <span class="ast-doc">     * &lt;p&gt;Demonstrates: Javadoc, multiple annotations, inheritance,</span>
    <span class="ast-doc">     * generics, throws, abstract methods, nested types.</span>
    <span class="ast-doc">     */</span>
        <span class="ast-attr">@Service @Deprecated(since = "2.0")</span> <span class="ast-keyword">public class</span> <span class="ast-type">UserService</span> <span class="ast-keyword">extends</span> <span class="ast-type">BaseService</span> <span class="ast-keyword">implements</span> <span class="ast-type">UserRepository</span>, <span class="ast-type">AutoCloseable</span>  <span class="ast-line">L13-63</span>
            <span class="ast-doc">/** Hard cap on concurrent users. */</span>
            <span class="ast-keyword">public static final int</span> <span class="ast-method">MAX_USERS</span> = <span class="ast-string">100</span>  <span class="ast-line">L18</span>
            <span class="ast-keyword">private final</span> <span class="ast-type">String</span> <span class="ast-method">name</span>  <span class="ast-line">L20</span>
            <span class="ast-keyword">protected</span> <span class="ast-type">List&lt;String&gt;</span> <span class="ast-method">items</span>  <span class="ast-line">L21</span>
            <span class="ast-keyword">public</span> <span class="ast-method">UserService</span>(<span class="ast-type">String</span> name)  <span class="ast-line">L24-27</span>
            <span class="ast-doc">/** Saves a user. */</span>
            <span class="ast-attr">@Override</span> <span class="ast-keyword">public void</span> <span class="ast-method">save</span>(<span class="ast-type">User</span> user) <span class="ast-keyword">throws</span> <span class="ast-type">IOException</span>, <span class="ast-type">IllegalArgumentException</span>  <span class="ast-line">L34-37</span>
            <span class="ast-keyword">private static</span> &lt;<span class="ast-type">T</span> <span class="ast-keyword">extends</span> <span class="ast-type">Comparable&lt;T&gt;</span>&gt; <span class="ast-type">T</span> <span class="ast-method">findMax</span>(<span class="ast-type">List&lt;T&gt;</span> items)  <span class="ast-line">L39-41</span>
            <span class="ast-keyword">public abstract int</span> <span class="ast-method">compute</span>()  <span class="ast-line">L43</span>
            <span class="ast-attr">@Override</span> <span class="ast-keyword">public void</span> <span class="ast-method">close</span>()  <span class="ast-line">L45-46</span></pre>
    </div>

=== ":material-language-go: Go"

    <div class="ast-console" markdown="0">
    <div class="ast-console__bar"><span class="ast-console__dots"><span class="y"></span><span class="g"></span></span><span class="ast-console__title">user_service.go — ast-outline</span></div>
    <div class="ast-console__cmd"><span class="ast-prompt">$</span> ast-outline service/user_service.go</div>
    <pre class="ast-console__out"><span class="ast-comment"># user_service.go (105 lines, ~635 tokens, 4 types, 9 methods, 9 fields)</span>
    <span class="ast-keyword">namespace</span> <span class="ast-type">service</span>
        <span class="ast-keyword">const</span> <span class="ast-method">MaxUsers</span> = <span class="ast-string">100</span>  <span class="ast-line">L16</span>
        <span class="ast-keyword">var</span> <span class="ast-method">GlobalCounter</span> <span class="ast-type">int</span> = <span class="ast-string">0</span>  <span class="ast-line">L23</span>

        <span class="ast-comment">// BaseService is a top-level service primitive other services embed.</span>
        <span class="ast-keyword">type</span> <span class="ast-type">BaseService</span> <span class="ast-keyword">struct</span>  <span class="ast-line">L29-44</span>
            <span class="ast-method">Name</span> <span class="ast-type">string</span>  <span class="ast-line">L30</span>
            closed <span class="ast-type">bool</span>  <span class="ast-line">L32</span>
            <span class="ast-comment">// Open marks the service as ready.</span>
            <span class="ast-keyword">func</span> (b *<span class="ast-type">BaseService</span>) <span class="ast-method">Open</span>()  <span class="ast-line">L36-38</span>
            <span class="ast-comment">// close is unexported.</span>
            <span class="ast-keyword">func</span> (b *<span class="ast-type">BaseService</span>) <span class="ast-method">close</span>() <span class="ast-type">error</span>  <span class="ast-line">L41-44</span>

        <span class="ast-comment">// UserService is the primary user-facing service.</span>
        <span class="ast-comment">//</span>
        <span class="ast-comment">// Embeds BaseService — Go's idiom for "extends BaseService" — and</span>
        <span class="ast-comment">// implements the io.Closer interface contract via Close().</span>
        <span class="ast-keyword">type</span> <span class="ast-type">UserService</span> <span class="ast-keyword">struct</span>  <span class="ast-line">L50-78</span>
            <span class="ast-method">Repo</span> <span class="ast-type">Repository</span>  <span class="ast-line">L52</span>
            cache <span class="ast-keyword">map</span>[<span class="ast-type">string</span>]<span class="ast-keyword">any</span>  <span class="ast-line">L53</span>
            <span class="ast-comment">// Save persists a user; returns an error on failure.</span>
            <span class="ast-keyword">func</span> (u *<span class="ast-type">UserService</span>) <span class="ast-method">Save</span>(user <span class="ast-type">string</span>) <span class="ast-type">error</span>  <span class="ast-line">L57-62</span>
            <span class="ast-comment">// Close satisfies io.Closer.</span>
            <span class="ast-keyword">func</span> (u *<span class="ast-type">UserService</span>) <span class="ast-method">Close</span>() <span class="ast-type">error</span>  <span class="ast-line">L76-78</span></pre>
    </div>

!!! info "+ 6 more languages with the same digest format"
    `ast-outline` also handles **JavaScript** (`.js`/`.jsx`/`.mjs`/`.cjs`),
    **C#** (`.cs` — Unity, ASP.NET, anything), **Kotlin** (`.kt`/`.kts` — Android,
    Spring), **Scala** (`.scala`/`.sc` — Scala 2 + Scala 3 with `enum`/`given`/`extension`),
    **Markdown** (`.md`/`.mdx` — heading hierarchy + code blocks), and **YAML**
    (`.yaml`/`.yml` — Kubernetes / OpenAPI / GitHub Actions, with key paths and
    sequence indices).

    Same digest format, same legend, same `[broken]` recovery semantics. Adding
    another language is a single new adapter file —
    [`src/ast_outline/adapters/`](https://github.com/ast-outline/ast-outline/tree/main/src/ast_outline/adapters).

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
