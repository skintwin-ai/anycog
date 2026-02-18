# AnyCog Quick Reference Guide

This guide provides a quick reference for common tasks when building integrated AnyCog models.

## AtomSpace Operations

### Creating Nodes

```java
// Create a ConceptNode for a type
atomSpace.createNode(ConceptNode, "Customer");

// Create an EntityNode for an instance
atomSpace.createNode(EntityNode, "Customer_123");

// Create a ProcessNode for an operation
atomSpace.createNode(ProcessNode, "OrderProcessing");

// Create a StateNode for system state
atomSpace.createNode(StateNode, "QueueLength");

// Create a ParameterNode for configuration
atomSpace.createNode(ParameterNode, "ArrivalRate");

// Create a MetricNode for measurements
atomSpace.createNode(MetricNode, "Throughput");
```

### Creating Links

```java
// Create an InheritanceLink (type hierarchy)
atomSpace.createLink(InheritanceLink, "Customer", "Agent");

// Create a TransitionLink (state change)
atomSpace.createLink(TransitionLink, "Waiting", "BeingServed");

// Create an InfluenceLink (causality)
atomSpace.createLink(InfluenceLink, "Demand", "ProductionRate");

// Create an InteractionLink (communication)
atomSpace.createLink(InteractionLink, "Agent_1", "Resource_2");

// Create a ContainmentLink (hierarchy)
atomSpace.createLink(ContainmentLink, "Item_5", "Warehouse");

// Create a TemporalLink (time ordering)
atomSpace.createLink(TemporalLink, "Event_A", "Event_B", time());
```

### Reading and Writing State

```java
// Update a state value
atomSpace.updateState("QueueLength", 15);

// Read a state value
double queueLength = atomSpace.getState("QueueLength");

// Update a parameter
atomSpace.updateParameter("ArrivalRate", 2.5);

// Read a parameter
double arrivalRate = atomSpace.getParameter("ArrivalRate");

// Update a metric
atomSpace.updateMetric("Throughput", 450);

// Read a metric
double throughput = atomSpace.getMetric("Throughput");

// Get state history
List<Double> history = atomSpace.getStateHistory("QueueLength", 100);
```

### Observing State Changes

```java
// Register an observer for a specific node
atomSpace.observeNode("QueueLength", (node, oldValue, newValue) -> {
    if (newValue > 20) {
        // React to queue building up
        handleQueueAlert(newValue);
    }
});

// Remove an observer
atomSpace.removeObserver("QueueLength", observerHandle);
```

## Message Bus Communication

### Sending Messages

```java
// Send a simple message
messageBus.send("TargetEngine", "EventType", eventData);

// Send with structured data
messageBus.send("SDEngine", "IncrementStock", 
    Map.of("stock", "Inventory", "amount", 5));

// Broadcast to all engines
messageBus.broadcast("AllEngines", "SimulationStep", time());
```

### Receiving Messages

```java
// Subscribe to a message type
messageBus.subscribe("EventType", (data) -> {
    // Handle the event
    processEvent(data);
});

// Subscribe with filter
messageBus.subscribe("OrderComplete", 
    (data) -> data.get("priority") > 5,
    (data) -> {
        // Handle high-priority orders only
        processHighPriorityOrder(data);
    }
);

// Unsubscribe
messageBus.unsubscribe("EventType", subscriptionHandle);
```

## Integration Patterns

### Pattern 1: DES Reading SD State

```java
// In SD model
StockVariable demand {
    onChange: {
        atomSpace.updateState("MarketDemand", this.getValue());
        
        // Derive parameter for DES
        double arrivalRate = this.getValue() / 480.0;
        atomSpace.updateParameter("ArrivalRate", arrivalRate);
    }
}

// In DES Source block
Source orderSource {
    arrivalRate: {
        // Bind to AtomSpace parameter
        return atomSpace.getParameter("ArrivalRate");
    }
}
```

