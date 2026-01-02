# Domain-Driven Design Definitions

## Table of Contents

1. [Domain-Driven Design (DDD)](#1-domain-driven-design-ddd)
2. [Domain](#2-domain)
3. [Model](#3-model)
4. [Ubiquitous Language](#4-ubiquitous-language)
5. [Domain Objects](#5-domain-objects)
6. [Entity](#6-entity)
7. [Value Objects](#7-value-objects)
8. [Service](#8-service)
9. [Service vs Entity](#9-service-vs-entity)
10. [Aggregate](#10-aggregate)
11. [Factory](#11-factory)
12. [Repository](#12-repository)
13. [Context](#13-context)
14. [Bounded Context](#14-bounded-context)
15. [Context Map](#15-context-map)
16. [Integration Patterns Between Contexts](#16-integration-patterns-between-contexts)
17. [Subdomain](#17-subdomain)
18. [Distillation Process](#18-distillation-process)
19. [Types of Domains](#19-types-of-domains)

## 1. Domain-Driven Design (DDD)

Domain-Driven Design is a software development approach that focuses on creating software deeply rooted in the business domain it serves. It emphasizes creating a model that accurately captures the core concepts and elements of the domain, then implementing that model directly in code. DDD combines design and development practices to ensure software fits harmoniously with the domain, making the code a reflection of the domain itself.

![DDD Elements](media/ddd_elements.png)
*Image source: "Patterns, Principles, and Practices of Domain-Driven Design" by Scott Millett, Nick Tune*

## 2. Domain

**Definition:** The problem space that a business occupies and provides solutions to.

A domain encompasses everything that the business must contend with, including:
- Rules and processes
- Ideas and concepts
- Business-specific terminology
- Anything related to its problem space, regardless of whether or not the business concerns itself with it

**Key Principle:** The domain exists regardless of the existence of the business.

The domain is the real-world problem space that software is meant to address - the business processes being automated or the real business problems that the software aims to solve.

**Example:** In a banking application, the domain includes concepts like accounts, transactions, customers, financial rules, regulatory compliance, risk management, and market conditions - whether the bank actively manages all these aspects or not.

## 3. Model

**Definition:** An abstraction of the actual domain useful for business purposes.

A model is a rigorously organized and selective abstraction of domain knowledge - it's the internal representation of the target domain. The pieces and properties of the domain that are most important to the business are used to generate the model.

**Key Principle:** According to Eric Evans, "a domain model is not a particular diagram; it is the idea that the diagram is intended to convey."

**Domain Model Characteristics:**
- Part of the **solution space** (a construct the business uses to solve problems)
- Focuses on business priorities and what matters most
- Synthesizes thinking about core concepts, relationships, and behaviors into a form that can be expressed in software

**The main domain model of a business is discernible through:**
- The products the business provides its customers
- The interfaces by which customers interact with the products
- The various other processes and functions by which the business fulfills its stated goals

**Evolution:** Models often need to be refined as the domain changes and as business priorities shift.

**Subdomain Model:** A subdomain can have its own model, which is essentially a domain model in its own right, focused on that specific subset of responsibilities.

## 4. Ubiquitous Language

The Ubiquitous Language is a common language shared by the entire team (developers, domain experts, stakeholders) that is based on the domain model. It's used consistently in all communications - speech, writing, diagrams, and code.

**Why We Need It:**
- Overcomes communication barriers between technical teams and domain experts
- Eliminates the need for translation between different dialects
- Ensures everyone shares the same understanding of domain concepts
- Connects the model directly to the code

**Key Principle:** The language evolves with the domain model - when terms change in discussions, they change in the code and vice versa.

**How It Works in Practice:**

1. **In Conversations:**
   - Domain expert: "When a customer places an order, we need to validate their credit limit before authorizing the transaction."
   - Developer: "So we'll have the Order aggregate check the Customer's CreditLimit before calling PaymentService.authorize()?"
   - Domain expert: "Exactly - and if the authorization succeeds, we publish an OrderAuthorized event."

2. **In Code:**
   ```java
   class Order {
       void placeOrder(Customer customer, PaymentService paymentService) {
           if (customer.hasAvailableCredit(this.totalAmount)) {
               paymentService.authorize(this);
               this.publishEvent(new OrderAuthorized(this.orderId));
           }
       }
   }
   ```
   Notice: The code uses the exact same terms (Order, Customer, CreditLimit, authorize, OrderAuthorized) as the conversation.

3. **In Documentation:**
   - User stories use domain terms: "As a customer, when I place an order that exceeds my credit limit, the system should reject the authorization."
   - Architecture diagrams label components with domain language: "Order Aggregate", "Payment Service", "Credit Limit Policy"

**Examples from Different Domains:**

**Banking Domain:**
- Domain terms: "Account", "Transaction", "Posting", "Balance", "Reconciliation", "Ledger"
- NOT technical terms: "Record", "Entry", "Update", "Calculate"
- Domain expert says: "We need to post the transaction to the ledger and reconcile the account balance"
- Code reflects this:
  ```java
  ledger.postTransaction(transaction);
  account.reconcileBalance();
  ```

**E-Commerce Domain:**
- Domain terms: "Cart", "Checkout", "Fulfillment", "Shipment", "Backorder", "Inventory"
- NOT technical terms: "Session data", "Process payment", "Database update"
- Domain expert says: "When inventory is depleted during checkout, create a backorder"
- Code reflects this:
  ```java
  if (inventory.isDepleted(product)) {
      backorderService.createBackorder(order);
  }
  ```

**Anti-Patterns (What NOT to do):**

❌ **Translation disconnect:**
- Domain expert: "We need to verify the customer's eligibility"
- Code: `checkUserValidation()` // Wrong - uses different terms

✅ **Ubiquitous Language:**
- Domain expert: "We need to verify the customer's eligibility"
- Code: `verifyCustomerEligibility()` // Correct - same terms

❌ **Technical jargon replacing domain language:**
- Code: `persistOrderData()`, `updateDatabase()`
- Should be: `saveOrder()`, `recordPayment()`

✅ **Domain language in code:**
- Code: `order.place()`, `payment.authorize()`, `shipment.dispatch()`

**Benefits of Using Ubiquitous Language:**
- Developers understand business requirements without translation
- Domain experts can read and validate code structure
- New team members learn the domain faster
- Bugs from miscommunication are reduced
- Refactoring preserves domain meaning
- Documentation stays synchronized with code

## 5. Domain Objects

A Domain Object is any object that is part of the domain layer and represents concepts from the business domain. This is a general category that encompasses Entities, Value Objects, Services, and Aggregates.

**Characteristics:**
- **Domain-focused**: Expresses concepts directly from the business domain
- **Part of the model**: Represents elements identified during domain modeling
- **Uses ubiquitous language**: Named and described using domain terminology
- **Encapsulates domain logic**: Contains behavior relevant to the domain, not infrastructure concerns
- **Isolated from infrastructure**: Kept separate from UI, database, and other technical concerns through layered architecture
- **Rich behavior**: Not just data holders - contains meaningful domain operations
- **Model integrity**: Maintains invariants and business rules
- **Expressed in code**: The domain objects in code should directly reflect the domain model

**Key Principle:** Domain objects should be "free of the responsibility of displaying themselves, storing themselves, managing application tasks" so they can "focus on expressing the domain model."

**Types of Domain Objects:**
1. **Entities** - Objects with identity
2. **Value Objects** - Objects defined by attributes
3. **Services** - Stateless operations
4. **Aggregates** - Clusters of related objects
5. **Factories** - Handle complex object creation
6. **Repositories** - Manage object retrieval and storage

## 6. Entity

An Entity is an object that has a distinct identity that remains constant throughout its lifecycle, regardless of changes to its attributes. It's distinguished by its identity rather than its attributes.

**Characteristics:**
- Has a unique, persistent identity
- Identity remains unchanged throughout the object's life
- Can be tracked across different states and representations
- Identity distinguishes it from other objects even with identical attributes
- Maintains continuity across the system's lifetime

**Examples:**

1. **Bank Account:** A bank account is an Entity identified by its account number. The balance, transactions, and even the account holder might change, but the account number remains constant and uniquely identifies that specific account throughout its existence.

2. **Person/Customer:** A person in a system has identity (perhaps Social Security number or a combination of name, birth date, and birthplace). Two people might have the same name, but they're still distinct individuals with separate identities.

## 7. Value Objects

A Value Object is an object used to describe certain aspects of a domain that has no conceptual identity. It's defined entirely by its attributes - what matters is what the object is, not who or which it is.

**Characteristics:**
- No unique identity - interchangeable with other instances having the same values
- Defined completely by its attributes
- Should be immutable (created via constructor, never modified)
- Can be freely shared when immutable
- Easily created and discarded without lifecycle tracking
- Equality based on attribute values, not reference

**Examples:**

1. **Address:** An Address object containing street, city, and state is a Value Object. Two addresses with identical values are completely interchangeable. You don't care about "which" address object it is, only what address it represents.
   ```
   Address: street="123 Main St", city="Dallas", state="TX"
   ```

2. **Money/Currency Amount:** A monetary value like $50.00 USD is a Value Object. If two objects both represent $50.00 USD, they're functionally identical and interchangeable. The specific object instance doesn't matter, only the value it represents.

## 8. Service

A Service is an object that provides operations representing significant domain concepts that don't naturally belong to any Entity or Value Object. It's an interface that provides behavior for the domain without holding state.

**Characteristics:**
- Stateless - has no internal state of its own
- Domain operation focus - performs operations significant to the domain
- Interfaces with domain objects - operates on Entities and Value Objects
- Clear domain concept - represents an important behavior explicitly mentioned in the Ubiquitous Language
- Multi-object operations - often coordinates operations across multiple objects or aggregates
- Named from domain language - operation names are part of the Ubiquitous Language

**Examples:**

1. **LoanEligibilityService:** Determines if a customer is eligible for a loan based on credit score and loan terms.
   ```
   class LoanEligibilityService {
       isEligible(customer: Customer, loanTerms: LoanTerms): boolean {
           return customer.creditScore > 650 && loanTerms.amount < 50000;
       }
   }
   ```

2. **Money Transfer Service:** A service that transfers money between bank accounts - this behavior doesn't naturally belong to either account entity.

**DDD Service vs SOA Service:**

DDD Services and SOA (Service-Oriented Architecture) Services serve different purposes. **DDD Services** are domain objects within a bounded context that encapsulate domain logic not belonging to entities or value objects (e.g., `TransferFundsService`, `CalculateShippingCost`). **SOA Services** are coarse-grained, independently deployable components exposed through network APIs that represent entire business capabilities or bounded contexts (e.g., `PaymentService`, `OrderManagementService`). A DDD Service operates at the domain model level, while an SOA Service operates at the distributed system architecture level—an SOA Service might contain multiple DDD Services within its implementation.

## 9. Service vs Entity

| Aspect | Service | Entity |
|--------|---------|--------|
| **State** | Stateless - no internal state | Stateful - holds data and has identity |
| **Purpose** | Performs operations/behavior | Represents domain concepts with identity |
| **Identity** | No identity needed | Has unique, persistent identity |
| **Focus** | What it does (behavior) | What it is (identity and attributes) |
| **Lifecycle** | No lifecycle - just provides operations | Has a lifecycle (created, modified, destroyed) |
| **Persistence** | Not persisted | Often persisted to maintain state and identity |

**Key Insight:** Services prevent domain behavior from bloating entities when that behavior doesn't naturally belong to any particular entity.

## 10. Aggregate

An Aggregate is a cluster of associated domain objects (Entities and Value Objects) that are treated as a single unit for data changes. It has a boundary that separates objects inside from those outside, with one Entity serving as the Aggregate Root.

**Characteristics:**
- Single unit for changes - all objects in the aggregate are treated as one unit regarding data changes
- Has a boundary - clear demarcation separating internal objects from external ones
- One root entity - only one Entity acts as the root, the sole entry point from outside
- Root-only external access - external objects can only hold references to the root, not internal objects
- Enforces invariants - the root is responsible for maintaining all business rules within the aggregate
- Transactional consistency - changes to the aggregate are atomic (all or nothing)
- Deletion cascade - when the root is deleted, all objects in the aggregate are deleted
- Local identity for internal entities - internal entities have identity that only makes sense within the aggregate

**Examples:**

1. **Customer Aggregate:**
   ```
   Customer (Root Entity)
   ├─ customerID (identity)
   ├─ name
   ├─ Address (Value Object)
   │  ├─ street
   │  ├─ city
   │  └─ state
   └─ ContactInfo (Value Object)
      ├─ homePhoneNumber
      ├─ workPhoneNumber
      └─ emailAddress
   ```
   The Customer is the aggregate root. External objects can only reference the Customer, not directly access Address or ContactInfo.

2. **Order Aggregate:**
   ```
   Order (Root Entity)
   ├─ orderID
   ├─ orderDate
   ├─ OrderLine items (Entities with local identity)
   │  ├─ lineItemID (only meaningful within this order)
   │  ├─ product reference
   │  ├─ quantity
   │  └─ price
   └─ ShippingAddress (Value Object)
   ```
   The Order root controls all line items. Line item IDs only make sense within their order context.

## 11. Factory

A Factory is an object responsible for encapsulating the knowledge and logic necessary to create complex objects or entire Aggregates. It handles the construction process, ensuring all invariants are met and objects are created in a valid, complete state.

**Characteristics:**
- Encapsulates creation logic - hides complex construction details from clients
- Ensures atomicity - creates objects completely or not at all (fails with exception if invalid)
- Enforces invariants - guarantees all business rules are satisfied at creation time
- Creates complete aggregates - when creating an aggregate, creates the root along with all required internal objects
- Part of domain design - belongs to the domain layer
- No client dependency on concrete classes - client doesn't need to know implementation details

**When to Use a Factory:**
- Construction is complicated
- Creating dependent objects is needed
- Object creation requires enforcing invariants
- Need to hide concrete class implementations

**When NOT to Use (use simple constructor instead):**
- Construction is simple
- No creation of dependent objects needed
- All attributes passed via constructor
- Client interested in choosing implementation/strategy

**Examples:**

1. **RouteFactory:**
   ```
   RouteFactory
     - getNextID() → generates unique ID (e.g., R2345)
     - createRoute(constraint, cities[]) → creates complete Route
       - Creates new Route entity with generated ID
       - Initializes constraint
       - Creates and associates City value objects
       - Returns fully formed, valid Route
   ```

2. **Container Factory Method:**
   ```
   Container (Aggregate Root)
     - createComponent(Type t) → Factory Method
       - Determines concrete component class based on type
       - Instantiates new component
       - Adds component to container's collection
       - Enforces container-component relationship
       - Returns component to client
   ```

## 12. Repository

A Repository is an object that encapsulates the logic for accessing domain objects from persistent storage, providing the illusion of an in-memory collection. It acts as a storage and retrieval mechanism for Aggregate Roots.

**Characteristics:**
- Storage abstraction - hides persistence infrastructure details from domain layer
- Collection illusion - provides interface like an in-memory collection
- Aggregate root access - only provides repositories for Aggregate Roots, not internal objects
- Query interface - offers methods to retrieve objects by criteria (ID, attributes, specifications)
- Add/remove operations - methods to store and delete objects
- Encapsulates retrieval - whether from cache, database, or other storage is hidden
- Reconstitutes objects - returns fully instantiated domain objects, not raw data

**Why Repository is Important:**
- Decouples domain from infrastructure
- Prevents model corruption
- Maintains aggregate integrity
- Simplifies testing
- Centralizes access logic
- Supports multiple storage strategies

**Relationship with Factory:**
- Factory creates NEW objects from scratch
- Repository retrieves EXISTING objects from storage
- When adding a new object to Repository, create it with Factory first, then store it

**Examples:**

1. **CustomerRepository:**
   ```
   CustomerRepository
     - findCustomer(string id) → Customer
       // Retrieves customer by ID from database

     - findCustomers(Criteria c) → Collection<Customer>
       // Returns all matching customers

     - addCustomer(Customer customer) → void
       // Persists new customer to database

     - removeCustomer(string id) → void
       // Deletes customer and all dependent data
   ```

   **Usage flow:**
   ```
   // Factory creates the object
   Customer newCustomer = customerFactory.createCustomer("C0123");

   // Repository stores it
   customerRepository.addCustomer(newCustomer);

   // Later, retrieve it
   Customer customer = customerRepository.findCustomer("C0123");
   ```

2. **OrderRepository:**
   ```
   OrderRepository
     - findOrder(orderId) → Order
       // Retrieves order with all line items

     - findOrdersByCustomer(customerId) → Collection<Order>
       // Returns all orders for a customer

     - findPendingOrders() → Collection<Order>
       // Uses specification/criteria pattern

     - save(Order order) → void
       // Persists order and all line items atomically
   ```

## 13. Context

The set of conditions and boundaries where a model's terms have specific, unambiguous meaning.

**What Happens When Application is Large:**
- Model fragments across teams
- Same terms mean different things
- One unified model becomes impossible
- Accidental corruption increases
- Communication breaks down
- Integration becomes tangled

**Solution:** Consciously divide into multiple bounded contexts with explicit boundaries and relationships.

## 14. Bounded Context

**Definition:** The logical boundaries, including the inputs, outputs, events, requirements, processes, and data models, relevant to the subdomain.

An explicit boundary within which a domain model is unified and terms have precise meaning.

**Key Principles:**

**Alignment with Subdomains:** While ideally a bounded context and a subdomain will be in complete alignment, legacy systems, technical debt, and third-party integrations often create exceptions.

**Solution Space Property:** Bounded contexts are a property of the solution space and have a significant impact on how microservices interact with one another.

**Cohesion:** The degree to which elements within a module (or bounded context) belong together—high cohesion means related functionality is grouped together while unrelated functionality is separated.

**High Cohesion:** Bounded contexts should be highly cohesive:
- Internal operations should be intensive and closely related
- Vast majority of communication occurs internally rather than cross-boundary
- Highly cohesive responsibilities allow for reduced design scope and simpler implementations

**Loose Coupling:** Connections between bounded contexts should be loosely coupled:
- Changes made within one bounded context should minimize or eliminate the impact on neighboring contexts
- Reduces dependencies and allows independent evolution

**Characteristics:**
- Explicit boundaries - clear demarcation where model applies
- Internal consistency - model unified within bounds
- Distinct language - terms have specific meanings inside
- Team alignment - typically one team per context
- Physical manifestation - maps to code bases, schemas, modules
- Isolated evolution - can refactor without affecting other contexts
- Integration points - defined relationships with other contexts
- Appropriate size - manageable by one team
- Conceptual cohesion - related concepts grouped together

**Examples:**

**Example 1: E-Commerce System**

In an e-commerce system, the same business concepts (like "Customer" and "Order") mean different things in different contexts:

1. **Shopping Context**
   - **Key model concept:** Customer (as shopper with preferences and cart)
   - **Domain responsibilities:** Product catalog browsing, cart management, checkout process
   - **Focus:** Web UI and user experience
   - **Ubiquitous language:** "Add to cart", "Browse products", "Checkout", "Apply coupon"

2. **Reporting Context**
   - **Key model concept:** Customer (as data point in analytics, not a person with behavior)
   - **Domain responsibilities:** Sales metrics, conversion analytics, revenue reporting
   - **Focus:** Read-only analytical model, may share same database but different model
   - **Ubiquitous language:** "Conversion rate", "Average order value", "Sales trends"

3. **Warehouse Context**
   - **Key model concept:** Order (as fulfillment task, not a shopping cart)
   - **Domain responsibilities:** Inventory management, picking, packing, shipping
   - **Focus:** Physical fulfillment operations
   - **Integration:** Receives Order as Value Object via messaging from Shopping Context
   - **Ubiquitous language:** "Pick order", "Pack shipment", "Update inventory"

**Key insight:** Notice "Customer" and "Order" have completely different meanings and responsibilities in each context, yet they all work together as one system.

**Example 2: Banking System**

In a banking system, contexts separate different aspects of banking operations:

1. **Customer Management Context**
   - **Key model concept:** Customer (as person/legal entity with identity and relationships)
   - **Domain responsibilities:** Customer onboarding, KYC (Know Your Customer), relationship management
   - **Focus:** Customer identity, compliance, documentation
   - **Ubiquitous language:** "Verify identity", "Complete KYC", "Update contact information"

2. **Account Operations Context**
   - **Key model concept:** Account (as financial instrument with balance and transactions)
   - **Domain responsibilities:** Transaction processing, posting, balance reconciliation, statement generation
   - **Focus:** Financial accuracy and transactional integrity
   - **Integration:** Customer is just an ID reference here (not a full entity)
   - **Ubiquitous language:** "Post transaction", "Reconcile balance", "Generate statement"

**Key insight:** In Account Operations, "Customer" is merely a reference ID, not a full entity—this context doesn't care about customer addresses or KYC documents, only which account belongs to which customer ID.

## 15. Context Map

A document outlining all Bounded Contexts and their relationships/integration points.

**Why Needed:**
- Prevents boundary confusion
- Enables team coordination
- Manages integration explicitly
- Documents dependencies
- Supports architecture decisions
- Serves as an onboarding tool

## 16. Integration Patterns Between Contexts

**1. Shared Kernel**
- Two teams share subset of model
- Requires coordination, shared tests
- High coupling, high effort
- **Example:** Payment and Order contexts both share a common `Money` value object with identical implementation

**2. Customer-Supplier**
- Supplier produces, customer consumes
- Planning sessions, acceptance tests
- Medium coupling
- **Example:** Order context (supplier) provides order data to Fulfillment context (customer) via domain events

**3. Conformist**
- Downstream adopts upstream model completely
- No translation layer
- When supplier won't accommodate
- **Example:** Analytics context conforms to Order context's data structure since the Order team won't modify their model for reporting needs

**4. Anticorruption Layer**
- Translation layer protects downstream
- Use with legacy/external systems
- Low coupling, high effort
- Components: Service → Façade → Adapter → Translator
- **Example:** Modern Order context uses ACL to translate legacy mainframe's COBOL data structures into domain objects

**5. Separate Ways**
- No integration - complete independence
- When integration costs exceed benefits
- **Example:** Customer Support context manually enters order IDs rather than integrating with Order context because integration complexity outweighs benefits

**6. Open Host Service**
- Standard API for multiple clients
- One protocol vs. many translators
- **Example:** Product Catalog context exposes REST API that multiple contexts (Shopping, Reporting, Inventory) consume using the same interface

**7. Published Language**
- Common exchange format (XML, JSON schema)
- Often used with Open Host Service
- **Example:** All contexts exchange order information using a standardized JSON schema with agreed-upon field names and data types

## 17. Subdomain

**Definition:** A component of the main domain.

Each subdomain focuses on a specific subset of responsibilities and typically reflects some of the business's organizational structure (such as Warehouse, Sales, and Engineering).

**Key Characteristics:**
- **Problem space property:** Subdomains, like the domain itself, belong to the problem space
- **Domain in its own right:** A subdomain can be seen as a domain in its own right
- **Organizational alignment:** Often reflects the business's organizational structure
- **Focused responsibilities:** Each subdomain has a specific subset of responsibilities
- **Has its own model:** Can have its own domain model focused on its specific concerns

**Examples:**
- **E-commerce Domain** might have subdomains like:
  - Catalog Management
  - Order Processing
  - Inventory Management
  - Customer Service
  - Payment Processing

- **Banking Domain** might have subdomains like:
  - Account Management
  - Loan Processing
  - Risk Assessment
  - Compliance
  - Customer Relationship Management

**Relationship to Bounded Contexts:** While ideally a subdomain and a bounded context align perfectly, in practice they may differ due to legacy systems, technical debt, or third-party integrations.

## 18. Distillation Process

Distillation is the process of refining a large domain model to extract and highlight its essential core, separating it from supporting parts.

**What It Produces:**

The distillation process identifies and separates three distinct components:

1. **Core Domain** - The essence that provides unique business value and competitive advantage
2. **Supporting Subdomains** - Business-specific functionality required for operations but not core differentiators
3. **Generic Subdomains** - Necessary but not unique, well-understood problems with standard solutions

**Key Principle:** "Justify investment in any other part by how it supports the distilled Core."

## 19. Types of Domains

### Core Domain

**What is the Core Domain?**

The Core Domain represents the motivation for the project - it's what provides unique business value and competitive advantage. It's the reason the system exists and what differentiates your business from competitors.

**Key Principle:** Core domain = motivation for the project

**Characteristics:**
- Business differentiator and competitive advantage
- Proprietary, strategic, requires deep expertise
- **Investment:** Maximum - best developers, deep modeling
- **Can buy?** No
- Reduce the complexity of the model
- Focus on maintainability of this part of the system

**Example - Delivery Company:**

If a company's value proposition is "We delivery quickly and reliably", then:
- **Core domain** = Delivery Process (the differentiator)
- **Supporting subdomains** = Order Processing, Invoicing (necessary but not unique)
- **Generic subdomains** = Authentication, Payment Processing (commodity features)

**Key Processes in a Typical System:**
- Order Processing
- Invoicing
- Delivery Process

The core domain is determined by asking: "What is our competitive advantage?" In the delivery company example, the Delivery Process is core because that's what customers value most.

**Other Examples:**
- Netflix: Recommendation engine
- Trading firm: Trading algorithms
- Air traffic system: Collision detection

**Tests to Identify:**
- Competitive advantage? (differentiates us)
- Proprietary knowledge? (unique expertise)
- Strategic value? (mission-critical)
- Worth best developers? (justify investment)
- Is this the motivation for the project?

### Supporting Subdomain
- **What:** Business-specific but not core differentiator
- **Characteristics:** Required for operations, moderate complexity
- **Investment:** Moderate - pragmatic approach
- **Can buy?** Rarely - too specific but not strategic
- **Examples:** Custom workflows, regulatory compliance, integration adapters

**Tests to Identify:**
- Business-specific but not core?
- Can't easily buy? (too customized)
- Operational necessity? (required but not innovative)

### Generic Subdomain
- **What:** Necessary but not unique - commodity functionality
- **Characteristics:** Well-understood, universal, standard solutions exist
- **Investment:** Minimal - junior devs, off-the-shelf preferred
- **Can buy?** Yes - often should
- **Examples:** Authentication, email, payment processing, PDF generation, routing

**Tests to Identify:**
- Universal need? (every business needs this)
- Can buy? (off-the-shelf exists)
- Standard solution? (well-understood)
- Complexity without value? (necessary but not differentiating)

### Comparison Table

| Type | Value | Uniqueness | Investment | Can Buy |
|------|-------|------------|------------|---------|
| Core | Highest | Proprietary | Maximum | No |
| Supporting | Medium | Specific | Moderate | Rarely |
| Generic | Low | Universal | Minimal | Yes |
