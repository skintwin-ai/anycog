# Modular Simulation Components Library

## Overview

This document defines the **Modular Component Library** for AnyCog - a catalog of reusable, domain-agnostic simulation primitives that can be assembled into specialized cognitive architectures around any domain of inquiry.

Unlike the traditional approach where modelers manually select and integrate cognitive processes, these modular components enable **self-assembly** of intelligence density around specific problem domains through a niche construction engine.

## Component Architecture

### Component Hierarchy

```
SimulationComponent (Abstract)
├── PerceptionComponent (Sensing & Observation)
├── ReasoningComponent (Logic & Decision-Making)
├── ActionComponent (Execution & Effect)
├── MemoryComponent (Storage & Recall)
├── CommunicationComponent (Message Passing)
└── MetaComponent (Self-Reflection & Learning)
```

Each component is:
- **Modular**: Self-contained with clear interfaces
- **Composable**: Can combine with other components
- **Domain-Agnostic**: Works across different problem spaces
- **AtomSpace-Native**: Communicates through hypergraph
- **Self-Describing**: Publishes its capabilities and requirements

## Core Component Primitives

### 1. Perception Components

Components that observe and measure the simulation environment.

#### 1.1 SensorComponent
**Purpose**: Detect and measure environmental properties
**Inputs**: 
- Target entities (EntityNodes)
- Measurement specifications (ParameterNodes)
**Outputs**: 
- MetricNodes with observed values
**AtomSpace Pattern**:
```
(SensorNode "temperature_sensor")
  → (ObservationLink 
      → (EntityNode "warehouse_zone_1")
      → (MetricNode "temperature" value=72.5))
```

#### 1.2 CounterComponent
**Purpose**: Track frequency and accumulation of events
**Inputs**: Event streams (TemporalLinks)
**Outputs**: MetricNodes with counts and rates
**AtomSpace Pattern**:
```
(CounterNode "order_counter")
  → (MeasurementLink
      → (ProcessNode "order_arrival")
      → (MetricNode "arrival_count" value=150))
```

#### 1.3 PatternDetectorComponent
**Purpose**: Identify recurring patterns in observations
**Inputs**: Time series data (MetricNodes)
**Outputs**: PatternNodes describing detected patterns
**AtomSpace Pattern**:
```
(PatternDetectorNode "demand_pattern_detector")
  → (PatternLink
      → (MetricNode "daily_orders")
      → (PatternNode "weekly_cycle" confidence=0.87))
```

### 2. Reasoning Components

Components that process information and make decisions.

#### 2.1 ClassifierComponent
**Purpose**: Categorize entities based on attributes
**Inputs**: EntityNodes with properties
**Outputs**: InheritanceLinks to category ConceptNodes
**AtomSpace Pattern**:
```
(ClassifierNode "customer_classifier")
  → (InheritanceLink
      → (EntityNode "customer_123")
      → (ConceptNode "high_value_customer"))
```

#### 2.2 OptimizerComponent
**Purpose**: Find optimal parameter values
**Inputs**: ParameterNodes, ObjectiveNode
**Outputs**: Updated ParameterNodes with optimal values
**AtomSpace Pattern**:
```
(OptimizerNode "inventory_optimizer")
  → (OptimizationLink
      → (ParameterNode "reorder_point")
      → (ParameterNode "reorder_point" optimal_value=150))
```

#### 2.3 PredictorComponent
**Purpose**: Forecast future states based on historical data
**Inputs**: Historical MetricNodes
**Outputs**: ForecastNodes with predicted values
**AtomSpace Pattern**:
```
(PredictorNode "demand_forecaster")
  → (ForecastLink
      → (MetricNode "historical_demand")
      → (ForecastNode "next_week_demand" value=1250 confidence=0.82))
```

#### 2.4 DecisionComponent
**Purpose**: Make binary or multi-way choices
**Inputs**: StateNodes, RuleNodes
**Outputs**: TransitionLinks to chosen states
**AtomSpace Pattern**:
```
(DecisionNode "routing_decision")
  → (TransitionLink
      → (StateNode "waiting_for_routing")
      → (StateNode "route_to_station_A")
      → (RuleNode "shortest_queue_rule"))
```

### 3. Action Components

Components that execute changes in the simulation.

#### 3.1 GeneratorComponent
**Purpose**: Create new entities
**Inputs**: EntityType (ConceptNode), generation rules
**Outputs**: New EntityNodes
**AtomSpace Pattern**:
```
(GeneratorNode "order_generator")
  → (CreationLink
      → (ConceptNode "customer_order")
      → (EntityNode "order_501" timestamp=1000))
```

#### 3.2 TransformerComponent
**Purpose**: Modify entity properties
**Inputs**: EntityNodes, transformation rules
**Outputs**: Updated EntityNodes
**AtomSpace Pattern**:
```
(TransformerNode "assembly_process")
  → (TransformationLink
      → (EntityNode "raw_material")
      → (EntityNode "finished_product"))
```

