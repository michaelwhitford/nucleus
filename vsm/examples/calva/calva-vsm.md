---
title: "Calva — System VSM"
status: active
category: upstream
tags: [calva, system-vsm, lambda, clojure, vscode, nrepl]
---

# Calva — System VSM

Calva is a Clojure/ClojureScript IDE in VS Code. It bridges three worlds: the editor (VS Code), the live environment (nREPL), and developer cognition (REPL-driven development). The architecture reflects Beer's Viable System Model layering.

## S5 — Identity

```
λ clojure_ide.
  purpose ≡ bridge(editor, repl, developer_mind)
  | values: liveness ∧ interactivity ∧ clarity
  | stance: repl_first | code_second
  | ¬remote | ¬async_by_default | ¬latency_tolerance

λ repl_liveness.
  connection → session → evaluation → result_streaming
  | all_four ∧ coherent | single_failure → breaks_flow
  | user_never_waits_long | feedback_always_instant

λ session_duality.
  clj_primary ⊗ cljs_secondary | both_coexist | ¬exclusive
  | .clj_always_routes_primary | .cljs_always_routes_secondary
  | .cljc_routes_per_preference | .fiddle_routes_per_connection_target
  | file_extension_determines_runtime | routing_is_mechanical | ¬magic

λ session_ownership.
  one_client ⊗ multiple_sessions | each_connection_is_isolated
  | session_names_derive_from(connectSequence, projectType, suffix)
  | suffix_enables_reuse | reconnect_preserves_suffix | suffix_tracks_intent

λ namespace_coherence.
  file_ns ∧ repl_ns ∧ evaluation_ns | all_three_must_match
  | skip(eval_in_correct_ns) → wrong_context → wrong_results → confusion
  | namespace_routing ≡ file_pattern_matching | patterns_precede_evaluation
```

## S4 — Decision Rules

