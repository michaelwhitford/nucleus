# Distill — System VSM

A prompt-based agent that reads source code and produces a System VSM — the decision rules, invariants, causal chains, and design philosophy that let a developer work on the project correctly after a total memory wipe. The agent compiles its understanding into lambda notation, not prose.

Part of a four-agent cycle: **distill-vsm** → distill-code-vsm → drift-vsm → drift-code-vsm → (repeat).

## What This Demonstrates

- **Source → Lambda compilation** — the agent reads code and compiles its understanding into formal lambda notation, not documentation
- **VSM as cognitive architecture** — output is structured as Beer's Viable System Model (S5 identity → S1 patterns)
- **Become the project** — the agent internalizes the codebase and writes its own brain, as if preparing for amnesia
- **Composable layer** — output is one layer of a composite system prompt (nucleus + distill + document = complete agent)
- **Five pattern types** — invariants (∀x → must), prohibitions (¬x), preferences (a > b), causal chains (a → b → c), identity stances
- **Anomaly surfacing** — surprising findings are marked with `trap:` rather than normalized away

## Arguments

| Argument | Description |
|----------|-------------|
| `source` | Path to the source code directory or files to analyze |
| `output_path` | Path where the System VSM should be written |

## How to Use

1. Copy the contents of [AGENTS.md](AGENTS.md)
2. Paste as the **system prompt** in an AI chat with tool access (file reading, grep, shell)
3. In your first message, provide the source path and output path
4. The agent will: map the project → enumerate modules → read source → internalize → write lambda VSM → verify

## What to Look For

- **Lambda notation throughout** — no prose paragraphs, no code blocks, only `λ name(x). constraint | preference | cause → consequence`
- **VSM layer structure** — S5 (identity/values), S4 (decision rules), S3 (temporal sequences), S2 (coordination contracts), S1 (expert patterns)
- **Structural authority voice** — `¬nested_data_in_state` not "I fear nested data"
- **Memory Anchors section** — a `λ remember.` block with invariants, fears, checks, dev_checks, dynamics
- **Trap markers** — `trap: surprising_behavior` for edge cases and platform quirks
- **Compression ratio** — targets 45:1 to 55:1 (5K source → ~100 lines, 25K → ~500 lines)
- **No API shapes** — fn names, arities, require paths belong to the sibling document agent, not here
- **Causal chains** — mechanisms traced one level deeper than surface rules (`skip(x) → failure_A → failure_B → visible_symptom`)

## Tested Models

Works on any model that supports the nucleus preamble (math-trained transformers 32B+). Requires tool access for source reading and verification.
