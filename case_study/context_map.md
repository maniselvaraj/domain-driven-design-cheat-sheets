# Online Photo Printing Service - Visual Context Map

```
┌─────────────────────────────────────────────────────────────────────────────────────────────┐
│                          ONLINE PHOTO PRINTING SERVICE - DDD CONTEXT MAP                     │
└─────────────────────────────────────────────────────────────────────────────────────────────┘

                                    CORE DOMAIN (⭐)
                    ┌──────────────────────────────────────────────────┐
                    │                                                  │
                    │                                                  │
      ┌─────────────▼──────────────────────────────────────────────────▼─────────────┐
      │  ⭐ PHOTO-TO-PRODUCT ORDERING CONTEXT                                         │
      │  (Core Domain)                                                                │
      ├───────────────────────────────────────────────────────────────────────────────┤
      │ Aggregates:                                                                   │
      │ • Photo                                                                       │
      │   - photoId, userId, qualityStatus                                            │
      │   - ImageMetadata (VO), EditHistory (VO)                                      │
      │ • Album                                                                       │
      │   - albumId, photoReferences                                                  │
      │ • Cart                                                                        │
      │   - cartId, CartItem entities                                                 │
      │ • Order                                                                       │
      │   - orderId, OrderLine entities                                               │
      │   - ShippingAddress (VO), OrderSummary (VO)                                   │
      │                                                                               │
      │ Services:                                                                     │
      │ • PhotoValidationService, CloudImportService, OrderPlacementService           │
      │                                                                               │
      │ Events Published:                                                             │
      │ • PhotoUploaded, PhotoValidated, PhotoEdited                                  │
      │ • OrderPlaced, OrderPaid, OrderCancelled, OrderStatusChanged                  │
      └───────────────────────────────────────┬───────────────────────────────────────┘
                                              │
                                              │ Customer-
                                              │ Supplier
                                              │
                                              ▼
                          SUPPORTING & GENERIC DOMAINS
      ┌───────────────────────────────────────────────────────────────────────────┐
      │                                                                           │
      │                                                                           │
      ┌─────────────▼──────────────┐              ┌──────────────────▼────────────┐
      │  FULFILLMENT CONTEXT       │              │  DELIVERY CONTEXT              │
      │  (Supporting)              │              │  (Supporting)                  │
      ├────────────────────────────┤              ├────────────────────────────────┤
      │ Aggregates:                │              │ Aggregates:                    │
      │ • PrintJob                 │              │ • Delivery                     │
      │   - printJobId             │              │   - deliveryId                 │
      │   - orderId (ref)          │              │   - orderId (ref)              │
      │   - PrintSpecification(VO) │              │   - courierPartner             │
      │   - QualityCheckResult(VO) │              │   - trackingNumber             │
      │   - ReprintReason (VO)     │              │   - DeliveryAddress (VO)       │
      │                            │              │   - DeliveryOption (VO)        │
      │ Services:                  │              │   - DeliveryAttempt (VO)       │
      │ • PrintPartnerService(ACL) │              │                                │
      │ • QualityAssuranceService  │              │ Services:                      │
      │                            │──────────────┤ • CourierIntegrationService    │
      │ Events Published:          │  Customer-   │   (ACL)                        │
      │ • PrintJobSubmitted        │  Supplier    │                                │
      │ • PrintJobCompleted        │              │ Events Published:              │
      │ • ReprintRequested         │              │ • DeliveryDispatched           │
      │                            │              │ • DeliveryCompleted            │
      └────────────────────────────┘              │ • DeliveryFailed               │
                                                  └────────────────────────────────┘

      ┌─────────────────────────────┐              ┌────────────────────────────────┐
      │  CUSTOMER CONTEXT          │              │  CATALOG CONTEXT               │
      │  (Supporting)              │              │  (Supporting)                  │
      ├────────────────────────────┤              ├────────────────────────────────┤
      │ Aggregates:                │              │ Aggregates:                    │
      │ • Customer                 │              │ • Product                      │
      │   - customerId             │              │   - productId                  │
      │   - email                  │              │   - ProductVariant entity      │
      │   - Address (VO)           │              │   - PriceRule (VO)             │
      │   - CommunicationPrefs(VO) │──────────────┤   - SizeSpecification (VO)     │
      │                            │  Customer-   │                                │
      │ Services:                  │  Supplier    │ Services:                      │
      │ • AuthenticationService    │              │ • PricingService               │
      │                            │              │                                │
      │ Events Published:          │              │ Events Published:              │
      │ • UserRegistered           │              │ • ProductPriceChanged          │
      │ • UserAuthenticated        │              │ • ProductAvailabilityChg       │
      └────────────────────────────┘              └────────────────────────────────┘

      ┌─────────────────────────────────────────────────────────────────────┐
      │  PAYMENT CONTEXT                                                    │
      │  (Generic)                                                          │
      ├─────────────────────────────────────────────────────────────────────┤
      │ Aggregates:                                                         │
      │ • Payment                                                           │
      │   - paymentId, orderId (ref), amount, status                        │
      │   - PaymentToken (VO), RefundDetails (VO)                           │
      │                                                                     │
      │ Services:                                                           │
      │ • PaymentGatewayService (ACL)                                       │
      │                                                                     │
      │ Events Published:                                                   │
      │ • PaymentAuthorized, PaymentCaptured, PaymentFailed, RefundIssued   │
      └─────────────────────────────────────────────────────────────────────┘
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
1. Customer → Photo-to-Product Ordering: User identity reference
2. Catalog → Photo-to-Product Ordering: Product pricing and configuration
3. Photo-to-Product Ordering → Payment: Payment authorization trigger
4. Payment → Photo-to-Product Ordering: Payment confirmation
5. Photo-to-Product Ordering → Fulfillment: Print job creation with photo specs after payment
6. Fulfillment → Delivery: Delivery scheduling after print completion
7. All Contexts → Engagement: Event-driven notifications
8. All Contexts → Insights: Event streaming for analytics
9. Support → Multiple: Query access for issue resolution
10. External Systems: Protected by ACL pattern (Print Partners, Payment Gateways, Couriers)

CONTEXT BOUNDARIES:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
• Each box represents an autonomous bounded context with its own ubiquitous language
• Contexts communicate through well-defined integration patterns (events, APIs)
• Core domains (⭐) receive highest investment and best engineering talent
• ACL protects internal models from external system complexity and changes
• Cross-cutting contexts (Engagement, Insights, Support) consume events from all domains
```
