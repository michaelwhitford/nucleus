# Distill — Code VSM

A prompt-based agent that reads source code and produces a Code VSM — the public API skeleton (fn signatures, arities, require paths, protocols, composition edges) that lets an agent generate correct code without hallucinating function names or arities. Output is lambda notation: shapes that expand fractally into correct code.

Part of a four-agent cycle: distill-vsm → **distill-code-vsm** → drift-vsm → drift-code-vsm → (repeat).

## What This Demonstrates

- **Source → API skeleton** — the agent extracts every public shape as a lambda signature, discarding implementation bodies
- **Generative seeds** — output shapes are designed so an agent can expand them into correct code with zero hallucinated fns or arities
- **VSM structure** — organized as Beer's Viable System Model (S5 identity → S1 patterns)
- **Composable layer** — output is the "skeleton" layer of a composite system prompt (nucleus + distill + document = complete agent)
- **Fan-in ranking** — modules are ranked by import count; high fan-in cores are documented first
- **Anomaly surfacing** — traps like `trap: v0_evaluateCode_stderr_callback_wired_to_stdout` are surfaced, not hidden
- **Language agnostic** — works on any codebase by discovering the language's declarator keywords

## Arguments

| Argument | Description |
|----------|-------------|
| `source` | Path to the source code directory or files to analyze |
| `output_path` | Path where the Code VSM should be written |

## How to Use

1. Copy the contents of [AGENTS.md](AGENTS.md)
2. Paste as the **system prompt** in an AI chat with tool access (file reading, grep, shell)
3. In your first message, provide the source path and output path
4. The agent will: map the project → rank modules by fan-in → enumerate all exports → write lambda shapes → verify against source

## What to Look For

- **Lambda signatures for every public fn** — `λ fn_name(args). return_type | constraints`
- **Require Map** — every namespace with canonical alias, copy-pasteable by agents
- **Complete export coverage** — the agent enumerates ALL exports before writing to avoid premature termination
- **Return type depth** — objects with lifecycle/methods get their own lambda, not just `→ Promise<string>`
- **Composition section** — edges between shapes as lambdas showing how fns feed into each other
- **Trap markers** — `trap:` for surprising access paths, callback wiring errors, arity mismatches
- **Compression ratio** — targets 25:1 to 35:1 (5K source → ~170 lines, 25K → ~800 lines)
- **No decision rules** — invariants, preferences, causal chains belong to the sibling distill agent, not here
- **Shape families** — siblings with shared signatures grouped as `λ family(args). variants: a | b | c`

## Tested Models

Works on any model that supports the nucleus preamble (math-trained transformers 32B+). Requires tool access for source reading and verification.