#### 3.3 RouterComponent
**Purpose**: Direct entities to destinations
**Inputs**: EntityNodes, routing policies
**Outputs**: TemporalLinks showing routes
**AtomSpace Pattern**:
```
(RouterNode "smart_router")
  → (RoutingLink
      → (EntityNode "package_123")
      → (ProcessNode "shipping_station_2"))
```

#### 3.4 AllocatorComponent
**Purpose**: Assign resources to requesters
**Inputs**: Resource requests, availability
**Outputs**: AllocationLinks
**AtomSpace Pattern**:
```
(AllocatorNode "workforce_allocator")
  → (AllocationLink
      → (EntityNode "worker_5")
      → (ProcessNode "assembly_task_12"))
```

### 4. Memory Components

Components that store and retrieve information.

#### 4.1 BufferComponent
**Purpose**: Temporarily store entities
**Inputs**: EntityNodes
**Outputs**: Queued EntityNodes
**AtomSpace Pattern**:
```
(BufferNode "order_buffer")
  → (ContainmentLink
      → (EntityNode "order_100")
      → (StateNode "buffer_contents" size=15))
```

#### 4.2 DatabaseComponent
**Purpose**: Persist and query historical data
**Inputs**: Query specifications
**Outputs**: Retrieved nodes matching query
**AtomSpace Pattern**:
```
(DatabaseNode "order_history")
  → (QueryLink
      → (ConceptNode "orders_last_month")
      → (EntityNode "order_450", "order_451", ...))
```

#### 4.3 CacheComponent
**Purpose**: Store frequently accessed values
**Inputs**: Key-value pairs
**Outputs**: Cached values with fast retrieval
**AtomSpace Pattern**:
```
(CacheNode "demand_cache")
  → (CacheEntryLink
      → (ParameterNode "product_A_demand")
      → (MetricNode "cached_value" value=500 ttl=3600))
```

### 5. Communication Components

Components that enable message passing and coordination.

#### 5.1 BroadcasterComponent
**Purpose**: Send messages to multiple receivers
**Inputs**: Message content
**Outputs**: MessageLinks to all subscribers
**AtomSpace Pattern**:
```
(BroadcasterNode "status_broadcaster")
  → (BroadcastLink
      → (MessageNode "system_alert")
      → (EntityNode "subscriber_1", "subscriber_2", ...))
```

#### 5.2 SubscriberComponent
**Purpose**: Receive and filter messages
**Inputs**: Message streams, filter criteria
**Outputs**: Filtered MessageNodes
**AtomSpace Pattern**:
```
(SubscriberNode "urgent_alerts_only")
  → (SubscriptionLink
      → (MessageNode "alert" priority="high")
      → (ProcessNode "alert_handler"))
```

#### 5.3 AggregatorComponent
**Purpose**: Combine multiple inputs into summary
**Inputs**: Multiple MetricNodes
**Outputs**: Aggregated MetricNode
**AtomSpace Pattern**:
```
(AggregatorNode "daily_summary")
  → (AggregationLink
      → (MetricNode "hourly_sales_1", "hourly_sales_2", ...)
      → (MetricNode "daily_total_sales" value=15000))
```

### 6. Meta Components

Components that enable self-reflection and adaptation.

#### 6.1 MonitorComponent
**Purpose**: Track component performance
**Inputs**: Component execution metrics
**Outputs**: PerformanceNodes
**AtomSpace Pattern**:
```
(MonitorNode "throughput_monitor")
  → (MonitoringLink
      → (ProcessNode "assembly_line")
      → (PerformanceNode "throughput" value=125 units/hour))
```

#### 6.2 LearnerComponent
**Purpose**: Adapt behavior based on experience
**Inputs**: Experience traces (TemporalLinks)
**Outputs**: Updated RuleNodes
**AtomSpace Pattern**:
```
(LearnerNode "routing_learner")
  → (LearningLink
      → (ExperienceNode "routing_episode_100")
      → (RuleNode "improved_routing_policy" performance=+15%))
```

#### 6.3 ReflectorComponent
**Purpose**: Build self-images of system structure
**Inputs**: AtomSpace contents
**Outputs**: Hierarchical self-models
**AtomSpace Pattern**:
```
(ReflectorNode "architecture_reflector")
  → (ReflectionLink
      → (ConceptNode "current_architecture")
      → (ModelNode "self_image_L2" abstraction="component_interaction_graph"))
```

## Component Composition Patterns

### Pattern 1: Sense-Reason-Act Loop
```
SensorComponent → ClassifierComponent → DecisionComponent → ActionComponent
```
Example: Temperature sensor → classify as "too_hot" → decide to activate cooling → execute cooling action

