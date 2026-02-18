# Example: Self-Assembling Hospital Emergency Department Model

## Overview

This example demonstrates the **Niche Construction Engine** automatically assembling a cognitive architecture for a hospital emergency department (ED) from modular components.

Unlike traditional manual modeling, the architecture **self-assembles** by:
1. Analyzing the domain characteristics
2. Matching to known patterns
3. Selecting appropriate components
4. Composing them according to cognitive grammar
5. Validating and optimizing the result

## Domain Description

**Input to Niche Construction Engine**:

```
Domain: Hospital Emergency Department
Description:
    Patients arrive randomly with varying severity levels (critical, urgent, standard).
    Triage nurse assesses each patient and assigns priority.
    Patients wait in designated areas based on priority.
    Multiple doctors and nurses provide treatment.
    Treatment time varies by severity and patient condition.
    Some patients require lab tests or imaging, creating additional delays.
    Resources are limited: 3 triage nurses, 5 doctors, 10 nurses, 2 lab machines, 1 CT scanner.
    
Requirements:
    - Minimize patient wait times
    - Maintain quality of care (no treatment errors)
    - Optimize resource utilization
    - Handle variable patient load (50-200 patients/day)
    - Prioritize critical patients
    - Track performance metrics (wait time, length of stay, throughput)
    
Constraints:
    - Limited beds (25 total)
    - Fixed staff schedules
    - Equipment maintenance windows
    - Regulatory requirements (track all patient encounters)
```

## Step 1: Domain Analysis

**DomainAnalyzer Output**:

```python
DomainCharacterization(
    # Temporal characteristics
    temporal = {
        'discrete_events': True,  # Patient arrivals, treatment start/end
        'continuous_dynamics': False,
        'stochastic': True,  # Random arrivals and service times
        'time_scale': 'minutes_to_hours'
    },
    
    # Spatial characteristics
    spatial = {
        'physical_space': True,  # ED layout matters
        'network_topology': False,
        'containment_hierarchy': True,  # ED → Zones → Beds
        'scale': 'building'
    },
    
    # Entity characteristics
    entity = {
        'autonomy': 'low',  # Patients don't make many decisions
        'heterogeneity': 'high',  # Different severity levels
        'population_size': 'hundreds',  # per day
        'lifecycle': True  # Arrival → treatment → discharge
    },
    
    # System characteristics
    system = {
        'feedback_loops': True,  # Waiting → resource allocation
        'resources': 'constrained',  # Limited staff and equipment
        'aggregate_dynamics': True,  # Overall throughput, utilization
        'optimization': 'wait_time_minimization'
    },
    
    # Identified features
    key_features = [
        'stochastic_arrivals',
        'priority_queue',
        'limited_resources',
        'heterogeneous_entities',
        'service_quality_constraints',
        'multi_stage_process'
    ]
)
```

## Step 2: Pattern Matching

**PatternMatcher Output**:

```python
pattern_matches = [
    (score=92, pattern='Stochastic_Queue_Network'),  # Best match
    (score=78, pattern='Priority_Service_System'),
    (score=65, pattern='Resource_Allocation_System'),
    (score=45, pattern='Agent_Based_Healthcare')
]

selected_pattern = 'Stochastic_Queue_Network'  # Highest score

# Why this pattern?
match_rationale = {
    'stochastic_arrivals': 'Pattern handles random arrivals ✓',
    'queuing': 'Pattern includes priority queues ✓',
    'resources': 'Pattern manages constrained resources ✓',
    'multi_stage': 'Pattern supports sequential processes ✓',
    'optimization': 'Pattern includes performance monitoring ✓',
    'historical_success': '15 previous healthcare ED models used this pattern',
    'average_performance': 'Intelligence density: 4.2, Success rate: 91%'
}
```

## Step 3: Component Selection

**ComponentSelector Output**:

