# Online Photo Printing Service - Domain-Driven Design

## Problem Statement

Design an online photo printing service that allows users to upload photos, configure print products, place orders, process payments, and receive physical prints through delivery partners. The system must handle the complete lifecycle from digital photo upload to physical product delivery while maintaining quality standards and providing real-time tracking.

## Business Context

The online photo printing service operates in a competitive market where:
- Users expect seamless photo upload from multiple sources
- Print quality and delivery speed are critical differentiators
- Integration with third-party services (cloud storage, payment gateways, delivery partners) is essential
- Business needs visibility into operations for optimization

## Use Cases Summary

### Customer-Facing Capabilities
1. **User Registration & Authentication** - Social login, profile management
2. **Photo Management** - Upload, organize, edit photos from multiple sources
3. **Product Selection** - Browse and configure print products (prints, canvas, books, posters)
4. **Order Placement** - Add to cart, apply discounts, checkout
5. **Payment** - Multiple payment methods (cards, digital wallets)
6. **Order Tracking** - Real-time status updates from order to delivery
7. **Support** - Raise tickets, report issues, request reprints

### Business Operations
1. **Catalog Management** - Manage products, pricing, and attributes
2. **Production Fulfillment** - Send jobs to printing partners, track production
3. **Delivery Coordination** - Integrate with delivery providers
4. **Analytics & Reporting** - Order volumes, revenue, performance metrics
5. **Customer Support** - Issue resolution, refunds, reprints

---

## Domain-Driven Design Application

### Strategic Design

## Domain

**Definition:** Online Photo Printing and Delivery

The domain encompasses the entire ecosystem of converting digital photos into physical printed products and delivering them to customers. This includes customer management, photo processing, product configuration, order fulfillment, payment processing, logistics, and customer support.

---

## Subdomains

### 1. **Customer Management** (Supporting Subdomain)

**Type:** Supporting

**Justification:** Necessary for operations but not the core differentiator. Many businesses need customer management.

**Responsibilities:** User identity, profiles, authentication, preferences

### 2. **Photo Processing & Library** (Core Domain)

**Type:** Core

**Justification:** The ability to ingest, validate, and prepare photos for printing is central to the business value. Quality checks and format handling are proprietary.

**Responsibilities:** Photo upload, validation, storage, editing, quality assessment

### 3. **Product Catalog** (Supporting Subdomain)

**Type:** Supporting

**Justification:** Required for business operations but catalog management itself is not unique.

**Responsibilities:** Product definitions, pricing, print configurations

### 4. **Order Management** (Core Domain)

**Type:** Core

**Justification:** Converting customer intent into print orders with proper configuration is critical business logic.

**Responsibilities:** Cart, order placement, order lifecycle, status tracking

### 5. **Payment Processing** (Generic Subdomain)

**Type:** Generic

**Justification:** Standard capability available off-the-shelf through payment gateways.

**Responsibilities:** Payment authorization, capture, refunds

### 6. **Print Fulfillment** (Core Domain)

**Type:** Core

**Justification:** Managing print production, quality, and partner coordination is a key differentiator.

**Responsibilities:** Print job submission, production tracking, quality assurance, reprints

### 7. **Delivery & Logistics** (Supporting Subdomain)

**Type:** Supporting

**Justification:** Business-specific coordination but leverages third-party delivery services.

**Responsibilities:** Delivery scheduling, partner integration, tracking

### 8. **Notifications** (Generic Subdomain)

**Type:** Generic

**Justification:** Standard communication capability, can use off-the-shelf notification services.

**Responsibilities:** Email, SMS, push notifications

### 9. **Customer Support** (Supporting Subdomain)

**Type:** Supporting

**Justification:** Necessary for operations, handles business-specific issues but not core innovation.

**Responsibilities:** Ticket management, issue resolution, refunds/reprints

### 10. **Analytics & Insights** (Supporting Subdomain)

**Type:** Supporting

**Justification:** Business intelligence needed for optimization but not the primary value proposition.

**Responsibilities:** Reporting, metrics, business intelligence

---

## Bounded Contexts

### 1. **Customer Context**

**Alignment:** Customer Management Subdomain

