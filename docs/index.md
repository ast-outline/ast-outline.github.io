---
hide:
  - navigation
  - toc
---

<div class="ast-hero" markdown>

<h1 class="ast-hero__title"><span class="ast-hero__bracket">[</span>ast-outline<span class="ast-hero__bracket">]</span></h1>

<p class="ast-hero__tagline">
A CLI that prints the <strong>structural outline</strong> of a source
file — classes, methods, signatures with line numbers,
<strong>no method bodies</strong>. Output is
<strong>5–10× smaller than the source</strong>, built for AI coding
agents (Claude Code, Cursor, Codex CLI) that scan a repo before reading
any file in full.
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

Each block is real `ast-outline` stdout — doc-comments, type
annotations, decorators, line ranges, no method bodies — syntax-
highlighted by Pygments. The italic line under the picker is context
(codebase, file kind), not part of the output.

<div class="ast-langs" markdown="1">

=== ":material-language-python: Python"

    *Real codebase — `ast-outline/cli.py` (this project itself).*

    ```python title="$ ast-outline src/ast_outline/cli.py"
    # src/ast_outline/cli.py [large] (563 lines, ~5,326 tokens, 2 types, 12 methods, 6 fields)
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

    *Browser app — IndexedDB storage layer with generic CRUD and async methods.*

    ```typescript title="$ ast-outline src/storage_service.ts"
    # src/storage_service.ts [tiny] (60 lines, ~352 tokens, 3 types, 8 methods, 10 fields)
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

=== ":material-language-javascript: JavaScript"

    *Node service module — parsed by the TypeScript grammar, so React / ES modules / CommonJS all flow through.*

    ```js title="$ ast-outline server/auth/session.js"
    # server/auth/session.js [tiny] (96 lines, ~389 tokens, 1 type, 6 methods, 2 fields)
    const SESSION_TTL_SEC  L4
    const REFRESH_GRACE_SEC  L5

    /** Minimal cookie-backed session manager — tokens are signed with */
    /** HMAC-SHA256 and pinned to user-agent + IP fingerprint. */
    class SessionManager  L9-94
        constructor(secret, store)  L13-17
        async create(userId, request)  L19-32
        async verify(token, request)  L34-58
        async refresh(token)  L60-78
        async revoke(token)  L80-86
        _signToken(payload)  L88-93
    ```

=== ":material-language-rust: Rust"

    *Library crate — struct + trait + service with `///` doc-comments and `#[derive]`.*

    ```rust title="$ ast-outline src/user_service.rs"
    # src/user_service.rs [tiny] (72 lines, ~396 tokens, 3 types, 9 methods, 6 fields)
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

=== ":material-language-csharp: C#"

    *Unity MonoBehaviour — `[Attribute]` decorators and XML doc-comments preserved.*

    ```csharp title="$ ast-outline Assets/Scripts/HeroController.cs"
    # Assets/Scripts/HeroController.cs [tiny] (54 lines, ~385 tokens, 3 types, 4 methods, 5 fields)
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

=== ":material-language-cpp: C++"

    *Unreal Engine actor — UCLASS / UPROPERTY / UFUNCTION render as decorators on the right declaration. Plain modern C++ works the same way without UE.*

    ```cpp title="$ ast-outline Source/MyGame/MyActor.h"
    # MyActor.h [medium] (97 lines, ~548 tokens, 6 types, 9 methods, 7 fields)
    UENUM(BlueprintType) enum class EWeaponSlot : uint8  L15-20
        Primary  L17
        Secondary  L18
        Sidearm  L19

    USTRUCT(BlueprintType) struct FItemData  L23-35
        UPROPERTY(EditAnywhere, BlueprintReadWrite) FString Name  L28
        UPROPERTY(EditAnywhere, BlueprintReadWrite) int32 Count  L31
        UPROPERTY() EWeaponSlot Slot  L34

    UINTERFACE(MinimalAPI, Blueprintable) class UInteractable : public UInterface  L38-41

    class IInteractable  L43-50
        UFUNCTION(BlueprintNativeEvent, BlueprintCallable, Category="Interaction") void Interact()  L49

    UCLASS(Blueprintable, BlueprintType) class AMyActor : public AActor, public IInteractable  L53-81
        AMyActor()  L58
        UPROPERTY(EditAnywhere, BlueprintReadWrite, Category="Stats") float Health  L61
        UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category="Components") UStaticMeshComponent* Mesh  L64
        UPROPERTY(EditDefaultsOnly, Category="Combat") TArray<FItemData> Inventory  L67
        UFUNCTION(BlueprintCallable, Category="Combat") virtual void TakeDamage(float Amount)  L70
        UFUNCTION(BlueprintNativeEvent, Category="Events") void OnHit()  L73
        UFUNCTION() void HandleOverlap(AActor* Other)  L76
        virtual void BeginPlay() override  L79
        virtual void Tick(float DeltaSeconds) override  L80
    ```

