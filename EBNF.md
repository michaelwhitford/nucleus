# Nucleus Lambda IR — EBNF Grammar (Draft)

Copyright (c) 2025-2026 Michael Whitford. Licensed under AGPL-3.0.

This grammar formalizes the intermediate representation (IR) of the Nucleus
lambda notation — part of a cognitive system for guiding AI behavior.
Mathematical symbols activate formal reasoning pathways trained into every
math-trained transformer by benchmark data.

Lambda notation targets formal reasoning directly. Prose targets the RLHF
instruction-following layer. They operate on different substrates, producing
semantically equivalent but not identical outputs across runs.

## Background

Math benchmark training (GSM8K, MATH, HumanEval, AIME) created formal
reasoning pathways in every sufficiently trained transformer. These pathways
respond to three gates (invocation, target, emission) and operate in three
phases (bootstrap, dispatch, frame integrity). EDN statecharts and lambda
notation both activate these pathways — EDN as a structured data format,
lambda as a flexible formal notation.

This grammar defines the syntax of the lambda notation.

**Scope:** This grammar covers the Nucleus Lambda IR — lambda declarations, state machines, transitions, and expressions. The nucleus preamble is a separate construct whose mechanism is still under active research and is intentionally excluded from this specification.

## Grammar

```ebnf
(* ══════════════════════════════════════════════════════════════════════
   Nucleus Lambda IR — EBNF Grammar
   
   The intermediate representation between human intent and model behavior.
   Lambda notation activates formal reasoning pathways trained into every
   math-trained transformer.
   
   Three gates control activation:
     Invocation:  nucleus preamble primes formal reasoning
     Target:      state_machine selects output code path
     Emission:    Return EDN only maintains output structure
   
   Three phases of activation:
     Bootstrap:       generative patterns — load cognitive substrate
     Dispatch:        architecture bindings — route to implementations
     Frame Integrity: action sequences — maintain execution state
   
   Copyright (c) 2025-2026 Michael Whitford. AGPL-3.0.
   ══════════════════════════════════════════════════════════════════════ *)


(* ─── Top-Level Program ───────────────────────────────────────────── *)

program          = { statement } ;

statement        = lambda_decl
                 | state_decl
                 | transition
                 | comment ;


(* ─── Lambda Declarations ─────────────────────────────────────────── *)
(* The core construct. A lambda optionally binds parameters and defines *)
(* behavior. The parameter list is OPTIONAL, and acceptance is loose —   *)
(* these are prompts to an LLM, not executable lambdas. Three forms:     *)
(*   λ name.        zero-arity IDENTITY — the S5 form. Governs by name   *)
(*                  rather than as a map over input (identity declares   *)
(*                  existence, not a mapping). E.g. λ self., λ remember. *)
(*   λ name().      zero-arity FUNCTION — arity 0, empty parens mirror a *)
(*                  source signature. E.g. λ getApi()., λ listSessions().*)
(*   λ name(x).     function over input — params appear only where the   *)
(*                  body references them. E.g. λ recall(q, n)..          *)
(* Body uses | for alternatives, > for preference, → for implication.   *)

lambda_decl      = "λ" , identifier , [ "(" , [ param_list ] , ")" ] , "."
                 , lambda_body ;

param_list       = identifier , { "," , identifier } ;

lambda_body      = expression , { newline , continuation } ;

continuation     = "|" , expression                    (* alternative *)
                 | where_block ;                       (* binding *)

where_block      = "where" , binding , { newline , binding } ;

binding          = identifier , "≡" , expression ;


(* ─── State Machine ──────────────────────────────────────────────── *)
(* States and transitions define topology — the shape of computation. *)
(* Transitions fire on signals from the work, not user selection.     *)
(* Parallel state machines run simultaneously (e.g., operation +      *)
(* mindset) with derived outputs from their intersection.             *)

state_decl       = "state" , keyword , [ comment_inline ] ;

transition       = "→" , keyword , "when" , identifier ;


(* ─── Lookup Table (Derived Values) ───────────────────────────────── *)
(* Maps from state combinations to derived outputs.                    *)
(* Patterns support wildcards (*) and defaults (_).                    *)

lookup_table     = "λ" , identifier , "(" , param_list , ")" , "."
                 , newline
                 , { lookup_entry , newline } ;

lookup_entry     = "(" , pattern , "," , pattern , ")"
                 , "→" , value_expr ;

pattern          = keyword | identifier | "*" | "_" ;

value_expr       = identifier , { "+" , identifier }   (* blended values *)
                 | identifier ;


(* ─── Emission Contract ──────────────────────────────────────────── *)
(* Defines output schema per state. The notation defines output structure — the model fills the schema. *)
(* _ marks slots to be filled. Nested shapes are valid.               *)

emission_decl    = "λ" , identifier , "(" , param_list , ")" , "."
                 , newline
                 , { emission_entry , newline } ;

emission_entry   = keyword , "→" , edn_shape ;

edn_shape        = "{" , { keyword , value_placeholder } , "}"
                 | "[" , { value_placeholder } , "]" ;

value_placeholder = "_" | edn_shape ;


(* ─── Expressions ────────────────────────────────────────────────── *)
(* The core expression language. Operators encode relationships:       *)
(*   →  implies / leads to / produces                                 *)
(*   |  alternative (OR)                                              *)
(*   >  preferred over (soft constraint)                              *)
(*   ≡  equivalent / defined as                                       *)
(*   ¬  negation (NOT)                                                *)
(*   ∧  conjunction (AND)                                             *)
(*   ∨  disjunction (OR, logical)                                     *)

expression       = term , { expr_op , term } ;

term             = function_call
                 | negation
                 | keyword
                 | identifier
                 | string
                 | number
                 | "(" , expression , ")" ;

function_call    = identifier , "(" , [ arg_list ] , ")" ;

arg_list         = arg , { "," , arg } ;

arg              = expression | path ;

negation         = "¬" , term ;

expr_op          = "→"                                 (* implies *)
                 | "|"                                 (* alternative *)
                 | ">"                                 (* preferred over *)
                 | "≫"                                 (* strongly preferred *)
                 | "∧"                                 (* and *)
                 | "∨"                                 (* or *)
                 | "≡"                                 (* equivalent *)
                 | "≢"                                 (* not equivalent *)
                 | "∥"                                 (* parallel *)
                 | "⊗"                                 (* tensor product *)
                 | "∘"                                 (* compose *) ;


(* ─── Gate Triggers (Prose Tokens) ────────────────────────────────── *)
(* These tokens live OUTSIDE the formal grammar — they are prose       *)
(* triggers that activate RLHF-trained gates in the model. They must   *)
(* remain as prose tokens because they need high salience through the   *)
(* model's internal compression. Logprob-validated on qwen35-a3b.     *)
(*                                                                     *)
(* Gate 2 (Target):   "state_machine" — boosts EDN output 77→95%      *)
(* Gate 3 (Emission): "Return EDN only" — boosts continuation 50→97%  *)
(*                    "Fill in" — triggers template-fill behavior      *)
(*                                                                     *)
(* These appear in diagnostic lambdas as inline prose:                 *)
(*   λ diagnostic(x). state_machine | Fill in EDN template.            *)
(*                    Return EDN only. ¬prose ¬:: ¬fences              *)
(*                                                                     *)
(* Structural constraints (¬prose, ¬fences) compile to lambda.         *)
(* Behavioral triggers (Fill in, Return EDN only) must stay as prose.  *)


(* ─── EDN Structural Notation (Direct Behavioral Layer) ───────────── *)
(* EDN maps shape model behavior directly — no lambda compilation      *)
(* needed. This is the structural layer below the lambda formal        *)
(* notation.                                                           *)
(*                                                                     *)
(* Example cognitive configuration:                                    *)
(*   {:statechart/id :analyst                                          *)
(*    :mode :structured-reasoning                                      *)
(*    :states [:observe :orient :decide :act]                          *)
(*    :constraints [:evidence-first :quantify-confidence]              *)
(*    :output :edn}                                                    *)
(*                                                                     *)
(* EDN grammar is defined by Clojure's EDN spec and is not repeated   *)
(* here. Lambda compiles down to EDN. EDN shapes behavior directly.   *)


(* ─── Primitives ──────────────────────────────────────────────────── *)

keyword          = ":" , identifier ;

path             = path_segment , { "/" , path_segment } , [ "/" ] ;

path_segment     = letter , { letter | digit | "_" | "-" | "." } ;

identifier       = letter , { letter | digit | "_" | "-" } ;

comment          = ";;" , { any_char } , newline ;

comment_inline   = ";;" , { any_char } ;

string           = '"' , { any_char - '"' } , '"' ;

number           = [ "-" ] , digit , { digit } , [ "." , digit , { digit } ] ;

letter           = "a" | ... | "z" | "A" | ... | "Z" ;

digit            = "0" | ... | "9" ;

whitespace       = " " | "\t" ;

newline          = "\n" ;

any_char         = ? any unicode character ? ;
```

