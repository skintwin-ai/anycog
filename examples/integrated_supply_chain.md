# Integrated Supply Chain Example

This example demonstrates the OpenCog-inspired AnyCog architecture with a complete integrated supply chain model that combines multiple cognitive processes.

## Model Overview

A manufacturer receives orders from customers, produces products, and ships them via a warehouse. The model integrates:
- **System Dynamics (SD)**: Aggregate demand forecasting and inventory policies
- **Discrete-Event Simulation (DES)**: Order processing and production workflow
- **Agent-Based Modeling (ABM)**: Individual customer purchase decisions
- **Material Handling (MH)**: Warehouse storage and retrieval operations
- **Meta-Cognitive Layer**: Pattern recognition and bottleneck detection
- **Autognosis Layer**: Self-optimization of the integrated architecture

## AtomSpace Schema

### Node Types

**ConceptNodes**:
- `Customer` (ABM agent type)
- `Order` (DES entity type)
- `Product` (Material entity type)
- `DES`, `ABM`, `SD`, `MH` (Paradigm concepts)

**EntityNodes**:
- `Customer_1`, `Customer_2`, ... (Individual customers)
- `Order_1`, `Order_2`, ... (Individual orders)
- `Product_1`, `Product_2`, ... (Individual products)
- `Warehouse` (Storage facility)
- `ProductionLine` (Manufacturing resource)

**ProcessNodes**:
- `OrderReceiving` (DES Source)
- `OrderValidation` (DES Service)
- `Production` (DES Service)
- `WarehouseStore` (MH RackStore)
- `WarehouseRetrieve` (MH RackPick)
- `Shipping` (DES Service)
- `OrderCompletion` (DES Sink)

**StateNodes**:
- `MarketDemand` (SD Stock - aggregate customer interest)
- `Backlog` (SD Stock - unfulfilled orders)
- `Inventory` (SD Stock - products in warehouse)
- `ProductionRate` (SD Flow)
- `FulfillmentRate` (SD Flow)
- `QueueLength_OrderValidation` (DES metric)
- `QueueLength_Production` (DES metric)
- `WarehouseUtilization` (MH metric)

**ParameterNodes**:
- `DemandForecast` (calculated from SD)
- `OrderArrivalRate` (used by DES)
- `ProductionCapacity` (constraint parameter)
- `WarehouseCapacity` (constraint parameter)
- `CustomerSatisfaction` (feedback parameter)

**MetricNodes**:
- `OrderFulfillmentTime` (DES output)
- `InventoryTurnover` (SD output)
- `WarehouseEfficiency` (MH output)
- `CustomerRetentionRate` (ABM output)

### Link Types

**InheritanceLinks**:
- `Customer` → `Agent`
- `Order` → `Entity`
- `Product` → `Entity`

**TransitionLinks**:
- `OrderReceiving` → `OrderValidation`
- `OrderValidation` → `Production`
- `Production` → `WarehouseStore`
- `WarehouseStore` → `WarehouseRetrieve`
- `WarehouseRetrieve` → `Shipping`
- `Shipping` → `OrderCompletion`

**InfluenceLinks** (Cross-Paradigm Feedback):
- `MarketDemand` → `OrderArrivalRate` (SD influences DES)
- `Backlog` → `ProductionRate` (SD feedback)
- `Inventory` → `FulfillmentRate` (SD feedback)
- `OrderFulfillmentTime` → `CustomerSatisfaction` (DES influences ABM)
- `CustomerSatisfaction` → `MarketDemand` (ABM influences SD)

**InteractionLinks**:
- `Customer_X` → `Order_Y` (ABM creates DES entity)
- `Order_Y` → `ProductionLine` (DES seizes resource)
- `Product_Z` → `Warehouse` (MH storage)

**ContainmentLinks**:
- `Warehouse` contains `Product_1`, `Product_2`, ...

**TemporalLinks**:
- `Order_Y` at time T1 before `Product_Z` at time T2
- Event ordering in DES process

