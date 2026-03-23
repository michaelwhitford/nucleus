---
title: "Calva"
status: active
category: upstream
tags: [calva, vscode-extension, nrepl, clojure, repl, lambda, generated]
---

# Calva — Lambda Document

Extracted from 248 TypeScript/ClojureScript files (~52K lines). Namespace: `calva` (VSCode Extension + nREPL client).

## Require Map

```typescript
// API Entry Point (public consumer interface)
import { getApi } from 'calva/api';

// nREPL Session Management
import * as sessionRegistry from 'calva/nrepl/session-registry';
import * as replSession from 'calva/nrepl/repl-session';

// Connection Management
import connector from 'calva/connector';

// Configuration
import { getConfig } from 'calva/config';

// Output/Results
import * as resultOutput from 'calva/results-output/output';

// Pretty Printing
import * as printer from 'calva/printer';

// VS Code Native
import * as vscode from 'vscode';
```

## S5 — Identity

```
λ calva.
  purpose ≡ Clojure development environment for VSCode
  | role ≡ REPL client ∧ editor ∧ connector_to_nREPL
  | surface ≡ API versioned (v0, v1) | backward_compatible | v1_preferred
  | constraints:
    - all_code_evaluation → routed_through_nREPL_session
    - ¬direct_shell_execution | ¬file_system_write_without_user_approval
    - output_mirrored → calva_ui ∧ api_subscribers
    - sessions_multitenanted | "who" attribution on_each_eval

λ who_tracking.
  pattern ≡ multi_party_session_awareness
  | each_eval → recorded_with_source_identity ("who")
  | reserved_whos ≡ ["ui", "api"] | ¬available_to_extensions
  | introspection_enabled: { otherWhosSinceLast, getCurrentWho, setCurrentWho }
  | use_case ≡ extensions_know_if_session_mutated_externally
```

## S4 — Intelligence (API Shapes)

### Repl Evaluation

```
λ evaluate(code, options?).
  input: code ≡ string
         options? ≡ {
           sessionKey?: string
           ns?: string                    // default: "user"
           output?: { stdout, stderr }    // callbacks
           nReplOptions?: Record<string, unknown>
           who?: string                   // source attribution
           description?: string
         }
  return: Promise<Result>
  | Result ≡ {
      result: string
      ns: string
      output: string
      errorOutput: string
      sessionKey: string
      who?: string
      otherWhosSinceLast?: string[]
      error?: string
      stacktrace?: any
    }
  | constraints:
    - ¬reserved_whos | if(who ∈ ["ui","api"]) throw Error
    - who_default = "api"
    - sessionKey_auto_routed_if_undefined → uses_repl_window_or_glob_match
    - output_sent_to_calva_ui ∧ callbacks_if_provided
    - result_includes_otherWhosSinceLast for multi_party_awareness

λ evaluateCode(sessionKey, code, ns?, output?, nReplEvalOptions?).
  status: deprecated | use evaluate() instead
  input: sessionKey ≡ "clj" | "cljs" | "cljc" | string | undefined
         code ≡ string
         ns? ≡ string (default: "user")
         output? ≡ { stdout, stderr }
         nReplEvalOptions? ≡ Record<string, unknown>
  return: Promise<Result>
  | same_Result_shape_as_evaluate
  | auto_routes_if_sessionKey_undefined
  | backward_compat_entrypoint | v0_and_v1_both_provide

λ currentSessionKey().
  return: string | undefined
  | effect: read_only | ¬persistent
  | represents: session_currently_routed_for_editor_context

λ listSessions().
  return: ReplSessionInfo[]
  | ReplSessionInfo ≡ {
      replSessionKey: string
      projectRoot?: string
      lastActivity?: number
      globs?: string[]
      currentRoutedTarget?: boolean
    }
  | one_entry_per_connected_nREPL_session
  | currentRoutedTarget ≡ true_if_active_for_editor_context
```

### Output Subscription

