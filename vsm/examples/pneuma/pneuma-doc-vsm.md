---
title: "Pneuma"
status: active
category: upstream
tags: [pneuma, vsm, lambda, generated, conformance]
---

# Pneuma — Lambda Document

Extracted from 62 files, 6,386 lines. Namespace: `pneuma.*`

## Require Map

```clojure
[pneuma.core :as core]                          ;; Public API entry point
[pneuma.protocol :as p]                         ;; IProjectable, IConnection, IReferenceable
[pneuma.formalism.capability :as cap]           ;; CapabilitySet
[pneuma.formalism.effect-signature :as es]      ;; EffectSignature
[pneuma.formalism.mealy :as mealy]              ;; MealyHandlerSet
[pneuma.formalism.optic :as optic]              ;; OpticDeclaration
[pneuma.formalism.resolver :as resolver]        ;; ResolverGraph
[pneuma.formalism.statechart :as chart]         ;; Statechart
[pneuma.formalism.type-schema :as ts]           ;; TypeSchema
[pneuma.morphism.existential :as ex]            ;; ExistentialMorphism
[pneuma.morphism.structural :as st]             ;; StructuralMorphism
[pneuma.morphism.containment :as ct]            ;; ContainmentMorphism
[pneuma.morphism.ordering :as ord]              ;; OrderingMorphism
[pneuma.gap.core :as gap]                       ;; gap-report, failures
[pneuma.gap.diff :as diff]                      ;; diff-reports
[pneuma.path.core :as path]                     ;; find-paths, ComposedPath
[pneuma.refinement :as rm]                      ;; RefinementMap
[pneuma.lean.core :as lean]                     ;; Lean 4 emission
[pneuma.doc.fragment :as doc-frag]              ;; Format-agnostic fragments
```

## S5 — Identity

```
λ pneuma.
  purpose ≡ conformance checking system | three-layer gap reporting
  | formalisms (7 kinds) ⊗ morphisms (4 kinds) → gap discovery
  | bidirectional projection: mathematical spec ↔ implementation state
  | Lean 4 proof emission on demand | test.check generation | monitoring

λ architecture.
  object_layer   ≡ formalism validation      | per-formalism gaps
  morphism_layer ≡ boundary checking         | morphism-specific gaps
  path_layer     ≡ composed morphism cycles  | axiom verification (A13, A14)
  fill_layer     ≡ (optional) fill manifests | gap-point coverage

λ dispatch.
  IProjectable(formalism) → schema ⊕ monitor ⊕ gen ⊕ gap-type ⊕ doc
  | →schema        ∷ Malli validation schema for state
  | →monitor       ∷ trace monitor fn (EventLogEntry → Verdict)
  | →gen           ∷ test.check generator (valid inputs)
  | →gap-type      ∷ gap descriptor map (formalism, kinds, statuses)
  | →doc           ∷ format-agnostic fragment tree

λ IConnection(morphism).
  check(source, target, refinement-map) → [Gap...]
  | structural    ≡ schema validation across boundary
  | existential   ≡ identifier existence
  | containment   ≡ reference subset containment
  | ordering      ≡ precidence chain validation

λ reference_extraction.
  IReferenceable(formalism).
    extract-refs(ref-kind) → Set[Identifier]
  | per-formalism semantics define ref-kinds
  | cross-formalism identity preserved through refs
```

## S4 — Intelligence (API Shapes)