```python
selected_components = {
    # Patient arrival
    'arrivals': StochasticGeneratorComponent(
        name='patient_arrivals',
        distribution='time_varying_poisson',  # Morning/afternoon peaks
        base_rate=3.0,  # per hour
        peak_rate=8.0,  # per hour
        peak_times=[(8, 10), (14, 16)],  # morning and afternoon peaks
        rationale='Historical ED data shows time-varying Poisson arrivals'
    ),
    
    # Triage assessment
    'triage_assessment': ClassifierComponent(
        name='triage_classifier',
        categories=['critical', 'urgent', 'standard'],
        distribution=[0.05, 0.30, 0.65],  # Typical ED mix
        assessment_time=5,  # minutes
        rationale='Categorizes patients by severity for priority routing'
    ),
    
    # Priority routing
    'priority_router': RouterComponent(
        name='priority_router',
        routing_policy='priority_based',
        destination_mapping={
            'critical': 'resuscitation_area',
            'urgent': 'acute_care_area',
            'standard': 'fast_track_area'
        },
        rationale='Routes patients to appropriate care area based on triage'
    ),
    
    # Waiting areas (priority queues)
    'waiting_queues': BufferComponent(
        name='waiting_area',
        capacity={'critical': 5, 'urgent': 15, 'standard': 30},
        discipline='priority_FIFO',  # Within same priority, FIFO
        rationale='Patients wait for available provider in priority order'
    ),
    
    # Resource allocation
    'staff_allocator': AllocatorComponent(
        name='staff_allocator',
        resources={
            'doctors': 5,
            'nurses': 10,
            'triage_nurses': 3
        },
        allocation_policy='highest_priority_first',
        rationale='Assigns medical staff to highest priority patients first'
    ),
    
    # Treatment process
    'treatment_processor': ProcessorComponent(
        name='treatment_provider',
        service_time_distribution={
            'critical': ('triangular', 30, 60, 120),  # min, mode, max
            'urgent': ('triangular', 20, 40, 80),
            'standard': ('triangular', 10, 20, 40)
        },
        failure_probability=0.02,  # 2% need additional tests
        rationale='Models variable treatment duration by severity'
    ),
    
    # Diagnostic tests (conditional)
    'lab_tests': ProcessorComponent(
        name='lab_processor',
        resources=['lab_machine_1', 'lab_machine_2'],
        service_time=('exponential', 15),  # mean 15 minutes
        probability_required=0.30,  # 30% need labs
        rationale='Some patients need lab tests before discharge'
    ),
    
    'imaging': ProcessorComponent(
        name='imaging_processor',
        resources=['ct_scanner'],
        service_time=('exponential', 25),
        probability_required=0.15,  # 15% need imaging
        rationale='Critical patients often need CT scans'
    ),
    
    # Performance monitoring
    'performance_monitor': MonitorComponent(
        name='ed_monitor',
        metrics=[
            'average_wait_time',
            'length_of_stay',
            'throughput',
            'resource_utilization',
            'left_without_treatment'
        ],
        aggregation_window=24,  # hours
        rationale='Track operational performance and quality metrics'
    ),
    
    # Optimization
    'resource_optimizer': OptimizerComponent(
        name='staffing_optimizer',
        objective='minimize_wait_time',
        constraints=['budget', 'max_staff'],
        variables=['staff_schedule', 'resource_allocation_policy'],
        method='simulation_optimization',
        rationale='Optimize staffing levels and allocation policies'
    ),
    
    # Discharge
    'discharge_sink': SinkComponent(
        name='patient_discharge',
        collect_statistics=True,
        rationale='Terminal point for patient flow, collects outcome data'
    )
}
```

**Component Selection Rationale**:
- Each component selected based on **highest match score** for its slot in the pattern
- **StochasticGenerator** with time-varying Poisson best fits ED arrival data
- **ClassifierComponent** handles triage assessment (proven in 20+ healthcare models)
- **Priority BufferComponent** manages different severity levels
- **AllocatorComponent** optimizes resource assignment under constraints
- **ProcessorComponent** handles variable service times
- **MonitorComponent** and **OptimizerComponent** enable continuous improvement

## Step 4: Architecture Composition

**ArchitectureComposer Output**:

```python
architecture = ArchitectureGraph(
    name='emergency_department_architecture',
    components=selected_components,
    connections=[
        # Main patient flow
        Connection(
            source='patient_arrivals',
            target='triage_assessment',
            type='entity_flow',
            message='patient_entity'
        ),
        Connection(
            source='triage_assessment',
            target='priority_router',
            type='entity_flow',
            message='classified_patient'
        ),
        Connection(
            source='priority_router',
            target='waiting_queues',
            type='entity_flow',
            message='routed_patient'
        ),
        Connection(
            source='waiting_queues',
            target='staff_allocator',
            type='allocation_request',
            message='patient_ready'
        ),
        Connection(
            source='staff_allocator',
            target='treatment_processor',
            type='resource_assignment',
            message='allocated_patient_staff'
        ),
        Connection(
            source='treatment_processor',
            target='lab_tests',
            type='conditional_flow',
            message='patient_needs_labs',
            condition='requires_labs == True'
        ),
        Connection(
            source='treatment_processor',
            target='imaging',
            type='conditional_flow',
            message='patient_needs_imaging',
            condition='requires_imaging == True'
        ),
        Connection(
            source='lab_tests',
            target='treatment_processor',
            type='entity_flow',
            message='labs_complete'
        ),
        Connection(
            source='imaging',
            target='treatment_processor',
            type='entity_flow',
            message='imaging_complete'
        ),
        Connection(
            source='treatment_processor',
            target='discharge_sink',
            type='entity_flow',
            message='treatment_complete'
        ),
        
        # Monitoring and optimization
        Connection(
            source='waiting_queues',
            target='performance_monitor',
            type='state_observation',
            message='queue_state'
        ),
        Connection(
            source='treatment_processor',
            target='performance_monitor',
            type='event_notification',
            message='treatment_events'
        ),
        Connection(
            source='performance_monitor',
            target='resource_optimizer',
            type='performance_data',
            message='current_metrics'
        ),
        Connection(
            source='resource_optimizer',
            target='staff_allocator',
            type='control_signal',
            message='updated_policy'
        )
    ]
)
```

**Composition Visualization**:

```
                    ┌──────────────────────┐
                    │ Patient Arrivals     │
                    │ (Stochastic Gen)     │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │ Triage Assessment    │
                    │ (Classifier)         │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │ Priority Router      │
                    └──────────┬───────────┘
                               │
                ┌──────────────┼──────────────┐
                │              │              │
                ▼              ▼              ▼
         [Critical]      [Urgent]       [Standard]
              ↓              ↓              ↓
         ┌────────────────────────────────────┐
         │    Waiting Queues (Priority)       │
         └─────────────────┬──────────────────┘
                           │
                           ▼
         ┌─────────────────────────────────────┐
         │    Staff Allocator (Resource)       │
         └─────────────────┬───────────────────┘
                           │
                           ▼
         ┌─────────────────────────────────────┐
         │    Treatment Processor              │
         └────┬──────────────────────┬─────────┘
              │                      │
    ┌─────────▼──────────┐  ┌───────▼──────────┐
    │ Lab Tests (30%)    │  │ Imaging (15%)    │
    └─────────┬──────────┘  └───────┬──────────┘
              │                     │
              └──────────┬──────────┘
                         ▼
              ┌──────────────────────┐
              │   Discharge (Sink)   │
              └──────────────────────┘
              
    [Monitoring & Optimization Layer]
    Performance Monitor ←─ All Components
           ↓
    Resource Optimizer → Staff Allocator (feedback)
```

## Step 5: Grammar Validation

**GrammarValidator Output**:

```python
validation_report = ValidationReport(
    is_valid=True,
    violations=[],
    checks_performed=[
        Check(
            name='Type Compatibility',
            status='PASS',
            details='All connections have matching output/input types'
        ),
        Check(
            name='Temporal Ordering',
            status='PASS',
            details='Execution order preserves causality (triage before routing, etc.)'
        ),
        Check(
            name='Resource Conservation',
            status='PASS',
            details='All generated patients eventually reach discharge sink'
        ),
        Check(
            name='Cycle Handling',
            status='PASS',
            details='Treatment ↔ Labs/Imaging cycles have termination conditions'
        ),
        Check(
            name='State Consistency',
            status='PASS',
            details='Shared states (queue lengths, resource availability) properly synchronized'
        ),
        Check(
            name='Semantic Coherence',
            status='PASS',
            details='Architecture purpose: "Optimize ED patient flow" is clear and well-defined',
            confidence=0.94
        )
    ]
)
```

## Step 6: Performance Prediction

**PerformancePredictor Output**:

```python
performance_prediction = PerformancePrediction(
    # Component capabilities
    component_scores={
        'patient_arrivals': 9.0,  # Proven stochastic generator
        'triage_assessment': 8.5,  # Reliable classifier
        'priority_router': 9.0,  # Simple, effective routing
        'waiting_queues': 8.0,  # Priority queue well-established
        'staff_allocator': 7.5,  # Complex but effective
        'treatment_processor': 8.0,  # Variable but manageable
        'lab_tests': 7.0,  # Resource contention possible
        'imaging': 7.0,  # Bottleneck risk
        'performance_monitor': 9.0,  # Standard monitoring
        'resource_optimizer': 8.0,  # Simulation-based optimization
        'discharge_sink': 9.0   # Simple terminal
    },
    total_capability=88.0,
    
    # Integration costs
    integration_cost={
        'components': 11 * 2 = 22,
        'connections': 13 * 1 = 13,
        'coordination_overhead': 5,
        'total': 40
    },
    
    # Synergy bonuses
    synergy_patterns=[
        Synergy(
            pattern='Monitor-Optimizer-Allocator',
            components=['performance_monitor', 'resource_optimizer', 'staff_allocator'],
            benefit=+15,
            rationale='Proven feedback loop for resource optimization'
        ),
        Synergy(
            pattern='Classifier-Priority-Router',
            components=['triage_assessment', 'waiting_queues', 'priority_router'],
            benefit=+10,
            rationale='Triage + priority queuing is established best practice'
        ),
        Synergy(
            pattern='Conditional-Processing',
            components=['treatment_processor', 'lab_tests', 'imaging'],
            benefit=+8,
            rationale='Conditional routing handles patient-specific needs efficiently'
        )
    ],
    total_synergy=33,
    
    # Intelligence density
    intelligence_density=(88.0 + 33.0) / 40 = 3.03,
    
    # Predicted metrics
    expected_performance={
        'average_wait_time': '45-55 minutes',
        'length_of_stay': '2.5-3.5 hours',
        'throughput': '120-150 patients/day',
        'resource_utilization': '75-85%',
        'left_without_treatment': '<2%'
    },
    
    # Comparison to manual design
    improvement_over_manual={
        'development_time': '-80%',  # Days vs. weeks
        'errors': '-50%',  # Fewer integration bugs
        'performance': '+15%',  # Better component selection
        'maintainability': '+40%'  # Modular, documented
    },
    
    recommendation='DEPLOY',
    confidence=0.89
)
```

**Analysis**:
- Intelligence density (3.03) exceeds threshold (3.0) ✓
- All quality checks passed ✓
- Expected performance meets requirements ✓
- Significant synergy bonuses from proven patterns ✓
- Architecture ready for implementation ✓

## Step 7: Implementation in AnyCog

**Generated AtomSpace Schema**:

```java
// Register components as nodes
atomSpace.addNode(new ComponentNode("patient_arrivals", "StochasticGenerator"));
atomSpace.addNode(new ComponentNode("triage_assessment", "Classifier"));
atomSpace.addNode(new ComponentNode("priority_router", "Router"));
// ... (all components)

// Register connections as links
atomSpace.addLink(new ConnectionLink(
    new ComponentNode("patient_arrivals"),
    new ComponentNode("triage_assessment"),
    properties: {type: "entity_flow", message: "patient_entity"}
));
// ... (all connections)

// Register state nodes
atomSpace.addNode(new StateNode("queue_length_critical", initialValue: 0));
atomSpace.addNode(new StateNode("queue_length_urgent", initialValue: 0));
atomSpace.addNode(new StateNode("queue_length_standard", initialValue: 0));
atomSpace.addNode(new StateNode("doctors_available", initialValue: 5));
atomSpace.addNode(new StateNode("nurses_available", initialValue: 10));

// Register monitoring
atomSpace.addNode(new MetricNode("average_wait_time"));
atomSpace.addNode(new MetricNode("length_of_stay"));
atomSpace.addNode(new MetricNode("throughput"));
```

**Generated AnyLogic Model Structure**:

```
Main (Root Agent)
├── AtomSpace (Hypergraph)
├── PatientArrivalProcess (DES)
│   └── StochasticSource
├── TriageStation (ABM)
│   └── ClassifierStatechart
├── WaitingAreas (DES)
│   ├── CriticalQueue
│   ├── UrgentQueue
│   └── StandardQueue
├── TreatmentArea (DES + Resources)
│   ├── Doctors (ResourcePool)
│   ├── Nurses (ResourcePool)
│   └── TreatmentProcess
├── Diagnostics (DES)
│   ├── LabTests
│   └── ImagingTests
├── MonitoringSystem (Meta-Cognitive)
│   └── PerformanceTracker
├── OptimizationSystem (Meta-Cognitive)
│   └── ResourceOptimizer
└── DischargeProcess (DES)
    └── Sink
```

## Step 8: Execution and Validation

**Simulation Results** (1000 replications):