### Pattern 2: DES Updating SD Stock

```java
// In DES block
Service production {
    onExit: {
        // Notify SD engine to increment stock
        messageBus.send("SDEngine", "IncrementStock", 
            Map.of("stock", "Inventory", "amount", 1));
    }
}

// In SD model
StockVariable inventory {
    // Listen for messages
    void initialize() {
        messageBus.subscribe("IncrementStock", (data) -> {
            if (data.get("stock").equals("Inventory")) {
                this.increment((Integer) data.get("amount"));
            }
        });
    }
}
```

### Pattern 3: ABM Creating DES Entity

```java
// In ABM agent behavior
State: DecideToPurchase {
    onEntry: {
        // Create order entity
        Order order = new Order();
        order.customer = this;
        
        // Register in AtomSpace
        String orderId = "Order_" + order.getId();
        atomSpace.createNode(EntityNode, orderId);
        atomSpace.createLink(InteractionLink, 
            "Customer_" + this.getIndex(), orderId);
        
        // Inject into DES process
        orderSource.inject(order);
    }
}
```

### Pattern 4: DES Notifying ABM

```java
// In DES block
Service fulfillment {
    onExit: {
        Order order = (Order) agent;
        
        // Calculate metrics
        double fulfillmentTime = time() - order.arrivalTime;
        
        // Notify the customer agent
        messageBus.send("ABMEngine", 
            "OrderComplete_" + order.customer.getId(),
            Map.of("orderId", order.getId(),
                   "fulfillmentTime", fulfillmentTime));
    }
}

// In ABM agent
void initialize() {
    messageBus.subscribe("OrderComplete_" + this.getId(), (data) -> {
        double fulfillmentTime = (double) data.get("fulfillmentTime");
        updateSatisfaction(fulfillmentTime);
    });
}
```

### Pattern 5: ABM Aggregating to SD

```java
// In ABM agent population
void updateAggregateMetrics() {
    // Calculate average across all agents
    double avgSatisfaction = population.stream()
        .mapToDouble(agent -> agent.satisfaction)
        .average()
        .orElse(0.8);
    
    // Update AtomSpace
    atomSpace.updateParameter("CustomerSatisfaction", avgSatisfaction);
    
    // SD can read this parameter
    messageBus.send("SDEngine", "UpdateParameter",
        Map.of("param", "CustomerInterest", 
               "value", avgSatisfaction * 50));
}
```

## Component Registration

### Registering a Cognitive Process

```java
class CustomEngine implements CognitiveProcess {
    void initialize(IntegrationManager manager) {
        // Register this engine
        manager.registry.register("CustomEngine", this);
        
        // Register components in AtomSpace
        registerComponents();
        
        // Subscribe to relevant messages
        setupMessageHandlers();
    }
    
    void registerComponents() {
        // Register nodes
        atomSpace.createNode(ConceptNode, "CustomType");
        
        // Register with metadata
        atomSpace.setNodeProperty("CustomType", "paradigm", "Custom");
        atomSpace.setNodeProperty("CustomType", "version", "1.0");
    }
    
    void setupMessageHandlers() {
        messageBus.subscribe("TriggerCustom", (data) -> {
            this.processTrigger(data);
        });
    }
}
```

## Meta-Cognitive Operations

### Adding a Pattern Mining Rule

```java
patternMiner.addRule("BottleneckDetection", () -> {
    double queueLength = atomSpace.getState("QueueLength");
    double utilization = atomSpace.getMetric("ResourceUtilization");
    
    if (queueLength > 20 && utilization > 0.95) {
        return new Pattern(
            "ResourceBottleneck",
            "Queue building due to high resource utilization",
            Map.of("queueLength", queueLength,
                   "utilization", utilization)
        );
    }
    return null;
});
```

### Adding a Coherence Check

