# Delivery System Workflow

## Overview
The platform is explicitly built for a **Delivery-Only** model using an in-house delivery fleet. The workflow enforces strict data collection and manual phone verification before order processing.

## Step-by-Step Workflow

### 1. Customer Ordering Phase
*   **Cart & Checkout:** Customer adds items (pastries/custom cakes) to the cart.
*   **Mandatory Data Collection:** Customer proceeds to checkout. The system **strictly** requires:
    *   Full Name
    *   Phone Number
    *   Exact Delivery Address (with GPS pin drop if possible)
*   **Blocker:** If any of the above information is missing, the checkout button remains disabled. No order is accepted without complete details.

### 2. Order Placement & Pending State
*   Order is placed and saved in the database with a status of `PENDING_VERIFICATION`.
*   Customer sees an "Order Received" screen notifying them that a Cafe Representative will call them shortly to confirm the order.

### 3. Cafe Verification Phase
*   **Dashboard Alert:** Cafe staff receives an instant notification (sound + popup) on their admin dashboard.
*   **Phone Call:** Staff calls the customer's provided phone number.
*   **Action:** 
    *   If verified: Staff clicks "Approve Order" in the dashboard. Order status changes to `PREPARING`.
    *   If unverified/unreachable: Staff clicks "Hold" or "Cancel".

### 4. Preparation & Dispatch
*   Kitchen prepares the order.
*   Once ready, staff assigns the order to an available in-house Delivery Boy via the dashboard.
*   Status changes to `OUT_FOR_DELIVERY`.

### 5. Delivery Phase
*   **Delivery Boy Interface (PWA):** The assigned delivery boy accesses a dedicated Progressive Web App (PWA) to see the customer's name, phone, and exact address on their mobile device.
*   **Delivery OTP (Anti-Fraud):** The customer's order tracking page displays a unique 4-digit OTP. 
*   Upon reaching the address, the delivery boy must ask the customer for this OTP and enter it into their app.
*   If the OTP is correct, the system successfully marks the order as `DELIVERED`.
*   Final notification is sent to the customer.
