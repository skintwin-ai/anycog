# Component Integration Guide

This guide provides detailed instructions for integrating different AnyLogic modeling components within the OpenCog-inspired AnyCog architecture.

## Integration Principles

### 1. Unified Knowledge Representation

All components share knowledge through the AtomSpace hypergraph:
- **Single Source of Truth**: The AtomSpace is the authoritative repository for all simulation knowledge
- **Typed Nodes and Links**: Every element has a well-defined type and purpose
- **Referential Integrity**: Links maintain valid references to existing nodes

### 2. Loose Coupling

Components communicate through well-defined interfaces:
- **Message-Based Communication**: Use the MessageBus for inter-component messages
- **Event-Driven Updates**: Components react to state changes via observers
- **No Direct Dependencies**: Components don't directly call each other's methods

### 3. Temporal Consistency

All components operate on a unified simulation clock:
- **Synchronized Time**: The Integration Manager coordinates time advancement
- **Event Ordering**: Events are processed in strict temporal order
- **Causality Preservation**: Cause always precedes effect across all paradigms

## Integration Patterns

### Pattern 1: DES + ABM Integration (Process with Agent Behavior)

**Use Case**: Agents with individual behaviors move through a discrete-event process.

**AtomSpace Structure**:
```
ConceptNode: "Customer"
├── InheritanceLink → ConceptNode: "Agent"
├── EntityNode: "Customer_1"
│   ├── StateNode: "CurrentState" = "Waiting"
│   ├── TransitionLink → ProcessNode: "CheckInService"
└── EntityNode: "Customer_2"
    └── StateNode: "CurrentState" = "BeingServed"

ProcessNode: "CheckInService"
├── StateNode: "QueueLength" = 5
├── TransitionLink → ProcessNode: "SecurityCheck"
└── InteractionLink → EntityNode: "Resource_Clerk1"
```

**Integration Points**:
1. **Entry**: When ABM agent decides to enter process, create EntityNode in AtomSpace
2. **State Sync**: Agent statechart state updates StateNode; ProcessNode reads it
3. **Exit**: When process completes, fire event to ABM engine for next behavior

**Implementation**:
```java
// In Agent behavior statechart
State: DecideToCheckIn {
    onEntry: {
        // Register in AtomSpace
        atomSpace.createNode(EntityNode, "Customer_" + this.getIndex());
        
        // Enter DES process
        checkInProcess.takeEntity(this);
    }
}

// In DES Service block
Service: CheckInService {
    onExit: {
        // Update AtomSpace
        atomSpace.updateState("Customer_" + agent.getIndex() + "_State", "Completed");
        
        // Notify ABM engine
        messageBus.send("ABMEngine", "ProcessComplete", agent);
    }
}
```

### Pattern 2: SD + DES Integration (Aggregate Dynamics Affect Process)

**Use Case**: System dynamics model determines arrival rates or resource availability for a process.

**AtomSpace Structure**:
```
StateNode: "MarketDemand" (SD Stock)
├── InfluenceLink → StateNode: "ProductionRate" (SD Flow)
└── InfluenceLink → ParameterNode: "ArrivalRate" (DES Source parameter)

ProcessNode: "CustomerSource" (DES)
├── ReadsParameter: "ArrivalRate"
└── TransitionLink → ProcessNode: "OrderFulfillment"
```

**Integration Points**:
1. **SD to DES**: SD flow calculation updates ParameterNode, DES Source reads it
2. **DES to SD**: DES process completion increments SD stock
3. **Feedback Loop**: Create InfluenceLink to represent feedback

**Implementation**:
```java
// In SD model
StockVariable: MarketDemand {
    onChange: {
        // Update AtomSpace
        atomSpace.updateState("MarketDemand", this.getValue());
        
        // Calculate derived parameter
        double arrivalRate = calculateArrivalRate(this.getValue());
        atomSpace.updateParameter("ArrivalRate", arrivalRate);
    }
}

// In DES Source block
Source: CustomerSource {
    arrivalRate: {
        // Read from AtomSpace (binding)
        return atomSpace.getParameter("ArrivalRate");
    }
}
```

### Pattern 3: ABM + SD Integration (Individual Behavior Affects Aggregate)

**Use Case**: Individual agent decisions accumulate to affect system-level stocks.

**AtomSpace Structure**:
```
EntityNode: "Consumer_1"
├── StateNode: "PurchaseDecision" = "Buy"
└── InteractionLink → StateNode: "TotalPurchases" (SD Stock)

StateNode: "TotalPurchases"
├── InfluenceLink → StateNode: "MarketShare"
└── InfluenceLink → ParameterNode: "AdvertisingBudget"

ParameterNode: "AdvertisingBudget"
└── InfluenceLink → EntityNode: "Consumer_*" (affects all consumers)
```

