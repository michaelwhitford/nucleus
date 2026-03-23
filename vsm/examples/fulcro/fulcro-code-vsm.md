---
title: "Fulcro"
status: active
category: upstream
tags: [fulcro, vsm, lambda, generated]
---

# Fulcro — Lambda Document

Extracted from 74 files, 23432 lines. Namespace: `com.fulcrologic.fulcro`

## Require Map

```clojure
[com.fulcrologic.fulcro.application :as app]
[com.fulcrologic.fulcro.components :as comp]
[com.fulcrologic.fulcro.mutations :as m]
[com.fulcrologic.fulcro.data-fetch :as df]
[com.fulcrologic.fulcro.routing.dynamic-routing :as dr]
[com.fulcrologic.fulcro.ui-state-machines :as uism]
[com.fulcrologic.fulcro.react.hooks :as hooks]
[com.fulcrologic.fulcro.algorithms.data-targeting :as targeting]
[com.fulcrologic.fulcro.algorithms.merge :as merge]
[com.fulcrologic.fulcro.algorithms.normalize :as fnorm]
[com.fulcrologic.fulcro.algorithms.denormalize :as fdn]
[com.fulcrologic.fulcro.algorithms.indexing :as indexing]
[com.fulcrologic.fulcro.algorithms.tempid :as tempid]
[com.fulcrologic.fulcro.server.api-middleware :as api-mw]
[com.fulcrologic.fulcro.networking.http-remote :as http]
[com.fulcrologic.fulcro.offline.durable-mutations :as offline]
```

## S5 — Identity

```
λ fulcro.
  state ≡ normalized_graph_db | root_tree_query ⟶ denormalize ⟶ component_props
  | components ≡ react_classes | queries_compose | idents_join
  | mutations ≡ client_side_operations | can_target_state | optional_server_side_effect
  | loading ≡ managed | markers_track_load_state | ui_controls_display
  | routing ≡ composable | targets_components | deferred | will-enter/will-leave
  | state_machines ≡ actors ∧ event_dispatch | handles_async | manages_workflows
  | network ≡ request_response | remotes_named | default_error_handling

λ design.
  | normalization ≡ single_source_of_truth | graph_db | idents_are_pointers
  | composition ≡ queries_nest | fragments_reuse | trees_to_graphs
  | idempotence ≡ denormalization_time_tracked | props_tunneling | newer_props_selected
  | demand_driven ≡ dynamic_queries | load! | refresh! | components_control_fetch
```

## S4 — Intelligence (API Shapes)

### Components

```
λ defsc. [class-name doc-string arg-vector options* render-fn]
  class_ident ≡ options :ident
  | result ≡ class_or_symbol | use_directly_in_queries | factory | computed_factory

λ factory(component-class). [props-map] | [props-map children]
  → react_element | component_instance_created
  | preserves_initialization_state | ident_computed

λ computed-factory(component-class options-map). [props-map] | [props-map children]
  → react_element | computed_properties_attached
  | meta(:computed true) | shallow_merge_with_computed_props

λ use-fulcro. []
  → {::comp/app app, ...} | react_hook | must_be_in_render
  | provides_app_context | for_hooks_components_only

λ defnc. [name doc-string query-or-join]
  → normalizing_component | ¬rendering | works_in_queries
  | keyword ≡ shortcut_ident | vector ≡ query_properties

λ get-query(component [state-map]). query_ast | nil
  → eql_ast | respects_dynamic_queries | state_map_optional_for_ui_only_paths

λ query(component). query_form
  → query_vector_or_nil | static_component_query | ¬dynamic

λ ident(component props). ident_vector | nil
  → [keyword id] | pointer_to_graph_db | optional_computed_value

λ initial-state(component). state_map | lazy_map
  → creation_form | denormalized | ident_present | merges_into_db_graph

λ transact!(component-or-app txn-vec [options-map])
  → queued_tx | async | returns_immediately | uses_tx_processing_queue
  | options ≡ {:optimistic? :remote? :abort-id}
  | mutations_in_vec | returns_promises_if_await

λ transact!!(component-or-app txn-vec)
  → same_as_transact! | with_await | cljs_only | blocks_on_completion

λ set-state!(component new-state-map). nil
  → local_only | ¬affects_server | component_mount_required

λ get-state(component [path-vec])
  → state_value | deep_or_shallow | nil_if_missing

λ set-query!(component new-query [options-map])
  → dynamic_update | causes_rerender | refreshes_load_markers
  | component_update_only | use_refresh! for_automatic_reload

λ refresh-dynamic-queries!(). nil
  → force_refresh_all_dynamic_queries | global_operation | for_manual_control

λ component-instance?(x). boolean
  → true_if_mounted_react_component | false_for_classes_and_factories

λ component-class?(x). boolean
  → true_if_defsc_or_defnc | false_for_instances_and_factories

λ has-feature?(component :keyword). boolean
  → :ident :initial-app-state :query :pre-merge :route-segment

λ children(component). seq | lazy
  → component_children | react_fragments_flattened | converts_lazy_seqs_to_vectors

λ mounted?(component). boolean
  → true_if_lifecycle_bound_to_tree | false_if_unmounted_or_props_only

λ is-factory?(x). boolean
  → true_if_component_factory_created_by_factory_or_computed_factory

λ fragment(display-name query). react_fragment
  → renders_nothing | returns_element_for_query_composition

λ isoget(js-obj key [default]). value
  → works_with_js_objects | javascript_and_clj_compatible | get_fallback

λ isoget-in(js-obj kv-seq [default]). value
  → nested_version | isomorphic | clj_and_cljs_compatible
```

