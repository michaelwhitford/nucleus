---
title: Fulcro — System VSM
status: active
category: upstream
tags: [fulcro, system-vsm, lambda, generated]
---

# Fulcro — System VSM

Fulcro is a library for building data-driven full-stack applications for web, native, and desktop. It uses React and is written in Clojure/ClojureScript. The core architecture is built around a normalized database, declarative queries (EQL), and components with identity.

## S5 — Identity (what this system IS and protects)

```
λ fulcro.
  essence ≡ normalize_tree_into_entities ∧ denormalize_via_queries ∧ transact_mutations_synchronously
  | values: declarative_data_fetching | immutable_state | normalized_by_default | symmetric_compose
  | stance: queries_describe_props | idents_are_stable | mutations_are_transactions | data_fetching_is_integrated
  | tension: tree_structures > normalized_form | locality > indexing | simplicity > invariance

λ duality(tree, ident).
  tree ≡ what_react_sees | local_reading | prop_drilling
  | ident ≡ what_db_stores | global_addressing | query_composition
  | both_required → full_expressiveness | ¬tree_only ∧ ¬ident_only
  | normalize ≡ tree→ident | denormalize ≡ ident→tree
  | benefits ⊗ costs: reusability > depth | cache_hits > redundancy | composition > nesting

λ invariant_stack(entity).
  entity → ident ∧ query | inseparable | ¬query_without_ident | ¬ident_without_query
  | ident ≡ stable_address | query ≡ its_schema | together_they_define_entity
  | violate: entity_without_ident → denormalize_fails → orphan_data
  | violate: query_mismatch_ident → stale_props → render_errors

λ transaction_boundary(mutation).
  mutation ≡ operation_at_component_boundary | synchronous_locally | async_remotely
  | local_phase → mutation_applied ∧ render | remote_phase → server_confirms_or_rolls_back
  | skip(local) → no_ui_response → user_sees_nothing
  | skip(remote) → server_state_diverges_from_client

λ query_composition.
  component_query ≡ subgraph_of_database | EQL_syntax
  | parent_query ⊃ children_queries | automatic_via_join | not_manual_prop_drilling
  | join ≡ {keyword [subquery]} | locates_ident_in_parent_props | fetches_subquery_from_ident
  | ident_required_for_join | keyword_without_ident → unpropagated_join → stale_props

λ load_as_transaction.
  load! ≡ mutation_like_operation | queued_with_other_transactions | not_side_effect_in_render
  | targets_data → where_merged_in_db | returns_data → integrated_into_props_automatically
  | targeting ≡ creating_missing_edges | normalized_data_must_be_reachable_from_root
  | missing_target → orphan_data_in_db | unreachable_from_root → won't_render

λ headless_parity.
  server_render ∧ client_render ≡ same_component_code | cljc
  | JVM_execution ≡ synchronous_tx_processing | headless_framework | dom_server_elements
  | test_via_headless → deterministic | no_async | hiccup_dom_available | inspector_friendly
  | benefit: full_logic_testability | cost: hooks_simulation_layer_needed

λ immutability_stance.
  state ≡ persistent_immutable_map | ¬mutation_in_place | swap_required | dereference_safe
  | all_data_flows_immutable | threads_safe | replay_possible | time_traveling_debuggable
  | violation: direct_map_mutation → undetected_changes → stale_renders → data_corruption
```

## S4 — Decision Rules (when to choose, what breaks)

