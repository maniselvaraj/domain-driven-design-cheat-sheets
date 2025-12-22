# Online Photo Printing Service - Visual Context Map

```
┌─────────────────────────────────────────────────────────────────────────────────────────────┐
│                          ONLINE PHOTO PRINTING SERVICE - DDD CONTEXT MAP                     │
└─────────────────────────────────────────────────────────────────────────────────────────────┘

                                    CORE DOMAINS (⭐)
                    ┌──────────────────────────────────────────────────┐
                    │                                                  │
                    │                                                  │
      ┌─────────────▼──────────────┐              ┌──────────────────▼────────────┐
      │  ⭐ PHOTO LIBRARY CONTEXT  │              │  ⭐ ORDERING CONTEXT           │
      │  (Core Domain)             │              │  (Core Domain)                 │
      ├────────────────────────────┤              ├───────────────────────────────┤
      │ Aggregates:                │              │ Aggregates:                   │
      │ • Photo                    │              │ • Cart                        │
      │   - photoId                │              │   - cartId                    │
      │   - userId                 │              │   - CartItem entities         │
      │   - qualityStatus          │              │ • Order                       │
      │   - ImageMetadata (VO)     │              │   - orderId                   │
      │   - EditHistory (VO)       │              │   - OrderLine entities        │
      │ • Album                    │              │   - ShippingAddress (VO)      │
      │   - albumId                │              │   - OrderSummary (VO)         │
      │   - photoReferences        │              │                               │
      │                            │              │ Services:                     │
      │ Services:                  │              │ • OrderPlacementService       │
      │ • PhotoValidationService   │              │                               │
      │ • CloudImportService       │              │ Events Published:             │
      │                            │──────────────┤ • OrderPlaced                 │
      │ Events Published:          │  Customer-   │ • OrderPaid                   │
      │ • PhotoUploaded            │  Supplier    │ • OrderCancelled              │
      │ • PhotoValidated           │              │ • OrderStatusChanged          │
      │ • PhotoEdited              │              │                               │
      └────────────────────────────┘              └───────────────┬───────────────┘
                    │                                             │
                    │                                             │
                    │                                             │
                    │                          ┌──────────────────▼────────────┐
                    │                          │  ⭐ FULFILLMENT CONTEXT       │
                    │                          │  (Core Domain)                │
                    │                          ├───────────────────────────────┤
                    │                          │ Aggregates:                   │
                    │                          │ • PrintJob                    │
                    └──────────────────────────┤   - printJobId                │
                                    Customer-  │   - orderId (ref)             │
                                    Supplier   │   - PrintSpecification (VO)   │
                                               │   - QualityCheckResult (VO)   │
                                               │   - ReprintReason (VO)        │
                                               │                               │
                                               │ Services:                     │
                                               │ • PrintPartnerService (ACL)   │
                                               │ • QualityAssuranceService     │
                                               │                               │
                                               │ Events Published:             │
                                               │ • PrintJobSubmitted           │
                                               │ • PrintJobCompleted           │
                                               │ • ReprintRequested            │
                                               └───────────────┬───────────────┘
                                                               │
                                                               │ Customer-
                                                               │ Supplier
                                                               │
                                                               ▼
                          SUPPORTING & GENERIC DOMAINS
                    ┌────────────────────────────────────────────────────┐
                    │                                                    │
      ┌─────────────▼──────────────┐              ┌────────────────────▼───────────┐
      │  CUSTOMER CONTEXT          │              │  DELIVERY CONTEXT              │
      │  (Supporting)              │              │  (Supporting)                  │
      ├────────────────────────────┤              ├────────────────────────────────┤
      │ Aggregates:                │              │ Aggregates:                    │
      │ • Customer                 │              │ • Delivery                     │
      │   - customerId             │              │   - deliveryId                 │
      │   - email                  │              │   - orderId (ref)              │
      │   - Address (VO)           │              │   - courierPartner             │
      │   - CommunicationPrefs(VO) │              │   - trackingNumber             │
      │                            │              │   - DeliveryAddress (VO)       │
      │ Services:                  │              │   - DeliveryOption (VO)        │
      │ • AuthenticationService    │              │   - DeliveryAttempt (VO)       │
      │                            │              │                                │
      │ Events Published:          │              │ Services:                      │
      │ • UserRegistered           │              │ • CourierIntegrationService    │
      │ • UserAuthenticated        │              │   (ACL)                        │
      │                            │              │                                │
      └────────────┬───────────────┘              │ Events Published:              │
                   │                              │ • DeliveryDispatched           │
                   │ Customer-                    │ • DeliveryCompleted            │
                   │ Supplier                     │ • DeliveryFailed               │
                   │                              └────────────────────────────────┘
                   │
                   │
      ┌────────────▼───────────────┐              ┌────────────────────────────────┐
      │  CATALOG CONTEXT           │              │  PAYMENT CONTEXT               │
      │  (Supporting)              │              │  (Generic)                     │
      ├────────────────────────────┤              ├────────────────────────────────┤
      │ Aggregates:                │              │ Aggregates:                    │
      │ • Product                  │              │ • Payment                      │
      │   - productId              │              │   - paymentId                  │
      │   - ProductVariant entity  │              │   - orderId (ref)              │
      │   - PriceRule (VO)         │              │   - amount                     │
      │   - SizeSpecification (VO) │              │   - status                     │
      │                            │              │   - PaymentToken (VO)          │
      │ Services:                  │──────────────│   - RefundDetails (VO)         │
      │ • PricingService           │  Customer-   │                                │
      │                            │  Supplier    │ Services:                      │
      │ Events Published:          │              │ • PaymentGatewayService (ACL)  │
      │ • ProductPriceChanged      │              │                                │
      │ • ProductAvailabilityChg   │              │ Events Published:              │
      │                            │              │ • PaymentAuthorized            │
      └────────────────────────────┘              │ • PaymentCaptured              │
                                                  │ • PaymentFailed                │
                                                  │ • RefundIssued                 │
                                                  └─────────────┬──────────────────┘
                                                                │
                                                                │ Consumes
                                                                │ events from
                                                                │ Ordering
                                                                │
┌───────────────────────────────────────────────────────────────┼──────────────────────────┐
│                            CROSS-CUTTING CONTEXTS             │                          │
├───────────────────────────────────────────────────────────────┼──────────────────────────┤
│                                                               │                          │
│  ┌────────────────────────────┐        ┌────────────────────▼──────────────────┐       │
│  │  ENGAGEMENT CONTEXT        │        │  INSIGHTS CONTEXT                      │       │
│  │  (Generic - Notifications) │        │  (Supporting - Analytics)              │       │
│  ├────────────────────────────┤        ├────────────────────────────────────────┤       │
│  │ Aggregates:                │        │ Read Models:                           │       │
│  │ • Notification             │        │ • Metric                               │       │
│  │   - Channel (VO)           │        │ • Report                               │       │
│  │   - Template (VO)          │        │ • Conversion                           │       │
│  │   - Recipient (VO)         │        │ • Revenue                              │       │
│  │                            │        │                                        │       │
│  │ Pattern:                   │        │ Pattern:                               │       │
│  │ • Open Host Service        │        │ • Conformist                           │       │
│  │                            │        │                                        │       │
│  │ Consumes:                  │        │ Consumes:                              │       │
│  │ • ALL domain events        │        │ • ALL domain events                    │       │
│  └────────────────────────────┘        └────────────────────────────────────────┘       │
│                                                                                          │
│  ┌──────────────────────────────────────────────────────────────────────────────────┐  │
│  │  SUPPORT CONTEXT (Supporting)                                                    │  │
│  ├──────────────────────────────────────────────────────────────────────────────────┤  │
│  │ Aggregates:                                                                      │  │
│  │ • SupportTicket                                                                  │  │
│  │   - ticketId                                                                     │  │
│  │   - orderId (optional ref)                                                       │  │
│  │   - TicketInteraction entities                                                   │  │
│  │   - Resolution (VO)                                                              │  │
│  │                                                                                  │  │
│  │ Services:                                                                        │  │
│  │ • IssueResolutionService (coordinates with Payment, Fulfillment, Delivery)      │  │
│  │                                                                                  │  │
│  │ Pattern: Customer-Supplier (reads from multiple contexts)                       │  │
│  │                                                                                  │  │
│  │ Events Published:                                                                │  │
│  │ • TicketCreated, IssueResolved                                                   │  │
│  └──────────────────────────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────────────────┐
│                              EXTERNAL SYSTEMS (with ACL)                                 │
├─────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                          │
│  ┌──────────────────────┐       ┌──────────────────────┐       ┌────────────────────┐ │
│  │ Payment Gateways     │       │ Printing Partners    │       │ Courier Services   │ │
│  │ (Stripe, PayPal)     │◄──ACL─┤ (Multiple vendors)   │◄──ACL─┤ (Uber, DoorDash)   │ │
│  └──────────────────────┘       └──────────────────────┘       └────────────────────┘ │
│           ▲                                ▲                              ▲             │
│           │                                │                              │             │
│      Payment Context              Fulfillment Context            Delivery Context      │
└─────────────────────────────────────────────────────────────────────────────────────────┘

LEGEND:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⭐ = Core Domain (maximum investment, competitive advantage)
(VO) = Value Object (immutable, no identity)
ACL = Anticorruption Layer (protects domain from external changes)
─────> = Integration direction (upstream → downstream)
Customer-Supplier = Integration pattern (supplier publishes, customer consumes)
Published Language = Shared event format across contexts
Conformist = Downstream accepts upstream model as-is
Open Host Service = Standard API for multiple consumers

KEY INTEGRATION FLOWS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. Customer → Ordering: User identity reference
2. Photo Library → Ordering: Photo validation before order
3. Photo Library → Fulfillment: Photo data for print specs
4. Catalog → Ordering: Product pricing and configuration
5. Ordering → Payment: Payment authorization trigger
6. Payment → Ordering: Payment confirmation
7. Ordering → Fulfillment: Print job creation after payment
8. Fulfillment → Delivery: Delivery scheduling after print completion
9. All Contexts → Engagement: Event-driven notifications
10. All Contexts → Insights: Event streaming for analytics
11. Support → Multiple: Query access for issue resolution
12. External Systems: Protected by ACL pattern

CONTEXT BOUNDARIES:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
• Each box represents an autonomous bounded context with its own ubiquitous language
• Contexts communicate through well-defined integration patterns (events, APIs)
• Core domains (⭐) receive highest investment and best engineering talent
• ACL protects internal models from external system complexity and changes
• Cross-cutting contexts (Engagement, Insights, Support) consume events from all domains
```
