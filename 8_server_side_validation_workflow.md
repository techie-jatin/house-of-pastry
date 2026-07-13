# Server-Side Validation Workflow

## Overview
Never trust the client. Even if the UI strictly enforces requirements, the server MUST double-check everything to prevent tampered requests.

## 1. Checkout Validation Pipeline
*   **Payload Schema Validation (e.g., using Zod or Joi):**
    *   `name`: String, min length 2, max length 100, no special characters (HTML tags).
    *   `phone`: Must match exact Regex for valid mobile numbers (e.g., strictly 10 digits for India `^[6-9]\d{9}$`).
    *   `address`: String, min length 10, cannot be purely numeric.
*   **Action on Failure:** Immediately return a `422 Unprocessable Entity` response detailing exactly which field failed.

## 2. Cart & Inventory Validation
*   **Price Tampering Check:** The server will recalculate the cart total based on the database's price list, ignoring any prices sent from the client UI.
*   **Stock Availability:** Before finalizing the order state to `PENDING_VERIFICATION`, a strict lock/check is performed on inventory for limited items.

## 3. Custom Cake Validation
*   Validating that selected options (e.g., 2kg, Sugar-Free) are a valid combination allowed by the system before accepting the order.

## 4. Delivery OTP Validation
*   **Strict Matching:** The server strictly validates that the 4-digit OTP sent by the delivery boy perfectly matches the one stored in the database for that specific `order_id`.
*   **Brute-Force Protection:** The server will rate-limit OTP attempts. If a delivery boy enters the wrong OTP 5 times, the endpoint locks out and alerts the Cafe Admin to investigate potential fraud.