```
λ formalism_constructors(formalisms_map).
  effect-signature    ∷ EffectSignature({:label, :operations})
  capability-set      ∷ CapabilitySet({:label, :id, :dispatch, :subscribe?, :query?})
  statechart          ∷ Statechart({:label, :states, :hierarchy?, :parallel?, :initial, :transitions})
  mealy-handler-set   ∷ MealyHandlerSet(...)
  optic-declaration   ∷ OpticDeclaration(...)
  resolver-graph      ∷ ResolverGraph(...)
  type-schema         ∷ TypeSchema(...)

λ morphism_constructors(morphisms_map).
  existential-morphism  ∷ ExistentialMorphism({:id, :from, :to, :source-ref-kind, :target-ref-kind})
                        | source refs must exist in target
  structural-morphism   ∷ StructuralMorphism({:id, :from, :to, :source-ref-kind, :target-ref-kind})
                        | source outputs must validate against target schema
  containment-morphism  ∷ ContainmentMorphism({:id, :from, :to, :source-ref-kind, :target-ref-kind})
                        | source refs must be subset of target refs
  ordering-morphism     ∷ OrderingMorphism({:id, :from, :to, :source-ref-kind, :target-ref-kind, :chain})
                        | source ref must precede target ref in chain

λ checking_api(formalism, event_log?, state?, num_tests?).
  check-schema(formalism, state)
    → {:status :conforms|:diverges, :detail {...}}
    | validates state against formalism's schema projection

  check-trace(formalism, event_log)
    → {:status :conforms|:diverges, :entries-checked N, :violations? [...]}
    | replays event log through formalism's monitor

  check-gen(formalism, {:num-tests 100})
    → {:status :conforms|:diverges, :tests-run N, :shrunk?, :fail?}
    | property-based testing via formalism's generator

  check-morphism(morphism, source, target, refinement-map?)
    → [Gap...]
    | delegates to morphism's IConnection implementation

λ gap_report_api(formalisms, registry, fill_manifest?, fill_registry?).
  gap-report({:formalisms {...}, :registry {...}, :fill-manifest?, :fill-registry?})
    → {:object-gaps [...], :morphism-gaps [...], :path-gaps [...], :fill-gaps? [...]}
    | assembles three-layer (or four-layer) gap report

  failures(report) → [NonConformingGap...]
  has-failures?(report) → Bool

  diff-reports(report-a, report-b)
    → {:introduced [...], :resolved [...], :changed [...]}
    | gap diff per-layer

  has-changes?(diff) → Bool
  gaps-involving(report, formalism-kind) → [Gap...]

λ path_composition_api(registry).
  find-paths(registry) → [ComposedPath...]
    | discovers elementary circuits via Johnson's algorithm

  ComposedPath{:id, :steps}
    | steps ≡ [Morphism...]
    | id ≡ derived from morphism sequence (keyword)

λ refinement_bridging.
  refinement-map({:atom-ref, :event-log-ref?, :accessors {}, :source-nss []})
    → RefinementMap

  deref-state(rm) → application_state
  deref-event-log(rm) → event_log | nil
  access(rm, accessor-key, ...args) → extracted_value

λ lean_emission.
  emit-lean(formalism) → String(Lean4)
    | type definitions, properties, proof scaffolding

  emit-lean-conn(morphism, source, target) → String(Lean4)
    | boundary propositions for single morphism

  emit-lean-system(spec-name, {:formalisms {...}, :registry {...}})
    → String(Lean4)
    | unified file: conforming morphisms get `decide`, failing get `sorry`

λ document_fragments.
  section(id, title, children) → Fragment
  table(id, columns, rows) → Fragment
  prose(id, text) → Fragment
  diagram-spec(id, dialect, data) → Fragment | dialect ∈ {:mermaid-state, :mermaid-sequence, :mermaid-graph}
  cross-ref(target-id, label) → Fragment
  status-annotation(target-id, status, detail) → Fragment
  code-block(id, language, code) → Fragment
  summary(id, text) → Fragment

  fragment?(x) → Bool
  section?(x), table?(x), prose?(x), diagram-spec?(x), ... → Bool

λ protocol_dispatch.
  p/IProjectable
    (->schema [this])      ∷ Malli schema
    (->monitor [this])     ∷ EventLogEntry → Verdict
    (->gen [this])         ∷ test.check generator
    (->gap-type [this])    ∷ gap descriptor map
    (->doc [this])         ∷ fragment tree

  p/IConnection
    (check [this source target rm])  ∷ [Gap...]

  p/IReferenceable
    (extract-refs [this ref-kind])   ∷ Set[Identifier]

λ gap_structure.
  Gap{:layer, :id?, :status, :detail?, :kind?}
  | :layer ∈ {:object, :morphism, :path, :fill}
  | :status ∈ {:conforms, :diverges, :absent}
  | :detail ≡ gap-kind-specific map (shape-mismatches, dangling-refs, etc.)

λ verdict.
  Verdict{:verdict, :violations?}
  | :verdict ∈ {:ok, :violation}
  | :violations ≡ [violation_map...]
```

