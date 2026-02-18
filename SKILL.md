---
name: anylogic-modeler
description: Build and analyze simulation models using AnyLogic with OpenCog-inspired cognitive architecture. Use for tasks involving discrete-event, agent-based, and system dynamics modeling, or any combination of these paradigms. Supports fully integrated multi-paradigm models with cognitive synergy. Triggers on mentions of AnyLogic, simulation modeling, DES, ABM, SD, or OpenCog.
---

# AnyCog: AnyLogic Modeler with OpenCog Architecture

This skill provides the knowledge and workflows required to build, analyze, and understand AnyLogic simulation models using an integrated OpenCog-inspired cognitive architecture. It is based on an analysis of over 400 official AnyLogic example models and implements a unified framework for cognitive simulation.

## Core Workflow

When tasked with an AnyLogic modeling problem, follow these sequential steps using the integrated OpenCog architecture:

1.  **Identify Required Components**: Determine which cognitive processes and paradigms are needed for the problem.
2.  **Design the AtomSpace Schema**: Define the knowledge representation structure (nodes and links).
3.  **Structure the Integrated Model**: Outline how different cognitive processes will work together.
4.  **Implement Component Integration**: Build the model with proper component registration and communication.
5.  **Enable Meta-Cognition**: Activate pattern recognition and coherence checking.
6.  **Analyze Results with Cognitive Synergy**: Run experiments that leverage cross-paradigm insights.
7.  **Apply Autognosis for Self-Optimization**: Let the system reflect on and improve its own architecture.

## Step 1: Identify Required Components

Before writing any code, you MUST determine which cognitive processes and modeling paradigms are needed. The OpenCog architecture integrates multiple specialized engines:

-   **Read First**: `references/opencog_architecture.md` - Understand the unified cognitive architecture
-   **Then Review**: `references/paradigms.md` - For paradigm-specific details

### Available Cognitive Processes

| Cognitive Process | Use When... | Key Capabilities |
| ----------------- | ----------- | ---------------- |
| **Process Flow Engine (DES)** | Modeling sequential processes, queues, resources | Entity lifecycle, resource scheduling, process optimization |
| **Agent Behavior Engine (ABM)** | Modeling autonomous individuals, emergent behavior | Statecharts, agent interactions, population dynamics |
| **System Dynamics Engine (SD)** | Modeling aggregate feedback loops, policies | Stock-flow calculations, feedback analysis, policy testing |
| **Material Flow Engine (MH)** | Modeling physical material handling | Conveyors, transporters, warehouses, AGVs |
| **Movement Engine (Spatial)** | Modeling pedestrians, vehicles, trains | Pathfinding, collision avoidance, traffic simulation |
| **Fluid Dynamics Engine** | Modeling continuous flows, chemicals, bulk materials | Tank levels, pipeline flows, batch processing |

Most real-world problems require **multiple cognitive processes working together** (cognitive synergy). The OpenCog architecture makes this integration seamless through the unified AtomSpace.

## Step 2: Design the AtomSpace Schema

Define the knowledge representation for your model using the OpenCog-inspired AtomSpace:

### 2.1 Identify Node Types

Map your domain entities to AtomSpace nodes:

-   **ConceptNode**: Entity types, paradigms, categories (e.g., "Customer", "Vehicle", "DES")
-   **EntityNode**: Individual instances (e.g., customer #123, truck #5)
-   **ProcessNode**: Active processes and operations (e.g., "CheckIn", "Transport", "Manufacture")
-   **StateNode**: System states and accumulations (e.g., "QueueLength", "InventoryLevel")
-   **ParameterNode**: Configuration values (e.g., "ArrivalRate", "ServiceTime")
-   **MetricNode**: Measurements and KPIs (e.g., "Throughput", "Utilization")

### 2.2 Define Link Types

Specify how nodes relate to each other:

-   **InheritanceLink**: Type hierarchies (e.g., Truck inherits from Vehicle)
-   **TransitionLink**: State changes (e.g., Waiting → Being Served)
-   **InfluenceLink**: Causal relationships (e.g., Demand influences Production)
-   **InteractionLink**: Agent communications (e.g., Agent A requests Resource from Agent B)
-   **ContainmentLink**: Hierarchical containment (e.g., Items in Rack, Agents in Population)
-   **TemporalLink**: Time-ordered sequences (e.g., Event A before Event B)

### 2.3 Document the Schema

Create a diagram or text description showing:
1. All node types and their purposes
2. All link types and what they represent
3. Key relationships between major components

This schema becomes the foundation for the unified knowledge representation.

## Step 3: Structure the Integrated Model

Design how different cognitive processes will work together in the OpenCog architecture:

### 3.1 Component Architecture

Define the model structure:

```
Main (Root Agent)
├── AtomSpace (Knowledge Base)
├── Cognitive Processes (Selected Engines)
├── Meta-Cognitive Layer (Pattern Recognition)
├── Autognosis Layer (Self-Awareness)
└── Integration Manager
```

### 3.2 Integration Points

Specify how components interact:

1.  **Data Flow**: Which engines read/write which StateNodes?
2.  **Event Dependencies**: What temporal ordering is required?
3.  **Feedback Loops**: Are there cross-paradigm feedback loops?
4.  **Synchronization**: Where is time coordination critical?

### 3.3 Agent Types and Hierarchy

Define agents as in traditional AnyLogic:
-   **Main Agent**: Contains AtomSpace and Integration Manager
-   **Domain Agents**: Entity types (e.g., Customer, Vehicle)
-   **Controller Agents**: Cognitive process implementations
-   **Environment**: Spatial representation if needed

### 3.4 Experiments

Choose experiment types:
-   **SimulationExperiment**: Standard simulation runs
-   **OptimizationExperiment**: Parameter optimization with cognitive synergy
-   **Sensitivity Analysis**: Test meta-cognitive insights

## Step 4: Implement Component Integration

Implement each cognitive process with proper integration hooks. Reference guides provide details on library blocks:

-   **For DES/Process Flow Engine**: Read `references/process_modeling_library.md`
-   **For Material Flow Engine**: Read `references/material_handling_library.md`
-   **For Movement/Spatial Engines**: Read `references/other_libraries.md`

### 4.1 Component Registration

Each component registers itself in the AtomSpace upon initialization:

```java
// Example: Register a process block
atomSpace.registerComponent(
    componentType: ProcessNode,
    name: "CustomerCheckIn",
    properties: {
        paradigm: "DES",
        library: "ProcessModeling",
        function: "Service"
    },
    connections: [downstreamBlocks, requiredResources]
);
```

### 4.2 Implementation Patterns by Engine

-   **Process Flow Engine (DES)**: Use Process Modeling Library blocks in flowcharts
    - Register Source, Queue, Delay, Service, Sink blocks as ProcessNodes
    - Create TransitionLinks between connected blocks
    - Register ResourcePools as EntityNodes

-   **Agent Behavior Engine (ABM)**: Use Statecharts for behavior
    - Register States as StateNodes
    - Register Transitions as TransitionLinks
    - Use InteractionLinks for agent communications

-   **System Dynamics Engine (SD)**: Use Stocks and Flows
    - Register Stocks as StateNodes
    - Register Flows as ProcessNodes
    - Create InfluenceLinks for dependencies

-   **Material Flow Engine**: Extend Process Flow with spatial elements
    - Register Conveyors, Transporters as ProcessNodes
    - Use ContainmentLinks for storage (Racks)
    - Coordinate with Process Flow Engine

-   **Movement Engine**: Handle spatial movement
    - Register movement commands as ProcessNodes
    - Maintain spatial relationships in AtomSpace
    - Integrate with environment representation

-   **Fluid Dynamics Engine**: Model continuous flows
    - Register Tanks as StateNodes
    - Register Pipelines as ProcessNodes
    - Use InfluenceLinks for flow relationships

### 4.3 Integration Manager Setup

Implement the Integration Manager to coordinate all components:

```java
// Pseudo-code for Integration Manager
class IntegrationManager {
    ComponentRegistry registry;
    MessageBus messageBus;
    SynchronizationController syncController;
    
    void initialize() {
        // Register all cognitive processes
        // Setup message routing
        // Initialize synchronization clock
    }
    
    void step() {
        // Coordinate time advancement across all engines
        // Route messages between components
        // Maintain AtomSpace consistency
    }
}
```

### 4.4 Custom Logic

For complex integration logic, use Java code in action handlers:
-   Read from AtomSpace to access shared knowledge
-   Write to AtomSpace to update shared state
-   Use MessageBus for inter-component communication

## Step 5: Enable Meta-Cognition

Activate the meta-cognitive layer to identify patterns and insights across the integrated model:

### 5.1 Pattern Mining

Configure the Pattern Miner to discover:
-   **Cross-paradigm patterns**: Recurring interaction sequences between different engines
-   **Bottleneck patterns**: Resource constraints affecting multiple paradigms
-   **Emergent behavior patterns**: Unexpected system-level behaviors

### 5.2 Coherence Checking

Enable the Coherence Checker to validate:
-   **Temporal consistency**: Events occur in correct order across engines
-   **State consistency**: Shared states remain valid across paradigm boundaries
-   **Semantic coherence**: Integration logic matches conceptual model

### 5.3 Optimization Detection

Activate the Optimization Detector to find:
-   **Parameter tuning opportunities**: Values that could improve cross-paradigm performance
-   **Architectural improvements**: Better ways to structure component interactions
-   **Simplification opportunities**: Redundant components that could be merged

### 5.4 Insight Generation

Configure the Insight Generator to produce:
-   **Natural language summaries**: Human-readable descriptions of discovered patterns
-   **Visualization recommendations**: Suggested charts to illustrate insights
-   **Action items**: Concrete suggestions for model improvement

## Step 6: Analyze Results with Cognitive Synergy

Run experiments that leverage the integrated architecture:

### 6.1 Multi-Level Analysis

Collect data at multiple levels:
-   **Micro-level**: Individual entity behaviors (from ABM and DES engines)
-   **Meso-level**: Process and flow metrics (from DES, Material Flow engines)
-   **Macro-level**: System-wide dynamics (from SD engine)
-   **Meta-level**: Cross-paradigm patterns (from meta-cognitive layer)

### 6.2 Cognitive Synergy Insights

Look for insights that emerge only from integration:
-   How individual behaviors (ABM) affect system dynamics (SD)
-   How aggregate policies (SD) constrain process performance (DES)
-   How material flows (MH) interact with agent decisions (ABM)

### 6.3 Present Results

Create comprehensive reports showing:
-   **Traditional metrics**: Standard statistics from each paradigm
-   **Integration metrics**: Cross-paradigm performance indicators
-   **Meta-cognitive insights**: Patterns discovered by pattern mining
-   **Recommendations**: Optimization suggestions from meta-cognitive layer

Use charts, plots, and visualizations. The `analysis` and `file` tools are well-suited for this.

## Step 7: Apply Autognosis for Self-Optimization

The final step enables the system to become self-aware and continuously improve its own architecture. This is the highest level of the cognitive hierarchy.

-   **Read for details**: `references/autognosis_integration.md`

### 7.1 Self-Monitoring

The Autognosis layer monitors the entire integrated system:
-   **Component selection**: Which cognitive processes were activated and why
-   **Integration patterns**: How components communicate and interact
-   **Performance metrics**: Execution time, memory usage, convergence rates
-   **Workflow adherence**: How closely the 7-step process was followed

### 7.2 Self-Modeling

Build hierarchical self-images of the integrated architecture:
-   **Level 0**: Raw AtomSpace structure (all nodes and links)
-   **Level 1**: Identified patterns (which engines are active, key interactions)
-   **Level 2**: Purpose and goals (what questions the simulation answers)
-   **Level 3**: Strategic context (why this architecture vs. alternatives)

### 7.3 Meta-Cognitive Analysis

Generate higher-order insights about the integration:
-   **Architecture fitness**: Is this the most efficient integration pattern?
-   **Cognitive synergy effectiveness**: Are the engines actually benefiting from integration?
-   **Complexity vs. value**: Is the integrated architecture justified by the insights gained?
-   **Abstraction opportunities**: Could the integration be simplified?

### 7.4 Self-Optimization

Propose and execute improvements:
-   **Refactor integration patterns**: Simplify complex interactions
-   **Optimize synchronization**: Reduce coordination overhead
-   **Prune unnecessary components**: Remove engines that aren't contributing
-   **Enhance cognitive synergy**: Strengthen beneficial cross-paradigm interactions

### 7.5 Continuous Improvement Cycle

The Autognosis layer operates continuously:
1. Monitor model execution and gather performance data
2. Build updated self-images reflecting current state
3. Generate new insights about integration effectiveness
4. Propose optimizations based on insights
5. Execute approved optimizations
6. Return to step 1

This ensures the integrated model continuously evolves toward optimal performance and design.

## Summary

The AnyCog architecture transforms AnyLogic modeling from paradigm-specific simulation into a unified cognitive system. By following this 7-step workflow, you create models where:

-   Multiple paradigms work seamlessly together through the AtomSpace
-   Meta-cognitive processes discover insights impossible to find in isolated paradigms
-   The system continuously improves itself through Autognosis
-   All components are differentiated yet integrated into a cohesive whole

This OpenCog-inspired approach represents the future of simulation modeling: not just models of systems, but cognitive architectures that think about systems.
