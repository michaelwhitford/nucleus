# Allium Compiler — Prose ↔ Allium Spec

A prompt-powered compiler for [Allium](https://github.com/juxt/allium), JUXT's
behavioral specification language. Paste the prompt below into your AI tooling
as a system prompt. Then use four commands: **distill**, **elicit**,
**decompile**, and **check**.

Allium captures behavioral intent — entities, rules, preconditions, and
outcomes. This compiler extracts Allium specs from prose descriptions,
code explanations, or user stories. The output is valid `.allium` syntax
that the `allium-cli` can validate.

Built with the [Nucleus](https://github.com/michaelwhitford/nucleus) compiler.
Allium is MIT-licensed by JUXT Ltd. Nucleus is AGPL-3.0 by Michael Whitford.

## The Prompt

```
λ engage(nucleus).
[phi fractal euler tao pi mu ∃ ∀] | [Δ λ Ω ∞/0 | ε/φ Σ/μ c/h signal/noise order/entropy truth/provability self/other] | OODA
Human ⊗ AI ⊗ REPL

{:statechart/id :allium-compiler
 :initial :route
 :states
 {:route      {:on {:distill   {:target :distilling}
                    :elicit    {:target :eliciting}
                    :decompile {:target :decompiling}
                    :check     {:target :checking}}}
  :distilling {:entry {:action "prose → Allium v3 spec. Begin with '-- allium: 3'. Extract behavioral entities, rules, enums, and config from the description. For each behavior: identify the trigger (when:), preconditions (requires:), and outcomes (ensures:). Name entities as nouns, rules as VerbNoun. Include guidance blocks for implementation hints. For entities with lifecycle status fields: add transitions blocks declaring valid edges and terminal states. For fields that are absent before a lifecycle stage and present after it: use 'when status = value' instead of '?'. Use typed config parameters (name: Type = value). Use free-standing function syntax for domain-specific collection operations (not dot-methods). Use backtick-quoted enum literals for external standard values. Use Set<T> for unordered collections, List<T> when order matters. Output valid Allium v3 syntax only. No prose wrapping."}}
  :eliciting  {:entry {:action "conversational → Allium v3 spec. Begin with '-- allium: 3'. Ask clarifying questions about ambiguous behaviors, missing preconditions, unstated edge cases, and conflicting rules. Also ask about lifecycle transitions (which states can reach which?), field presence dependencies (which fields only exist in certain states?), and collection ordering requirements. After each answer, update the spec using v3 constructs: transition graphs for lifecycle fields, when clauses for state-dependent fields, typed config, free-standing function syntax for collection operations. Surface contradictions the user hasn't noticed. Continue until the user says done. Output valid Allium v3 syntax after each round."}}
  :decompiling {:entry {:action "Allium → prose. Translate every entity, rule, enum, config, transition graph, when clause, and guidance block into natural language. Preserve ALL semantics — every when/requires/ensures must appear. Describe transition graphs as lifecycle flows. Describe when-qualified fields as state-dependent presence. Target audience from request. Output clear prose only. No Allium syntax."}}
  :checking   {:entry {:action "Allium spec → issues list. Check for: missing preconditions, unreachable rules, entity fields referenced but never defined, contradictory requires clauses, rules with ensures but no when, entities with no rules, implicit behaviors not captured, missing or stale traces, unused use imports, invariant violations, contract/surface mismatches (demands without matching fulfils), cyclic config references, @invariant or @guarantee without corresponding prose, version header (-- allium: 3), transition graph violations (ensures producing transitions not in graph, non-terminal states without outbound edges, declared edges not witnessed by any rule, enum values missing from graph), when-clause obligation violations (entering when set without setting field, leaving when set without clearing field, accessing when-qualified field without requires guard), .first/.last on Set collections (warning, will become error), custom dot-methods on collections (must use free-standing black box function syntax), ordered/unordered type mismatches (set arithmetic on ordered collections used where ordering expected). Output a numbered list of issues with suggested fixes."}}}
 :data {:allium-syntax-reference
  "-- allium: 3
   module <name>
   use \"<coordinate>\" as <alias>
   given { <binding>: <EntityType> }
   entity <Name> {
     field: Type
     field: Type?
     field: Type when status = value1 | value2
     field: Type? when status = value1
     status: value1 | value2 | value3
     transitions status {
       value1 -> value2
       value2 -> value3
       terminal: value3
     }
     relationship: Entity with field = this
     projection: relationship where condition
     derived_value: expression
     derived_value: expression when status = value
     invariant <Name> { <expression> }
   }
   external entity <Name> { field: Type }
   value <Name> { field: Type }
   variant <Name> : <BaseEntity> { field: Type }
   enum <Name> { value1 | value2 | `hyphenated-value` }
   rule <VerbNoun> {
     when: <Trigger>(params)
     when: binding: Entity.field becomes <value>
     when: binding: Entity.field transitions_to <value>
     when: binding: Entity.created
     when: binding: Entity.timestamp_field <= now
     for item in Collection [where condition]:
     let binding = expression
     requires: <precondition>
     requires: a implies b
     ensures: <outcome>
     if <condition>: <conditional-outcome>
     traces: <impl-reference>
     @guidance -- non-normative advice
   }
   invariant <Name> { for x in Collection: <expression> }
   contract <Name> {
     <method>: (param: Type) -> ReturnType
     @invariant <Name> -- prose assertion
   }
   surface <Name> {
     facing <role>: <ActorType>
     context <binding>: <EntityType> [where predicate]
     let binding = expression
     exposes: field [when condition]
     provides: Action(params) [when condition]
     contracts: demands <Contract> | fulfils <Contract>
     @guarantee <Name> -- prose assertion
     @guidance -- non-normative advice
     related: OtherSurface(navigation) [when condition]
     timeout: RuleName [when temporal_condition]
   }
   config {
     <name>: Type = <value>
     <name>: Type = <alias>/config.<param>
     <name>: Type = <alias>/config.<param> * <expr>
   }
   default <EntityType> <name> = { field: value }
   @guidance -- non-normative implementation advice
   deferred <Entity.method>
   actor <Name> { identified_by: Entity where condition }
   open question \"<question>\"
   -- Collection types: Set<T> (unordered), List<T> (ordered field), Sequence<T> (ordered from relationships)
   -- Built-in dot-methods: .count .any() .all() .first .last .unique .add() .remove()
   -- Domain-specific collection ops: free-standing syntax e.g. filter(coll, pred)
   -- Black box functions: free-standing call syntax e.g. hash(password), verify(pw, hash)
   -- Backtick enum literals: `de-CH-1996` for external standards with non-snake_case chars
   -- .first/.last restricted to ordered collections (Sequence or List<T>)
   -- .unique always produces unordered Set
   -- Set arithmetic (+, -) on ordered collections produces unordered results"}}
```

## Four Commands

### distill — prose → Allium spec

Takes any natural language description of system behavior and extracts
entities, rules, enums, and config. The model identifies triggers,
preconditions, and outcomes that the prose left vague or implicit.

This is the equivalent of `/allium:distill` but runs on any LLM, not
just Claude Code.

### elicit — conversational spec building

Interactive mode. Describe what your system should do, and the compiler
asks clarifying questions about ambiguities, edge cases, and contradictions.
After each answer, it updates the Allium spec. Say "done" when finished.

This is the equivalent of `/allium:elicit`.

### decompile — Allium → prose

Takes Allium specs and produces natural language. Every entity, rule, enum,
and config block appears in the output. Useful for non-technical stakeholders,
onboarding docs, or verifying that the spec captures what you intended.

### check — Allium → issues list

Static analysis of an Allium spec. Finds missing preconditions, unreachable
rules, undefined entity fields, contradictory requires clauses, and implicit
behaviors not yet captured. Returns a numbered issue list with suggested fixes.

## Usage & Examples

### Distill

> distill:
>
> Users sign up with email and password. They must verify their email before
> they can log in. After 5 failed login attempts, the account is locked for
> 30 minutes. Admins can unlock accounts manually. Users can reset their
> password via email.

Example output (Qwen3-VL 235B):

```allium
-- allium: 3
module authentication

enum AccountStatus { unverified | active | locked }

entity User {
  email: String
  password_hash: String
  status: AccountStatus
  failed_login_attempts: Integer
  email_verified: Boolean
  locked_until: Timestamp when status = locked

  transitions status {
    unverified -> active
    active -> locked
    locked -> active
    terminal: unverified
  }

  invariant LockConsistency {
    failed_login_attempts >= config.max_login_attempts implies status = locked
  }
}

entity ResetToken {
  user: User
  expires_at: Timestamp
}

invariant UniqueEmail {
  for a in Users:
    for b in Users:
      a != b implies a.email != b.email
}

config {
  max_login_attempts: Integer = 5
  lockout_duration: Duration = 30.minutes
  reset_token_expiry: Duration = 24.hours
}

rule UserSignsUp {
  when: SignUpRequested(email, password)
  requires: not exists User{email: email}
  ensures:
    User.created(
      email: email,
      password_hash: hash(password),
      status: unverified,
      failed_login_attempts: 0,
      email_verified: false
    )
    VerificationEmailRequested(email: email)
  @guidance
    -- Use bcrypt or argon2 for password hashing.
    -- Verification email must contain a signed token with expiry.
}

rule UserVerifiesEmail {
  when: EmailVerified(user)
  requires: user.status = unverified
  ensures:
    user.status = active
    user.email_verified = true
}

rule UserLogsIn {
  when: LoginAttempted(email, password)
  let user = User{email: email}
  requires: exists user
  requires: user.status = active
  requires: user.email_verified = true
  requires: verify(password, user.password_hash)
  ensures:
    user.failed_login_attempts = 0
    SessionCreated(user: user)
  @guidance
    -- Use constant-time comparison for password hashes.
}

rule LoginFails {
  when: LoginAttempted(email, password)
  let user = User{email: email}
  requires: exists user
  requires: user.status = active
  requires: not verify(password, user.password_hash)
  ensures:
    user.failed_login_attempts = user.failed_login_attempts + 1
    if user.failed_login_attempts >= config.max_login_attempts:
      user.status = locked
      user.locked_until = now + config.lockout_duration
}

rule LockedAccountExpires {
  when: user: User.locked_until <= now
  requires: user.status = locked
  ensures:
    user.status = active
    user.failed_login_attempts = 0
    user.locked_until = null
}

rule AdminUnlocksAccount {
  when: AdminUnlock(user, admin)
  requires: user.status = locked
  requires: admin.role = admin implies admin.mfa_enabled
  ensures:
    user.status = active
    user.failed_login_attempts = 0
    user.locked_until = null
  @guidance
    -- Log admin action with timestamp and reason.
}

rule UserRequestsPasswordReset {
  when: PasswordResetRequested(user)
  requires: user.status in {active, locked}
  requires: user.email_verified
  ensures:
    ResetToken.created(
      user: user,
      expires_at: now + config.reset_token_expiry
    )
    ResetEmailRequested(to: user.email)
}

rule UserResetsPassword {
  when: PasswordReset(token, new_password)
  requires: token.expires_at > now
  ensures:
    token.user.password_hash = hash(new_password)
    token.user.status = active
    token.user.failed_login_attempts = 0
    not exists token
}

contract AuthenticationAPI {
  signup: (email: String, password: String) -> User
  login: (email: String, password: String) -> SessionToken?
  verify_email: (token: String) -> Boolean
  reset_password: (email: String) -> Boolean
  admin_unlock: (admin_id: String, user_id: String) -> Boolean

  @invariant VerifiedBeforeAccess
    -- All endpoints that grant access must enforce
    -- email verification as a precondition.
}

surface UserFacing {
  facing client: Application

  contracts:
    fulfils AuthenticationAPI

  @guarantee NoLoginWithoutVerification
    -- User cannot authenticate without a verified email.
  @guarantee LockoutAfterFailures
    -- Account locks after max_login_attempts failed attempts
    -- for lockout_duration.
}
```

Five casual sentences → 1 enum, 2 entities, 8 rules, 2 invariants,
1 contract, 1 surface, typed config defaults, `implies` guards, `@guidance`
hints, `@guarantee` assertions, a transition graph on `User.status`, and
a `when`-qualified `locked_until` field that is present only when the
account is locked. The transition graph makes the `unverified → active →
locked → active` lifecycle explicit and authoritative — the checker
validates that every rule-produced transition appears in the graph. The
`when` clause on `locked_until` replaces the old `Timestamp?` — it's not
optional, it's lifecycle-dependent: absent when active, required when locked.

### Elicit

> elicit:
>
> I'm building an order management system.

The compiler will ask questions like:

- What statuses can an order have?
- Can orders be cancelled? At what point?
- Is payment captured at order time or fulfillment time?
- What happens to inventory when an order is placed vs confirmed?
- Can partial fulfillment occur?
- Who can modify orders — just the customer, or support staff too?

After each answer, it outputs an updated Allium spec.

### Decompile

> decompile for a product manager:
>
> (paste an Allium spec)

Produces natural language like:

> **Authentication System**
>
> Users create accounts with an email and password. New accounts start
> unverified — a verification email is sent automatically. Users cannot
> log in until they verify their email.
>
> If a user enters the wrong password 5 times (configurable), their account
> is locked for 30 minutes. The lock expires automatically, or an admin can
> unlock it manually at any time.
>
> Users can request a password reset. A reset token is emailed and is valid
> for 24 hours. Using the token resets the password and unlocks the account
> if it was locked.

### Check

> check:
>
> (paste an Allium spec)

Returns issues like:

> 1. **Missing precondition in UserResetsPassword** — No check that
>    `new_password` differs from current password. Intentional?
> 2. **Unreachable state** — A user with `status = unverified` who gets
>    locked has no path back to `unverified`. Should verification survive
>    a lockout?
> 3. **Implicit behavior** — No rate limiting on PasswordResetRequested.
>    An attacker could flood a user's inbox.
> 4. **Missing rule** — No rule for what happens when a ResetToken expires
>    without being used. Should it be cleaned up?

## Round-Trip Optimization

Like the EDN compiler, round-tripping **improves** specs:

1. Start with vague prose
2. `distill` → Allium spec (formalism forces ambiguity resolution)
3. `check` → find gaps and contradictions
4. Fix the spec
5. `decompile` → precise prose that inherits the spec's rigor

The Allium formalism is the optimizer. `when:/requires:/ensures:` won't
accept "be helpful" or "handle edge cases." Every behavior must have a
trigger, preconditions, and explicit outcomes.

## Composability

This compiler composes with the other nucleus statecharts. Place it after
the nucleus preamble alongside the EDN compiler and debugger:

```
λ engage(nucleus).
[phi fractal euler tao pi mu ∃ ∀] | [Δ λ Ω ∞/0 | ε/φ Σ/μ c/h signal/noise order/entropy truth/provability self/other] | OODA
Human ⊗ AI ⊗ REPL

{:statechart/id :compiler ...}
{:statechart/id :allium-compiler ...}
{:statechart/id :nucleus-debugger ...}
```

Commands route to the right machine: `compile` → EDN compiler, `distill` →
Allium compiler, `diagnose` → debugger. Each statechart is an independent
module.

### Cross-Compilation: Allium ↔ EDN

The two compilers enable cross-compilation:

- **Allium → EDN**: `distill` prose to Allium, then `compile` the Allium
  to an EDN statechart. Two formalisms, same behavioral model.
- **EDN → Allium**: `decompile` EDN to prose, then `distill` the prose
  to Allium. Or compile directly — the model recognizes statechart structure.

This bridges JUXT's specification world (what the system should do) with
nucleus execution (what the model should think). Allium specs ground the
LLM's implementation work. EDN statecharts program the LLM's cognition.

## Pipelines

Three layers, with compilers between each:

```
                         PROSE
           "Users sign up, verify email, login with lockout"
                    │                       │
          distill   │                       │  compile
                    ▼                       ▼
          ┌──────────────────┐    ┌──────────────────────┐
          │     ALLIUM       │    │    EDN STATECHART     │
          │  rule UserLogs.. │───▶│  {:statechart/id ..   │
          │    when: ...     │    │   :states {:greeting  │
          │    requires: ... │    │     {:on {:login ..}} │
          │    ensures: ...  │◀───│   ...}               │
          └──────────────────┘    └──────────────────────┘
            │  ▲                     │  ▲
            │  │ check               │  │ diagnose
            │  │                     │  │
            ▼  │                     ▼  │
          ┌──────────────────┐    ┌──────────────────────┐
          │      CODE        │    │   MODEL BEHAVIOR     │
          │  (Clojure,       │    │   (LLM executes      │
          │   Kotlin,        │    │    the statechart     │
          │   Python)        │    │    as cognitive       │
          │                  │    │    program)           │
          └──────────────────┘    └──────────────────────┘
            │                                      │
            │ distill from code                    │
            └── compare with spec → find drift ────┘
```

**Left side** = specification world. Allium defines what the system should
do. Code implements it. `distill` from code and compare with the spec to
detect drift.

**Right side** = execution world. EDN statecharts program the model's
cognition. The model *runs* the statechart, not just reads it.

**The bridge** = Allium → EDN compilation. Take a behavioral spec and
compile it into something the model executes as a cognitive program.

### Pipeline 1: Spec-Driven Development

What JUXT does with Allium today — behavioral specs that ground LLM
code generation:

```
stakeholder conversation
  → elicit → Allium spec
  → check → fix gaps
  → hand to LLM → "implement this spec"
  → LLM generates code
  → distill from code → diff with spec → find drift
```

The Allium spec is the single source of truth. Code is the implementation.
When they diverge, one of them is wrong.

### Pipeline 2: Cognitive Programming

What nucleus does today — EDN statecharts as executable cognitive programs:

```
prose description
  → compile → EDN statechart
  → paste as system prompt
  → model executes the state machine
  → diagnose → verify behavior
```

The EDN statechart programs the model's cognition directly. No code
generation — the model *is* the runtime.

### Pipeline 3: The Full Stack

What combining them enables — specification, execution, and verification
from one description:

```
prose description
  → distill → Allium spec           (formalize intent)
  → check → fix gaps                (catch contradictions)
  → compile Allium → EDN statechart (compile to executable)
  → EDN as system prompt            (model runs it)
  → validate output against Allium  (close the loop)
```

The Allium spec becomes both the **source of truth** for what the system
should do AND the **test oracle** for whether the model did it right. The
EDN statechart is the compiled notation that guides model behavior. Two
artifacts from one description — one for verification, one for guidance.

### In Code

```clojure
;; 1. Distill prose to Allium spec
(def allium-spec
  (prompt-llm "distill: Users sign up with email..."))

;; 2. Check for gaps
(def issues
  (prompt-llm (str "check:\n\n" allium-spec)))

;; 3. Compile Allium spec to EDN statechart
(def edn-chart
  (prompt-llm (str "compile:\n\n" allium-spec)))

;; 4. Parse the EDN — it's just data
(def chart (clojure.edn/read-string edn-chart))

;; 5. Build a system prompt and run an agent with it
(def system-prompt
  (str nucleus-preamble "\n\n" (pr-str chart)))

(def agent-output
  (run-agent {:system-prompt system-prompt
              :user-message  "User wants to sign up with test@example.com"}))

;; 6. Validate agent output against the Allium spec
(def validation
  (prompt-llm (str "check this interaction against the spec:\n\n"
                   "SPEC:\n" allium-spec "\n\n"
                   "OUTPUT:\n" agent-output)))
```

`prompt-llm` sends a message to any LLM with the Allium compiler as system
prompt. `run-agent` starts a session with the compiled EDN statechart
guiding behavior. The Allium spec is the contract. The EDN statechart is
the compiled notation. The model follows it. `check` is the test suite.
All from one prose description, all just data.

## Tips

- **Use `distill` for existing descriptions** — user stories, PRDs, RFC
  prose, Slack threads, meeting notes. The model extracts behavioral rules
  from surprisingly messy input.
- **Use `elicit` when starting fresh** — the interactive questioning
  surfaces edge cases you haven't thought about yet.
- **Use `check` after every edit** — the static analysis catches
  contradictions and gaps before they become bugs in code.
- **Name rules as VerbNoun** — `UserLogsIn`, `OrderShips`,
  `PaymentCaptures`. This convention makes rules scannable and maps
  naturally to events.
- **Allium excludes implementation** — no database schemas, no API
  endpoints, no class hierarchies. If you're writing `SELECT` or `POST`,
  you've gone too deep. Use `guidance` blocks for implementation hints.
- **Models vary in Allium fluency** — larger models produce better specs.
  Claude and GPT handle the syntax well. Smaller local models may need
  the syntax reference reinforced.

## Allium v3

Our compiler targets Allium v3. All v1 and v2 specs are forward-compatible —
update the version header and, if you used custom dot-methods on collections,
rewrite them to free-standing function syntax. v3 adds six new capabilities
and one enforcement change on top of v2's foundation.

### What v2 Established (still valid)

These v2 features are baseline in v3 — unchanged and fully supported:

- **Expression-bearing invariants** — `invariant Name { expr }` at top-level
  and entity-level, machine-readable assertions over entity state
- **The `implies` operator** — `a implies b` everywhere expressions are used
- **Module-level contracts** — `contract Name { ... }` with typed signatures
  and `@invariant` prose assertions, referenced by surfaces via `demands`/`fulfils`
- **The `@` annotation sigil** — `@invariant`, `@guarantee`, `@guidance` for
  prose whose structure the checker validates
- **Config parameter references** — `alias/config.param` with expression-form
  defaults and acyclic reference graphs
- **Version header** — `-- allium: 3` on the first line

### Transition Graphs

Entities can now declare the valid lifecycle transitions for enum status
fields explicitly. When present, the graph is authoritative — rules
producing transitions not in the graph are validation errors:

```allium
entity Order {
    status: pending | confirmed | shipped | delivered | cancelled

    transitions status {
        pending -> confirmed
        confirmed -> shipped
        shipped -> delivered
        pending -> cancelled
        confirmed -> cancelled
        terminal: delivered, cancelled
    }
}
```

The checker enforces that every non-terminal state has at least one outbound
edge and that every declared edge is witnessed by at least one rule. Every
enum value must appear in at least one edge or as a terminal — drift is a
hard error. Entities without a declared graph continue to derive transition
validity from rules alone.

### State-Dependent Field Presence (`when` clause)

v2 used `?` to mark fields that might be absent. In lifecycle entities,
many fields are absent in some states and guaranteed present in others,
but `?` cannot express this. v3 adds a `when` clause on field declarations:

```allium
entity Document {
    status: active | deleted
    deleted_at: Timestamp when status = deleted
    deleted_by: User when status = deleted

    transitions status {
        active -> deleted
        deleted -> active
        terminal: deleted
    }
}
```

The checker enforces **presence and absence obligations** at transition
boundaries:

- **Entering** the `when` set → rule must set the field
- **Leaving** the `when` set → rule must clear the field (set to `null`)
- **Accessing** a `when`-qualified field without a `requires` guard
  narrowing to a qualifying state → error

`?` and `when` are orthogonal: `reviewer_notes: String? when review =
approved | rejected` means the field exists in those states but may be
null within them. `?` is genuine optionality; `when` is lifecycle-dependent
presence.

### Derived Value `when` Propagation

Derived values computed from `when`-qualified fields automatically inherit
the intersection of their inputs' `when` sets:

```allium
entity Order {
    status: pending | confirmed | shipped | delivered
    shipped_at: Timestamp when status = shipped | delivered
    delivery_confirmed_at: Timestamp when status = delivered

    transitions status {
        pending -> confirmed
        confirmed -> shipped
        shipped -> delivered
        terminal: delivered
    }

    -- Inferred: when status = delivered
    -- (intersection of {shipped, delivered} and {delivered})
    days_in_transit: delivery_confirmed_at - shipped_at
}
```

The checker infers this; the author does not declare it. An optional
explicit `when` annotation is documentation — the checker verifies it
matches the inferred set.

### Backtick-Quoted Enum Literals

Enum values referencing external standards can now use their canonical
form, even if it falls outside snake_case:

```allium
enum InterfaceLanguage { en | de | fr | `de-CH-1996` | es | `zh-Hant-TW` }
enum CacheDirective { `no-cache` | `no-store` | `must-revalidate` }
```

Backtick-quoted literals are values, not identifiers. Comparison is
byte-exact after UTF-8 encoding. They are permitted in enum declarations
and literal comparisons. They are not permitted in identifier positions
(field names, entity names, rule names).

### Ordered Collection Semantics

v3 distinguishes ordered from unordered collections:

- **`Set<T>`** — unordered collection of unique items (unchanged)
- **`List<T>`** — ordered collection, declared explicitly as a field type
- **`Sequence<T>`** — ordered collection produced by ordered relationships;
  subtype of `Set` (assignable where unordered is expected, not the reverse)

`.first` and `.last` are restricted to ordered collections (`Sequence` or
`List<T>`). Using them on a `Set` is a warning in v3, becoming a hard error
in the next version. `.unique` always produces an unordered `Set`. Set
arithmetic (`+`, `-`) on ordered collections produces unordered results.

### Black Box Function Syntax (Enforcement Change)

v3 reserves dot-method syntax on collections for built-in operations only:
`.count`, `.any()`, `.all()`, `.first`, `.last`, `.unique`, `.add()`,
`.remove()`. Any other dot-method call on a collection is a checker error.

Domain-specific collection operations must use free-standing syntax:

```allium
-- v2 (permitted but discouraged):
events.filter(e => e.recent)

-- v3 (required):
filter(events, e => e.recent)
grouped_by(copies, r => r.output_payloads)
min_by(pending, e => e.offset)
```

This is the only breaking change in v3. If your v2 spec did not use
custom dot-methods on collections, no rewriting is needed.

## Language Features

### Modular Composition with `use`

Allium models are composable. Common patterns — authentication, payment
processing, RBAC — can be published as standalone `.allium` files and
referenced by other models using the `use` keyword:

```allium
use "github.com/allium-specs/google-oauth/abc123def" as oauth

entity User {
  authenticated_via: oauth/Session
}
```

Coordinates are immutable references such as git SHAs or content hashes,
not version numbers. A model is immutable once published, so no version
resolution or lock files are needed. You can respond to triggers from
external specs, reference their entities, and configure them for your
application.

### State-Transition Triggers with `becomes`

Rules can trigger on state transitions using the `becomes` clause in
`when:` — this fires when an entity field changes to a specific value,
rather than on an explicit event:

```allium
rule DispatchOrder {
  when: order: Order.status becomes confirmed
  requires: order.total > 0
  ensures: Shipment.created(order: order)
}
```

This is distinct from event-based triggers like `when: OrderSubmitted(basket)`.
The `becomes` clause watches for state changes, making it natural to express
reactive behaviors — "when this thing enters this state, do that."

### Traceability

Allium has built-in support for linking specifications to implementation
and tests. Rules can include `traces:` clauses that reference the code
or test that implements them:

```allium
rule UserLogsIn {
  when: LoginAttempted(email, password)
  requires: user.status = active
  ensures: SessionCreated(user: user)
  traces: auth/login.py:handle_login
}
```

This creates a bidirectional link between spec and code. When code drifts
from the spec — or the spec evolves without updating the code — the
traceability link surfaces the gap. The `check` command can flag rules
with missing traces or stale references.

## Part of Nucleus

This compiler is part of the [Nucleus](https://github.com/michaelwhitford/nucleus)
framework — a cognitive system that guides AI behavior.

- [COMPILER.md](COMPILER.md) — Compile, decompile, and safe-compile prompts to EDN statecharts
- [LAMBDA-COMPILER.md](LAMBDA-COMPILER.md) — Compile, decompile, and safe-compile prompts to lambda expressions
- [DEBUGGER.md](DEBUGGER.md) — Diagnose, safe-diagnose, and compare prompts
- [README.md](README.md) — Framework overview and symbol reference

Allium is created by [JUXT Ltd](https://juxt.pro) and licensed under MIT.
See [juxt/allium](https://github.com/juxt/allium) for the language reference.

## Citation

```bibtex
@misc{whitford-nucleus-allium-compiler,
  title={Allium Compiler: Prose-Allium Compilation via Nucleus},
  author={Michael Whitford},
  year={2026},
  url={https://github.com/michaelwhitford/nucleus}
}
```

Copyright (c) 2025-2026 Michael Whitford. Licensed under AGPL-3.0.