**Integration Points**:
1. **ABM to SD**: Agent decision events increment/decrement SD stocks
2. **SD to ABM**: SD state changes affect agent behavior parameters
3. **Emergent Feedback**: Collective agent behavior → SD → Individual parameters

**Implementation**:
```java
// In Agent behavior
Transition: DecideToPurchase {
    onFire: {
        // Update AtomSpace
        atomSpace.createLink(InteractionLink, 
            "Consumer_" + this.getIndex(), 
            "TotalPurchases");
        
        // Notify SD engine
        messageBus.send("SDEngine", "IncrementStock", "TotalPurchases", 1);
    }
}

// In SD model
StockVariable: TotalPurchases {
    inflows: {
        // Listen for messages from ABM
        // Message structure: Map with "stock" (String) and "amount" (Integer)
        messageBus.subscribe("IncrementStock", (data) -> {
            String stock = (String) data.get("stock");
            Integer amount = (Integer) data.get("amount");
            if (stock.equals("TotalPurchases")) {
                this.increment(amount);
            }
        });
    }
}
```

### Pattern 4: Multi-Engine Integration (Full Cognitive Synergy)

**Use Case**: All engines work together (e.g., Smart Factory with workers, machines, materials, and policies).

**AtomSpace Structure**:
```
Main Model
├── DES: Production Process
│   ├── ProcessNode: "MachineProcess"
│   └── EntityNode: "WorkPiece_*"
├── ABM: Worker Behavior
│   ├── EntityNode: "Worker_*"
│   └── StateNode: "WorkerState"
├── SD: Overall Throughput Dynamics
│   ├── StateNode: "WIPLevel"
│   └── StateNode: "ThroughputRate"
├── Material Flow: Physical Movement
│   ├── ProcessNode: "ConveyorBelt"
│   └── EntityNode: "AGV_*"
└── Fluid: Chemical Batching
    ├── StateNode: "TankLevel"
    └── ProcessNode: "ChemicalMix"
```

**Integration Manager Coordination**:
```java
class FactoryIntegrationManager extends IntegrationManager {
    
    void step() {
        // 1. Advance SD (fastest, continuous time)
        sdEngine.step(dt);
        double wipTarget = atomSpace.getState("WIPTarget");
        
        // 2. Update DES parameters based on SD
        atomSpace.updateParameter("ProductionTarget", wipTarget);
        
        // 3. Process DES events
        desEngine.processEvents();
        
        // 4. Update ABM agents based on DES state
        int queueLength = atomSpace.getState("MachineQueue");
        messageBus.broadcast("ABMEngine", "QueueAlert", queueLength);
        
        // 5. Execute agent behaviors
        abmEngine.step();
        
        // 6. Move materials based on process state
        materialFlowEngine.step();
        
        // 7. Update fluid levels
        fluidEngine.step(dt);
        
        // 8. Feed back to SD
        double actualThroughput = atomSpace.getMetric("CompletedParts");
        atomSpace.updateState("ThroughputRate", actualThroughput);
        
        // 9. Check coherence
        coherenceChecker.validate();
    }
}
```

## Communication Protocols

### 1. Synchronous Communication (Read/Write AtomSpace)

**When to Use**: For direct state sharing between components.

**Pattern**:
```java
// Writer
atomSpace.updateState("SharedState", newValue);

// Reader (elsewhere)
double value = atomSpace.getState("SharedState");
```

**Guarantees**: Immediate consistency, but tight temporal coupling.

### 2. Asynchronous Communication (Message Bus)

**When to Use**: For event notifications and loose coupling.

**Pattern**:
```java
// Sender
messageBus.send("ReceiverComponent", "EventType", eventData);

// Receiver (registered earlier)
messageBus.subscribe("EventType", (data) -> {
    handleEvent(data);
});
```

**Guarantees**: Loose coupling, but requires message ordering.

### 3. Observer Pattern (State Change Notifications)

**When to Use**: For reactive updates when state changes.

**Pattern**:
```java
// Observer registration
atomSpace.observeNode("StateNode_X", (node, oldValue, newValue) -> {
    reactToChange(oldValue, newValue);
});

// Automatic notification when state changes
atomSpace.updateState("StateNode_X", newValue); // triggers observers
```

**Guarantees**: Reactive, but can create cascading updates.

## Synchronization Strategies

### Strategy 1: Lockstep Synchronization

All engines advance in lockstep (same timestep).

**Pros**: Simple, deterministic, no race conditions
**Cons**: Limited by slowest engine, poor performance

