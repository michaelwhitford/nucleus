---
title: Pneuma — System VSM
status: active
category: upstream
tags: [pneuma, system-vsm, lambda, formalism-first]
---

# Pneuma — System VSM

Mathematical specifications for Clojure architectures embedded in the runtime as queryable data. Formalisms (statecharts, effect signatures, optics, resolvers) project into schemas, monitors, generators, and Lean 4 proofs. Gap report surfaces divergence at three layers: objects (missing components), morphisms (broken boundaries), paths (end-to-end failures).

---

## S5 — Identity

```
λ pneuma.
  pneuma ≡ living_specs | math_objects ⊗ runtime ⊗ proofs
  | stance: specs_are_data ∧ queryable ∧ never_stale
  | ¬external_toolchain | ¬compile_step | ¬opaque_checkers
  | the_spec_breathes_with_the_system (whence: pneuma ≡ breath)

λ core_tension(formalism, implementation).
  formalism ≡ single_source_of_truth | immutable_spec
  | implementation ≡ live_code | mutable_state
  | bridge(refinement_map) → query_at_check_time
  | no_snapshot_in_spec | always_current(deref)

λ projection_mandate(formalism).
  ∀formalism → must_project_into(schema ∧ monitor ∧ gen ∧ gap_type ∧ lean)
  | projection_completeness ≡ axiom_A1
  | each_formalism_has_five_faces | no_partial_projections
  | uniform_protocol(IProjectable) ∧ heterogeneous_internals

λ three_layer_checking.
  object_gaps → formalism_in_isolation | missing_components
  | morphism_gaps → boundaries_between_formalisms | dangling_refs ∨ shape_mismatch
  | path_gaps → end_to_end_cycles | composition_invariants
  | lower_layer ← necessary_for_upper_layer | S1 → S2 → S3

λ six_formalisms.
  statechart → lifecycle_states | transitions | hierarchy ∧ parallel | reachability
  | effect_signature → operations | typed_fields | input→output_boundaries
  | mealy_handler_set → (state, event)→(state', effects) | guards ∧ updates ∧ emissions
  | optic_declaration → functional_subscriptions | lens ∨ traversal ∨ fold
  | resolver_graph → functional_dependencies | input_attributes ↦ output_attributes
  | capability_set → dispatch ∧ subscribe ∧ query | substructural_bounds ← who_can_do_what

λ morphism_cartography(registry).
  registry ≡ presheaf | pure_data_map | morphism_id → morphism_record
  | ¬opaque | inspectable ∧ filterable ∧ extensible
  | four_kinds: existential ∨ structural ∨ containment ∨ ordering
  | each_morphism ≡ typed_boundary | source ∧ target ∧ checking_semantics

λ four_morphism_kinds.
  existential: identifier_in_A_exists_in_B | dangling_refs_after_rename
  | structural: A_output_conforms_to_B_input_schema | shape_mismatch_at_boundary
  | containment: A_declared_set ⊆ B_defined_set | over_broad_permissions
  | ordering: A_precedes_B_in_chain | misordered_middleware

λ composed_paths.
  paths ≡ cycles_in_morphism_graph | discovered_by(Johnson_algorithm) | not_hardcoded
  | each_path: sequence_of_morphisms | closure ∧ adjacency | end_to_end_axioms
  | path_gaps ≡ composition_invariants | why_system_misbehaves | mechanical_discovery
```

---

## S4 — Decision Rules