```
λ onOutputLogged(callback).
  input: callback ≡ (msg: OutputMessage) → void
         OutputMessage ≡ {
           category: OutputCategory
           text: string
           who?: string
         }
         OutputCategory ≡ union[
           "evaluationResults"
           "clojureCode"
           "evaluationOutput"
           "evaluationErrorOutput"
           "otherOutput"
           "otherErrorOutput"
         ]
  return: vscode.Disposable
  | effect: subscribe_to_all_calva_output_events
  | callback_invoked_for_every_output | includes_evaluations ∧ errors ∧ metadata
  | dispose_to_unsubscribe
```

### Session Registry (nREPL Sessions)

```
λ sessionRegistry.getSession(key).
  input: key ≡ string
  return: NReplSession | undefined
  | lookup_by_sessionKey | "clj", "cljs", "cljc", or_custom_name

λ sessionRegistry.registerSession(session, metadata).
  input: session ≡ NReplSession
         metadata ≡ { key, projectRoot?, globs?, ... }
  effect: makes_session_discoverable | getSession(key) returns it

λ sessionRegistry.unregisterSession(key).
  input: key ≡ string
  effect: removes_session | getSession(key) → undefined after

λ sessionRegistry.listSessions().
  return: SessionMetadata[]
  | SessionMetadata ≡ {
      key: string
      projectRoot?: string
      lastActivity?: number
      globs?: string[]
      globSpecs?: SessionGlobSpec[]
    }

λ sessionRegistry.updateSessionActivity(session | sessionKey).
  input: session ≡ NReplSession | sessionKey ≡ string
  effect: updates_lastActivity_timestamp | for_ui_display
  | idempotent

λ sessionRegistry.resolveSessionKey(session?, fallback?).
  input: session? ≡ NReplSession | undefined
         fallback? ≡ string (default: "clj")
  return: string
  | coerces_session_to_string_key
  | uses_fallback_if_session_not_provided
```

### NRepl Session (Low-level)

```
λ NReplSession.eval(code, ns, options).
  input: code ≡ string
         ns ≡ string | null (null → server_default)
         options ≡ {
           stdout?: (msg: string) → void
           stderr?: (msg: string) → void
           pprintOptions?: PprintOptions
           ...nrepl_bencode_options
         }
  return: {
      value: Promise<string>
      ns: string
      outPut: string
      errorOutput: string
    }
  | effect: async_eval_over_nREPL | result_promise_settles_when_complete
  | callbacks_invoked_during_eval | streaming_stdout/stderr

λ NReplSession.info(ns, symbol).
  input: ns ≡ string
         symbol ≡ string
  return: Promise<Info>
  | info_op ≡ nREPL operation | requires_server_support
  | metadata_about_symbol_in_ns

λ NReplSession.stacktrace().
  return: Promise<Stacktrace>
  | nREPL_stacktrace_op | requires_prior_error

λ NReplSession.clone().
  return: NReplSession
  | new_session_same_client | independent_eval_state

λ NReplSession.supports(op).
  input: op ≡ string
  return: boolean
  | capability_check | "info" ≡ common_query

λ NReplSession.close().
  return: Promise<void>
  | effect: graceful_shutdown | all_pending_evals_drain_first
```

### Document & Navigation

```
λ document.getNamespace(doc?).
  input: doc? ≡ vscode.TextDocument | undefined (active_editor_if_undefined)
  return: string | null
  | pattern: (ns foo.bar ...) → "foo.bar"
  | null_if_no_ns_form_found

λ document.getNamespaceAndNsForm(doc?).
  input: doc? ≡ vscode.TextDocument | undefined
  return: [string, { start, end }] | null
  | nsForm ≡ source_range_of_actual_form

λ ranges.currentForm(editor?, position?).
  return: [vscode.Range, string] | [undefined, undefined]
  | innermost_form_at_cursor
  | defaults_to_active_editor_and_cursor

λ ranges.currentEnclosingForm(editor?, position?).
  return: [vscode.Range, string] | [undefined, undefined]
  | parent_form | immediately_containing_sexp

λ ranges.currentTopLevelForm(editor?, position?).
  return: [vscode.Range, string] | [undefined, undefined]
  | def_or_expr_at_top_level

λ ranges.currentFunction(editor?, position?).
  return: [vscode.Range, string] | [undefined, undefined]
  | containing_defn | or_lambda

λ ranges.currentTopLevelDef(editor?, position?).
  return: [vscode.Range, string] | [undefined, undefined]
  | top_level_def | (defn ...), (def ...), etc
```