## S3 — Lifecycle

```
λ initialization(formalisms, morphisms).
  1_define: each formalism with label, id, state structure
  2_define: each morphism with source, target, ref-kind pair
  3_assemble: build config map {:formalisms {...}, :registry {...}}
  | skip_assembly → cannot_run_gap_report

λ checking_workflow.
  1_construct: formalism from map (validated via Malli schema)
  2_project: call (IProjectable) to get schema, monitor, gen, gap-type, doc
  3_extract: call (IReferenceable) to get cross-formalism refs
  4_dispatch: call (IConnection/check) for boundary morphisms
  5_aggregate: combine object, morphism, path gaps
  | skip_step_N → N-level checking unavailable

λ lean_emission_workflow.
  1_run: gap-report on full spec
  2_read: gap-report to determine proof tactic per morphism
  3_emit: formalism projections to Lean type definitions
  4_emit: morphism propositions with decide/sorry based on status
  5_emit: path composition proofs (verify A13, A14)
  6_return: complete Lean 4 source file (ready for leanpkg)
  | skip_after_step_1 → proof emission uses sorry for all gaps

λ path_discovery.
  1_build: morphism graph from registry
  2_find: elementary circuits via Johnson (acyclic → ∅ paths)
  3_resolve: node circuits to ComposedPath (all morphism combinations)
  4_verify: closure (A13: first→from ≡ last→to)
  5_verify: adjacency (A14: step[i]→to ≡ step[i+1]→from)
  | any_A13_failure → cycle_gap
  | any_A14_failure → adjacency_gap
```

## S2 — Coordination

```
λ formalism_boundary(source, target).
  structural → source outputs must validate target schema
    | gap-kind ≡ :shape-mismatch
    | source_shape ≠ target_input_shape → :diverges

  existential → source identifiers must exist in target
    | gap-kind ≡ :dangling-ref
    | source_id ∉ target_ids → :diverges

  containment → source identifiers must be subset of target
    | gap-kind ≡ :out-of-bounds
    | source_ids ⊄ target_ids → :diverges

  ordering → source must precede target in chain
    | gap-kind ≡ :order-violation
    | idx(source) ≮ idx(target) in chain → :diverges

λ morphism_check_consequence.
  status:conforms → [{:kind, :status :conforms}]
  status:diverges → [{:kind, :status :diverges, :detail {...}}]
  source:absent   → [{:status :absent, :reason :source-formalism-missing}]
  target:absent   → [{:status :absent, :reason :target-formalism-missing}]

λ trace_monitoring.
  entry ≡ EventLogEntry | implementation-generated event record
  monitor(entry) → Verdict
    | inspect :capability-checks → check-operation per entry
    | inspect :config-before, :event, :config-after → check-step
    | violations detected → {:verdict :violation, :violations [...]}
    | all_pass → {:verdict :ok}

λ gap_report_assembly.
  object-gaps ← ∀formalism: check-object-gaps(formalism schema valid?)
  morphism-gaps ← ∀morphism ∈ registry: check(source, target, rm)
  path-gaps ← ∀ComposedPath: check-closure + check-adjacency per step
  fill-gaps ← (optional) ∀fill-point: conformance check

  three_layers ≡ [object, morphism, path]
  four_layers ≡ [object, morphism, path, fill]

λ report_diffing.
  diff-reports(report_a, report_b) → {:introduced, :resolved, :changed}
  per_layer: compare gaps
    | ∈ a ∧ ∉ b → :introduced
    | ∉ a ∧ ∈ b → :resolved
    | ∈ a ∧ ∈ b ∧ changed(:status | :detail) → :changed

λ cross_morphism_composition.
  path ≡ [morph_1, morph_2, ..., morph_n]
  morph_i.to ≡ morph_{i+1}.from ∀i ∈ [1, n-1]  (A14 adjacency)
  morph_1.from ≡ morph_n.to                      (A13 closure)

  if any_morphism_diverges → gap_layer:path, status:diverges
  else → gap_layer:path, status:conforms

λ lean_proof_generation.
  gap.status:conforms → proof tactic ≡ `decide` (decidable predicate)
  gap.status:diverges → proof tactic ≡ `sorry` (incomplete proof)
  gap.status:absent   → skip (formalism or morphism not present)

  emit → (definition | prop | sorry | decide) per gap
```