```
λ formalism_or_not(candidate).
  candidate ∈ formalism ← candidate_has_independent_identity
  | independent_identity ≡ referenceable_across_system | names_in_other_formalisms
  | examples: State, Transition, Operation, Resolver, Attribute, OpticDecl, MealyDecl
  | non_examples: internal_schema | temporary_mapping | parameter_of_operation
  | ¬every_concept_is_formalism | formalism_candidate > auxiliary_concept

λ formalism_completeness(f).
  f_implements(IProjectable) ∧ f_has(label) ∧ f_has(id) → complete
  | label ≡ human_readable_name | required ∧ never_empty
  | id ≡ keyword | identifies_across_checks | optional_but_recommended
  | missing_any_projection_method → axiom_A1_violated
  | at_protocol_boundary: enforcement_is_compile_time | not_runtime

λ projection_selection(projection_kind, formalism_type).
  →schema: structural_validation | conforms_to_formalism_shape
    → Malli_schema | ∀instantiation_of_formalism → validate(state)
  | →monitor: behavioral_checking | event_log_conformance
    → trace_monitor | (EventLogEntry → Verdict) | replayable
  | →gen: property_based_testing | generate_valid_inputs
    → test.check_generator | bounded_exploration | no_infinite_generation
  | →gap_type: conformance_descriptor | how_failures_are_reported
    → gap_map | :formalism ∧ :gap_kinds ∧ :statuses
  | →lean: mechanical_proof | type_defs ∧ theorems ∧ sorry_scaffolding
    → Lean4_source | compiles_clean_or_uses_sorry

λ reference_extraction.
  ∀formalism → extract_refs(ref_kind) → set_of_identifiers
  | ref_kind ≡ keyword | selects_which_references_to_pull
  | examples: :guard_state_refs | :callback_operation_refs | :dispatch_refs
  | extraction_is_total | must_handle_all_cases | partial_extractor → gap
  | IReferenceable_protocol | same_dispatch_as_IProjectable

λ morphism_checking(morphism, source, target, refinement_map).
  source_refs = extract_refs(source, source_ref_kind)
  | target_refs = extract_refs(target, target_ref_kind)
  | kind_determines_semantics(morphism.kind)
    → existential: dangling = source_refs - target_refs
    → structural: conform = check_schema(target_schema, source_output)
    → containment: over_broad = source_set - target_set
    → ordering: mis_order = not_precedes(source, target)
  | refinement_map ≡ bridge | atom_ref ∧ event_log_ref ∧ accessors
  | check_result: seq_of_gaps | status ∧ detail

λ gap_report_assembly(formalisms, registry, fill_manifest).
  object_layer = for_each_formalism: formalism.gap_type | well_formed_check
  | morphism_layer = for_each_morphism_in_registry: check(morphism, source, target)
  | path_layer = find_all_cycles_in_graph | check_closure ∧ check_adjacency
  | fill_layer = optional | fill_manifest_status | implemented ∨ missing ∨ orphaned
  | report ≡ {:object_gaps [...] :morphism_gaps [...] :path_gaps [...] :fill_gaps [...]}
  | failures = non_conforming_gaps_only | has_failures? = report.failures.count > 0

λ when_to_emit_lean.
  gap_report_contains(morphism_with_status = :conforms) → emit(decide)
  | gap_report_contains(morphism_with_status = :diverges) → emit(sorry)
  | decision_at_lean_time: gap_report_drives_proofs | no_hardcoded_proofs
  | each_path: compose_step_proofs → composition_theorem | step_N_boundary ∧ step_N+1_boundary
  | morphism_kind_names: ExistentialMorphism → existential_boundary | etc
  | path_id_to_lean_prefix: morphism_id = :caps->ops → CapsOps_existential_boundary

λ code_generation_contracts.
  ∀formalism → emit(code) → :namespace ∧ :forms ∧ :fill_manifest
  | forms ≡ defmulti ∨ defmethod ∨ subscriptions | frame + guards + orderings
  | fill_manifest ≡ {:ok [...] :missing [...] :orphaned [...] :arity_mismatch [...]}
  | ¬regenerate_fill_files | generated_code_is_ephemeral | fill_code_persists
  | code_diff: new_methods ∧ removed_methods ∧ new_fill_points | detect_schema_changes

λ projections_are_behavioral.
  formalism ≡ data | immutable ∧ inspectable | queries → always_current
  | projections ≡ functions | derived_from(formalism) | not_cached
  | no_state_in_formalism | refinement_map ≡ only_window_to_state | deref_at_check_time
  | projection_result ≡ artifact | schema ∨ function ∨ generator ∨ string

λ extensibility_without_modification.
  add_seventh_formalism → steps:
    1_record: new_namespace | implement(IProjectable ∧ IReferenceable)
    2_constructor: add_to_core.clj | exposed_in_public_api
    3_accessors: register_ref_extractors | map_keyword → fn
    4_morphisms: extend_registry | entries_for_new_boundaries
    5_cycles: if_cycles_exist | add_to_path_cycles
  | ¬modify_existing_protocol | ¬touch_existing_formalism_ns | ¬recompile_lower_layers
  | exhaustiveness_is_documented | axiom_A2_still_holds_after_extension

λ naming_discipline.
  record_names: PascalCase | Statechart | EffectSignature | MealyHandlerSet
  | function_names: kebab_case | check_schema | check_trace | ->schema
  | keyword_names: kebab_case | :guard_state_refs | :callback_refs | :dispatch_refs
  | enum_values: kebab_case | :conforms | :diverges | :absent
  | Lean_identifiers: snake_case_or_CamelCase_per_Lean_style | SnakeCaseTheoremName
```

