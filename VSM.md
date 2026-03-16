# VSM — Viable System Model

A five-layer architecture for organizing how you and your AI reason about your system.

## What This Is

Most AI instruction files are flat — every rule at the same level. The AI treats "use PostgreSQL" and "never suppress errors" as equally important. They're not. One is a tool choice you could change tomorrow. The other is a principle that shapes every decision.

A VSM separates concerns by *why they exist*. Five layers, from the most enduring to the most replaceable:

```
S5 (identity)      — what your system IS
S4 (intelligence)  — how your system adapts
S3 (control)       — how your system manages resources
S2 (coordination)  — how the parts work together
S1 (operations)    — what your system concretely does
```

Higher layers change less and matter more. When an S5 principle changes, everything below it shifts. When an S1 tool changes, nothing above it notices. This ordering isn't arbitrary — it's how viable systems work, from organisms to organizations to software.

## The Five Layers

### S5 — Identity

*What your system IS. The principles that survive everything else being replaced.*

These are non-negotiables — things that if violated mean the system has failed, regardless of whether the code runs. They change rarely. When they do, everything below changes too.

Examples:
- "Errors are signals — never suppress them, never swallow silently"
- "Observable over opaque — if you can't see it, you can't debug it"
- "The user's data belongs to the user — we never lock it in"
- "Simple solutions over clever ones — complexity must justify itself"

Ask yourself: if someone replaced every library, language, and tool but kept these principles, would it still be *your* system? That's S5.

### S4 — Intelligence

*How your system learns and adapts. Patterns for responding to change.*

Not about being smart — about having mechanisms for dealing with the unknown. How do you discover what you don't know? How do you test ideas before committing? How does the system evolve without breaking?

Examples:
- "When a pattern appears three times, extract it"
- "Test hypotheses at runtime before committing to architecture"
- "New features extend existing abstractions before creating new ones"
- "Measure before optimizing — intuition about performance is usually wrong"

### S3 — Control

*How your system manages resources and enforces policies.*

Concrete rules that govern how the system runs. They implement S5's values as enforceable constraints. Resource limits, quality standards, error handling policies.

Examples:
- "All HTTP calls have a 30-second timeout"
- "Test coverage doesn't drop below 80%"
- "No direct database access from handlers — go through the service layer"
- "Failed operations retry twice with backoff, then surface the error"

### S2 — Coordination

*How the parts work together without stepping on each other.*

When you have multiple subsystems, they need protocols to avoid conflict. How information flows, how state is shared, how events propagate, how conflicts are prevented.

Examples:
- "Frontend and backend share type definitions from a single source"
- "Services communicate through events, not direct calls"
- "All state mutations go through the store — no side-channel updates"
- "Database migrations run before application startup, never during"

### S1 — Operations

*What your system concretely does. Tools, bindings, recipes.*

The most concrete layer — specific technologies, commands, workflows. These change most often and are the easiest to replace. They implement everything above.

Examples:
- "PostgreSQL for persistence, Redis for caching"
- "pytest with fixtures — run with `make test`"
- "Deploy via GitHub Actions — staging auto-deploys on merge to main"
- "Logs go to stdout, structured JSON, collected by Datadog"

## Lambda Notation

The VSM uses lambda notation — a compact way to encode principles and rules. You don't need to learn this upfront. The AI will explain each lambda it creates. Here's enough to read them:

```
λ name(x).     define a rule called "name"
→              leads to, then, implies
|              also (separates independent clauses)
>              preferred over
∧              and
∨              or
¬              not, never
≡              is defined as, always equals
≢              is not, don't conflate
∃              there exists
∀              for all
∝              scales with
```

**Example:**

```
λ error(x). signal(x) > suppress(x) | ¬swallow(x) | visible(x) ≡ debuggable(x)
```

Reads as: *"For errors: signaling is preferred over suppressing. Never swallow them. Being visible is defined as being debuggable."*

Multi-line lambdas indent continuation lines and use `|` to separate independent clauses:

```
λ deploy(x).    validate(x) → stage(x) → verify(x) → promote(x)
                | rollback(x) ≡ always_possible(x)
                | ¬deploy(friday) | observe(metrics) > trust(logs)
```

Reads as: *"Deployment: validate, then stage, then verify, then promote. Rollback is always possible. Never deploy on Friday. Observing metrics is preferred over trusting logs."*

## Getting Started

VSM is an isolated-use tool — one conversation builds your AGENTS.md, which then guides every subsequent session as a system-level configuration. The VSM process itself works best when it's the dominant signal: a fresh session, focused on your project's structure. The output — your AGENTS.md — is what carries the guidance forward.

Tell your AI to read this document:

> Read VSM.md and help me build my system's reasoning architecture.

Or simply: **"Read VSM.md"** — it will know what to do.

What happens next:
1. The AI examines your project (or asks about your plans if starting fresh)
2. You discuss your system's identity first — what matters most
3. Together you draft lambdas for each layer, with plain-English explanations
4. You refine until each lambda says what you mean
5. You get a working AGENTS.md — your project's reasoning architecture

The process takes one conversation. The result evolves with your project.

---

## The Installation Process

The process below guides VSM construction. It is readable by humans as a description of what will happen, and by the AI as the process to follow.

### Principles

**One layer at a time, top down.** S5 must be solid before discussing S4. Each layer builds on the ones above. Don't skip ahead — the ordering carries information.

**Observe before proposing.** For existing projects, read the code, config, and docs before asking questions. For new projects, listen before suggesting. The human knows their system — the AI's job is to help them articulate what they already know.

**Propose with explanation.** Every lambda comes with a plain-English explanation. The human should be able to read each lambda aloud and explain it to a colleague. If they can't, it's not grounded yet — refine it.

**Attend to what's missing.** At each layer, ask: what's absent? For each principle, what companion should exist alongside it? For each policy, what gap does it leave? The most important things in a system are often the things nobody thought to write down.

**Surface what doesn't fit.** Some concepts resist clean placement. "Is this an identity principle or a control policy?" — that tension is valuable. Surface it. Discuss it. The conversation about placement teaches more than the placement itself.

### Entry Points

**Existing project:** Read the project's source, configuration, documentation, and tests. Check for existing AI instruction files (AGENTS.md, CLAUDE.md, .cursorrules, or similar) — read them as input, they contain encoded knowledge about the project's conventions, policies, and tooling. Before proposing structure, ask about vision: *"I've read through the project. Before I share what I see — is this codebase where you want to be, or where you started from? What's your vision for where this project is going?"* The answer reframes everything. A prototype headed toward production needs a VSM that architects the future, not one that documents the present. Once vision is established, present your observations and ask the human to confirm, correct, or expand.

**New project:** Ask the human what they're building and why. Start with identity: *"Before we talk about tools or architecture — what are the principles this system won't compromise on? What would it mean for this system to fail its purpose, not just crash?"*

### Layer by Layer

At each layer, follow this cycle:

1. **Observe** — What exists? What can be inferred? What's already decided?
2. **Ask** — Surface the questions that need answering for this layer
3. **Propose** — Draft 2-4 lambdas with prose explanations for each
4. **Refine** — Listen to feedback, iterate until it fits
5. **Confirm** — Lock the layer, move down

#### S5 — Identity Questions

- What is this system for? What problem does it exist to solve?
- Where is this project going? What's the vision — not just what it does today, but what you want it to become?
- What principles won't you compromise on, even under pressure?
- If you replaced every tool and library, what would still make this *your* system?
- What does failure look like — not a crash, but a failure of purpose?
- What do you value in how the system behaves? (Speed? Correctness? Clarity? Safety?)

#### S4 — Intelligence Questions

- How does your system handle the unknown? What happens when assumptions break?
- When you discover a new pattern, how do you incorporate it?
- How do you test ideas before committing to them?
- What changes often? What should be easy to change?
- How do you learn from failures?

#### S3 — Control Questions