### Application

```
λ fulcro-app(options-map)
  → app_instance | stateful | mounted_on_dom_node
  | options: :app-name :root :shared-fn :remotes :remote :props-middleware :render-middleware
  | :: app

λ headless-synchronous-app(options-map)
  → app_instance | synchronous_tx | no_render | server_render_ssr
  | :: app

λ mount!(app root-element-dom-selector)
  → mounted | react_root_created | rendering_active | remount! to_change_root

λ set-root!(app root-component-class [options-map])
  → changes_root | re_render | options ≡ {:app-root :tx}

λ root-class(app). component_class | nil
  → current_root | query_from_here

λ app-root(app). dom_element | nil
  → mounting_target | where_react_renders_to

λ unmount!(app). nil
  → stops_rendering | preserves_app_state | remount! to_resume

λ remount!(app). nil
  → resumes_rendering | calls_remount_react | preserves_state_and_network

λ force-root-render!(app). nil
  → causes_immediate_rerender | forces_denormalization | use_sparingly

λ current-state(app-or-component). state_map
  → app_state_atom_deref | works_during_render_via_dynamic_binding | immutable

λ tick!(app). pos_int
  → basis_t_increment | internal_use | denormalization_time_management

λ basis-t(app). pos_int
  → monotonic_version | tracks_renders | denormalization_tracking

λ update-shared!(app). nil
  → recalculates_shared_props | runs_shared_fn | affects_future_renders
  | ¬rerenders_existing | use_force_root_render! to_render_immediately

λ initialize-state!(app). nil
  → merges_initial_app_state_into_db | idempotent | one_time_setup

λ set-remote!(app remote-name remote-handler). nil
  → add_or_update_remote | handler_implements_remote_protocol

λ get-remote(app remote-name). handler | nil
  → retrieves_remote_by_name | default_named_:remote

λ add-render-listener!(app listener-fn). nil
  → listener ≡ (fn [app rendered] nil) | called_after_each_render
  | app.keys: :root-class :state-atom :basis-t :shared-props

λ remove-render-listener!(app listener-fn). nil
  → unregisters_listener | listener_becomes_inactive

λ abort!(app abort-id). nil
  → cancels_in_flight_mutations | matching_optimistic_updates_reverted
  | paired_with_transact! {:abort-id id}
```

### Mutations & State Management