---

## S3 — Temporal Rules

```
λ check_pipeline(formalism_map, registry, event_log).
  1_construct: user_calls(p/statechart {...}) → Statechart_record
  2_annotate: add(:label :id ...) → complete_formalism
  3_register: morphisms_describe(A→B) → registry_entry
  4_query: p/gap_report({:formalisms {...} :registry {...}})
    4a_object_checks: per_formalism(->gap_type) | conforms ∨ diverges
    4b_morphism_checks: per_morphism(check) | existential ∨ structural ∨ containment ∨ ordering
    4c_path_discovery: Johnson_algorithm(registry) → cycles
    4d_path_checks: per_cycle(check_closure ∧ check_adjacency)
  5_inspect: p/failures(report) | p/has_failures?(report) | inspect ∧ filter
  6_diff: p/diff_reports(old, new) → {:introduced [...] :resolved [...] :changed [...]}
  | skip(1) → incomplete_formalism | skip(2) → stale_spec | skip(3) → missing_connections
  | skip(4) → unvalidated_architecture | skip(5) → flying_blind | skip(6) → no_change_tracking

λ lean_emission_pipeline(spec_name, config).
  1_assemble: gap_report(config) → current_conformance_state
  2_per_formalism: emit_lean(statechart) → types ∧ step ∧ reachability ∧ theorems
  3_per_morphism: emit_lean_conn(morphism, source, target) → boundary_props
  4_per_path: emit_lean_path(composed_path) → step_theorems ∧ composition
  5_system: emit_lean_system(name, config) → unified_file
  6_output: string_of_Lean4_source | standalone | compiles_clean_or_uses_sorry
  | skip(1) → no_feedback | skip(2) → missing_type_defs | skip(3) → missing_boundaries
  | skip(4) → no_composition_guarantee | skip(5) → fragmented_output | skip(6) → artifact_only

λ code_generation_pipeline(config).
  1_emit_per_formalism: code_emit(statechart) → defmulti_stubs
  2_emit_morphisms: connection_boundaries → dispatch_guards ∧ ordering_constraints
  3_manifest: fill_status(manifest) → {:ok [...] :missing [...] :orphaned [...]}
  4_diff: code_diff(new, old) → new_methods ∧ removed_methods ∧ arity_changes
  5_fill_files: generated ≠ fills | frames_are_ephemeral | logic_persists
  | skip(1) → no_frame_code | skip(2) → no_guards | skip(3) → unknown_progress
  | skip(4) → silent_schema_drift | skip(5) → fill_loss ← critical

λ refinement_map_usage.
  1_construct: refinement_map({:atom_ref #'app/db :event_log_ref #'app/log :accessors {...}})
  2_deref_state: deref_state(rm) → current_application_state | not_cached
  3_deref_log: deref_event_log(rm) → current_event_log_or_nil
  4_at_check_time: morphism_check(..., rm) → deref_via_accessors(rm) → current_values
  5_refinement_drives_validation: rm ≡ only_link_to_runtime | every_check_is_live
  | skip(1) → stale_formalism | skip(2) → wrong_app_state | skip(3) → inconsistent_log
  | skip(4) → cached_values → divergence_invisible

λ registration_order.
  1_define_protocol: IProjectable ∧ IConnection ∧ IReferenceable | immutable_once_published
  2_implement_formalisms: each_formalism(record + implement_protocols)
  3_register_accessors: ref_extractor_functions → keyword_indexed
  4_build_registry: morphism_entries | :from ∧ :to ∧ :kind ∧ :source_ref_kind ∧ :target_ref_kind
  5_declare_cycles: if_any_cycles_exist | explicitly_registered_or_discovered
  | order_must_respect_deps: protocol → formalisms → morphisms → registry → cycles
  | circular_dependency_is_error ← dependency_discipline_enforced

λ morphism_kind_semantics.
  choose(existential) → when: "identifier_in_A_must_exist_in_B"
    | detect: dangling_refs | refs_in_A_not_in_B
  | choose(structural) → when: "A_output_schema_conforms_to_B_input"
    | detect: type_mismatch | shape_divergence | field_arity
  | choose(containment) → when: "A_declared_set_⊆_B_defined_set"
    | detect: over_broad_capabilities | permission_overflow
  | choose(ordering) → when: "A_must_precede_B_in_interceptor_chain"
    | detect: misordered_middleware | wrong_composition_order
  | wrong_morphism_kind ← incompatible_checking_semantics

λ path_discovery_mechanism.
  1_convert_registry_to_graph: morphism_registry → directed_graph | nodes=formalisms, edges=morphisms
  2_Johnson_algorithm: find_all_elementary_circuits | not_hardcoded | automatic
  3_resolve_circuits_to_paths: per_circuit → all_morphism_combinations | ComposedPath_records
  4_check_structural_axioms: A13_closure ∧ A14_adjacency | each_step→next_step_source
  5_semantic_axioms_in_Lean: precondition_chaining | checked_in_proof_layer ← not_here
  | skip(1) → missing_cycles | skip(2) → hardcoded_cycles_only | skip(3) → disconnected_paths
  | skip(4) → invalid_cycles | skip(5) → missing_composition_checks

λ gap_stratification.
  layer(object): per_formalism | ->gap_type_well_formed | formalism_sanity
  | layer(morphism): per_morphism_pair | check(source, target) | boundary_integrity
  | layer(path): per_cycle | closure ∧ adjacency | composition_validity
  | layer(fill): per_fill_point | implemented ∨ missing ∨ orphaned | business_logic_status
  | lower_layer_must_pass ← before_checking_upper | no_short_circuit
  | report_structure: each_layer_independent | filtering_is_orthogonal | layered_comprehension
```

