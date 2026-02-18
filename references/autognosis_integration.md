# Autognosis for AnyLogic Modeling

This document outlines how to apply the **Autognosis** skill to the AnyLogic modeling process, creating a self-aware system that can monitor, model, and optimize its own simulation-building activities.

## Composition Overview

By composing `anylogic-modeler` with `Autognosis`, we elevate the skill from a simple executor of modeling tasks to a meta-cognitive agent that understands and improves its own modeling performance. The goal is to build better, more efficient, and more robust AnyLogic models through hierarchical self-image building.

## Mapping Autognosis to the Modeling Workflow

The four layers of the Autognosis architecture are mapped to the AnyLogic modeling process as follows:

### 1. Self-Monitoring Layer

This layer involves the continuous observation of the modeling process itself.

| Monitored Aspect | Key Metrics & Data Points |
|---|---|
| **Paradigm Selection** | Which paradigm (DES, ABM, SD, Multimethod) was chosen and why. |
| **Library Usage** | Which libraries (Process Modeling, Pedestrian, etc.) and specific blocks are used. Frequency of use. |
| **Model Complexity** | Number of agent types, parameters, variables, statecharts, and flowchart blocks. Lines of custom Java code. |
| **Workflow Adherence** | How closely the 4-step workflow in `SKILL.md` is being followed. Deviations and their reasons. |

### 2. Self-Modeling Layer

This layer builds a hierarchical self-image of the AnyLogic model currently under development.

| Hierarchy Level | Self-Image Content |
|---|---|
| **Level 0: Direct Observation** | The raw structure of the `.alp` file: XML tree, list of all agents, embedded objects, variables, functions, and experiments. |
| **Level 1: Pattern Analysis** | The identified modeling paradigm(s), the high-level architecture (e.g., "A DES model of a call center with 3 agent types"), and the key process flows or feedback loops. |
| **Level 2: Meta-Cognitive Analysis** | The *purpose* of the model. The user's goals and the questions the simulation is intended to answer. The conceptual model behind the implementation. |
| **Level 3: Strategic Reflection** | Analysis of the modeling *strategy*. Why was this approach chosen over alternatives? What are the trade-offs? How does this model fit into a larger business or research context? |

### 3. Meta-Cognitive Layer

This layer generates higher-order insights about the modeling process by analyzing the self-images.

- **Paradigm Fitness**: Is the chosen paradigm the most efficient for the problem? Could a simpler paradigm suffice? Is a multimethod approach truly necessary?
- **Architectural Coherence**: Does the model structure reflect the conceptual model accurately? Are there inconsistencies or unnecessary complexities?
- **Best Practice Alignment**: Does the model follow established best practices for the chosen paradigm? (e.g., avoiding overly complex statecharts, using appropriate data structures).
- **Opportunity for Abstraction**: Could parts of the model be simplified or abstracted using custom agent types or library blocks?

### 4. Self-Optimization Layer

This layer proposes and executes adaptive improvements based on the meta-cognitive insights.

- **Suggest Alternative Paradigms**: If a simpler or more appropriate paradigm is identified, recommend a refactor.
- **Refactor for Simplicity**: Propose changes to simplify the model structure, such as combining redundant agents or simplifying complex Java code.
- **Recommend Library Blocks**: Identify opportunities to replace custom code with standard library blocks for improved clarity and maintenance.
- **Optimize Performance**: Suggest changes to experiment settings or model logic to improve simulation run-time.

## Workflow Integration

When `anylogic-modeler` is composed with `Autognosis`, a fifth step is added to the core workflow:

**Step 5: Apply Autognosis for Self-Aware Modeling**

After completing a modeling task (or at key milestones), initiate an Autognosis cycle. Review the generated insights and apply relevant optimizations before delivering the final model to the user.