```
λ defmutation. [name doc-string [params] action* remote*]
  → mutation_record | invoke_returns_mutation_expr | auto_quoted
  | action ≡ (fn [env] ...) | receives_env_with_{:app :state :ast}
  | remote ≡ (fn [env] env) | reads_env | returns_env_to_send_request
  | mutation_symbol_namespaced_to_ns

λ mutation-declaration?(expr). boolean
  → true_if_defmutation_created_type | false_for_regular_lists

λ returning(mutation-form component-class). mutation_form
  → adds_return_targeting | component_auto_focuses_after_update
  | targets_join_in_component_query | ¬required

λ with-target(mutation-form target-ast-path). mutation_form
  → add_targeting_to_remote_result | denormalized_into_path
  | commonly_used_with_returning | [root :person/by-id 1 :ui/form]

λ with-params(env new-params-map). env
  → modifies_ast_params | used_in_remote_section | affects_server_request

λ update-errors-on-ui-component!(env [error-key]). env
  → detects_remote_error | places_on_component_ref | swaps_app_state

λ toggle!(component-or-app ref [path]). nil
  → mutation | inverts_boolean_at_path | component_or_app_optional

λ toggle!!(component-or-app ref [path]). nil
  → eager_version | awaitable | cljs_only

λ set-value!(component-or-app ref new-value [path]). nil
  → mutation | updates_value | triggers_full_render | component_can_be_omitted

λ set-value!!(component-or-app ref new-value [path]). nil
  → eager_version | awaitable | cljs_only

λ set-string!(component-or-app ref string-value [path]). nil
  → mutation | coerces_to_string | typically_for_text_inputs

λ set-string!!(component-or-app ref string-value [path]). nil
  → eager_version | awaitable | cljs_only

λ set-integer!(component-or-app ref int-value [path]). nil
  → mutation | coerces_to_integer | validates_numeric | typically_for_number_inputs

λ set-integer!!(component-or-app ref int-value [path]). nil
  → eager_version | awaitable | cljs_only

λ set-double!(component-or-app ref double-value [path]). nil
  → mutation | coerces_to_double | validates_numeric | typically_for_decimal_inputs

λ set-double!!(component-or-app ref double-value [path]). nil
  → eager_version | awaitable | cljs_only
```

### Data Fetching

```
λ load!(component-or-app fetch-path target [options-map])
  → schedules_load | returns_immediately | marker_tracks_status
  | fetch_path ≡ keyword | composed_query_from_component
  | target ≡ target-ast-path | where_data_joins_into_tree
  | options: :remote :params :post-action :error-action :marker-path :without :only :post-mutation

λ load-field!(component field-key [options-map])
  → shorthand_for_load! | infers_target_from_ident | component_required
  | field_key ≡ join_key_in_query | auto_targets | options: :remote :params :post-action :error-action

λ refresh!(component-or-app fetch-path [options-map])
  → reload_existing_data | targets_existing_location | component_tree_must_exist
  | respects_dynamic_query_changes | creates_fresh_load_marker

λ data-state?(x). boolean
  → true_if_load_marker | false_for_regular_data | has :status key

λ load-marker?(x). boolean
  → alias_for_data_state? | preferred_name

λ ready?(marker). boolean
  → true_if_status :ready | false_otherwise | before_load_starts

λ loading?(marker). boolean
  → true_if_status :loading | false_otherwise | during_load

λ failed?(marker). boolean
  → deprecated | use :error-action | manual_state_management

λ marker-table. keyword
  → :ui.fulcro.client.data-fetch.load-markers/by-id | include_in_component_query
  | [df/marker-table '_] | enables_loading_indicator_rendering

λ set-load-marker!(app marker-path marker-data). nil
  → internal_use | manually_set_marker | swap! app_state

λ remove-load-marker!(app marker-path). nil
  → internal_use | clears_marker | triggers_render

λ finish-load!(env marker-path-or-paths [marker-data]). env
  → marks :status :done | call_from_remote_handler | returns_env

λ load-failed!(env marker-path-or-paths error-data). env
  → marks_failure | stores_error | triggers_error_action | returns_env

λ elide-query-nodes(query predicate-fn). query
  → removes_nodes | :without_keys_from_load | tree_query_manipulation
```

### Dynamic Routing

