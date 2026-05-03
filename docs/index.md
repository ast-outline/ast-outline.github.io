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

=== ":simple-javascript: JavaScript"

    <pre class="ast-console"><span class="ast-prompt">$</span> ast-outline server.mjs
    <span class="ast-comment"># server.mjs (164 lines, Node.js / Express)</span>
    <span class="ast-keyword">import</span> express <span class="ast-keyword">from</span> <span class="ast-keyword">'express'</span>                              <span class="ast-line">L1-1</span>
    <span class="ast-keyword">import</span> { authMiddleware } <span class="ast-keyword">from</span> <span class="ast-keyword">'./auth.mjs'</span>             <span class="ast-line">L2-2</span>
    <span class="ast-keyword">const</span> <span class="ast-method">app</span> = express()                                    <span class="ast-line">L8-8</span>
    <span class="ast-keyword">function</span> <span class="ast-method">attachMiddleware</span>(app)                            <span class="ast-line">L14-32</span>
    <span class="ast-keyword">async function</span> <span class="ast-method">getUsers</span>(req, res) <span class="ast-marker">[async]</span>            <span class="ast-line">L38-58</span>
    <span class="ast-keyword">async function</span> <span class="ast-method">createUser</span>(req, res) <span class="ast-marker">[async]</span>          <span class="ast-line">L62-92</span>
    <span class="ast-keyword">async function</span> <span class="ast-method">deleteUser</span>(req, res) <span class="ast-marker">[async]</span>          <span class="ast-line">L96-118</span>
    <span class="ast-keyword">function</span> <span class="ast-method">errorHandler</span>(err, req, res, next)               <span class="ast-line">L124-138</span>
    app.<span class="ast-method">listen</span>(<span class="ast-marker">3000</span>, () =&gt; ...)                          <span class="ast-line">L160-162</span></pre>

=== ":simple-csharp: C# (Unity)"

    <pre class="ast-console"><span class="ast-prompt">$</span> ast-outline Assets/Scripts/Player.cs
    <span class="ast-comment"># Assets/Scripts/Player.cs (382 lines)</span>
    [<span class="ast-type">RequireComponent</span>(<span class="ast-keyword">typeof</span>(<span class="ast-type">Rigidbody</span>))]
    <span class="ast-keyword">public class</span> <span class="ast-type">Player</span> : <span class="ast-type">MonoBehaviour</span>, <span class="ast-type">IDamageable</span>             <span class="ast-line">L18-298</span>
      [<span class="ast-type">SerializeField</span>] <span class="ast-keyword">private float</span> <span class="ast-method">MaxHealth</span>                <span class="ast-line">L22-22</span>
      [<span class="ast-type">SerializeField</span>] <span class="ast-keyword">private float</span> <span class="ast-method">Speed</span>                    <span class="ast-line">L23-23</span>
      <span class="ast-keyword">public int</span> <span class="ast-method">Health</span> <span class="ast-marker">[property]</span>                          <span class="ast-line">L28-32</span>
      <span class="ast-keyword">private void</span> <span class="ast-method">Awake</span>()                                    <span class="ast-line">L40-52</span>
      <span class="ast-keyword">private void</span> <span class="ast-method">Update</span>()                                   <span class="ast-line">L56-78</span>
      <span class="ast-keyword">private void</span> <span class="ast-method">FixedUpdate</span>()                              <span class="ast-line">L82-98</span>
      <span class="ast-keyword">public void</span> <span class="ast-method">TakeDamage</span>(<span class="ast-keyword">float</span> amount)                   <span class="ast-line">L104-130</span>
      <span class="ast-keyword">public</span> <span class="ast-type">IEnumerator</span> <span class="ast-method">Heal</span>(<span class="ast-keyword">float</span> amount, <span class="ast-keyword">float</span> dur)        <span class="ast-line">L134-162</span>
      <span class="ast-keyword">private void</span> <span class="ast-method">OnTriggerEnter</span>(<span class="ast-type">Collider</span> other)            <span class="ast-line">L168-198</span>
      <span class="ast-keyword">private void</span> <span class="ast-method">Die</span>()                                      <span class="ast-line">L202-220</span>
    <span class="ast-keyword">public class</span> <span class="ast-type">HealthChangedEvent</span> : <span class="ast-type">UnityEvent</span>&lt;<span class="ast-type">float</span>&gt;       <span class="ast-line">L302-310</span></pre>