**Ubiquitous Language:** User, Profile, Address, Preferences, Authentication

**Bounded Context Rationale:** Clear boundary around user identity and profile management. Minimal coupling with other contexts.

**Integration Points:**
- Publishes: `UserRegistered`, `UserAuthenticated`
- Consumed by: Ordering Context, Notifications Context

---

### 2. **Photo Library Context**

**Alignment:** Photo Processing & Library Subdomain (Core)

**Ubiquitous Language:** Photo, Album, Upload, Validation, Quality Check, Enhancement

**Bounded Context Rationale:** Highly cohesive photo management operations. Photo quality and processing rules are encapsulated here.

**Integration Points:**
- Publishes: `PhotoUploaded`, `PhotoValidated`, `PhotoEdited`
- Consumed by: Ordering Context, Fulfillment Context

---

### 3. **Catalog Context**

**Alignment:** Product Catalog Subdomain

**Ubiquitous Language:** Product, PrintOption, Size, PaperType, Finish, Price

**Bounded Context Rationale:** Product definitions and pricing rules are isolated. Changes to catalog don't affect order processing logic directly.

**Integration Points:**
- Publishes: `ProductPriceChanged`, `ProductAvailabilityChanged`
- Consumed by: Ordering Context

---

### 4. **Ordering Context**

**Alignment:** Order Management Subdomain (Core)

**Ubiquitous Language:** Cart, Order, LineItem, PrintConfiguration, OrderStatus, PromoCode

**Bounded Context Rationale:** Captures the core business logic of converting customer intent into orders. High cohesion around order lifecycle.

**Integration Points:**
- Publishes: `OrderPlaced`, `OrderCancelled`, `OrderStatusChanged`
- Consumes: `PhotoValidated`, `PaymentCaptured`, `PrintJobCompleted`

---

### 5. **Payment Context**

**Alignment:** Payment Processing Subdomain (Generic)

**Ubiquitous Language:** Payment, Authorization, Capture, Refund, PaymentMethod

**Bounded Context Rationale:** Isolated payment processing. Uses Anticorruption Layer to integrate with external payment gateways.

**Integration Points:**
- Publishes: `PaymentAuthorized`, `PaymentCaptured`, `PaymentFailed`, `RefundIssued`
- Consumed by: Ordering Context

---

### 6. **Fulfillment Context**

**Alignment:** Print Fulfillment Subdomain (Core)

**Ubiquitous Language:** PrintJob, PrintSpecification, PrintingPartner, ProductionStatus, Reprint

**Bounded Context Rationale:** Encapsulates print production logic and partner coordination. Critical quality and production rules live here.

**Integration Points:**
- Publishes: `PrintJobSubmitted`, `PrintJobCompleted`, `ReprintRequested`
- Consumes: `OrderPlaced`, `DeliveryCompleted`

---

### 7. **Delivery Context**

**Alignment:** Delivery & Logistics Subdomain

**Ubiquitous Language:** Delivery, DeliveryOption, Courier, TrackingInfo, DeliveryStatus

**Bounded Context Rationale:** Logistics coordination separated from fulfillment. Different team responsibilities and integration points.

**Integration Points:**
- Publishes: `DeliveryDispatched`, `DeliveryCompleted`, `DeliveryFailed`
- Consumes: `PrintJobCompleted`

---

### 8. **Engagement Context**

**Alignment:** Notifications Subdomain (Generic)

**Ubiquitous Language:** Notification, Channel, Template, Recipient

**Bounded Context Rationale:** Communication mechanisms isolated. Can evolve independently of business domains.

**Integration Points:**
- Consumes: All domain events that trigger notifications
- Uses: Open Host Service pattern for notification delivery

---

### 9. **Support Context**

**Alignment:** Customer Support Subdomain

**Ubiquitous Language:** Ticket, Issue, Resolution, Feedback, Agent

**Bounded Context Rationale:** Support operations have distinct workflows and terminology. Interfaces with multiple contexts for issue resolution.

**Integration Points:**
- Publishes: `TicketCreated`, `IssueResolved`
- Consumes: Events from Ordering, Fulfillment, Delivery contexts

---

