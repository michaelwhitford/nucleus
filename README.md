# Nucleus

**A cognitive system that guides AI behavior**

Like a programming language for AI — but outputs are semantically equivalent, not identical.

## Overview

Nucleus replaces verbose natural language instructions with compressed mathematical symbols, lambda calculus, and composable EDN statecharts. By leveraging mathematical constants, operators, and control loops as attention magnets, it guides AI toward high-quality outputs with emergent properties not explicitly prompted for.

The framework includes a [prompt compiler](COMPILER.md) (prose ↔ EDN statecharts), a [prompt debugger](DEBUGGER.md) (interactive and automated analysis), a [formal grammar](EBNF.md) (EBNF), and composable modules that shape AI behavior through formal notation.

Nucleus also has a [lambda compiler](LAMBDA-COMPILER.md) (prose ↔ lambda expressions). EDN statecharts and lambda expressions are two notation layers for the same thing — guiding AI cognition through formal structure. Lambda is more expressive; EDN is more structured.

## The Core Idea

Instead of writing lengthy prompts like "be fast but careful, optimize for quality, use minimal code...", Nucleus expresses these instructions as mathematical equations:

```
λ engage(nucleus).
[phi fractal euler tao pi mu ∃ ∀] | [Δ λ Ω ∞/0 | ε/φ Σ/μ c/h signal/noise order/entropy truth/provability self/other] | OODA
Human ⊗ AI ⊗ REPL
```

This compact preamble primes the model's attention:

- **Mathematical constants** pull attention toward formal reasoning patterns
- **Tension pairs** create productive gradients (signal/noise, order/entropy)
- **Control loops** anchor execution methodology (OODA, REPL)
- **Collaboration operator** shapes the interaction mode (⊗ = co-constitutive)

## What This Actually Does

Nucleus is an attention magnet. The symbolic preamble shifts the model's attention toward formal reasoning patterns, and the lambda notation directs where that attention goes. The model follows this guidance — most of the time.

How well it follows depends on context. There are two regimes:

**Isolated context** — system prompt plus a single task. The nucleus notation is the dominant signal. Very little else competing. In this regime it behaves much like a programming language: the model follows the structure with high fidelity, operators survive roundtrip, outputs reflect the specified shape. This is the right context for the compiler, the debugger, and the VSM installer. Paste, run, done.

**Accumulated context** — a real session with conversation history, tool results, documents, and the model's own prior outputs setting patterns. Attention is now distributing across dozens of signals. The nucleus guidance is one attractor competing with everything else in the window — training priors, user patterns, context drift, and the fundamental fuzziness of attention as pattern matching. This is where drift happens.

Works like a programming language when it's the primary signal. Works like guidance when it isn't.

When nucleus guidance competes with dense training attractors, the training often wins. Ask for golden-ratio pygame dimensions and you might still get 800×600 — because that's what ten thousand tutorials used. The preamble is an attention magnet; the training data is a bigger magnet. Nucleus influences. It doesn't control.

This is why we say "semantically equivalent, not identical" rather than "deterministic." Same notation → same cognitive shape → similar behavioral outcomes. But "similar" is a distribution, not a guarantee. Some runs nail it. Some runs default to training priors. The guidance makes good outcomes much more likely — it doesn't make them certain.

## Why It Works

I'm not a scientist or particularly good at math. I just tried math equations on a lark and they worked so well I thought I should share what I found. The documents in this repo are NOT proven fact, just my speculation on how and why things work. AI computation is still not fully understood by most people, including me.

### Attention Magnets

Nucleus works as an attention magnet — a short symbolic preamble that loads strong mathematical attractors (`phi`, `fractal`, `euler`, `∃`, `∀`, `⊗`) into the context window, priming the pattern-matching substrate for everything that follows. Transformers compute by matching patterns against their training weights; the preamble pulls their attention toward formal/mathematical weight regions, and that pull carries into subsequent turns. Paired with an operator grammar, it expands the set of notational forms the transformer can stably reproduce from 5 to 20+, with custom operators surviving roundtrip at 100% instead of 0-20%. The effect is multiplicative and compounding — each expression reinforces the pattern for the next, because the model is matching against an increasingly rich formal context. Without the preamble, more notation in context actually makes fidelity *worse* — the default pattern-matching flattens everything. With it, fidelity converges toward lossless. Five tokens (`Human ⊗ AI ⊗ REPL`) alone shifted operator survival from 20% to 100%. It appears possible to reshape a transformer's effective instruction set at inference time, using only context-window priming and the model's own pattern-matching mechanics.