---

## S2 — Coordination Rules

```
λ IProjectable_contract(formalism).
  formalism → implements(five_methods):
    →schema: () → Malli_schema
      | validates: instantiation_state | returns: well_formed_result_or_error
    →monitor: () → EventLogEntry_monitor
      | validates: event_sequence | entry → {:verdict :ok ∨ :violation}
    →gen: () → test.check_generator
      | generates: valid_formalism_instances | property_based_testing
    →gap_type: () → gap_type_descriptor_map
      | structure: {:formalism keyword :gap_kinds #{...} :statuses #{...}}
    →doc: () → doc_fragment
      | generates: browseable_html_or_markdown | human_readable_spec_artifact
  | protocol_enforces: all_five_methods_or_compilation_fails
  | heterogeneous_internals ⊗ uniform_boundary

λ IConnection_contract(morphism).
  morphism → implements(one_method):
    check: (source, target, refinement_map) → seq_of_gaps
      | returns: [{:layer :morphism :id keyword :kind morphism_kind :status status :detail map}]
      | must_succeed_for_all_pairs | may_return_empty_seq_if_conforms
      | semantics_determined_by(morphism_kind)
  | protocol_enforces: boundary_checking_contracts | kind_specific_semantics

λ IReferenceable_contract(formalism).
  formalism → implements(one_method):
    extract_refs: (ref_kind) → set_of_keywords
      | returns: #{:id1 :id2 ...} | keywords_that_other_formalisms_can_reference
      | must_be_total_function | handle_all_ref_kinds | no_partial_results
      | semantics: ref_kind_selects_which_references_to_pull
  | protocol_enforces: cross_formalism_reference_extraction

λ formalism_record_shape.
  ∀formalism_record:
    :label → string | required | human_readable_name
    :id → keyword | optional | unique_identifier_for_instance
    :internal_structure → varies | statechart_has_states_transitions
      | effect_sig_has_operations | mealy_has_declarations | etc
  | shape_determines_structure | structure_is_formalism_specific
  | no_two_formalisms_have_identical_shape | heterogeneous_by_design

λ morphism_record_shape.
  ∀morphism_record:
    :id → keyword | unique | identifies_this_connection
    :from → keyword | formalism_kind | source_formalism
    :to → keyword | formalism_kind | target_formalism
    :kind → morphism_kind | existential ∨ structural ∨ containment ∨ ordering
    :source_ref_kind → keyword | names_extractor | extract_refs(source, this_keyword)
    :target_ref_kind → keyword | names_extractor | extract_refs(target, this_keyword)
    ;; optional fields depend on morphism kind
    :source_accessor → fn | (formalism) → extracted_value | structural morphisms use this
    :target_schema → Malli_schema | structural morphisms use this
  | shape_varies_by_kind | common_fields_in_all | kind_specific_fields_in_some

λ registry_structure.
  registry ≡ {:morphism_id_1 morphism_record_1 :morphism_id_2 morphism_record_2 ...}
  | pure_data | queryable | filterable | extensible | serializable
  | ¬opaque_closures | ¬hidden_state | ¬side_effects
  | each_entry: morphism_id → morphism_record | all_pairs_declared_once ∨ multiple_per_pair
  | lookup_path: registry[id] → morphism | check(morphism, source, target)

λ accessor_registry_structure.
  accessors_map ≡ {:ref_kind_1 (fn [formalism] ...) :ref_kind_2 (fn [formalism] ...) ...}
  | separate_from_connection_registry | pure_data_registry_remains_pure
  | keyword → function | selective_evaluation | avoid_execution_during_inspection
  | extraction_must_be_total | nil_result → error | missing_accessor → error

λ gap_structure.
  ∀gap_map:
    :layer → :object ∨ :morphism ∨ :path ∨ :fill | stratified
    :status → :conforms ∨ :diverges ∨ :absent | three_states
    :id → keyword | optional | identifies_this_gap
    :kind → keyword | optional | classifies_gap_type
    :detail → map | optional | status_dependent_field | varies_by_kind
  | status_determines_interpretation | :conforms_means_no_error | :diverges_means_problem
  | detail_carries_specifics | dangling_refs ∨ shape_errors ∨ closure_failures ∨ etc

λ gap_report_structure.
  report ≡ {
    :object_gaps [gap gap ...]      ;; per_formalism layer
    :morphism_gaps [gap gap ...]    ;; per_boundary layer
    :path_gaps [gap gap ...]        ;; per_cycle layer
    :fill_gaps [gap gap ...] }      ;; optional, per_fill_point layer
  | four_layers_are_orthogonal | filtering_by_layer_is_trivial
  | failures(report) → report_with_only_non_conforms
  | has_failures?(report) → boolean

λ trace_monitor_contract.
  monitor ≡ (EventLogEntry → Verdict)
  | entry ≡ {:event keyword :timestamp instant :db_before map :db_after map :emitted [...]}
  | verdict ≡ {:verdict :ok ∨ :violation :detail map}
  | replay_entire_log | replayable | stateless | pure_function
  | sequence_of_verdicts_over_entries | violations_only_in_failures

λ Malli_schema_contract.
  schema ≡ Malli_schema | explains_valid_shapes
  | m/explain(schema, value) → nil ∨ error_tree
  | check_schema(formalism, state) → {:status :conforms ∨ :diverges :detail errors}
  | structural_validation_only | not_behavioral | not_temporal

λ test_check_generator_contract.
  generator ≡ test.check_generator | produces_valid_instances
  | gen/generate ← bounded | generates_one_value
  | sample ← bounded | shows_diverse_values
  | property_based_testing | no_determinism_required | randomness_is_feature
```