## Component Implementation

### 1. System Dynamics Engine

Models aggregate system behavior:

```java
// In Main agent - SD Model
StockVariable marketDemand {
    initialValue: 1000;
    
    // Inflow from customer interest
    inflow: customerInterestFlow;
    
    // Outflow from fulfillment
    outflow: fulfillmentFlow;
    
    onChange: {
        // Update AtomSpace
        atomSpace.updateState("MarketDemand", this.getValue());
        
        // Calculate derived parameter
        double forecastedDemand = this.getValue() * 1.1; // 10% safety margin
        atomSpace.updateParameter("DemandForecast", forecastedDemand);
        
        // Influence DES arrival rate
        double arrivalRate = this.getValue() / 480.0; // per minute in 8-hour day
        atomSpace.updateParameter("OrderArrivalRate", arrivalRate);
    }
}

Flow customerInterestFlow {
    rate: {
        // Influenced by customer satisfaction
        double satisfaction = atomSpace.getParameter("CustomerSatisfaction");
        return 50 * satisfaction; // Base rate 50 customers/day * satisfaction multiplier
    }
}

StockVariable backlog {
    initialValue: 0;
    
    inflow: orderArrivalFlow;
    outflow: productionCompletionFlow;
    
    onChange: {
        atomSpace.updateState("Backlog", this.getValue());
        
        // Pressure on production
        if (this.getValue() > 100) {
            messageBus.send("DESEngine", "ProductionUrgent", this.getValue());
        }
    }
}

StockVariable inventory {
    initialValue: 200;
    
    inflow: productionCompletionFlow;
    outflow: fulfillmentFlow;
    
    onChange: {
        atomSpace.updateState("Inventory", this.getValue());
        
        // Reorder point logic
        double forecastedDemand = atomSpace.getParameter("DemandForecast");
        if (this.getValue() < forecastedDemand * 0.2) {
            messageBus.send("DESEngine", "ProductionNeeded", forecastedDemand);
        }
    }
}
```

### 2. Agent-Based Engine

Models individual customer behavior:

```java
// Customer agent type
agent Customer {
    // States
    statechart {
        state Inactive {
            onEntry: {
                // Wait for interest trigger
            }
        }
        
        state Considering {
            onEntry: {
                // Check satisfaction level
                double satisfaction = atomSpace.getParameter("CustomerSatisfaction");
                
                // Decision: purchase probability based on satisfaction
                if (uniform() < satisfaction) {
                    // Transition to purchasing
                } else {
                    // Transition back to inactive
                }
            }
        }
        
        state Purchasing {
            onEntry: {
                // Create order in DES
                Order order = new Order();
                order.customer = this;
                
                // Register in AtomSpace
                String orderId = "Order_" + order.getId();
                atomSpace.createNode(EntityNode, orderId);
                atomSpace.createLink(InteractionLink, 
                    "Customer_" + this.getIndex(), orderId);
                
                // Inject into DES process
                orderReceivingSource.inject(order);
                
                // Update SD
                messageBus.send("SDEngine", "IncrementStock", "Backlog", 1);
            }
        }
        
        state AwaitingDelivery {
            onEntry: {
                // Wait for order completion notification
                messageBus.subscribe("OrderComplete_" + this.getId(), (data) -> {
                    // Update satisfaction based on delivery time
                    double deliveryTime = (double) data.get("deliveryTime");
                    updateSatisfaction(deliveryTime);
                });
            }
        }
    }
    
    void updateSatisfaction(double deliveryTime) {
        // Faster delivery = higher satisfaction
        double satisfaction = Math.max(0.5, Math.min(1.0, 1.0 - (deliveryTime / 1440.0)));
        
        // Aggregate satisfaction across all customers
        double avgSatisfaction = population.stream()
            .mapToDouble(c -> c.satisfaction)
            .average()
            .orElse(0.8);
        
        // Update AtomSpace
        atomSpace.updateParameter("CustomerSatisfaction", avgSatisfaction);
    }
}

// Customer population
population<Customer> customers {
    size: 1000;
}
```

