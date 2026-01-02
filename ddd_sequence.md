# Domain-Driven Design Implementation Sequence

## Table of Contents

1. [High-Level Process](#high-level-process)
2. [Event Storming Process](#event-storming-process)
3. [Hierarchical Relationship of DDD Concepts](#hierarchical-relationship-of-ddd-concepts)
4. [Techniques to Identify Bounded Contexts](#techniques-to-identify-bounded-contexts)
5. [Techniques to Identify Aggregates](#techniques-to-identify-aggregates)
6. [How to Identify Domain Types](#how-to-identify-domain-types)
7. [Factory-Repository Workflow](#factory-repository-workflow)

## High-Level Process

The Domain-Driven Design implementation follows six main phases:

### Phase 1: Domain Discovery & Knowledge Crunching
- Collaborate with domain experts to understand the business domain
- Conduct workshops and interviews to extract tacit knowledge
- Create a shared understanding between technical and business teams
- Use Event Storming as a collaborative modeling technique

### Phase 2: Strategic Design
- Identify and map bounded contexts (logical groupings of domain models with coherent areas of responsibility)
- Define the core domain (areas of highest business value)
- Identify core, supporting, and generic subdomains and their relationships
- Create a context map showing relationships between bounded contexts
- Establish a ubiquitous language (shared vocabulary)

### Phase 3: Tactical Design & Domain Modeling
- Create domain models for each bounded context
- Identify entities, value objects, aggregates, and domain events
- Define domain services for complex operations
- Ensure domain services have a single bounded context responsibility
- Domain services are good candidates for containerization with independent resource and scaling needs
- Establish repositories for persistence access

### Phase 4: Define Boundaries & Integration
- Specify boundaries between contexts
- Design integration patterns (shared kernel, customer/supplier, etc.)
- Create anti-corruption layers where needed
- Determine communication patterns between contexts

### Phase 5: Implementation
- Translate models into code structures
- Apply hexagonal/clean architecture principles
- Implement bounded contexts and their interactions
- Develop event-driven mechanisms for cross-context communication

### Phase 6: Continuous Refinement
- Regularly review and refactor the model
- Realign with business as understanding evolves
- Refine the ubiquitous language
- Adapt boundaries as the domain knowledge deepens

## Event Storming Process

Event Storming is a collaborative modeling technique used in Phase 1 (Domain Discovery).

**Goal:** Create a shared understanding of the business domain by visualizing the complete business process flow as a timeline of domain events. The output helps identify bounded contexts, aggregate boundaries, and business workflows.

### The Event Storming Workshop

**Who participates:** Domain experts, developers, business stakeholders, and anyone with domain knowledge (10+ people can work in parallel).

**Materials needed:** Large wall space, colored sticky notes (orange, blue, yellow, green, lilac, pink), markers.

**Duration:** Typically 4-8 hours for initial modeling, with follow-up sessions for refinement.

### Process: Four Sequential Phases

#### Phase 1: Chaotic Exploration (Big Picture)

**Steps:**
1. Ask everyone to write domain events (things that happened in the domain)
2. Place orange sticky notes on the wall in any order
3. Generate as many events as possible without restrictions
4. Everyone contributes simultaneously - no discussion yet

**Output:**
- Unordered collection of domain events
- Many duplicates and gaps
- Raw business knowledge captured

#### Phase 2: Timeline Enforcement

**Steps:**
1. Organize events chronologically from left to right
2. Remove duplicate events
3. Identify missing events (gaps in the timeline)
4. Establish temporal relationships ("X happens before Y")
5. Discuss and resolve conflicting interpretations

**Output:**
- Chronological timeline of domain events
- Cleaned, de-duplicated event list
- Shared understanding of business flow

#### Phase 3: Parallel Activities Identification

**Steps:**
1. Group related events into horizontal swim lanes
2. Identify concurrent processes that happen in parallel
3. Recognize different areas/subdomains of the business
4. Add commands (blue) that trigger events
5. Add actors (small yellow) who issue commands
6. Add policies (lilac) that react to events automatically

**Output:**
- Swim lanes representing different business areas
- Commands and actors linked to events
- Business rules and automated reactions identified

#### Phase 4: Pivotal Events & Bounded Context Discovery

**Steps:**
1. Identify pivotal events (major state changes or decision points)
2. Mark boundaries where context or language changes
3. Identify aggregates (large yellow) that handle commands and produce events
4. Add read models (green) showing information needs
5. Mark external systems (pink) that interact with the domain
6. Draw boundaries around cohesive areas → these become bounded context candidates

**Output:**
- Pivotal events marked
- Candidate bounded contexts identified
- Aggregates and their responsibilities
- Integration points with external systems
- Foundation for strategic design phase

### Key Elements Used in Event Storming

Event Storming uses different colored sticky notes to represent different concepts:

#### Domain Events (Orange Sticky Notes)

Events are things that happened (past tense: "Order Placed", "Payment Captured"). They are the primary building blocks—capturing essential business facts and forming the timeline. See [Domain Events definition](definitions.md#20-domain-events).

#### Commands (Blue Sticky Notes)

Commands are requests to do something (imperative: "Place Order", "Process Payment"). They trigger events when successful but can fail if business rules are violated. See [Commands definition](definitions.md#21-commands).

#### Other Event Storming Elements

- **Actors (Small Yellow Sticky Notes)**: People or systems that trigger commands
- **Read Models/Queries (Green Sticky Notes)**: Information needed to make decisions
- **Policies/Business Rules (Lilac Sticky Notes)**: Automated reactions ("Whenever X happens, then do Y")
- **Aggregates (Large Yellow Sticky Notes)**: Clusters that handle commands and produce events
- **External Systems (Pink Sticky Notes)**: Third-party systems that interact with the domain

### Benefits

- Easy for domain experts to understand (no technical diagrams or notation)
- Allows many people (10+) to work in parallel on the model
- Reveals how people interact and who has specialized knowledge
- Creates common understanding of the domain

## Hierarchical Relationship of DDD Concepts

```
Domain (The overall business area, e.g., e-commerce)
└── Subdomains (Core, Supporting, Generic)
    └── Bounded Contexts (Order Processing, Invoicing, Delivery Process)
        └── Domain Model (with ubiquitous language)
            ├── Aggregates (consistency boundaries)
            │   ├── Entities (objects with identity)
            │   └── Value Objects (immutable objects)
            ├── Domain Events (things that happened)
            ├── Services (logic that doesn't fit in entities)
            ├── Factories (complex object creation)
            └── Repositories (persistence abstraction)
```

### Understanding the Hierarchy

- **Domain**: The entire business area you're working in
- **Subdomains**: Logical divisions of the domain (Core, Supporting, Generic)
- **Bounded Contexts**: Explicit boundaries where models are unified
- **Domain Model**: The conceptual model within each bounded context
- **Building Blocks**: Aggregates, Entities, Value Objects, Services, Factories, Repositories

## Techniques to Identify Bounded Contexts

### 1. Swim Lanes and Pivotal Events
Areas between swimlanes and pivotal events in Event Storming diagrams can be candidates for bounded contexts.

### 2. Different Ubiquitous Languages
When the same term has different meanings in different contexts (e.g., billing customer vs. shipping customer), this suggests separate bounded contexts.

### 3. Separate Functionality & Model Applicability
- Areas with distinct functionality that could be assigned to different teams
- Ask if a model that covers one area is also suitable for related but different functionality
- Consider whether different processes should have separate bounded contexts

### 4. Process Boundaries
- Look for natural separations in the business process
- Areas where the data that is important changes
- **Example**: When a parcel leaves the warehouse, product data becomes less important and tracking data becomes more important

### 5. Discussion and Iteration
- Identifying bounded contexts is not purely mechanical
- Requires discussion, analysis, and iteration
- Team needs to discuss if the model for one bounded context can be reused for another

### 6. Team Topologies
Align stream-aligned teams with bounded contexts.

## Techniques to Identify Aggregates

### Key Principles

- **Consistency Boundaries**: Aggregates define transactional consistency boundaries
- **Aggregate Roots**: Each aggregate has a root entity as the entry point
- **Business Rules and Invariants**: Aggregates enforce rules that must always be true

### Identification Techniques

1. **Analyze Business Requirements**
   - Examine relationships and dependencies between domain objects

2. **Identify Transactional Needs**
   - Determine which groups of objects need to be modified within a single transaction

3. **Focus on Invariants**
   - Identify business rules and constraints that must be enforced

4. **Consider Data Modification Patterns**
   - Observe how domain experts interact with the system

5. **Event Storming**
   - Reveals patterns of commands and events related to specific business processes

6. **Look for Natural Groupings**
   - Objects that conceptually belong together (e.g., Order and OrderLines)

### Important Considerations

- Minimize aggregate size to improve performance and reduce contention
- Minimize cross-aggregate relationships to maintain loose coupling
- Use aggregate IDs and eventual consistency to manage relationships between aggregates

## How to Identify Domain Types

### Core Domain Tests

**Questions to Ask:**
- Competitive advantage? (differentiates us)
- Proprietary knowledge? (unique expertise)
- Strategic value? (mission-critical)
- Worth best developers? (justify investment)

**Indicators:**
- Business strategy mentions it
- Requires rare expertise
- Trade secret territory
- High innovation frequency
- "Why customers choose us"

### Generic Subdomain Tests

**Questions to Ask:**
- Universal need? (every business needs this)
- Can buy? (off-the-shelf exists)
- Standard solution? (well-understood)
- Complexity without value? (necessary but not differentiating)

**Indicators:**
- Books/libraries exist
- Every system needs it
- Technical not domain problem
- "Every company has this"

### Supporting Subdomain Tests

**Questions to Ask:**
- Business-specific but not core?
- Can't easily buy? (too customized)
- Operational necessity? (required but not innovative)

**Indicators:**
- Process-specific to company
- Required but not strategic
- Medium complexity
- "How we do it, not why we win"

## Factory-Repository Workflow

This workflow demonstrates the complete object lifecycle in DDD.

### Creating a New Object

1. **CLIENT** needs new Order aggregate
2. Calls **FACTORY**.createOrder(customerId, items)
3. **FACTORY** creates Order (root) + OrderLines (internal entities)
   - Enforces: order must have at least one line item
   - Generates order ID
   - Returns complete, valid Order aggregate
4. **CLIENT** calls **REPOSITORY**.save(order)
5. **REPOSITORY** persists entire aggregate to database

### Retrieving an Existing Object

6. **CLIENT** needs existing Order
7. Calls **REPOSITORY**.findOrder(orderId)
8. **REPOSITORY** retrieves from database
   - May use Factory for reconstitution
   - Returns complete Order aggregate
9. **CLIENT** modifies through Order root (Aggregate pattern)
   - Example: `order.addLineItem(product, quantity)`
   - Root enforces invariants
10. **CLIENT** calls **REPOSITORY**.save(order) to persist changes

### Key Relationships

- **Aggregate** = WHAT (structure of domain objects)
- **Factory** = HOW to CREATE (construction mechanism)
- **Repository** = HOW to ACCESS (retrieval/storage mechanism)

## Integration Patterns Between Contexts

When implementing Phase 4 (Define Boundaries & Integration), use integration patterns to define how bounded contexts communicate and relate to each other.

For detailed information on all 7 integration patterns (Shared Kernel, Customer-Supplier, Conformist, Anticorruption Layer, Separate Ways, Open Host Service, Published Language) with examples, see [Definitions - Integration Patterns Between Contexts](definitions.md#16-integration-patterns-between-contexts).

## Context Map

A Context Map is created during Phase 2 (Strategic Design) and maintained throughout the project. It provides a global view of all bounded contexts, their boundaries, relationships, and integration patterns.

For detailed information about Context Maps, see [Definitions - Context Map](definitions.md#15-context-map).