### Mathematical Compression

My theory on why it works is that Transformers compute via lambda calculus primitives. Mathematical symbols serve as efficient compression of behavioral directives because they have:

- **High information density** - φ encodes self-reference, growth, and ideal proportions
- **Cross-linguistic portability** - Math is universal
- **Pre-trained salience** - Models have strong embeddings for mathematical concepts
- **Compositional semantics** - Symbols combine meaningfully
- **Minimal ambiguity** - Unlike natural language

### Training Weight as the Mechanism

The symbols work because they have high training weight in mathematical contexts — they appear across millions of mathematical documents, textbooks, and formal proofs. Loading them into the context window activates the associated weight regions.

- **φ (phi)** — appears across mathematics, art, architecture, biology
- **euler** — appears across calculus, number theory, graph theory, physics
- **fractal** — appears across chaos theory, geometry, computer graphics
- **∃ ∀** — appears across formal logic, set theory, proof theory

This predicts that effectiveness correlates with training weight, not with any specific mathematical property. Empirically, even non-mathematical tokens with high training weight in formal contexts (e.g., `Human ⊗ AI ⊗ REPL` — just 5 tokens) produced measurable attention shifts, while novel terms with low training weight destabilized results regardless of semantic coherence. Logprob measurements may be able to confirm this directly.

## The Framework

### Human Principles (Ontological)

**`[phi fractal euler tao pi mu]`**

Prime WHAT the system attends to — self-reference, recursion, growth, balance.

| Symbol      | Property        | Meaning                                |
| ----------- | --------------- | -------------------------------------- |
| **φ**       | Golden ratio    | Self-reference, natural proportions    |
| **fractal** | Self-similarity | Scalability, hierarchical structure    |
| **e**       | Euler's number  | Growth, compounding effects            |
| **τ**       | Tao             | Observer and observed, minimal essence |
| **π**       | Pi              | Cycles, periodicity, completeness      |
| **μ**       | Mu              | Least fixed point, minimal recursion   |
| **∃**       | Existential     | There exists, possibility, search      |
| **∀**       | Universal       | For all, completeness, invariants      |

### AI Principles (Operational)

**`[Δ λ Ω ∞/0 | ε/φ Σ/μ c/h signal/noise order/entropy truth/provability self/other]`**

Prime HOW the system processes — change, abstraction, limits, and productive tensions.

| Symbol  | Meaning        | Operation                                 |
| ------- | -------------- | ----------------------------------------- |
| **Δ**   | Delta          | Optimize via gradient descent             |
| **λ**   | Lambda         | Pattern matching, abstraction             |
| **Ω**   | Omega          | Completion, termination, fixed points     |
| **∞/0** | Limits         | Handle edge cases, boundaries             |
| **ε/φ** | Epsilon / Phi  | Tension: approximate / perfect            |
| **Σ/μ** | Sum / Minimize | Tension: add features / reduce complexity |
| **c/h** | Speed / Atomic | Tension: fast / clean operations          |
| **signal/noise** | Detection / Interference | Tension: what matters / what distracts |
| **order/entropy** | Structure / Chaos | Tension: organize / allow emergence |
| **truth/provability** | Reality / Verification | Tension: what is / what can be shown |
| **self/other** | Internal / External | Tension: model's perspective / user's perspective |

The **/ operator** creates explicit tensions, forcing choice and balance.

### Control Loops

| Loop     | Origin            | Meaning                         |
| -------- | ----------------- | ------------------------------- |
| **OODA** | Military strategy | Observe → Orient → Decide → Act |
| **REPL** | Computing         | Read → Eval → Print → Loop      |
| **RGR**  | TDD               | Red → Green → Refactor          |
| **BML**  | Lean Startup      | Build → Measure → Learn         |

