---
title: "Fulcro — System VSM"
status: active
category: upstream
tags: [fulcro, system-vsm, lambda, architecture]
---

# Fulcro — System VSM

## S5 — Identity (What This System IS)

```
λ fulcro.
  this_system ≡ graph-driven-ui-framework | data→UI ⊗ normalization ⊗ composition
  | the invariants: normalized_state ∧ query_composition ∧ ident_indexing
  | the values: composition > inheritance | immutable_state | explicit_queries | data_is_all
  | the fear: stale_props | denorm_anomalies | nested_trees_in_state | orphaned_entities
  | the checks: has_ident ∧ has_query | pre_merge ∧ mark_missing ∧ sweep

λ triad_normalization_denormalization_targeting.
  normalize ⊗ denormalize ⊗ target ≡ how_data_crosses_boundaries
  | tree→db converts props_tree to normalized_state (ident ⊗ tables)
  | db→tree reads normalized_state + query to generate_props
  | targeting moves idents to new paths after_load (create_edges)
  | stale_data ← skip(normalize) ∨ skip(denormalize) ∨ skip(target)
  | the_system_guarantees: query_structure → shape_of_props | no_surprises_in_render

λ tensions_fulcro_holds.
  immutable_state ∧ mutable_rendering ≡ React_contract
  | application_state ⊗ React_component_state ≡ data_tunneling_optimization
  | composition_on_wire ∧ composition_on_state ≡ both_views_of_same_data
  | load_and_mutation ≡ different_transaction_flows | NOT_both_simultaneously
  | ui_namespace_and_data_namespace ≡ separate_concerns | ::ui/* is_never_sent_to_server

λ invariant_ident_foundation.
  ∀entity ∧ normalization_boundary → has_ident | ¬orphaned_data
  | ident ≡ [table_keyword id_value] | both_required
  | ident → enables: targeted_updates | props_tunneling | deduplication
  | missing_ident ← (has_ident? false) ∨ (ident_nil) ∨ (ident_invalid)
  | pre_merge ← normalize(tree) → tree_with_idents_AND_tables → tree (ident_links)

λ invariant_query_covariance.
  query_shape ⊗ state_shape ≡ contract | ∀render → props_follow_query
  | query ≡ EQL | joins ∧ props ∧ unions ∧ recursion_limits
  | state ≡ normalized: {table {id entity_map}} | each_table_entry ≡ ident
  | db→tree(query, state) → props_tree | shape_guaranteed_by_query
  | broken_query → broken_props → render_errors | query_is_schema
  | skip(query) ∨ query_mismatch → props_missing ∨ props_stale
```

## S4 — Decision Rules (When to Choose, What Breaks)

