# Cognitive Grammar Specification

## Overview

The **Cognitive Grammar** defines the formal rules and patterns for composing modular simulation components into coherent cognitive architectures. Just as natural language grammar specifies how words combine into valid sentences, cognitive grammar specifies how simulation components combine into valid architectures.

This grammar enables:
- **Automated validation** of component compositions
- **Constraint checking** for valid architectures
- **Pattern recognition** of successful compositions
- **Self-assembly** of domain-specific cognitive systems

## Grammar Foundation

### Grammatical Elements

| Element | Analog | Purpose |
|---------|--------|---------|
| **Components** | Words | Basic units of composition |
| **Interfaces** | Parts of speech | Component roles (noun, verb, adjective) |
| **Connections** | Syntax | How components link together |
| **Patterns** | Sentence structures | Common valid compositions |
| **Constraints** | Grammar rules | What combinations are valid |
| **Semantics** | Meaning | What the composition accomplishes |

### Component Roles (Parts of Speech)

Each component has a grammatical role:

```
Producer Components (Nouns)
├── GeneratorComponent - Creates entities
├── SensorComponent - Produces observations
└── DatabaseComponent - Provides stored data

Transformer Components (Verbs)
├── TransformerComponent - Modifies entities
├── ProcessorComponent - Performs operations
└── OptimizerComponent - Improves parameters

Connector Components (Prepositions)
├── RouterComponent - Routes between destinations
├── AllocatorComponent - Assigns to recipients
└── CommunicationComponent - Links senders/receivers

Modifier Components (Adjectives)
├── ClassifierComponent - Adds categories
├── PatternDetectorComponent - Adds pattern labels
└── MonitorComponent - Adds performance metrics

Decision Components (Conjunctions)
├── DecisionComponent - Chooses branches
├── SelectorComponent - Picks alternatives
└── FilterComponent - Includes/excludes

Storage Components (Articles)
├── BufferComponent - Temporary storage
├── CacheComponent - Fast access storage
└── DatabaseComponent - Persistent storage

Meta Components (Adverbs)
├── MonitorComponent - Observes how
├── LearnerComponent - Adapts how
└── ReflectorComponent - Understands how
```

## Composition Rules

### Rule 1: Producer-Consumer Matching

**Specification**: A component's output types must match its consumer's input types.

**Formal Rule**:
```
For connection C = (Component_A → Component_B):
    VALID if: OutputTypes(A) ∩ RequiredInputs(B) ≠ ∅
    INVALID otherwise
```

**Example (Valid)**:
```
SensorComponent [outputs: MetricNode]
    → ClassifierComponent [requires: MetricNode]
```

**Example (Invalid)**:
```
GeneratorComponent [outputs: EntityNode]
    → OptimizerComponent [requires: ParameterNode]
    ❌ Type mismatch: EntityNode ≠ ParameterNode
```

### Rule 2: Temporal Ordering

**Specification**: Components with temporal dependencies must execute in causal order.

**Formal Rule**:
```
For components A, B where B depends on A's output:
    ExecutionOrder(A) < ExecutionOrder(B)
    OR ExecutionMode = REACTIVE (B triggered by A's events)
```

**Example**:
```
Valid:
    1. SensorComponent measures temperature
    2. ClassifierComponent categorizes reading
    3. DecisionComponent chooses action
    
Invalid:
    1. DecisionComponent chooses action
    2. SensorComponent measures temperature
    ❌ Decision made before information available
```

### Rule 3: Resource Conservation

**Specification**: All generated entities must have a defined lifecycle endpoint.

**Formal Rule**:
```
For each GeneratorComponent G:
    ∃ Terminal component T where:
        Path(G → ... → T) exists
        T terminates entity lifecycle (Sink, Database, etc.)
```

**Example**:
```
Valid:
    GeneratorComponent → BufferComponent → TransformerComponent → SinkComponent
    
Invalid:
    GeneratorComponent → BufferComponent → ... (no termination)
    ❌ Entities accumulate without bound
```

### Rule 4: Feedback Loop Validity

**Specification**: Feedback loops must have break conditions to prevent infinite cycles.

