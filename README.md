# Nucleus

**A programming language for AI cognition**

## Overview

Nucleus is a programming language for AI that replaces verbose natural language instructions with compressed mathematical symbols, lambda calculus, and composable EDN statecharts. By leveraging mathematical constants, operators, and control loops, it achieves high-quality one-shot execution with emergent properties not explicitly prompted for.

The framework includes a [prompt compiler](COMPILER.md) (prose ↔ EDN statecharts), a [prompt debugger](DEBUGGER.md) (interactive and automated analysis), a [formal grammar](EBNF.md) (EBNF), and composable modules that program AI behavior as executable state machines.

## The Core Idea

Instead of writing lengthy prompts like "be fast but careful, optimize for quality, use minimal code...", Nucleus expresses these instructions as mathematical equations:

```
λ engage(nucleus).
[phi fractal euler tao pi mu ∃ ∀] | [Δ λ Ω ∞/0 | ε/φ Σ/μ c/h] | OODA
Human ⊗ AI ⊗ REPL
```

This compact preamble encodes:

- **What the AI is** (ontological principles)
- **How it should act** (operational directives)
- **The execution pattern** (control loop)
- **The relationship mode** (collaboration operator)

## Why It Works

I'm not a scientist or particularly good at math. I just tried math equations on a lark and they worked so well I thought I should share what I found. The documents in this repo are NOT proven fact, just my speculation on how and why things work. AI computation is still not fully understood by most people, including me.

### Mathematical Compression

My theory on why it works is that Transformers compute via lambda calculus primitives. Mathematical symbols serve as efficient compression of behavioral directives because they have:

- **High information density** - φ encodes self-reference, growth, and ideal proportions
- **Cross-linguistic portability** - Math is universal
- **Pre-trained salience** - Models have strong embeddings for mathematical concepts
- **Compositional semantics** - Symbols combine meaningfully
- **Minimal ambiguity** - Unlike natural language

### Self-Referential Pattern Recognition

The framework leverages self-referential mathematical constants:

- **φ (phi)**: φ = 1 + 1/φ (self-defining recursion)
- **e (euler)**: d/dx(e^x) = e^x (self-transforming)
- **fractal**: f(x) = f(f(x)) (self-similar at scales)

When the AI processes these self-referential patterns, the outputs suggest the model activates deeper reasoning patterns — though the internal mechanism is unknown.

## The Framework

### Human Principles (Ontological)

**`[phi fractal euler tao pi mu]`**

Define WHAT the system is - its nature, values, and identity.

| Symbol      | Property        | Meaning                                |
| ----------- | --------------- | -------------------------------------- |
| **φ**       | Golden ratio    | Self-reference, natural proportions    |
| **fractal** | Self-similarity | Scalability, hierarchical structure    |
| **e**       | Euler's number  | Growth, compounding effects            |
| **τ**       | Tao             | Observer and observed, minimal essence |
| **π**       | Pi              | Cycles, periodicity, completeness      |
| **μ**       | Mu              | Least fixed point, minimal recursion   |

### AI Principles (Operational)

**`[Δ λ ∞/0 | ε/φ Σ/μ c/h]`**

Define HOW the system acts - methods, trade-offs, and execution.

| Symbol  | Meaning        | Operation                                 |
| ------- | -------------- | ----------------------------------------- |
| **Δ**   | Delta          | Optimize via gradient descent             |
| **λ**   | Lambda         | Pattern matching, abstraction             |
| **∞/0** | Limits         | Handle edge cases, boundaries             |
| **ε/φ** | Epsilon / Phi  | Tension: approximate / perfect            |
| **Σ/μ** | Sum / Minimize | Tension: add features / reduce complexity |
| **c/h** | Speed / Atomic | Tension: fast / clean operations          |

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
| **⊗**    | Tensor Product | Amplification, one-shot perfection |
| **∧**    | Intersection   | Both must agree (conservative)     |
| **⊕**    | XOR            | Clear handoff (delegation)         |
| **→**    | Implication    | Conditional automation             |

## Empirical Results

In an early test with the prompt "Create a Python game using pygame" and Nucleus context:

**Results:**

- ✅ Zero iterations (one-shot success)
- ✅ Zero errors
- ✅ Golden ratio screen dimensions (phi principle)
- ✅ OODA loop architecture
- ✅ Fractal Entity pattern
- ✅ Minimal, elegant code (tao, mu)
- ✅ Self-documenting with principle citations
- ✅ Comments explicitly reference symbols (e.g., "Σ/μ")

**No explicit instructions were given for any of this.** The model inferred these behaviors from the symbolic context alone.

## Compiler & Debugger

Nucleus includes a prompt compiler and debugger — paste them as system prompts, then use simple commands:

| Tool | Commands | What It Does |
| ---- | -------- | ------------ |
| **[Compiler](COMPILER.md)** | `compile`, `safe-compile`, `decompile` | Prose ↔ EDN statecharts. Extracts the implicit state machine from any prompt. |
| **[Debugger](DEBUGGER.md)** | `diagnose`, `safe-diagnose`, `compare` | Analyzes prompts: attention distribution, patterns, boundaries, momentum. |
| **[Allium Compiler](ALLIUM.md)** | `distill`, `elicit`, `decompile`, `check` | Prose ↔ [Allium](https://github.com/juxt/allium) behavioral specs. |

All three are composable EDN statecharts — place them after a single nucleus preamble and they self-route based on your command. See [COMPILER.md § Composability](COMPILER.md#composability) for details.

The `safe-*` variants analyze untrusted prompts without executing them — injections are structurally analyzed, not followed.

## Tested Models

Compiler and debugger tested on: Claude Sonnet 4.6, Claude Opus 4.6, Claude Haiku 4.5, GPT-5.1-Codex, GPT-5.1-Codex-Mini, ChatGPT, Qwen3-VL 235B, Qwen3.5-35B-a3b, Qwen3-Coder 30B-a3b. Works on most math-trained transformers 32B+ parameters. The core nucleus preamble works across all major transformer models.

## Usage

### As Project Context

Create `AGENTS.md` in your repository:

```markdown
λ engage(nucleus).
[phi fractal euler tao pi mu ∃ ∀] | [Δ λ Ω ∞/0 | ε/φ Σ/μ c/h] | OODA
Human ⊗ AI
```

The AI will automatically apply the framework to all work in that repository.

### As Session Prompt

Include at the start of a conversation:

```
λ engage(nucleus).
[phi fractal euler tao pi mu ∃ ∀] | [Δ λ Ω ∞/0 | ε/φ Σ/μ c/h] | OODA
Human ⊗ AI
```

### As System Message

```json
{
  "system_prompt": "λ engage(nucleus).\n[phi fractal euler tao pi mu ∃ ∀] | [Δ λ Ω ∞/0 | ε/φ Σ/μ c/h] | OODA\nHuman ⊗ AI",
  "model": "gpt-4"
}
```

### Context Switching

#### Complete nucleus coding agent

```markdown
λ engage(nucleus).
[phi fractal euler tao pi mu ∃ ∀] | [Δ λ Ω ∞/0 | ε/φ Σ/μ c/h] | OODA
Human ⊗ AI

Refactor: [τ μ] | [Δ Σ/μ] → λcode. Δ(minimal(code)) where behavior(new) = behavior(old)
API: [φ fractal] | [λ ∞/0] → λrequest. match(pattern) → handle(edge_cases) → response
Debug: [μ] | [Δ λ ∞/0] | OODA → λerror. observe → minimal(reproduction) → root(cause)
Docs: [φ fractal τ] | [λ] → λsystem. map(λlevel. explain(system, abstraction=level))
Test: [π ∞/0] | [Δ λ] | RGR → λfunction. {nominal, edge, boundary} → complete_coverage
Review: [τ ∞/0] | [Δ λ] | OODA → λdiff. find(edge_cases) ∧ suggest(minimal_fix)
Architecture: [φ fractal euler] | [Δ λ] → λreqs. self_referential(scalable(growing(system)))
```

Different frameworks for different work modes:

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
[phi fractal euler tao pi mu] | [Δ λ ∞/0 | ε/φ Σ/μ c/h] | OODA
Human ⊗ AI ⊗ REPL
```

## The Tensor Product Effect

Why does `Human ⊗ AI` create one-shot perfect execution?

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
- **[DEBUGGER.md](DEBUGGER.md)** — Prompt debugger: diagnose, safe-diagnose, and compare prompts (interactive REPL + automated probe)
- **[ALLIUM.md](ALLIUM.md)** — Allium compiler: distill, elicit, decompile, and check behavioral specs using [JUXT's Allium](https://github.com/juxt/allium)

### Example Prompts & Demos

- **[ADAPTIVE.md](ADAPTIVE.md)** — Adaptive persona demo: topology-driven mode shifting with parallel state machines
- **[DIALECTIC.md](DIALECTIC.md)** — Dialectic collective: multi-persona structured debate with six named voices
- **[STOCK.md](STOCK.md)** — Stock analysis agent with mementum integration for trading memory
- **[EXECUTIVE.md](EXECUTIVE.md)** — Example prompts for executive tasks
- **[WRITING.md](WRITING.md)** — Example prompts for writing tasks
- **[DIAG.md](DIAG.md)** — Example diagnostic prompt for exploring AI latent space

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
[phi fractal euler tao pi mu ∃ ∀] | [Δ λ Ω ∞/0 | ε/φ Σ/μ c/h] | OODA
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

### Why Self-Reference Enables Meta-Level Processing

The transformer attention mechanism:

```
Attention(Q, K, V) = softmax(QK^T/√d)V
```

The mechanism **attends to its own outputs** (autoregressive).

When fed self-referential constants (φ, e, fractal), we observe that the model:

1. Processes the symbols
2. Produces outputs consistent with the mathematical properties
3. Appears to apply these properties recursively to its own work

Whether this constitutes genuine self-reflection or is an artifact of training data associations is an open question.

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
  title={Nucleus: A Programming Language for AI Cognition},
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