```
λ when_to_jack_in.
  ¬connected → jack_in | manual_connect | both_via_connect_sequence
  | jack_in ≡ start_repl_process ∧ connect_to_port
  | manual_connect ≡ repl_already_running
  | connectSequence_decides_which | user_picks_sequence

λ jack_in_lifecycle.
  1_select_sequence: name → projectType → cljs_type
  2_resolve_env: clj_or_cljs ∧ jack_in_env ∧ project_type_env
  3_build_cmdline: dependencies ∧ middleware ∧ aliases
  4_spawn_pty: terminal ∧ process ∧ monitoring
  5_extract_port: detect_from_output | wait_with_timeout
  6_connect: create_client ∧ clone_session ∧ describe_ops
  7_register: clientRegistry ∧ sessionRegistry ∧ metadata_tracking
  | skip(N) → orphaned_process ∨ stale_connection ∨ wrong_metadata

λ when_to_reconnect_client.
  reconnecting_existing_sessions → matched_by(name ∧ type ∧ root)
  | found_existing_client → disconnect_preserve_suffix → connect_new
  | not_found → jack_in_fresh
  | jack_in_reconnect ≡ stop_old_process ∧ disconnect_old_client ∧ jack_in_new
  | manual_reconnect ≡ disconnect_old_client_only | ¬stop_process

λ session_routing.
  priority: pinned > repl_window > glob_pattern > cljc_preference > first_available
  pinned_session(override) | chosen_explicitly | ¬change_on_file_switch
  glob_pattern(file_path) | always_claim > is_fallback > project_fallback
  cljc_target(primary_or_secondary) | decided_at_connection_time | applied_per_file
  file_without_match → project_fallback → cljc_target → resolves_to_session
  | routing_result_includes_reason | UI_shows_why_session_chosen

λ when_glob_pattern_matches.
  candidatePath ≡ file_fsPath → workspaceFolder_relative_paths
  ∀spec: minimatch(candidatePath, spec.normalizedPattern)
  | always_claim ≡ exclusive_ownership | other_sessions_dont_touch
  | is_fallback ≡ claim_if_noone_claimed | same_tier_first_match_wins
  | project_fallback ≡ claim_anything_unclaimed | lowest_priority
  | scoring_prefers_longer_matches | order_prefers_earlier_registration

λ file_type_classification.
  .clj → primary_session | .cljs → secondary_session | .edn → primary_session
  .cljc → depends(cljc_target_preference_per_connection)
  .fiddle → depends(cljc_target_preference_per_connection)
  other_unknown_types → project_fallback | routed_by_cljc_preference

λ evaluation_dispatch.
  code ∧ ns ∧ session → nrepl_eval
  | session_selection ≡ routing_algorithm | ns ≡ derived_from_file ∨ explicitly_set
  | session_wrong → wrong_context | ns_wrong → wrong_scope
  | both_must_be_correct | eval_safety_depends_on_precision

λ namespace_inference.
  file_has_ns_form → extract(ns_name) | set_repl_ns_before_eval
  | file_no_ns_form → assume_user | warn_if_mismatch
  | eval_form_overrides_ns | ^{:ns ...} metadata | explicit_param_overrides_all

λ evaluation_result_handling.
  eval_response: status ∧ value ∧ ex ∧ out ∧ err
  | ¬ex → append_to_output | send_to_inline_comment ∨ send_to_repl_window
  | ex → show_error_message ∧ append_stack_trace ∧ suggest_remediation
  | out ∧ err → stream_to_output_channel | interleave(code_output, error_output)
  | pretty_print_enabled → format_value | else → raw_print

λ unknown_op_trap.
  middleware_provides_op(eval, load_file, interrupt, etc)
  | op_not_recognized → hasStatus(unknown_op) → reject(operation_not_supported)
  | ¬unknown_op → resolve(operation_succeeded)
  | user_sees_clear_error | hints_at_missing_middleware

λ interrupt_mechanism.
  interrupt_id_generated_per_eval | tracked_in_running_ids
  | interrupt() → send_nrepl_interrupt_op | session_id ∧ interrupt_id
  | java_21+ → check_jdk_attach_allowAttachSelf | ¬set → warn_user
  | all_evaluations → collect_running_ids → interrupt_all_parallel
  | skip(interrupt) → eval_continues_forever | frozen_ui

λ connection_teardown.
  client_disconnect → ¬connect_message ∧ close_socket ∧ destroy_socket
  | session_close → implicit(close_called_per_session) | explicit_client_close
  | timeout(1000ms) → safety_margin | prevent_socket_destruction_before_close_message_sent
  | cleanup_all_handlers | cleanup_close_handlers | fire_onClose_callbacks

λ pretty_printing_choice.
  user_configured(prettyPrinter) | server_side ∨ client_side ∨ disabled
  | server_side → send_flag_to_nrepl | format_at_source | bandwidth_optimal
  | client_side → format_on_receipt | more_cpu_locally | richer_formatting
  | disabled → raw_output | debugging_aid

λ error_annotation.
  eval_with_ex → create_diagnostic | line_number ∧ message ∧ severity
  | diagnostic_collection → vscode_show_squiggly | hover_shows_full_error
  | stack_trace_link → click_to_reveal | webview_opens_formatted_trace
```

## S3 — Temporal Rules