### 10. **Insights Context**

**Alignment:** Analytics & Insights Subdomain

**Ubiquitous Language:** Metric, Report, Conversion, Revenue, Performance

**Bounded Context Rationale:** Read-model optimized for analytics. Separate from operational contexts.

**Integration Points:**
- Consumes: All business events for analytics
- Uses: Conformist pattern (accepts events as-is)

---

## Tactical Design: Domain Models by Bounded Context

### 1. Customer Context

#### Aggregates

**Customer Aggregate**
- **Root Entity:** `Customer`
  - `customerId` (identity)
  - `email`
  - `name`
  - `phoneNumber`
  - `socialAuthProvider`
- **Value Objects:**
  - `Address` (street, city, state, zipCode, country)
  - `CommunicationPreferences` (orderUpdates, promotions)
- **Invariants:**
  - Email must be unique
  - At least one delivery address required for placing orders
  - Cannot delete address if used in pending orders

**Design Rationale:** Customer is the aggregate root as it controls all personal data. Address is a Value Object (no identity needed, fully defined by attributes). Changes to customer profile are atomic.

#### Domain Events
- `UserRegistered`
- `UserAuthenticated`
- `UserProfileUpdated`
- `DeliveryAddressAdded`

#### Services
**AuthenticationService** - Stateless service coordinating with external auth providers (Google, Apple, Facebook)

#### Repositories
**CustomerRepository** - Persist and retrieve Customer aggregates

---

### 2. Photo Library Context

#### Aggregates

**Photo Aggregate**
- **Root Entity:** `Photo`
  - `photoId` (identity)
  - `userId` (owner reference)
  - `uploadSource` (device, cloud)
  - `format` (JPEG, PNG, HEIC)
  - `uploadDate`
  - `qualityStatus` (approved, warning, rejected)
- **Value Objects:**
  - `ImageMetadata` (width, height, dpi, fileSize)
  - `EditHistory` (crops, rotations, enhancements applied)
- **Invariants:**
  - Photo must pass format validation
  - Quality warnings must be acknowledged before printing
  - Cannot delete photo if referenced in active orders

**Album Aggregate**
- **Root Entity:** `Album`
  - `albumId` (identity)
  - `userId` (owner reference)
  - `name`
  - `photoReferences` (list of photoIds)
- **Invariants:**
  - Album name must be unique per user
  - Cannot add photos from different users

**Design Rationale:** Photo and Album are separate aggregates - they have independent lifecycles. Photo changes don't require loading the entire album. Photo quality validation is encapsulated within the aggregate.

#### Domain Events
- `PhotoUploaded`
- `PhotoValidated`
- `PhotoQualityWarningIssued`
- `PhotoEdited`
- `PhotoDeleted`
- `AlbumCreated`

#### Services
- **PhotoValidationService** - Validates format, resolution, DPI
- **CloudImportService** - Coordinates import from Google Photos, iCloud, Dropbox

#### Repositories
- **PhotoRepository**
- **AlbumRepository**

---

### 3. Catalog Context

#### Aggregates

**Product Aggregate**
- **Root Entity:** `Product`
  - `productId` (identity)
  - `name`
  - `type` (print, canvas, book, poster)
  - `isActive`
- **Entities (within aggregate):**
  - `ProductVariant`
    - `variantId` (local identity)
    - `size`
    - `paperType`
    - `finish`
    - `basePrice`
    - `productionTime`
- **Value Objects:**
  - `PriceRule` (quantity, discount)
  - `SizeSpecification` (width, height, unit)
- **Invariants:**
  - Must have at least one active variant
  - Variant sizes must be valid for product type
  - Base price must be positive

**Design Rationale:** Product is the aggregate root containing variants. Variants don't exist independently. Price calculations are based on variant and quantity, encapsulated within the aggregate.

#### Domain Events
- `ProductCreated`
- `ProductPriceChanged`
- `ProductAvailabilityChanged`

#### Services
**PricingService** - Calculates final price including discounts and promo codes

#### Repositories
**ProductRepository**

---

### 4. Ordering Context

#### Aggregates

**Cart Aggregate**
- **Root Entity:** `Cart`
  - `cartId` (identity)
  - `userId`
  - `createdAt`
  - `expiresAt`
