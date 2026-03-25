# Drift — Code VSM Verifier

A prompt-based agent that verifies a Code VSM against the actual source code. Every fn signature, arity, require path, and composition edge in the seed is a hypothesis — this agent tests each one and produces an EDN drift report showing what's verified, stale, missing, or changed.

Part of a four-agent cycle: distill-vsm → distill-code-vsm → drift-vsm → **drift-code-vsm** → (repeat).

## What This Demonstrates

- **API shapes as testable claims** — every `λ fn_name(args).` in the Code VSM is verified against actual source declarations
- **Section-specific verification** — require paths checked first (most critical), then fn shapes, sequences, contracts, patterns
- **Batch verification** — related claims grouped into efficient batched grep calls rather than one-per-claim
- **Structured EDN output** — drift report is machine-parseable EDN with sections for requires, shapes, sequences, and new API
- **New API scanning** — after verifying existing claims, scans for new public declarations not in the seed
- **Immune system for Code VSMs** — closes the creation→verification loop, preventing silent rot

## Arguments

| Argument | Description |
|----------|-------------|
| `seed_path` | Path to the Code VSM file to verify |
| `source_path` | Path to the source code directory to verify against |

## How to Use

1. Copy the contents of [AGENTS.md](AGENTS.md)
2. Paste as the **system prompt** in an AI chat with tool access (file reading, grep, shell)
3. In your first message, provide the seed path and source path
4. The agent will: read seed → enumerate all claims → verify requires first → verify shapes → verify sequences → scan for new API → write EDN report

## What to Look For

- **Require paths verified first** — wrong require → agent can't import → everything fails; this is checked before anything else
- **Arity verification** — not just fn existence, but correct argument count confirmed against source
- **Complete claim enumeration** — agent counts total claims before starting ("I have N claims to verify") to prevent premature termination
- **EDN drift report written to file** — not just discussed in chat, actually written as parseable EDN
- **Status values** — `:verified`, `:stale`, `:missing`, `:changed` with evidence for each non-verified item
- **Health summary** — `:current` (all good), `:drifted` (mostly accurate), or `:stale` (needs regeneration)
- **New public API section** — functions, protocols, or records added since the seed was written, prioritized by fan-in
- **Batch grep efficiency** — multiple fn names checked per grep call rather than one grep per function

## Tested Models

Works on any model that supports the nucleus preamble (math-trained transformers 32B+). Requires tool access for source reading, grep, and file writing.