=== ":material-language-java: Java"

    *Spring-style service — Javadoc, annotations, generics, abstract + nested types.*

    ```java title="$ ast-outline src/main/java/com/example/UserService.java"
    # UserService.java [tiny] (64 lines, ~352 tokens, 3 types, 9 methods, 5 fields)
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

=== ":material-language-kotlin: Kotlin"

    *Android Compose ViewModel — `data class`, `sealed class`, `suspend`, `@Composable`.*

    ```kotlin title="$ ast-outline app/src/main/kotlin/ProfileScreen.kt"
    # app/src/main/kotlin/ProfileScreen.kt [tiny] (105 lines, ~451 tokens, 3 types, 5 methods, 2 fields)
    data class UserProfile(val id: String, val name: String, val avatarUrl: String?)  L9

    sealed class ProfileState  L11-15
        object Loading  L12
        data class Success(val profile: UserProfile)  L13
        data class Error(val message: String)  L14

    @HiltViewModel
    class ProfileViewModel : ViewModel()  L19-50
        private val _state: MutableStateFlow<ProfileState>  L21
        val state: StateFlow<ProfileState>  L22

        suspend fun loadProfile(userId: String)  L24-37
        fun refresh()  L39-41
        private suspend fun fetchAndCache(userId: String): UserProfile  L43-49

    @Composable
    fun ProfileScreen(viewModel: ProfileViewModel = hiltViewModel())  L53-105
    ```

=== ":simple-scala: Scala"

    *Scala 3 — `enum`, `given`, `extension`. Indentation-based bodies.*

    ```scala title="$ ast-outline core/src/main/scala/Json.scala"
    # core/src/main/scala/Json.scala [medium] (118 lines, ~492 tokens, 3 types, 7 methods)
    package json  L1

    enum Json  L4-12
        case JNull [enum_member]  L5
        case JBool(value: Boolean) [enum_member]  L6
        case JNumber(value: Double) [enum_member]  L7
        case JString(value: String) [enum_member]  L8
        case JArray(items: List[Json]) [enum_member]  L9
        case JObject(fields: Map[String, Json]) [enum_member]  L10

    trait Encoder[A]  L15-17
        def encode(value: A): Json

    object Encoder  L19-58
        given Encoder[Int]  L21-22
        given Encoder[String]  L24-25
        given [A](using e: Encoder[A]): Encoder[List[A]]  L27-30

    extension [A](value: A)(using enc: Encoder[A])  L33-37
        def toJson: Json  L34
        def toJsonString: String  L36
    ```

=== ":material-language-go: Go"

    *Standard service — methods grouped under their receiver type.*

    ```go title="$ ast-outline service/user_service.go"
    # user_service.go [medium] (105 lines, ~635 tokens, 4 types, 9 methods, 9 fields)
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