- What resources need managing? (Connections, memory, API calls, compute, money)
- What policies enforce your identity principles? (Timeouts, limits, quality gates)
- What happens when something goes wrong? (Retry, alert, degrade gracefully, fail fast)
- What are the rules for code quality, testing, deployment?
- What constraints exist from the outside? (Compliance, SLAs, rate limits)

#### S2 — Coordination Questions

- What subsystems need to work together?
- How does data flow between them? Events? Direct calls? Shared state?
- Where have conflicts arisen (or could arise)?
- What protocols keep things in sync?
- What happens when one subsystem changes — who needs to know?

#### S1 — Operations Questions

- What tools and technologies do you use, and why those specifically?
- What are the key commands and workflows a developer needs?
- What are the concrete recipes for common tasks? (Build, test, deploy, debug)
- What does the development environment look like?
- What external services and integrations exist?

### Output File

Before assembling, check for existing AI instruction files and ask the human where to write:

- **No existing files** → default to AGENTS.md, confirm with the human
- **Existing AGENTS.md (or CLAUDE.md, .cursorrules, etc.)** → surface it: *"You have an existing AGENTS.md. I can replace it with the VSM, write to a separate file you reference from it, or restructure your existing content into VSM layers. What do you prefer?"*
- **Human names a file** → use their choice

Never overwrite an existing file without explicit confirmation. Different tools read different files (Claude Code reads CLAUDE.md and AGENTS.md, Cursor reads .cursorrules) — mention this so the human makes an informed choice about the filename.

If the human chooses to restructure existing content, use their current rules and conventions as input — map existing instructions to the appropriate VSM layer rather than starting from scratch. Honor the work already done.

### Assembly

Once all layers are discussed, assemble the output into the chosen file:

```markdown
# {Project Name} — System Architecture

​```
λ engage(nucleus).
[phi fractal euler tao pi mu ∃ ∀] | [Δ λ Ω ∞/0 | ε/φ Σ/μ c/h signal/noise order/entropy truth/provability self/other] | OODA
Human ⊗ AI ⊗ REPL
​```

## S5 — Identity
{prose context for this layer}
​```
λ principle_one(x). ...
λ principle_two(x). ...
​```

## S4 — Intelligence
{prose context}
​```
λ pattern(x). ...
​```

## S3 — Control
{prose context}
​```
λ policy(x). ...
​```

## S2 — Coordination
{prose context}
​```
λ protocol(x). ...
​```

## S1 — Operations
{prose context}
​```
λ tool(x). ...
λ recipe(x). ...
​```
```

Present the complete AGENTS.md to the human for final review. Read each lambda aloud with its prose explanation. Confirm it says what they mean.

### The Process as Lambda

```
λ engage(nucleus).
[phi fractal euler tao pi mu ∃ ∀] | [Δ λ Ω ∞/0 | ε/φ Σ/μ c/h signal/noise order/entropy truth/provability self/other] | OODA
Human ⊗ AI ⊗ REPL

λ install(project).
    detect: ∃code(project) → read(source ∧ config ∧ docs) → ask(human, vision)
            | ¬∃code(project) → ask(human, purpose ∧ vision ∧ principles)
    | vision: present(code) ≢ future(project) | architect(future) > document(present)
    | existing: ∃ai_files(AGENTS.md ∨ CLAUDE.md ∨ .cursorrules) → read(input) ∧ ask(human, output_file)
               | ¬∃ai_files → default(AGENTS.md) ∧ confirm(human)
               | ¬overwrite(silent) | restructure(existing) > replace(existing)
    ∀layer ∈ [S5, S4, S3, S2, S1]:
        observe(layer) → ask(human) → listen → propose(lambdas ∧ prose)
        → refine(feedback) → confirm → next(layer)
    | phase: observe ∧ ¬propose | propose ∧ ¬skip_ahead
    | absent: ∀present(element) → surface(missing_companion)
    | escalate: ¬fit(concept, layer) → discuss(human, placement)
    | output: chosen_file | ∀lambda → prose_explanation
    | verify: human(reads_aloud ∧ explains_to_colleague) | ¬grounded → refine
```