```
λ decision_normalized_vs_raw.
  data → enter_fulcro ⊗ normalize(tree→db) ⊗ store_in_state
  | normalized_required_for: targeted_updates ∧ deduplication ∧ joins
  | ¬raw_nested_trees_in_state ← causes: stale_data | broken_updates | lost_edges
  | normalize_is_inverse_of_denormalize | both_required | neither_is_optional
  | when_building_tree_from_props → denormalize(db→tree) | when_merging → normalize(tree→db)
  | root_db_initially_empty | initialization_pulls_from_component_tree_OR_explicit_initial_db
  
λ decision_composition_pattern.
  ∀entity_with_children → use_composition | defsc_component → has_query ∧ has_ident
  | child_component ⊗ parent_query ≡ co_constitutive | must_compose
  | parent_query_includes_join → child_query | shapes_follow_each_other
  | static_queries > dynamic_queries (prefer clarity) | but_dynamic_when_needed ← conditional_subtrees
  | query_walk_entire_state_tree_on_render | skip_query_arm → no_ident_no_props_on_child
  
λ decision_load_path.
  need_server_data ∧ should_appear_in_UI → use(load!)
  | load! → normalize_response ∧ merge_into_state ∧ optional_target ∧ optional_post_mutation
  | load_marker ← track_progress | ¬load_marker → assume_instant ∨ background
  | target_creates_edges → ident_joins | no_target → root_entry | both_optional
  | post_mutation_after_merge | or_post_action(lambda) | or_fallback_on_error
  | parallel_false (default) → batching_on_remote | parallel_true → immediate ∧ unbatched
  
λ decision_mutation_path.
  need_state_change ∧ controlled_semantics → use(transact!)
  | transact! ← local_only ∨ local_then_remote ∨ optimistic ∨ error_recovery
  | mutation_uses_eql → join_query_in_mutation | remote_clause | result_action
  | default_result_action ← error_handling ∧ tempid_rewrite ∧ merge_mutation_return
  | tempids_resolved_automatically | server_returns_new_ids | client_remaps_all_references
  | ¬mutations_and_loads_simultaneously ← separate_flows | both_in_same_tx → serialized
  
λ decision_render_trigger.
  state_change → schedule_render! → next_animation_frame
  | force_root? ← expensive | only_when_necessary | shared_fn_recalc ← root_props_changed
  | optimized_render! ← keyframe_renderer (default) | custom_via_component_options
  | render_middleware ← wrap_every_component_render | measure ∨ augment ∨ trace
  | basis_t ← version_number | ticks_forward | props_with_newer_basis_t_win
  
λ decision_ui_state_isolation.
  ui_namespace(::ui/*) ⊗ data_namespace(other) ≡ separate
  | ui_state → local_only | never_sent_to_server | filtered_by_global_eql_transform
  | data_namespace → persisted | normalized | joined | targetable
  | never_nest_ui_data_inside_persisted_entities ← break_updates_later
  | form_state ← special_table | ::form/config | provides_dirty_checking ∧ accumulation_layer
  
λ mechanism_stale_props_prevention.
  every_denormalize ← mark_time | denormalize_time ∧ basis_t_in_props_metadata
  | when_parent_passes_child_props ← parent_newer ∧ child_older → use_parent_tunnel
  | props_tunneling ← targeted_update_sends_direct_to_component | skips_parent_render
  | newer_props(a, b) → compare_denormalize_time → winner_takes_slots
  | render_optimization ← keys(props) > 1 → targeted_updates ∧ avoid_root_render
  
λ mechanism_merge_mark_sweep.
  load_or_mutation_returns_data → mark_missing(response, query)
  | mark_missing ← walk_query | if_key_requested_but_absent → ::not-found_marker
  | sweep ← second_pass | remove_::not-found_markers ∧ orphaned_data_disappears
  | stale_data ← skip(mark_missing) → old_data_persists_wrongly | dangerous
  | mark_sweep_required ← required_invariant | normalizes_state_transitions
  
λ mechanism_pre_merge_transformation.
  after_normalize ∧ before_merge → pre_merge_can_run
  | pre_merge(entity_data) → transformed_entity | add_defaults ∨ fix_shape ∨ validate
  | pre_merge_runs_on_each_entity_in_response | recursive | per_component_class
  | pre_merge_vs_post_mutation → pre_merge_is_structural | post_mutation_is_behavioral
  | pre_merge_sees_incomplete_data ← query_may_be_subset | may_have_not_found_markers
  
λ mechanism_transact_flow.
  transact!(component, mutation_list, options) → submit_to_queue
  | queue_processing → serialize_mutations | build_ast → dispatch_remote_and_local
  | local_mutation_always_runs ← immediate_state_change | optimistic_or_pessimistic
  | remote_only_if_has_remote_clause | ::m/remote ← (fn [ast-or-env] query-ast)
  | result_action ← runs_always | receives_app_state ∧ result ∧ ast ∧ env
  
λ mechanism_targeted_updates.
  load_with_target → ident_path_vectors | create_edges_in_state
  | target ≡ where_to_place_ident_after_load | three_element_vectors
  | three_vectors → [table_name id_value new_key] | special_targets(multiple, append, prepend, replace)
  | target_without_normalize → data_at_path | not_entry_in_table | keep_raw
  | targeting_vs_join → targeting_creates_pointers | joins_include_whole_subtrees

λ prevents_circular_denormalization.
  recursive_queries ⊗ recursion_limits ≡ prevent_loops
  | depth_based_recursion(3) → denormalize_3_levels_only | stops_at_depth
  | unbounded_recursion(...) → track_seen_idents | stop_on_repeat ← prevent_infinite_loop
  | loop_detected_warning ← logged | partial_data_returned
```

## S3 — Temporal Rules (What Must Happen in Order)