=== ":material-language-php: PHP"

    *Laravel-style service — PHP 8 readonly fields, ctor property promotion, typed constants.*

    ```php title="$ ast-outline app/Services/UserService.php"
    # app/Services/UserService.php [tiny] (66 lines, ~317 tokens, 2 types, 8 methods, 5 fields)
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

=== ":simple-ruby: Ruby"

    *Rails ActiveRecord-style model — `has_many` / `belongs_to` associations and `attr_accessor` surface as fields, mixins on the type header.*

    ```ruby title="$ ast-outline app/models/user.rb"
    # app/models/user.rb [tiny] (52 lines, ~242 tokens, 1 type, 9 methods, 8 fields)
    class User < ApplicationRecord  L8-50
        MAX_NAME_LENGTH: field  L11
        DEFAULT_ROLE: field  L12
        [has_many] posts  L14
        [belongs_to] company  L15
        [accessor] name  L17
        [accessor] email  L17
        [reader] id  L18
        # rdoc-style constructor doc.
        def initialize(name, email)  L21-24
        def display_name  L26-28
        [static] def self.find_by_name(name)  L30-32
        def <=>(other)  L35-37
        def ==(other)  L39-41
        def [](key)  L43-45
        def []=(key, value)  L47-49
    ```

    Also recognised: `class << self` blocks, `alias` / `alias_method`, `private` / `protected` state machine (`private :foo, :bar` flips named methods retroactively), `Rakefile` / `Gemfile` (resolved by basename, no extension needed). The MRO clause `: ApplicationRecord, include Comparable, extend Searchable` shows superclass + mixins as one inheritance line in the digest.

=== ":material-language-css3: CSS"

    *Design tokens + components — `:root` token block, themed selectors via `[data-theme=dark]`, `@media` / `@keyframes` / `@layer` / `@font-face`, native nesting with `&`.*

    ```css title="$ ast-outline src/styles/theme.css"
    # src/styles/theme.css [tiny] (96 lines, ~371 tokens)
    # imports: @import url("reset-extended.css"); @import "vendor/normalize.css" layer(reset)
    :root  L2-12
    [data-theme=dark]  L14-19
    *, *::before, *::after  L23-27
    body  L29-34
    .container  L37-41
    #main-header > .nav .item:hover  L44-46
    .btn-primary, .btn-secondary  L49-55
    .modal .btn-primary[disabled]  L58-61
    @media (max-width: 768px)  L64-71
        .container  L65-67
        .btn-primary  L69-70
    @media (min-width: 769px) and (max-width: 1024px)  L73-77
        .container  L74-76
    @keyframes fadeIn  L80-83
    @keyframes slideUp  L85-88
    @layer base  L91-95
        h1, h2, h3  L92-94
    @font-face  L98-101
    :is(.alert, .warning, .error)  L104-107
    :not(.disabled)  L109-111
    ```

    `find_symbols(".btn-primary")` returns every cascade-relevant rule — top-level group, the `@media` override, and `.modal .btn-primary[disabled]` descendant — with the wrapping at-rule visible in the breadcrumb. Pseudo-classes and attribute filters are stripped for matching, so `.btn-primary:hover` and `.btn-primary[disabled]` both match `.btn-primary`. `:is()` / `:where()` arguments recurse (additive); `:not()` / `:has()` don't.

=== ":material-language-css3: SCSS"

    *Component stylesheet — `&` resolves against the parent, so nested rules become findable as their fully-qualified BEM selectors. Mixins and functions render as callables.*

    ```scss title="$ ast-outline styles/_components.scss"
    # styles/_components.scss [tiny] (84 lines, ~370 tokens)
    # imports: @use "sass:math"; @use "../tokens" as t; @forward "icons"
    $primary: #007bff !default  L8
    $secondary: #6c757d !default  L9
    $radius: 4px  L10
    %button-base  L18-24
    @mixin button($bg: $primary, $fg: white, $size: medium)  L29-46
    @function strip-unit($value)  L54-56
    .btn  L62-64
    .btn-primary  L66-68
    .btn-secondary  L70-77
        .btn-secondary.disabled  L73-76
    .card  L80-108
        .card__header  L84-92
            h2  L88-91
        .card__body  L94-96
        .card--featured  L98-107
            .card__header  L101-106
    @media (max-width: 768px)  L113-122
        .card  L114-121
            .card__header, .card__body  L117-120
    ```

    `find_symbols(".card__header")` returns every cascade-relevant definition — top-level, inside `@media`, themed under `.card--featured` — with the wrapping at-rule visible in the breadcrumb. `:is(...)` / `:where(...)` selectors recurse (additive); `:not(...)` / `:has(...)` don't. Pseudo-classes and attribute filters are stripped for matching, so `.btn-primary` finds the rule whether it carries `:hover`, `[disabled]`, or sits in `.modal`. Plain CSS gets the same treatment minus the SCSS-specific symbols.

=== ":simple-yaml: YAML"

    *Kubernetes Deployment — format auto-detected, sequence items use `[i]` paths.*

    ```yaml title="$ ast-outline deploy/k8s_deployment.yaml"
    # deploy/k8s_deployment.yaml [tiny] (37 lines, ~187 tokens) — Deployment apps/v1 prod/api-server
    apiVersion: apps/v1  L1
    kind: Deployment  L2
    metadata:  L3-7
        name: api-server  L4
        namespace: prod  L5
        labels:  L6-7
            app: api  L7
    spec:  L8-36
        replicas: 3  L9
        selector:  L10-12
            matchLabels:  L11-12
                app: api  L12
        template:  L13-36
            metadata:  L14-16
                labels:  L15-16
                    app: api  L16
            spec:  L17-36
                containers: (2 items)  L18-36
                    - api  L19-34
                        name: api  L19
                        image: registry.example.com/api:v1.2.3  L20
                        ports: (1 item)  L21-22
                            - 8080  L22
                                containerPort: 8080  L22
    ```

=== ":material-language-markdown: Markdown"

    *Hierarchical TOC — heading levels indented, code blocks tagged by language.*

    ```markdown title="$ ast-outline README.md"
    # README.md [tiny] (52 lines, ~153 tokens, 8 headings, 5 code blocks)
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