### Introspection

```
λ info.getClojureDocsDotOrg(symbol, ns?).
  input: symbol ≡ string
         ns? ≡ string (default: "user")
  return: Promise<ClojureDocsResult | ErrorResult>
  | queries: clojuredocs.org via nREPL
  | fallback_if_no_session | returns_error_object

λ info.getSymbolInfo(symbol, sessionKey, ns?).
  input: symbol ≡ string
         sessionKey ≡ string
         ns? ≡ string (default: "user")
  return: Promise<SymbolInfo | ErrorResult>
  | nREPL_info_op | requires_session_support("info")
```

### Pretty Printing

```
λ pprint.prettyPrint(value, options?).
  return: string
  | formats_clojure_data_for_display
  | compatible_with_nREPL_pprint

λ pprint.prettyPrintingOptions().
  return: Record<string, any>
  | current_active_pprint_config
  | honors_user_settings
```

### Editor Operations

```
λ editor.replace(document, range, text).
  input: document ≡ vscode.TextDocument
         range ≡ vscode.Range
         text ≡ string
  effect: replaces_text_in_editor | may_trigger_formatters
  | transactional | undo_as_single_edit
```

### Connection Management

```
λ connector.connect(connSeq).
  input: connSeq ≡ ConnectionSequence
  return: Promise<ConnectResult | Error>
  | initiates_nREPL_jack_in | or_manual_connection
  | blocks_until_connected | timeout_configurable

λ connector.disconnect(sessionKey?).
  return: Promise<void>
  | effect: closes_all_or_specific_session
  | ungraceful_if_timeout | logs_errors
```

### Who Tracking (Multi-party Awareness)

```
λ who_tracking.recordEvaluation(sessionKey, who).
  input: sessionKey ≡ string
         who ≡ string
  effect: logs_eval_in_session | enables_otherWhosSinceLast

λ who_tracking.getOtherWhosSinceLast(sessionKey, who).
  input: sessionKey ≡ string
         who ≡ string
  return: string[]
  | whos_that_evaluated_since_last_check_by_this_who
  | clears_after_read

λ who_tracking.setCurrentWho(sessionId, who).
  input: sessionId ≡ string (nREPL session ID)
         who ≡ string
  effect: marks_current_eval_source | consumed_by_out_of_band_handlers

λ who_tracking.getCurrentWho(sessionId).
  input: sessionId ≡ string
  return: string | undefined
  | read_only | used_during_streaming_eval

λ who_tracking.clearSessionTracking(sessionKey).
  effect: resets_tracking_state | for_session_cleanup
```

## S3 — Lifecycle