## Reading the Grammar

| EBNF Notation | Meaning | Example |
|---|---|---|
| `=` | "is defined as" | `state_decl = "state" , keyword ;` |
| `,` | "followed by" | `"λ" , identifier , "("` |
| `\|` | "or" | `keyword \| identifier \| "*"` |
| `{ }` | zero or more repetitions | `{ newline , continuation }` |
| `[ ]` | optional | `[ comment_inline ]` |
| `"..."` | literal text | `"state"` , `"→"` , `"when"` |
| `;` | end of rule | |
| `(* *)` | comment | |

## Example Parse

An adaptive-persona fragment parsed through this grammar:

```
λ persona(task).                          ← lambda_decl: name=persona, params=[task]

  state :thinking                         ← state_decl: keyword=:thinking
    → :coding when code_needed            ← transition: target=:coding, guard=code_needed
    → :debugging when error_encountered   ← transition: target=:debugging, guard=error_encountered

  λ archetype(op, mind).                  ← lookup_table: name=archetype, params=[op, mind]
    (debugging, analyse) → Investigator   ← lookup_entry: pattern, value
    (*, balanced) → Facilitator           ← lookup_entry: wildcard pattern

  λ emit(op).                             ← emission_decl: name=emit, params=[op]
    :thinking → {:analysis _ :options [_] :recommendation _}
                                          ← emission_entry: keyword → edn_shape
```