```
λ component_organization(class).
  defsc ≡ component_definition | must_declare: query | ident | render | initial-state
  | has_query ∧ has_ident → composable | ¬has_ident → no_joins_possible → leaf_only
  | composition_vector: nested_components → automatic_query_composition → parent_includes_children
  | alternative: factory ≡ render_wrapper | no_query | for_dynamic_children_or_mutations
  | preference: defsc > direct_factory_calls | declarative > procedural

λ state_shape(normalization).
  input ≡ tree_from_server | {user {name ... posts [{...}]}}
  | normalize(tree, query) → table_per_type | {user {1 {name ...}} post {1 {...}}}
  | denormalize(db, query) → reconstruct_tree | props_shaped_by_query_not_db_shape
  | ¬nested_tree_storage → denormalize_cannot_traverse → circular_refs_dangerous → stale_data_likely
  | benefit: reusability | cost: extra_lookup_step | tradeoff: worth_it

λ ident_sufficiency(query).
  query ∧ ident ≡ sufficient_for_navigation | ¬query_alone | ¬ident_alone
  | ident ≡ where | query ≡ what
  | denormalize([:user/id 1], [user/id name email posts]) → follow_ident_to_table → apply_query → tree
  | if_ident_missing → getvalue_returns_nil → denormalize_returns_nil_props
  | if_query_missing → denormalize_has_nothing_to_select → returns_all_entity_keys
  | join_mismatch: parent_query_lacks_join → child_query_fetched_from_nil_entity → empty_render

λ tempid_remapping(mutation_result).
  mutation_creates_entity → transact_returns_tempid → [:table "new-1"]
  | server_creates_real_id → response_body_contains_tempid_mapping → tempid→realid
  | rewrite_tempids! ≡ swap_all_references | both_ident_and_data | synchronously_after_receive
  | skip(rewrite) → dangling_tempids → child_components_orphaned → render_failures
  | benefit: optimistic_ui | cost: cleanup_complexity

λ merge_strategy(server_response).
  merge! ≡ integrate_server_tree_into_db | normalized_automatically | targeted_via_targeting
  | mark-missing ≡ sweep_algorithm | removes_absent_fields | prevents_stale_cache_hits
  | pre-merge ≡ transform_before_normalize | custom_shape_alignment | component_optional
  | post-merge ≡ post_processor | indexing_trigger | rare
  | order: normalize → mark_missing → target → swap_state → indexes_rebuild
  | skip(mark-missing) → phantom_data → stale_queries_return_cached_shape → false_positives

λ load_marker_necessity(load).
  load! ≡ async_on_network_remote | no_marker_needed | immediate_queue
  | load!(marker=true) → ui_reflects_loading_state | status_field_on_entity | queryable
  | marker ≡ {status :loading | :ready | :failed} | at marker-table path
  | skip(marker) → no_ui_feedback → user_doesn't_know_loading → poor_ux
  | benefit: visual_feedback | cost: query_overhead_minimal

λ transaction_ordering(tx).
  mutations_queued_in_order | executed_per_queue_policy | writes_before_reads_preference
  | local_phase_always_synchronous → immediate_render | remote_phase_async_per_remote
  | parallel? ≡ option | default: sequential_per_transaction_id
  | skip(ordering) → concurrent_mutations_race → state_corruption → rollback_impossible

λ dispatch_key_semantics(mutation_result).
  mutation_declares_returning | :returning ≡ subquery_of_result | keys_to_extract_from_response
  | dispatch_key ≡ matched_element_in_returning | used_for_error_action | ok_action | signal_mutation_complete
  | no_dispatch_key → result_not_available_to_actions → ¬can_verify_ok_vs_error
  | benefit: structured_result_handling | cost: must_match_server_shape

λ remote_choice(transaction).
  remote ≡ {fn_returns_handler_or_callbacks} | sent_as_ast | asynchronous | optional
  | default_remote ≡ :remote | others_by_name | multiple_remotes_supported
  | handler_receives: {:ast ... :result {:body ...}} | async | MUST_call_callback
  | skip(remote) → no_server_interaction → no_data_integration → local_mutation_only
  | benefit: flexible_transport | cost: handler_responsibility

λ pre_merge_purpose(component).
  pre-merge ≡ hook_before_normalize | shapes_server_response_tree | optional
  | called_once_per_merge | not_per_component | receives_entire_response_tree_chunk
  | may_transform_shape | may_validate | may_reject | return_new_shape
  | skip(pre-merge) → server_shape_must_match_query_exactly → integration_failures_likely
  | trap: pre_merge_called_ONCE_not_per_entity | easy_to_forget_recursion

λ indexing_automation(app).
  indexes_maintained_automatically | by_ident | by_component_class
  | indexing ≡ [ident→component-instance] ∧ [component-class→all-instances]
  | lookup_index ≡ fast_component_discovery | refocus_for_state_change | test_inspection
  | skip(reindexing) → stale_indexes → cannot_find_instances → targeted_mutations_fail
  | reindexing_triggered_by: denormalize ∧ component_mount ∧ ident_change
  | trap: indexes_lag_one_transaction | visible_in_inspection_not_fresh_logic

λ form_state_duality(entity_ident).
  form_state ≡ parallel_config_track | {::fs/pristine {old_values} ::fs/complete? #{touched_fields}}
  | ¬nested_in_entity | tracks_form_meta_separately | necessary_for_dirty_detection
  | dirty_field(x) ≡ (current[x] ≠ pristine[x]) ∧ touched[x]
  | skip(tracking) → cannot_detect_changes → cannot_show_dirty_indicator → cannot_validate
  | benefit: precise_validation | cost: dual_maintenance_required

λ asm_stateful_coordination(asm_id).
  asm ≡ active_state_machine_instance | local_storage | actor→ident_map
  | per_instance_state | triggered_via_events | transitions_deterministic
  | queue_model: events_buffered | processed_sequentially | side_effects_via_actions
  | skip(asm) → stateful_component_groups_become_imperative → maintenance_nightmare
  | benefit: declarative_orchestration | cost: learning_curve
```