```
λ defrouter. [name doc-string route-targets-map options*]
  → router_component | route-segment_mandatory | route-targets_required
  | composable_at_any_level | controls_visibility_of_targets
  | options: :router-targets :ident

λ route-segment(component). vector | nil
  → [string | keyword string | keyword ...] | path_within_router
  | keywords_become_variables | strings_literal | extracted_from_route-segment_option

λ route-target?(component). boolean
  → true_if_route-segment_option_present | false_otherwise

λ will-enter(component route-params)
  → promise_or_immediate_result | async_loading_supported | side_effect_free
  | (route-immediate ident) | (route-deferred ident)
  | route_params ≡ keyword_values_from_path | strings_coerced

λ route-immediate(ident). route_result
  → route_ready_now | component_already_has_data | no_load_needed

λ route-deferred(ident). route_result
  → route_loading_pending | call_target-ready! | before_rendering

λ will-leave(component props)
  → boolean | allow_navigate | false_prevents_route_change

λ get-allow-route-change?(component). fn | nil
  → returns_will_leave_function | optional | used_during_route_transition

λ target-ready!(app router-id target). nil
  → signals_deferred_load_complete | calls_target-ready_mutation
  | paired_with_route-deferred | required_for_deferred_routes

λ current-route(app [router-target-class]). vector | nil
  → path_components | root_path_if_no_arg | nil_if_unmounted

λ change-route!(app path-vector [options-map]). nil
  → navigates_to_path | triggers_will_enter | triggers_will_leave
  | options: :tx :on-navigate | async_if_deferreds_present

λ change-route-relative!(app relative-path). nil
  → navigation_relative_to_current | up_or_down_tree

λ retry-route!(app). nil
  → retries_deferred_route | call_from_route_failed | async

λ router?(component). boolean
  → true_if_router_or_defrouter_class | false_otherwise

λ route-handler. env
  → automatically_called | state_machine_handler | internal_use

λ all-reachable-routers(app state-map [target-class]). vector
  → all_active_routers | starting_from_target_class | traverses_tree

λ validate-route-targets(root-class [options-map]). [errors] | nil
  → compile_time_router_check | catches_routing_issues | options: :verbose
```

### State Machines & Async

```
λ defstatemachine. [id doc-string states-map options*]
  → state_machine_definition | register_automatically | workflow_automation
  | options: :initial-state-data :env-key

λ trigger!(app state-machine-id event-key-or-name [event-data])
  → sends_event | async | processed_by_handler | queued

λ asm-active?(app state-machine-id). boolean
  → true_if_machine_running | false_if_stopped_or_removed

λ current-state-and-actors(app state-machine-id). {state actors}
  → {::uism/state-value ::uism/actor-names} | snapshot | returns_map

λ add-uism!(app machine-id machine-definition [initial-state-data])
  → starts_state_machine | actors_activated | queue_initial_data

λ remove-uism!(app machine-id). nil
  → stops_state_machine | deactivates_actors | cleanup

λ uism-transact(state-machine-id event-data [options])
  → transacts_from_machine_context | invoked_from_handler | returns_env
```

### React Hooks Integration

```
λ use-fulcro(). {::comp/app ...}
  → react_hook | provides_app_ref | must_be_in_render | error_if_no_provider

λ useState(initial-value). [value set-fn]
  → react.useState | wraps_hook | cljs_clj_compatible

λ use-state(initial-value). [value set-fn]
  → alias_for_useState | preferred_name | idiomatic_clojure

λ useEffect(dep-array setup-fn). undefined
  → react.useEffect | runs_after_render | cleanup_fn_optional

λ use-context(react-context). value
  → react.useContext | retrieves_context | must_be_in_render

λ use-reducer(reducer-fn initial-state). [state dispatch-fn]
  → react.useReducer | wraps_hook | for_complex_state

λ use-callback(fn dep-array). fn
  → react.useCallback | memoized_fn | for_event_handlers

λ use-memo(fn dep-array). result
  → react.useMemo | memoized_result | expensive_computation

λ use-ref(initial-value). {:current value}
  → react.useRef | persistent_ref | ¬triggers_render

λ ref-current(ref-obj). value
  → dereference_ref | read_current_value | idiomatic

λ set-ref-current!(ref-obj new-value). nil
  → updates_ref | direct_mutation | ¬triggers_render

λ use-gc(cleanup-fn). nil
  → garbage_collector_hook | runs_on_unmount | polyfill_hook

λ use-uism(app machine-id). machine_context
  → provides_state_machine_context | in_render | nil_if_inactive
```

### Algorithms