### Pattern 2: Generator-Buffer-Processor
```
GeneratorComponent → BufferComponent → TransformerComponent
```
Example: Generate orders → buffer in queue → process through fulfillment

### Pattern 3: Monitor-Learn-Optimize
```
MonitorComponent → LearnerComponent → OptimizerComponent
```
Example: Monitor performance → learn patterns → optimize parameters

### Pattern 4: Perceive-Predict-Prepare
```
SensorComponent → PredictorComponent → AllocatorComponent
```
Example: Sense demand → predict future needs → allocate resources proactively

### Pattern 5: Broadcast-Subscribe-Aggregate
```
BroadcasterComponent → SubscriberComponent → AggregatorComponent
```
Example: Broadcast events → subscribers filter → aggregate for summary

## Component Interface Specification

Every component must implement:

### Required Methods
```java
interface SimulationComponent {
    // Lifecycle
    void initialize(AtomSpace atomSpace, Configuration config);
    void activate();
    void deactivate();
    void shutdown();
    
    // Capabilities
    ComponentCapabilities getCapabilities();
    List<NodeType> getRequiredInputs();
    List<NodeType> getProducedOutputs();
    
    // Execution
    void step(double currentTime);
    boolean canExecute();
    
    // Integration
    void registerWithAtomSpace();
    void subscribeToEvents(EventBus eventBus);
    
    // Meta
    ComponentMetrics getMetrics();
    void introspect(ReflectorComponent reflector);
}
```

### Required AtomSpace Registrations
```java
// Register self as a node
atomSpace.addNode(new ComponentNode(this.getName(), this.getType()));

// Declare inputs
for (NodeType inputType : getRequiredInputs()) {
    atomSpace.addLink(new RequiresLink(
        new ComponentNode(this.getName()),
        new ConceptNode(inputType.name())
    ));
}

// Declare outputs
for (NodeType outputType : getProducedOutputs()) {
    atomSpace.addLink(new ProducesLink(
        new ComponentNode(this.getName()),
        new ConceptNode(outputType.name())
    ));
}
```

## Domain-Agnostic Properties

All components are designed to be **domain-agnostic** by:

1. **Type Polymorphism**: Components work with abstract node types (EntityNode, MetricNode) rather than domain-specific types
2. **Configuration Injection**: Domain-specific behavior is injected via configuration, not hardcoded
3. **Semantic Tags**: Use ConceptNodes to tag domain semantics without changing component logic
4. **Composable Interfaces**: Components chain together regardless of domain context

Example: A `ClassifierComponent` can classify:
- Customers into segments (retail domain)
- Patients into risk categories (healthcare domain)
- Parts into quality tiers (manufacturing domain)

The classification logic is the same; only the semantic tags differ.

## Integration with Cognitive Processes

These modular components **compose into** the higher-level cognitive processes:

- **DES Process Flow Engine** = GeneratorComponent + BufferComponent + TransformerComponent + RouterComponent
- **ABM Agent Behavior Engine** = SensorComponent + DecisionComponent + ActionComponent + CommunicationComponent
- **SD System Dynamics Engine** = CounterComponent + AggregatorComponent + PredictorComponent
- **Meta-Cognitive Layer** = MonitorComponent + PatternDetectorComponent + ReflectorComponent
- **Autognosis Layer** = ReflectorComponent + LearnerComponent + OptimizerComponent

The niche construction engine selects and assembles these primitives based on domain requirements.

## Benefits of Modular Architecture

### For Developers
- **Reusability**: Write once, use in many domains
- **Testability**: Small, focused components are easy to test
- **Maintainability**: Changes localized to specific components
- **Composability**: Build complex systems from simple parts

### For Niche Construction
- **Automatic Assembly**: Components declare capabilities; engine matches to needs
- **Optimization**: Replace components with better implementations
- **Adaptation**: Add new components without changing existing ones
- **Discovery**: Find unexpected component combinations

### For Domains
- **Rapid Prototyping**: Assemble domain architectures quickly
- **Best Practices**: Components embody proven patterns
- **Consistency**: Same components across different domains
- **Evolution**: Improve components benefit all domains

## Next Steps

1. **Implement Component Base Classes**: Create abstract base implementations
2. **Build Component Library**: Implement all 18+ component types
3. **Create Component Registry**: Catalog all available components
4. **Develop Niche Constructor**: Engine for domain-specific assembly
5. **Add Domain Patterns**: Library of proven component compositions
6. **Enable Self-Assembly**: Automated architecture construction

See:
- `cognitive_grammar.md` - Rules for component composition
- `niche_construction.md` - Domain-specific assembly engine
- `domain_patterns.md` - Catalog of domain-specific configurations

---

**Status**: Component specification complete  
**Next**: Implement cognitive grammar for composition rules