```java
coherenceChecker.addRule("StateConsistency", () -> {
    double inventory = atomSpace.getState("Inventory");
    int warehouseSize = atomSpace.getContainmentCount("Warehouse");
    
    if (Math.abs(inventory - warehouseSize) > 1) {
        return new CoherenceViolation(
            "InventoryMismatch",
            "SD inventory doesn't match MH warehouse contents",
            Severity.HIGH
        );
    }
    return null;
});
```

### Generating Custom Insights

```java
insightGenerator.addRule("PerformanceInsight", () -> {
    double throughput = atomSpace.getMetric("Throughput");
    double targetThroughput = atomSpace.getParameter("TargetThroughput");
    
    if (throughput < targetThroughput * 0.8) {
        return new Insight(
            "Underperformance",
            "Throughput is 20% below target",
            Severity.MEDIUM,
            List.of(
                "Check for bottlenecks in production",
                "Consider increasing resource capacity",
                "Review SD demand forecast accuracy"
            )
        );
    }
    return null;
});
```

## Autognosis Operations

### Building a Self-Image

```java
HierarchicalModel selfImage = autognosis.buildSelfImage();

// Level 0: Raw structure
RawModel raw = selfImage.getLevel0();
System.out.println("Nodes: " + raw.getNodeCount());
System.out.println("Links: " + raw.getLinkCount());

// Level 1: Patterns
PatternModel patterns = selfImage.getLevel1();
System.out.println("Active engines: " + patterns.getActiveEngines());
System.out.println("Integration patterns: " + patterns.getIntegrationPatterns());

// Level 2: Purpose
PurposeModel purpose = selfImage.getLevel2();
System.out.println("Model purpose: " + purpose.getPurpose());
System.out.println("Synergy effectiveness: " + purpose.getSynergyScore());

// Level 3: Strategy
StrategyModel strategy = selfImage.getLevel3();
System.out.println("Design rationale: " + strategy.getRationale());
System.out.println("Alternative approaches: " + strategy.getAlternatives());
```

### Analyzing and Optimizing

```java
// Generate insights
List<Insight> insights = autognosis.analyze(selfImage);

for (Insight insight : insights) {
    System.out.println(insight.getType() + ": " + insight.getDescription());
}

// Generate optimizations
List<Optimization> optimizations = autognosis.proposeOptimizations(insights);

// Review and apply
for (Optimization opt : optimizations) {
    System.out.println("Optimization: " + opt.getDescription());
    System.out.println("Priority: " + opt.getPriority());
    System.out.println("Risk: " + opt.getRisk());
    
    if (opt.getRisk() == Risk.LOW && opt.getPriority() == Priority.HIGH) {
        // Auto-apply low-risk, high-priority optimizations
        opt.apply();
    } else {
        // Ask user for approval
        if (userApproves(opt)) {
            opt.apply();
        }
    }
}
```

## Synchronization Strategies

### Lockstep Synchronization

```java
void simulationStep() {
    // All engines advance together
    sdEngine.step(dt);
    desEngine.step(dt);
    abmEngine.step(dt);
    mhEngine.step(dt);
    
    // Advance time
    currentTime += dt;
}
```

### Event-Based Synchronization

```java
void simulationStep() {
    // Process events until next synchronization point
    double nextSync = currentTime + dt;
    
    while (!eventQueue.isEmpty() && eventQueue.peek().time < nextSync) {
        Event event = eventQueue.poll();
        
        // Advance to event time
        currentTime = event.time;
        
        // Process event in appropriate engine
        event.engine.processEvent(event);
    }
    
    // Advance continuous engines to sync point
    currentTime = nextSync;
    sdEngine.step(dt);
}
```

### Hybrid Synchronization

```java
void simulationStep() {
    // Process discrete events in current timestep
    processDiscreteEventsUntil(currentTime + dt);
    
    // Step continuous engines
    sdEngine.step(dt);
    fluidEngine.step(dt);
    
    // Step agent engine (may generate new discrete events)
    abmEngine.step(dt);
    
    // Advance time
    currentTime += dt;
}
```

