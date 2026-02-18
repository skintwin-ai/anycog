# AnyCog: OpenCog-Inspired AnyLogic Modeling Framework

AnyCog is a comprehensive framework for building integrated, multi-paradigm simulation models using AnyLogic with an OpenCog-inspired cognitive architecture. It enables seamless integration of Discrete-Event Simulation (DES), Agent-Based Modeling (ABM), System Dynamics (SD), Material Handling (MH), Movement, and Fluid dynamics into a unified cognitive system.

**NEW**: AnyCog now includes a **Niche Construction Engine** that enables **self-assembly of intelligence density** around domains of inquiry using modular simulation components as a cognitive grammar.

## Key Features

### 🧠 **Unified Cognitive Architecture**
- **AtomSpace**: Hypergraph knowledge representation for all simulation components
- **Multiple Cognitive Processes**: Specialized engines (DES, ABM, SD, MH, Movement, Fluid) working in harmony
- **Meta-Cognitive Layer**: Pattern recognition and insight generation across paradigms
- **Autognosis Layer**: Self-awareness and continuous self-optimization

### 🔗 **Seamless Multi-Paradigm Integration**
- Components communicate through a unified AtomSpace
- Cross-paradigm feedback loops are first-class citizens
- Integration patterns handle synchronization and state consistency
- Message-based communication ensures loose coupling

### 🎯 **Cognitive Synergy**
- Insights emerge from component interactions that would be invisible in single-paradigm models
- Meta-cognitive processes discover patterns across integrated subsystems
- Higher-order understanding from multiple levels of abstraction

### 🔄 **Self-Improving Systems**
- Autognosis layer monitors its own architecture
- Automatically identifies integration inefficiencies
- Proposes and executes optimizations
- Continuously evolves toward better performance

### 🧩 **Modular Component Library** (NEW)
- **18+ Reusable Components**: Perception, Reasoning, Action, Memory, Communication, Meta components
- **Domain-Agnostic Design**: Components work across different problem domains
- **Composable Architecture**: Build complex systems from simple, tested parts
- **Self-Describing**: Components publish capabilities and requirements

### 📐 **Cognitive Grammar** (NEW)
- **Formal Composition Rules**: Type checking, temporal ordering, resource conservation
- **Validation**: Automatic checking of architecture correctness
- **Pattern Library**: Proven component combinations for common scenarios
- **Extensible**: Add new rules and patterns as needed

### 🏗️ **Niche Construction Engine** (NEW)
- **Self-Assembly**: Architectures construct themselves from domain descriptions
- **Domain Analysis**: Automatically characterizes problem domains
- **Pattern Matching**: Finds optimal architectural patterns
- **Component Selection**: Chooses best components for the niche
- **Intelligence Density Optimization**: Maximizes capability per unit complexity
- **Continuous Learning**: Improves from experience

## Repository Structure

```
anycog/
├── SKILL.md                             # Main workflow documentation
├── README.md                            # This file
├── references/
│   ├── opencog_architecture.md          # Core architecture specification
│   ├── integration_guide.md             # Detailed integration patterns
│   ├── autognosis_integration.md        # Self-optimization documentation
│   ├── modular_components.md            # NEW: Modular component library
│   ├── cognitive_grammar.md             # NEW: Composition rules and patterns
│   ├── niche_construction.md            # NEW: Self-assembly engine
│   ├── domain_patterns.md               # NEW: Pattern catalog
│   ├── paradigms.md                     # Modeling paradigm reference
│   ├── psystem_membrane_computing.md    # P-system reference documentation
│   ├── process_modeling_library.md      # DES blocks reference
│   ├── material_handling_library.md     # MH blocks reference
│   ├── quick_reference.md               # Developer quick reference
│   └── other_libraries.md               # Movement & Fluid blocks reference
├── examples/
│   ├── integrated_supply_chain.md       # Complete integrated example
│   ├── psystem_sat_solver.md            # P-system SAT solver example
│   ├── torch_nn_neural_network.md       # Neural network training with torch/nn
│   ├── self_assembly_emergency_dept.md  # NEW: Self-assembly demonstration
│   ├── basicmodels/                     # AnyLogic basic examples
│   ├── bigbookmodels/                   # AnyLogic advanced examples
│   ├── models/                          # Additional examples
│   └── sdmodels/                        # System dynamics examples
```