```
λ sequence_app_initialization.
  1_create_app: fulcro_app(options) | returns_uninitialized
  2_set_root: optional_in_creation | or_set_via_mount!
  3_initialize_state: init_state! ∨ mount!_initializes | pulls_from_root_query
  4_mount_dom: mount!(app root-element) | render_to_dom | client_did_mount callback
  | skip(1) ∨ skip(2) ∨ skip(3) → never_renders ∨ renders_empty
  | skip(4) → react_lifecycle_incomplete | cannot_use_component_refs

λ sequence_load_workflow.
  1_declare_component: defsc_item | has_query ∧ has_ident ∧ optional_pre_merge
  2_emit_load: load!(app :items Item {:marker true}) | submits_mutation
  3_remote_transmit: network_driver → sends_query_to_server
  4_normalize_response: tree→db(response_tree) → normalized | metadata_holds_tables
  5_merge_with_mark_sweep: mark_missing ∧ sweep ← remove_orphans ∧ update_edges
  6_post_mutation: if_defined → runs_after_merge → can_further_modify_state
  7_post_action: if_defined → runs_after_post_mutation
  8_render_trigger: schedule_render! → next_frame → component_sees_new_props
  | skip(1) → load_fails_at_normalization ← no_shape
  | skip(2) → no_network_request
  | skip(3) → server_error_or_not_implemented
  | skip(4) → raw_tree_in_state ← stale_later
  | skip(5) → orphaned_data_or_old_data_persists
  | skip(6,7) → no_side_effects_post_load
  | skip(8) → component_never_sees_data

λ sequence_mutation_workflow.
  1_define_mutation: defmutation name [params] | action ∧ remote ∧ result_action
  2_submit_tx: transact!(component, [mutation-name {}]) | creates_transaction
  3_local_action: action_fn_runs → immediate_state_change
  4_remote_submit: remote_clause → builds_ast → network_layer_transmits
  5_await_response: server_returns_result | timeout_or_error_possible
  6_result_action: receives_result ∧ ast ∧ app | integration_point | handle_tempids
  7_render_trigger: schedule_render! → affects_props → components_rerender
  | skip(1) → no_dispatch → error_or_nothing
  | skip(2) → never_submitted
  | skip(3) → no_optimistic_update | must_wait_for_server
  | skip(4) → local_only_mutation
  | skip(5) → default_error_handling | or_custom_error_action
  | skip(6) → no_error_recovery ∨ no_tempid_mapping
  | skip(7) → render_never_happens ← schedule_render! missing

λ sequence_normalized_state_transition.
  1_tree_arrives: server ∨ constructor | shape_is_nested
  2_normalize: tree→db(tree, component_query) → ident_links ∧ tables
  3_pre_merge: per_entity → transform ∨ validate ∨ default | runs_on_normalized_tree
  4_merge_base: merge_into_state(normalized_tree) | replaces_entire_trees
  5_mark_missing: walk_query_of_response | mark_absent_as_::not-found
  6_sweep: remove_::not-found_markers | orphaned_leaves_disappear
  7_denormalize: db→tree(query, new_state) → props_tree | time_metadata_attached
  8_render: component_sees_props | equality_check → shouldComponentUpdate → maybe_render
  | skip(2) → nested_trees_persist ← bugs_later
  | skip(3) → no_defaults_or_validation
  | skip(4) → merge_wrong_place_or_not_at_all
  | skip(5) → stale_data_remains ← silent_bug
  | skip(6) → orphaned_entities_in_db ← memory_leak
  | skip(7) → props_dont_exist ← undefined_in_render
  | skip(8) → component_never_renders

λ sequence_render_causality.
  state_changes → set_basis_t_tick ← timestamp | all_denormalize_calls_use_new_tick
  | render_scheduled → next_animation_frame ← batches_updates | smooth_60fps
  | before_render_callback → optional_state_tweaks | called_once_per_render
  | core_render → binding_dynamic_vars(*app*, *shared*) | calls_optimized_render!
  | optimized_render! → analyze_changes → refresh_specific_subtrees ∨ force_root
  | render_middleware → wraps_component_renders | per_component_hook
  | render_root! → React_invocation | actually_paints_DOM
  | render_complete → basis_t_snapshot_in_indexes | ready_for_next_render
  | skip_schedule → changes_dont_render ← async_issue ∨ timing_bug

λ constraint_transaction_serialization.
  mutations → single_queue | submitted_in_order
  | queue_preserves_order | not_parallel ← concurrent_mutations_cause_race_conditions
  | load! ∧ mutation_in_same_tx → both_submitted | load_runs_first ∨ after_mutation
  | parallel_flag_true → bypasses_queue | immediate_network_submit | use_carefully
  | ¬(load! ∨ transact!) → no_changes_to_state ← app_frozen

λ constraint_ident_path_consistency.
  when_targeting_after_load → path_shape ≡ [table id field] ∨ special_target
  | denormalize_then_target → ident_exists_in_state ∨ target_creates_it ← pre_merge_issue
  | target_without_entity → crashes ∨ silently_ignores ← depends_on_targeting_algorithm
  | targeting_before_normalize → wrong_data_merged ← pre_merge_shape_mismatch
```