### 3. Discrete-Event Engine

Models the order fulfillment process:

```java
// In Main agent - DES Process
Source orderReceivingSource {
    arrivalRate: {
        // Read from AtomSpace (bound to SD)
        return atomSpace.getParameter("OrderArrivalRate");
    }
    
    onExit: {
        Order order = (Order) agent;
        
        // Register in AtomSpace
        String orderId = "Order_" + order.getId();
        atomSpace.updateState(orderId + "_Status", "Received");
        
        // Create temporal link
        atomSpace.createLink(TemporalLink, orderId, "OrderValidation", time());
    }
}

Service orderValidation {
    queueCapacity: 50;
    resourcePool: validationClerks;
    serviceTime: triangular(2, 5, 10); // minutes
    
    onEnter: {
        Order order = (Order) agent;
        atomSpace.updateState("QueueLength_OrderValidation", 
            this.queue.size());
    }
    
    onExit: {
        Order order = (Order) agent;
        atomSpace.updateState("Order_" + order.getId() + "_Status", "Validated");
    }
}

Service production {
    queueCapacity: {
        return (int) atomSpace.getParameter("ProductionCapacity");
    }
    resourcePool: productionLine;
    serviceTime: triangular(30, 60, 120); // minutes
    
    onEnter: {
        atomSpace.updateState("QueueLength_Production", this.queue.size());
    }
    
    onExit: {
        Order order = (Order) agent;
        
        // Create product
        Product product = new Product(order);
        String productId = "Product_" + product.getId();
        atomSpace.createNode(EntityNode, productId);
        
        // Update SD inventory
        messageBus.send("SDEngine", "IncrementStock", "Inventory", 1);
        messageBus.send("SDEngine", "DecrementStock", "Backlog", 1);
        
        // Pass to material handling
        order.product = product;
    }
}

// Continued in next section...
```

### 4. Material Handling Engine

Models warehouse operations:

```java
// Warehouse rack system
RackSystem warehouse {
    capacity: {
        return (int) atomSpace.getParameter("WarehouseCapacity");
    }
}

RackStore warehouseStore {
    rackSystem: warehouse;
    
    onStore: {
        Product product = (Product) agent;
        
        // Register in AtomSpace
        atomSpace.createLink(ContainmentLink, 
            "Product_" + product.getId(), "Warehouse");
        
        // Update metrics
        double utilization = (double) warehouse.size() / warehouse.capacity;
        atomSpace.updateMetric("WarehouseUtilization", utilization);
    }
}

Delay waitForShipping {
    delayTime: {
        // Wait for shipping request
        // Could be triggered by customer order priority
        return 0; // Immediate for this simple model
    }
}

RackPick warehouseRetrieve {
    rackSystem: warehouse;
    
    onPick: {
        Product product = (Product) agent;
        
        // Remove from AtomSpace
        atomSpace.removeLink(ContainmentLink, 
            "Product_" + product.getId(), "Warehouse");
        
        // Update metrics
        double utilization = (double) warehouse.size() / warehouse.capacity;
        atomSpace.updateMetric("WarehouseUtilization", utilization);
    }
}

Service shipping {
    resourcePool: shippingTrucks;
    serviceTime: exponential(60); // 1 hour average
    
    onExit: {
        Order order = (Order) agent;
        
        // Calculate fulfillment time
        double fulfillmentTime = time() - order.arrivalTime;
        atomSpace.updateMetric("OrderFulfillmentTime", fulfillmentTime);
        
        // Notify customer (ABM)
        messageBus.send("ABMEngine", 
            "OrderComplete_" + order.customer.getId(), 
            Map.of("deliveryTime", fulfillmentTime));
        
        // Update SD
        messageBus.send("SDEngine", "IncrementFlow", "FulfillmentFlow", 1);
    }
}

Sink orderCompletion {
    // Order complete
}
```