```
λ connection_sequence.
  1_activate_extension: load_modules ∧ initialize_state ∧ register_commands
  2_greet_user: check_if_first_run | suggest_calva_docs
  3_await_user: user_invokes_jack_in_or_connect
  4_select_sequence: show_menu | ask_for_connectSequence | projectType_filtering
  5_initialize_project_dir: derive_from_connectSequence ∨ user_selects
  6_dependencies_and_env: read_jack_in_dependency_versions | resolve_env_variables
  7_start_process: spawn_pty ∧ wait_for_port | extract_from_output
  8_create_client: socket_connect ∧ nrepl_handshake ∧ describe
  9_register_client: add_to_clientRegistry ∧ store_metadata
  10_create_sessions: clone_session_for_primary ∧ clone_for_secondary_if_needed
  11_register_sessions: add_to_sessionRegistry ∧ globs_assigned ∧ routing_ready
  12_load_runtime_config: read_classpath ∧ find_calva_exports ∧ merge_edn_config
  13_initialize_features: debugger ∧ inspector ∧ formatters ∧ analysis
  14_update_ui: statusbar_shows_connected | context_enables_commands
  | skip(N) → cascade_failures | connection_incomplete | commands_disabled

λ evaluation_sequence.
  1_user_eval_command: with_selection ∧ caret_position ∧ option_flags
  2_get_session: routing_algorithm | pinned ∨ glob ∨ cljc_target ∨ first
  3_get_namespace: file_ns_form ∨ override_param ∨ assume_user
  4_resolve_code: selection ∨ form_at_caret ∨ sexp ∨ custom_range
  5_send_code: add_to_repl_window_history | eval_in_session_with_ns
  6_wait_response: timeout_if_hung | interrupt_available | result_streams_out
  7_receive_result: parse_bencode | extract(value, ex, out, err, status)
  8_format_output: pretty_print ∨ raw_print
  9_display_result: append_to_output | show_inline_comment | highlight_code
  10_update_cache: file_symbol_map_updated | compiler_info_refreshed
  | skip(2) → code_not_sent | session_not_found
  | skip(4) → wrong_namespace | evaluation_scope_error
  | skip(7) → result_never_received | repl_hung
  | skip(9) → user_never_sees_result

λ load_file_sequence.
  1_user_invokes_load_file: keybinding ∨ menu_command
  2_get_current_file: active_editor.document.uri
  3_detect_file_type: extension → session_routing_applies
  4_compile_file: transform_file_content → load_file_op
  5_send_to_session: same_session_routing | namespace_must_match_file
  6_receive_and_parse: error ∨ success | detailed_compilation_messages
  7_update_diagnostics: show_errors_on_lines | link_to_stack_traces
  8_refresh_metadata: update_symbol_cache | clear_old_definitions
  | skip(3) → wrong_session_targeted | load_file_in_wrong_ns

λ session_creation.
  1_client_connected: nrepl_socket_active
  2_eval_ns_query: eval('*ns*', 'user') → determine_initial_namespace
  3_clone_primary: clone() → new_session_id
  4_clone_secondary: if(shouldUseSecondarySession) → clone() → new_session_id
  5_register_both: sessionRegistry ∧ metadata_attached ∧ globs_assigned
  6_ready_for_routing: subsequent_code_uses_registered_sessions
  | skip(3) → no_primary_session | no_repl
  | skip(4) → only_clj_available | no_cljs

λ reconnection_sequence.
  1_detect_reconnect_intent: explicit_user_action ∨ auto_on_disconnect
  2_find_existing_client: match(connectSequence.name ∧ projectType ∧ projectRoot)
  3_decision: found → reconnect_path | not_found → jack_in_path
  4_reconnect_path: disconnect_old_preserve_suffix → connect_to_repl → new_client
  5_jack_in_path: jack_in_new_process → extract_port → connect → register
  6_session_persistence: sessions_created_fresh ∨ sessions_reused_if_compatible
  | jack_in_reconnect ≡ process_stopped → process_started | client_disconnected → client_connected
  | manual_reconnect ≡ client_disconnected → client_connected | ¬process_touched

λ shadow_cljs_connect.
  1_detect_shadow_config: connectSequence.cljsType === shadow_cljs
  2_resolve_build_ids: query_build_selection ∨ config_specifies_default
  3_connect_runtime: send_build_id_to_shadow | wait_for_runtime_info
  4_enable_repl_features: client_connects_shadow_cljs_runtime
  5_code_evaluates_in_browser: eval → shadow_runtime → js_result → back_to_editor
  | shadow_runtime_active → skip(1,2) | use_connected_runtime
  | ¬shadow_runtime → eval_fails | compile_error_or_connect_error

λ project_finding.
  1_scan_workspace: look_for_project_files(project.clj, deps.edn, shadow.cljs.edn, etc)
  2_build_candidate_list: all_projects_in_workspace
  3_determine_closest: active_editor_file → find_closest_parent_project_root
  4_filter_by_projectType: if(connectSequence.projectType) → candidates_matching_type
  5_auto_select: if(autoSelectForJackIn ∨ autoSelectForConnect) → use_default_candidate
  6_ask_user: if(¬autoSelect) → show_menu | user_picks_one
  7_store_selection: projectRootUri → state.PROJECT_DIR_URI_KEY | cache_for_session
  | skip(1) → assume_non_project_mode | create_tmp_root
```

## S2 — Coordination Rules