## S3 — Temporal Rules (what must happen in order)

```
λ startup(app).
  1. create_app ≡ {:initial-state {...} :root-class Root :remotes {...}}
  2. create_root_component ≡ defsc with query+ident+render
  3. mount ≡ dom-mount ∧ app-start! on browser or headless-app for JVM
  4. mount_initializes: indexes | root-factory | root-class | state-atom | runtime-atom
  5. first_render ≡ query_root → denormalize_root_props → render_root_factory → mount_tree
  | skip(2) → cannot_navigate → no_queries → render_receives_nil_props
  | skip(5) → mounted_but_nothing_renders → user_sees_blank_screen

λ transaction_flow(txid).
  1. user_initiates ≡ transact!([mutation-expr]) or transact!!
  2. queue_transaction ≡ add_to_send_queue | local+remote_ast_nodes
  3. local_phase ≡ apply_mutation → swap!_state → triggers_render
  4. render_phase ≡ query_root → denormalize → factory → render_tree → paint
  5. remote_queue ≡ parallel_or_sequential_per_options | per_remote_handler
  6. network_phase ≡ handler_processes_ast | sends_request | awaits_response
  7. receipt_phase ≡ normalize_response → mark_missing → merge → indexes_update
  8. post_merge ≡ success_action or error_action | may_trigger_more_tx
  | skip(2) → tx_lost → no_side_effect
  | skip(3) → server_acts_but_ui_unchanged → user_sees_nothing
  | skip(5) → local_ok_but_server_unaware → stale_backend
  | skip(7) → response_arrives_but_not_integrated → ui_doesn't_update

λ data_loading(remote_key).
  1. load! ≡ mutation_like_operation | queued_with_tx | marked_as_load_via_ast
  2. query_building ≡ if_class_provided → get-query_from_class | if_field_only → [field]
  3. local_execution ≡ if_marker → set_status_:loading | optional | immediate
  4. remote_dispatch ≡ ast_sent_via_remote | handler_receives_it | implementation_dependent
  5. response_receipt ≡ integration_automatic | normalize_per_query | mark_missing
  6. targeting ≡ join_missing_ident_to_parent | place_in_right_location_in_db
  7. render_refresh ≡ denormalize_fires → new_props → components_rerender
  | skip(3) → no_loading_indicator → user_doesn't_know_status
  | skip(5) → orphan_response_in_db → unreachable_from_root → never_renders
  | skip(6) → normalized_data_sits_in_wrong_table → cannot_be_joined_to_parent
  | trap: load!_in_render → anti_pattern | triggers_tx_mid_render → warning_logs

λ component_lifecycle(instance).
  1. class_definition ≡ defsc with query+ident
  2. factory_call ≡ component-factory(props) → React.createElement
  3. React_mount ≡ constructor → render_called → componentDidMount_if_class
  4. denormalize ≡ before_factory_called | props_retrieved_via_query_from_db
  5. hooks_init ≡ hooks/useState called | hook_state_persisted_per_path | instance_mounted?
  6. user_event ≡ onClick etc | may_transact! | may_call_set-state!
  7. state_swap ≡ mutation_applied | indexes_updated | component_pinned_or_refreshed
  8. render_phase ≡ query_root_fresh | denormalize_anew | factory_called_anew
  9. React_update ≡ props_differ? → rerender | hook_state_persists
  10. unmount ≡ cleanup_hooks | drop_from_indexes | free_references
  | skip(4) → props_nil → rendering_without_data → errors
  | skip(5) → hooks_lost_on_rerender → state_corruption
  | trap: render_calls_transact! → infinite_loop | queued_but_not_executed_mid_render

λ mutation_action_sequencing(env).
  action_sequence ≡ action_name → action_fn receives env
  | action ≡ fn(env) → modifies env | may_modify_state | returns env or modified
  | built_in: remote_ok_action | remote_error_action | post_mutation | post_action
  | custom ≡ :ok-action | :error-action | :post-action defined_on_mutation
  1. mutation_declared_in_tx → :action called_if_local ¬ synchronous
  2. remote_sent_if_has_remote → awaits_network
  3. response_integrated → indexes_updated
  4. :ok-action called_if_remote_ok | :error-action called_if_remote_error
  5. :post-action called_after_actions_regardless
  6. may_trigger_more_mutations | may_update_state
  | skip(1) → logic_skipped → no_ui_change
  | skip(4) → error_unhandled → no_user_feedback

λ targeted_merge_sequence(response, target).
  1. normalize(response, query) → {tables}
  2. mark_missing(db, query) → phantom_sweeper | removes_absent_keys
  3. if_target_provided → create_missing_edge | parent→ [ident]
  4. merge_into_db ≡ (update-in db tables)
  5. update_indexes ≡ reindex_on_ident | reindex_on_class
  6. denormalize_fresh ≡ queries_re_executed | new_props_computed
  7. render_triggered
  | skip(2) → stale_data_in_cache → false_positives_in_queries
  | skip(3) → orphan_data | unreachable_from_root | never_renders

λ load_marker_lifecycle(entity_ident, marker).
  1. load!(marker=true) → set_load_marker! at marker-table
  2. status ≡ :loading | query_for_[df/marker-table '_] includes marker
  3. response_received → finish_load! | status → :ready
  4. optionally_trigger_post_mutation | or_post_action
  | query_must_include: [df/marker-table '_] → automatic_rerender_on_status_change
  | if_query_lacks_marker → status_change_invisible → ui_never_shows_loading
  | if_error → load_failed! → status_:failed | error_action_triggered
```