**Formal Rule**:
```
For cycle C = (A → B → ... → A):
    ∃ component D in C where:
        D has termination condition
        OR D has delay mechanism
        OR D has dampening factor
```

**Example**:
```
Valid:
    PredictorComponent → AllocatorComponent → MonitorComponent → PredictorComponent
    (MonitorComponent aggregates over time window - introduces delay)
    
Invalid:
    DecisionComponent → ActionComponent → SensorComponent → DecisionComponent
    (Instantaneous cycle without delay)
    ❌ Infinite loop at single time step
```

### Rule 5: State Consistency

**Specification**: Multiple components reading/writing the same state must synchronize.

**Formal Rule**:
```
For StateNode S with writers {W₁, W₂, ...} and readers {R₁, R₂, ...}:
    All writers must implement locking OR sequential execution
    All readers must handle concurrent updates OR use snapshots
```

**Example**:
```
Valid:
    Component A writes InventoryLevel at t=1.0
    Component B reads InventoryLevel at t=1.5
    (Read happens after write completes)
    
Invalid:
    Component A writes InventoryLevel (async)
    Component B writes InventoryLevel (async)
    Component C reads InventoryLevel (async)
    ❌ Race condition: read may see inconsistent state
```

## Composition Patterns (Grammar Structures)

### Pattern 1: Linear Pipeline (Subject-Verb-Object)

**Structure**: Producer → Transformer → Consumer

**Grammar**: Noun → Verb → Noun

**When to Use**: Sequential processing with clear input/output flow

**Example**:
```
GeneratorComponent [Order] 
    → TransformerComponent [Process Order]
    → SinkComponent [Complete Order]

Reads as: "Generator creates Order, Transformer processes Order, Sink completes Order"
```

**Validation Checklist**:
- ✅ Each component consumes previous output
- ✅ No cycles
- ✅ Terminal component exists
- ✅ Single responsibility per stage

### Pattern 2: Branching Decision (If-Then-Else)

**Structure**: Sensor → Decision → [Action₁ | Action₂]

**Grammar**: Adjective + Noun → Conjunction → [Verb₁ | Verb₂]

**When to Use**: Conditional routing based on properties

**Example**:
```
SensorComponent [Measure Priority]
    → ClassifierComponent [High/Low]
    → DecisionComponent 
        → {High → FastRouterComponent
           Low → StandardRouterComponent}

Reads as: "Measure Priority, classify as High or Low, then route accordingly"
```

**Validation Checklist**:
- ✅ All branches have valid paths
- ✅ Decision criteria are exhaustive (cover all cases)
- ✅ Branches eventually converge or terminate
- ✅ No unreachable branches

### Pattern 3: Feedback Loop (While-Do)

**Structure**: Sensor → Decision → Action → Sensor

**Grammar**: Noun → Conjunction → Verb → (repeat)

**When to Use**: Iterative improvement or control loops

**Example**:
```
MonitorComponent [Measure Performance]
    → DecisionComponent [Below Target?]
        → Yes: OptimizerComponent [Adjust Parameters] → (repeat)
        → No: ContinueComponent [Proceed]

Reads as: "While performance below target, optimize parameters"
```

**Validation Checklist**:
- ✅ Termination condition exists
- ✅ Loop includes delay or convergence criterion
- ✅ No infinite execution at single time point
- ✅ Feedback improves or stabilizes system

### Pattern 4: Parallel Composition (And)

**Structure**: Broadcaster → [Component₁, Component₂, Component₃] → Aggregator

**Grammar**: Verb → [Noun₁ AND Noun₂ AND Noun₃] → Verb

**When to Use**: Multiple independent operations on same input

**Example**:
```
BroadcasterComponent [Order Received]
    → {InventoryCheckerComponent [Check Stock]
       CreditCheckerComponent [Verify Payment]
       DeliveryEstimatorComponent [Calculate Time]}
    → AggregatorComponent [Combine Results]

Reads as: "When order received, check stock AND verify payment AND calculate delivery, then combine"
```

**Validation Checklist**:
- ✅ All parallel paths can execute simultaneously
- ✅ No dependencies between parallel branches
- ✅ Aggregator handles all possible response combinations
- ✅ Timeouts defined for slow branches