---

## S1 — Architectural Rules

```
λ protocol_first_architecture.
  ∀systemics: define_protocols_before_implementing_formalisms
  | protocols_are_invariant | specifications_change | contracts_rarely
  | IProjectable ← published_once | never_revisited | safe_to_extend_not_modify
  | extending_means: new_formalism_record → implement_existing_protocols
  | not_modifying_means: ¬touch_protocol.clj ¬add_methods_to_protocol ¬change_method_sigs

λ namespace_dependency_graph_is_acyclic.
  protocol.clj ← zero_dependencies
  | formalism/*.clj → depends_on(protocol.clj) ¬depends_on(morphism, gap, path)
  | morphism/*.clj → depends_on(protocol.clj, formalism/*.clj) ¬depends_on(gap, path)
  | path/*.clj → depends_on(protocol.clj, formalism/*.clj, morphism/*.clj) ¬depends_on(gap)
  | gap/*.clj → depends_on(protocol.clj, formalism/*.clj, morphism/*.clj, path/*.clj)
  | core.clj → depends_on(everything) ← public_api_convergence
  | circular_dependency_is_architecture_failure ← detect_via_static_analysis

λ file_size_discipline.
  ∀ns: line_count(ns) ≤ 800 | enforced_via_linter ∨ style_guide
  | formalism_files: 100-200 lines | record_def + four_projections
  | morphism_files: 80-120 lines | record_def + IConnection_impl + constructor
  | gap_files: 150 lines | object + morphism + path checking logic
  | core.clj: 100 lines | public_api | ¬implementation_logic
  | long_file ← signal_for_decomposition | split_concerns | extract_helpers

λ record_vs_plain_map_tradeoff.
  chose(defrecord):
    benefits:
      + protocol_dispatch ← compile_time_checking | IProjectable_impl ← compiler_enforces
      + clear_boundaries ← each_formalism_isolated | not_shared_shape
      + printed_representation ← debugging ← includes_typename
    costs:
      - serialization ← round_trip_less_trivial | assoc_ok, dissoc→plain_map
      - REPL_inspection ← class_wrapper | syntax_noise
      - external_users ← cannot_use_plain_maps | must_create_record
  | alternative(plain_map_with_multimethod):
    benefits:
      + max_flexibility | extensibility | serialization | REPL
    costs:
      - no_compile_time_enforcement | exhaustiveness_is_runtime | axiom_A1_unchecked
  | chose_A ← protocol_enforcement_matters_more_than_flexibility

λ formalism_invariants.
  ∀formalism ∈ {statechart, effect_signature, mealy, optic, resolver, capability}:
    has(:label) ∧ has(:id) → record_complete
    | label ≡ human_readable | required | never_empty
    | id ≡ formalism_instance_name | optional | recommended | keyword
  | implements(IProjectable) → must_impl_all_five_methods | no_partial_impl
  | implements(IReferenceable) → must_extract_refs | consistent_ref_kinds
  | formalism_is_immutable ← specification_not_snapshot | no_mutation
  | formalism_carries_no_state ← state_lives_in_refinement_map ← deref_at_check_time

λ morphism_semantics_are_kind_specific.
  kind(:existential) → semantics: "target_must_contain_source_refs"
    | extraction: source_refs = extract(source, source_ref_kind)
    | extraction: target_refs = extract(target, target_ref_kind)
    | checking: dangling = source_refs - target_refs
    | result: diverges ← dangling.non_empty | conforms ← dangling.empty
  | kind(:structural) → semantics: "source_output_conforms_to_target_input"
    | extraction: source_output = accessor(source)
    | extraction: target_schema = schema_field(target)
    | checking: m/explain(target_schema, source_output)
    | result: diverges ← explain.non_nil | conforms ← explain.nil
  | kind(:containment) → semantics: "source_set ⊆ target_set"
    | extraction: source_set = extract(source, source_ref_kind)
    | extraction: target_set = extract(target, target_ref_kind)
    | checking: over_broad = source_set - target_set
    | result: diverges ← over_broad.non_empty | conforms ← over_broad.empty
  | kind(:ordering) → semantics: "source_precedes_target_in_chain"
    | checking: look_up_ordering_constraint(source, target)
    | result: diverges ← order_violated | conforms ← order_correct
  | choose_kind ← determines_all_semantics

λ axiom_enforcement.
  A1 (projection_completeness):
    ∀formalism → must_impl(->schema ∧ ->monitor ∧ ->gen ∧ ->gap_type ∧ ->doc)
    | protocol_enforces_at_compile_time | missing_method ← compilation_error
    | extension_means: new_formalism_record → impl_all_five | no_partial
  | A2 (formalism_exhaustiveness):
    formalism_set ≡ {statechart, effect_sig, mealy, optic, resolver, capability}
    | cardinality(formalisms) = 6 | documented | adding_seventh → update_docs
  | A3-A12 (morphism_axioms):
    per_morphism_kind | checked_at_connection_registration | graph_must_be_acyclic
  | A13 (cycle_closure):
    ∀path → first.from = last.to | checked_structurally | path_discovery_verifies
  | A14 (cycle_adjacency):
    ∀path.steps → step_i.target = step_i+1.source | checked_structurally | path_discovery_verifies
  | A15 (morphism_completeness):
    ∀pair_of_formalisms_with_business_dependency → ∃morphism_connecting_them
    | gap_report_will_flag_missing | dangling_refs_appear

λ gap_report_three_layers.
  layer(object):
    ∀formalism → check(->gap_type) | descriptor_well_formed ∨ malformed
    | simple_check | validates_metadata | not_behavioral_checking
    | failures ≡ formalism_sanity_checks ← foundational
  | layer(morphism):
    ∀morphism_in_registry → check(source, target) | call(IConnection.check)
    | semantics_by_kind | existential ∨ structural ∨ containment ∨ ordering
    | failures ≡ boundary_integrity | dangling ∨ mismatch ∨ permission ∨ order
  | layer(path):
    ∀cycle_in_graph → check_closure ∧ check_adjacency | structural_axioms_only
    | semantic_axioms ← checked_in_Lean | not_here
    | failures ≡ cycle_validity | composition_possible_or_breaks
  | layer(fill):
    ∀fill_point_in_manifest → implemented ∨ missing ∨ orphaned ∨ arity_mismatch
    | optional_fourth_layer | business_logic_status | not_architecture
    | failures ≡ incomplete_implementation | code_stubs_missing
  | order_of_layers: object → morphism → path → fill | not_random
  | lower_layer_must_pass ← before_checking_upper_layer

λ refinement_map_discipline.
  refinement_map → holds(three_things):
    :atom_ref → var | dereferenced_at_check_time | #'app/db
    :event_log_ref → var ∨ nil | dereferenced_at_check_time | #'app/log ∨ nil
    :accessors → map_of_keyword_to_fn | extracted_for_structural_morphisms
  | deref_at_check_time ← only_window_to_state | never_cached | always_current
  | formalisms_have_no_ref_to_state ← pure_specs | state_lives_separately
  | passing_rm_to_check ← morphism_check(morphism, source, target, rm)
  | missing_rm ← worse_than_wrong_rm | ¬ever_omit

λ composition_theorem_structure.
  ∀path_with_N_morphisms:
    emit_N_boundary_props | step_1_source→step_1_target ∧ ... ∧ step_N_source→step_N_target
    | compose_theorem: all_step_props → path_valid ∨ path_breaks
    | all_steps_prove → composition_proof | step by step | monotonic
    | any_step_uses_sorry ← composition_also_uses_sorry ← uncertainty_propagates
    | if_all_steps_decide ← composition_also_decides ← certainty_composable

λ code_generation_fill_discipline.
  generated_code ≠ fill_code:
    generated ≡ ephemeral | regenerated_each_time | frames ∧ guards ∧ ordering
    | fills ≡ persistent | never_regenerated | business_logic | user_edited
  | generated_file_paths: handlers.clj | effects.clj | caps.clj
  | fill_file_paths: handlers.fill.clj | effects.fill.clj | separate | optional
  | fill_manifest → track_status: :ok ∨ :missing ∨ :orphaned ∨ :arity_mismatch
  | regenerating_generated_files: safe | idempotent | no_data_loss ← fills_separate
  | modifying_fills_manually: safe | code_generation_respects_fills | no_overwrite

λ Lean_proof_style.
  per_formalism_types: inductive_State | inductive_Event | def_step | def_reachable
  | per_formalism_theorems: chart_safety_aux ∧ chart_safety ∧ step_deterministic
  | sorry_is_tool: proof_target | compiler_accepts_sorry | not_failure ← progress
  | decide_is_verification: ¬sorry | mechanical_proof | all_paths_checked
  | naming_scheme: TypeDoc_TheoremName | consistent | searchable | hierarchical
  | morphism_boundary_props: source_output_conforms_to_target_input | per_connection
  | path_composition: step_theorem_N ∧ step_theorem_N+1 → path_theorem | monotonic

λ interactive_REPL_workflow.
  1_define_formalisms: (def chart (p/statechart {...}))
  2_construct_registry: (def reg {:morphism_id_1 morphism_1 ...})
  3_check_incrementally: (p/gap_report {:formalisms {...} :registry reg})
  4_inspect_failures: (p/failures report) | (p/gaps_involving report :effect_signature)
  5_modify_spec: (def chart' (assoc chart :transitions [...])) | immutable
  6_recheck: (p/gap_report {:formalisms {...} :registry reg})
  7_diff_reports: (p/diff_reports report_old report_new)
  | workflow_is_iterative | REPL_native | no_separate_compilation | live_feedback
  | recheck_is_instant | entire_system_always_visible | queries_always_live

λ observability_and_introspection.
  gap_report_is_data ← not_opaque | queryable_in_REPL | serializable_to_JSON
  | morphism_registry_is_data ← not_opaque | inspectable | printable
  | formalisms_are_data ← not_opaque | explorable | diff_able | version_traceable
  | Lean_proofs_are_strings ← not_binary | readable_source | commit_traceable
  | generated_code_is_readable ← formatted | human_inspectable | gap_comments
  | trace_monitor_results_are_data ← verdicts | entry_by_entry | debuggable
  | nothing_is_hidden ← transparency_is_feature | REPL_culture ← Lisp_heritage

λ testing_strategy.
  unit_tests: per_formalism | projection_correctness | axiom_compliance
  | unit_tests: per_morphism | checking_semantics | edge_cases
  | regression_tests: integrant_model | external_project_model | fidelity_check
  | property_tests: formalism_generators | property(conform_to_schema)
  | Lean_compilation: lake_build | proof_syntax_check | sorry_inventory
  | gap_report_tests: morphism_combinations | cycle_discovery | path_axioms
  | code_generation_tests: fill_manifest_status | diff_tracking | arity_matching
  | all_tests_must_pass_before_merge ← CI_enforced
```