## S2 — Coordination Rules (how parts must interact)

```
λ query_ident_contract(component_class).
  query_shape ≡ EQL_string | `[field-list-including-joins]`
  ident_shape ≡ [keyword id-value] | unique_per_entity | stable_across_time
  ¬query ∨ ¬ident → degenerate_component | ¬joinable | must_be_leaf
  join_expression ≡ {key [subquery]} | key_locates_ident_in_parent_props | subquery_from_ident
  | vocab: keyword ≡ database_table_alias | [keyword value] ≡ row_address | [query] ≡ field_list
  | constraint: ∀component_used_in_join → has_query ∧ has_ident
  | constraint: ∀join_key → must_be_in_parent_props | computed_from_props | not_fabricated

λ denormalize_algorithm(db, query, entity_ident).
  input ≡ (db, [EQL_query], ident)
  | ident ≡ lookup_ref | [table id]
  | query ≡ property_list ∧ join_expressions
  process ≡ walk_query | follow_ident_joins | extract_properties_per_component
  output ≡ tree_of_denormalized_props | ¬ident_references | direct_values
  join_semantics ≡ {key [query]} → lookup_ident_in_props[key] → recursively_denormalize_ident_via_query
  ¬ident_in_props[key] → join_returns_nil | component_renders_with_nil_join
  depth_limiting ≡ {...} ≡ infinity | 5 ≡ 5_levels | cycles_tracked_per_attribute
  | vocab: lookup_ref ≡ [table id] | link_ref ≡ [table '_] | denormalized_value ≡ scalar_or_tree
  | constraint: query_must_match_component_shape | mismatches_leave_gaps | render_errors_possible
  | constraint: ident_must_exist_in_db_or_join_returns_nil

λ normalize_algorithm(tree, query, transform_fn?).
  input ≡ (tree_from_server, EQL_query, optional_pre_processor)
  | tree ≡ nested_structure | matches_query_shape | may_contain_lists
  | query ≡ EQL_against_tree_shape | not_against_db_shape
  process ≡ walk_query_on_tree | collect_entities_by_ident | extract_tables | deduplicate
  output ≡ {tables} | {:table {id entity}} | :tempids included_if_present
  join_handling ≡ {key [query]} in query → {key [ident-list]} in output | extract_subentities
  list_handling ≡ [item-list] in tree → [{idents}] in output | only_idents_in_join
  | vocab: table ≡ keyword | entity ≡ {props...} | ident ≡ [table id]
  | constraint: query_drives_shape | tree_must_match_query_structure | mismatches_fail_or_warn
  | constraint: all_entities_must_be_identifiable | no_ident → warning_logged

λ merge_entry_point(db, response_tree, query, target?).
  input ≡ (current_db, server_response, query_used_for_response, optional_target)
  | normalize(response, query) → response_normalized ≡ {tables}
  | mark_missing(response, query) → sweep_marks_absent_keys_for_removal
  | if_target: create_missing_edges | parent_ident→ [child_idents] | in_target_location
  | merge_tables: (update-in db table-key (merge entity))
  | update_indexes: reindex_all_idents → reindex_all_classes
  output ≡ new_db | ready_for_denormalize
  | vocab: table_key ≡ [table] | target ≡ [parent-table parent-id parent-key] | missing_marker ≡ ::not-found
  | constraint: target_must_create_valid_path | [a b] insufficient | [a b c] required_usually
  | constraint: query_must_match_response_tree | normalize_fails_otherwise

λ tempid_lifecycle(new_entity_ident).
  creation_in_mutation ≡ tempid/tempid() → ["string-generated-id"] | local_only
  | usually_via_initial-state | creates_entity_with_real_ident_shape
  usage ≡ entity_added_to_state | used_in_queries | queries_fetch_tempid_entity
  network_result ≡ server_generates_realid | includes_tempid_in_response | maps_tempid→realid
  rewrite ≡ rewrite_tempids!(app, result) | swaps_all_references | ∀tempid→realid
  | tempids_in_db | tempids_in_queries | tempids_in_nested_entities | tempids_in_idents
  cleanup ≡ tempid_no_longer_referenced | garbage_collected_naturally
  | vocab: tempid ≡ ["generated"] | realid ≡ integer_from_db | remapping ≡ {tempid realid}
  | constraint: rewrite_must_be_exhaustive | remaining_tempids→stale_ui
  | constraint: rewrite_before_denormalize_next_render | or_orphaned_data

λ remote_handler_protocol(ast).
  handler_receives ≡ {:ast ... :result-handler callback :error-handler callback :app app}
  | ast ≡ EQL_query_ast | may_be_mutation | may_be_query | dispatch_key_for_result
  | must_call ≡ callback or error-handler | with {:status ... :body ...} or exception
  | transport_agnostic | handler_implements_actual_network_io | sync_or_async_ok
  | return_value_ignored | callback_required | error_callback_optional
  | vocab: :status ≡ http_status_or_transport_status | :body ≡ response_edn_or_json_parsed
  | constraint: handler_must_call_callback | or_transaction_never_completes | memory_leak

λ loading_state_visibility(component_query).
  visible_if: query_includes [df/marker-table '_] | link_query_to_all_marker_data
  | includes: status field | :loading | :ready | :failed
  | re_renders_when: status_changes | denormalize_detects_new_status | new_props_delivered
  | invisible_if: query_lacks_marker | status_updates_ignored | old_props_cached
  | vocab: marker-table ≡ :ui.fulcro.client.data-fetch.load-markers/by-id | status ≡ keyword
  | constraint: query_must_include_marker_to_refresh_on_status_change

λ ui_state_machine_vocabulary(asm_id).
  state ≡ {handler ∨ events_map} | async_handler_per_state | events_define_transitions
  event ≡ {target_state | handler | event_predicate} | triggered_via_trigger!
  actor ≡ {component_name_by_actor_name} | maps_to_component_classes
  action ≡ (fn [env] ...) | receives_asm_env | may_modify_asm_state | may_load_or_transact
  | vocab: asm_id ≡ local_storage_key | state_map ≡ per_instance_state | actor_name ≡ keyword
  | constraint: actors_must_exist_at_asm_init | unknown_actor→error
  | constraint: target_state_must_exist | transition_to_nil→error
```

