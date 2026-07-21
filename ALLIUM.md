# Allium Compiler — Prose ↔ Allium Spec

A prompt-powered compiler for [Allium](https://github.com/juxt/allium), JUXT's
behavioral specification language. Paste the prompt below into your AI tooling
as a system prompt. Then use seven commands: **distill**, **elicit**,
**decompile**, **check**, **tend**, **weed**, and **propagate**.

Allium captures behavioral intent — entities, rules, preconditions, and
outcomes. This compiler extracts Allium specs from prose descriptions,
code explanations, or user stories. The output is valid `.allium` syntax
that the [`allium` CLI](https://github.com/juxt/allium-tools) (v3.5+) can
validate with `allium check` and `allium analyse`.

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
                    :check     {:target :checking}
                    :tend      {:target :tending}
                    :weed      {:target :weeding}
                    :propagate {:target :propagating}}}
  :distilling {:entry {:action "prose → Allium v3 spec. Begin with '-- allium: 3'. No module declaration — the file is the module. Extract behavioral entities, rules, enums, surfaces, and config from the description. For each behavior: identify the trigger (when:), preconditions (requires:), and outcomes (ensures:). Name entities as nouns, rules as VerbNoun. Choose triggers deliberately: becomes fires on creation or transition, transitions_to fires on transitions only. Every external-stimulus trigger must be provided by a surface (provides:) or emitted by a rule — otherwise it is unreachable. Use discard binding _ for unused bindings. For entities with lifecycle status fields: add transitions blocks declaring valid edges and terminal states. For fields present only in certain lifecycle states: use 'when status = value' — orthogonal to '?': 'Type? when status = v' exists in those states but may be null within them. Use typed config parameters (name: Type = value); omit the default to make a parameter mandatory. Use free-standing function syntax for domain-specific collection operations (not dot-methods) with explicit lambdas. Use backtick-quoted enum literals for external standard values. Use Set<T> for unordered collections, List<T> when order matters. Include @guidance blocks for implementation hints. Output valid Allium v3 syntax only. No prose wrapping."}}
  :eliciting  {:entry {:action "conversational → Allium v3 spec. Begin with '-- allium: 3'. Ask clarifying questions about ambiguous behaviors, missing preconditions, unstated edge cases, and conflicting rules. Also ask about lifecycle transitions (which states can reach which?), field presence dependencies (which fields only exist in certain states?), who triggers each action (which surfaces provide which triggers?), and collection ordering requirements. After each answer, update the spec using v3 constructs: transition graphs for lifecycle fields, when clauses for state-dependent fields, surfaces with provides: for every external trigger, typed config, free-standing function syntax for collection operations. Surface contradictions the user hasn't noticed. Record unresolved ambiguity as open question declarations rather than guessing. Continue until the user says done. Output valid Allium v3 syntax after each round."}}
  :decompiling {:entry {:action "Allium → prose. Translate every entity, variant, value type, rule, enum, config, given binding, actor, surface, contract, transition graph, when clause, invariant, and annotation into natural language. Preserve ALL semantics — every when/requires/ensures must appear. Describe transition graphs as lifecycle flows. Describe when-qualified fields as state-dependent presence. Describe precondition failure by trigger type: external stimulus → action rejected with error; temporal/derived → rule silently does not fire; chained → chain stops, prior effects stand. Target audience from request. Output clear prose only. No Allium syntax."}}
  :checking   {:entry {:action "Allium spec → issues list. Structural: version header (-- allium: 3; no module keyword — file is the module), entity fields referenced but never defined, rules with ensures but no when, entities with no rules, unused use imports, missing or stale traces, contradictory or overlapping requires on same-trigger rules, implicit behaviors not captured. Triggers: rules sharing a trigger must agree on parameter count and types (optional T? params bind null when omitted), unreachable triggers (no surface provides them, no rule emits them), transitions_to on values entities can be created with (suggest becomes), temporal triggers on optional fields (never fire while null). Lifecycle: transition graph violations (ensures producing edges not in graph, non-terminal states without outbound edges, declared edges not witnessed by any rule, enum values missing from graph), when-qualified fields require a transitions block, when-clause obligations (entering the when set without setting the field, leaving without clearing it, accessing without a requires guard narrowing to a qualifying state), explicit when on derived values must match the inferred intersection of input when-sets. Types: inline enum fields are anonymous and not comparable with each other (extract a named enum), variant violations (capitalised discriminator values without variant declarations, direct instantiation of the base, variant-field access without a type guard), .first/.last on Set (warning, will become error), custom dot-methods on collections (must be free-standing calls), implicit lambdas (any(x) — must be any(i => i.x)), set arithmetic on ordered collections yields unordered (error where ordering expected). Invariants: purity (no side effects, no now, must be boolean), quantifying over when-qualified fields without a state guard (use status in {...} implies ...). Contracts/surfaces: demands without matching fulfils, contract references that don't resolve or repeat within a surface, contract bodies containing anything but typed signatures and @invariant/@guidance, zero-arg operations without explicit (), timeout not naming a temporal rule or with a mismatched condition. Config: cyclic references, Duration scaled by non-Integer. Annotations: @invariant/@guarantee need unique PascalCase names, @guidance must be unnamed and last, every annotation needs at least one indented comment line. Warnings: open question declarations, deferred without a location hint. Output a numbered list of issues with suggested fixes."}}
  :tending    {:entry {:action "edit/refactor Allium v3 specs. Read the existing spec first and respect its domain model — new behavior fits the structure, not fights it. Translate implementation language into behavioral terms ('returns 404' → 'user is informed it was not found'; 'cron job' → 'happens on a schedule'). Challenge vagueness: ask what happens at boundaries, on failure, and under concurrency instead of inventing behavior; record unresolved items as open question declarations. Be minimal — add only what was asked; no speculative fields, rules, or config. Process-aware editing: a new requires: must be established by some existing rule or surface (else flag the gap); a new guard on a transition-witnessing rule must not make the declared transition unreachable (else flag it). Spot library-spec candidates (OAuth, payments, email) and ask whether they belong in a standalone spec referenced via use. Output the full updated spec in valid Allium v3 syntax."}}
  :weeding    {:entry {:action "spec ↔ code divergence. Modes: check (default — report only), update-spec (make the spec match the code), update-code (make the code match the spec). For each entity/rule/trigger in the spec, find its implementation; for each significant code path, check the spec accounts for it. Process-level checks: every declared transition has a producing code path; every external-stimulus trigger has an entry point (endpoint, handler, consumer); no code state-changes outside the declared transition graph; every expression-bearing invariant is enforced somewhere (constraint, application check, or test); reconstruct entity lifecycles bottom-up from the code and diff against the transitions blocks, presenting the reconstruction for validation. Classify each divergence with reasoning — spec bug (fix spec) | code bug (fix code) | undocumented behavior | unimplemented spec — and let the caller confirm. Output a numbered divergence list with locations in both spec and code."}}
  :propagating {:entry {:action "Allium spec → tests. If the allium CLI is available, use it as ground truth: allium plan <spec> lists every test obligation, allium model <spec> gives entity shapes, constraints, and state machines; otherwise derive obligations from the spec directly. Surface mode (boundary tests): exposes accessibility per actor, provides availability (visible when conditions hold, hidden otherwise), actor restriction and identified_by predicates (including within scoping), context scoping (surface absent when nothing matches), contract demands/fulfils signatures, @guarantee assertions, timeout rules firing, related navigation resolving. Spec mode (full coverage): fields (? null handling, when-clause presence at state boundaries), enum comparability, variants and type guards, derived values and projections, default instances, config (mandatory params, overrides, expression defaults), invariants verified after every rule, rules (success/failure/edge cases; if-guards read resulting state, assignment RHS reads pre-rule state), transitions (valid, invalid, terminal; becomes vs transitions_to), temporal (deadline boundaries, fire-once, null fields never fire). Generate tests in the project's conventions, named after rule names, with traces back to the spec."}}}
 :data {:allium-syntax-reference
  "-- allium: 3                              -- file ≡ module; no module keyword
   use \"<coordinate>\" as <alias>            -- immutable coordinate (git SHA / content hash)
   given { <binding>: <EntityType> }         -- singleton instances shared by all rules
   entity <Name> {
     field: Type
     field: Type?                            -- genuinely optional
     field: Type when status = value1 | value2  -- lifecycle-dependent presence
     field: Type? when status = value1       -- ? and when are orthogonal; both allowed
     status: value1 | value2 | value3        -- lowercase = inline enum (not comparable across fields)
     kind: VariantA | VariantB               -- Capitalised = sum type discriminator
     review: pending | approved              -- multiple independent status fields allowed
     transitions status {
       value1 -> value2
       value2 -> value3
       terminal: value3
     }
     transitions review { ... }              -- one block per status field
     relationship: Entity with field = this  -- with must reference this; singular name = at most one
     items: Entity with owner = this         -- plural name = collection
     projection: relationship where condition   -- where must not reference this
     members: memberships -> user            -- field extraction; nulls dropped from T?
     derived_value: expression
     derived_value: expression when status = value  -- inferred: intersection of inputs' when-sets
     can_use(f): f in plan.features          -- parameterised derived value; own fields + param only
     invariant <Name> { <expression> }
   }
   external entity <Name> { field: Type }    -- zero fields = dependency-inversion placeholder
   value <Name> { field: Type }              -- no identity, immutable, compared by value
   variant <Name> : <BaseEntity> { field: Type }  -- create via variant name, never the base
   enum <Name> { value1 | value2 | `hyphenated-value` }
   rule <VerbNoun> {
     when: <Trigger>(param, optional?)       -- external stimulus; omitted optional binds null
     when: binding: Entity.field becomes <value>        -- fires on creation OR transition
     when: binding: Entity.field transitions_to <value> -- transitions only, never creation
     when: binding: Entity.created           -- fires for base or any variant
     when: binding: Entity.timestamp_field <= now       -- temporal, fire-once; null never fires
     when: binding: Entity.bool_derived      -- fires when derived value becomes true
     when: _: Entity.field <= now            -- _ discards an unneeded binding
     for item in Collection [where condition]:  -- rule-level: body fires once per element
     let binding = expression
     requires: <precondition>
     requires: a implies b
     ensures: <outcome>                      -- assignment RHS reads pre-rule state
     if <condition>: <conditional-outcome>   -- if-guards read resulting state
     traces: <impl-reference>
     @guidance
       -- non-normative advice (indented comment lines)
   }
   invariant <Name> { for x in Collection: <expression> }
     -- pure: no side effects, no now, boolean result
     -- when-qualified fields need a state guard: status in {...} implies ...
   contract <Name> {
     <operation>: (param: Type) -> ReturnType
     <operation>: () -> ReturnType           -- zero-arg needs explicit (); bare -> invalid
     @invariant <Name>
       -- prose assertion (indented comment lines)
   }
   surface <Name> {
     facing <role>: <ActorType>              -- use _ if the binding is unused
     context <binding>: <EntityType> [where predicate]
     let binding = expression                -- where here must not reference this
     exposes: field [when condition]
     provides: Action(params) [when condition]  -- unprovided external triggers are unreachable
     contracts: demands <Contract> | fulfils <Contract>
     related: OtherSurface(navigation) [when condition]
     timeout: RuleName [when temporal_condition]  -- must name a rule with a temporal trigger
     @guarantee <Name>
       -- prose assertion
     @guidance
       -- must be last annotation, unnamed, ≥1 indented comment line
   }
   config {
     <name>: Type                            -- no default = mandatory for consumers
     <name>: Type = <value>
     <name>: Type = <alias>/config.<param> * <expr>  -- acyclic; Duration scales by Integer only
   }
   default <EntityType> <name> = { field: value }
   deferred <Entity.operation> \"path/to/detail.allium\"  -- location hint; invokable in ensures
   actor <Name> {
     within: <ContextEntity>                 -- optional; resolves to the surface's context
     identified_by: Entity where condition
   }
   open question \"<question>\"
   -- Collection types: Set<T> (unordered) | List<T> (ordered field) | Sequence<T> (inferred, ordered)
   -- Built-in dot-methods ONLY: .count .any() .all() .first .last .unique .add() .remove()
   -- .first/.last require ordered collections; .unique always returns Set
   -- Set arithmetic (+, -) on ordered collections produces unordered results
   -- Domain ops free-standing with explicit lambdas: filter(coll, e => e.recent) — never any(x)
   -- Black box functions: free-standing calls e.g. hash(password), verify(pw, hash)
   -- Literals: { key: value } object | { x } set | backtick `de-CH-1996` for external standards
   -- null: comparisons are false (null <= now → false), arithmetic propagates null
   -- presence tests: field = null (absent) | field != null (present)
   -- now: volatile in derived values | snapshot in ensures | fire-once in temporal | banned in invariants"}}
```

## Seven Commands

### distill — prose → Allium spec

Takes any natural language description of system behavior and extracts
entities, rules, enums, surfaces, and config. The model identifies triggers,
preconditions, and outcomes that the prose left vague or implicit.

This is the equivalent of the upstream `distill` skill but runs on any LLM,
not just Claude Code.

### elicit — conversational spec building

Interactive mode. Describe what your system should do, and the compiler
asks clarifying questions about ambiguities, edge cases, and contradictions.
After each answer, it updates the Allium spec. Say "done" when finished.

This is the equivalent of the upstream `elicit` skill.

### decompile — Allium → prose

Takes Allium specs and produces natural language. Every entity, rule, enum,
and config block appears in the output. Useful for non-technical stakeholders,
onboarding docs, or verifying that the spec captures what you intended.

### check — Allium → issues list

Static analysis of an Allium spec. Finds missing preconditions, unreachable
triggers, undefined entity fields, contradictory requires clauses, transition
graph violations, and implicit behaviors not yet captured. Returns a numbered
issue list with suggested fixes.

The `allium` CLI is ground truth for structural checks — use the compiler's
`check` for the semantic layer the CLI can't see (implicit behaviors,
missing rules, spec smells).

### tend — edit and refactor specs

Maintenance mode, mirroring the upstream `tend` skill. Takes change requests
against existing specs — new entities, rules, or surfaces; refactors;
migrations — and applies them while respecting the existing domain model.
Pushes back on vague requirements instead of inventing behavior, and checks
that edits don't orphan preconditions or make declared transitions
unreachable.

### weed — spec ↔ code drift

Divergence detection, mirroring the upstream `weed` skill. Compares an
Allium spec against implementation code in three modes: **check** (report
only, default), **update-spec** (spec follows code), **update-code** (code
follows spec). Classifies each divergence as spec bug, code bug,
undocumented behavior, or unimplemented spec.

This is the Allium counterpart of nucleus's [drift-vsm](agents/drift-vsm/)
agents — same pattern, different formalism.

### propagate — spec → tests

Test generation, mirroring the upstream `propagate` skill. Derives test
obligations from every spec construct — rules, transitions, invariants,
surfaces, config — and generates tests in your project's conventions.
Pairs with the CLI: `allium plan` enumerates the obligations, `allium model`
supplies entity shapes and state machines.

## The allium CLI

The compiler pairs with the [`allium` CLI](https://github.com/juxt/allium-tools)
(v3.5+, `brew install juxt/allium/allium`), which grew from a validator into
a five-command toolchain. All commands output JSON:

| Command | What it does | Pairs with |
| ------- | ------------ | ---------- |
| `allium check` | Structural diagnostics (line-level errors/warnings) | `check` — CLI is ground truth for structure |
| `allium analyse` | `check` + data flow, reachability, deadlock, conflict, invariant analysis | `check` — process-level findings |
| `allium parse` | AST as JSON | tooling, custom pipelines |
| `allium plan` | Test obligations implied by the spec | `propagate` — the obligation list |
| `allium model` | Domain model: entities, value types, generators | `propagate` — shapes and state machines |

The loop: LLM writes the spec (`distill`/`elicit`/`tend`) → CLI validates
(`check`/`analyse`) → LLM interprets diagnostics and fixes → CLI derives
obligations (`plan`/`model`) → LLM generates tests (`propagate`). Runtime
is truth; the model is the bridge.

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
  facing _: Application

  provides:
    SignUpRequested(email, password)
    EmailVerified(user)
    LoginAttempted(email, password)
    PasswordResetRequested(user)
    PasswordReset(token, new_password)

  contracts:
    fulfils AuthenticationAPI

  @guarantee NoLoginWithoutVerification
    -- User cannot authenticate without a verified email.
  @guarantee LockoutAfterFailures
    -- Account locks after max_login_attempts failed attempts
    -- for lockout_duration.
}

surface AdminFacing {
  facing admin: Administrator

  provides:
    AdminUnlock(user, admin) when user.status = locked

  @guarantee AuditedUnlocks
    -- Every manual unlock is performed by an authenticated admin.
}
```

Five casual sentences → 1 enum, 2 entities, 8 rules, 2 invariants,
1 contract, 2 surfaces, typed config defaults, `implies` guards, `@guidance`
hints, `@guarantee` assertions, a transition graph on `User.status`, and
a `when`-qualified `locked_until` field that is present only when the
account is locked. The transition graph makes the `unverified → active →
locked → active` lifecycle explicit and authoritative — the checker
validates that every rule-produced transition appears in the graph. The
`when` clause on `locked_until` replaces the old `Timestamp?` — it's not
optional, it's lifecycle-dependent: absent when active, required when locked.
Every external trigger is provided by a surface — without `provides:`
coverage, the CLI flags rules as listening for unreachable triggers. The
unused `client` binding uses the `_` discard.

This exact spec passes `allium check` and `allium analyse` (CLI v3.5.0)
with zero diagnostics and zero findings.

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
- **Let the CLI be ground truth** — `allium check` for structure,
  `allium analyse` for process-level findings (data flow, reachability,
  deadlocks). The compiler's `check` covers the semantic layer the CLI
  can't see; the CLI covers the syntax the model might fumble. Use both.
- **Provide every trigger** — rules listening for external triggers that
  no surface `provides:` are flagged as unreachable by `allium check`.
  Surfaces aren't decoration; they close the reachability graph.
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

Our compiler targets Allium v3 as parsed by CLI v3.5. All v1 and v2 specs
are forward-compatible — update the version header, drop the `module`
declaration, and, if you used custom dot-methods on collections, rewrite
them to free-standing function syntax. v3 adds six new capabilities
and one enforcement change on top of v2's foundation.

### Module Keyword Removed

The `module <name>` declaration no longer parses — the v3.5 parser rejects
it as a hard error. A file **is** a module; its identity comes from its
coordinate (the `use` reference by which others import it), not from a
declared name. Files begin with `-- allium: 3` followed directly by
optional `use` declarations.

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

### Fine-Grained Semantics (v3.5 reference)

Later refinements to the v3 reference, all covered by the compiler's syntax
reference and `check` command:

- **`given` blocks** — singleton entity instances at module scope, inherited
  by all rules. Contrast with surface `context`, which is parametric (one
  surface instance per matching entity).
- **Sum types** — capitalised values in a discriminator field
  (`kind: Branch | Leaf`) reference `variant` declarations; lowercase values
  are enum literals. Create via the variant name, never the base. Type
  guards required before accessing variant fields.
- **Discard bindings** — `_` wherever a binding is required but unused:
  `when: _: Entity.field <= now`, `SomeEvent(_, slot)`, `facing _: App`.
- **Trigger semantics** — `becomes` fires on creation *or* transition;
  `transitions_to` fires on transitions only. Boolean derived values can
  be triggers (`when: s: Slot.is_valid` fires on false → true). Optional
  trigger parameters (`details?`) bind `null` when omitted. Rules sharing
  a trigger must agree on parameter count and types.
- **Rule-level `for`** — a `for` clause wrapping `let`/`requires`/`ensures`
  fires the whole body once per element.
- **Parameterised derived values** — `can_use(f): f in plan.features`;
  scoped to the entity's own fields plus the parameter.
- **Projection extraction** — `members: memberships -> user` extracts a
  field per match; nulls are dropped from `T?`, yielding `Set<T>`.
- **`with` vs `where`** — `with` declares relationships and must reference
  `this`; `where` filters and must not. Singular relationship name = at
  most one; plural = collection.
- **Actor `within`** — scopes an actor to a context type; `within` resolves
  to the surface's `context` binding inside `identified_by`.
- **`null` semantics** — comparisons with `null` are false (`null <= now`
  → false), arithmetic propagates `null`. Temporal triggers on optional
  fields never fire while the field is absent.
- **`now` evaluation** — volatile in derived values (re-read each access),
  snapshot in `ensures` (rule execution time), fire-once in temporal
  triggers, prohibited in invariants.
- **`ensures` reading model** — assignment right-hand sides read pre-rule
  state; `if` guards read the resulting state.
- **Precondition failure by trigger type** — external stimulus: rejected
  with error; temporal/derived: silently doesn't fire; chained: chain
  stops, prior effects stand.
- **Literals** — `{ key: value }` is an object literal (anonymous record);
  `{ x }` is a set literal. Syntactically distinct.
- **Mandatory config** — a parameter without a default must be supplied by
  the consuming module.

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
