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

</div>

[![Code: Apache 2.0](https://img.shields.io/badge/code-Apache%202.0-blue.svg)](https://github.com/ast-outline/ast-outline/blob/main/LICENSE)
[![Docs: CC BY 4.0](https://img.shields.io/badge/docs-CC%20BY%204.0-lightgrey.svg)](https://github.com/ast-outline/ast-outline/blob/main/LICENSE-DOCS)
[![PyPI](https://img.shields.io/pypi/v/ast-outline.svg)](https://pypi.org/project/ast-outline/)
[![Python: 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://pypi.org/project/ast-outline/)

---

## See it live

Real `ast-outline` output — including extracted **doc-comments**,
**type annotations**, **decorators / attributes**, and per-file stats
in the header. No method bodies, no fluff. Each tab below is the
actual textual output of running `ast-outline` against a file in the
language; syntax-highlighted by **Pygments** so the structure reads
the same way it does in your editor.

=== ":material-language-python: Python"

    ```python title="$ ast-outline src/ast_outline/cli.py"
    # src/ast_outline/cli.py (563 lines, ~5,326 tokens, 2 types, 12 methods, 6 fields)
    SUBCOMMANDS  L30

    class _LLMArgumentParser(argparse.ArgumentParser)  L33-51
        """ArgumentParser that doesn't ``sys.exit`` on bad args.

            Default ``argparse`` behavior on bad arguments is to print to stderr
            and call ``sys.exit(2)``. For an LLM-facing CLI that breaks parallel
            bash chains in Claude Code. Instead we raise a sentinel exception
            that ``main()`` turns into a short ``# note:`` line on stdout..."""
        def error(self, message: str) -> None  L43-44
        def exit(self, status: int = 0, message: str | None = None) -> None  L46-51

    def main(argv: list[str] | None = None) -> int  L58-132

    def _cmd_version(_args) -> int  L135-146
        """Print version + authorship in the standard `tool x.y.z` form..."""

    def _cmd_prompt(_args) -> int  L149-158
        """Print the canonical copy-paste LLM-agent prompt snippet verbatim."""
    ```

=== ":material-language-typescript: TypeScript"

    ```typescript title="$ ast-outline src/storage_service.ts"
    # src/storage_service.ts (60 lines, ~352 tokens, 3 types, 8 methods, 10 fields)
    const DB_NAME = "demo-db"  L5
    const DB_VERSION = 1  L6

    interface DBSchema  L8-12
        projects: Project  L9
        documents: Document  L10
        settings: AppSettings  L11

    export class StorageService  L14-44
        private db: IDBDatabase | null  L15
        private initPromise: Promise<void> | null  L16
        async init(): Promise<void>  L18-22
        // Generic CRUD
        private async getAll<T>(storeName: keyof DBSchema): Promise<T[]>  L29-31
        async getProject(id: string): Promise<Project | null>  L33-35
        async saveProject(project: Project): Promise<void>  L37-39
        protected log(msg: string): void  L41-43

    export const storage = new StorageService()  L58
    export const language = new LanguageService()  L59
    ```

=== ":material-language-rust: Rust"

    ```rust title="$ ast-outline src/user_service.rs"
    # src/user_service.rs (72 lines, ~396 tokens, 3 types, 9 methods, 6 fields)
    /// Represents a registered user account.
    ///
    /// Carries the public name visible to others plus the (private) raw
    /// id used for storage indexing.
    #[derive(Debug, Clone)] pub struct User  L10-47
        pub name: String  L11
        pub email: String  L12
        id: u64  L13
        /// Constructor — assigns a fresh id at creation time.
        pub fn new(name: String, email: String, id: u64) -> Self  L29-31
        /// Read-only accessor for the raw id.
        pub fn raw_id(&self) -> u64  L34-36

    /// Trait describing anything that can be addressed by a unique id.
    pub trait HasId  L17-20
        /// Numeric identifier — must be stable for the lifetime of the value.
        fn id(&self) -> u64  L19

    /// Service that owns a registry of users keyed by id.
    pub struct UserService  L23-63
        users: HashMap<u64, User>  L24
        pub fn register(&mut self, user: User)  L56-58
        pub fn lookup(&self, id: u64) -> Option<&User>  L60-62

    pub const MAX_USERS: u32 = 10_000  L70
    ```

=== ":material-language-csharp: C# (Unity)"

    ```csharp title="$ ast-outline Assets/Scripts/HeroController.cs"
    # Assets/Scripts/HeroController.cs (54 lines, ~385 tokens, 3 types, 4 methods, 5 fields)
    namespace Demo.Combat
        /// <summary>
        /// Controls the hero in-scene: movement, damage, death.
        /// </summary>
        [RequireComponent(typeof(Rigidbody2D))] public class HeroController : MonoBehaviour, IDamageable  L18-47
            [SerializeField] private float _speed = 5f  L21
            [SerializeField] private int _maxHealth = 100  L22
            public int CurrentHealth { get; private set; }  L24
            public bool IsAlive => CurrentHealth > 0  L25
            /// <summary>Fired whenever health changes.</summary>
            public event Action<int> OnHealthChanged  L28
            /// <summary>Apply damage to the hero.</summary>
            /// <param name="amount">HP to subtract.</param>
            public void TakeDamage(int amount)  L34-39
            private void Die()  L41-44
            public enum State  L46
                Idle, Moving, Dead  L46

        public interface IDamageable  L49-52
            void TakeDamage(int amount)  L51
    ```

=== ":material-language-java: Java"

    ```java title="$ ast-outline src/main/java/com/example/UserService.java"
    # UserService.java (64 lines, ~352 tokens, 3 types, 9 methods, 5 fields)
    namespace com.example.demo.service
        /**
         * Service layer for user accounts.
         *
         * <p>Demonstrates: Javadoc, multiple annotations, inheritance,
         * generics, throws, abstract methods, nested types.
         */
        @Service @Deprecated(since = "2.0", forRemoval = false) public class UserService extends BaseService implements UserRepository, AutoCloseable  L13-63
            /** Hard cap on concurrent users. */
            public static final int MAX_USERS = 100  L18
            private final String name  L20
            protected List<String> items  L21
            public UserService(String name)  L24-27
            /** Saves a user. */
            @Override public void save(User user) throws IOException, IllegalArgumentException  L34-37
            private static <T extends Comparable<T>> T findMax(List<T> items)  L39-41
            public abstract int compute()  L43
            @Override public void close()  L45-46
            public static final class Inner  L48-58
                public Inner(int value)  L51-53
                public int value()  L55-57
    ```

=== ":material-language-go: Go"

    ```go title="$ ast-outline service/user_service.go"
    # user_service.go (105 lines, ~635 tokens, 4 types, 9 methods, 9 fields)
    namespace service
        const MaxUsers = 100  L16
        var GlobalCounter int = 0  L23

        // BaseService is a top-level service primitive other services embed.
        type BaseService struct  L29-44
            Name string  L30
            closed bool  L32
            // Open marks the service as ready.
            func (b *BaseService) Open()  L36-38
            // close is unexported.
            func (b *BaseService) close() error  L41-44

        // UserService is the primary user-facing service.
        //
        // Embeds BaseService — Go's idiom for "extends BaseService" — and
        // implements the io.Closer interface contract via Close().
        type UserService struct  L50-78
            Repo Repository  L52
            cache map[string]any  L53
            // Save persists a user; returns an error on failure.
            func (u *UserService) Save(user string) error  L57-62
            // Close satisfies io.Closer.
            func (u *UserService) Close() error  L76-78

        // Repository is the contract user-stores must satisfy.
        type Repository interface  L81-88
            Get(id string) (string, error)  L83
            List() []string  L85
    ```

=== ":material-language-php: PHP (WordPress / Laravel)"

    ```php title="$ ast-outline app/Services/UserService.php"
    # app/Services/UserService.php (66 lines, ~317 tokens, 2 types, 8 methods, 5 fields)
    namespace App\Service
        /**
         * Coordinates user-related use cases.
         */
        final class UserService  L17-48
            private readonly Repository $repository  L24
            protected int $maxCacheSize = 100  L25
            public const string DEFAULT_ROLE = "guest"  L19
            public function __construct( private readonly Repository $repository, protected int $maxCacheSize = 100 )  L23-26
            /** Fetch a user by id, caching the result. */
            public function getUser(int $id): ?User  L31-34
            /** @deprecated Use getUser instead. */
            public function loadUser(int $id): ?User  L37-40
            public function makeOrder(User $user): OrderModel  L42-45
            private function flush(): void  L47

        abstract class BaseService  L50-58
            abstract public function name(): string  L52
            final public function tag(): string  L54-57

        function make_service(Repository $r): UserService  L60-63
        const APP_VERSION = "1.0.0"  L65
    ```

=== ":material-language-markdown: Markdown"

    ```markdown title="$ ast-outline README.md"
    # README.md (52 lines, ~153 tokens, 8 headings, 5 code blocks)
    # Sample Project  L1-51
        ## Installation  L5-26
            bash code block  L9-11
            ### From source  L13-20
                bash code block  L15-19
            ### Via pipx  L21-26
                bash code block  L23-25
        ## Usage  L27-44
            python code block  L31-34
            ### Configuration  L36-44
                code code block  L40-43
        ## Contributing  L45-48
        ## License  L49-51
    ```

!!! info "+ 4 more languages with the same digest format"
    `ast-outline` also handles **JavaScript** (`.js`/`.jsx`/`.mjs`/`.cjs` — parsed by
    the TypeScript grammar, so React/Node/ES-module files all flow through),
    **Kotlin** (`.kt`/`.kts` — Android, Compose, Spring; `data class`, `sealed`,
    `suspend`, KDoc), **Scala** (`.scala`/`.sc` — Scala 2 + Scala 3 with
    `enum`/`given`/`extension`, Scaladoc), and **YAML** (`.yaml`/`.yml` —
    Kubernetes / OpenAPI / GitHub Actions, with dotted key paths and
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

- :material-format-list-checks: **One tool, twelve languages**

    C#, Python, TypeScript, JavaScript, Java, Kotlin, Scala, Go, Rust,
    PHP, Markdown, YAML — same digest format, same legend.

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
ast-outline --version          # latest from PyPI
ast-outline path/to/Player.cs  # outline one file
ast-outline digest src/        # whole-folder map
ast-outline help               # full reference
```

### Update / uninstall

=== "uv"

    ```bash
    uv tool upgrade ast-outline      # update
    uv tool uninstall ast-outline    # remove
    ```

=== "pipx"

    ```bash
    pipx upgrade ast-outline
    pipx uninstall ast-outline
    ```

=== "pip"

    ```bash
    pip install -U ast-outline
    pip uninstall ast-outline
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

### Why no MCP server?

Same shape: a stateless CLI is the right form factor for this tool.
In coding agents (Claude Code, Cursor, Codex CLI, Aider) `ast-outline`
already runs through the agent's `bash` tool — composes with
`grep` / `find` / `xargs`, runs in parallel batches, and adds zero
registration overhead. An MCP server would either become a long-running
daemon (which kills the "parse on demand, exit" model) or a thin shim
around the same CLI — either way, more setup friction for users and a
second ABI to keep in sync, with no extra capability for the actual
target audience.

The narrow case where MCP would help is non-shell hosts (e.g. Claude
Desktop). If demand shows up there, a wrapper takes a day to write —
but it's a separate add-on, not a redesign.

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
| PHP        | `.php`, `.phtml`, `.phps`, `.php8` |
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