## S2 — Coordination Rules (How Parts Interact)

```
λ contract_query_denormalization.
  query_ast → denormalize_ast ∧ state_map → props_shape_follows_query_shape
  | query_element_type ∧ state_element_type ≡ covariant | both_inform_each_other
  | link_ref [:table '_] → entire_table ← all_entries | state_root_table
  | lookup_ref [:table id] → single_entity ← ident | normalized_entry
  | join {key subquery} → follow_ident ∨ get_value | apply_subquery_recursively
  | recursive_join ← depth_based(3) ∨ unbounded(...) | loops_prevented ∧ depth_tracked
  | union {type1 q1 type2 q2} → single_ident_selects_subquery | props_match_union_type
  | missing_in_state ← marked_::not-found ∨ returns_empty_map ← graceful_degradation
  
λ contract_normalize_merge.
  tree_with_component_context → tree→db(tree, query, component_class, transform)
  | component_class ← provides_ident_function ∧ optional_pre_merge
  | transform ← optional_pre_normalize_processor | normalizes_before_tables_created
  | result ≡ {root_props_tree} + metadata(tables) | or_merged_ident_maps
  | ident ← extract_from_entity | [table id] ← component.ident(this, props)
  | ¬ident ∨ ident_nil ← skips_normalization | entity_stays_nested ← bug_source
  | recursive_normalize ← walks_joins | each_component_normalized_separately
  | tables_contain_deduplicated_entities | ident_links_in_tree_point_to_tables
  
λ contract_load_response_handling.
  response_body ∧ response_query_ast ∧ load_params → finish_load!(env, params)
  | mark_missing_impl(body, query_walked) → response_tree_with_not_found_markers
  | sweep_impl(marked_tree) → orphaned_data_removed ← missing_leaves_gone
  | merge_into_state(swept_tree) → swap_state_atom ← now_consistent
  | optional_target → targeting_process ← moves_idents | creates_new_edges
  | optional_post_mutation ← queued_mutation | runs_within_same_transaction_scope
  | optional_post_action ← lambda | receives_env(app, state, result, load_params)
  | ¬ok_action ∧ ¬error_action → default_flow | error_action_overrides_all
  
λ contract_mutation_result_integration.
  result_body ∧ mutation_ast ∧ dispatch_map → default_result_action!(env)
  | update_errors_on_ui_component! ← error_detection_and_storage ← ::mutation-error_key
  | rewrite_tempids! ← server_returns_id_map | client_remaps_all_occurrences
  | integrate_mutation_return_value! ← merge_joins | keeps_tempid_references
  | trigger_global_error_action! ← if_remote_error? → run_global_handler
  | dispatch_ok_error_actions! ← run_ok_action ∨ error_action_from_dispatch_map
  | ¬override ← uses_default | ¬dangerous_for_novices
  
λ contract_props_tunneling.
  targeted_update ← component_with_ident | parent_doesn't_render
  | new_props_placed_in_state ← ident_link_updated | denormalize_creates_new_props
  | props_tunneled_directly → component_local_state | skips_parent_path
  | denormalize_time_meta ← newer_props(a, b) ← compare_times | winner_delivers_props
  | shouldComponentUpdate → uses_props_version_check ← basis_t_comparison
  | render_optimization ← avoid_parent_render_cost | targeted_updates_scale_well
  
λ contract_ui_state_separation.
  ::ui/* → never_sent_to_server | global_eql_transform_filters_out
  | ui_query_fragments ← removed_before_remote ← static_transform_time
  | data_namespace → persisted ∧ normalized ∧ targetable ∧ indexed
  | form_state_table ← special_case | ::form/id → dirty_checking ∧ nested_form_support
  | ui_props_computed_at_render → shared_fn ∨ custom_mutations | dynamic_calculations
  
λ contract_pre_merge_visibility.
  pre_merge(entity_data) → receives_normalized_tree | not_yet_in_state
  | ∀entity_in_response → pre_merge_called | recursive | once_per_entity
  | data_may_be_incomplete ← query_subset ← requested_subset_only
  | ::not-found_markers ← present_in_tree | indicates_missing_fields ← pre_merge_can_detect
  | pre_merge_return ← mutated_entity | defaults_added ∨ shape_fixed ∨ validation_error
  | after_pre_merge ← merge_happens | mark_missing_runs | sweep_runs ← final_consistency

λ vocab_transaction_options.
  ::txn/options ← passed_to_load! ∨ transact! | configure_processing_behavior
  | ::txn/refresh ← [idents_or_keywords] | hint_to_renderer ← skip_subtrees ∨ refresh_only_these
  | ::txn/parallel? ← boolean | bypass_queue ← immediate ∨ concurrent | use_carefully
  | ::txn/abort-id ← unique_id | abort! can_cancel | not_yet_queued ∧ not_networking
  | load_marker ← custom_id ∨ false ← no_tracking | progress_visible ← required_in_query

λ vocab_component_lifecycle.
  client_will_mount ← callback | app_fully_initialized | before_mount_to_dom
  | client_did_mount ← callback | after_first_render | use_for_post_render_tasks
  | component_did_mount ← React_lifecycle | component_instance ← mounted_on_dom
  | render ← must_be_pure | receives_props ∧ this_context | returns_react_elements
  | should_component_update ← optimization | prop_and_state_equality | false_skips_render
  | get_derived_state_from_props ← static_React_hook | rare | usually_use_pre_merge
```