=== ":material-database: SQL"

    *Order-processing module — schema + audit trigger + two views + business-logic functions. The source is 95 lines; outline is 50 (1.9× by lines, 1.5× by chars). Tables keep all columns verbatim; PL/pgSQL function bodies, view `SELECT`s, and trigger timing/event details get stripped — agents see the schema shape and the function contracts, not the implementation.*

    ```sql title="$ ast-outline orders.sql --imports"
    # orders.sql [medium] (96 lines, ~674 tokens, 6 types, 3 methods, 18 fields)
    # imports: CREATE EXTENSION IF NOT EXISTS pgcrypto
    CREATE TYPE order_status AS ENUM  L4
        'pending'  L4
        'paid'  L4
        'shipped'  L4
        'cancelled'  L4

    CREATE TABLE customers  L6-11
        id BIGINT PRIMARY KEY  L7
        email TEXT NOT NULL UNIQUE  L8
        display_name TEXT  L9
        created_at TIMESTAMPTZ NOT NULL DEFAULT now()  L10

    CREATE TABLE orders  L13-20
        id BIGINT PRIMARY KEY  L14
        customer_id BIGINT NOT NULL REFERENCES customers(id)  L15
        total_cents INTEGER NOT NULL  L16
        status order_status NOT NULL DEFAULT 'pending'  L17
        placed_at TIMESTAMPTZ NOT NULL DEFAULT now()  L18
        updated_at TIMESTAMPTZ  L19

    -- Audit log captures every status transition for compliance.
    CREATE TABLE order_audit  L23-30
        id BIGSERIAL PRIMARY KEY  L24
        order_id BIGINT NOT NULL  L25
        old_status order_status  L26
        new_status order_status NOT NULL  L27
        actor TEXT  L28
        changed_at TIMESTAMPTZ NOT NULL DEFAULT now()  L29

    -- Computes total cents the customer has paid across all paid orders.
    CREATE FUNCTION customer_lifetime_value(cid BIGINT) RETURNS BIGINT  L33-47

    -- Audit hook: records every status change and stamps updated_at.
    CREATE FUNCTION audit_order_status() RETURNS TRIGGER  L50-59

    CREATE TRIGGER orders_audit ON orders  L61-64

    -- Active orders for the dashboard — joined with customer email,
    -- excluding cancellations and anything older than 90 days.
    CREATE VIEW active_orders  L68-81

    -- Daily revenue rollup, refreshed nightly by a cron job.
    CREATE MATERIALIZED VIEW daily_revenue  L84-92

    CREATE INDEX idx_orders_status ON orders(status, placed_at DESC)  L94

    CREATE INDEX idx_orders_customer ON orders(customer_id)  L95
    ```

    `show customers` returns the full `CREATE TABLE` block, `show customers.email` returns the single column line, `show customer_lifetime_value` returns the function header, `show active_orders` returns the view's `SELECT` — bodies are one `show` away when actually needed. PostgreSQL is the primary target — every modern construct works including `CREATE PROCEDURE`, `CREATE DOMAIN`, `CREATE TABLE … PARTITION OF`, `SECURITY DEFINER` functions, `LOAD`, and `IMPORT FOREIGN SCHEMA` (a regex fallback recovers what the upstream grammar errors on, gated by AST skip-ranges so red herrings inside comments and PL/pgSQL bodies don't surface). MySQL and SQLite schemas extract tables / columns / indexes / views cleanly with some `error_count > 0` noise on dialect-specifics like `ENGINE=InnoDB` and `AUTOINCREMENT`.

