---
description: >-
  ast-outline lets AI coding agents pull exactly the code context they need —
  a repo skeleton, a file outline, or one symbol — instead of reading whole
  files. A stateless, tree-sitter-based CLI.
hide:
  - navigation
  - toc
---

<div class="ast-hero" markdown>

<h1 class="ast-hero__title"><span class="ast-hero__bracket">[</span>ast-outline<span class="ast-hero__bracket">]</span></h1>

<p class="ast-hero__tagline">
Your <strong>AI coding agent</strong> reads whole files to understand
code. <code>ast-outline</code> lets it pull <strong>exactly what it
needs</strong> — a whole-repo skeleton, a file's structure, or a single
symbol's body — instead of reading files in full. Runs come out
<strong>cheaper and faster</strong>, and don't drown in large codebases.
</p>

<p class="ast-hero__toolkit">
One stateless CLI: map a repo · outline a file · show a symbol ·
structural-grep for usages · trace imports.
</p>

<div class="ast-hero__cta" markdown>

[:octicons-rocket-16: Install](#install){ .md-button .md-button--primary }
[:material-robot: Install with AI](#install-with-ai){ .md-button .md-button--primary }
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

    *Order-processing pipeline — branded types, abstract base class, async pipeline + compensation. Outline collapses 183 lines / ~1.5k tokens of source down to a single screen.*

    ```typescript title="$ ast-outline src/services/order_service.ts"
    # src/services/order_service.ts [medium] (183 lines, ~1,549 tokens, 7 types, 15 methods, 22 fields)
    export type OrderId = string & { readonly __brand: "OrderId" }  L11
    export type SkuId = string & { readonly __brand: "SkuId" }  L12
    export type CustomerId = string & { readonly __brand: "CustomerId" }  L13

    export const MAX_LINE_ITEMS = 100  L15
    export const DEFAULT_CURRENCY: Currency = "USD"  L16
    export const RESERVATION_TTL_SEC = 600  L17

    export interface LineItem  L19-23
        sku: SkuId  L20
        quantity: number  L21
        unitPrice: Money  L22

    export interface Order  L25-31
        id: OrderId  L26
        customer: CustomerId  L27
        items: LineItem[]  L28
        shippingAddress: Address  L29
        placedAt: Date  L30

    export interface Address  L33-40
        line1: string  L34
        line2?: string  L35
        city: string  L36
        region: string  L37
        postalCode: string  L38
        country: string  L39

    export type OrderState = | { kind: "pending" } | { kind: "validated"; total: Money } | { kind: "reserved"; total: Money; reservationId: string } | { kind: "paid"; total: Money; reservationId: string; chargeId: string } | { kind: "shipped"; total: Money; trackingNumber: string } | { kind: "failed"; reason: string }  L42-48

    export class OrderValidationError extends Error  L50-55
        constructor(public readonly order: Order, message: string)  L51-54

    export class StockReservationError extends Error  L57-62
        constructor(public readonly sku: SkuId, public readonly requested: number)  L58-61

    abstract class StagedProcessor<TIn, TOut>  L64-72
        constructor(protected readonly log: Logger)  L65
        protected logEntry(stage: string, info: Record<string, unknown>): void  L69-71

    export class OrderService extends StagedProcessor<Order, OrderState>  L74-175
        private readonly inflight  L75
        constructor( private readonly payments: PaymentProvider, private readonly stock: StockProvider, private readonly shipments: ShipmentProvider, log: Logger, )  L77-84
        /** Run the full pipeline. Returns the terminal state. */
        async process(order: Order): Promise<OrderState>  L87-103
        /** Cancel an in-flight order. No-op if the order has already settled. */
        cancel(orderId: OrderId): boolean  L106-111
        private validate(order: Order): void  L113-125
        private computeTotal(order: Order): Money  L127-133
        private async reserveStock(order: Order): Promise<{ reservationId: string }>  L135-145
        private async capturePayment(order: Order, total: Money): Promise<{ chargeId: string }>  L147-154
        private async dispatchShipment( order: Order, reservation: { reservationId: string }, ): Promise<{ trackingNumber: string }>  L156-165
        private async compensate(order: Order, err: Error): Promise<void>  L167-174

    export function totalQuantity(order: Order): number  L177-179
    export const isShipped = (s: OrderState): s is Extract<OrderState, { kind: "shipped" }> => s.kind === "shipped"  L181-182
    ```

=== ":material-language-javascript: JavaScript"

    *Node service — payment-router with circuit-breaker, retry budget, fan-out health checks. Parsed by the TypeScript grammar, so React / ES modules / CommonJS all flow through.*

    ```js title="$ ast-outline services/payments/router.js"
    # services/payments/router.js [medium] (150 lines, ~1,335 tokens, 2 types, 12 methods, 4 fields)
    const PROCESSORS = ["stripe", "adyen", "braintree", "worldpay"]  L10
    const TIER_PRIORITY = { enterprise: ["stripe", "adyen"], business: ["adyen", "braintree", "stripe"], consumer: ["stripe", "braintree", "w...  L11-15
    const DEFAULT_RETRY = { attempts: 3, baseDelayMs: 250, maxDelayMs: 5000 }  L17
    const HEALTH_CHECK_INTERVAL_MS = 30_000  L18

    class PaymentError extends Error  L20-28
        constructor(message, { code, retryable = false, processor } = {})  L21-27

    class PaymentRouter extends EventEmitter  L30-139
        constructor({ processors, breakerOpts = {}, logger = new Logger("router") } = {})  L31-40
        /**
         * Attempt to charge `amount` using the best available processor.
         * Returns a settled charge or throws PaymentError after exhausting retries.
         */
        async charge(transaction)  L46-69
        /** Voids a previous successful charge. */
        async refund(charge, amount = charge.amount)  L72-80
        startHealthChecks()  L82-85
        stopHealthChecks()  L87-92
        _validate(transaction)  L94-102
        _candidatesFor(transaction)  L104-108
        async _chargeWithRetry(name, transaction, opts = DEFAULT_RETRY)  L110-125
        async _sweepHealth()  L127-138

    function sleep(ms)  L141-143
    function summarize(charge)  L145-147
    ```

=== ":material-language-rust: Rust"

    *Inventory crate — `struct` / `enum` / `trait` / `impl` with newtypes, lifetimes, generics, `#[derive]` and `///` doc-comments.*

    ```rust title="$ ast-outline src/inventory.rs"
    # src/inventory.rs [medium] (192 lines, ~1,525 tokens, 9 types, 16 methods, 15 fields)
    /// Stable SKU identifier — opaque to consumers.
    #[derive(Debug, Copy, Clone, Eq, PartialEq, Hash)] pub struct Sku(pub u64)  L12
        pub u64  L12

    /// Distinct warehouse keyed by region.
    #[derive(Debug, Copy, Clone, Eq, PartialEq, Hash)] pub struct WarehouseId(pub u32)  L16
        pub u32  L16

    /// Customer account — only the fields the inventory layer cares about.
    #[derive(Debug, Clone, Eq, PartialEq)] pub struct Account  L20-24
        pub id: u64  L21
        pub tier: AccountTier  L22
        pub region: String  L23

    /// Account priority for reservation conflicts.
    #[derive(Debug, Copy, Clone, Eq, PartialEq, Ord, PartialOrd)] pub enum AccountTier  L28-32
        Standard  L29
        Plus  L30
        Enterprise  L31

    /// Result of a reservation attempt against a warehouse.
    #[derive(Debug, Clone)] pub enum ReservationOutcome  L36-45
        Granted { reservation_id: u64, expires_at: Instant }  L38
        Partial { granted: u32, requested: u32, reservation_id: u64 }  L40
        OutOfStock { available: u32, requested: u32 }  L42
        UnknownSku  L44

    /// Anything that can be reserved against (a warehouse, a virtual bucket).
    pub trait Reservable  L48-52
        fn reserve(&mut self, sku: Sku, qty: u32) -> ReservationOutcome  L49
        fn release(&mut self, reservation_id: u64) -> bool  L50
        fn on_hand(&self, sku: Sku) -> u32  L51

    /// Concrete warehouse — a flat per-SKU ledger with a small reservation log.
    #[derive(Debug)] pub struct Warehouse  L56-136
        pub id: WarehouseId  L57
        on_hand: HashMap<Sku, u32>  L58
        reservations: HashMap<u64, ReservationEntry>  L59
        next_id: u64  L60
        ttl: Duration  L61
        pub fn new(id: WarehouseId, ttl: Duration) -> Self  L73-81
        pub fn receive(&mut self, sku: Sku, qty: u32)  L84-86
        pub fn sweep_expired(&mut self, now: Instant) -> u32  L89-93
        fn allocate_id(&mut self) -> u64  L95-99
        fn reserve(&mut self, sku: Sku, qty: u32) -> ReservationOutcome  L103-121
        fn release(&mut self, reservation_id: u64) -> bool  L123-131
        fn on_hand(&self, sku: Sku) -> u32  L133-135

    /// Cross-warehouse router. Picks a warehouse per request based on region
    /// affinity, then falls back to any warehouse with stock.
    pub struct InventoryRouter  L140-180
        warehouses: Vec<Warehouse>  L141
        pub fn new(warehouses: Vec<Warehouse>) -> Self  L145-147
        pub fn warehouse_count(&self) -> usize  L149-151
        pub fn reserve_for(&mut self, account: &Account, sku: Sku, qty: u32) -> ReservationOutcome  L155-173
        fn preferred_index(&self, account: &Account) -> Option<usize>  L175-179

    pub fn total_on_hand<R: Reservable>(stores: &[R], sku: Sku) -> u32  L187-189
    pub const MAX_RESERVATION_QTY: u32 = 10_000  L191
    ```

=== ":material-language-csharp: C#"

    *Unity MonoBehaviour — `[Attribute]` / `[Header]` decorators, properties, events, generics, nested enum + struct, all preserved with their XML doc-comments.*

    ```csharp title="$ ast-outline Assets/Scripts/HeroController.cs"
    # Assets/Scripts/HeroController.cs [medium] (193 lines, ~1,570 tokens, 7 types, 19 methods, 23 fields)
    namespace Demo.Combat
        /// <summary>
        /// Player-controlled hero. Owns movement input, health, status
        /// effects and combat events. Built as a MonoBehaviour so it
        /// can sit on a prefab and read serialized data from the editor.
        /// </summary>
        [RequireComponent(typeof(Rigidbody2D))] [RequireComponent(typeof(Animator))] public class HeroController : MonoBehaviour, IDamageable, IHealable  L14-158
            [Header("Movement")] [SerializeField] private float _moveSpeed = 5f  L18-19
            [SerializeField] private float _jumpForce = 10f  L20
            [SerializeField] private LayerMask _groundLayer  L21
            [Header("Combat")] [SerializeField] private int _maxHealth = 100  L23-24
            [SerializeField] private float _invulnerabilityTime = 0.6f  L25
            [SerializeField] private AudioClip _hurtSound  L26
            [Header("References")] [SerializeField] private Transform _attackOrigin  L28-29
            [SerializeField] private GameObject _hitEffectPrefab  L30
            public int CurrentHealth { get; private set; }  L32
            public bool IsAlive => CurrentHealth > 0  L33
            public bool IsGrounded { get; private set; }  L34
            /// <summary>Fired whenever current health changes. Argument is the new value.</summary>
            public event Action<int> OnHealthChanged  L37
            /// <summary>Fired exactly once when the hero dies.</summary>
            public event Action OnDied  L40
            public UnityEvent<DamageInfo> OnDamageTaken = new UnityEvent<DamageInfo>()  L42
            private Rigidbody2D _body  L44
            private Animator _animator  L45
            private readonly List<IStatusEffect> _activeEffects = new List<IStatusEffect>()  L46
            private float _invulnerableUntil = 0f  L47
            private State _state = State.Idle  L48
            private void Awake()  L50-55
            private void Update()  L57-63
            private void FixedUpdate()  L65-68
            private void HandleMovementInput()  L70-75
            public void Jump()  L77-81
            /// <summary>Apply incoming damage. Respects current invulnerability window.</summary>
            public void TakeDamage(DamageInfo info)  L85-95
            /// <summary>Restore HP up to <see cref="_maxHealth"/>.</summary>
            public void Heal(int amount)  L98-103
            public void ApplyEffect(IStatusEffect effect)  L105-109
            public IEnumerator DashCoroutine(Vector2 direction, float duration)  L111-118
            private int GetResistance(DamageElement element)  L120-125
            private void TickEffects(float deltaTime)  L127-134
            private void UpdateAnimator()  L136-141
            private void Die()  L143-148
            public enum State  L150-157
                Idle, Moving, Attacking, Hurt, Dead

        public interface IDamageable  L160-163
        public interface IHealable  L165-168
        public interface IStatusEffect  L170-176
            bool IsExpired { get; }  L172
            void OnApply(HeroController hero)  L173
            void Tick(HeroController hero, float deltaTime)  L174
            int ResistanceFor(DamageElement element)  L175

        public enum DamageElement  L178
            Physical, Fire, Frost, Lightning, Holy

        public readonly struct DamageInfo  L180-191
            public readonly int Amount  L182
            public readonly DamageElement Element  L183
            public readonly GameObject Source  L184
            public DamageInfo(int amount, DamageElement element, GameObject source)  L185-190
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

    *Spring service — Javadoc, multiple annotations, inheritance, generics, throws clauses, nested static classes for events. The outline collapses 162 lines of source body to a one-screen API surface.*

    ```java title="$ ast-outline src/main/java/com/example/UserService.java"
    # UserService.java [medium] (162 lines, ~1,287 tokens, 4 types, 13 methods, 11 fields)
    namespace com.example.demo.service
        /**
         * Service layer for user accounts.
         *
         * <p>Owns persistence, in-memory caching, and event fan-out. Other
         * services should never reach into the repository directly — go
         * through this service so audit logs stay accurate.
         *
         * <p>Thread-safety: read-mostly cache guarded by a
         * {@link ReentrantReadWriteLock}.
         */
        @Service public class UserService extends BaseService implements UserRepository, AutoCloseable  L25-161
            /** Hard cap on concurrent users. */
            public static final int MAX_USERS = 10_000  L29
            /** Cache TTL applied to every {@link #findById} hit. */
            public static final long CACHE_TTL_SECONDS = 300L  L32
            private final UserDao dao  L34
            private final EventBus events  L35
            private final Map<Long, CachedUser> cache = new HashMap<>()  L36
            private final ReentrantReadWriteLock lock = new ReentrantReadWriteLock()  L37
            private volatile boolean closed = false  L38
            @Autowired public UserService(UserDao dao, EventBus events)  L40-44
            /** Persist a user — new or existing. */
            @Override @Transactional public void save(User user) throws IOException, IllegalArgumentException  L52-66
            /** Look up a user by id, hitting the cache when fresh. */
            public Optional<User> findById(long id)  L69-87
            /** Bulk fetch — preserves request order, missing ids are skipped. */
            public List<User> findAllById(List<Long> ids)  L90-94
            /** Remove a user. Idempotent — returns false if no row was deleted. */
            @Transactional public boolean delete(long id)  L97-108
            private static <T extends Comparable<T>> T findMax(List<T> items)  L110-115
            private void ensureCapacity()  L117-121
            private void validate(User user)  L123-127
            @Override public void close()  L129-133
            /** Cache entry — stamped with the time it was admitted. */
            public static final class CachedUser  L136-148
                public final User user  L137
                public final Instant cachedAt  L138
                public CachedUser(User user, Instant cachedAt)  L140-143
                public boolean isFresh()  L145-147

            public static final class UserSaved  L151-154
                public final long userId  L152
                public UserSaved(long userId)  L153

            public static final class UserDeleted  L157-160
                public final long userId  L158
                public UserDeleted(long userId)  L159
    ```

=== ":material-language-kotlin: Kotlin"

    *Android Compose screen + ViewModel + intent system — `data class`, `sealed class` / `sealed interface` discriminated unions, `suspend`, `companion object`, `@Composable`, top-level extension function.*

    ```kotlin title="$ ast-outline app/src/main/kotlin/ProfileScreen.kt"
    # app/src/main/kotlin/ProfileScreen.kt [medium] (199 lines, ~1,733 tokens, 14 types, 11 methods, 19 fields)
    namespace com.example.app.ui.profile
        /**
         * Public-facing user profile shape — what we render on the screen.
         */
        data class UserProfile( val id: String, val name: String, val handle: String, val avatarUrl: String?, val bio: String, val followers: Int, val following: Int, )  L32-40
            val id: String  L33
            val name: String  L34
            val handle: String  L35
            val avatarUrl: String?  L36
            val bio: String  L37
            val followers: Int  L38
            val following: Int  L39

        /** Discriminated union of the screen's possible states. */
        sealed class ProfileState  L43-48
            object Loading : ProfileState()  L44
            data class Success(val profile: UserProfile, val isCurrentUser: Boolean) : ProfileState()  L45
            data class Error(val message: String, val retryable: Boolean = true) : ProfileState()  L46
            object Empty : ProfileState()  L47

        /** UI-side intents the screen can emit. ViewModel folds them into state. */
        sealed interface ProfileIntent  L51-57
            data class Load(val userId: String) : ProfileIntent  L52
            object Refresh : ProfileIntent  L53
            object Follow : ProfileIntent  L54
            object Unfollow : ProfileIntent  L55
            data class EditBio(val newBio: String) : ProfileIntent  L56

        @HiltViewModel class ProfileViewModel @Inject constructor( private val repository: ProfileRepository, private val authProvider: AuthProvider, ) : ViewModel()  L59-123
            private val repository: ProfileRepository  L61
            private val authProvider: AuthProvider  L62
            private val _state = MutableStateFlow<ProfileState>(ProfileState.Loading)  L65
            val state: StateFlow<ProfileState> = _state.asStateFlow()  L66
            private var currentUserId: String? = null  L68
            fun handle(intent: ProfileIntent)  L70-78
            private fun load(userId: String)  L80-96
            private fun changeFollow(follow: Boolean)  L98-104
            private fun editBio(newBio: String)  L106-112
            /** Suspend helper — preloads followers, used by the parent screen. */
            suspend fun preloadFollowers(): List<UserProfile>  L115-118
            companion object  L120-122
                const val MAX_BIO_LENGTH = 280  L121

        @Composable fun ProfileScreen( userId: String, viewModel: ProfileViewModel = hiltViewModel(), onNavigateToFollowers: (String) -> Unit = {}, )  L125-150
        @Composable private fun LoadingBlock()  L152-158
        @Composable private fun EmptyBlock()  L160-163
        @Composable private fun ErrorBlock(message: String, onRetry: (() -> Unit)?)  L165-173
        @Composable private fun ProfileBlock( profile: UserProfile, isCurrentUser: Boolean, onFollow: () -> Unit, onUnfollow: () -> Unit, onShowFollowers: () -> Unit, )  L175-195
        /** Extension — small convenience for previews and tests. */
        fun UserProfile.shortLabel(): String  L198
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

    *Laravel-style service — PHP 8 readonly fields, ctor property promotion, typed constants, `@deprecated` markers, abstract class + free functions.*

    ```php title="$ ast-outline app/Services/UserService.php"
    # app/Services/UserService.php [medium] (181 lines, ~1,226 tokens, 2 types, 15 methods, 9 fields)
    namespace App\Service
        /**
         * Coordinates user-related use cases.
         *
         * Owns persistence, simple in-memory caching, audit logging and event
         * fan-out. Controllers should never reach into the repository directly
         * — go through this service so audit logs stay accurate.
         */
        final class UserService  L23-163
            private readonly Repository $repository  L33
            private readonly EventDispatcherInterface $events  L34
            private readonly LoggerInterface $logger  L35
            protected int $maxCacheSize = self::MAX_CACHE_SIZE  L36
            public const string DEFAULT_ROLE = 'guest'  L25
            public const int MAX_CACHE_SIZE = 1_000  L26
            public const int CACHE_TTL_SECONDS = 300  L27
            /** @var array<int, array{user: User, expires: int}> */
            private array $cache = []  L30
            public function __construct( private readonly Repository $repository, private readonly EventDispatcherInterface $events, private readonly LoggerInterface $logger, protected int $maxCacheSize = self::MAX_CACHE_SIZE, )  L32-38
            /** Fetch a user by id, hitting the cache when fresh. */
            public function getUser(int $id): User  L45-57
            /** @deprecated Use {@see getUser()} instead. */
            public function loadUser(int $id): ?User  L60-67
            /** Save a user — new or existing. */
            public function save(User $user): User  L73-81
            /** Bulk fetch — preserves request order, missing ids are skipped. */
            public function getMany(array $ids): array  L89-100
            /** Remove a user. Returns false if no row was deleted. */
            public function delete(int $id): bool  L103-112
            /** Build an order on behalf of a user. */
            public function makeOrder(User $user, array $items): OrderModel  L115-119
            /** Promote / demote a user's role. */
            public function changeRole(int $id, UserRole $role): User  L122-127
            public function flushCache(): void  L129-132
            private function validate(User $user): void  L134-139
            private function prime(User $user): void  L141-147
            private function evictOldest(): void  L149-162

        abstract class BaseService  L165-173
            abstract public function name(): string  L167
            final public function tag(): string  L169-172

        function make_user_service(Repository $r, EventDispatcherInterface $e, LoggerInterface $l): UserService  L175-178
        const APP_VERSION = '1.0.0'  L180
    ```

=== ":simple-ruby: Ruby"

    *Rails ActiveRecord-style model — `has_many` / `belongs_to` associations and `attr_accessor` surface as fields, mixins on the type header.*

    ```ruby title="$ ast-outline app/models/user.rb"
    # app/models/user.rb [medium] (158 lines, ~824 tokens, 2 types, 24 methods, 14 fields)
    # == Schema
    #
    # Mirrors the migration in `db/schema.rb`.
    class User < ApplicationRecord  L8-132
        MAX_NAME_LENGTH  L13
        DEFAULT_ROLE  L14
        PASSWORD_MIN_LENGTH  L15
        [accessor] unhashed_password  L18
        [reader] id  L19
        [reader] created_at  L19
        [has_many] posts  L21
        [has_many] comments  L22
        [has_many] follows  L23
        [has_many] following  L24
        [belongs_to] company  L25
        [belongs_to] primary_team  L26
        # Build a User from raw attributes; assigns the default role
        # when none is provided.
        def initialize(attrs = {})  L42-45
        # Display name shown in headers and lists.
        def display_name  L48-50
        # Whether the user is an admin — convenience for views.
        def admin?  L53-55
        def soft_delete!  L57-59
        def restore!  L61-63
        # Returns a hash of the email — used for gravatar-style URLs.
        def email_hash  L66-68
        def follow!(other)  L70-72
        def unfollow!(other)  L74-76
        def following?(other)  L78-80
        # Class-level finder that takes a fuzzy name string.
        [static] def self.find_by_name(query)  L83-85
        [static] def self.bulk_import(rows)  L87-91
        def <=>(other)  L93-96
        def ==(other)  L98-100
        def [](key)  L102-104
        def []=(key, value)  L106-108
        [static] def archived  L111-113
        [static] def with_role(role)  L115-117
        def normalize_email  L122-124
        def send_welcome_email  L126-128
        [alias] to_s → display_name  L130
        [alias] follower_count → followers_count  L131

    # Query object for user-facing search forms.
    class UserQuery  L135-157
        def initialize(scope = User.active)  L136-138
        def by_name(name)  L140-143
        def in_company(company)  L145-148
        def page(num, size: 25)  L150-152
        def to_a  L154-156
    ```

    Also recognised: `class << self` blocks, `alias` / `alias_method`, `private` / `protected` state machine (`private :foo, :bar` flips named methods retroactively), `Rakefile` / `Gemfile` (resolved by basename, no extension needed). The MRO clause `: ApplicationRecord, include Comparable, extend Searchable` shows superclass + mixins as one inheritance line in the digest.

=== ":simple-lua: Lua"

    *Neovim plugin — the classic `local M = {} ... return M` module shape with `M.setup(opts)`, instance methods via the `:` syntax, and `require` imports.*

    ```lua title="$ ast-outline plugin/init.lua"
    # plugin/init.lua [tiny] (52 lines, ~310 tokens, 1 method, 4 fields)
    --- Plugin entry — gets loaded by Neovim on `require("myplugin")`.
    function M.setup(opts)  L14-22
    function M.run()  L24-27
    -- Internal helper, not exposed.
    function _validate(opts)  L30-32
    M.config  L4-7
    M.DEFAULT_KEYMAP  L9
    function State:tick()  L40-44
    function State:reset()  L46-48
    ```

    Notable rules: `function M.foo()` (dot) is `KIND_FUNCTION`; `function M:bar()` (colon) is `KIND_METHOD` — the colon is Lua's source-true marker for implicit `self`. Metamethods (`__add`, `__index`, `__tostring`, …) all classify as `KIND_OPERATOR` regardless of declaration shape, so `--kind operator` isolates every protocol declaration. `local function foo()` is private (`local` IS the language's private scope); names starting with `_` (and not `__name__` metamethods) are also private. Direct-return-table modules (`return { foo = function() end, V = 1 }`) walk the returned fields. `require "x"` / `require("x")` / `local Y = require("x")` register as imports — calls inside function bodies are conditional and bump `[+ N conditional includes]`. Long-bracket comments `--[[ ]]` / `--[==[ ]==]` and long strings `[[ ]]` / `[=[ ]=]` ride in `noise_regions` so grep filters matches inside them.

=== ":simple-godotengine: GDScript"

    *A Godot 4 player script — `class_name` + `extends` header with an `@icon` annotation, signals, an enum, a `preload` const, `@export` / `@onready` vars, a property with a `get`/`set` block, engine callbacks, a static factory and an inner class.*

    ```gdscript title="$ ast-outline player.gd"
    # player.gd [tiny] (74 lines, ~426 tokens, 4 types, 8 methods, 13 fields)
    ## Player avatar — movement, health and inventory.
    @icon("res://icons/player.svg") class_name Player extends CharacterBody2D  L3-5
    signal died(cause)  L7
    enum State  L10
        IDLE  L10
        RUNNING  L10
    const BulletScene = preload("res://weapons/bullet.tscn")  L15
    @export var max_health: int  L17
    @onready var sprite: Sprite2D  L19
    ## Current health. Clamped by the setter.
    var health: int  L25-31
    func _ready() -> void  L36-41
    func _physics_process(delta: float) -> void  L43-45
    func take_damage(amount: int, source: Node = null) -> void  L48-51
    static func from_save(data: Dictionary) -> Player  L56-59
    class Inventory  L65-73
        func add(slot: int, item: Resource) -> void  L69-70
    ```

    Notable rules: the only adapter with a **hand-written parser** (no maintained `tree-sitter-gdscript` wheel exists on PyPI) — declarations are scanned from logical lines with string contents masked, so a `func fake():` inside a string literal can never produce a declaration. `class_name X` + `extends Y` merge into ONE class node (the script's implicit class); a bare `extends Y` becomes a class node named after the base, so symbol search answers "which scripts extend `CharacterBody2D`". `signal` → `KIND_EVENT`; `_init` → ctor; `var` with `get`/`set` blocks, `get = `/`set = ` references, or legacy Godot 3 `setget` → `KIND_PROPERTY`; lambdas are never captured. Engine virtual callbacks (`_ready`, `_process`, …) stay public so they survive digest's private filter; other `_name` members are private by convention. `const X = preload("res://...")` and `extends "res://..."` register as imports — `load(`/`preload(` inside function bodies bump `[+ N conditional includes]`. Godot 3 shapes (`export var` / `onready var` / rpc keywords) are kept in signatures. Strings spanning lines (Godot allows raw newlines in ANY string literal) ride in `noise_regions` so grep filters matches inside them.

=== ":simple-swift: Swift"

    *A typical service file — a `Codable` struct, a raw-valued `enum`, a `protocol`, a `@MainActor` class conforming to it, and an `extension` adding publisher-based API.*

    ```swift title="$ ast-outline Sources/UserService.swift"
    # Sources/UserService.swift [tiny] (69 lines, ~423 tokens, 5 types, 8 methods, 8 fields)
    # imports: import Foundation; import Combine
    /// User data model
    public struct User: Codable, Identifiable  L5-9
        public let id: UUID  L6
        public var name: String  L7
        public var email: String  L8
    /// User service protocol
    public protocol UserServiceProtocol  L19-23
        func getUser(byId id: UUID) async throws -> User  L20
        func saveUser(_ user: User) async throws  L21
        var currentUser: User? { get }  L22
    /// Concrete user service implementation
    @available(iOS 15, *) @MainActor public final class UserService: UserServiceProtocol  L26-50
        @Published public private(set) var currentUser: User?  L29-30
        public init(apiClient: URLSession = .shared)  L35-37
        public func getUser(byId id: UUID) async throws -> User  L39-41
        static func shared() -> UserService  L47-49
    /// Extension for publisher-based API
    extension UserService  L53-57
        func fetchUsers() -> AnyPublisher<[User], Error>  L54-56
    /// Type alias for callback
    public typealias UserCallback = (Result<User, Error>) -> Void  L60
    ```

    Notable rules: `struct` / `enum` / `protocol` / `actor` each map to their own kind; an `extension` surfaces as a type group so its members stay attached to the type they augment. `init` / `deinit` become constructor / destructor, `subscript` an indexer. Default visibility is `internal`; `private` / `fileprivate` are honoured, and protocol members are implicitly public. Attributes (`@MainActor`, `@available`, `@Published`) ride along in the signature, generics and protocol conformance are preserved, and `///` doc comments attach to the following declaration. `import` lines are collected; structural `grep` treats `//` / `///` matches as noise.

=== ":material-language-css3: CSS"

    *Design tokens + components — `:root` token block, themed selectors via `[data-theme=dark]`, `@media` / `@keyframes` / `@layer` / `@font-face`, native nesting with `&`.*

    ```css title="$ ast-outline src/styles/theme.css"
    # src/styles/theme.css [medium] (260 lines, ~1,456 tokens)
    :root  L8-39
    [data-theme="dark"]  L41-48
    [data-theme="high-contrast"]  L50-54
    *, *::before, *::after  L59-63
    html  L65-68
    body  L70-76
    .container  L81-85
    .stack > * + *  L87-89
    .cluster  L91-96
    .grid-12  L98-102
    #main-header  L104-109
    #main-header > .nav .item:hover  L111-113
    .btn-primary, .btn-secondary, .btn-danger  L118-128
    .btn-primary  L130-133
    .btn-primary:hover  L135-137
    .btn-secondary  L139-143
    .btn-danger  L145-148
    .modal .btn-primary[disabled]  L150-153
    input[type="text"], input[type="email"], textarea  L155-164
    input:focus, textarea:focus  L166-169
    .card  L174-179
    .card .card-title  L181-184
    .card .card-body  L186-188
    .card.is-featured  L190-192
    @media (max-width: 768px)  L197-207
        .container  L198-200
        .btn-primary, .btn-secondary, .btn-danger  L201-203
        .grid-12  L204-206
    @media (min-width: 769px) and (max-width: 1024px)  L209-213
        .container  L210-212
    @media (prefers-reduced-motion: reduce)  L215-220
        *  L216-219
    @keyframes fadeIn  L225-228
    @keyframes slideUp  L230-233
    @layer base  L235-243
        h1, h2, h3  L236-239
        code, pre  L240-242
    @font-face  L245-250
    :is(.alert, .warning, .error)  L252-255
    :not(.disabled):hover  L257-259
    ```

    `find_symbols(".btn-primary")` returns every cascade-relevant rule — top-level group, the `@media` override, and `.modal .btn-primary[disabled]` descendant — with the wrapping at-rule visible in the breadcrumb. Pseudo-classes and attribute filters are stripped for matching, so `.btn-primary:hover` and `.btn-primary[disabled]` both match `.btn-primary`. `:is()` / `:where()` arguments recurse (additive); `:not()` / `:has()` don't.

=== ":material-language-css3: SCSS"

    *Component stylesheet — `&` resolves against the parent, so nested rules become findable as their fully-qualified BEM selectors. Mixins and functions render as callables.*

    ```scss title="$ ast-outline styles/_components.scss"
    # styles/_components.scss [medium] (263 lines, ~1,464 tokens, 5 methods)
    $primary: #007bff !default  L8
    $secondary: #6c757d !default  L9
    $danger: #dc3545 !default  L10
    $success: #28a745 !default  L11
    $radius-sm: 4px !default  L12
    $radius-md: 8px !default  L13
    $radius-lg: 16px !default  L14
    $transition-fast: 0.15s ease !default  L15
    $breakpoint-sm: 480px  L17
    $breakpoint-md: 768px  L18
    $breakpoint-lg: 1024px  L19
    %button-base  L24-33
    %card-base  L35-40
    @mixin button($bg: $primary, $fg: white, $size: medium)  L45-71
    @mixin elevated($level: 1)  L73-81
    @mixin respond-to($name)  L83-91
    @function strip-unit($value)  L93-95
    @function fluid($min, $max, $min-vw: 320, $max-vw: 1200)  L97-105
    .btn  L110-112
    .btn-primary  L114-116
    .btn-secondary  L118-126
        .btn-secondary.disabled  L122-125
    .btn-danger  L128-130
    .btn-success  L132-134
    .card  L139-156
        h2  L147-150
        .card__actions  L152-155
    .card__body  L158-161
    .card__footer  L163-167
    &.card--featured  L169-180
        .card__header  L173-179
    &.card--compact  L182-188
        .card__body  L185-187
    .form-field  L194-229
        label  L199-202
        input, textarea, select  L204-213
            input:focus  L209-212
        .form-field__hint  L215-218
        .form-field.is-invalid  L220-228
            input, textarea, select  L221-223
            .form-field__hint  L225-227
    @media print  L254-262
        .btn, .btn-primary, .btn-secondary, .btn-danger, .btn-success  L255-257
        .card  L258-261
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
    # README.md [medium] (185 lines, ~1,091 tokens, 22 headings, 7 code blocks)
    # Project Apollo  L1-184
        ## Status  L7-15
        ## Quickstart  L16-54
            bash code block  L18-22
            bash code block  L26-28
            ### Prerequisites  L32-38
            ### Running tests  L39-54
                bash code block  L41-45
                python code block  L47-51
        ## Architecture  L55-118
            ### Services  L61-92
                #### Orders  L63-72
                    text code block  L67-71
                #### Payments  L73-82
                    go code block  L78-81
                #### Warehouse  L83-87
                #### Shipments  L88-92
            ### Data flow  L93-110
                mermaid code block  L101-109
            ### Storage  L111-118
        ## Configuration  L119-139
            ### Secrets  L132-139
        ## Deployment  L140-162
            ### Release process  L145-152
            ### Observability  L153-162
        ## Contributing  L163-181
            ### Coding conventions  L169-174
            ### Reporting bugs  L175-181
        ## License  L182-184
    ```

=== ":material-language-html5: HTML"

    *A typical landing page — `<head>` zone with metadata and asset imports, semantic `<body>` structure (header / main / sections / footer), a FAQ with collapsed `<details>`, and a signup form. Elements render as CSS-selector tokens so the agent uses one selector grammar across HTML, CSS, and SCSS — `show landing.html '#hero'` reads exactly like `show theme.css '#hero'`.*

    ```html title="$ ast-outline landing.html"
    # landing.html [tiny] (62 lines, ~487 tokens, 33 elements)
    html[lang=en]
        head
            meta[name=description content=Pre-reading layer for AI coding agents]
            title
            [import] link[rel=stylesheet href=/css/main.css]
            [import] link[rel=icon href=/favicon.svg type=image/svg+xml]
            [import] script[src=/js/analytics.js defer]
        body
            header.site-nav
                a.logo[href=/]
                nav
                    a[href=#features]
                    a[href=#pricing]
            main
                section#hero
                    h1: Pull exactly the context you need
                    a.cta[href=#install]
                section#features
                    h2: Why teams switch
                    h3: Faster reads
                    h3: Sharper answers
                    h3: No daemon
                section#faq
                    h2: Frequently asked
                    details ×6
            footer.site-footer
            form#newsletter[action=/subscribe method=post]
                input[name=email type=email required]
                button[type=submit]
    ```

    Notable rules: every element renders as a CSS-selector token (`tag`, `tag#id`, `tag.cls1.cls2`, `tag[attr=val …]`) — the same shape `show` expects, so the outline line and the lookup command share a vocabulary. Headings `<h1>`–`<h6>` carry a 60-char text preview. Bare `<div>` / `<span>` / `<p>` / `<li>` / `<tr>` (no id, no class, no significant attribute) are dropped from the outline but their meaningful descendants float up to the parent's depth — real-world templates have 5-10 wrapping containers per visible block, listing each one inflates the outline to no signal. `<svg>` / `<math>` render the root only (inline icon paths aren't CSS-addressable). Three or more consecutive bare `<details>` siblings collapse to one `details ×N` line so FAQ pages don't dominate the outline with identical leaves. `<link rel=stylesheet|preload|prefetch|modulepreload|icon|manifest>` and `<script src=…>` are surfaced three ways: the signature gets an `[import]` prefix, they appear in the `--imports` listing, and grep classifies matches inside their byte range as `[import]` automatically. Inline `<script>` / `<style>` bodies and `<!-- comments -->` ride in `noise_regions` so grep filters them by default. Templated HTML (Jinja `{% if %}`, Vue / Svelte raw templates, PHP `<?php`) gets a partial outline via ERROR-node recovery instead of a blank one.

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

- :material-database-arrow-down: **2–10× fewer tokens**

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
    Java, Kotlin, Scala, Go, Rust, PHP, Ruby (incl. Rails), Lua,
    GDScript (Godot 4 + 3), Swift, CSS, SCSS, SQL (PostgreSQL
    primary), Markdown, YAML — same digest format, same legend.

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
  <td>"Where is <code>User.save</code> called from?" (with scope)</td>
  <td class="meh">flat hits</td>
  <td class="no">no</td>
  <td class="meh">need a query</td>
  <td class="yes">yes (grep)</td>
</tr>
<tr>
  <td>Find a structural pattern (<code>if $X &amp;&amp; $Y</code>)</td>
  <td class="meh">regex</td>
  <td class="no">no</td>
  <td class="yes">yes</td>
  <td class="no">no</td>
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

Naming inspired by [`ast-grep`](https://github.com/ast-grep/ast-grep)
— both tools build on tree-sitter but answer different questions.
`ast-grep` rewrites code with structural patterns (`if $X && $Y`);
`ast-outline` maps and searches code at the symbol level for human
or agent reading. They pipe together cleanly when needed.

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

## Install with AI { #install-with-ai }

With `ast-outline` on your machine, **let your coding agent wire it
into your repo's persistent-context file** — one paste, no manual
markdown editing:

!!! success ":material-robot-happy-outline: One-line setup &mdash; paste into your agent"

    Works in Claude Code, Codex CLI, Gemini CLI, or Cursor.

    ```text { .ast-cta }
    Run `ast-outline setup-prompt` and follow its instructions.
    ```

    *Re-run after every `ast-outline` upgrade — the bundled snippet
    is refreshed in place, your local edits are preserved.*

The agent reads a one-shot install-time checklist from stdout and
walks you through:

1. **Verify the CLI** — runs `ast-outline --version`. If missing,
   offers `uv tool install` (recommended), `pipx install`, or
   `pip install` and can install on your behalf with explicit
   consent. Best-effort PyPI version check; surfaces the matching
   upgrade command without auto-upgrading.
2. **Pick the right file and write the snippet** — `./AGENTS.md` is
   the cross-tool default (read by Codex CLI, Claude Code via
   `@AGENTS.md` import, Gemini CLI with `settings.json` config, and
   Cursor); single-vendor users get the native file (`./CLAUDE.md`,
   `./GEMINI.md`); a project-local vs global scope choice
   (`~/.claude/CLAUDE.md`, `~/.codex/AGENTS.md`,
   `~/.gemini/GEMINI.md`) is offered. The snippet lives inside
   `<!-- ast-outline:start -->` / `<!-- ast-outline:end -->` markers
   so re-runs upgrade in place. Diff-aware: if the existing block
   differs from the fresh canonical, the agent shows the diff and
   asks before overwriting — your manual edits are never lost
   silently.
3. **Optional subagent patches** — finds exploration-oriented
   subagent files in `.claude/agents/` / `.codex/agents/` /
   `.gemini/agents/` and inserts a small `## Tooling — ast-outline`
   block, with per-agent permission.

Cross-vendor universal — same checklist works in Claude Code, Codex
CLI, Gemini CLI, and Cursor. Cross-OS — the agent translates
`which`, `$VIRTUAL_ENV`, `curl` examples to PowerShell or `cmd.exe`
on Windows. Adapts to whichever human language the surrounding
conversation is using.

For the manual equivalent (no agent involvement), see
[`ast-outline prompt`](commands.md#ast-outline-prompt-manual-install-path).

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
| Lua        | `.lua`, `.wlua` *(vanilla 5.1–5.4; `function M:foo` → method, metamethods → operator; covers Neovim configs, LÖVE games, OpenResty / Nginx, Redis scripts)* |
| GDScript   | `.gd` *(Godot 4 + Godot 3; hand-written parser — no tree-sitter wheel exists; `class_name`/`extends` merged into the script's implicit class, signals → events, properties with `get`/`set`/`setget`, `preload` → imports, engine callbacks stay public)* |
| Swift      | `.swift` *(structs, enums, protocols, extensions, actors; generics & protocol conformance)* |
| CSS        | `.css` |
| SCSS       | `.scss` *(mixins, functions, variables, placeholders; `&` resolves against parent)* |
| SQL        | `.sql` *(tables w/ columns, views, types, enums, functions, procedures, triggers, indexes, sequences, schemas, domains; PostgreSQL primary, MySQL/SQLite usable)* |
| HTML       | `.html`, `.htm` *(elements rendered as CSS-selector tokens — `section#hero`, `form[action=/x]`; `<link rel=stylesheet>` / `<script src>` collected as imports; `<script>`/`<style>`/`<!-- -->` filtered from grep; ERROR-node recovery for templated HTML — Jinja, Vue, Handlebars get a partial outline instead of blank)* |
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
