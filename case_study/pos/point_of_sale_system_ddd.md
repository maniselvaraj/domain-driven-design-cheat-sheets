# Point of Sale System - Domain-Driven Design

## Problem Statement

Design a comprehensive Point of Sale (POS) system that enables retail businesses to process customer transactions efficiently, manage inventory in real-time, handle payments securely, and maintain compliance with fiscal regulations. The system must support high-volume transaction processing, integrate with multiple payment gateways and external services, provide real-time inventory updates, and generate comprehensive reports for business intelligence. The solution must scale from single-store operations to multi-location enterprises while maintaining performance, security, and regulatory compliance.

## Business Context

The Point of Sale system operates in a highly competitive retail technology market where:
- **Speed and Reliability** are critical - every second at checkout impacts customer satisfaction and revenue
- **Payment Security** (PCI DSS compliance) is mandatory - any breach can destroy business reputation
- **Fiscal Compliance** varies by region (e.g., Rwanda's Electronic Billing Machine requirements, EBM)
- **Integration Complexity** is high - must connect with payment processors, inventory systems, accounting software, CRM platforms, and delivery services
- **Scalability Requirements** differ - from small businesses to large retail chains with multiple locations
- **Real-time Data Synchronization** is essential - inventory levels, pricing, and customer data must be consistent across all channels
- **Business Intelligence** drives competitive advantage - detailed analytics on sales patterns, inventory trends, and customer behavior
- **Multi-Channel Operations** are standard - in-store, online, mobile, and kiosk transactions need unified processing

## Use Cases Summary

### Customer-Facing Capabilities
1. **Product Scanning & Lookup** - Barcode scanning, manual search, product catalog browsing
2. **Price Calculation** - Real-time pricing with discounts, promotions, and tax calculations
3. **Cart Management** - Add/remove items, quantity adjustments, apply promo codes
4. **Payment Processing** - Multiple payment methods (cards, digital wallets, cash, gift cards, split payments)
5. **Receipt Generation** - Digital and printed receipts with QR codes for verification
6. **Customer Loyalty** - Points accumulation, rewards redemption, tier-based discounts
7. **Returns & Exchanges** - Process refunds, store credits, product exchanges
8. **Self-Checkout** - Customer-operated terminals with assistance features

### Business Operations
1. **Inventory Management** - Real-time stock tracking, low-stock alerts, automated reordering
2. **Purchase Order Management** - Supplier orders, receiving goods, invoice reconciliation
3. **Catalog Management** - Product CRUD, pricing rules, variant management, categorization
4. **Employee Management** - Clock in/out, shift tracking, commission calculations, role-based access
5. **Sales Reporting** - Daily sales summaries, product performance, revenue analytics
6. **Fiscal Compliance** - Electronic invoicing, digital signatures, tax authority data transmission (EBM)
7. **Multi-Store Coordination** - Centralized catalog, distributed inventory, consolidated reporting
8. **CRM Integration** - Customer profiles, purchase history, marketing campaigns

### Backend Processes
1. **Transaction Coordination** - Distributed transaction management across payment, inventory, and fiscal services
2. **Event Processing** - Asynchronous event handling for inventory updates, notifications, and analytics
3. **Data Synchronization** - Real-time sync between stores, channels, and external systems
4. **Audit Logging** - Comprehensive transaction logs for compliance and troubleshooting
5. **System Monitoring** - Health checks, performance metrics, anomaly detection

---

## High Level Process Output

### Phase 2: Strategic Design Outputs

| Output | Description |
|--------|-------------|
| **Domain** | Point of Sale and Retail Operations |
| **Subdomains (9)** | • **Core:** Transaction Processing & Sales<br>Inventory Management<br>Product Catalog & Pricing<br>• **Supporting:** Fiscal Compliance & Invoicing<br>Customer Management<br>Purchasing & Supplier Management<br>Analytics & Reporting<br>• **Generic:** Payment Processing<br>Notifications & Alerts |
| **Bounded Contexts (11)** | • POS Console<br>• Transaction<br>• Inventory<br>• Catalog<br>• Payment<br>• Fiscal Compliance<br>• Customer<br>• Purchasing<br>• Analytics<br>• Account Management<br>• Notification |
| **Ubiquitous Language** | Defined per context:<br>• Sale, LineItem, Tender, Receipt<br>• Stock, SKU, Availability, Reorder<br>• Product, Variant, PriceRule, Discount<br>• FiscalInvoice, DigitalSignature, EBM |

### Subdomain → Bounded Context Mapping

| Subdomain | Type | Bounded Contexts |
|-----------|------|------------------|
| Transaction Processing & Sales | Core | POS Console, Transaction |
| Inventory Management | Core | Inventory |
| Product Catalog & Pricing | Core | Catalog |
| Fiscal Compliance & Invoicing | Supporting | Fiscal Compliance |
| Customer Management | Supporting | Customer |
| Purchasing & Supplier Management | Supporting | Purchasing |
| Analytics & Reporting | Supporting | Analytics |
| Payment Processing | Generic | Payment |
| Authentication & Authorization | Generic | Account Management |
| Notifications & Alerts | Generic | Notification |

### Phase 3: Tactical Design Outputs

| Bounded Context | Aggregates | Key Entities | Key Value Objects | Domain Events |
|-----------------|------------|--------------|-------------------|---------------|
| **POS Console** | Sale | Sale, SaleLineItem | Tender, DiscountApplication | SaleStarted, ItemScanned, SaleCompleted |
| **Transaction** | Transaction | Transaction, TransactionLine | TransactionSummary, PaymentDetails | TransactionCreated, TransactionCommitted |
| **Inventory** | InventoryItem, StockLevel | InventoryItem, StockMovement | SKU, Quantity, Location | StockUpdated, LowStockDetected |
| **Catalog** | Product | Product, ProductVariant | PriceRule, Category, Attribute | PriceChanged, ProductActivated |
| **Payment** | Payment | Payment, PaymentAuthorization | PaymentMethod, PaymentToken | PaymentAuthorized, PaymentCaptured |
| **Fiscal Compliance** | FiscalInvoice | FiscalInvoice, InvoiceLineItem | DigitalSignature, QRCode | InvoiceSigned, InvoiceTransmitted |
| **Customer** | Customer | Customer, LoyaltyAccount | Address, ContactInfo | CustomerRegistered, PointsEarned |
| **Purchasing** | PurchaseOrder | PurchaseOrder, OrderLine, Supplier | DeliverySchedule, PaymentTerms | OrderPlaced, GoodsReceived |

### Phase 4: Integration Outputs

| Integration | Pattern | Direction |
|-------------|---------|-----------|
| POS Console → Transaction | Customer-Supplier | Events: SaleCompleted → TransactionCreated |
| Transaction → Inventory | Customer-Supplier | Events: TransactionCommitted → StockUpdated |
| Transaction → Payment | Customer-Supplier | Events: TransactionCreated → PaymentAuthorized |
| Transaction → Fiscal Compliance | Customer-Supplier | Events: TransactionCommitted → InvoiceSigned |
| Catalog → POS Console | Customer-Supplier | Events: PriceChanged |
| Inventory → POS Console | Customer-Supplier | Events: StockUpdated |
| Customer → POS Console | Customer-Supplier | Events: CustomerIdentified |
| Payment → External Gateway | Anticorruption Layer | Stripe, PayPal, Square APIs |
| Fiscal Compliance → Tax Authority | Anticorruption Layer | EBM/EFDMS APIs |
| Purchasing → Supplier Systems | Anticorruption Layer | Supplier-specific APIs |
| All Contexts → Notification | Published Language | Domain events → Notifications |
| All Contexts → Analytics | Conformist | Domain events → Reporting |

---

## Domain-Driven Design Application

### Strategic Design

## Domain

**Definition:** Point of Sale and Retail Operations

The domain encompasses the complete ecosystem of retail transaction processing, from customer checkout experience through payment processing, inventory management, fiscal compliance, and business analytics. This includes real-time product catalog management, multi-channel sales coordination, supplier purchasing, customer relationship management, and regulatory compliance with tax authorities.

---

## Subdomains

### 1. **Transaction Processing & Sales** (Core Domain)

**Type:** Core

**Justification:** This is THE competitive differentiator - the speed, reliability, and user experience of the checkout process directly impacts customer satisfaction, revenue, and operational efficiency. The ability to process transactions quickly, handle complex pricing rules, manage split payments, and provide seamless returns is what separates successful POS systems from failures. This is the heart of the business value proposition.

**Responsibilities:**
- Real-time sale processing with barcode scanning and manual entry
- Complex pricing calculation with discounts, taxes, and promotions
- Multi-tender payment coordination (cash, cards, digital wallets, gift cards)
- Transaction validation and business rule enforcement
- Receipt generation and customer notification
- Returns, exchanges, and void transaction handling

**Why Core:** The checkout experience is the moment of truth in retail. A slow, error-prone, or complicated POS directly loses sales and frustrates customers. The sophistication of transaction orchestration, pricing logic, and payment handling is a primary competitive advantage.

### 2. **Inventory Management** (Core Domain)

**Type:** Core

**Justification:** Real-time inventory accuracy is a critical business differentiator. The ability to show accurate stock levels instantly, prevent overselling, automatically trigger reorders, and track inventory across multiple locations and channels is essential for retail success. Inventory errors lead to lost sales, stockouts, and overstocking costs.

**Responsibilities:**
- Real-time stock level tracking with multi-location support
- Automatic inventory updates on sales, returns, and stock movements
- Low-stock detection and automated reorder point management
- Stock transfer between locations
- Inventory reconciliation and cycle counting
- Item availability lookup with millisecond response times

**Why Core:** Inventory is the lifeblood of retail. Real-time accuracy prevents stockouts (lost sales) and overstocking (tied-up capital). The sophistication of inventory prediction, multi-location optimization, and real-time sync across channels is a major competitive advantage.

### 3. **Product Catalog & Pricing** (Core Domain)

**Type:** Core

**Justification:** The product catalog and dynamic pricing engine are core to business operations. The ability to manage complex product hierarchies, variants, bulk pricing rules, time-based promotions, customer-specific pricing, and margin optimization directly impacts profitability and competitive positioning.

**Responsibilities:**
- Product and variant management (size, color, SKU)
- Dynamic pricing rules and promotional pricing
- Category and taxonomy management
- Product attribute and specification management
- Price synchronization across channels
- Margin calculation and profitability tracking

**Why Core:** Pricing strategy and product organization are fundamental to retail competitiveness. The sophistication of pricing rules (volume discounts, time-based promotions, customer segments) and catalog management directly impacts revenue and margins.

### 4. **Fiscal Compliance & Invoicing** (Supporting Subdomain)

**Type:** Supporting

**Justification:** While critical for regulatory compliance (especially in regions like Rwanda with EBM requirements), fiscal compliance itself is not a competitive differentiator - it's a necessary requirement. However, it requires business-specific implementation to match local tax regulations and cannot be purchased off-the-shelf without customization.

**Responsibilities:**
- Electronic invoice generation with digital signatures
- Integration with tax authority systems (Electronic Fiscal Data Management System)
- QR code generation for invoice verification
- Audit data preservation and secure storage
- Real-time fiscal data transmission to government systems
- Tax calculation and reporting

### 5. **Customer Management** (Supporting Subdomain)

**Type:** Supporting

**Justification:** Customer relationship management is important for business operations (loyalty programs, purchase history, personalized marketing) but not the primary competitive differentiator. Many retail businesses need CRM capabilities, but the core value is in the transaction processing itself.

**Responsibilities:**
- Customer profile and contact information management
- Loyalty program administration (points, tiers, rewards)
- Purchase history tracking
- Customer segmentation and analytics
- Preference management
- Marketing campaign integration

### 6. **Purchasing & Supplier Management** (Supporting Subdomain)

**Type:** Supporting

**Justification:** Necessary for business operations but not the core innovation. Purchase order management is important for inventory replenishment but is a well-understood problem without significant competitive differentiation.

**Responsibilities:**
- Purchase order creation and management
- Supplier relationship management
- Goods receiving and invoice reconciliation
- Payment term management
- Supplier performance tracking
- Inventory reorder automation

### 7. **Analytics & Reporting** (Supporting Subdomain)

**Type:** Supporting

**Justification:** Business intelligence is valuable for decision-making but is a supporting capability. Reporting on sales, inventory, and customer behavior supports business optimization but isn't the primary value proposition of a POS system.

**Responsibilities:**
- Sales performance reporting (daily, weekly, monthly)
- Inventory turnover and valuation reports
- Customer behavior analytics
- Employee performance tracking
- Financial reconciliation reports
- Trend analysis and forecasting

### 8. **Payment Processing** (Generic Subdomain)

**Type:** Generic

**Justification:** Payment processing is a solved problem available through third-party payment gateways (Stripe, PayPal, Square, etc.). While critical for operations, this is a commodity service that should be purchased, not built. PCI DSS compliance requirements make it even more important to use established providers.

**Responsibilities:**
- Payment authorization and capture
- Multiple payment method support
- Tokenization and PCI compliance
- Refund processing
- Payment gateway integration
- Fraud detection

### 9. **Notifications & Alerts** (Generic Subdomain)

**Type:** Generic

**Justification:** Email, SMS, and push notifications are commodity services available through third-party providers (Twilio, SendGrid, etc.). While useful for customer engagement and operational alerts, this is not business-specific functionality.

**Responsibilities:**
- Email and SMS notifications
- Push notification delivery
- Alert routing and escalation
- Notification template management
- Delivery tracking and retry logic

---

## Bounded Contexts

### 1. **POS Console Context**

**Alignment:** Transaction Processing & Sales Subdomain (Core)

**Ubiquitous Language:** Sale, LineItem, Tender, Cashier, Receipt, Void, Return, Refund, Barcode, Session

**Bounded Context Rationale:** The POS Console represents the user-facing checkout experience and the point of sale transaction initiation. This is separated from the transaction processing engine because it handles presentation logic, user interactions, hardware integration (barcode scanners, receipt printers, cash drawers), and session management - concerns distinct from transactional consistency and payment processing.

**Integration Points:**
- Publishes: `SaleStarted`, `ItemScanned`, `ItemRemoved`, `SaleCompleted`, `SaleCancelled`, `ReturnInitiated`
- Consumes: `ProductPriceChanged`, `StockUpdated`, `CustomerIdentified`, `LoyaltyPointsCalculated`
- Consumed by: Transaction Context, Analytics Context

---

### 2. **Transaction Context**

**Alignment:** Transaction Processing & Sales Subdomain (Core)

**Ubiquitous Language:** Transaction, TransactionLine, Commit, Rollback, TransactionState, PaymentAllocation, TaxCalculation, DiscountApplication

**Bounded Context Rationale:** The Transaction Context is the core business logic for processing sales transactions. It orchestrates the entire transaction lifecycle, coordinates with Payment, Inventory, and Fiscal Compliance contexts, and enforces business invariants (transaction totals must balance, payments must be authorized before completion, inventory must be available). This is separated from the POS Console to isolate transactional consistency concerns and enable headless commerce scenarios (online, mobile, kiosk).

**Integration Points:**
- Publishes: `TransactionCreated`, `TransactionCommitted`, `TransactionFailed`, `TransactionVoided`, `RefundProcessed`
- Consumes: `SaleCompleted`, `PaymentAuthorized`, `PaymentCaptured`, `StockReserved`, `InvoiceSigned`
- Consumed by: Payment Context, Inventory Context, Fiscal Compliance Context, Analytics Context

---

### 3. **Inventory Context**

**Alignment:** Inventory Management Subdomain (Core)

**Ubiquitous Language:** Stock, SKU, Quantity, Location, Availability, StockMovement, Reorder, Transfer, Adjustment, Shrinkage

**Bounded Context Rationale:** Inventory management has distinct business rules around stock levels, multi-location tracking, and availability calculations. It has a different lifecycle from transactions (inventory exists before and after sales) and different consistency requirements (eventual consistency for cross-location sync is acceptable). Separating this context allows independent scaling of inventory queries (high read volume) from inventory updates (lower write volume).

**Integration Points:**
- Publishes: `StockUpdated`, `LowStockDetected`, `StockTransferred`, `InventoryAdjusted`, `ItemReceived`
- Consumes: `TransactionCommitted`, `TransactionVoided`, `GoodsReceived`, `StockTransferRequested`
- Consumed by: POS Console Context, Catalog Context, Purchasing Context, Analytics Context

---

### 4. **Catalog Context**

**Alignment:** Product Catalog & Pricing Subdomain (Core)

**Ubiquitous Language:** Product, Variant, SKU, Category, Attribute, Price, PriceRule, Promotion, Discount, Margin

**Bounded Context Rationale:** Product catalog and pricing have complex business rules around variant management, hierarchical categorization, time-based promotions, and customer-specific pricing. These rules evolve independently from transaction processing. Separating the catalog allows merchandising teams to manage products without impacting transaction processing, and enables catalog changes to propagate asynchronously to other contexts.

**Integration Points:**
- Publishes: `ProductCreated`, `PriceChanged`, `ProductActivated`, `ProductDeactivated`, `PromotionStarted`, `PromotionEnded`
- Consumes: (minimal - largely autonomous)
- Consumed by: POS Console Context, Transaction Context, Purchasing Context, Analytics Context

---

### 5. **Payment Context**

**Alignment:** Payment Processing Subdomain (Generic)

**Ubiquitous Language:** Payment, Authorization, Capture, Settlement, Refund, PaymentMethod, PaymentToken, Gateway, Tender

**Bounded Context Rationale:** Payment processing is isolated to protect the domain from external payment gateway specifics and manage PCI DSS compliance boundaries. Payment has strict state machine rules (authorize before capture, capture before settlement) and integrates with multiple external providers through an Anticorruption Layer. Isolating payments enables swapping payment providers without impacting core transaction logic.

**Integration Points:**
- Publishes: `PaymentAuthorized`, `PaymentCaptured`, `PaymentFailed`, `PaymentVoided`, `RefundIssued`, `RefundCompleted`
- Consumes: `TransactionCreated`, `RefundRequested`
- Consumed by: Transaction Context, Fiscal Compliance Context, Analytics Context

---

### 6. **Fiscal Compliance Context**

**Alignment:** Fiscal Compliance & Invoicing Subdomain (Supporting)

**Ubiquitous Language:** FiscalInvoice, DigitalSignature, QRCode, TaxAuthority, EBM, EFDMS, AuditData, InvoiceSequence, TaxRate

**Bounded Context Rationale:** Fiscal compliance rules are country and region-specific, requiring isolation from core transaction processing. This context handles the specialized logic for electronic invoicing, digital signatures (using secure elements), QR code generation for verification, and integration with government tax authority systems. Separating this allows the core transaction logic to remain clean and enables supporting multiple fiscal regimes without complexity in the core domain.

**Integration Points:**
- Publishes: `InvoiceSigned`, `InvoiceTransmitted`, `InvoiceVerified`, `AuditDataArchived`
- Consumes: `TransactionCommitted`, `PaymentCaptured`
- Consumed by: Analytics Context

---

### 7. **Customer Context**

**Alignment:** Customer Management Subdomain (Supporting)

**Ubiquitous Language:** Customer, LoyaltyAccount, Points, Tier, Reward, Redemption, Preference, PurchaseHistory

**Bounded Context Rationale:** Customer relationship management has a lifecycle independent of individual transactions. Customers exist before and after purchases, have evolving preferences and loyalty status, and may interact through multiple channels. Isolating customer management allows CRM features to evolve independently and enables personalization across all customer touchpoints (not just POS).

**Integration Points:**
- Publishes: `CustomerRegistered`, `CustomerUpdated`, `PointsEarned`, `PointsRedeemed`, `TierUpgraded`, `RewardIssued`
- Consumes: `TransactionCommitted`, `SaleCompleted`
- Consumed by: POS Console Context, Transaction Context, Analytics Context

---

### 8. **Purchasing Context**

**Alignment:** Purchasing & Supplier Management Subdomain (Supporting)

**Ubiquitous Language:** PurchaseOrder, OrderLine, Supplier, DeliverySchedule, Invoice, GoodsReceipt, PaymentTerms, Reconciliation

**Bounded Context Rationale:** Purchasing and supplier management has distinct workflows from customer-facing sales. Purchase orders have approval workflows, payment terms, delivery schedules, and invoice reconciliation - all separate from sales transaction processing. This context manages the supply side of the business, while Transaction/POS Console manage the demand side.

**Integration Points:**
- Publishes: `OrderPlaced`, `OrderApproved`, `GoodsReceived`, `InvoiceReconciled`, `SupplierPaymentScheduled`
- Consumes: `LowStockDetected`, `ReorderTriggered`
- Consumed by: Inventory Context, Analytics Context

---

### 9. **Analytics Context**

**Alignment:** Analytics & Reporting Subdomain (Supporting)

**Ubiquitous Language:** Report, Metric, KPI, Trend, Forecast, Dashboard, Aggregation, TimeSeries

**Bounded Context Rationale:** Analytics is a read-model optimized for reporting and business intelligence. It consumes events from all other contexts but does not influence their behavior (Conformist pattern). Separating analytics allows independent optimization for query performance using OLAP databases or data warehouses without impacting OLTP transaction processing.

**Integration Points:**
- Publishes: (minimal - mostly read-only)
- Consumes: Events from all contexts (TransactionCommitted, StockUpdated, PriceChanged, CustomerRegistered, etc.)
- Uses: Conformist pattern (accepts events as-is, no translation)

---

### 10. **Account Management Context**

**Alignment:** Authentication & Authorization (Generic)

**Ubiquitous Language:** User, Role, Permission, Session, Authentication, Authorization, AccessControl

**Bounded Context Rationale:** User authentication and authorization is a cross-cutting concern affecting all contexts. Isolating account management allows centralized security policy management, integration with enterprise SSO/LDAP systems, and independent evolution of security features without touching business logic.

**Integration Points:**
- Publishes: `UserLoggedIn`, `UserLoggedOut`, `PermissionsChanged`, `SessionExpired`
- Consumes: (minimal)
- Consumed by: All contexts for authentication/authorization checks

---

### 11. **Notification Context**

**Alignment:** Notifications & Alerts Subdomain (Generic)

**Ubiquitous Language:** Notification, Channel, Template, Recipient, Delivery, Retry, Alert

**Bounded Context Rationale:** Notification delivery is a technical concern separate from business logic. It handles the mechanics of sending emails, SMS, push notifications, and managing delivery failures and retries. Using an Open Host Service pattern, this context accepts events from all business contexts and determines appropriate notification strategies.

**Integration Points:**
- Publishes: `NotificationSent`, `DeliveryFailed`
- Consumes: All business events that trigger notifications
- Uses: Published Language pattern (domain events as input)

---

## Tactical Design: Domain Models by Bounded Context

### 1. POS Console Context

#### Aggregates

**Sale Aggregate**
- **Root Entity:** `Sale`
  - `saleId` (identity)
  - `registerId` (POS terminal identifier)
  - `cashierId` (employee reference)
  - `customerId` (optional reference)
  - `sessionId` (checkout session)
  - `status` (open, completed, cancelled, suspended)
  - `startedAt` (timestamp)
  - `completedAt` (timestamp)
- **Entities (within aggregate):**
  - `SaleLineItem`
    - `lineItemId` (local identity)
    - `sku` (product reference)
    - `description`
    - `quantity`
    - `unitPrice`
    - `discounts` (list of DiscountApplication)
    - `lineTotal`
- **Value Objects:**
  - `Tender` (paymentMethod, amount, reference)
  - `DiscountApplication` (discountCode, discountType, amount)
  - `Receipt` (receiptNumber, format, deliveryMethod)
- **Invariants:**
  - Sale total must equal sum of line items plus tax minus discounts
  - Cannot add items to completed or cancelled sale
  - Tender total must match sale total before completion
  - Line item quantity must be positive
  - Cannot complete sale without at least one line item

**Design Rationale:** Sale is the aggregate root representing a single checkout session. Line items are entities within the aggregate because they have no meaning outside a sale. Tenders and discounts are Value Objects (immutable, no independent identity). The aggregate enforces pricing and payment totals consistency.

#### Domain Events
- `SaleStarted` (saleId, cashierId, timestamp)
- `ItemScanned` (saleId, sku, quantity)
- `ItemRemoved` (saleId, lineItemId)
- `DiscountApplied` (saleId, discountCode, amount)
- `TenderReceived` (saleId, tender)
- `SaleCompleted` (saleId, total, tenders, lineItems)
- `SaleCancelled` (saleId, reason)
- `ReturnInitiated` (originalSaleId, returnedItems)

#### Services
**BarcodeLookupService** - Resolves SKUs from barcode scans, integrates with Catalog Context
**ReceiptGenerationService** - Formats and prints/emails receipts
**HardwareIntegrationService** - Manages barcode scanners, receipt printers, cash drawers, card readers

#### Repositories
**SaleRepository** - Persist and retrieve Sale aggregates

---

### 2. Transaction Context

#### Aggregates

**Transaction Aggregate**
- **Root Entity:** `Transaction`
  - `transactionId` (identity)
  - `saleId` (reference to originating sale)
  - `storeId` (location identifier)
  - `transactionDate`
  - `status` (initiated, authorized, committed, failed, voided)
  - `totalAmount`
  - `taxAmount`
  - `discountAmount`
  - `netAmount`
- **Entities (within aggregate):**
  - `TransactionLine`
    - `lineId` (local identity)
    - `sku` (product reference)
    - `quantity`
    - `unitPrice`
    - `extendedPrice`
    - `taxRate`
- **Value Objects:**
  - `TransactionSummary` (subtotal, tax, discount, total)
  - `PaymentDetails` (paymentId, method, amount, status)
  - `AuditInfo` (createdBy, createdAt, modifiedBy, modifiedAt)
- **Invariants:**
  - Transaction total must match payment allocation
  - Cannot commit transaction without successful payment authorization
  - Cannot void transaction after fiscal invoice is issued
  - Transaction lines must reference valid, active SKUs
  - Inventory must be available for all transaction lines

**Design Rationale:** Transaction is the aggregate root orchestrating the complete transaction lifecycle. It coordinates with Payment, Inventory, and Fiscal Compliance contexts. The transaction enforces critical business rules around payment-inventory-fiscal consistency. Separating Transaction from Sale allows the same transaction processing logic to work for online, mobile, and in-store channels.

#### Domain Events
- `TransactionCreated` (transactionId, saleId, lineItems)
- `PaymentAllocated` (transactionId, paymentDetails)
- `InventoryReserved` (transactionId, reservations)
- `TransactionCommitted` (transactionId, timestamp, summary)
- `TransactionFailed` (transactionId, reason, failureCode)
- `TransactionVoided` (transactionId, reason, voidedBy)
- `RefundProcessed` (transactionId, refundAmount, refundMethod)

#### Services
**TransactionCoordinatorService** - Orchestrates distributed transaction across Payment, Inventory, Fiscal contexts using Saga pattern
**PricingService** - Calculates final pricing with taxes, discounts, promotions
**TaxCalculationService** - Determines applicable tax rates based on product, location, customer

#### Repositories
**TransactionRepository** - Persist and retrieve Transaction aggregates

---

### 3. Inventory Context

#### Aggregates

**InventoryItem Aggregate**
- **Root Entity:** `InventoryItem`
  - `itemId` (identity)
  - `sku` (unique product identifier)
  - `description`
  - `categoryId` (reference)
  - `unitOfMeasure`
  - `status` (active, discontinued, seasonal)
- **Value Objects:**
  - `SKU` (formatted product code)
  - `Attribute` (name, value pairs)
- **Invariants:**
  - SKU must be unique across the system
  - Cannot delete item with active stock levels
  - Cannot deactivate item with pending purchase orders

**StockLevel Aggregate**
- **Root Entity:** `StockLevel`
  - `stockLevelId` (identity)
  - `sku` (reference to InventoryItem)
  - `locationId` (warehouse, store, zone)
  - `quantityOnHand`
  - `quantityReserved`
  - `quantityAvailable` (computed: onHand - reserved)
  - `reorderPoint`
  - `reorderQuantity`
  - `lastUpdated`
- **Entities (within aggregate):**
  - `StockMovement`
    - `movementId` (local identity)
    - `movementType` (sale, return, adjustment, transfer, receipt)
    - `quantity` (positive or negative)
    - `reference` (transaction, PO, transfer)
    - `timestamp`
- **Value Objects:**
  - `Quantity` (amount, unit)
  - `Location` (locationId, locationType, name)
- **Invariants:**
  - Quantity available cannot go negative
  - Stock movements must maintain audit trail
  - Reserved quantity cannot exceed quantity on hand
  - Reorder point must be less than or equal to reorder quantity

**Design Rationale:** InventoryItem and StockLevel are separate aggregates because they have different lifecycles and consistency requirements. An item definition exists independently of stock levels (can define product before receiving stock). StockLevel is per-location, allowing independent inventory tracking across stores/warehouses. Stock movements within the aggregate provide complete audit history.

#### Domain Events
- `StockUpdated` (sku, locationId, quantityOnHand, quantityAvailable)
- `LowStockDetected` (sku, locationId, currentQuantity, reorderPoint)
- `StockReserved` (sku, locationId, quantity, reservationId)
- `StockReleased` (sku, locationId, quantity, reservationId)
- `StockTransferred` (sku, fromLocation, toLocation, quantity)
- `InventoryAdjusted` (sku, locationId, adjustmentType, quantity, reason)
- `ItemReceived` (sku, locationId, quantity, purchaseOrderId)

#### Services
**AvailabilityQueryService** - Fast SKU availability lookup (potentially using NoSQL read model)
**StockReservationService** - Manages temporary inventory reservations during checkout
**ReorderAutomationService** - Triggers purchase orders when stock falls below reorder points

#### Repositories
- **InventoryItemRepository**
- **StockLevelRepository**

---

### 4. Catalog Context

#### Aggregates

**Product Aggregate**
- **Root Entity:** `Product`
  - `productId` (identity)
  - `name`
  - `description`
  - `categoryId` (reference to category hierarchy)
  - `brand`
  - `status` (active, inactive, discontinued)
  - `createdAt`
  - `updatedAt`
- **Entities (within aggregate):**
  - `ProductVariant`
    - `variantId` (local identity)
    - `sku` (unique across all products)
    - `variantAttributes` (size, color, material, etc.)
    - `basePrice`
    - `costPrice`
    - `isActive`
- **Value Objects:**
  - `PriceRule` (ruleType, condition, discountPercent, startDate, endDate)
  - `Category` (categoryId, name, parentCategoryId)
  - `Attribute` (name, value, dataType)
  - `Margin` (costPrice, sellingPrice, marginPercent)
- **Invariants:**
  - Product must have at least one active variant
  - SKU must be unique across all variants and products
  - Base price must be greater than cost price
  - Variant attributes must match product's allowed attribute set
  - Price rules cannot overlap for same product/variant

**Design Rationale:** Product is the aggregate root containing variants. Variants are entities with local identity because they share product-level attributes (name, brand, category) but have unique SKUs and pricing. Price rules are Value Objects defining pricing logic. The aggregate enforces SKU uniqueness and pricing consistency.

#### Domain Events
- `ProductCreated` (productId, name, category)
- `ProductUpdated` (productId, changes)
- `VariantAdded` (productId, variantId, sku)
- `PriceChanged` (sku, oldPrice, newPrice, effectiveDate)
- `PriceRuleCreated` (ruleId, applicableProducts, rule)
- `ProductActivated` (productId, sku)
- `ProductDeactivated` (productId, sku, reason)
- `CategoryAssigned` (productId, categoryId)

#### Services
**PricingCalculationService** - Determines effective price given product, quantity, customer, date
**CategoryManagementService** - Manages hierarchical category structures
**PromotionService** - Creates and manages time-based and condition-based promotions

#### Repositories
**ProductRepository**

---

### 5. Payment Context

#### Aggregates

**Payment Aggregate**
- **Root Entity:** `Payment`
  - `paymentId` (identity)
  - `transactionId` (reference to Transaction)
  - `amount`
  - `currency`
  - `paymentMethod` (card, cash, digital wallet, gift card)
  - `status` (pending, authorized, captured, failed, voided, refunded)
  - `gatewayProvider` (Stripe, PayPal, Square)
  - `gatewayTransactionId` (external reference)
  - `attemptedAt`
  - `authorizedAt`
  - `capturedAt`
- **Value Objects:**
  - `PaymentToken` (tokenized payment instrument, PCI-safe)
  - `PaymentMethod` (type, lastFourDigits, expiryDate)
  - `AuthorizationDetails` (authCode, avsResult, cvvResult)
  - `RefundDetails` (refundId, amount, reason, refundedAt)
- **Invariants:**
  - Cannot capture payment before authorization
  - Cannot refund more than captured amount
  - Payment amount must match transaction total
  - Failed payments cannot transition to captured
  - Authorization expires after configurable timeout

**Design Rationale:** Payment is the aggregate root managing payment lifecycle with strict state transitions. Payment tokens are Value Objects to maintain PCI compliance (never store raw card data). Authorization and refund details are Value Objects capturing gateway responses. The aggregate enforces payment processing rules and gateway interaction consistency.

#### Domain Events
- `PaymentInitiated` (paymentId, transactionId, amount)
- `PaymentAuthorized` (paymentId, authCode, authDetails)
- `PaymentCaptured` (paymentId, capturedAmount)
- `PaymentFailed` (paymentId, errorCode, errorMessage)
- `PaymentVoided` (paymentId, reason)
- `RefundIssued` (paymentId, refundId, refundAmount)
- `RefundCompleted` (paymentId, refundId)

#### Services
**PaymentGatewayService** - Anticorruption Layer wrapping Stripe, PayPal, Square APIs
**TokenizationService** - PCI-compliant payment instrument tokenization
**FraudDetectionService** - Integration with fraud screening services

#### Repositories
**PaymentRepository**

---

### 6. Fiscal Compliance Context

#### Aggregates

**FiscalInvoice Aggregate**
- **Root Entity:** `FiscalInvoice`
  - `invoiceId` (identity)
  - `invoiceNumber` (sequential, per location)
  - `transactionId` (reference)
  - `storeId` (location)
  - `issuedAt` (timestamp)
  - `status` (draft, signed, transmitted, verified)
  - `customerTaxId` (optional)
  - `totalAmount`
  - `taxAmount`
  - `netAmount`
- **Entities (within aggregate):**
  - `InvoiceLineItem`
    - `lineId` (local identity)
    - `description`
    - `quantity`
    - `unitPrice`
    - `taxRate`
    - `lineTotal`
- **Value Objects:**
  - `DigitalSignature` (signature, algorithm, timestamp, certificateId)
  - `QRCode` (qrCodeData, verificationUrl)
  - `TaxBreakdown` (taxType, taxRate, taxableAmount, taxAmount)
- **Invariants:**
  - Invoice number must be sequential and unique per location
  - Cannot modify invoice after signing
  - Digital signature must be valid
  - Invoice must be transmitted to tax authority within regulatory timeframe
  - Voided invoices must reference original invoice

**Design Rationale:** FiscalInvoice is the aggregate root for tax-compliant invoicing. It's separate from Transaction because fiscal requirements vary by jurisdiction and may have additional validation rules. Digital signatures use secure hardware elements (for Rwanda EBM compliance). QR codes enable customer verification. The aggregate enforces fiscal compliance rules and audit trail integrity.

#### Domain Events
- `InvoiceCreated` (invoiceId, transactionId, invoiceNumber)
- `InvoiceSigned` (invoiceId, digitalSignature, qrCode)
- `InvoiceTransmitted` (invoiceId, transmissionTimestamp, authoritySysRef)
- `InvoiceVerified` (invoiceId, verificationStatus)
- `InvoiceVoided` (invoiceId, voidReason, originalInvoiceId)
- `AuditDataArchived` (invoiceId, archivalTimestamp)

#### Services
**DigitalSignatureService** - Integrates with secure element hardware for cryptographic signatures
**QRCodeGenerationService** - Creates verification QR codes with government-specified format
**EBMIntegrationService** - Anticorruption Layer for Electronic Billing Machine / EFDMS APIs
**AuditDataArchivalService** - Secures and preserves fiscal data for regulatory retention periods

#### Repositories
**FiscalInvoiceRepository**

---

### 7. Customer Context

#### Aggregates

**Customer Aggregate**
- **Root Entity:** `Customer`
  - `customerId` (identity)
  - `customerNumber` (business identifier)
  - `firstName`
  - `lastName`
  - `email`
  - `phoneNumber`
  - `registeredAt`
  - `status` (active, inactive, blocked)
- **Entities (within aggregate):**
  - `LoyaltyAccount`
    - `accountId` (local identity)
    - `currentPoints`
    - `lifetimePoints`
    - `tier` (bronze, silver, gold, platinum)
    - `tierExpiryDate`
- **Value Objects:**
  - `Address` (street, city, state, zipCode, country)
  - `ContactInfo` (email, phone, preferredChannel)
  - `Preference` (category, value)
- **Invariants:**
  - Email must be unique if provided
  - Phone number must be unique if provided
  - Points balance cannot go negative
  - Tier must match point thresholds
  - Cannot delete customer with unredeemed points or active orders

**Design Rationale:** Customer is the aggregate root managing customer identity and loyalty. LoyaltyAccount is an entity within the aggregate because it's tightly coupled to customer lifecycle and point balance is a critical invariant. Addresses and contact info are Value Objects (can be duplicated, no independent identity). The aggregate enforces customer uniqueness and loyalty program rules.

#### Domain Events
- `CustomerRegistered` (customerId, registrationChannel)
- `CustomerUpdated` (customerId, changes)
- `PointsEarned` (customerId, transactionId, pointsEarned)
- `PointsRedeemed` (customerId, pointsRedeemed, redemptionValue)
- `PointsExpired` (customerId, pointsExpired)
- `TierUpgraded` (customerId, oldTier, newTier)
- `TierDowngraded` (customerId, oldTier, newTier)
- `RewardIssued` (customerId, rewardType, rewardValue)

#### Services
**LoyaltyCalculationService** - Computes points earned based on purchase amount and tier
**CustomerIdentificationService** - Lookup by phone, email, card number
**PreferenceManagementService** - Manages customer communication and shopping preferences

#### Repositories
**CustomerRepository**

---

### 8. Purchasing Context

#### Aggregates

**PurchaseOrder Aggregate**
- **Root Entity:** `PurchaseOrder`
  - `purchaseOrderId` (identity)
  - `poNumber` (business identifier)
  - `supplierId` (reference)
  - `requestedBy` (employee reference)
  - `approvedBy` (optional, manager reference)
  - `orderDate`
  - `expectedDeliveryDate`
  - `status` (draft, submitted, approved, shipped, received, closed, cancelled)
  - `totalAmount`
- **Entities (within aggregate):**
  - `OrderLine`
    - `lineId` (local identity)
    - `sku` (product reference)
    - `description`
    - `quantityOrdered`
    - `quantityReceived`
    - `unitCost`
    - `lineTotal`
  - `Supplier`
    - `supplierId` (identity)
    - `name`
    - `contactInfo`
- **Value Objects:**
  - `DeliverySchedule` (expectedDate, deliveryMethod, trackingNumber)
  - `PaymentTerms` (termsDays, discountPercent, discountDays)
  - `InvoiceReconciliation` (invoiceNumber, invoiceAmount, matched, variance)
- **Invariants:**
  - Cannot submit PO without at least one line item
  - Cannot receive more quantity than ordered
  - PO total must match sum of line totals
  - Cannot modify PO after approval without creating amendment
  - Received quantity must match invoice quantity for reconciliation

**Design Rationale:** PurchaseOrder is the aggregate root for supplier ordering. Order lines are entities within the aggregate with distinct quantities ordered/received. Supplier information is referenced. Payment terms and delivery schedules are Value Objects. The aggregate enforces ordering workflow and invoice reconciliation rules.

#### Domain Events
- `PurchaseOrderCreated` (purchaseOrderId, supplierId)
- `PurchaseOrderSubmitted` (purchaseOrderId, submittedBy)
- `PurchaseOrderApproved` (purchaseOrderId, approvedBy)
- `PurchaseOrderShipped` (purchaseOrderId, trackingInfo)
- `GoodsReceived` (purchaseOrderId, receivedLines)
- `InvoiceReconciled` (purchaseOrderId, invoiceNumber, reconciliationResult)
- `SupplierPaymentScheduled` (purchaseOrderId, paymentAmount, paymentDate)

#### Services
**SupplierIntegrationService** - Anticorruption Layer for supplier EDI/API integrations
**InvoiceMatchingService** - Three-way match (PO, receipt, invoice)
**AutoReorderService** - Generates POs based on low stock triggers

#### Repositories
- **PurchaseOrderRepository**
- **SupplierRepository**

---

### 9. Analytics Context

#### Aggregates

**SalesReport Aggregate**
- **Root Entity:** `SalesReport`
  - `reportId` (identity)
  - `reportType` (daily, weekly, monthly, custom)
  - `startDate`
  - `endDate`
  - `generatedAt`
  - `totalSales`
  - `totalTransactions`
  - `averageTransactionValue`
- **Value Objects:**
  - `Metric` (name, value, unit, trend)
  - `Dimension` (dimensionType, dimensionValue, metricValue)
  - `TimeSeries` (timestamp, value)

**InventoryReport Aggregate**
- **Root Entity:** `InventoryReport`
  - `reportId` (identity)
  - `asOfDate`
  - `totalSKUs`
  - `totalValue`
  - `turnoverRate`
- **Value Objects:**
  - `StockValuation` (sku, quantity, unitCost, extendedValue)
  - `TurnoverMetric` (sku, salesVelocity, daysOfSupply)

**Design Rationale:** Analytics uses specialized read models optimized for reporting queries. These are not transactional aggregates but denormalized projections built from domain events. The context uses CQRS pattern - write side is event consumption, read side is optimized queries. Reports are generated on-demand or scheduled.

#### Domain Events
- `ReportGenerated` (reportId, reportType, timeframe)
- (This context primarily consumes events from other contexts)

#### Services
**EventProjectionService** - Builds read models from domain events
**ReportGenerationService** - Creates formatted reports (PDF, Excel, dashboards)
**ForecastingService** - Predictive analytics for inventory and sales trends

#### Repositories
- **SalesReportRepository** (read-model store, potentially OLAP/columnar DB)
- **InventoryReportRepository**

---

### 10. Account Management Context

#### Aggregates

**User Aggregate**
- **Root Entity:** `User`
  - `userId` (identity)
  - `username`
  - `email`
  - `passwordHash`
  - `status` (active, inactive, locked)
  - `lastLogin`
  - `failedLoginAttempts`
- **Entities (within aggregate):**
  - `Role`
    - `roleId` (identity)
    - `roleName`
    - `permissions` (list of Permission)
- **Value Objects:**
  - `Permission` (resource, action)
  - `Session` (sessionId, expiresAt, ipAddress)
- **Invariants:**
  - Username must be unique
  - Email must be unique
  - User must have at least one role
  - Locked accounts cannot create new sessions
  - Failed login attempts reset on successful login

**Design Rationale:** User is the aggregate root for authentication. Roles are entities defining permission sets. Sessions are Value Objects (short-lived, no persistent identity). The aggregate enforces authentication rules and account security policies.

#### Domain Events
- `UserCreated` (userId, username, roles)
- `UserLoggedIn` (userId, sessionId, timestamp)
- `UserLoggedOut` (userId, sessionId)
- `LoginFailed` (username, reason, attemptCount)
- `AccountLocked` (userId, reason)
- `PasswordChanged` (userId, changedBy)
- `RoleAssigned` (userId, roleId)
- `PermissionsChanged` (roleId, addedPermissions, removedPermissions)

#### Services
**AuthenticationService** - Validates credentials, manages sessions
**AuthorizationService** - Checks permissions for operations
**PasswordPolicyService** - Enforces password complexity and rotation rules

#### Repositories
**UserRepository**

---

### 11. Notification Context

#### Aggregates

**Notification Aggregate**
- **Root Entity:** `Notification`
  - `notificationId` (identity)
  - `recipientId` (customer or employee)
  - `channel` (email, sms, push, in-app)
  - `templateId`
  - `status` (pending, sent, delivered, failed)
  - `createdAt`
  - `sentAt`
  - `deliveredAt`
  - `retryCount`
- **Value Objects:**
  - `Recipient` (recipientId, contactInfo, preferredChannel)
  - `Template` (templateId, subject, body, placeholders)
  - `DeliveryAttempt` (attemptNumber, attemptedAt, statusCode, errorMessage)
- **Invariants:**
  - Notification must have valid recipient contact info for chosen channel
  - Retry count cannot exceed maximum retry attempts
  - Cannot mark delivered without successful send confirmation

**Design Rationale:** Notification is the aggregate root for message delivery. Templates are Value Objects defining message format. Delivery attempts track retry history. The aggregate enforces delivery retry logic and channel-specific validation.

#### Domain Events
- `NotificationCreated` (notificationId, recipientId, channel)
- `NotificationSent` (notificationId, sentAt, provider)
- `NotificationDelivered` (notificationId, deliveredAt)
- `DeliveryFailed` (notificationId, failureReason, willRetry)
- `NotificationExpired` (notificationId, expiredAt)

#### Services
**ChannelAdapterService** - Integrates with Twilio, SendGrid, Firebase for delivery
**TemplateRenderingService** - Populates templates with dynamic data
**DeliveryRetryService** - Manages retry policies and exponential backoff

#### Repositories
**NotificationRepository**

---

## Context Map

### Integration Patterns

1. **POS Console Context → Transaction Context**
   - **Pattern:** Customer-Supplier (POS Console is Upstream, Transaction is Downstream)
   - **Integration:** When sale is completed at POS, `SaleCompleted` event triggers transaction creation
   - **Rationale:** POS initiates the transaction but doesn't manage transaction consistency logic

2. **Transaction Context → Inventory Context**
   - **Pattern:** Customer-Supplier (Transaction is Upstream, Inventory is Downstream)
   - **Integration:** `TransactionCommitted` event triggers stock deduction, `TransactionVoided` triggers stock restoration
   - **Rationale:** Transaction success/failure drives inventory changes, but transaction doesn't control inventory logic

3. **Transaction Context → Payment Context**
   - **Pattern:** Customer-Supplier (Transaction is Upstream, Payment is Downstream)
   - **Integration:** `TransactionCreated` triggers payment authorization, Transaction waits for `PaymentAuthorized` before committing
   - **Rationale:** Transaction orchestrates but Payment context owns payment processing rules

4. **Transaction Context → Fiscal Compliance Context**
   - **Pattern:** Customer-Supplier (Transaction is Upstream, Fiscal is Downstream)
   - **Integration:** `TransactionCommitted` triggers fiscal invoice generation and signing
   - **Rationale:** Fiscal compliance is triggered by completed transactions but has independent regulatory rules

5. **Catalog Context → POS Console Context**
   - **Pattern:** Customer-Supplier (Catalog is Upstream, POS is Downstream)
   - **Integration:** POS queries Catalog for current prices, `PriceChanged` events update POS cache
   - **Rationale:** Catalog owns product definitions and pricing rules, POS consumes for display

6. **Inventory Context → POS Console Context**
   - **Pattern:** Customer-Supplier (Inventory is Upstream, POS is Downstream)
   - **Integration:** POS queries Inventory for stock availability, `StockUpdated` events refresh POS cache
   - **Rationale:** Inventory is source of truth for availability, POS shows real-time stock

7. **Customer Context → POS Console Context**
   - **Pattern:** Customer-Supplier (Customer is Upstream, POS is Downstream)
   - **Integration:** POS identifies customer (phone/card scan), retrieves loyalty account for point calculation
   - **Rationale:** Customer context owns customer data, POS uses for personalization

8. **Payment Context → External Payment Gateways**
   - **Pattern:** Anticorruption Layer
   - **Integration:** PaymentGatewayService wraps Stripe, PayPal, Square APIs, translates external models to domain Payment concepts
   - **Rationale:** Protect domain from gateway-specific protocols, enable multi-gateway support, isolate PCI compliance

9. **Fiscal Compliance Context → Tax Authority Systems**
   - **Pattern:** Anticorruption Layer
   - **Integration:** EBMIntegrationService wraps EBM/EFDMS APIs, handles digital signatures with secure elements
   - **Rationale:** Tax authority APIs are external and government-controlled, ACL protects domain from regulatory system changes

10. **Purchasing Context → Supplier Systems**
    - **Pattern:** Anticorruption Layer
    - **Integration:** SupplierIntegrationService wraps EDI and supplier-specific APIs
    - **Rationale:** Suppliers have heterogeneous integration methods (EDI, REST, FTP), ACL provides unified interface

11. **All Contexts → Notification Context**
    - **Pattern:** Published Language (Domain Events)
    - **Integration:** All business contexts publish domain events, Notification subscribes and sends appropriate messages
    - **Rationale:** Notifications are cross-cutting concern, event-driven enables loose coupling

12. **All Contexts → Analytics Context**
    - **Pattern:** Conformist
    - **Integration:** Analytics consumes all domain events as-is, builds read models for reporting
    - **Rationale:** Analytics adapts to business domains, doesn't influence them, eventual consistency acceptable

13. **Inventory Context → Purchasing Context**
    - **Pattern:** Customer-Supplier (Inventory is Upstream, Purchasing is Downstream)
    - **Integration:** `LowStockDetected` event triggers auto-reorder workflow in Purchasing
    - **Rationale:** Inventory monitoring drives purchasing decisions

14. **Purchasing Context → Inventory Context**
    - **Pattern:** Customer-Supplier (Purchasing is Upstream, Inventory is Downstream)
    - **Integration:** `GoodsReceived` event triggers inventory addition
    - **Rationale:** Purchase receipt flows update inventory levels

15. **Account Management Context → All Contexts**
    - **Pattern:** Shared Kernel (minimally) or Customer-Supplier
    - **Integration:** All contexts call Account Management for authentication/authorization checks
    - **Rationale:** Security is cross-cutting, centralized identity management

### Visual Context Map

For a comprehensive visual representation of all bounded contexts, their aggregates, relationships, and integration patterns, see the [Visual Context Map](point_of_sale_system_context_map.html).

---

## Key Design Decisions & Rationale

### 1. **Separate POS Console from Transaction Processing**
**Decision:** POS Console and Transaction are different bounded contexts
**Rationale:**
- POS Console handles presentation, user interaction, hardware integration (scanners, printers)
- Transaction handles business logic, orchestration, consistency enforcement
- Separation enables headless commerce - same Transaction context serves online, mobile, kiosk channels
- POS UI can be redesigned without touching transaction processing logic
- Different scaling characteristics - POS console is session-based, Transaction is event-driven

### 2. **Inventory as Separate Aggregate per Location**
**Decision:** StockLevel aggregate is per-location, not global
**Rationale:**
- Different stores/warehouses have independent stock levels
- Enables eventual consistency across locations (acceptable for inventory sync)
- Allows store-specific inventory policies (reorder points, safety stock)
- Prevents distributed locking issues when multiple locations sell same SKU simultaneously
- Supports future multi-location transfer and allocation strategies

### 3. **Fiscal Compliance as Dedicated Context**
**Decision:** Fiscal compliance isolated from core transaction processing
**Rationale:**
- Regulatory requirements vary by country/region (Rwanda EBM, EU VAT, US sales tax)
- Fiscal rules change frequently due to legislation - isolation limits impact
- Digital signature and secure element integration is specialized concern
- Enables supporting multiple fiscal regimes without polluting Transaction context
- Audit and compliance requirements differ from business transaction rules

### 4. **Payment Context with Anticorruption Layer**
**Decision:** All payment gateway interactions go through ACL, not direct integration
**Rationale:**
- Payment gateways have different APIs (Stripe, PayPal, Square, Adyen)
- PCI DSS compliance requires careful data handling - ACL enforces boundaries
- Enables switching providers without changing Transaction context
- Translates gateway-specific errors to domain-meaningful payment failures
- Centralizes tokenization and secure payment handling

### 5. **Event-Driven Communication Between Contexts**
**Decision:** Contexts communicate primarily through domain events, not synchronous calls
**Rationale:**
- Loose coupling - contexts don't need direct dependencies
- Scalability - events can be queued and processed asynchronously
- Resilience - if Fiscal Compliance is down, Transaction still completes (fiscal invoice generated later)
- Audit trail - events provide complete business history for compliance
- Multiple consumers - same event triggers Inventory update, Notification, Analytics

### 6. **Transaction Coordinator with Saga Pattern**
**Decision:** Use Saga pattern for distributed transaction across Payment, Inventory, Fiscal contexts
**Rationale:**
- Cannot use traditional ACID transactions across microservices/contexts
- Saga provides compensating actions if steps fail (refund payment if fiscal invoice fails)
- Each context maintains local consistency, Saga ensures eventual consistency
- Explicit failure handling - clear compensation logic for each step

### 7. **CQRS for Analytics Context**
**Decision:** Analytics uses separate read models, not queries against transactional databases
**Rationale:**
- Reporting queries are complex and resource-intensive, would impact transaction performance
- Read models can be optimized for analytics (columnar databases, aggregations)
- Events provide natural source for building analytics projections
- Eventual consistency acceptable for business intelligence (reports don't need real-time accuracy)

### 8. **Catalog with Hierarchical Product-Variant Model**
**Decision:** Product is aggregate root containing Variants, not separate aggregates
**Rationale:**
- Variants share product-level attributes (brand, category, description)
- Pricing rules often apply at product level (all sizes 20% off)
- Variants don't exist independently - deleting product deletes variants
- Transactional consistency needed for product-variant changes

### 9. **Customer Loyalty Within Customer Aggregate**
**Decision:** LoyaltyAccount is entity within Customer aggregate, not separate aggregate
**Rationale:**
- Point balance is critical invariant tightly coupled to customer identity
- Tier calculation depends on customer lifetime points
- Redemption rules tied to customer status (active/blocked)
- Loyalty doesn't make sense without customer - strong lifecycle dependency

### 10. **Purchasing Context Separate from Inventory**
**Decision:** Purchase orders managed in separate context from inventory
**Rationale:**
- Purchasing has distinct workflows (approval, receiving, invoice matching)
- Purchasing involves supplier relationships - different domain than stock management
- Purchase orders exist before goods are received (forward-looking), inventory is current state
- Different teams manage purchasing (procurement) vs inventory (operations)

---

## Conclusion

This DDD design for the Point of Sale system:

1. **Identifies Core Business Logic** - Transaction Processing, Inventory Management, and Product Catalog receive maximum investment as competitive differentiators, while Payment and Notifications use off-the-shelf solutions

2. **Separates Concerns Through Bounded Contexts** - Each context has clear responsibilities and ubiquitous language, enabling independent evolution and team ownership

3. **Uses Appropriate Integration Patterns** - Anticorruption Layers protect domain from external systems (payment gateways, tax authorities), events enable loose coupling internally

4. **Maintains Aggregate Boundaries** - Aggregates enforce business invariants (transaction totals balance, inventory can't go negative, payment state transitions are valid)

5. **Scales Independently** - POS Console, Transaction, Inventory, and Analytics can scale separately based on load characteristics

6. **Ensures Fiscal Compliance** - Dedicated Fiscal Compliance context isolates regulatory complexity, supporting multiple tax regimes

7. **Enables Multi-Channel Operations** - Separating Transaction processing from POS Console allows same core logic to serve in-store, online, mobile, and kiosk channels

8. **Provides Comprehensive Audit Trail** - Event-driven architecture creates complete business history for compliance, troubleshooting, and analytics

9. **Protects Payment Security** - Payment context with ACL enforces PCI DSS boundaries and enables multi-gateway support

10. **Supports Business Intelligence** - CQRS pattern in Analytics context enables sophisticated reporting without impacting transaction performance

The design allows each bounded context to evolve independently while maintaining clear contracts through domain events and well-defined interfaces. The strategic classification of subdomains (Core, Supporting, Generic) guides investment decisions and build-vs-buy choices.