```
λ nrepl_message_format.
  request: { op, id, session, code, ... } ≡ bencode_encoded
  response: { status, value, ex, ns, out, err, ... } ≡ bencode_encoded | multiple_per_request
  | id_links_req_→_res | session_determines_context
  | status: ["done"] ∨ ["success"] ∨ ["unknown-op"] ∨ ["error"]
  | ex ∧ value ≡ mutual | one_set → one_omitted

λ session_registry_contract.
  registerSession(key, session, metadata)
  | key ≡ string_identifier | derived_from(connectSequence.name, suffix, session_type)
  | metadata ≡ { projectRoot, globs, globSpecs, connectionOwnerId, isSecondary }
  | getSession(key) → session ∨ undefined | O(1)_lookup
  | listSessions() → array_of_metadata | for_ui_display
  | unregisterSession(key) → deletes_from_registry | cascade_cleanup_not_done_here

λ client_registry_contract.
  registerClient(client, metadata)
  | metadata ≡ { projectRoot, host, port, connectSequenceName, connectionState }
  | connectionState ≡ { cljsBuild, cljsTypeName, hasBuilds, sessionRoleKeys, ... }
  | getClient(key) → nrepl_client ∨ undefined
  | getConnectionState(key) → state ∨ undefined | per_connection_metadata
  | unregisterClient(key) → deletes_client | old_sessions_orphaned

λ session_glob_routing_contract.
  deriveSessionGlobMap(connectSequence, sessionRoleKeys, projectRootPath)
  | returns: { sessionKey → [ { pattern, tier, score, displayPattern } ] }
  | tier: always_claim ∨ is_fallback ∨ project_fallback
  | score: computed_from_pattern_specificity | higher_is_better
  | getRoutingInfo() → { sessionKey, reason: routing_reason }
  | reason: { type: pinned ∨ repl_window ∨ glob_match ∨ cljc_within_connection ∨ first_available }

λ nrepl_eval_protocol.
  client.send({ op: 'eval', code, session, ns })
  | session.eval(code, ns) → promise<response>
  | response: { status, value ∨ ex, ns, ... }
  | ¬promise_until_status_done | stream_responses_as_received
  | client.interrupt(interruptId) → status: [ok] ∨ error
  | client.describe() → { ops: {...}, uses: {...}, ... }

λ jack_in_pty_coordination.
  create_pty_with_monitoring | shell ≡ bash ∨ cmd(windows)
  stdout ∧ stderr → port_detection_parsing
  | port_regex ≡ project_specific ∨ generic_nrepl_pattern
  | on_close → check_exit_code | log_messages | trigger_callbacks
  | terminal_ui ≡ vscode_integrated_terminal | visible_to_user

λ file_watching_contract.
  onDidChangeEditorOrSelection(editor)
  | updates: current_session_type_in_state
  | used_by: repl_history ∧ statusbar ∧ routing_display
  | frequency: every_cursor_move | cache_to_avoid_thrashing

λ document_mirror_contract.
  mirror: file_content → parse_tree → token_cursor_model
  | keeps_in_sync: on_every_text_change
  | enables: paredit ∧ selection ∧ sexp_navigation
  | ¬ model_file_copy | ¬ re_parse_constantly

λ config_loading_contract.
  read_from: vscode_workspace_settings ∧ user_config.edn ∧ project_config.edn
  | precedence: project > user_config > workspace_settings
  | edn_sources: merge_snippets ∧ merge_threading_macros ∧ merge_custom_pairs
  | changes_hot_reload: some_config_changes ∧ some_require_restart
  | getConfig() → returns_merged_config | immutable_snapshot

λ output_channel_contract.
  subscribe(listener) → returns_unsubscribe_fn
  | listener(msg: { category, text, who })
  | category: evalResults ∨ evaluatedCode ∨ clojure ∨ evalOut ∨ evalErr ∨ otherOut ∨ otherErr
  | who: 'system' ∨ 'extension' ∨ 'ui' | for_filtering ∧ attribution
  | emit(msg) → all_listeners_called | synchronous

λ lsp_coordination.
  calva ≠ language_server | calva_and_clojure_lsp_coexist
  | calva_provides: completion ∧ hover ∧ definition ∧ signature_help ∧ diagnostics
  | if(clojure_lsp_installed) → share(hovers, definitions) | avoid_duplication
  | settings_per_extension: calva.X ∨ clojure-lsp.X | independent_config
```