=== ":simple-openjdk: Java (Spring)"

    <pre class="ast-console"><span class="ast-prompt">$</span> ast-outline src/main/java/com/example/UserController.java
    <span class="ast-comment"># UserController.java (218 lines)</span>
    @<span class="ast-type">RestController</span>
    @<span class="ast-type">RequestMapping</span>(<span class="ast-keyword">"/api/users"</span>)
    <span class="ast-keyword">public class</span> <span class="ast-type">UserController</span>                                <span class="ast-line">L24-198</span>
      @<span class="ast-type">Autowired</span> <span class="ast-keyword">private</span> <span class="ast-type">UserService</span> <span class="ast-method">userService</span>             <span class="ast-line">L28-28</span>
      @<span class="ast-type">Autowired</span> <span class="ast-keyword">private</span> <span class="ast-type">UserMapper</span> <span class="ast-method">userMapper</span>               <span class="ast-line">L29-29</span>
      @<span class="ast-type">GetMapping</span>
      <span class="ast-keyword">public</span> <span class="ast-type">ResponseEntity</span>&lt;<span class="ast-type">List</span>&lt;<span class="ast-type">UserDto</span>&gt;&gt; <span class="ast-method">findAll</span>()              <span class="ast-line">L36-48</span>
      @<span class="ast-type">GetMapping</span>(<span class="ast-keyword">"/{id}"</span>)
      <span class="ast-keyword">public</span> <span class="ast-type">ResponseEntity</span>&lt;<span class="ast-type">UserDto</span>&gt; <span class="ast-method">findById</span>(<span class="ast-type">Long</span> id)        <span class="ast-line">L52-72</span>
      @<span class="ast-type">PostMapping</span>
      <span class="ast-keyword">public</span> <span class="ast-type">ResponseEntity</span>&lt;<span class="ast-type">UserDto</span>&gt; <span class="ast-method">create</span>(<span class="ast-type">UserDto</span> dto)       <span class="ast-line">L76-98</span>
      @<span class="ast-type">PutMapping</span>(<span class="ast-keyword">"/{id}"</span>)
      <span class="ast-keyword">public</span> <span class="ast-type">ResponseEntity</span>&lt;<span class="ast-type">UserDto</span>&gt; <span class="ast-method">update</span>(<span class="ast-type">Long</span> id, ...)       <span class="ast-line">L102-130</span>
      @<span class="ast-type">DeleteMapping</span>(<span class="ast-keyword">"/{id}"</span>)
      <span class="ast-keyword">public</span> <span class="ast-type">ResponseEntity</span>&lt;<span class="ast-type">Void</span>&gt; <span class="ast-method">delete</span>(<span class="ast-type">Long</span> id)              <span class="ast-line">L134-148</span></pre>

=== ":simple-kotlin: Kotlin (Android)"

    <pre class="ast-console"><span class="ast-prompt">$</span> ast-outline app/src/main/java/MainActivity.kt
    <span class="ast-comment"># MainActivity.kt (276 lines)</span>
    <span class="ast-keyword">class</span> <span class="ast-type">MainActivity</span> : <span class="ast-type">AppCompatActivity</span>()                  <span class="ast-line">L20-198</span>
      <span class="ast-keyword">private lateinit var</span> <span class="ast-method">binding</span> : <span class="ast-type">ActivityMainBinding</span>      <span class="ast-line">L24-24</span>
      <span class="ast-keyword">private val</span> <span class="ast-method">viewModel</span> : <span class="ast-type">MainViewModel</span> <span class="ast-keyword">by</span> viewModels()    <span class="ast-line">L26-26</span>
      <span class="ast-marker">[override]</span> <span class="ast-keyword">fun</span> <span class="ast-method">onCreate</span>(savedInstanceState: <span class="ast-type">Bundle?</span>)     <span class="ast-line">L30-72</span>
      <span class="ast-marker">[override]</span> <span class="ast-keyword">fun</span> <span class="ast-method">onResume</span>()                              <span class="ast-line">L76-88</span>
      <span class="ast-keyword">private fun</span> <span class="ast-method">setupRecyclerView</span>()                          <span class="ast-line">L106-138</span>
      <span class="ast-keyword">private fun</span> <span class="ast-method">observeState</span>()                               <span class="ast-line">L142-168</span>
      <span class="ast-keyword">private suspend fun</span> <span class="ast-method">loadUsers</span>() <span class="ast-marker">[suspend]</span>                <span class="ast-line">L172-198</span>
    <span class="ast-keyword">class</span> <span class="ast-type">MainViewModel</span> : <span class="ast-type">ViewModel</span>()                          <span class="ast-line">L210-262</span>
      <span class="ast-keyword">val</span> <span class="ast-method">uiState</span> : <span class="ast-type">StateFlow</span>&lt;<span class="ast-type">UiState</span>&gt;                       <span class="ast-line">L214-216</span>
      <span class="ast-keyword">fun</span> <span class="ast-method">refresh</span>()                                          <span class="ast-line">L222-238</span>
    <span class="ast-keyword">sealed class</span> <span class="ast-type">UiState</span>                                       <span class="ast-line">L268-274</span>
      <span class="ast-keyword">object</span> <span class="ast-type">Loading</span> : <span class="ast-type">UiState</span>
      <span class="ast-keyword">data class</span> <span class="ast-type">Success</span>(<span class="ast-keyword">val</span> users : <span class="ast-type">List</span>&lt;<span class="ast-type">User</span>&gt;) : <span class="ast-type">UiState</span>
      <span class="ast-keyword">data class</span> <span class="ast-type">Error</span>(<span class="ast-keyword">val</span> message : <span class="ast-type">String</span>) : <span class="ast-type">UiState</span></pre>