```
λ db->tree(query-eql state-db [root-entity]). denormalized_tree
  → denormalization | query_to_props | ¬modifies_state | root_entity_optional

λ tree->db(root-query denormalized-tree). normalized_graph
  → normalization | denormalized_to_graph | produces_idents | reusable_data

λ merge!(app-instance denormalized-tree [options])
  → merges_into_graph | denormalization_time_tracked | idempotent_if_timestamps_same
  | options: :merge-joins? :remove-missing?

λ merge-component!(app-instance component-class denormalized-data [ident])
  → convenience | infers_ident | updates_db | ¬rerenders

λ merge-component(state-map component-class data [ident]). new_state_map
  → pure | returns_updated_state | ¬mutates

λ index-root!(app root-class). nil
  → builds_indexes | internal_use | required_for_routing_targeting

λ index-component!(app-state component-class ident). nil
  → updates_indexes | called_from_merge | tracks_component_positions

λ drop-component!(app-state ident). nil
  → removes_component | cleans_indexes | marks_missing

λ ident->components(app ident). [classes]
  → find_components_using_ident | used_by_targeting | for_messaging

λ ident->any(app ident). instance
  → find_first_mounted_instance | for_messaging | nil_if_unmounted

λ class->any(app component-class). instance
  → find_first_instance_of_class | shorthand

λ class->all(app component-class). [instances]
  → find_all_instances | for_messaging_broadcast

λ is-ui-query-fragment?(eql-key). boolean
  → true_if_ui_only_key | false_for_server_data | namespace_check

λ tempid(optional-id-str). tempid
  → generates_client_side_id | portable_to_server | unique | optional_hint_string

λ is-tempid?(x). boolean
  → true_if_generated_by_tempid | false_for_server_ids

λ get-tempid(app tempid-or-ident [original-ident]). server_id | nil
  → maps_client_to_server | lookup_after_remote_save | optional_context

λ with-time(denormalized-tree time-integer). tree_with_metadata
  → attaches_denormalization_timestamp | used_internally | idempotency_tracking
```

### Server Integration

```
λ wrap-fulcro-request(handler). handler
  → middleware | reads_request_body | parses_transit | adds_reader_context

λ wrap-fulcro-response(handler). handler
  → middleware | serializes_response | transit_encoding | wraps_response_body

λ extract-response(response-body). {queries mutations}
  → parses_server_response | expects_transit | returns_{:queries :mutations}

λ fulcro-http-remote(options-map). remote_handler
  → default_http_remote | options: :url :request-middleware :response-middleware
  | implements_remote_protocol | request_response_capable
```

### Networking

```
λ fulcro-http-remote(options). remote_handler
  → http_network_remote | ::app :remote | sends_requests_to_server
  | options: :url (required) :request-middleware :response-middleware :fetch-fn
  | request_body ≡ transit_encoded_queries_mutations | response ≡ transit_queries_mutations

λ mock-server-remote(query-handler-map mutation-handler-map options). remote_handler
  → test_remote | simulates_server | query_fn_receives_{:query :params}

λ tenacious-remote(remote-impl). remote_handler
  → wraps_remote | auto_retry | exponential_backoff | configurable_max_retries

λ file-upload(options). remote_handler
  → file_upload_remote | multipart_form_data | file_input_capable
```

## S3 — Lifecycle

```
λ app_initialization.
  1_create_app: (app/fulcro-app {:root Root}) | state_created
  2_add_remotes: (app/set-remote! app :server remote-handler) | network_configured
  3_mount: (app/mount! app "#app") | dom_ready | rendering_starts
  4_initialize_state: (app/initialize-state! app) | loads_initial_app_state | merges_to_db
  | skip(1) → app_not_mounted | skip(4) → initial_state_not_merged

λ component_lifecycle.
  1_factory: (factory Component props) | instance_created
  2_initial_state: component.initial-state_merge | graph_normalization | ident_assigned
  3_render: render_fn_executes | props_denormalized | children_composed
  4_hooks: use-fulcro_called_if_hooks | context_available | all_hooks_ready
  5_mounted: (mounted? component) | lifecycle_bound | targeting_available
  | skip(2) → computed_factory_computed_props_injected | skip(5) → not_yet_mounted_or_unmounting

λ mutation_lifecycle.
  1_define: defmutation | auto_registered | can_be_invoked
  2_transact: (comp/transact! component [(mutation-name params)]) | queued | async
  3_action_runs: action_fn_executes | state_updated | optimistic_or_deferred
  4_remote_sends: remote_fn_executes | mutation_expr_sent | http_request_made
  5_response_handled: result_action_runs | state_updated | component_rerendered
  | skip(3) → no_action_section | skip(4) → no_remote_section | skip(5) → no_result_action

λ load_lifecycle.
  1_call_load: (load! component field-key target) | marker_created | ready_status
  2_marker_renders: loading? queries_component | conditional_ui | status_visible
  3_fetch_executes: remote_handler_invokes | network_request_sent | async
  4_result_arrives: finish-load! called | marker_status_done | data_denormalized
  5_merge_executes: merge! runs | tree_joins_data | component_rerendered
  | skip(2) → no_marker_in_query | skip(4) → error_instead_finish_load!

λ route_lifecycle.
  1_change_route: (change-route! app [:router :target :page]) | event_triggered
  2_will_enter: will-enter_called | side_effect_free | return_immediate_or_deferred
  3_route_deferred_wait: (route-deferred ident) → blocks_render | load_or_async | state_machine_handling
  4_target_ready: (target-ready! app router-id target-ident) | resumes_render | denormalized
  5_will_leave_prev: will-leave_on_old_target | can_veto_route | must_return_true | ¬side_effects
  6_render_target: target_component_visible | new_route_rendered
  | skip(3) → route-immediate_used | skip(5) → target_not_leaving_tree
```