## S1 — Operations

```
λ CapabilitySet(label, id, dispatch, subscribe?, query?).
  ->schema() → [:enum ...ops]
  ->monitor(entry) → {:verdict, :violations?}
    | check per entry.capability-checks: [{:kind :op, :op keyword}...]
    | kind ∈ {:dispatch, :subscribe, :query}
    | ∃ kind:op ∉ capability-set → violation
  ->gen() → gen/elements(all-ops)
  ->gap-type() → {:label, :formalism :capability-set, :gap-kinds, :statuses}
  ->doc() → Fragment(section :capability/root, summary, table :capability/permissions)
  extract-refs(ref-kind) → Set[keyword]
    | ref-kind ∈ {:dispatch-refs, :subscribe-refs, :query-refs, :all-refs}

λ EffectSignature(label, operations).
  operations ≡ {op-keyword → {:input {field-kw type-kw, ...}, :output type-kw}, ...}
  ->schema() → [:multi {:dispatch :op}, op-kw → [:map [:op [:= kw]], fields...], ...]
  ->monitor(entry) → {:verdict, :violations?}
    | check per entry.effects: [{:op keyword, :field value, ...}...]
    | ¬(contains? operations :op) → missing-operation
    | ¬(m/validate schema effect) → malformed-fields
  ->gen() → gen/map + gen/elements per field type
  ->gap-type() → {:formalism :effect-signature, :gap-kinds, :statuses}
  ->doc() → Fragment(section :effect-signature/root, table :effect-signature/operations)
  extract-refs(:operation-refs) → Set[keyword] (operation keys)
  register-type!(type-kw, malli-schema) ← extend global type-registry

λ Statechart(label, states, hierarchy?, parallel?, initial, transitions).
  transitions ≡ [{:source :keyword, :event :keyword, :target :keyword, :raise?, :guard?}, ...]
  initial ≡ {composite-id → child-id, ...}  (root-following map)
  ->schema() → [:set (into [:enum] leaf-states)]
  ->monitor(entry) → {:verdict, :violations?}
    | check config-before, event, config-after
    | step(config, event) must match config-after
    | invalid-config | missing-transition → violation
  ->gen() → gen/elements(reachable-configs)
    | reachability via BFS(config, all-events, transitions)
  ->gap-type() → {:formalism :statechart, :gap-kinds #{:missing-state, ...}, ...}
  ->doc() → Fragment(section, diagram-spec :mermaid-state, table :statechart/transitions)
  extract-refs(:state-ids) → states (all state keywords)
  extract-refs(:event-ids) → (all :event from transitions)
  extract-refs(:raised-events) → (all :raise from transitions)

λ ExistentialMorphism(id, from, to, source-ref-kind, target-ref-kind).
  check(source, target, rm) → [Gap...]
    | dangling ← source.extract-refs(source-ref-kind) - target.extract-refs(target-ref-kind)
    | ∅(dangling) → [{:kind :existential, :status :conforms}]
    | else → [{:kind :existential, :status :diverges, :detail {:dangling-refs dangling}}]

λ StructuralMorphism(id, from, to, source-ref-kind, target-ref-kind).
  check(source, target, rm) → [Gap...]
    | target-schema ← target.->schema()
    | source-outputs ← source.extract-refs(source-ref-kind)
    | mismatches ← [output | output ← source-outputs, ¬(m/validate target-schema output)]
    | ∅(mismatches) → [{:kind :structural, :status :conforms}]
    | else → [{:kind :structural, :status :diverges, :detail {:shape-mismatches mismatches}}]

λ ContainmentMorphism(id, from, to, source-ref-kind, target-ref-kind).
  check(source, target, rm) → [Gap...]
    | target-refs ← target.extract-refs(target-ref-kind)
    | source-refs ← source.extract-refs(source-ref-kind)
    | out-of-bounds ← source-refs - target-refs
    | ∅(out-of-bounds) → [{:kind :containment, :status :conforms}]
    | else → [{:kind :containment, :status :diverges, :detail {:out-of-bounds}}]

λ OrderingMorphism(id, from, to, source-ref-kind, target-ref-kind, chain).
  check(source, target, rm) → [Gap...]
    | source-ref ← first(source.extract-refs(source-ref-kind))
    | target-ref ← first(target.extract-refs(target-ref-kind))
    | source-idx ← chain.indexOf(source-ref)
    | target-idx ← chain.indexOf(target-ref)
    | (source-idx ≥ 0 ∧ target-idx ≥ 0 ∧ source-idx < target-idx)
      → [{:kind :ordering, :status :conforms}]
    | else → [{:kind :ordering, :status :diverges, :detail {:order-violation, ...}}]

λ ComposedPath(id, steps).
  steps ≡ [Morphism...]
  check-closure() → Gap | axiom A13: morph[0].from ≡ morph[n].to
  check-adjacency() → [Gap...] | axiom A14: morph[i].to ≡ morph[i+1].from
  id ← keyword derived from morphism sequence (morph-a->morph-b->morph-c)

λ RefinementMap(atom-ref, event-log-ref, accessors, source-nss).
  atom-ref ≡ var | application state atom
  event-log-ref ≡ var | event log (may be nil)
  accessors ≡ {keyword → (fn [db & args] ...)}  | custom extractors
  source-nss ≡ [symbol...] | namespaces containing implementation code
  deref-state() → current @atom-ref
  deref-event-log() → current @event-log-ref | nil
  access(accessor-key, ...args) → (accessors[accessor-key]) state args
```