**Implementation**:
```java
void lockstepSimulation() {
    while (currentTime < endTime) {
        // All engines step together
        processFlowEngine.step(dt);
        agentBehaviorEngine.step(dt);
        systemDynamicsEngine.step(dt);
        
        currentTime += dt;
    }
}
```

### Strategy 2: Event-Based Synchronization

Engines advance independently, coordinated by events.

**Pros**: Efficient, engines advance at natural rates
**Cons**: Complex, requires careful event ordering

**Implementation**:
```java
void eventBasedSimulation() {
    PriorityQueue<Event> eventQueue = new PriorityQueue<>();
    
    while (!eventQueue.isEmpty()) {
        Event event = eventQueue.poll();
        
        // Advance time to event
        currentTime = event.time;
        
        // Process event in appropriate engine
        event.engine.processEvent(event);
        
        // Generate new events
        eventQueue.addAll(event.engine.generateEvents());
    }
}
```

### Strategy 3: Hybrid Synchronization

Combine lockstep and event-based for different engine types.

**Pros**: Balance between simplicity and efficiency
**Cons**: Requires careful design

**Implementation**:
```java
void hybridSimulation() {
    while (currentTime < endTime) {
        // Process discrete events up to next timestep
        processDiscreteEventsUntil(currentTime + dt);
        
        // Step continuous engines
        systemDynamicsEngine.step(dt);
        fluidEngine.step(dt);
        
        // Step ABM (variable internal timestep)
        agentBehaviorEngine.stepUntil(currentTime + dt);
        
        currentTime += dt;
    }
}
```

## Common Integration Challenges

### Challenge 1: Time Scale Differences

**Problem**: SD operates on continuous time, DES on event time, ABM on both.

**Solution**:
- Use hybrid synchronization
- Define a common base timestep (e.g., 1 minute)
- Allow SD/Fluid engines to integrate within base timestep
- Process DES events that occur within base timestep
- Update ABM agents at base timestep boundaries

### Challenge 2: State Inconsistency

**Problem**: Multiple engines update the same state simultaneously.

**Solution**:
- Define clear ownership: one engine owns each StateNode
- Other engines read but don't write owned states
- Use InfluenceLinks to request changes (message passing)
- Integration Manager enforces ownership rules

### Challenge 3: Circular Dependencies

**Problem**: Engine A depends on B's output, B depends on A's output.

**Solution**:
- Identify the feedback loop in AtomSpace (circular InfluenceLinks)
- Use lagged values: A(t) depends on B(t-1)
- Or use iterative convergence: alternate updates until stable
- Document the assumption in the AtomSpace schema

### Challenge 4: Performance Overhead

**Problem**: AtomSpace access and message passing add overhead.

**Solution**:
- Cache frequently accessed nodes/values
- Batch multiple updates into single AtomSpace transaction
- Use direct communication for performance-critical paths
- Profile to identify bottlenecks

## Validation and Testing

### 1. Component-Level Tests

Test each engine independently:
- DES: Verify process logic with test entities
- ABM: Test agent behaviors in isolation
- SD: Validate stock-flow calculations

### 2. Integration Tests

Test pairwise integrations:
- DES + ABM: Do agents flow through process correctly?
- SD + DES: Does demand affect arrival rate?
- ABM + SD: Do agent decisions aggregate properly?

### 3. End-to-End Tests

Test full integrated model:
- Run complete simulation scenario
- Verify all engines contribute meaningful output
- Check that cross-paradigm patterns emerge as expected

### 4. Coherence Tests

Use Coherence Checker to validate:
- No orphaned nodes in AtomSpace
- All links reference existing nodes
- Temporal ordering is preserved
- State updates are consistent

## Best Practices

1. **Start Simple**: Begin with two engines, add more as needed
2. **Document Integration Points**: Clearly specify what each engine reads/writes
3. **Use AtomSpace Consistently**: Don't bypass it with direct communication
4. **Monitor Performance**: Profile the integration overhead
5. **Validate Early**: Run coherence checks during development
6. **Design for Modularity**: Each engine should work independently if needed
7. **Leverage Meta-Cognition**: Let Pattern Miner discover integration issues
8. **Enable Autognosis**: Let the system optimize its own integration

## Example: Integrated Supply Chain Model

See the examples directory for a complete implementation demonstrating all integration patterns in a realistic supply chain scenario with:
- SD: Aggregate demand forecasting and inventory policies
- DES: Order fulfillment and shipping processes
- ABM: Customer purchasing decisions and behavior
- Material Flow: Warehouse operations and transportation
- Meta-Cognitive: Bottleneck detection and optimization suggestions
- Autognosis: Continuous model improvement

This example showcases how differentiated components integrate into a cohesive, intelligent simulation system.