</div>

Same digest format, same legend, same `[broken]` recovery semantics across
every language. Adding another is a single new adapter file —
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

- :material-format-list-checks: **One tool, every major language**

    C#, C++ (incl. Unreal Engine), Python, TypeScript, JavaScript,
    Java, Kotlin, Scala, Go, Rust, PHP, Ruby (incl. Rails), CSS, SCSS,
    SQL (PostgreSQL primary), Markdown, YAML — same digest format,
    same legend.

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
the embedding-MCP crowd). AI coding agents are sharp enough to chain
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

Concretely, these all fold into **one** agent round-trip — one `bash`
tool call, one stdout result:

```bash
# Filter the digest for one class across a whole tree
ast-outline digest src/ | rg 'class .*Service'

# Outline only files changed since a baseline
find src -name '*.py' -newer .last-review | xargs ast-outline --imports

# List deprecated symbols across a module
ast-outline digest src/ | grep '\[deprecated\]'

# Drive one ast-outline call from the output of another tool
ast-grep -l --pattern 'class $X extends Component' src \
  | xargs ast-outline --imports
```

Through MCP the same outcomes are **N+1 round-trips**: the agent
calls `list_files`, then `outline_file` per match, then filters
results itself — each step paid for in context tokens and latency.
A pipe in `bash` does that work in the kernel, invisibly.

The narrow case where MCP would help is non-shell hosts (e.g. Claude
Desktop). If demand shows up there, a wrapper takes a day to write —
but it's a separate add-on, not a redesign.

---

## Supported languages

| Language   | Extensions |
| ---        | --- |
| C#         | `.cs` |
| C++        | `.cpp`, `.cc`, `.cxx`, `.c++`, `.h`, `.hpp`, `.hh`, `.hxx`, `.h++`, `.ipp`, `.tpp`, `.inl`, `.cppm`, `.ixx` *(Unreal Engine reflection macros recognised)* |
| Python     | `.py`, `.pyi` |
| TypeScript | `.ts`, `.tsx` |
| JavaScript | `.js`, `.jsx`, `.mjs`, `.cjs` *(via the TS grammar)* |
| Java       | `.java` |
| Kotlin     | `.kt`, `.kts` |
| Scala      | `.scala`, `.sc` |
| Go         | `.go` |
| Rust       | `.rs` |
| PHP        | `.php`, `.phtml`, `.phps`, `.php8` |
| Ruby       | `.rb`, `.rake`, `.gemspec`, `.ru`, `Rakefile`, `Gemfile` *(Rails associations recognised)* |
| CSS        | `.css` |
| SCSS       | `.scss` *(mixins, functions, variables, placeholders; `&` resolves against parent)* |
| SQL        | `.sql` *(tables w/ columns, views, types, enums, functions, procedures, triggers, indexes, sequences, schemas, domains; PostgreSQL primary, MySQL/SQLite usable)* |
| Markdown   | `.md`, `.markdown`, `.mdx`, `.mdown` |
| YAML       | `.yaml`, `.yml` |

Adding another language is a single new adapter file. See
[`src/ast_outline/adapters/`](https://github.com/ast-outline/ast-outline/tree/main/src/ast_outline/adapters).

---

## Read next

<div class="grid cards" markdown>

-   :material-console: **[Commands](commands.md)**

    Outline, digest, show, prompt — full CLI reference with examples.

-   :material-robot: **[AI agents](agents.md)**

    How to wire `ast-outline` into Claude Code, Cursor, Aider, custom
    agents.

-   :material-format-list-bulleted: **[Output format](output-format.md)**

    Digest legend, marker tags, size labels, broken-tag handling.

-   :material-source-branch: **[GitHub](https://github.com/ast-outline/ast-outline)**

    Source, issues, releases, contribution guide.

</div>