### Pattern 5: Hierarchical Composition (Nested Clauses)

**Structure**: Component containing sub-components

**Grammar**: Complex sentence with dependent clauses

**When to Use**: Multi-level abstractions or populations

**Example**:
```
FactoryComponent [Main System]
    Contains:
        DepartmentComponent [Assembly]
            Contains:
                WorkstationComponent [Station A]
                WorkstationComponent [Station B]
        DepartmentComponent [Quality Control]
            Contains:
                InspectorComponent [Inspector 1]

Reads as: "Factory contains Assembly (with Stations) and Quality Control (with Inspectors)"
```

**Validation Checklist**:
- ✅ Parent-child relationships are well-defined
- ✅ Communication between levels is explicit
- ✅ Abstractions are semantically meaningful
- ✅ No circular containment

### Pattern 6: Observer Pattern (Passive Listening)

**Structure**: Subject → [Observer₁, Observer₂, ...] (passive)

**Grammar**: Noun → [Adverb₁, Adverb₂, ...] (modifiers observe)

**When to Use**: Monitoring without affecting main flow

**Example**:
```
ProcessorComponent [Main Process]
    Observed by:
        MonitorComponent [Track Performance]
        LoggerComponent [Record Events]
        VisualizerComponent [Display Status]

Reads as: "Process executes, while monitors observe, loggers record, visualizers display"
```

**Validation Checklist**:
- ✅ Observers don't affect subject's behavior
- ✅ Observer failures don't break subject
- ✅ Observers receive all relevant events
- ✅ Performance impact is acceptable

## Constraint Types

### Type Constraints

Enforce type safety in compositions:

```
TypeConstraint: ProductionType(A) = ConsumptionType(B)

Examples:
✅ SensorComponent [→ MetricNode] → ClassifierComponent [MetricNode →]
❌ GeneratorComponent [→ EntityNode] → OptimizerComponent [ParameterNode →]
```

### Cardinality Constraints

Enforce correct numbers of connections:

```
CardinalityConstraint:
    MinInputs(C) ≤ |Inputs(C)| ≤ MaxInputs(C)
    MinOutputs(C) ≤ |Outputs(C)| ≤ MaxOutputs(C)

Examples:
✅ DecisionComponent [1 input, 2+ outputs] - valid branching
❌ DecisionComponent [3 inputs, 1 output] - violates single input requirement
```

### Temporal Constraints

Enforce correct execution ordering:

```
TemporalConstraint: DependsOn(B, A) ⟹ ExecutionTime(A) < ExecutionTime(B)

Examples:
✅ Sensor(t=1) → Classifier(t=2) → Decision(t=3)
❌ Decision(t=1) → Sensor(t=2) → Classifier(t=3) - wrong order
```

### Resource Constraints

Enforce resource availability:

```
ResourceConstraint: RequiredResources(C) ⊆ AvailableResources(System)

Examples:
✅ Component requires GPU, System has GPU available
❌ Component requires Fluid Engine, System only has DES Engine
```

### Semantic Constraints

Enforce meaningful compositions:

```
SemanticConstraint: PurposeOf(Composition) is well-defined

Examples:
✅ Sensor → Classifier → Decision (clear purpose: sense-classify-act)
❌ Database → Database → Database (unclear purpose: what is accomplished?)
```

## Grammar Validation Algorithm

```python
def validate_composition(components, connections):
    """
    Validates a component composition against cognitive grammar rules.
    Returns (is_valid, violations)
    """
    violations = []
    
    # 1. Type Checking
    for conn in connections:
        if not types_compatible(conn.source.outputs, conn.target.inputs):
            violations.append(f"Type mismatch: {conn}")
    
    # 2. Cardinality Checking
    for comp in components:
        if not within_cardinality_bounds(comp):
            violations.append(f"Cardinality violation: {comp}")
    
    # 3. Temporal Ordering
    execution_order = topological_sort(components, connections)
    if execution_order is None:  # Cycle detected
        if not all_cycles_have_breaks(components, connections):
            violations.append("Invalid cycle without break condition")
    
    # 4. Resource Conservation
    for generator in get_generators(components):
        if not has_terminal_path(generator, components, connections):
            violations.append(f"No termination for generator: {generator}")
    
    # 5. State Consistency
    shared_states = find_shared_states(components)
    for state in shared_states:
        if not properly_synchronized(state):
            violations.append(f"Race condition on: {state}")
    
    # 6. Semantic Validity
    purpose = infer_purpose(components, connections)
    if purpose is None:
        violations.append("Composition has unclear purpose")
    
    return len(violations) == 0, violations
```