=== ":simple-scala: Scala 3"

    <pre class="ast-console"><span class="ast-prompt">$</span> ast-outline src/main/scala/com/example/Routes.scala
    <span class="ast-comment"># Routes.scala (148 lines)</span>
    <span class="ast-keyword">case class</span> <span class="ast-type">User</span>(id: <span class="ast-type">Long</span>, name: <span class="ast-type">String</span>, email: <span class="ast-type">String</span>) <span class="ast-line">L8-12</span>
    <span class="ast-keyword">enum</span> <span class="ast-type">AuthError</span>                                            <span class="ast-line">L16-22</span>
      <span class="ast-keyword">case</span> <span class="ast-type">NotAuthenticated</span>, <span class="ast-type">Forbidden</span>, <span class="ast-type">TokenExpired</span>
    <span class="ast-keyword">trait</span> <span class="ast-type">UserRepository</span>                                       <span class="ast-line">L26-38</span>
      <span class="ast-keyword">def</span> <span class="ast-method">findById</span>(id: <span class="ast-type">Long</span>): <span class="ast-type">IO</span>[<span class="ast-type">Option</span>[<span class="ast-type">User</span>]]
      <span class="ast-keyword">def</span> <span class="ast-method">create</span>(user: <span class="ast-type">User</span>): <span class="ast-type">IO</span>[<span class="ast-type">User</span>]
      <span class="ast-keyword">def</span> <span class="ast-method">delete</span>(id: <span class="ast-type">Long</span>): <span class="ast-type">IO</span>[<span class="ast-type">Unit</span>]
    <span class="ast-keyword">class</span> <span class="ast-type">Routes</span>(repo: <span class="ast-type">UserRepository</span>) : <span class="ast-type">Http4sDsl</span>[<span class="ast-type">IO</span>]      <span class="ast-line">L42-128</span>
      <span class="ast-keyword">val</span> <span class="ast-method">routes</span> : <span class="ast-type">HttpRoutes</span>[<span class="ast-type">IO</span>]                            <span class="ast-line">L48-122</span>
      <span class="ast-keyword">given</span> <span class="ast-method">userEncoder</span> : <span class="ast-type">EntityEncoder</span>[<span class="ast-type">IO</span>, <span class="ast-type">User</span>]            <span class="ast-line">L126-126</span>
    <span class="ast-keyword">extension</span> (req: <span class="ast-type">Request</span>[<span class="ast-type">IO</span>])                              <span class="ast-line">L132-146</span>
      <span class="ast-keyword">def</span> <span class="ast-method">authenticated</span> : <span class="ast-type">EitherT</span>[<span class="ast-type">IO</span>, <span class="ast-type">AuthError</span>, <span class="ast-type">User</span>]</pre>