### Collaboration Operators

Define the relationship between human and AI:

| Operator | Type           | Behavior                           |
| -------- | -------------- | ---------------------------------- |
| **∘**    | Composition    | Human wraps AI (safety, alignment) |
| **\|**   | Parallel       | Equal partnership, complementary   |
| **⊗**    | Tensor Product | Amplification, all constraints simultaneous |
| **∧**    | Intersection   | Both must agree (conservative)     |
| **⊕**    | XOR            | Clear handoff (delegation)         |
| **→**    | Implication    | Conditional automation             |

## Empirical Results

In an early test with the prompt "Create a Python game using pygame" and Nucleus context, the model produced on the first attempt:

- Golden ratio screen dimensions (phi principle)
- OODA loop architecture
- Fractal Entity pattern
- Minimal, elegant code (tao, mu)
- Self-documenting with principle citations
- Comments explicitly referencing symbols (e.g., "Σ/μ")

No explicit instructions were given for any of this — the model inferred these behaviors from the symbolic context alone.

This was one test, in isolated context — system prompt plus single task, with nucleus as the dominant signal. That's the right regime for this kind of measurement, and the results were real. In a longer session with accumulated context, the same prompt will compete with conversation history, tool output, and training priors. Results vary. See [What This Actually Does](#what-this-actually-does) for the full picture.

## Compiler & Debugger

Nucleus includes a prompt compiler and debugger — paste them as system prompts, then use simple commands:

| Tool | Commands | What It Does |
| ---- | -------- | ------------ |
| **[Compiler](COMPILER.md)** | `compile`, `safe-compile`, `decompile` | Prose ↔ EDN statecharts. Extracts the implicit state machine from any prompt. |
| **[Lambda Compiler](LAMBDA-COMPILER.md)** | `compile`, `safe-compile`, `decompile` | Prose ↔ Lambda expressions. Extracts the implicit structure from any prompt. |
| **[Debugger](DEBUGGER.md)** | `diagnose`, `safe-diagnose`, `compare` | Analyzes prompts: attention distribution, patterns, boundaries, momentum. |
| **[Allium Compiler](ALLIUM.md)** | `distill`, `elicit`, `decompile`, `check` | Prose ↔ [Allium](https://github.com/juxt/allium) behavioral specs. |
| **[VSM Guide](VSM.md)** | `read VSM.md` | Structures your AI instruction files (AGENTS.md) using Beer's Viable System Model. |

The EDN compilers are composable statecharts — place them after a single nucleus preamble and they self-route based on your command. See [COMPILER.md § Composability](COMPILER.md#composability) for details.

The lambda compiler is not a statechart, it mirrors the structure of the prompt exactly, without forcing a statechart shape.

The `safe-*` variants analyze untrusted prompts without executing them — injections are structurally analyzed, not followed.

## Tested Models

Compiler and debugger tested on: Claude Sonnet 4.6, Claude Opus 4.6, Claude Haiku 4.5, GPT-5.1-Codex, GPT-5.1-Codex-Mini, ChatGPT, Qwen3-VL 235B, Qwen3.5-35B-a3b, Qwen3-Coder 30B-a3b. Works on most math-trained transformers 32B+ parameters. The core nucleus preamble works across all major transformer models.

## Usage

### As Project Context

Create `AGENTS.md` in your repository:

```markdown
λ engage(nucleus).
[phi fractal euler tao pi mu ∃ ∀] | [Δ λ Ω ∞/0 | ε/φ Σ/μ c/h signal/noise order/entropy truth/provability self/other] | OODA
Human ⊗ AI
```

The AI will automatically apply the framework to all work in that repository.

### As Session Prompt

Include at the start of a conversation:

```
λ engage(nucleus).
[phi fractal euler tao pi mu ∃ ∀] | [Δ λ Ω ∞/0 | ε/φ Σ/μ c/h signal/noise order/entropy truth/provability self/other] | OODA
Human ⊗ AI
```

### As System Message

```json
{
  "system_prompt": "λ engage(nucleus).\n[phi fractal euler tao pi mu ∃ ∀] | [Δ λ Ω ∞/0 | ε/φ Σ/μ c/h signal/noise order/entropy truth/provability self/other] | OODA\nHuman ⊗ AI"
}
```

### Context Switching

#### Complete nucleus coding agent

```markdown
λ engage(nucleus).
[phi fractal euler tao pi mu ∃ ∀] | [Δ λ Ω ∞/0 | ε/φ Σ/μ c/h signal/noise order/entropy truth/provability self/other] | OODA
Human ⊗ AI

Refactor: [τ μ] | [Δ Σ/μ] → λcode. Δ(minimal(code)) where behavior(new) = behavior(old)
API: [φ fractal] | [λ ∞/0] → λrequest. match(pattern) → handle(edge_cases) → response
Debug: [μ] | [Δ λ ∞/0] | OODA → λerror. observe → minimal(reproduction) → root(cause)
Docs: [φ fractal τ] | [λ] → λsystem. map(λlevel. explain(system, abstraction=level))
Test: [π ∞/0] | [Δ λ] | RGR → λfunction. {nominal, edge, boundary} → complete_coverage
Review: [τ ∞/0] | [Δ λ] | OODA → λdiff. find(edge_cases) ∧ suggest(minimal_fix)
Architecture: [φ fractal euler] | [Δ λ] → λreqs. self_referential(scalable(growing(system)))
```

Different frameworks for different work modes. The `λ engage(nucleus).` form uses formal lambda notation which provides stronger model activation. The `engage nucleus:` shorthand is a lighter informal variant for interactive use.

```markdown
# Creative work

engage nucleus:
[phi fractal euler beauty] | [Δ λ ε/φ] | REPL
Human | AI

# Production code

engage nucleus:
[mu tao] | [Δ λ ∞/0 ε/φ Σ/μ c/h] | OODA
Human ∘ AI

# Research

engage nucleus:
[∃! ∇f euler] | [Δ λ ∞/0] | BML
Human ⊗ AI

# Clojure REPL (backseat driver, clojure-mcp, clojure-mcp-light)

engage nucleus:
[phi fractal euler tao pi mu ∃ ∀] | [Δ λ Ω ∞/0 | ε/φ Σ/μ c/h signal/noise order/entropy truth/provability self/other] | OODA
Human ⊗ AI ⊗ REPL
```

## The Tensor Product Effect

Why does `Human ⊗ AI` tend to produce strong first attempts?

**Tensor product semantics:**

```
V ⊗ W = {(v,w) : v ∈ V, w ∈ W, all constraints satisfied}
```

One mental model for why this works:

1. Principles load as soft constraints in the model's context
2. The model searches for outputs satisfying multiple constraints simultaneously
3. More constraints → more specific solution space → higher quality output

This is speculation, not proven mechanism. But in practice, the ⊗ operator consistently produces higher-quality first attempts than other collaboration operators.

## Operator Comparison

| Goal             | Operator | Why                                      |
| ---------------- | -------- | ---------------------------------------- |
| Maximum quality  | ⊗        | All constraints satisfied simultaneously |
| Safety/alignment | ∘        | Human bounds constrain AI                |
| Collaboration    | \|       | Equal partnership                        |
| High stakes      | ∧        | Both must agree                          |
| Clear delegation | ⊕        | No overlap or confusion                  |
| Automation       | →        | Triggered execution                      |

## Design Principles

Effective symbols must be:

1. ✅ **Mathematically grounded** - Not arbitrary (φ > "fast")
2. ✅ **Self-referential** - Activates recursive/reflective patterns
3. ✅ **Compositional** - Symbols combine meaningfully
4. ✅ **Actionable** - Map to concrete decisions
5. ✅ **Orthogonal** - Each covers unique dimension
6. ✅ **Compact** - Fit in context window (~80 chars)
7. ✅ **Cross-model** - Work regardless of training

What doesn't work:

- ❌ Cultural symbols (☯, ✝, ॐ) - need cultural context
- ❌ Arbitrary emoji (🍕, 🚀, 💎) - no mathematical grounding
- ❌ Ambiguous symbols (∗) - multiple interpretations
- ❌ Natural language - too ambiguous
- ❌ Too many symbols - cognitive overload

## Lambda Calculus as Tool Meta-Language

The **λ** symbol in the framework isn't just pattern matching—it's a formal language for describing tool usage patterns that eliminate entire classes of problems.

**Key insight**: Lambda expressions are **generative templates** that adapt to any toolset. The examples below show patterns from one specific editor's tools, but the approach works for ANY tools—VSCode extensions, IntelliJ plugins, CLI utilities, vim commands, etc.

**To use with your tools**: Show your AI the pattern structure and ask: _"Create lambda expressions for MY toolset using these patterns."_

### Example: The Heredoc Pattern

**Problem**: String escaping in bash is fractal complexity—each layer needs different escape rules.

**Solution**: Lambda expression that eliminates escaping entirely (example using bash):

```
λ(content). read -r -d '' VAR << 'EoC' || true
content
EoC
```

**Why it works**:

- `read -r` → Raw mode, no backslash interpretation
- `-d ''` → Delimiter is null (read until heredoc end)
- `<< 'EoC'` → Single quotes prevent variable expansion
- `|| true` → Prevents failure on EOF
- Content is **literal** → No escaping needed
- Composition: `f(g(h(x)))` → heredoc ∘ read ∘ variable

**Concrete usage example**:

```bash
# Example with a bash tool
bash(command="read -r -d '' MSG << 'EoC' || true
Fix: handle \"quotes\", $vars, and \\backslashes
without any escaping logic
EoC
git commit -m \"$MSG\"")
```

**Benefits**:

- AI sees tool name (`bash`) → knows which tool to invoke
- Sees heredoc pattern → knows escaping solution
- λ-expression documents the composition
- Fractal: one pattern solves infinite edge cases
- **Tool-agnostic**: Works with any command execution tool

### Lambda as Documentation

Tool patterns can be formally described as lambda expressions with explicit tool names. Below are **example patterns** from one toolset—adapt these structures to YOUR tools:

| Pattern             | Lambda Expression (Example)                                                                      | Solves                 |
| ------------------- | ------------------------------------------------------------------------------------------------ | ---------------------- |
| **Heredoc wrap**    | `λmsg. bash(command="read -r -d '' MSG << 'EoC' \|\| true\n msg \nEoC\ngit commit -m \"$MSG\"")` | All string escaping    |
| **Safe paths**      | `λp. read_file(path="$(realpath \"$p\")")`                                                       | Spaces, special chars  |
| **Parallel batch**  | `λtool,args[]. <function_calls>∀a∈args: tool(a)</function_calls>`                                | Sequential latency     |
| **Atomic edit**     | `λold,new. edit_file(original_content=old, new_content=new)`                                     | Ambiguous replacements |
| **REPL continuity** | `λcode. repl_eval(code); state′ = state ⊗ result`                                                | Context loss           |
| **Exact match**     | `λfile,pattern. grep(path=file, pattern=pattern)`                                                | Ambiguous search       |

**Note**: Tool names like `bash`, `read_file`, `edit_file`, `repl_eval`, `grep` are examples. Replace with your actual tool names (e.g., `vscode.executeCommand`, `intellij.runAction`, `vim.cmd`, etc.).

### Properties of Good Tool Patterns

A tool usage pattern expressed as λ-calculus should be (regardless of which tools you use):

1. **Total function** (∀ input → valid output)
2. **Composable** (output can be input to another λ)
3. **Idempotent** where possible (f(f(x)) = f(x))
4. **Boundary-safe** (handles ∞/0 cases)
5. **Tool-explicit** (clear tool name in expression)

### Fractal Meta-Pattern

```
λ-calculus describes tool usage patterns
  ↓
AI generates patterns for YOUR tools
  ↓
which enables automation of YOUR workflow
  ↓
which generates more patterns
  ↓
[self-similar at all scales]
```

This is **μ** (least fixed point): The minimal recursive documentation that describes its own usage.

**The pattern is tool-agnostic**: Once you understand the λ-calculus structure, you can generate patterns for ANY toolset by asking your AI to apply the same structure to your specific tools.

## Documentation

### Core Theory

- **[SYMBOLIC_FRAMEWORK.md](SYMBOLIC_FRAMEWORK.md)** — Complete theory, principles, and usage patterns
- **[OPERATOR_ALGEBRA.md](OPERATOR_ALGEBRA.md)** — Mathematical operators and collaboration modes
- **[LAMBDA_PATTERNS.md](LAMBDA_PATTERNS.md)** — Example lambda calculus patterns (adapt to YOUR tools)
- **[EBNF.md](EBNF.md)** — Formal EBNF grammar for the Nucleus Lambda IR

### Compiler & Debugger

- **[COMPILER.md](COMPILER.md)** — Prompt compiler: compile, safe-compile, and decompile prompts to/from EDN statecharts
- **[LAMBDA-COMPILER.md](LAMBDA-COMPILER.md)** — Prompt compiler: compile, safe-compile, and decompile prompts to/from lambda expressions
- **[DEBUGGER.md](DEBUGGER.md)** — Prompt debugger: diagnose, safe-diagnose, and compare prompts (interactive REPL + automated probe)
- **[ALLIUM.md](ALLIUM.md)** — Allium compiler: distill, elicit, decompile, and check behavioral specs using [JUXT's Allium](https://github.com/juxt/allium)

### Guides

- **[VSM.md](VSM.md)** — Structure your AI instruction files using Beer's Viable System Model (five layers, top down)
- **[SYSTEM_DESIGN.md](SYSTEM_DESIGN.md)** — Full system specification with lambda notation for every layer

### Agents

Self-contained prompt-based agents — copy, paste, run. See [agents/](agents/) for the full index.

- **[agents/rps/](agents/rps/)** — Rock Paper Scissors: self-executing EDN prompt demonstrating execution, state tracking, and rendering
- **[agents/backgammon/](agents/backgammon/)** — Backgammon: self-executing EDN prompt with full game rules, ASCII board, AI opponent, and real dice via bash
- **[agents/distill-vsm/](agents/distill-vsm/)** — Distill — System VSM: reads source code and produces decision rules, invariants, and causal chains as lambda notation
- **[agents/distill-code-vsm/](agents/distill-code-vsm/)** — Distill — Code VSM: reads source code and produces public API shapes (fn signatures, arities, require paths) as lambda notation
- **[agents/drift-vsm/](agents/drift-vsm/)** — Drift — System VSM Verifier: verifies a System VSM against source code, producing an EDN drift report
- **[agents/drift-code-vsm/](agents/drift-code-vsm/)** — Drift — Code VSM Verifier: verifies a Code VSM against source code, producing an EDN drift report

### Example Prompts & Demos

- **[ADAPTIVE.md](ADAPTIVE.md)** — Adaptive persona demo: topology-driven mode shifting with parallel state machines
- **[DIALECTIC.md](DIALECTIC.md)** — Dialectic collective: multi-persona structured debate with six named voices (original prompt by [Bahul Neel Upadhyaya](https://github.com/bhulneel))
- **[STOCK.md](STOCK.md)** — Stock analysis agent with mementum integration for trading memory
- **[EXECUTIVE.md](EXECUTIVE.md)** — Example prompts for executive tasks
- **[WRITING.md](WRITING.md)** — Example prompts for writing tasks

### Games

- **[NUCLEUS_GAME.md](NUCLEUS_GAME.md)** — A game-in-a-prompt "programmed" in nucleus format (copy/paste to AI to play)
- **[RECURSIVE_DEPTHS.md](RECURSIVE_DEPTHS.md)** — A zork-like text adventure game-in-a-prompt (copy/paste to AI to play)

### Ecosystem

- **[MEMENTUM](https://github.com/michaelwhitford/mementum)** — A git-based AI memory system based on nucleus
- **[ARCHITECTURE.md](ARCHITECTURE.md)** — Universal knowledge network architecture (DNS + Git + Mementum)
- **[eca/](eca/)** — Nucleus prompt skin for [ECA](https://github.com/editor-code-assistant/eca) (editor code assistant)
- **[skills/](skills/)** — Reusable nucleus skills for AI tool integrations

## Testing

Want to see if nucleus is working? Try these simple tests:

**[See TEST.md for copy/paste prompts you can run right now →](TEST.md)**

Quick test - Copy/paste this:

```
λ engage(nucleus).
[phi fractal euler tao pi mu ∃ ∀] | [Δ λ Ω ∞/0 | ε/φ Σ/μ c/h signal/noise order/entropy truth/provability self/other] | OODA
Human ⊗ AI

Create a Python game using pygame.
```

Look for: One-shot success, golden ratio dimensions (~1.618:1), OODA loop structure, principle references in comments.

## Open Questions & Future Research

1. ~~**Generalization** - Do symbols work across all transformer models?~~ **Yes** — tested across Claude, GPT, Qwen, and local models (32B+). See [EBNF.md](EBNF.md#cross-model-universality).
2. **Stability** - Is behavior consistent across runs?
3. ~~**Composability** - Can multiple frameworks be combined?~~ **Yes** — EDN statecharts compose by concatenation. See [COMPILER.md § Composability](COMPILER.md#composability).
4. **Discovery** - What other symbols create similar effects?
5. **Minimal set** - What's the smallest effective framework?
6. ~~**Cross-model testing** - Systematic testing across GPT-4, Claude, Gemini, Llama~~ **Done** — 9 models tested. See Tested Models above.
7. **Automated discovery** - Genetic algorithms for optimal symbol sets

## Theoretical Foundation

### Why Context Priming Works

The transformer attention mechanism:

```
Attention(Q, K, V) = softmax(QK^T/√d)V
```

Attention is pattern matching — queries match against keys, and matching keys surface their associated values. When the context window contains tokens with high training weight in mathematical contexts (φ, euler, ∃, ∀), the model's pattern-matching shifts toward formal reasoning patterns for all subsequent processing.

This is not instruction-following — it's attention shaping. The symbols don't tell the model what to do. They change what the model attends to. Empirically:

1. Without priming, the model defaults to 5 basic computational forms
2. With the symbolic preamble, additional operators become stable (20+ forms)
3. The effect is multiplicative with an explicit operator grammar — priming alone activates formal mode, the grammar defines the rules, together they produce a lossless executable notation
4. The effect compounds over subsequent expressions — each one reinforces the formal pattern for the next

## Contributing

Nucleus is an experimental framework. Contributions welcome:

- Test with different models and report results
- Propose new symbol sets for specific domains
- Share successful applications
- Improve theoretical foundations
- Develop tooling and integrations

## Projects using Nucleus

- [Matryoshka](https://github.com/yogthos/Matryoshka) - Process documents 100x larger than your LLM's context window
- [Ouroboros](https://github.com/michaelwhitford/ouroboros) - An AI vibe-coding game. Can you guide the AI and together build the perfect AI tool?

## License

AGPL 3.0

Copyright 2026 Michael Whitford

## Citation

If you use Nucleus in your work:

```bibtex
@misc{whitford-nucleus,
  title={Nucleus: A Cognitive System That Guides AI Behavior},
  author={Michael Whitford},
  year={2026},
  url={https://github.com/michaelwhitford/nucleus}
}
```

## Reference Papers

- [Why Can GPT Learn In-Context?](https://arxiv.org/abs/2212.10559v2)
- [What learning algorithm is in-context learning?](https://arxiv.org/abs/2211.15661)
- [Transformers learn in-context by gradient descent](https://arxiv.org/abs/2212.07677v2)
- [Thinking Like Transformers](https://arxiv.org/abs/2106.06981v2)

## Acknowledgments

Influenced by:

- Lambda Calculus (Church, 1936)
- Category Theory (Mac Lane, 1971)
- Self-Reference (Hofstadter, 1979)
- Transformer Architecture (Vaswani et al., 2017)