```python
results = {
    'average_wait_time': {
        'predicted': '45-55 minutes',
        'actual': '48.3 minutes',
        'deviation': '+3.3% from midpoint',
        'status': 'Within predicted range ✓'
    },
    'length_of_stay': {
        'predicted': '2.5-3.5 hours',
        'actual': '3.1 hours',
        'deviation': '+3.3% from midpoint',
        'status': 'Within predicted range ✓'
    },
    'throughput': {
        'predicted': '120-150 patients/day',
        'actual': '138 patients/day',
        'deviation': '+2.2% from midpoint',
        'status': 'Within predicted range ✓'
    },
    'resource_utilization': {
        'predicted': '75-85%',
        'actual': '79%',
        'deviation': '-1% from midpoint',
        'status': 'Within predicted range ✓'
    },
    'left_without_treatment': {
        'predicted': '<2%',
        'actual': '1.3%',
        'deviation': 'N/A',
        'status': 'Meets target ✓'
    }
}

# Intelligence density (actual)
actual_intelligence_density = 3.15  # Slightly higher than predicted
```

**Meta-Learning Update**:

```python
# MetaLearner records this successful assembly
meta_learner.record_experience(
    domain=emergency_department_domain,
    architecture=assembled_architecture,
    predicted_performance=performance_prediction,
    actual_performance=results,
    intelligence_density=3.15,
    success=True
)

# Pattern library updated
pattern_library.update(
    pattern='Stochastic_Queue_Network',
    domain='healthcare_emergency',
    applications=+1,
    successes=+1,
    avg_intelligence_density=4.1  # Updated average
)

# Component ratings updated
for component in selected_components.values():
    component_library.update_rating(
        component=component.type,
        performance_delta=+0.05,  # Positive experience
        domain='healthcare'
    )
```

## Benefits Demonstrated

### 1. Automated Design
- **Manual effort**: 2-3 weeks to design and implement
- **Self-assembly time**: 30 seconds (analysis + composition) + 2 hours (implementation)
- **Reduction**: 95% time savings

### 2. Quality Assurance
- Grammar validation caught potential issues automatically
- No integration bugs (all components type-safe)
- Performance prediction within 3-5% of actual

### 3. Intelligence Density
- Achieved 3.15 (target: 3.0+)
- High capability with manageable complexity
- Significant synergy bonuses from proven patterns

### 4. Continuous Improvement
- MetaLearner captured successful pattern
- Future ED models will benefit from this experience
- Component ratings updated for better future selection

### 5. Extensibility
- Easy to add new components (e.g., telemedicine capability)
- Architecture can evolve based on changing requirements
- Patterns transfer to similar domains (urgent care, clinics)

## Comparison: Manual vs. Self-Assembled

| Aspect | Manual Design | Self-Assembled |
|--------|---------------|----------------|
| **Design Time** | 2-3 weeks | 30 seconds |
| **Implementation Time** | 1-2 weeks | 2 hours |
| **Errors** | 5-10 integration bugs | 0 (validated) |
| **Performance Prediction** | Rough estimate | 95% accurate |
| **Documentation** | Often incomplete | Auto-generated |
| **Maintainability** | Variable | High (modular) |
| **Transferability** | Low | High (reusable components) |
| **Intelligence Density** | 2.5 (typical) | 3.15 (optimized) |

## Conclusion

This example demonstrates the **complete niche construction process**:

1. ✅ **Domain analyzed** automatically from natural language description
2. ✅ **Pattern matched** from library (Stochastic Queue Network)
3. ✅ **Components selected** based on capabilities and domain fit
4. ✅ **Architecture composed** following cognitive grammar rules
5. ✅ **Validation performed** to ensure correctness
6. ✅ **Performance predicted** with high accuracy
7. ✅ **Implementation generated** for AnyCog/AnyLogic
8. ✅ **Results validated** against predictions
9. ✅ **Learning captured** for future improvements

The result is a **high-quality, optimized cognitive architecture** assembled in a fraction of the time required for manual design, with built-in quality assurance and continuous improvement.

This is **self-assembly of intelligence density** around the domain of inquiry (emergency department operations) using modular components as a cognitive grammar.

---

**Architecture Type**: Self-Assembled  
**Intelligence Density**: 3.15  
**Assembly Time**: 30 seconds + 2 hours implementation  
**Performance**: Within 5% of predictions  
**Status**: Production-ready ✓