## Getting Started

### Quick Start: Self-Assembly (NEW)

For automatic architecture construction:
1. **Describe your domain** in natural language
2. **Run the niche construction engine** to auto-generate architecture
3. **Validate and deploy** the assembled model

See:
- **[Niche Construction Engine](references/niche_construction.md)**: Self-assembly process
- **[Self-Assembly Example](examples/self_assembly_emergency_dept.md)**: Complete demonstration

### 1. Understand the Architecture

Start by reading the core architecture document:
- **[OpenCog Architecture](references/opencog_architecture.md)**: Learn about AtomSpace, cognitive processes, and the layered architecture

### 2. Explore Modular Components (NEW)

Learn about reusable building blocks:
- **[Modular Components](references/modular_components.md)**: 18+ component types
- **[Cognitive Grammar](references/cognitive_grammar.md)**: Composition rules
- **[Domain Patterns](references/domain_patterns.md)**: 8 proven patterns

### 3. Follow the Workflow

The complete workflow is documented in:
- **[SKILL.md](SKILL.md)**: Step-by-step guide to building integrated models

The 7-step workflow:
1. **Identify Required Components**: Which cognitive processes are needed?
2. **Design the AtomSpace Schema**: Define nodes and links for your domain
3. **Structure the Integrated Model**: Plan how components work together
4. **Implement Component Integration**: Build with proper registration and communication
5. **Enable Meta-Cognition**: Activate pattern recognition and coherence checking
6. **Analyze Results with Cognitive Synergy**: Extract cross-paradigm insights
7. **Apply Autognosis**: Let the system optimize itself

### 3. Study Integration Patterns

Learn how to integrate different paradigms:
- **[Integration Guide](references/integration_guide.md)**: Detailed patterns and protocols

Common patterns:
- **DES + ABM**: Agents with behaviors moving through processes
- **SD + DES**: Aggregate dynamics affecting process parameters
- **ABM + SD**: Individual decisions affecting system-level stocks
- **Multi-Engine**: Full cognitive synergy with all components

### 4. Explore the Examples

See complete implementations:
- **[Self-Assembly Emergency Department](examples/self_assembly_emergency_dept.md)**: NEW - Automated architecture construction
- **[Integrated Supply Chain Example](examples/integrated_supply_chain.md)**: Full model with all cognitive layers
- **[P-System SAT Solver](examples/psystem_sat_solver.md)**: Massively parallel computation for NP-complete problems
- **[Torch/nn Neural Network](examples/torch_nn_neural_network.md)**: Neural network training as a cognitive architecture

The examples demonstrate:
- **Self-assembly** of cognitive architectures from domain descriptions (NEW)
- Multi-paradigm integration (SD + DES + ABM + MH + P-System)
- Cross-paradigm feedback loops
- Meta-cognitive pattern mining
- Autognosis-driven optimization
- Neural architecture search and self-improvement

## Core Concepts

### AtomSpace (Layer 1)

A hypergraph containing all simulation knowledge:

**Nodes**:
- `ConceptNode`: Types and concepts
- `EntityNode`: Individual instances
- `ProcessNode`: Active processes
- `StateNode`: System states
- `ParameterNode`: Configuration values
- `MetricNode`: Measurements

**Links**:
- `InheritanceLink`: Type hierarchies
- `TransitionLink`: State changes
- `InfluenceLink`: Causal relationships
- `InteractionLink`: Agent communications
- `ContainmentLink`: Hierarchical structure
- `TemporalLink`: Time ordering

### Modular Components (NEW)

Reusable building blocks for cognitive architectures:

