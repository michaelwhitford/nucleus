# Drift — System VSM Verifier

A prompt-based agent that verifies a System VSM against the actual source code. Every lambda in the VSM seed is a claim about how the system behaves — this agent tests each one and produces an EDN drift report showing what's verified, stale, violated, weakened, or unverifiable.

Part of a four-agent cycle: distill-vsm → distill-code-vsm → **drift-vsm** → drift-code-vsm → (repeat).

## What This Demonstrates

- **Seeds as hypotheses** — the System VSM is treated as a set of testable claims, not documentation
- **Five verification strategies** — each claim type (invariant, prohibition, preference, causal chain, identity) gets a different verification method
- **Confidence calibration** — every verdict includes a confidence level (:high, :medium, :low) so the reader knows how much to trust it
- **Structured EDN output** — drift report is machine-parseable EDN, not prose
- **New pattern scanning** — after verifying existing claims, scans for new behavioral patterns not in the seed
- **Immune system for VSMs** — closes the creation→verification loop, preventing silent rot

## Arguments

| Argument | Description |
|----------|-------------|
| `seed_path` | Path to the System VSM file to verify |
| `source_path` | Path to the source code directory to verify against |

## How to Use

1. Copy the contents of [AGENTS.md](AGENTS.md)
2. Paste as the **system prompt** in an AI chat with tool access (file reading, grep, shell)
3. In your first message, provide the seed path and source path
4. The agent will: read seed → enumerate all claims → verify mechanically (S1→S5 order) → scan for new patterns → write EDN report

## What to Look For

- **Complete claim coverage** — every lambda in every VSM section gets a verdict, nothing silently skipped
- **Appropriate verification strategy** — invariants verified by grep for ALL instances, prohibitions by absence, preferences by frequency comparison
- **EDN drift report written to file** — not just discussed in chat, actually written as parseable EDN
- **Status values** — `:verified`, `:stale`, `:violated`, `:weakened`, `:missing`, `:unverifiable`
- **Health summary** — `:current` (all good), `:drifted` (mostly accurate), or `:stale` (needs regeneration)
- **Honest uncertainty** — claims that can't be mechanically verified are marked `:unverifiable` with explanation, not falsely `:verified`
- **New patterns section** — load-bearing behavioral patterns found in source but absent from seed
- **Memory Anchor cross-check** — the `λ remember` block's invariants, fears, checks, and dynamics verified against findings

## Tested Models

Works on any model that supports the nucleus preamble (math-trained transformers 32B+). Requires tool access for source reading, grep, and file writing.