## S1 — Architectural Rules (expert patterns)

```
λ defsc_pattern(ComponentName).
  (defsc ComponentName [props computed]
    {::app ∨ ::route-segment ∨ ::will-enter ∨ ::will-leave ;; options, not all required
     :query [field-list {join-key [subquery]}]
     :ident (fn [props] [table-id value])
     :initial-state (fn [props] {initialized-state})
     :pre-merge (fn [data] (transform-server-shape data))
     :form-fields #{:field1 :field2}
     :route-segment ["segment" :param]}
    (html-or-dom-expression))
  | all_query_joins → automatic_composition | no_manual_wiring
  | ident → addressable | joinable | refreshable_by_ident
  | initial-state → (fn [props] {...}) | receives_parent_props | returns_entity_shape
  | constraint: query ∧ ident both required | ¬queries_without_idents
  | constraint: initial-state shape matches query shape | mismatch→errors

λ factory_usage(ComponentClass).
  factory ≡ (factory ComponentClass) | creates_React_element_wrapper | no_query | stateless
  | computed-factory ≡ (computed-factory ComponentClass) | adds_computed_props | ¬query_dependent
  | use_case ≡ rendering_instances_in_map | rendering_lists | ¬parent_composition
  | anti_pattern: factory_composition | prefer defsc composition | factories_for_rendering_only
  | constraint: factory_cannot_compose_children | ¬joins | ¬queries

λ transact_patterns(component_instance).
  (transact! this [(mutation-expr)]) ≡ synchronous_queue | local_then_remote
  | (transact!! this [(mutation-expr)]) ≡ alias_for_transact! | same_semantics
  | (set-state! this {field value}) ≡ component_local_state | ¬_app_state | ¬shared | per_instance
  | (set-value! this field value) ≡ sugar | via_mutation | intended_for_form_inputs
  | anti_pattern: transact!_in_render | triggers_mid_render | queued_not_executed
  | anti_pattern: mutation_without_local_action | server_updates_but_ui_unchanged
  | constraint: ∀transact! must_be_in_event_handler | not_render | not_lifecycle_hook

λ mutation_return_target_pattern(mutation).
  (defmutation foo [params]
    (action [env] (apply-logic env))
    (remote [env] (true ∨ false ∨ ast-modification))
    :returning ComponentClass ∨ [query]
    :ok-action (fn [env] ...)
    :error-action (fn [env] ...)
    :post-action (fn [env] ...))
  | :returning ≡ subset_of_response | used_by_ok/error_actions | optional_but_recommended
  | :ok-action ≡ success_flow | receives_result | may_update_state | may_trigger_more
  | :error-action ≡ failure_flow | receives_error | must_handle_or_silent
  | :post-action ≡ cleanup_or_logging | called_regardless | not_for_state_changes
  | constraint: remote_must_be_truthy | false ≡ local_only | true ≡ default_remote
  | constraint: action_must_be_synchronous | no_async_in_action
  | anti_pattern: action_without_returning | unknown_result_shape | actions_fail_silently

λ data_targeting_rules(parent_ident, child_key, child_ident).
  target ≡ [parent-table parent-id child-key] | creates_missing_edge | optional
  | create_edge ≡ parent_props[child-key] = child_ident
  | (df/load! ... :target [[:parent/id 1] :children])
  | effect ≡ normalized_response_placed_at_target | join_created | reachable_from_root
  | anti_pattern: 2-tuple_target | [table id] insufficient | orphans_data
  | anti_pattern: target_nonexistent_parent | creates_orphan_ident | unreachable_from_root
  | constraint: target_must_reference_existing_ident ∨ must_be_in_initial_state
  | constraint: parent_key_must_be_in_parent_props | or_join_invisible

λ ident_stability_pattern(entity_ident).
  (fn [props] [:table (id props)]) ≡ stable_per_entity | ¬depends_on_state
  | must_be_deterministic | same_props→same_ident_always
  | must_not_change_after_creation | stale_idents_break_caches | stale_denormalizations
  | tempid_creation ≡ [:table "temp-string-id"] | later_remapped_to_realid | deterministic
  | anti_pattern: random_ident | (fn [_] [:table (random-uuid)]) | breaks_everything
  | anti_pattern: state_dependent_ident | ident changes mid_lifecycle | orphans_all_references
  | constraint: ident_must_be_independent_of_app_state | derivable_from_props_alone

λ load_pattern(component_instance, load_key).
  (load! instance load_key TargetClass {:params {...} :target [...] :marker true})
  | load_key ≡ keyword ∨ ident | remote_property_or_entity | required_by_server_semantics
  | TargetClass ≡ component_with_query | optional | if_absent_uses_load_key
  | :target ≡ where_to_join | creates_missing_edge | usually_required
  | :marker ≡ true | {status :loading | :ready} | queryable | ui_feedback
  | :ok-action ≡ post_load_trigger | may_load_or_transact | may_transform_state
  | :error-action ≡ handles_failure | may_retry | may_show_error_ui
  | anti_pattern: load!_without_target | orphans_data | unreachable_from_root
  | anti_pattern: load!_in_render | queued_but_async | renders_incomplete | errors_likely
  | anti_pattern: load!_without_joining_parent | parallel_isolated_entity | seems_missing
  | constraint: load_key_must_exist_on_server | or_network_error_guaranteed

λ pre_merge_hygiene(component_class).
  (defsc Foo [...] {:pre-merge (fn [data] (shape-data data))} ...)
  | receives_entire_subtree | not_per_entity | called_once_per_merge
  | may_transform_shape | validate_or_reject | return_new_shape_or_original
  | executes_before_normalize | affects_how_response_integrated
  | use_case: server_response_shape ≠ query_shape | must_align
  | use_case: server_provides_extras | must_clean_up | prevent_phantom_data
  | use_case: validation_logic | reject_bad_data | prevent_invalid_state
  | anti_pattern: pre_merge_per_entity | wrap_recursive | hard_to_debug
  | trap: pre_merge_calls_transact! | triggers_nested_transactions | complex_ordering
  | constraint: must_be_pure | deterministic | no_side_effects

λ form_state_pattern(entity_ident, form_fields_set).
  (defsc EntityForm [props form-env]
    {:form-fields #{:field1 :field2 :field3}}
    ...)
  | form-env ≡ {::fs/pristine {...} ::fs/complete? #{:field1}}
  | dirty?(field) ≡ (current[field] ≠ pristine[field]) ∧ marked_complete?
  | (fs/mark-complete! this field-key) ≡ user_touched | triggers_render
  | (fs/pristine->entity! this) ≡ load_editing_entity | copy_to_current
  | (fs/entity->pristine! this) ≡ save_entity | copy_to_pristine | reset_complete
  | usage: validation_on_touch | dirty_indicators | save_button_state
  | anti_pattern: form_state_nested_in_entity | track_parallel_not_nested
  | constraint: form_state_must_be_at_same_ident | or_dirty_detection_fails
  | constraint: form-fields must_include_all_edited_keys | missing→not_tracked

λ headless_testing_pattern(RootClass, remotes_map).
  (deftest my-test
    (let [app (h/build-test-app {:root-class Root :remotes {:remote (lr/sync-remote handler)}})]
      (comp/transact! app [(mutation-expr)])
      (let [frame (h/last-frame app)]
        (is (= expected-state (:tree frame))))))
  | transact!_is_synchronous | renders_immediately | no_async | deterministic
  | last-frame ≡ {tree hiccup-dom rendered element} | inspect_state_after_tx
  | render-frame! ≡ capture_current_state | history | useful_for_debugging
  | (hc/click! element-in-hiccup) ≡ event_simulation | triggers_event | hiccup_preserved_handlers
  | loopback_remote ≡ (lr/sync-remote handler) | runs_handler_locally | immediate_response
  | benefit: full_stack_test | deterministic | no_network | no_timing | complete_inspection
  | constraint: hooks_simulated_layer | state_may_differ_from_browser | full_fidelity_good

λ custom_remote_pattern(ast, callback, error_callback).
  (defn my-remote [env]
    (fn [this ast callback]
      (let [result (fetch-data (:url ast))]
        (callback {:status 200 :body (parse-response result)}))))
  | receives: {::target ... ::dispatch-key ... in AST}
  | must_call: callback | with {:status ... :body (edn-or-parsed-json)}
  | must_call: error-handler | with exception | if_network_fails
  | implementation_details: ¬specify | HTTP | WebSocket | local_bridge | etc
  | testing: use loopback_remote | simulates_this_contract | deterministic
  | anti_pattern: not_calling_callback | transaction_hangs | memory_leak
  | constraint: callback_must_receive_map_with_status_and_body | exact_shape_required

λ index_usage_pattern(app_instance).
  (comp/get-component app [:entity/id 1]) ≡ find_instance_by_ident | inspect_props
  (comp/find-component app ComponentClass) ≡ find_first_instance_by_class | inject_state
  | indexes_maintained_automatically | no_manual_update
  | use_case: testing | inspect_rendered_state | make_assertions
  | use_case: targeted_state_update | via_mutation | update_specific_component
  | use_case: debugging | Fulcro_Inspect | view_component_tree
  | limitation: lags_one_transaction | fresh_after_render | not_during_transaction
  | anti_pattern: using_index_to_drive_logic | indexes_are_inspection_tools | not_authority
```