- **Entities (within aggregate):**
  - `CartItem`
    - `cartItemId` (local identity)
    - `photoId` (reference)
    - `productVariantId` (reference)
    - `quantity`
    - `unitPrice`
- **Value Objects:**
  - `PromoCode` (code, discountPercentage, expiryDate)
- **Invariants:**
  - Cart items must reference valid photos and product variants
  - Quantity must be positive
  - Cannot checkout with expired cart

**Order Aggregate**
- **Root Entity:** `Order`
  - `orderId` (identity)
  - `userId` (reference)
  - `orderDate`
  - `status` (placed, paid, production, shipped, delivered, cancelled)
  - `totalAmount`
- **Entities (within aggregate):**
  - `OrderLine`
    - `orderLineId` (local identity)
    - `photoId` (reference)
    - `productVariantId` (reference)
    - `quantity`
    - `unitPrice`
    - `subtotal`
- **Value Objects:**
  - `ShippingAddress` (from Customer context, copied)
  - `OrderSummary` (subtotal, discount, tax, total)
- **Invariants:**
  - Order lines cannot be modified after payment
  - Cannot cancel order once production starts
  - Total must match sum of line items

**Design Rationale:** Cart and Order are separate aggregates with different lifecycles. Cart is mutable; Order becomes immutable after payment. Order holds a snapshot of shipping address (not a reference) to preserve historical data even if customer changes address later.

#### Domain Events
- `ItemAddedToCart`
- `CartCheckedOut`
- `OrderPlaced`
- `OrderPaid`
- `OrderCancelled`
- `OrderStatusChanged`

#### Services
**OrderPlacementService** - Coordinates cart conversion to order, validates inventory, applies pricing

#### Repositories
- **CartRepository**
- **OrderRepository**

---

### 5. Payment Context

#### Aggregates

**Payment Aggregate**
- **Root Entity:** `Payment`
  - `paymentId` (identity)
  - `orderId` (reference)
  - `amount`
  - `status` (pending, authorized, captured, failed, refunded)
  - `paymentMethod`
  - `gatewayTransactionId`
- **Value Objects:**
  - `PaymentToken` (tokenized card info)
  - `RefundDetails` (amount, reason, date)
- **Invariants:**
  - Cannot capture payment before authorization
  - Refund amount cannot exceed captured amount
  - Failed payments cannot be retried without new authorization

**Design Rationale:** Payment is the aggregate root managing the payment lifecycle. Payment tokens are Value Objects (immutable). Payment state transitions are strictly controlled through the aggregate.

#### Domain Events
- `PaymentAuthorized`
- `PaymentCaptured`
- `PaymentFailed`
- `RefundIssued`

#### Services
**PaymentGatewayService** - Anticorruption Layer wrapping external payment providers (Stripe, PayPal)

#### Repositories
**PaymentRepository**

---

### 6. Fulfillment Context

#### Aggregates

**PrintJob Aggregate**
- **Root Entity:** `PrintJob`
  - `printJobId` (identity)
  - `orderId` (reference)
  - `orderLineId` (reference)
  - `status` (queued, submitted, accepted, inProduction, completed, failed)
  - `assignedPartner`
  - `submittedAt`
  - `completedAt`
- **Value Objects:**
  - `PrintSpecification` (photoId, size, paperType, finish, quantity)
  - `QualityCheckResult` (passed, defects, notes)
  - `ReprintReason` (defect, customerRequest, productionError)
- **Invariants:**
  - Cannot submit job without valid print specification
  - Must have quality check before completion
  - Reprints require reason and reference to original job

**Design Rationale:** Each order line becomes a separate PrintJob for tracking. Print specifications are Value Objects (immutable once job starts). Quality checks are part of the completion process.

#### Domain Events
- `PrintJobSubmitted`
- `PrintJobAccepted`
- `PrintJobCompleted`
- `PrintJobFailed`
- `ReprintRequested`

#### Services
- **PrintPartnerService** - Coordinates with external printing partners
- **QualityAssuranceService** - Validates print quality before dispatch

#### Repositories
**PrintJobRepository**