### 5. Integration Manager

Coordinates all cognitive processes:

```java
class SupplyChainIntegrationManager extends IntegrationManager {
    
    void initialize() {
        // Register all engines
        registry.register("SDEngine", systemDynamicsEngine);
        registry.register("DESEngine", processFlowEngine);
        registry.register("ABMEngine", agentBehaviorEngine);
        registry.register("MHEngine", materialFlowEngine);
        
        // Setup message routing
        messageBus.initialize();
        
        // Initialize AtomSpace with schema
        atomSpace.loadSchema("supply_chain_schema.json");
        
        // Enable meta-cognitive processes
        enablePatternMining();
        enableCoherenceChecking();
        enableOptimizationDetection();
    }
    
    void step() {
        // 1. Advance SD (continuous time, integrated over timestep)
        sdEngine.step(dt);
        
        // 2. Process ABM decisions (may create DES entities)
        abmEngine.step();
        
        // 3. Process DES events (may create MH tasks)
        desEngine.processEvents();
        
        // 4. Execute MH operations
        mhEngine.step();
        
        // 5. Update metrics
        updateMetrics();
        
        // 6. Check coherence
        if (time() % 60 == 0) { // Every hour
            coherenceChecker.validate();
        }
    }
    
    void updateMetrics() {
        // Calculate cross-paradigm metrics
        double avgFulfillmentTime = atomSpace.getMetric("OrderFulfillmentTime");
        double inventoryLevel = atomSpace.getState("Inventory");
        double warehouseUtil = atomSpace.getMetric("WarehouseUtilization");
        double customerSat = atomSpace.getParameter("CustomerSatisfaction");
        
        // Overall performance index
        double performanceIndex = 
            (customerSat * 0.4) + 
            ((1.0 / (avgFulfillmentTime / 1440.0)) * 0.3) +
            (warehouseUtil * 0.2) +
            ((inventoryLevel / 200.0) * 0.1);
        
        atomSpace.updateMetric("OverallPerformance", performanceIndex);
    }
}
```

### 6. Meta-Cognitive Layer

Discovers patterns and generates insights:

```java
class SupplyChainMetaCognition {
    
    void enablePatternMining() {
        patternMiner.addRule("BottleneckDetection", () -> {
            // Detect process bottlenecks
            double prodQueue = atomSpace.getState("QueueLength_Production");
            double valQueue = atomSpace.getState("QueueLength_OrderValidation");
            
            if (prodQueue > 20) {
                return new Pattern("ProductionBottleneck", 
                    "Production queue consistently above 20 orders",
                    Map.of("queueLength", prodQueue));
            }
            return null;
        });
        
        patternMiner.addRule("InventoryOscillation", () -> {
            // Detect boom-bust inventory cycles
            List<Double> inventoryHistory = 
                atomSpace.getStateHistory("Inventory", 100);
            
            if (detectOscillation(inventoryHistory)) {
                return new Pattern("BullwhipEffect",
                    "Inventory oscillating due to demand amplification",
                    Map.of("amplitude", calculateAmplitude(inventoryHistory)));
            }
            return null;
        });
        
        patternMiner.addRule("SynergyOpportunity", () -> {
            // Detect missing cross-paradigm interactions
            double satisfaction = atomSpace.getParameter("CustomerSatisfaction");
            double inventoryLevel = atomSpace.getState("Inventory");
            
            if (satisfaction < 0.7 && inventoryLevel > 150) {
                return new Pattern("MissedSynergyOpportunity",
                    "Low satisfaction despite high inventory - shipping bottleneck?",
                    Map.of("satisfaction", satisfaction, 
                           "inventory", inventoryLevel));
            }
            return null;
        });
    }
}
```

### 7. Autognosis Layer

Optimizes the integrated architecture:

```java
class SupplyChainAutognosis {
    
    void runOptimizationCycle() {
        // Build self-image
        HierarchicalModel selfImage = buildSelfImage();
        
        // Analyze
        List<Insight> insights = analyze(selfImage);
        
        // Generate optimizations
        for (Insight insight : insights) {
            if (insight.getType() == InsightType.WEAK_INTEGRATION) {
                proposeIntegrationStrengthening(insight);
            } else if (insight.getType() == InsightType.PERFORMANCE_OVERHEAD) {
                proposePerformanceOptimization(insight);
            } else if (insight.getType() == InsightType.UNNECESSARY_COMPLEXITY) {
                proposeSimplification(insight);
            }
        }
    }
    
    Optimization proposeIntegrationStrengthening(Insight insight) {
        // Example: Strengthen ABM-SD connection
        return new Optimization(
            "Add direct influence from individual customer satisfaction to market demand",
            () -> {
                // Create InfluenceLink in AtomSpace
                atomSpace.createLink(InfluenceLink,
                    "CustomerSatisfaction_Aggregate",
                    "MarketDemand");
                
                // Modify SD flow to include this influence
                // ... implementation details ...
            },
            Priority.MEDIUM
        );
    }
}
```

## Running the Model

### Experiment Configuration

```java
SimulationExperiment experiment {
    duration: 7200; // 2 weeks (in minutes)
    dt: 1; // 1 minute timestep
    
    onInitialize: {
        // Initialize Integration Manager
        integrationManager.initialize();
        
        // Start with initial conditions
        atomSpace.updateState("MarketDemand", 1000);
        atomSpace.updateState("Inventory", 200);
        atomSpace.updateParameter("CustomerSatisfaction", 0.8);
    }
    
    onStep: {
        // Integration Manager coordinates all engines
        integrationManager.step();
        
        // Collect data for analysis
        collectMetrics();
    }
    
    onFinish: {
        // Generate report
        generateIntegratedReport();
        
        // Run Autognosis analysis
        autognosis.runOptimizationCycle();
        
        // Display recommendations
        displayOptimizations();
    }
}
```

## Expected Outcomes

### Cross-Paradigm Insights

1. **From DES + ABM**: Individual customer satisfaction directly correlates with queue lengths in validation and production
2. **From SD + DES**: Bullwhip effect visible as demand variability amplifies through the supply chain
3. **From ABM + SD**: Customer retention rate (ABM) significantly affects long-term market demand (SD)
4. **From All Engines**: Overall system performance is a complex emergent property requiring all paradigms to understand

### Meta-Cognitive Discoveries

1. **Pattern**: Production bottlenecks occur predictably on day 3 of each week
2. **Insight**: Warehouse utilization below 60% indicates shipping is the bottleneck, not storage
3. **Opportunity**: Customer satisfaction would increase more from faster shipping than increased inventory

### Autognosis Optimizations

1. **Refactoring**: Simplified message passing between ABM and SD by caching aggregate metrics
2. **Performance**: Reduced AtomSpace queries by 35% through strategic caching
3. **Enhancement**: Added feedback loop from fulfillment time to production priority (cognitive synergy)

## Model Files

This example would be implemented across multiple files:
- `Main.java` - Main agent with all engines and Integration Manager
- `Customer.java` - ABM Customer agent
- `Order.java` - DES entity
- `Product.java` - MH entity  
- `SupplyChainIntegrationManager.java` - Custom integration logic
- `SupplyChainMetaCognition.java` - Pattern mining rules
- `SupplyChainAutognosis.java` - Self-optimization logic
- `supply_chain_schema.json` - AtomSpace schema definition

## Conclusion

This integrated supply chain model demonstrates how the OpenCog-inspired AnyCog architecture enables:
- **Seamless multi-paradigm integration** through the AtomSpace
- **Cognitive synergy** that produces insights impossible in single-paradigm models
- **Meta-cognitive pattern recognition** that discovers non-obvious system behaviors
- **Self-optimization** through Autognosis that continuously improves the architecture

The result is not just a simulation model, but a cognitive system that thinks about supply chains.