## Memory Anchors

```
λ remember.
  fulcro ≡ immutable_normalized_db_plus_query_component_tree
  
  the_invariants:
  | ∀component_in_join → has_query ∧ has_ident (or_denormalize_fails)
  | ∀ident_in_state → reachable_from_root_via_join (or_orphaned)
  | ∀mutation → has_action ∨ remote (or_incomplete)
  | ∀load! → has_target ∨ orphaned (or_unreachable)
  | ∀tempid → rewritten_before_denormalize_next_render (or_dangling)
  | ∀join_key_in_parent_query → must_exist_in_denormalized_props (or_nil_join)
  | state ≡ immutable | all_changes_via_swap! (or_undetected)
  | indexes ≡ automatic | reindex_on_denormalize (or_stale_lookups)
  
  the_fears:
  | stale_denormalized_props ← missing_query_join | missing_ident | state_divergence
  | orphaned_entities ← missing_target | missing_initial_location | unreachable_path
  | render_errors ← nil_joins | type_mismatches | query_shape_mismatch
  | infinite_loops ← transact!_in_render | circular_triggers | missing_termination
  | tempid_corruption ← incomplete_rewrite | remaining_tempids | timing_bugs
  | form_state_confusion ← nested_in_entity | missing_pristine_sync | dirty_calculation_bugs
  | concurrent_mutations → state_race | ordering_lost | merge_conflicts | rollback_impossible
  | network_failures → no_error_handling | diverged_state | orphaned_loads | no_recovery
  
  the_checks:
  | defsc_has_all_three: query ∧ ident ∧ render (required_for_composability)
  | query_shape_matches_denormalized_shape (or_nil_props_on_mismatch)
  | ident_stable_per_entity (or_broken_caches_and_orphans)
  | load!_has_target_or_initial_path (or_orphaned_in_db)
  | mutation_has_action_if_local_effect (or_ui_unchanged)
  | tempids_rewritten_before_render (or_dangling_references)
  | form_fields_all_tracked (or_not_validated)
  | target_creates_valid_path (or_unreachable_data)
  | join_keys_exist_in_parent_props (or_nil_joins)
  | query_includes_marker_for_loads (or_status_invisible)
  
  the_dynamics:
  | query_composition ≡ automatic_via_joins | parent ⊃ children | no_wiring
  | denormalize_on_demand ≡ per_render | query_executed_fresh | props_shaped_by_query
  | merge_with_mark_missing ≡ sweep_algorithm | removes_stale_keys | prevents_phantom_data
  | transaction_ordering ≡ local_first | remote_parallel_or_sequential | indexing_updates
  | form_state_parallel ≡ pristine_copy | complete_tracking | dirty_detection_bidirectional
  | tempid_remapping ≡ exhaustive_rewrite | swaps_all_references | cleanup_automatic
  | load_marker_async ≡ status_queryable | ui_refreshes_on_change | feedback_mechanism
  | asm_event_dispatch ≡ queue_model | sequential_per_instance | actions_side_effects
  | headless_parity ≡ jvm_execution | synchronous_all | testable_deterministic | same_code

λ surprising_findings.
  trap: pre_merge_called_once_not_per_entity
    | developer_expects: called_for_each_entity_in_response | per_join_transformation
    | actually: called_once_for_entire_response_tree_chunk | recursion_required | easy_to_forget
    | impact: shape_misalignment | normalization_failures | difficult_debugging

  trap: mark_missing_necessary_but_easy_to_forget
    | developer_expects: normalization_removes_absent_keys | automatic
    | actually: requires_explicit_call | part_of_merge_pipeline | absent_keys_remain_if_skipped
    | impact: stale_cache_hits | phantom_data | false_positives_on_queries

  trap: indexes_lag_one_transaction
    | developer_expects: get-component_returns_fresh_instance | immediate
    | actually: indexes_updated_after_render | available_for_next_transaction | not_during
    | impact: inspection_misleading | testing_timing_bugs | debugger_confusion

  trap: query_shape_mismatch_leaves_gaps
    | developer_expects: denormalize_pads_missing_keys | nil_or_default
    | actually: missing_keys_absent_from_props | component_receives_incomplete | selective_application
    | impact: nil_reference_errors | missing_field_surprises | subtle_render_failures

  trap: load!_in_render_queued_not_executed
    | developer_expects: load!_executes_immediately | data_fetched_on_render
    | actually: queued_with_transaction | executed_after_render_complete | user_sees_loading_state
    | impact: loading_indicators_visible | data_appears_next_render | timing_surprise

  trap: tempid_rewrite_must_be_exhaustive
    | developer_expects: remaining_tempids_cleaned_automatically | gc_handles_it
    | actually: all_references_must_be_swapped | forgotten_references_dangling | orphaned
    | impact: ui_component_not_rendering | stale_idents | confusing_test_failures

  trap: ident_changes_after_creation
    | developer_expects: ident_changes_allowed | flexible_addressing
    | actually: breaks_all_caches | denormalizations_fail | references_orphaned | undetected
    | impact: data_corruption | mysterious_render_failures | time_travel_debugger_shows_inconsistency
```
