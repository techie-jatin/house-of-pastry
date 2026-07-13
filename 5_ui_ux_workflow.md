# UI/UX Workflow

## Overview
The goal is to replicate the premium, luxurious feel of Concu while optimizing for a strict delivery-only funnel.

## 1. Integration with Existing Stitch Codes
*   **Reusability:** We will utilize the existing UI/UX codes built in Stitch.
*   **Cross-Checking:** All Stitch components will be audited against the client's custom requirements (e.g., ensuring the checkout screen enforces Name, Phone, and Address). We will copy and adapt these files as the foundation.

## 2. The Customer "Hook" Strategy
Concu hooks customers through visual appeal and simplicity. We will implement:
*   **Hero Section:** High-resolution, mouth-watering imagery of signature pastries and custom cakes right on the landing page.
*   **Micro-Animations:** Smooth hover effects on product cards and fluid page transitions to convey a "premium" software feel.
*   **Simplified Navigation:** Clear calls to action: "Order Pastries" vs "Customize a Cake".

## 3. Custom Cake Workflow
*   Step 1: Choose Base/Flavor.
*   Step 2: Choose Size/Weight.
*   Step 3: Enter Custom Message.
*   Step 4: Image Reference Upload (Optional).
*   *UI Focus:* A seamless, step-by-step wizard format to avoid overwhelming the user.

## 4. Checkout Enforcements
*   The UI will clearly mark Name, Phone, and Address with red asterisks.
*   **Address Auto-complete:** Use Google Maps Places API for accurate address selection to aid delivery boys.
*   If a user tries to submit without these fields, clear, polite inline error messages will appear: "Please provide your phone number so we can verify your delicious order."

## 5. Live Order Tracking & OTP UI
*   **Post-Checkout Experience:** After ordering, the customer gets a beautiful tracking page (like Swiggy/Zomato).
*   **Delivery OTP Display:** Once the order is `OUT_FOR_DELIVERY`, the tracking page prominently displays a **4-digit Delivery OTP** in a large, bold font, instructing the customer to share it with the delivery boy to receive their package.
