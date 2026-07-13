# Legal & Compliance Workflow

## Overview
To protect both the cafe and the developers, the platform must adhere to strict legal guidelines, especially concerning food delivery and customer data privacy.

## 1. Mandatory Legal Pages
The platform (Customer PWA) must have a dedicated footer containing links to the following legal pages:

*   **Terms & Conditions (T&C):** Placed persistently in the footer. Explicitly states that by browsing the PWA or placing an order, the customer legally agrees to the Terms of Service governing the cafe.
*   **Privacy Policy:** Since the platform *strictly* collects sensitive PII (Personally Identifiable Information) like Full Name, Phone Number, and Exact GPS Address, this policy must explain how data is stored, encrypted, and not sold to third parties.
*   **Refund & Cancellation Policy (The Concu Standard):** **Crucial for bakeries.** Implementing the strict, industry-standard timeline used by top-tier bakeries like Concu:
    *   **36-Hour Window:** Defects or damages for regular items must be reported within 36 hours.
    *   **Same-Day Shelf Life:** For highly perishable pastries or custom cakes, defects MUST be reported on the exact **same day**. After this window passes, absolutely no refunds or exchanges are permitted.
*   **Shipping/Delivery Policy:** Explains delivery radii, estimated times, and "Force Majeure" clauses (e.g., delays due to heavy rain or traffic).

## 2. Food Safety Compliance (India)
Since the cafe operates in India (like Concu):
*   **FSSAI Display:** The Food Safety and Standards Authority of India (FSSAI) mandates that the cafe's 14-digit FSSAI registration/license number must be visibly displayed on the platform (usually in the footer or checkout page).

## 3. Digital Consent & Cookies
*   **Web-Push Opt-In:** Before OneSignal can send notifications, the platform must display a clear prompt asking for permission to send order updates.
*   **Cookie Banner:** A small banner notifying users that local storage/cookies are used to maintain their cart session.

## 4. Platform Attribution & Intellectual Property
To ensure proper credit is given to the architect and developer of this custom, enterprise-grade solution:

*   **Developer Attribution:** The footer of both the Customer PWA and Admin Dashboard will contain a permanent attribution link pointing to your Instagram: 
    *   [`Designed & Engineered by techie.jatin`](https://www.instagram.com/techie.jatin)
*   **Technology Licensing:** While the cafe owns the customer data and the branding/images, the underlying source code, architecture, and workflow logic remain the intellectual property of **techie.jatin** (unless explicitly transferred via a separate buyout contract). This protects your code from being resold by the client without your permission.