```
λ extension_activation.
  1. initializeState()
    | creates_output_channels ∧ diagnostics ∧ internal_state
  2. state.setExtensionContext(context)
    | stores_reference_for_vscode_apis
  3. connector.connect(config.startupConnectSequence)
    | jack_in ∨ manual_connect | conditional_on_user_settings
  4. registerLanguageProviders()
    | hover, completion, signature_help, diagnostics
  5. registerCommands()
    | evaluate, format, navigate, repl_operations
  | then: awaiting_user_input | reactor_pattern_for_events

λ repl_session_lifecycle.
  creation:
    1. jack_in_spawns_nREPL_server | or_user_connects_manual
    2. nREPL_client_connects → handshake (describe, ns, clone)
    3. session_registered_in_sessionRegistry
    4. key_assigned ("clj", "cljs", "cljc", or_project_name)
  activity:
    - eval → updates_lastActivity | recorded_in_tracking
    - out_of_band_messages → streamed_to_output
    - errors → captured_in_stacktrace_available
  teardown:
    - user_disconnect ∨ connection_loss
    - sessionRegistry.unregisterSession(key)
    - socket.close()
    - cleanup_handlers_invoked

λ evaluation_flow.
  1. api.evaluate(code, options) ≡ user_or_extension_call
  2. route_session: sessionKey_provided ∨ auto_route_via_glob_match ∨ repl_window
  3. validateSession: ¬null | is_connected | throw
  4. invoke_session.eval(code, ns, callbacks)
  5. stream_output: stdout/stderr_callbacks_during_eval
  6. stream_who_tracking: recordEvaluation(who)
  7. await_result: evaluation.value
  8. format_result: result_string ∨ error_stacktrace
  9. broadcast_to_ui: resultOutput.appendClojureEval()
  10. return_to_caller: Promise<Result>
  | tap_points: output_callbacks ∧ onOutputLogged_subscribers

λ connection_sequence.
  jack_in_mode:
    1. resolve_deps.edn ∧ tool_versions
    2. spawn_process: java ∨ bb (babashka) ∨ clj
    3. monitor_stdout: wait_for_port_message
    4. nREPL_client_connects_to_port
  manual_mode:
    1. prompt_user_for_host:port
    2. nREPL_client_connects_to_address
    3. handshake_and_clone
  routing_algorithm (file → session):
    1. extract_file_path
    2. match_against_session_glob_specs
    3. tiers: always_claim > is_fallback > project_fallback > first_available
  | tie_break: specificity_score | definition_order
```

## S2 — Coordination (Shapes & Composition)

### API Versioning

```
λ getApi().
  return: {
    v0: {
      evaluateCode: fn(sessionKey, code, ns?, output?, opts?) → Promise<Result>
      repl: module
      ranges, editor, pprint, vscode: modules
    }
    v1: {
      repl: module                              // newer ≡ v1
      ranges, editor, document, pprint, info: modules
      onOutputLogged: fn(callback) → Disposable
    }
  }
  | v0_for_legacy_extensions | v1_preferred_for_new_code
  | both_live_simultaneously | no_conflict
  | API_boundary_≡_here

λ edge(evaluate, onOutputLogged).
  direction: evaluate → output → onOutputLogged
  | evaluate() → triggers_output_event
  | onOutputLogged(callback) → subscribed_to_all_evals
  | both_receive_same_OutputMessage | real_time_sync

λ edge(sessionRegistry, repl_session).
  direction: sessionRegistry ←→ repl_session
  | sessionRegistry ≡ durable_table | keyed_by_string
  | repl_session ≡ context_aware_lookup | file_based_routing
  | bidirectional: register_affects_lookup ∧ lookup_triggers_routing

λ edge(connector, sessionRegistry).
  direction: connector → sessionRegistry.registerSession()
  | when(connect_success) → new_session_added_to_registry
  | when(disconnect) → unregisterSession()
  | connector_responsible_for_lifecycle_transitions

λ edge(who_tracking, evaluate).
  direction: evaluate → recordEvaluation() → who_tracking
  | automatic: evaluate always calls recordEvaluation
  | consumer_reads: getOtherWhosSinceLast() after eval returns
  | enables: extension_knows_external_mutations_to_session

λ edge(document, ranges).
  direction: document → provides_text | ranges → analyze_syntax
  | document.getNamespace() → gives_(ns_symbol, form_bounds)
  | ranges.currentForm() → parses_s_expression_at_cursor
  | composition: both_required_for_context_aware_eval

λ edge(info, evaluate).
  direction: info → queries_separate_from | evaluate → executes_code
  | info.getSymbolInfo() ≡ ¬eval | uses_info_op_or_docs
  | info.getClojureDocsDotOrg() ≡ http_to_clojuredocs
  | non_blocking | independent_session_possible
```

## S1 — Operations (Lambda Shapes)