**Component Categories**:
- **Perception**: Sensors, counters, pattern detectors
- **Reasoning**: Classifiers, optimizers, predictors, decision-makers
- **Action**: Generators, transformers, routers, allocators
- **Memory**: Buffers, databases, caches
- **Communication**: Broadcasters, subscribers, aggregators
- **Meta**: Monitors, learners, reflectors

Each component:
- Is **domain-agnostic** (works across problem domains)
- Has **clear interfaces** (declared inputs/outputs)
- Is **composable** (combines with other components)
- Is **self-describing** (publishes capabilities)

See **[Modular Components](references/modular_components.md)** for full catalog.

### Cognitive Grammar (NEW)

Formal rules for composing components into valid architectures:

**Core Rules**:
1. **Type Matching**: Output types must match input types
2. **Temporal Ordering**: Causality preserved in execution order
3. **Resource Conservation**: All generated entities have lifecycle endpoints
4. **Feedback Validity**: Cycles must have break conditions
5. **State Consistency**: Shared state must be synchronized

**Composition Patterns**:
- Linear Pipeline (sequential processing)
- Branching Decision (conditional routing)
- Feedback Loop (iterative refinement)
- Parallel Composition (concurrent operations)
- Hierarchical Structure (nested systems)
- Observer Pattern (passive monitoring)

See **[Cognitive Grammar](references/cognitive_grammar.md)** for complete specification.

### Niche Construction Engine (NEW)

Automated system for self-assembling cognitive architectures:

**Process**:
1. **Domain Analysis**: Extract characteristics from description
2. **Pattern Matching**: Find optimal architectural patterns
3. **Component Selection**: Choose best components for niche
4. **Architecture Composition**: Assemble according to grammar
5. **Validation**: Ensure correctness
6. **Performance Prediction**: Estimate fitness
7. **Evolution**: Iteratively improve
8. **Meta-Learning**: Capture experience for future use

**Intelligence Density**:
```
Intelligence Density = (Capability + Synergy) / Complexity
```

Higher density = more problem-solving power per unit of model complexity.

See **[Niche Construction](references/niche_construction.md)** for complete specification.

### Cognitive Processes (Layer 2)

Specialized engines operating on the AtomSpace:

- **Process Flow Engine (DES)**: Sequential processes, queues, resources
- **Agent Behavior Engine (ABM)**: Autonomous agents, statecharts, interactions
- **System Dynamics Engine (SD)**: Stocks, flows, feedback loops
- **Material Flow Engine (MH)**: Conveyors, transporters, warehouses
- **Movement Engine**: Pedestrians, vehicles, trains
- **Fluid Dynamics Engine**: Tanks, pipelines, continuous flows
- **P-System Membrane Computing**: Bio-inspired parallel computation, membrane hierarchies, massively parallel rule execution

### Meta-Cognitive Layer (Layer 3)

Higher-order pattern recognition:

- **Pattern Miner**: Discovers recurring patterns
- **Coherence Checker**: Validates consistency
- **Optimization Detector**: Identifies improvements
- **Insight Generator**: Produces human-readable insights

### Autognosis Layer (Layer 4)

Self-awareness and optimization:

- **Self-Monitor**: Observes all cognitive processes
- **Self-Modeler**: Builds hierarchical self-images
- **Meta-Cognitive Analyzer**: Generates insights about architecture
- **Self-Optimizer**: Proposes and executes improvements

## Integration Principles

### 1. Unified Knowledge Representation
- All components share knowledge through the AtomSpace
- Single source of truth for all simulation state
- Typed nodes and links ensure semantic clarity

### 2. Loose Coupling
- Components communicate through well-defined interfaces
- Message-based communication via MessageBus
- No direct dependencies between engines

### 3. Temporal Consistency
- Unified simulation clock across all paradigms
- Strict event ordering preserves causality
- Synchronization strategies handle different time scales

### 4. Cognitive Synergy
- Components designed to enhance each other
- Cross-paradigm feedback loops encouraged
- Emergent insights from component interactions

## Use Cases

### Supply Chain Management
- **SD**: Demand forecasting, inventory policies
- **DES**: Order fulfillment, production processes
- **ABM**: Customer purchasing behavior
- **MH**: Warehouse and transportation operations

