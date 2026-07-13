# Delivery Fleet PWA: Sitemap & Pages Architecture

## Overview
The Delivery Fleet PWA is designed for speed, safety, and battery efficiency. It is an isolated app used only by the cafe's delivery boys. The interface is highly simplified to ensure they can operate it with one hand while on a motorcycle.

## 1. Authentication
*   **1. Rider Login (`/login`)**
    *   *Purpose:* Secure access for authorized riders only.
    *   *Components:* Phone Number + OTP. 
    *   *Strict Constraint:* No registration. Only phone numbers manually added by the Manager (in the Admin Dashboard) can log in.

## 2. Core Operations
*   **2. Rider Dashboard (`/`)**
    *   *Purpose:* The home screen and status toggle.
    *   *Components:*
        *   **Duty Toggle:** A massive "Go Online / Offline" toggle. When online, the Admin Dashboard shows them as 'Available' to receive orders.
        *   **Current Assignment:** If the Manager assigns them an order, it pops up here with a loud notification sound.
*   **3. Active Delivery Map (`/delivery/:id`)**
    *   *Purpose:* Navigation, Status Updates, and Customer Contact.
    *   *Components:*
        *   **Customer Details:** Name and Address.
        *   **Granular Status Buttons:** Large, high-contrast buttons for "Picked Up" and "Reached Customer Location" (these instantly update the Customer PWA tracking timeline).
        *   **Navigation Button:** "Open in Google Maps" which automatically launches the Maps app with the destination pre-loaded.
        *   **Contact Button:** "Call Customer" for when they reach the gate.
        *   **COD Alert:** If the order is Cash on Delivery, a massive red banner displays: **"COLLECT CASH: ₹XXX"**.

## 3. Security & Fulfillment
*   **4. OTP Verification (`/verify/:id`)**
    *   *Purpose:* Anti-theft and strict proof of delivery.
    *   *Components:* A large keypad interface. The rider *must* ask the customer for the 4-Digit OTP displayed on the customer's tracking screen. If it is a COD order, the screen forces the rider to confirm "Cash Collected" before typing the OTP.

## 4. History & Cash Settlement
*   **5. Delivery & Cash Log (`/history`)**
    *   *Purpose:* End-of-day reconciliation.
    *   *Components:* A simple list of all successful deliveries they completed that day, plus a bold **Total Floating Cash** tracker. *Crucial Logic:* This cash balance does NOT reset at midnight. It continues to accumulate across days until the Rider physically hands the cash to the Manager, and the Manager clicks "Settle Cash" on the Admin Dashboard to reset their balance to ₹0.