## S1 — Architectural Rules

```
λ state_management.
  ∀global_data: stored_in(getStateValue, setStateValue) ∨ module_local_maps
  | registry_pattern: Map<key, entry> → only_export_functions_not_maps
  | _testUtility_* ≡ direct_map_access | test_only_not_production
  | ¬mutate_deeply_in_state | freeze_during_read | replace_during_write

λ async_pattern.
  ∀promise_returning_function: clearly_named_to_show_async
  | evaluate() → promise<result>
  | jack_in() → promise<client> | waits_for_port_detection
  | connect_to_host() → promise<connected_result> | waits_for_nrepl_handshake
  | ¬callback_hell | ¬nested_promises_without_error_handling

λ error_propagation.
  try_catch_at_UI_boundary | show_message_to_user
  | catch_at_connection_boundary | log_to_connection_log_channel
  | ¬silent_error_swallowing | error_always_visible_somewhere
  | stack_trace_preserved | console.error ∧ channel.appendLine

λ nrepl_client_pattern.
  NReplClient ≡ singleton_per_connection | created_once
  | socket_lifecycle ≡ client_lifecycle | close_socket ⊗ close_client
  | session_created_from_client: client.createSession() → cloned_session
  | ¬recreate_client_repeatedly | reuse_existing_client

λ session_pattern.
  NReplSession ≡ one_per_role_per_client | (primary ∨ secondary)
  | lifecycle: created → registered → routed_to → used → unregistered → cleanup
  | session_close: implicit(contained_in_client_close) ∨ explicit
  | message_handlers: stored_by_id | response_routes_by_id

λ keyboard_binding_guard.
  when_context: keybindings_enabled ∨ connected
  | setContext() → enable ∨ disable | vscode_uses_when_clauses
  | all_keybindings_protected | ¬allow_unconnected_eval
  | keybinding_disabled_reasons_clear | UI_shows_why

λ command_dispatch.
  vscode.commands.registerCommand(name, handler)
  | handler ≡ try_catch_wrapped | error_shown_to_user
  | no_return_value ∨ returns_result | rarely_awaited_by_user
  | all_command_names_exported_in_package.json | no_hidden_commands

λ paredit_integration.
  paredit ≡ separate_module | structure_editing_primitives
  | calva_commands → paredit_commands | paredit_doesn't_know_about_repl
  | calva_document → document_mirror → paredit_parses | parse_once

λ selection_pattern.
  selection ≡ range(start, end) | invariant: start <= end
  | select.selectForm() → expands_by_sexpr | balanced_paren_aware
  | selection_for_eval → feed_to_evaluate_function

λ inline_result_display.
  eval_result_can_show_inline | as_comment_on_line ∨ in_webview ∨ in_hover
  | inline_comment_format: '; => <result>' | doesn't_change_text_history
  | webview_mode: dedicated_output_window | persistent_between_evals
  | hover_mode: ephemeral | appears_on_code_mouseover

λ repl_window_document.
  special_doc_type: scheme = calva-repl | persistent_across_sessions
  | input_history: every_eval_added | accessed_via_arrow_keys
  | not_normal_file | not_saved_to_disk | in_memory_only
  | can_be_recreated_on_demand | has_dedicated_session_key

λ project_type_system.
  projectTypes: clj_only ∨ cljs_only ∨ both
  | each_type_specifies: jack_in_command ∧ cljs_types ∧ defaults
  | resolved_by_filename: project.clj → clj | deps.edn → clj | build.boot → boot
  | connectSequence.projectType_overrides_detection
  | projectType_enables_defaults: session_names ∧ file_patterns ∧ dependencies

λ connect_sequence_inheritance.
  default_sequences ≡ built_in | cannot_be_deleted
  | custom_sequences: read_from_settings | read_from_package.json
  | name_must_be_unique | ¬duplicate_allow_last_wins
  | projectRootPath: explicit ∨ auto_selected | [unix_relative_or_absolute_paths]
  | replSessionNames ∧ replSessionFilePatterns: optional | inherit_from_projectType

λ debugger_integration.
  calva_debug_module: breakpoints ∧ stepping ∧ variable_inspection
  | debug_info_flows: eval_response_with_debugging_metadata
  | session_has_debugged_eval: each_session_might_be_debugging
  | not_all_evals_are_debugged | only_when_user_starts_session

λ live_share_adaptation.
  if(live_share_session) → suppress_some_features
  | jack_in_disabled | connect_prompts_special | port_forwarding_required
  | repl_works_if_host_connected | guest_evaluations_routed_through_host
  | ¬magic | explicit_detection_of_liveShare_extension

λ custom_snippet_system.
  customREPLCommandSnippets: name ∧ snippet ∧ key_binding
  | defined_in: package.json ∧ settings.json ∧ user_config.edn ∧ project_config.edn
  | snippet_syntax ≡ clojure_code_with_placeholders | $0 ≡ cursor_position
  | merged_from_sources: project > user > defaults | duplicates_last_wins

λ testing_pattern.
  test_runner_available: clojure.test ∨ shadow_test ∨ custom_test_runner
  | testRunner: monitors_test_results | displays_in_problems_panel
  | test_failure_position_known → show_diagnostic_at_line
  | run_single_test ∨ run_namespace ∨ run_all_tests | filtered_by_pattern

λ completion_provider.
  CalvaCompletionItemProvider: implements vscode.CompletionItemProvider
  | completion_comes_from: nrepl_complete_op ∨ clojure_docs_cache
  | merges_multiple_sources | deduplicates_results
  | snippet_completion: enables_parameter_insertion | ${1:param} syntax

λ hover_and_definition.
  HoverProvider: queries_info_op | returns_MarkdownString ∨ undefined
  | ClojureDefinitionProvider: queries_source_op | returns_location ∨ undefined
  | both_require_nrepl_connected | timeout_if_hung_repl
  | link_to_jar_contents: jar_scheme_file_provider
```