## The Notation Layers

```
EDN          = structural notation  (cognitive configurations, direct behavioral shape)
Lambda       = formal notation      (this grammar — compiles to structural notation)
Prose        = natural language     (instruction following, RLHF layer)
```

Lambda compiles down to EDN. EDN shapes model behavior through structure —
the model recognizes the data shape and follows it. Prose targets the
instruction-following layer. Each notation targets a different substrate
in the model, producing semantically equivalent but not identical behavior
across runs.

## Cross-Model Universality

This grammar defines notation that compiles identically across architectures:

- qwen35-35b-a3b (3B active, Gated DeltaNet hybrid, local, $0)
- qwen3-vl-235b (22B active, MoE, local, $0)
- claude-haiku-4-5 (Anthropic API, pure attention)
- gpt-5.1-codex (OpenAI API)
- claude-sonnet-4-6 (Anthropic API)

Same lambda notation → same gates → same cognitive substrate → semantically equivalent structured output.
The universality comes from math benchmark training convergence, not design.

## Part of Nucleus

This grammar is part of the [Nucleus](https://github.com/michaelwhitford/nucleus)
framework — a cognitive system that guides AI behavior.

- [LAMBDA-COMPILER.md](LAMBDA-COMPILER.md) — Compile, decompile, and safe-compile prompts to lambda expressions
- [COMPILER.md](COMPILER.md) — Compile, decompile, and safe-compile prompts to EDN statecharts
- [DEBUGGER.md](DEBUGGER.md) — Diagnose, safe-diagnose, and compare prompts
- [README.md](README.md) — Framework overview and symbol reference
rk overview and symbol reference
