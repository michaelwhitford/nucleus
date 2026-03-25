# Backgammon

A self-executing prompt that runs a full game of Backgammon inside the LLM. The entire game is a single EDN statechart — board layout, move rules, legal move validation, bearing off, hitting, the bar, and AI strategy are all defined as data. The LLM reads the structure and plays as both runtime and opponent.

First to bear off all 15 checkers wins.

## What This Demonstrates

- **EDN as executable program** — the complete backgammon ruleset is a statechart, not prose instructions
- **Complex game state** — 24 points, bar, borne-off counts, dice, turn tracking, checker conservation invariant (always 15 per player)
- **LLM as game engine** — the model enforces legal moves, detects blocked points, handles hits, manages bar re-entry, validates bearing off
- **AI opponent** — the AI plays its full turn autonomously, choosing moves strategically (hitting blots, making points, advancing back checkers)
- **Real randomness** — dice are rolled via `bash: echo $((1 + $RANDOM % 6))` for actual random values, not LLM-generated numbers
- **ASCII board rendering** — a consistent two-panel board with checkers hanging down from top and stacking up from bottom
- **TRACE as proof of computation** — every roll and move gets a TRACE line showing dice, move, hits, and remaining dice
- **Zero arguments** — paste and play, no parameterization needed

## How to Use

1. Copy the contents of [AGENTS.md](AGENTS.md)
2. Paste as the **system prompt** or **first message** in any AI chat (must support tool/shell access for dice rolling)
3. Send any message (or type `roll`) to start the game
4. Type your moves: `13/7` (point to point), `bar/22` (enter from bar), `6/off` (bear off)
5. The AI plays its turn automatically after yours
6. First to bear off all 15 checkers wins. Type `rematch` to play again.

## Move Format

| Format | Meaning |
|--------|---------|
| `13/7` | Move checker from point 13 to point 7 |
| `bar/22` | Enter a checker from the bar onto point 22 |
| `6/off` | Bear off a checker from point 6 |

## What to Look For

- **Consistent ASCII board** — the two-panel layout should render identically every turn with checkers in correct positions
- **Legal move enforcement** — blocked points (2+ opponent checkers) rejected, bar re-entry forced when checkers are on bar
- **Hit detection** — landing on a lone opponent checker sends it to the bar
- **Bearing off rules** — only allowed when all 15 checkers are in the home board; exact die or higher die when no higher point occupied
- **Doubles handling** — rolling doubles gives 4 moves instead of 2
- **Checker conservation** — `checkers on board + bar + borne-off = 15` for each player at all times
- **TRACE lines** — `[TRACE] turn:1 | O rolled [6,3] | move 13→7 | hit:none | dice-left:[3] | borne-off ●:0 ○:0`
- **AI plays autonomously** — the AI rolls, computes, and executes its full turn without asking for input
- **No prose leakage** — the model should execute the EDN, not narrate about it

## Tested Models

Works on any model that supports the nucleus preamble (math-trained transformers 32B+). Requires shell/tool access for dice rolling via bash.

Tested successfully on Claude Haiku — even smaller models handle the full ruleset (legal move validation, bar re-entry, bearing off, hit detection, checker conservation) when the EDN statechart provides the complete structure. Notably, Haiku maintains coherent state tracking across the entire conversation — 24 points of board state, bar counts, borne-off counts, dice remaining, and turn number all tracked accurately turn after turn in accumulated context.