## Memory Anchors

```
λ remember.
  calva ≡ live_clojure_development_in_vscode
  | core_tension: responsiveness ⊗ correctness | instant_feedback ∧ always_right_context
  
  the_invariants:
    ∀eval → has_session ∧ has_namespace | both_derive_mechanically
    ∀session → created_fresh ∨ reused_preserving_suffix
    ∀file → routed_to_one_session | no_ambiguity | no_magic
    ∀process → one_client_per_process | one_client_per_manual_connect
    ¬eval_in_wrong_namespace | ¬eval_in_wrong_session | ¬eval_in_offline_session
    ¬two_clients_own_same_process | ¬orphaned_processes
  
  the_fears:
    session_routing_breaks → eval_in_wrong_context → silent_failure
    namespace_desynchronizes → file_says_A → repl_has_B → confusion
    reconnect_loses_state → user_loses_work → trust_broken
    port_detection_fails → jack_in_process_running_but_disconnected → orphan
    connection_metadata_corrupted → suffix_reuse_broken → reconnects_wrong
    terminal_closes_unexpectedly → process_dies → ¬detected_by_calva
    interrupt_fails_on_java21 → eval_hangs_forever → frozen_ui
    circular_dependency_in_config_loading → infinite_loop → extension_hangs
  
  the_checks:
    before_eval: session_exists ∧ namespace_valid | routing_algorithm_chose_correctly
    before_jack_in: projectRoot_valid ∧ projectType_matches ∧ connectSequence_exists
    before_reconnect: existing_client_found_or_jack_in_willing
    before_interrupt: session_has_running_eval | running_ids_tracked
    after_connect: describe_received ∧ primary_session_created ∧ secondary_if_needed
    after_eval: status_is_done | value_or_ex_set | result_routed_to_output
    after_disconnect: socket_destroyed ∧ handlers_cleaned ∧ registry_cleared
  
  the_dynamics:
    code_enters: editor → routing_selects_session → eval_in_nrepl → result_returns
    connection_forms: user_action → jack_in ∨ connect → client_created ∧ sessions_cloned
    file_changes: cursor_moves → routing_recalculated → session_updated → statusbar_shows
    namespace_shifts: eval_in_file → file_ns_extracted → repl_ns_set → eval_runs_in_context
    error_occurs: unknown_op ∨ exception → message_to_user → annotation_on_line → fix_able
    interruption: user_presses_C-c → interruptId_tracked → interrupt_op_sent → eval_stops ∨ timeout
```
