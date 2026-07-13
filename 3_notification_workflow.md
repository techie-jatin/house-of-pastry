# Notification Workflow

## Overview
Timely notifications are critical to keeping the customer informed and ensuring the cafe staff and delivery boys act quickly.

## 1. Customer Notifications
*   **Order Placed (Trigger: Checkout Complete):** 
    *   *Channel:* OneSignal Web-Push Notification
    *   *Message:* "Thank you for choosing [Cafe Name]. We have received your order #[ID]. Our staff will call you shortly to verify your details before processing."
*   **Order Verified (Trigger: Staff Approves):**
    *   *Channel:* OneSignal Web-Push Notification
    *   *Message:* "Your order #[ID] has been verified and is now being prepared! We will notify you once it's out for delivery."
*   **Out for Delivery (Trigger: Delivery Boy Assigned):**
    *   *Channel:* OneSignal Web-Push Notification
    *   *Message:* "Your delicious treats are on the way! Delivery associate [Name] is en route. Contact: [Phone]."
*   **Delivered (Trigger: Delivery Completed):**
    *   *Channel:* OneSignal Web-Push Notification / Email
    *   *Message:* "Your order has been delivered. Enjoy! Leave us a review at [Link]."

## 2. Cafe Staff Notifications
*   **New Order Alert:**
    *   *Channel:* Web Dashboard Push Notification & Sound Alert.
    *   *Action Required:* "New Order #[ID] received. Please call [Phone Number] to verify address: [Address]."
*   **Delivery Boy Status:**
    *   *Channel:* Dashboard Updates (Silent visual update when an order is marked delivered).

## 3. Delivery Boy Notifications
*   **New Delivery Assigned:**
    *   *Channel:* Mobile Web App Notification / OneSignal Web-Push
    *   *Message:* "New delivery assigned: Order #[ID]. Deliver to [Name] at [Address]."