=== ":simple-go: Go"

    <pre class="ast-console"><span class="ast-prompt">$</span> ast-outline internal/http/handlers.go
    <span class="ast-comment"># handlers.go (264 lines)</span>
    <span class="ast-keyword">type</span> <span class="ast-type">Handler</span> <span class="ast-keyword">struct</span>                                       <span class="ast-line">L16-28</span>
      db        *<span class="ast-type">sql.DB</span>
      logger    *<span class="ast-type">slog.Logger</span>
      cache     <span class="ast-type">Cache</span>
    <span class="ast-keyword">func</span> <span class="ast-method">NewHandler</span>(db *<span class="ast-type">sql.DB</span>, log *<span class="ast-type">slog.Logger</span>) *<span class="ast-type">Handler</span>     <span class="ast-line">L32-40</span>
    <span class="ast-keyword">func</span> (h *<span class="ast-type">Handler</span>) <span class="ast-method">Routes</span>() <span class="ast-type">http.Handler</span>                   <span class="ast-line">L44-78</span>
    <span class="ast-keyword">func</span> (h *<span class="ast-type">Handler</span>) <span class="ast-method">listUsers</span>(w <span class="ast-type">http.ResponseWriter</span>, r ...) <span class="ast-line">L82-118</span>
    <span class="ast-keyword">func</span> (h *<span class="ast-type">Handler</span>) <span class="ast-method">getUser</span>(w, r)                          <span class="ast-line">L122-148</span>
    <span class="ast-keyword">func</span> (h *<span class="ast-type">Handler</span>) <span class="ast-method">createUser</span>(w, r)                       <span class="ast-line">L152-198</span>
    <span class="ast-keyword">func</span> (h *<span class="ast-type">Handler</span>) <span class="ast-method">updateUser</span>(w, r)                       <span class="ast-line">L202-240</span>
    <span class="ast-keyword">func</span> (h *<span class="ast-type">Handler</span>) <span class="ast-method">deleteUser</span>(w, r)                       <span class="ast-line">L244-260</span>
    <span class="ast-keyword">type</span> <span class="ast-type">Cache</span> <span class="ast-keyword">interface</span>                                     <span class="ast-line">L268-274</span>
      <span class="ast-method">Get</span>(key <span class="ast-keyword">string</span>) (<span class="ast-keyword">any</span>, <span class="ast-keyword">bool</span>)
      <span class="ast-method">Set</span>(key <span class="ast-keyword">string</span>, value <span class="ast-keyword">any</span>) <span class="ast-keyword">error</span></pre>

=== ":simple-kubernetes: YAML (k8s)"

    <pre class="ast-console"><span class="ast-prompt">$</span> ast-outline deploy/k8s/web.yaml
    <span class="ast-comment"># web.yaml (84 lines, kubernetes)</span>
    apiVersion: <span class="ast-type">apps/v1</span>                                       <span class="ast-line">L1-1</span>
    kind: <span class="ast-type">Deployment</span>                                            <span class="ast-line">L2-2</span>
    metadata.name: <span class="ast-method">web-frontend</span>                                 <span class="ast-line">L4-4</span>
    metadata.labels.app: <span class="ast-method">web</span>                                    <span class="ast-line">L6-6</span>
    spec.replicas: <span class="ast-marker">3</span>                                            <span class="ast-line">L9-9</span>
    spec.selector.matchLabels.app: <span class="ast-method">web</span>                          <span class="ast-line">L11-12</span>
    spec.template.metadata.labels.app: <span class="ast-method">web</span>                      <span class="ast-line">L14-15</span>
    spec.template.spec.containers[0].name: <span class="ast-method">web</span>                  <span class="ast-line">L18-18</span>
    spec.template.spec.containers[0].image: <span class="ast-method">ghcr.io/me/web:1.4</span>  <span class="ast-line">L19-19</span>
    spec.template.spec.containers[0].ports[0].containerPort: <span class="ast-marker">8080</span> <span class="ast-line">L21-21</span>
    spec.template.spec.containers[0].env[0].name: <span class="ast-keyword">DATABASE_URL</span>    <span class="ast-line">L24-24</span>
    spec.template.spec.containers[0].resources.limits.memory: <span class="ast-method">512Mi</span> <span class="ast-line">L29-29</span>
    spec.template.spec.containers[0].livenessProbe.httpGet.path: <span class="ast-method">/healthz</span> <span class="ast-line">L34-34</span></pre>

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