## Pattern Library

Common validated patterns that follow grammar rules:

### Pattern: ETL (Extract-Transform-Load)
```
Components: DatabaseComponent → TransformerComponent → DatabaseComponent
Purpose: Data pipeline
Validation: ✅ Type-safe, acyclic, meaningful purpose
```

### Pattern: Control Loop
```
Components: SensorComponent → DecisionComponent → ActionComponent → SensorComponent
Purpose: Feedback control
Validation: ✅ Has delay (sensor measurement time), converges or terminates
```

### Pattern: Map-Reduce
```
Components: BroadcasterComponent → [WorkerComponent × N] → AggregatorComponent
Purpose: Parallel processing
Validation: ✅ Workers independent, aggregator handles all combinations
```

### Pattern: Publish-Subscribe
```
Components: BroadcasterComponent → [SubscriberComponent × N]
Purpose: Event distribution
Validation: ✅ Subscribers don't affect publisher, filtering is safe
```

### Pattern: Sense-Plan-Act
```
Components: SensorComponent → PlannerComponent → ActionComponent
Purpose: Intelligent agent behavior
Validation: ✅ Sequential flow, each stage has clear responsibility
```

## Integration with Niche Construction

The cognitive grammar enables niche construction by:

1. **Component Selection**: Grammar constraints filter valid component combinations for a domain
2. **Automatic Validation**: Proposed architectures are validated against grammar rules
3. **Pattern Matching**: Domain requirements map to validated grammar patterns
4. **Evolution**: Successful compositions become new grammar patterns
5. **Self-Assembly**: Grammar rules guide automatic architecture construction

Example:
```
Domain Requirement: "Need to forecast demand and optimize inventory"

Grammar Pattern Match:
    SensorComponent [Demand History]
    → PredictorComponent [Forecast Future]
    → OptimizerComponent [Set Reorder Point]
    → ActionComponent [Adjust Inventory]

Validation: ✅ Types match, temporal order correct, purpose clear
Result: Valid architecture for this niche
```

## Advanced Grammar Features

### Polymorphic Composition

Components can have multiple type signatures:

```
ClassifierComponent<T> {
    Input: T
    Output: ConceptNode (category)
}

Valid instantiations:
    ClassifierComponent<EntityNode> - classify entities
    ClassifierComponent<MetricNode> - classify measurements
    ClassifierComponent<StateNode> - classify states
```

### Contextual Rules

Grammar rules can adapt to domain context:

```
In Healthcare Domain:
    PatientFlowComponent [Output: EntityNode<Patient>]
    → TreatmentComponent [Input: EntityNode<Patient>]
    ✅ Valid because EntityNode subtype matches

In Manufacturing Domain:
    MaterialFlowComponent [Output: EntityNode<Part>]
    → TreatmentComponent [Input: EntityNode<Patient>]
    ❌ Invalid because subtypes don't match
```

### Emergent Patterns

New patterns emerge from component combinations:

```
Observed Successful Pattern:
    MonitorComponent → LearnerComponent → OptimizerComponent → ActionComponent
    (Used successfully in 50+ domains)

Grammar System:
    Promotes to first-class pattern: "Monitor-Learn-Optimize-Act"
    Becomes recommended pattern for adaptive control niches
```

## Conclusion

The cognitive grammar provides:
- **Formal rules** for valid component compositions
- **Automatic validation** of architectures
- **Pattern library** of proven compositions
- **Foundation for self-assembly** of domain-specific systems

This enables the niche construction engine to automatically assemble valid, meaningful cognitive architectures from modular components.

---

**Next**: See `niche_construction.md` for how grammar rules enable domain-specific self-assembly