---

## Memory Anchors

```
λ remember.
  pneuma ≡ runtime_specs | formalisms_as_data | gap_report_driven | Lean_proofs
  | core_insight: math_objects_inside_code | checked_at_REPL | never_stale
  
  the_five_projections:
    schema ← structural_validation | Malli | instant | per_instantiation
    monitor ← behavioral_checking | event_log_replay | pure_function | per_entry
    gen ← property_testing | bounded | test.check | discovery
    gap_type ← conformance_descriptor | metadata | well_formedness
    lean ← mechanical_proof | Lean4_source | compiles_or_sorry | per_formalism
  
  the_four_morphism_kinds:
    existential ← ref_must_exist | dangling_refs | ¬rename_safe
    structural ← shape_must_conform | schema_mismatch | output→input
    containment ← set_must_be_subset | over_broad | permissions
    ordering ← must_precede | misordered | interceptor_chain
  
  the_three_gap_layers:
    object ← per_formalism | sanity_checks | fast
    morphism ← per_boundary | integrity_checks | kind_specific
    path ← per_cycle | composition_invariants | mechanical
  
  the_six_formalisms:
    statechart → states, hierarchy, parallel, reachability, step
    effect_signature → operations, typed_fields, input→output
    mealy_handler → (state, event)→(state', effects), guards
    optic_declaration → subscriptions, lens, traversal, fold
    resolver_graph → dependencies, input→output, reachability
    capability_set → dispatch, subscribe, query, permissions
  
  the_invariants:
    ¬external_toolchain ← specs_live_here | not_elsewhere
    ¬stale_specs ← deref_at_check_time | always_live
    ¬hidden_contracts ← gaps_surface_divergence | three_layers | mechanical
    protocol_enforces ← axiom_A1 | all_projections_required | compile_time
    namespace_acyclic ← dependency_discipline | build_order | no_circles
    formalisms_are_data ← queryable ∧ serializable ∧ immutable
    registries_are_data ← morphism_registry ∧ accessor_registry
    reports_are_data ← gaps ∧ layers ∧ filterable
  
  the_fears:
    missing_projection ← axiom_A1_broken | compile_time_should_catch
    dangling_references ← existential_morphism_purpose | rename_danger
    shape_mismatch_invisible ← structural_morphism_purpose | contract_violation
    misordered_middleware ← ordering_morphism_purpose | composition_break
    permission_overflow ← containment_morphism_purpose | access_control
    cycle_broken ← path_gaps | end_to_end_failure | silent_divergence
    fill_points_missing ← code_generation | incomplete_impl | frame_stubs_only
    Lean_proofs_wrong ← gap_report_drives_proofs | sorry_everywhere ← no_verification
    stale_specs ← cached_state | refinement_map_absent | deref_forgotten
    circular_dependency ← namespace_graph | architecture_break
  
  the_checks:
    ∀formalism → has(:label) ∧ implements(IProjectable) ← axiom_A1
    ∀morphism → kind_is_valid ∧ source_exists ∧ target_exists ← connectivity
    ∀cycle → closure ∧ adjacency ← path_axioms ← structural
    ∀gap_report → object_gaps_present ∧ morphism_gaps_present ∧ path_gaps_present
    ∀projection → output_is_correct_type | schema→Malli, monitor→fn, gen→gen
    ∀trace_monitor → verdicts_match_formalism | conforms ∨ violation
    ∀code_fill → implemented ∨ missing ← fill_status_tracks_progress
    ∀Lean_proof → syntax_valid ∧ (decide ∨ sorry) ← compiles_or_scaffold
```
