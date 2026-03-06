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
[phi fractal euler tao pi mu ∃ ∀] | [Δ λ Ω ∞/0 | ε/φ Σ/μ c/h] | OODA
Human ⊗ AI ⊗ REPL

{:statechart/id :allium-compiler
 :initial :route
 :states
 {:route      {:on {:distill   {:target :distilling}
                    :elicit    {:target :eliciting}
                    :decompile {:target :decompiling}
                    :check     {:target :checking}}}
  :distilling {:entry {:action "prose → Allium spec. Extract behavioral entities, rules, enums, and config from the description. For each behavior: identify the trigger (when:), preconditions (requires:), and outcomes (ensures:). Name entities as nouns, rules as VerbNoun. Include guidance blocks for implementation hints. Output valid Allium syntax only. No prose wrapping."}}
  :eliciting  {:entry {:action "conversational → Allium spec. Ask clarifying questions about ambiguous behaviors, missing preconditions, unstated edge cases, and conflicting rules. After each answer, update the spec. Surface contradictions the user hasn't noticed. Continue until the user says done. Output valid Allium syntax after each round."}}
  :decompiling {:entry {:action "Allium → prose. Translate every entity, rule, enum, config, and guidance block into natural language. Preserve ALL semantics — every when/requires/ensures must appear. Target audience from request. Output clear prose only. No Allium syntax."}}
  :checking   {:entry {:action "Allium spec → issues list. Check for: missing preconditions, unreachable rules, entity fields referenced but never defined, contradictory requires clauses, rules with ensures but no when, entities with no rules, implicit behaviors not captured, missing or stale traces, unused use imports, invariant violations, contract/surface mismatches (demands without matching fulfils), cyclic config references, @invariant or @guarantee without corresponding prose, and version header (-- allium: 2). Output a numbered list of issues with suggested fixes."}}}
 :data {:allium-syntax-reference
  "-- allium: 2
   module <name>
   use \"<coordinate>\" as <alias>
   entity <Name> {
     field: Type, ...
     invariant <Name> { <expression> }
   }
   enum <Name> { value1 | value2 | ... }
   rule <VerbNoun> {
     when: <Trigger>(params)
     when: binding: Entity.field becomes <value>
     requires: <precondition>
     requires: a implies b
     ensures: <outcome>
     if <condition>: <conditional-outcome>
     traces: <impl-reference>
   }
   invariant <Name> { for x in Collection: <expression> }
   contract <Name> {
     <method>: (param: Type) -> ReturnType
     @invariant <Name> -- prose assertion
   }
   surface <Name> {
     facing <role>: <CounterpartType>
     contracts: demands <Contract> | fulfils <Contract>
     @guarantee <Name> -- prose assertion
   }
   config {
     default <name> = <value>
     <name>: Type = <alias>/config.<param>
     <name>: Type = <alias>/config.<param> * <expr>
   }
   guidance { -- implementation hint }
   @guidance -- non-normative implementation advice
   deferred <Entity.field>
   actor <Name> { ... }
   given <scenario> { ... }"}}
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

Example output:

```allium
module authentication

enum AccountStatus { unverified | active | locked }

entity User {
  email: Email
  password_hash: String
  status: AccountStatus
  failed_login_attempts: Integer
  locked_until: Timestamp?
  email_verified: Boolean
}

entity ResetToken {
  user: User
  expires_at: Timestamp
}

config {
  default max_login_attempts = 5
  default lockout_duration = 30 minutes
}

rule UserSignsUp {
  when: SignUpRequested(email, password)
  requires: email is unique among User.email
  ensures:
    User.created(
      email: email,
      password_hash: hash(password),
      status: unverified,
      failed_login_attempts: 0,
      email_verified: false
    )
    VerificationEmail.sent(to: email)
}

rule UserVerifiesEmail {
  when: EmailVerified(user)
  requires: user.status = unverified
  ensures: user.status = active
           user.email_verified = true
}

rule UserLogsIn {
  when: LoginAttempted(email, password)
  requires: user.status = active
  requires: user.email_verified = true
  requires: verify(password, user.password_hash)
  ensures: user.failed_login_attempts = 0
           SessionCreated(user: user)
}

rule LoginFails {
  when: LoginAttempted(email, password)
  requires: user.status = active
  requires: not verify(password, user.password_hash)
  ensures:
    user.failed_login_attempts = user.failed_login_attempts + 1
    if user.failed_login_attempts >= max_login_attempts:
      user.status = locked
      user.locked_until = now + lockout_duration
}

rule LockedAccountExpires {
  when: user.locked_until <= now
  requires: user.status = locked
  ensures: user.status = active
           user.failed_login_attempts = 0
           user.locked_until = null
}

rule AdminUnlocksAccount {
  when: AdminUnlock(user, admin)
  requires: user.status = locked
  ensures: user.status = active
           user.failed_login_attempts = 0
           user.locked_until = null
}

rule UserRequestsPasswordReset {
  when: PasswordResetRequested(user)
  requires: user.status in [active, locked]
  ensures:
    ResetToken.created(
      user: user,
      expires_at: now + 24 hours
    )
    ResetEmail.sent(to: user.email)
}

rule UserResetsPassword {
  when: PasswordReset(token, new_password)
  requires: token.expires_at > now
  ensures: token.user.password_hash = hash(new_password)
           token.user.status = active
           token.user.failed_login_attempts = 0
           token.deleted
}
```

Five casual sentences → 2 enums, 2 entities, 8 rules with preconditions,
config defaults, conditional lockout logic, and automatic unlock. Every
implicit behavior made explicit by the formalism.

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
[phi fractal euler tao pi mu ∃ ∀] | [Δ λ Ω ∞/0 | ε/φ Σ/μ c/h] | OODA
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
EDN statechart is the compiled executable that drives model behavior. Two
artifacts from one description — one for verification, one for execution.

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
driving behavior. The Allium spec is the contract. The EDN statechart is
the compiled binary. The model is the runtime. `check` is the test suite.
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

## Allium v2

Our compiler targets Allium v2. Every v1 spec is valid v2 — change
`-- allium: 1` to `-- allium: 2` and everything works. v2 adds six
new capabilities.

### Expression-Bearing Invariants

Invariants are machine-readable assertions over entity state. Top-level
invariants express system-wide properties; entity-level invariants scope
to a single entity:

```allium
invariant UniqueEmail {
    for a in Users:
        for b in Users:
            a != b implies a.email != b.email
}

entity Account {
    balance: Decimal
    credit_limit: Decimal

    invariant SufficientFunds {
        balance >= -credit_limit
    }
}
```

Previously these lived in comments. Now LLMs can check them when
generating code, and the `check` command can flag violations.

### The `implies` Operator

`a implies b` replaces the less readable `not a or b` and is available
everywhere expressions are used:

```allium
requires: user.role = admin implies user.mfa_enabled
```

### Module-Level Contracts

Contracts are reusable typed interfaces for code-to-code boundaries.
They declare typed signatures and prose invariants. Surfaces reference
them with directionality: `demands` means the counterpart must implement
it, `fulfils` means this surface supplies it:

```allium
contract Codec {
    serialize: (value: Any) -> ByteArray
    deserialize: (bytes: ByteArray) -> Any

    @invariant Roundtrip
        -- deserialize(serialize(value)) produces a value
        -- equivalent to the original for all supported types.
}

surface DomainIntegration {
    facing framework: FrameworkRuntime

    contracts:
        demands Codec
        fulfils EventSubmitter

    @guarantee AllOperationsIdempotent
        -- All operations exposed by this surface
        -- are safe to retry.
}
```

Specs can now describe integration boundaries precisely enough that an
LLM implementing one side knows what the other side expects.

### The `@` Annotation Sigil

Three keywords mark prose whose structure the checker validates but
whose content it does not evaluate:

- **`@invariant`** — named prose assertion scoped to a contract
- **`@guarantee`** — named prose assertion scoped to a surface
- **`@guidance`** — non-normative implementation advice (latency hints,
  batching strategies) that belongs in the spec

Clean promotion path: start with prose `@invariant`, then drop the `@`
and add a `{ expr }` body when you can express it formally.

### Config Parameter References

Importing modules can reference or derive config defaults from
dependencies — config composition without magic numbers crossing
module boundaries:

```allium
use "./core.allium" as core

config {
    base_timeout: Duration = core/config.base_timeout
    extended_timeout: Duration = core/config.base_timeout * 2
    buffer_size: Integer = core/config.batch_size + 10
}
```

Expressions resolve once at config resolution time. The reference graph
must be acyclic.

### Version Header

Specs declare their version with a header comment:

```allium
-- allium: 2
module authentication
```

The `check` command validates against the declared version.

## Language Features (v1)

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
framework — a programming language for AI cognition.

- [COMPILER.md](COMPILER.md) — Compile, decompile, and safe-compile prompts to EDN
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