## Common Pitfalls and Solutions

### Pitfall 1: Circular Dependencies

```java
// WRONG: A depends on B, B depends on A (same timestep)
// SD Flow A reads State B
// SD Flow B reads State A

// SOLUTION: Use lagged values
Flow A {
    rate: atomSpace.getState("B", time() - dt); // Previous value
}

Flow B {
    rate: atomSpace.getState("A", time() - dt); // Previous value
}
```

### Pitfall 2: State Inconsistency

```java
// WRONG: Multiple engines writing the same state
desEngine.updateState("Inventory", 100);
mhEngine.updateState("Inventory", 105); // Conflict!

// SOLUTION: Define clear ownership
// SD owns "Inventory" state
// Others send messages to request changes
messageBus.send("SDEngine", "IncrementStock", 
    Map.of("stock", "Inventory", "amount", 5));
```

### Pitfall 3: Message Order Issues

```java
// WRONG: Assuming messages arrive in order
messageBus.send("TargetEngine", "EventA", dataA);
messageBus.send("TargetEngine", "EventB", dataB);
// EventB might arrive before EventA!

// SOLUTION: Use temporal links or sequence numbers
atomSpace.createLink(TemporalLink, "EventA", "EventB", time());

// Or include sequence numbers
messageBus.send("TargetEngine", "Event", 
    Map.of("sequence", sequenceNumber++, "data", data));
```

### Pitfall 4: Performance Overhead

```java
// WRONG: Excessive AtomSpace queries in tight loops
for (int i = 0; i < 1000; i++) {
    double param = atomSpace.getParameter("Rate"); // Query every iteration
    // ... use param
}

// SOLUTION: Cache frequently accessed values
double rate = atomSpace.getParameter("Rate");
for (int i = 0; i < 1000; i++) {
    // ... use cached rate
}
```

## Debugging Tips

### Trace AtomSpace Operations

```java
atomSpace.enableTracing(true);
atomSpace.setTraceLevel(TraceLevel.DETAILED);

// All operations will be logged
atomSpace.updateState("QueueLength", 15);
// Log: [AtomSpace] updateState(StateNode:QueueLength, 15)
```

### Monitor Message Bus Traffic

```java
messageBus.enableLogging(true);

// All messages will be logged
messageBus.send("SDEngine", "IncrementStock", data);
// Log: [MessageBus] SEND: SDEngine <- IncrementStock {stock=Inventory, amount=5}
```

### Visualize AtomSpace Graph

```java
// Export AtomSpace to GraphML for visualization
atomSpace.exportGraph("atomspace.graphml");

// Open in graph visualization tool (e.g., Gephi, Cytoscape)
```

### Check Coherence on Demand

```java
// Run coherence check manually
CoherenceReport report = coherenceChecker.validate();

if (report.hasViolations()) {
    for (CoherenceViolation violation : report.getViolations()) {
        System.err.println(violation.getType() + ": " + 
                          violation.getDescription());
    }
}
```

## Performance Optimization Checklist

- [ ] Cache frequently accessed AtomSpace values
- [ ] Batch multiple AtomSpace updates into transactions
- [ ] Use appropriate synchronization strategy for your model
- [ ] Profile to identify bottlenecks
- [ ] Consider message batching for high-volume communication
- [ ] Prune unused nodes/links from AtomSpace periodically
- [ ] Use lazy evaluation where possible
- [ ] Monitor memory usage of AtomSpace
- [ ] Disable detailed tracing in production runs
- [ ] Let Autognosis suggest performance optimizations

## Next Steps

- Read the [Full Integration Guide](integration_guide.md) for detailed patterns
- Study the [Supply Chain Example](../examples/integrated_supply_chain.md)
- Review [Autognosis Documentation](autognosis_integration.md)
- Explore [OpenCog Architecture](opencog_architecture.md)

---

*This quick reference is part of the AnyCog framework documentation.*
