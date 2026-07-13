# Server-Side Workflow

## Overview
The server handles complex states, from strict data validation during checkout to managing the live status of the delivery fleet. 

## 1. Database Architecture (Key Entities)
*   **User/Customer:** id, name, email, phone, created_at, is_first_order, wallet_balance, deletion_requested (boolean)
*   **Category/Collection:** id, name, sort_order (integer), is_active (boolean)
*   **Ingredient Watchlist:** id, name, in_stock (boolean)
*   **SavedAddress:** id, user_id, tag (Home/Work/Other), flat_no, building_street, landmark, lat, lng, is_default
*   **Product:** id, name, description, price, type (regular/custom), in_stock, category_id, dietary_preference (veg/egg/non-veg)
*   **Coupon:** id, code, discount_type (flat/percent), discount_value, max_cap, min_order_value, start_date, end_date, target_audience, usage_limit, is_public
*   **Order:** id, user_id, total_amount, coupon_id, discount_applied, status, payment_method (ONLINE/COD), payment_status (PAID/PENDING), delivery_boy_id, delivery_address, phone_to_call, delivery_otp
*   **Review:** id, order_id, user_id, rating (1-5), comment, created_at
*   **WalletTransaction:** id, user_id, amount (positive/negative), transaction_type (REFUND, PURCHASE), related_order_id, created_at
*   **DeliveryBoy:** id, name, phone, current_status, floating_cash

## 2. API Workflows

> *Security Note:* All API endpoints are protected by `express-rate-limit`. Input parameters are strictly sanitized via parameterized queries (anti-SQLi) and `DOMPurify` (anti-XSS) before hitting business logic. Any file uploads are scanned for Magic Bytes (anti-malware).

*   **POST /api/orders/checkout:**
    *   *Input:* Cart items, Customer Name, Phone, Address, Coupon Code (optional).
    *   *Process:* Validates inputs. If a coupon is provided, strictly validates expiration, Minimum Order Value, and category restrictions. Calculates the `discount_applied` up to the max cap. Calculates final total, saves order as `PENDING_VERIFICATION`, and triggers notifications.
*   **PUT /api/orders/{id}/verify:**
    *   *Process:* Called by staff after phone verification. Updates state to `PREPARING`.
*   **PUT /api/orders/{id}/assign:**
    *   *Input:* delivery_boy_id
    *   *Process:* Admin assigns a delivery boy. Order becomes `READY_FOR_PICKUP` when the kitchen finishes it. Generates a 4-digit `delivery_otp` and notifies customer and delivery boy.
*   **PUT /api/orders/{id}/status:**
    *   *Process:* Delivery boy presses Granular Status buttons. State moves from `PICKED_UP` -> `REACHED_LOCATION`.
*   **PUT /api/orders/{id}/deliver:**
    *   *Input:* delivery_otp
    *   *Process:* Validates the OTP. If valid, updates state to `DELIVERED`. If COD, marks order as `PAID` and adds the order amount to the DeliveryBoy's `floating_cash`.
*   **POST /api/fleet/{id}/settle-cash:**
    *   *Process:* Used at the end of a shift. The Manager clicks "Settle" to confirm they received physical cash, resetting the rider's `floating_cash` to 0.
*   **POST /api/orders/{id}/refund:**
    *   *Input:* `refund_amount` (can be full or partial amount).
    *   *Process:* Admin initiates a partial or full refund. If `payment_method == ONLINE`, it triggers the Razorpay/Stripe refund API for that exact `refund_amount`. If `payment_method == COD`, that exact `refund_amount` is credited to the customer's `wallet_balance` (Store Credits).
*   **POST /api/users/request-deletion:**
    *   *Process:* Customer requests deletion. Sets `deletion_requested = true`.
*   **POST /api/admin/approve-deletion/{id}:**
    *   *Process:* Admin approves deletion. Permanently wipes the user's PII, order history, and wallet balance.
*   **POST /api/admin/cancel-deletion/{id}:**
    *   *Process:* Admin successfully retains the customer. Sets `deletion_requested = false` to restore the account.

## 3. Order State Machines
The server strictly enforces that orders can only move in sequential states. To handle custom requests, we have split the logic into two distinct pipelines:

### A. Standard Order Pipeline (Menu Items)
1.  **`PENDING_VERIFICATION`**: Order hits the dashboard.
2.  **`PREPARING`**: Staff confirms on phone and pushes it directly to the kitchen.
3.  **`OUT_FOR_DELIVERY`**: Handed to delivery boy.
4.  **`DELIVERED`**: OTP verified.
### B. Custom Cake Pipeline (The Safety Net)
Standard orders go straight to the kitchen. **Custom Cakes do not.**
1.  **`CUSTOM_REQUEST_RECEIVED`**: Sits in the Manager's queue.
2.  **`PENDING_CUSTOMER_AGREEMENT`**: The Manager calls the customer to negotiate the design. They can override the final price here.
3.  **`PREPARING`**: After clicking "Accept", the order officially drops into the Kitchen's queue.
4.  **`READY_FOR_MANAGER_REVIEW`**: Kitchen finishes baking. Order hits a hard stop. The Manager inspects the physical cake against the customer's photo.
5.  **`READY_FOR_PICKUP`**: Manager approves quality. Delivery boy notified.
6.  **`DELIVERED`**: Same as standard pipeline.
7.  *(Alternative)* **`REJECTED`**: If the design is impossible, the manager rejects it before the kitchen ever sees it.
## 4. Background Jobs & Algorithms
*   **Fleet Auto-Assignment Algorithm (FIFO):** When an order state changes to `READY_FOR_PICKUP`, the backend queries the `DeliveryBoy` database for anyone with `current_status == AVAILABLE`. It automatically assigns the order to the rider who has been waiting at the cafe the longest. (The Manager can always manually override this assignment via the Admin Dashboard).
*   **Stuck Order Alert:** Cron job to automatically flag orders stuck in `PENDING_VERIFICATION` for more than 15 minutes to alert staff to call again.
*   **Delayed Review Push Notification:** A scheduled background task (e.g., via BullMQ/Redis). Exactly 11 minutes after an order is marked `DELIVERED`, the server sends a customized Push Notification (e.g., "Hey John, how was your Custom Cake?") to bring the customer back to the app to submit a review if they missed the immediate post-delivery screen.