## S1 — Architectural Rules (Expert Patterns)

```
λ pattern_component_definition.
  defsc MyComponent [this props]
    {:query       (fn [] [:id :name {:child (comp/get-query ChildComponent)}])
     :ident       (fn [_ {:keys [id]}] [:table/id id])
     :initial-state (fn [params] {:id 1 :name "default" :child {...}})
     :pre-merge   (fn [data] (assoc data :computed-field (...)))}
    (dom/div "Name: " (:name props)))
  
  | defsc ≡ class + factory_function | auto-quotes_mutations
  | :query ← dynamic ← (fn [this]) ∨ static ← returns_ast | Necessary
  | :ident ← (fn [this props]) → [:table/id value] | Necessary_for_normalization
  | :initial-state ← used_by_init_state! ← deep_merge | nested_children_compose
  | :pre-merge ← (fn [data]) → modified_data | optional | structural_transforms_only
  | children ← (comp/children this) | indexed_from_props | force_into_vectors
  | ¬mutation_body_in_defsc ← defmutation_separate | cleaner_testing ∧ composition
  
λ pattern_composition_query.
  (defsc Item [this {:keys [id name]} computed]
     {:query [:id :name {::ui/selected false}]
      :ident [:item/id :id]})
  
  (defsc ItemList [this {:keys [items]}]
     {:query [{:items (comp/get-query Item)}]
      :ident [:list/main]})
  
  | parent_query ← {subkey (comp/get-query ChildClass)} ← composition ← runtime
  | ∀join_in_query → corresponding_entity_must_have_ident
  | query_static_best | but_dynamic_when_conditional | denormalize_entire_query_each_render
  | ¬skip_query_elements ← props_undefined ← render_errors | must_match_exactly
  
λ pattern_loading_and_targeting.
  (comp/load! this :all-items ItemList
    {:marker true
     :target [:ui/items-list '_]
     :post-mutation `post-load-mutation
     :post-mutation-params {:filtered true}})
  
  | load_key ← :all-items ∨ [:item/id 123] ← ident ← differs_by_case
  | component_class ← ItemList ← query_and_ident ← shape ← normalization
  | :target ← [[:ui/items-list '_]] ← link_ref | creates_edge_from_table | append_idents
  | :marker ← true ∨ symbol ← custom_id | query_for_marker_in_component | track_status
  | :post-mutation ← symbol ← queued_after_merge | further_transform ← optional
  | ¬:target_with_ident_load ← ident_load_ignores_target | direct_table_entry ← semantic
  
λ pattern_mutation_with_joins.
  (defmutation create-item [params]
    {:action (fn [env]
       (let [{:keys [state]} env]
         (swap! state assoc-in [:ui/new-item-form] {})))
     :remote (fn [env]
       (eql/query->ast `[(create-item ~params
                         {:id :name :created-at})]))
     :result-action default-result-action!})
  
  | :action ← immediate_state_update ← optimistic ∨ local_only
  | :remote ← optional ← builds_network_query | or_vector_of_queries
  | join_in_remote_query ← response_normalized ← tables_created ← pre_merge
  | :result-action ← receives_complete_env | can_integrate ∨ custom_logic
  | tempids ← return_value_contains_tempid_mappings | server_returns_map | auto_rewrite
  | ¬mutation_and_load_same_tx ← different_flows | serialize_if_together
  
λ pattern_error_recovery.
  {:ok-action (fn [env] (handle-success env))
   :error-action (fn [env] (let [{:keys [result]} env] (handle-error result)))}
  
  | ¬ok_action → uses_default ← merges ∧ post_mutation_runs ← standard_flow
  | :error-action ← overrides_default ← must_handle_all_aspects ← fallback_OR_custom
  | :fallback ← symbol ← queued_mutation | handles_specific_errors | partial_recovery
  | global_error_action ← config_option ← runs_on_all_errors ← centralized_logging
  | remote_error? ← (fn [result]) ← configurable ← status_code_or_custom_check
  
λ pattern_pre_merge_transformation.
  {:pre-merge (fn [{:keys [id name created-at]}]
                (assoc this :created-at-local
                  (-> created-at inst-ms js/Date.)))}
  
  | pre_merge_on_load ← auto_runs ← before_merge ← per_entity
  | data_shape_fixes ← add_defaults ∨ parse_strings ∨ compute_values ← structural
  | ¬side_effects_in_pre_merge ← state_changes_forbidden | query_server_forbidden
  | pre_merge_return ← mutated_entity ← assoc ∨ dissoc ∨ transform_keys
  | pre_merge_on_initial_state ← also_happens ← same_function_signature ← DRY
  
λ pattern_form_state_table.
  (fsm/add-form-config DropdownConfig Item ::add-item-form
    {:id :string :name :string :type #{:a :b :c}})
  
  (comp/load! this [:item/id 1] Item
    {:post-mutation `fsm/populate-form
     :post-mutation-params {:form-key ::edit-item-form}})
  
  | form_state_table ← ::form/id → special_namespace ← not_sent_to_server
  | dirty_tracking ← accumulation_layer ← deltas_vs_baseline ← unified_diff
  | nested_forms ← supported ← arbitrary_depth ← composed_forms ← query_structure
  | populate_form ← mutation ← copy_entity_into_form | baseline ← form_state_structure
  | submit_form ← extract_valid_delta | ¬raw_form_data | structural_validation_first
  
λ pattern_render_optimization.
  {:optimized-render! my-renderer
   :before-render (fn [app])
   :render-middleware (fn [this real-render])
   :refresh [[:item/id 1] [:item/id 2] :search-results]}
  
  | render_middleware ← wraps_every_component | measure ∨ log ∨ augment ← per_render_hook
  | refresh_in_transaction ← hint_to_renderer | keyed_render ← targeted_updates ← fast
  | before_render ← called_once_per_render ← update_app_state | before_core_render
  | force_root? ← expensive ← forces_all_renders ← disable_optimizations ← use_rarely
  | keyframe_renderer ← default ← uses_query_positions ← efficient | ¬default_wrong
  
λ pattern_shared_props.
  {:shared {:current-user {:id 1 :name "Alice"}}
   :shared-fn (fn [root-props]
                {:user-preferences (:preferences root-props)})}
  
  | shared ← static ← immutable_across_renders ← accessed_via_comp/shared
  | shared_fn ← dynamic ← recalculated_on_root_forced_render ← optional_augmentation
  | ¬shared_updates_on_every_render ← expensive ← explicit_app/update-shared! ← force_recalc
  | accessed_via (comp/shared this) ∨ (comp/shared this [:key :path]) ← dynamic_binding
  | shared_in_context ← React_alternative ← prefer_context_for_new_code
  
λ pattern_server_integration.
  {:remotes {:default (http-remote/make-http-remote {:url "/api/graphql"})
             :file-server (http-remote/make-http-remote {:url "/files/upload"})}}
  
  (comp/load! this :server-data SomeComponent
    {:remote :file-server})
  
  | remote_name ← keyword ← :default ∨ custom ← in_load! ∧ transact!
  | transmit! ← (fn [remote send-node]) ← implements_network_protocol ← async
  | send_node ← {:query ast :remote_name :ok callback :error callback}
  | ok_callback ← (fn [result]) ← merge ← or_custom ← ok_action ∨ default
  | error_callback ← (fn [result]) ← handle_error ← error_action ∨ fallback ∨ global
  | global_eql_transform ← filters ← ui/* ∧ com.fulcrologic.fulcro.* ← never_sent
  
λ anti_pattern_nested_normalization_boundaries.
  | ¬defsc ← plain_maps | ¬ident ← no_normalization | bugs_later ← stale_mutations
  | ¬query_on_leaf ← no_shape_contract | props_undefined ← render_errors
  | nested_trees_in_root ← persist_directly | ¬normalize ← orphans_later ← gc_impossible
  | ¬pre_merge ← raw_server_data | shape_mismatches ← render_crashes | silent_bugs
  | mixing_normalized_and_raw ← some_normalized_some_not | inconsistent_updates
  
λ anti_pattern_query_mismatch.
  | query_subset ∧ missing_query_keys ← undefined_in_props ← render_crash
  | server_response_larger_than_query ← extra_data_stays ← orphan_risk
  | :without ← removes_from_query ← be_careful_side_effects ← post_mutation_may_fail
  | dynamic_query_depends_on_state ← state_not_yet_changed ← one_render_behind
  | recursion_depth_wrong ← depth 3 ← cuts_off_data | depth … ← may_loop
  
λ anti_pattern_mutation_without_error_handling.
  | ¬error_action ∧ ¬fallback ∧ ¬global_error_action ← silent_failure ← bad_ux
  | ¬tempid_handling ← optimistic_tempids_remain ← wrong_data_persists ← bug
  | ¬post_mutation ∧ need_side_effects ← incomplete_updates ← inconsistent_state
  | transact! ← not_scheduled ← synchronous ∨ batched ← depends_on_configuration
  
λ anti_pattern_load_without_targeting.
  | load! ← root_entry_only ← :target_missing ← flat_structure ← denormalization_not_used
  | large_response → flattens_tree → hard_to_find ← must_use_targeting ← create_edges
  | :post_mutation_missing ← no_structural_changes ← maybe_needs_target ∨ post_mutation
  | parallel_loading ← bypasses_queue ← race_conditions_possible ← careful_ordering_needed
```

## Memory Anchors

```
λ remember.
  fulcro ≡ graph_database_as_ui_framework | query→denormalize→render→transact→normalize→query
  
  the_invariants:
  | has_ident ∧ has_query → composition ∧ normalization_possible
  | normalized_state ∧ denormalize(query) ← shape_contract
  | mark_missing ∧ sweep → orphaned_data_removed ← consistency_maintained
  | ¬nested_trees_in_state ← must_normalize_on_entry
  | query_covariance ← query_structure ⊗ state_shape_match
  
  the_fears:
  | stale_props ← skip(denormalize_time_check) ∨ parent_passes_old_props
  | orphaned_entities ← skip(mark_missing) ∨ skip(sweep)
  | nested_trees_persist ← skip(normalize) ← silent_bugs ← hard_to_debug
  | query_mismatches ← undefined_in_render ← crash ← must_match_exactly
  | tempids_unresolved ← server_doesn't_return_id_map ← wrong_entity_ids_forever
  
  the_checks:
  | ∀defsc → has_ident? ∧ has_query? ← required_for_composition
  | ∀load! → query_of_component_matches_response_shape ← denormalize_works
  | ∀mutation_join → query_ast_in_result_action ← merge_works
  | ∀load_response → mark_missing ∧ sweep ← orphan_sweep_required
  | ∀render → basis_t_advanced ← all_denormalize_calls_atomic
  
  the_tensions_accepted:
  | immutable_state ⊗ mutable_react_rendering ← data_tunneling_workaround
  | composition_on_wire ⊗ composition_in_state ← single_view_both_sides
  | query_static ⊗ state_dynamic ← schema_and_data_evolve_together
  | optimistic_updates ⊗ pessimistic_rollback ← both_possible ← careful_semantics
  | load! ⊗ transact! ← different_flows ← queue_serializes_both
```