## Composition

```
λ formalism_→_morphism(f1, f2).
  f1.extract-refs(source-ref-kind)
    → feeds_into_check(source-ref-kind, target-ref-kind)
    → f2.->schema() ∨ f2.extract-refs(target-ref-kind)
  | all four morphism kinds (existential, structural, containment, ordering) consume refs from f1
  | all check against state ∨ schema ∨ refs of f2

λ morphism_→_gap_report().
  registry[morphism-id] → p/check(morphism, source, target, rm)
    → [Gap{:layer :morphism, :kind, :status, :detail}...]
  | all morphism gaps collected per-morphism
  | all gaps then aggregated with object-gaps and path-gaps

λ path_→_gap_report().
  registry → find-paths() → [ComposedPath...]
    → ∀path: check-closure() + check-adjacency()
    → [Gap{:layer :path, :id, :axiom, :status}...]
  | paths discovered from morphism graph
  | each path checked for A13, A14 structural axioms
  | gaps feed into path-gaps collection

λ gap_report_→_lean_emission().
  gap-report → system/emit-system-lean(spec-name, config)
    → ∀morphism: (status:conforms → decide | status:diverges → sorry)
    → String(Lean4 source with complete type definitions, propositions, proofs)
  | lean emission reads gap report to set proof tactics
  | all formalisms emit type structure independently
  | morphisms emit propositions with decide/sorry based on check results

λ refinement_map_→_monitoring(rm, formalism, event_log).
  event_log[entry] → formalism.->monitor(entry)
    → monitor uses rm.accessors to extract formalism-specific state
    → formalism.->monitor applies to extracted state
    → [Verdict...] | violations per entry
  | refinement map bridges generic event entries to formalism-specific extraction
  | allows reuse of monitor projection across implementations

λ test_generation_→_validation(formalism, num-tests).
  gen ← formalism.->gen()
  schema ← formalism.->schema()
  ∀i ∈ [1, num-tests]: v ← gen(), m/validate(schema, v)
  | property holds if all generated values pass schema validation
  | failures (shrunk) provide minimal counterexample
```

---

**Dimensions**: 62 modules | 7 formalisms | 4 morphisms | 3 layers + fill | 5 projections per formalism