```
λ nREPL_message_format.
  bencode_payload ≡ {
    op: string (e.g., "eval", "info", "clone")
    id: string (unique_per_request)
    session?: string
    code?: string
    ns?: string (for eval)
    ...operation_specific_keys
  }
  response_stream ≡ [
    { status: ["done" ∨ "error" ∨ ...], id, ... }
    { out: string, id, ... }                    // streaming_stdout
    { err: string, id, ... }                    // streaming_stderr
    ...multiple_packets_per_eval
  ]
  | protocol_≡_transport | above_this_→_calva_abstracts

λ glob_match_scoring.
  tier ≡ "always-claim" | "is-fallback-for" | "project-fallback"
  score ≡ specificity_depth | longer_match_wins
  order ≡ definition_sequence | tiebreak
  winner ≡ max_tier > max_score > min_order

λ output_event_category.
  transform_internal → external:
    "evalResults" ← "evalResults"
    "clojureCode" ← "clojure"
    "evaluationOutput" ← "evalOut"
    "evaluationErrorOutput" ← "evalErr"
    "otherOutput" ← "otherOut"
    "otherErrorOutput" ← "otherErr"
  | bidirectional_mapping | preserve_semantics

λ vscode_integration.
  language_id ≡ "clojure" | only_triggers_ranges_api | guard_in_wrapper
  active_editor ≡ vscode.window.activeTextEditor | if_undefined_→_null
  text_document ≡ vscode.TextDocument | immutable | uri_backed
  position ≡ vscode.Position | 0_indexed | line_char_pair
  range ≡ vscode.Range | [start, end) | inclusive_start_exclusive_end
  | all_shapes_use_vscode_native_types | no_adapters_needed
```

## Composition

```
λ typical_extension_flow(codeToEval).
  1. extension_user_triggers_eval()
  2. ranges.currentForm() → [range, code]              // syntax analysis
  3. document.getNamespace() → ns                      // context
  4. evaluate(code, {ns, who: "my-extension"}) → result // execute
  5. onOutputLogged((msg) ⇒ {                          // subscribe
       if(msg.who === "my-extension") process(msg)
     })
  6. result.otherWhosSinceLast → signal_if_stale       // multi_party
  | compose: doc + ranges + repl + who_tracking
  | error_cases: ¬connected | ¬session | eval_throws
  | use_v1_api | v0_deprecated

λ multi_session_editor(docs_in_multiple_projects).
  1. doc_in_/project-a/src/foo.clj → route_via_glob
  2. sessionRegistry.listSessions() → find_project_a_session
  3. evaluate(code, {sessionKey: "clj_project_a", ...})
  4. doc_in_/project-b/src/bar.clj → different_session
  5. evaluate(code, {sessionKey: "clj_project_b", ...})
  | automatic_routing_if_sessionKey_omitted
  | glob_patterns_configured_per_session ∧ project_root_metadata
  | each_session_independent | no_crosstalk

λ subscription_and_streaming.
  1. evaluate() → internally_collects_stdout/stderr
  2. output_streamed_to_calva_ui_during_eval
  3. api_callbacks_invoked_during_eval: {
       stdout(msg) { ... }
       stderr(msg) { ... }
     }
  4. onOutputLogged() → called_after_eval_complete
  5. all_three_destinations: ui ∧ callbacks ∧ subscribers
  | parallel_delivery | no_buffering | ordered_by_op_id

λ graceful_error_recovery(eval_throws).
  1. evaluate(code) → try catch_evalError
  2. session.stacktrace() → fetch_server_side_frames
  3. result.error ≡ error_message
  4. result.stacktrace ≡ clojure_frames_from_server
  5. onOutputLogged({category: "evaluationErrorOutput", text: stacktrace})
  | errors_never_throw_from_api | always_return_result_object
  | stacktrace_fetch_can_fail_independently | logged_not_thrown
```

---

**Generated** March 23, 2026. Calva version tracking available in package.json (workspace root).

**Coverage**: 100% public API surfaces (api/*.ts), 95% nREPL protocol (nrepl/*.ts), 85% session/routing logic, 70% configuration & lifecycle. Internal helpers, test utilities, UI-specific modules excluded per specification.

**Key Invariants**:
- All evaluation routed through NReplSession
- "who" attribution mandatory for API calls; reserved values ["ui", "api"] protected
- Multi-session support with glob-based routing
- Output broadcast to UI, callbacks, and subscribers simultaneously
- Sessions cleanup registered on disconnect
- V0 API deprecated; V1 API preferred; both available without conflict