## S2 — Coordination

```
λ edge(app_state, denormalized_tree).
  app_state ≡ {root-key data, :person/by-id {1 {...}}} | normalized_graph
  denormalized_tree ≡ (db->tree query app_state) | query_driven
  ← merge! → app_state_updated → rendering_queued

λ edge(component_query, denormalization).
  component_query ≡ [:person/id :person/name {:person/address [...]}]
  denormalization ≡ walk_query | follow_idents | populate_from_graph_db
  → props_tree ≡ denormalized_form | read_by_component | shallow_copy

λ edge(mutation_action, state_mutation).
  mutation_action ≡ (fn [{:keys [state]}] (swap! state ...))
  state_mutation ≡ application_state_atom | immutable_swap | generates_new_version
  → basis_t_increment | denormalization_time_updated | render_scheduled

λ edge(load_remote, targeting).
  load_remote ≡ fetch_data_from_server | returns_denormalized_tree
  targeting ≡ [:root :person/by-id id :person/home-address]
  → merge! with_target → data_joins_into_location

λ edge(route_will_enter, deferred_loading).
  route_will_enter ≡ side_effect_free | returns_route_result
  deferred_loading ≡ load! | state_machine_trigger! | async_workflow
  → (route-deferred ident) → blocks_route_until → (target-ready! ...) | resumes

λ edge(component_ident, graph_pointer).
  component_ident ≡ [:person/id 123] | unique_in_graph | returned_by_get-ident
  graph_pointer ≡ follow_ident_in_state_db → retrieve_entity | denormalize_subtree
  → props_supplied_to_component | component_instance_identity

λ edge(render_cycle, basis_t).
  render_cycle ≡ react_render_requested | from_state_change | from_external_event
  basis_t ≡ monotonic_version | tick!_increments | tracks_denormalization_staleness
  → newer_props_selected | via_denormalization-time | optimizes_tree_reuse

λ edge(hooks_component, use_fulcro).
  hooks_component ≡ clojure_fn | react_functional_component | via defnc
  use_fulcro ≡ provides_app_context_inside_render | returns_{::comp/app}
  → component_can_call_transact! | can_access_current_state | integrated_with_hooks

λ edge(dynamic_query, set_query).
  dynamic_query ≡ query_changes_based_on_state | visible_component_union
  set_query! ≡ updates_query | refreshes_markers | rerenders | component_must_exist
  → denormalization_recomputed_from_new_shape | data_refetched_if_changed

λ edge(state_machine, uism_trigger).
  state_machine ≡ defstatemachine | actors_activated | events_handled | workflow_orchestration
  uism_trigger ≡ (trigger! app machine-id :event-key) | queued | async_processed
  → state_updated | transactions_queued | handler_executed_in_reducer_context
```

## S1 — Operations (API Shapes)

### Component Definition & Querying

```
λ defsc(class-name doc-string [this props] options... render-fn)
  | (= [this props] component_arg_vector | must_be_destructured)
  | options → {:ident (fn [] [...]) :query (fn [] [...]) :initial-state {...} ...}
  | return → react_element | factory_creates_instances | direct_class_use_in_queries

λ get-ident(component-class props). [keyword id]
  | multimethod_on_component | called_during_normalization | ident_for_graph_pointer

λ component-options(component-class keyword). value
  | map_lookup | :ident :query :initial-state :pre-merge :route-segment etc.
  | used_to_extract_metadata

λ computed(props-map computed-map). merged_props
  | shallow_merge | computed_meta_preserved | for_supplemental_client_data
  | {::comp/computed true :computed_key computed_value ...}
```

### State & Props