### Smart Manufacturing
- **DES**: Production process flow
- **MH**: Material handling (AGVs, conveyors)
- **Fluid**: Chemical batch processing
- **ABM**: Worker behavior and learning
- **SD**: Overall throughput dynamics
- **P-System**: Parallel scheduling optimization

### Urban Mobility
- **Movement**: Pedestrian, vehicle, and rail flows
- **SD**: Infrastructure capacity modeling
- **ABM**: Individual travel decisions
- **DES**: Transit scheduling

### Healthcare Systems
- **DES**: Patient flow through departments
- **ABM**: Individual patient and staff behaviors
- **SD**: Resource capacity planning
- **Meta-Cognitive**: Bottleneck identification

### Computational Biology
- **P-System**: Cellular processes and membrane dynamics
- **ABM**: Cell-to-cell interactions
- **SD**: Population-level biological dynamics
- **Meta-Cognitive**: Emergent pattern discovery

### Optimization and Search
- **P-System**: Massively parallel search (NP-complete problems)
- **DES**: Sequential constraint checking
- **ABM**: Distributed algorithm coordination
- **Meta-Cognitive**: Solution quality analysis

### Neural Networks and Machine Learning
- **DES**: Training loop iterations, batch processing
- **ABM**: Individual neuron behaviors, weight updates
- **SD**: Learning rate schedules, gradient dynamics
- **P-System**: Parallel layer computations, backpropagation
- **Meta-Cognitive**: Architecture optimization, hyperparameter tuning
- **Autognosis**: Neural architecture search, self-improving networks

## Benefits

### For Modelers
- **Reduced Complexity**: Framework handles integration boilerplate
- **Higher Quality**: Coherence checking catches errors early
- **Better Insights**: Meta-cognitive layer discovers non-obvious patterns
- **Continuous Improvement**: Autognosis optimizes over time

### For Organizations
- **More Accurate Models**: Multi-paradigm integration captures reality better
- **Faster Development**: Reusable patterns and proven architectures
- **Maintainable Code**: Clear separation of concerns and documentation
- **Future-Proof**: Extensible architecture adapts to new requirements

### For Research
- **Novel Insights**: Cognitive synergy reveals emergent phenomena
- **Reproducible**: Well-documented architecture and integration patterns
- **Extensible**: Easy to add new cognitive processes or meta-cognitive rules
- **Self-Documenting**: AtomSpace schema serves as living documentation

## Advanced Topics

### Performance Optimization
- AtomSpace caching strategies
- Message batching for reduced overhead
- Synchronization strategy selection
- Profiling and bottleneck identification

### Custom Cognitive Processes
- Extending the framework with new engines
- Defining custom node and link types
- Creating specialized integration patterns

### Meta-Cognitive Rules
- Writing custom pattern mining rules
- Defining coherence checking constraints
- Creating domain-specific optimizations

### Autognosis Configuration
- Manual vs. automatic optimization modes
- Risk assessment for proposed changes
- Continuous improvement cycles

## Contributing

When extending AnyCog:

1. **Follow the Architecture**: Use AtomSpace for all shared state
2. **Document Integration Points**: Specify what your component reads/writes
3. **Enable Meta-Cognition**: Make your component observable
4. **Support Autognosis**: Allow architectural introspection
5. **Test Integration**: Validate with existing components
6. **Add Examples**: Demonstrate your contribution in context

## References

- OpenCog Project: https://opencog.org
- AnyLogic Simulation Software: https://www.anylogic.com
- Cognitive Architecture Literature: See `references/` directory

## License

This framework is based on analysis of official AnyLogic examples and implements an OpenCog-inspired architecture for simulation modeling.

## Version

AnyCog Framework v1.0 - OpenCog-Inspired Multi-Paradigm Simulation Architecture

---

**Built on**: Analysis of 400+ AnyLogic example models  
**Inspired by**: OpenCog cognitive architecture  
**Purpose**: Differentiate all features and functions to integrate as a single cohesive whole with all components as a unified cognitive simulation architecture
