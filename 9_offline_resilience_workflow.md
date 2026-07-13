# Offline Resilience Workflow

## Overview
To make the platform extremely user-friendly, it will be built as a Progressive Web App (PWA) that handles flaky network connections gracefully.

## 1. Service Worker Caching
*   **Static Assets:** HTML, CSS, JavaScript, and UI icons are cached locally.
*   **Menu Caching:** The product catalog and images are cached using a `Stale-While-Revalidate` strategy. Customers can browse the menu even in airplane mode.

## 2. Offline UI States
*   If the user's connection drops, a subtle, non-intrusive banner appears: "You are browsing offline. Prices and availability may not be up to date."
*   **Cart Actions:** Users can add items to their local cart while offline.

## 3. Checkout Resilience
*   **Network Check:** At the exact moment of checkout, the app checks for a live connection.
*   **Blocker:** Since the platform strictly requires real-time phone verification and immediate kitchen dispatch, offline checkout is **prevented**. The UI will state: "Please connect to the internet to place your order so we can call and verify your details."