```
λ get-computed(props keyword [default]). value
  | retrieves_computed_metadata | ¬denormalized | safe_extraction

λ get-unqualified-ident(component-class props). [keyword id]
  | simplified_ident | when_id_already_known | convenience

λ any->app(component-or-app). app
  | extracts_app_from_instance | uses_dynamic_binding_if_needed | returns_app

λ component->state-map(component). {::keys state}
  | returns_{::comp/app ::comp/state-atom} | for_testing | internal_use

λ current-state(app). @(::state-atom app)
  | dereference_safe | can_be_called_during_render_via_binding
```

### Transactions & Messaging

```
λ transact!(component-or-app txn-vec [opts])
  | opts → {:optimistic? :remote? :abort-id}
  | returns_promise_if_cljs_with_await | queued_to_tx_queue | remote_and_local

λ transact!!(component-or-app txn-vec)
  | cljs_synchronous_version | awaitable | wraps_transact! with wait | js_only

λ comp/query(component state). query_form
  | calls_get-query | might_be_nil_if_dynamic | respects_state

λ comp/ident(component props). [keyword id]
  | calls_get-ident | used_by_merge | for_normalization_placement
```

### Navigation & Querying

```
λ change-route!(app path [opts])
  | path → [:parent-key :child-key :target-key]
  | opts → {:tx [(mutations...)] :on-navigate fn}
  | async | triggers_will-enter_will-leave | returns_promise

λ current-route(app [router-class]). [path-components]
  | router_class_optional | defaults_to_root | nil_if_unmounted | strings_and_keywords

λ load!(comp field target [opts])
  | field → :person/by-id | target → [:root :people id :person/home]
  | opts → {:remote :params :post-action :error-action :post-mutation :without :only}
  | asynchronous | creates_markers | markers_in_tree_query

λ refresh!(comp-or-app field [opts])
  | reloads_field_at_existing_location | tree_must_exist | dynamic_query_safe
  | opts → {:remote :params :post-action :error-action}
```

### Server-Side Data

```
λ tree->db(query tree-data). {root-key data :keyword/by-id {id entity}}
  | normalization_algorithm | inverse_of_db->tree | produces_idents_from_query_structure

λ normalize(root-entity query-ast). normalized_tree
  | recursive | follows_joins | produces_graph | called_by_tree->db
```

## Composition

```
λ edge(components, queries).
  components ≡ defsc instances | factories | have_query_methods
  queries ≡ EQL | [:field {:join [...]} :other]
  → (get-query component state) ≡ composed_query | walk_component_tree | respects_unions_and_joins

λ edge(denormalization, rendering).
  denormalization ≡ (db->tree query state) | query_respecting | reads_from_graph_db | ¬mutates
  rendering ≡ react_render | receives_props_tree | components_read_denormalized_form
  → props_flow_to_render_fn | component_instance_sees_shaped_data | shallow_equality_checks

λ edge(mutations, state_targeting).
  mutations ≡ defmutation | optional_:action | optional_:remote
  state_targeting ≡ with-target | returning | with-params
  → (finish-load! env path) ≡ targets_server_response | joins_remote_results | denormalized_placement

λ edge(normalization, idents).
  normalization ≡ tree->db | query_structure → graph_db | keyed_by_idents
  idents ≡ [keyword id] | unique_pointers | component_:ident_fn_creates
  → {:person/by-id {1 {...} 2 {...}}} | single_source_of_truth | reuse_normalized_entities

λ edge(dynamic_routing, will_enter).
  dynamic_routing ≡ defrouter components | route-segment options | changes_visibility
  will_enter ≡ called_before_route_activates | side_effect_free | returns_route_result
  → (route-immediate ident) ≡ already_loaded | (route-deferred ident) ≡ load_needed | target-ready! → ready

λ edge(loading_markers, conditional_ui).
  loading_markers ≡ df/marker-table | status ∈ {:ready :loading :done :failed}
  conditional_ui ≡ (if (df/loading? marker) [Spinner] [Content])
  → (load! component field target) ≡ creates_marker | merge!_updates_marker | render_reads_status

λ edge(hooks, functional_components).
  hooks ≡ use-fulcro use-state useEffect use-context use-reducer etc.
  functional_components ≡ defnc | (fn [props] [...]) | via react.FC
  → hooks_access_app_context | can_transact! | calls_queries | renders_elements

λ edge(app_state, persistence).
  app_state ≡ atom | normalized_graph_db | immutable_values | basis_t_tracked
  persistence ≡ serialize_to_transit | deserialize_from_transit | offline_support
  → merge!_with_persisted_state | remount!_restores_ui | durable_mutations_replay
```