---

### 7. Delivery Context

#### Aggregates

**Delivery Aggregate**
- **Root Entity:** `Delivery`
  - `deliveryId` (identity)
  - `orderId` (reference)
  - `status` (scheduled, dispatched, inTransit, delivered, failed)
  - `courierPartner`
  - `trackingNumber`
  - `scheduledDate`
  - `deliveredAt`
- **Value Objects:**
  - `DeliveryAddress` (copied from order)
  - `DeliveryOption` (standard, express, estimatedDays, cost)
  - `DeliveryAttempt` (attemptDate, status, notes)
- **Invariants:**
  - Cannot dispatch without valid tracking number
  - Delivery attempts must be recorded
  - Cannot mark delivered without confirmation

**Design Rationale:** Delivery aggregate manages the logistics lifecycle. Address is copied (Value Object) to preserve snapshot. Delivery attempts track retry history.

#### Domain Events
- `DeliveryScheduled`
- `DeliveryDispatched`
- `DeliveryInTransit`
- `DeliveryCompleted`
- `DeliveryFailed`

#### Services
**CourierIntegrationService** - Anticorruption Layer for third-party delivery APIs (Uber Eats, DoorDash)

#### Repositories
**DeliveryRepository**

---

### 8. Support Context

#### Aggregates

**SupportTicket Aggregate**
- **Root Entity:** `SupportTicket`
  - `ticketId` (identity)
  - `userId` (reference)
  - `orderId` (optional reference)
  - `status` (open, inProgress, resolved, closed)
  - `priority` (low, medium, high)
  - `category` (printQuality, delivery, refund, general)
  - `createdAt`
  - `resolvedAt`
- **Entities (within aggregate):**
  - `TicketInteraction`
    - `interactionId` (local identity)
    - `agentId` (optional)
    - `message`
    - `timestamp`
    - `attachments` (photos of defects)
- **Value Objects:**
  - `Resolution` (type, outcome, notes)
- **Invariants:**
  - Cannot close without resolution
  - Must have at least one interaction
  - Resolution type must match category

**Design Rationale:** Ticket is the aggregate root containing interaction history. Interactions are entities with local identity. Resolution details are captured as Value Object.

#### Domain Events
- `SupportTicketCreated`
- `TicketAssignedToAgent`
- `IssueResolved`
- `CustomerFeedbackSubmitted`

#### Services
**IssueResolutionService** - Coordinates refunds/reprints with Payment and Fulfillment contexts

#### Repositories
**SupportTicketRepository**

---

## Context Map

### Integration Patterns

1. **Customer Context → Ordering Context**
   - **Pattern:** Customer-Supplier
   - **Integration:** Ordering context consumes `UserRegistered` event, stores userId reference
   - **Rationale:** Ordering depends on Customer but they evolve independently

2. **Photo Library Context → Ordering Context**
   - **Pattern:** Customer-Supplier
   - **Integration:** Ordering validates photo references, consumes `PhotoValidated`
   - **Rationale:** Orders can only include validated photos

3. **Catalog Context → Ordering Context**
   - **Pattern:** Customer-Supplier
   - **Integration:** Ordering retrieves current pricing, consumes `ProductPriceChanged`
   - **Rationale:** Catalog changes affect order pricing

4. **Ordering Context → Payment Context**
   - **Pattern:** Customer-Supplier
   - **Integration:** Payment processes order payments, publishes `PaymentCaptured`
   - **Rationale:** Payment is triggered by order placement

5. **Payment Context → External Gateway**
   - **Pattern:** Anticorruption Layer
   - **Integration:** PaymentGatewayService wraps Stripe/PayPal APIs
   - **Rationale:** Protect domain from external payment provider changes

6. **Ordering Context → Fulfillment Context**
   - **Pattern:** Customer-Supplier
   - **Integration:** Fulfillment creates print jobs from orders after payment
   - **Rationale:** Production starts only after successful payment

7. **Fulfillment Context → Delivery Context**
   - **Pattern:** Customer-Supplier
   - **Integration:** Delivery scheduled after print job completion
   - **Rationale:** Sequential workflow - print then deliver

