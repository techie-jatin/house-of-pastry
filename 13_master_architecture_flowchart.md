# Master Architecture & Workflow Pipeline

This document visualizes the complete "chain" of workflows (Customer Journey, Offline Resilience, Validation, Notifications, and Delivery) into a single, cohesive master pipeline.

## 1. End-to-End Sequence Diagram
This diagram shows the exact step-by-step communication between all entities on the platform.

```mermaid
sequenceDiagram
    autonumber
    actor Customer
    participant PWA as PWA (Frontend + Cloudflare)
    participant Server as VPS (Node.js Server)
    participant DB as Database (PostgreSQL/MongoDB)
    actor Admin as Cafe Admin
    actor DeliveryBoy as Delivery Boy

    %% Browsing & Offline Resilience
    Customer->>PWA: Opens Website
    PWA-->>Customer: Serves UI & Cached Menu (Offline Resilient)
    Customer->>PWA: Adds items to Cart
    
    %% Checkout & Validation
    Customer->>PWA: Initiates Checkout (Google Maps Autocomplete)
    PWA->>Server: POST /checkout (Name, Phone, Address, Payment)
    
    %% Security & Server Validation
    rect rgb(200, 220, 240)
        note right of Server: Cloudflare WAF, Rate Limiting & SSL
        Server->>Server: Validate Regex (Phone/Name/Address)
        Server->>DB: Check Inventory & Recalculate Prices
    end
    
    %% Database & Notification
    Server->>DB: Save Order (Status: PENDING_VERIFICATION)
    Server->>PWA: Return "Order Received"
    PWA-->>Customer: Show Success Screen
    Server->>Admin: OneSignal Push Alert (New Order!)
    
    %% Admin Verification
    Admin->>Customer: Phone Call Verification
    Customer-->>Admin: Confirms Details
    Admin->>Server: Approves Order (Dashboard)
    Server->>DB: Update Status (PREPARING)
    Server->>Customer: OneSignal Push Alert (Order is being prepared)
    
    %% Delivery Phase
    Admin->>Server: Assigns Delivery Boy
    Server->>DB: Update Status (OUT_FOR_DELIVERY)
    Server->>DeliveryBoy: OneSignal Push Alert (New Delivery Assigned)
    Server->>Customer: OneSignal Push Alert (Order is on the way)
    
    DeliveryBoy->>Customer: Hands over package & Asks for OTP
    Customer-->>DeliveryBoy: Provides OTP from tracking page
    DeliveryBoy->>Server: Submits OTP via App
    Server->>Server: Validates OTP
    Server->>DB: Update Status (DELIVERED)
    Server->>Customer: Final Email (Hostinger Email) / Push Alert
    
    %% Automated Maintenance
    note over Server, DB: 3:00 AM Every Night
    Server->>DB: Dump Database
    Server->>Server: Zip & Encrypt Data
    Server->>Server: rclone upload to Google Drive (15GB Free)
```

## 2. Order State Machine (The "Block" Chain)
This flowchart visualizes the strict path an order takes. An order cannot skip a block in the chain, ensuring absolute data integrity and preventing fake deliveries.

```mermaid
flowchart TD
    %% Define styles
    classDef offline fill:#f9f,stroke:#333,stroke-width:2px,color:#000;
    classDef checkout fill:#bbf,stroke:#333,stroke-width:2px,color:#000;
    classDef server fill:#bfb,stroke:#333,stroke-width:2px,color:#000;
    classDef admin fill:#fbb,stroke:#333,stroke-width:2px,color:#000;
    classDef delivery fill:#fbf,stroke:#333,stroke-width:2px,color:#000;

    A[1. Browse Menu]:::offline -->|Offline Resilient| B(2. Add to Cart)
    B --> C{3. Network Check}:::checkout
    
    C -->|Offline| D[Block Checkout: Alert User]:::offline
    C -->|Online| E[4. Checkout Form]:::checkout
    
    E -->|Submit| F{5. Server Validation}:::server
    F -->|Fails Regex/Price Check| G[Reject Order / Error 422]
    F -->|Passes Validation| H[6. Process Payment Gateway]:::server
    
    H --> I[(7. Database: PENDING_VERIFICATION)]:::server
    I -->|OneSignal Alert to Admin| J{8. Phone Verification}:::admin
    
    J -->|Failed/Fake| K[Cancel Order & Refund]
    J -->|Verified| L[(9. Database: PREPARING)]:::server
    
    L --> M[10. Kitchen Finishes Baking]:::admin
    M --> N[(11. Database: OUT_FOR_DELIVERY)]:::server
    
    N -->|OneSignal Alert to Customer & Boy| O[12. Delivery Routing via Google Maps]:::delivery
    O --> P{13. Delivery Boy Enters OTP}:::delivery
    P -->|Invalid OTP| O
    P -->|Valid OTP| Q[(14. Database: DELIVERED)]:::server
    
    Q -->|Hostinger Email / Push| R[15. Review Request]:::delivery
```

## 3. Technology Pipeline Summary

*   **Frontend (The Face):** Cloudflare Pages + Service Workers (Offline browsing) + Google Maps Autocomplete + OneSignal Web Push.
*   **Security (The Shield):** Cloudflare WAF + Origin Certificate (End-to-End SSL) + Server-side Regex validation.
*   **Backend (The Brain):** Hostinger KVM 2 VPS (Node.js/Express) + strict state machines + Payment Gateway logic.
*   **Database (The Memory):** PostgreSQL/MongoDB + 3:00 AM Cron Job sending Zipped dumps to Google Drive via `rclone`.
*   **Communication (The Voice):** Hostinger Business Email (`hello@cafe.com`) + OneSignal Alerts + Manual Verification Calls.