8. **Fulfillment Context → External Printing Partners**
   - **Pattern:** Anticorruption Layer
   - **Integration:** PrintPartnerService wraps partner-specific APIs
   - **Rationale:** Multiple printing partners with different protocols

9. **Delivery Context → External Couriers**
   - **Pattern:** Anticorruption Layer
   - **Integration:** CourierIntegrationService wraps courier APIs
   - **Rationale:** Protect from courier provider changes

10. **All Contexts → Engagement Context**
    - **Pattern:** Published Language (Events)
    - **Integration:** Engagement subscribes to domain events, sends notifications
    - **Rationale:** Notifications are cross-cutting concern

11. **All Contexts → Insights Context**
    - **Pattern:** Conformist
    - **Integration:** Insights consumes all events for analytics
    - **Rationale:** Analytics adapts to domain events, no impact on source contexts

12. **Support Context → Multiple Contexts**
    - **Pattern:** Customer-Supplier (multiple)
    - **Integration:** Support queries Order, Fulfillment, Delivery for issue resolution
    - **Rationale:** Support needs read access across contexts for troubleshooting

### Visual Context Map

For a comprehensive visual representation of all bounded contexts, their domain objects, relationships, and integration patterns, see the [Visual Context Map](context_map.md).

---

## Key Design Decisions & Rationale

### 1. **Separate Photo Library from Ordering**
**Decision:** Photo management is its own bounded context
**Rationale:**
- Photos have lifecycle independent of orders
- Photo quality validation logic is complex and core to business
- Users can upload and manage photos without placing orders
- Allows photo processing to scale independently

### 2. **Order and Cart as Separate Aggregates**
**Decision:** Cart and Order are different aggregates
**Rationale:**
- Different lifecycles - Cart is temporary and mutable, Order is permanent
- Different invariants - Cart allows modification, Order becomes immutable
- Different access patterns - Cart is frequently updated, Order is append-only

### 3. **PrintJob per OrderLine**
**Decision:** Each order line becomes a separate PrintJob
**Rationale:**
- Different line items may be assigned to different printing partners
- Allows independent tracking and retry of failed print jobs
- Quality checks happen at individual print level, not order level
- Enables parallel production

### 4. **Copied vs Referenced Addresses**
**Decision:** Order and Delivery copy address values instead of referencing Customer
**Rationale:**
- Preserves historical data - even if customer changes address, order history remains accurate
- Reduces coupling - changes to customer profile don't affect past orders
- Addresses are Value Objects - no identity, immutable once set

### 5. **Anticorruption Layers for External Services**
**Decision:** Use ACL pattern for payments, printing partners, and couriers
**Rationale:**
- External APIs are outside our control and may change
- Domain model should not be polluted by external provider concepts
- Allows switching providers without changing domain logic
- Translates provider-specific errors to domain-meaningful exceptions

### 6. **Event-Driven Communication Between Contexts**
**Decision:** Contexts communicate primarily through domain events
**Rationale:**
- Loose coupling - contexts don't need to know about each other directly
- Scalability - events can be queued and processed asynchronously
- Audit trail - events provide complete business history
- Multiple consumers - same event can trigger fulfillment, notifications, analytics

### 7. **Core vs Supporting vs Generic Classification**
**Decision:** Photo Library, Ordering, and Fulfillment are Core; Others are Supporting/Generic
**Rationale:**
- Core domains receive maximum investment, best developers
- Photo quality and print production are competitive differentiators
- Customer management, catalog, notifications are necessary but not unique
- Payment and basic notifications can use off-the-shelf solutions

---

## Conclusion

This DDD design for the online photo printing service:

1. **Separates concerns** through well-defined bounded contexts
2. **Identifies core business logic** (photo processing, ordering, fulfillment) for maximum investment
3. **Uses appropriate integration patterns** (ACL for external systems, events for internal communication)
4. **Maintains aggregate boundaries** for transactional consistency
5. **Preserves historical data** through value object snapshots
6. **Enables independent scaling** of different business capabilities
7. **Protects domain model** from external service changes through anticorruption layers

The design allows each bounded context to evolve independently while maintaining clear contracts through domain events and well-defined interfaces.
