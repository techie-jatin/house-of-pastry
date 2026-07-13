# Admin Dashboard (PWA): Sitemap & Pages Architecture

## Overview
The Admin Dashboard is the mission control for the cafe staff. Unlike the Customer PWA which focuses on aesthetics, the Admin PWA focuses purely on **speed, utility, and real-time data**. It must handle incoming orders instantly and manage the delivery fleet without friction.

## 1. Authentication
*   **1. Secure Staff Login (`/login`)**
    *   *Purpose:* Gateway for staff.
    *   *Components:* Email/Password or OTP login (Firebase/Auth0). 
    *   *Strict Security Constraint:* There is **no signup or registration page**. A Manager ID/Account can *only* be created manually at the database level by the system developer (`techie.jatin`). This ensures absolute security and prevents anyone from creating rogue admin accounts.

## 2. Core Operations (The Front Desk)
*   **2. Live Orders Dashboard (`/` or `/live-orders`)**
    *   *Purpose:* The main screen that is always kept open on the cafe's iPad or desktop.
    *   *Components:* Two distinct columns/tabs (Notice: The Kitchen now has its own isolated PWA, so `Preparing` is hidden from the front desk default view):
        1.  **Pending Verification:** Blinking visual alerts and a **soft bell sound** for incoming new orders. A prominent "Mute/Unmute Audio Alerts" toggle is pinned to the top of the dashboard so staff can quickly silence it during busy rushes. Staff clicks here to see the phone number, calls the customer, and clicks "Approve & Send to Kitchen".
        2.  **Live Fleet Tracking:** Granular tracking of orders currently in transit. The manager can see exactly if an order is `Picked Up`, `Reached Customer Location`, or `Delivered`.
*   **3. Custom Cake Requests (`/custom-cakes`)**
    *   *Purpose:* A dedicated queue to manage highly specialized, custom orders to ensure 100% accuracy.
    *   *Workflow Components:* 
        *   **Customer Verification Call:** A prominent "Call Customer" button linked to their provided phone number. The manager *must* call the customer to discuss details before proceeding.
        *   **Requirement Checklist & Notes:** A digital notepad built into the order card where the manager types out the exact, agreed-upon requirements while on the phone with the customer.
        *   **Dynamic Price Adjustment:** If the customer's request requires more expensive ingredients (e.g., adding fresh strawberries or gold flakes), the manager has an "Edit Price" input field to instantly update the final cost of the order while on the phone.
        *   **Reference Viewer:** High-res image viewer to see the customer's uploaded reference image.
        *   **Final Approval:** Once the call is finished, the price is adjusted, and requirements are logged, the manager clicks "Accept & Send to Kitchen" or "Reject" (if the design is impossible).
        *   **Quality Control Queue (Manager Review):** When the Kitchen finishes baking a custom cake, it reappears here with a blinking alert. The manager inspects the physical cake, compares it to the customer's reference image on the screen, and clicks "Approve & Dispatch" to finally ping the delivery fleet.

## 3. Inventory & Fleet Management
*   **4. Menu & Inventory Manager (`/menu-manager`)**
    *   *Purpose:* Immediate control over what customers can buy and see.
    *   *Components:* 
        *   **Collection / Category Manager:** A dedicated tool for the manager to Create, Rename, Hide, or Re-order the categories (e.g., "Signature Cakes", "Pastries"). The exact order set here dynamically dictates how the categories appear on the Customer PWA's Left Sidebar. 
        *   **Ingredient Watchlist Manager:** A simple tool where the Admin can type in the names of critical raw ingredients (e.g., "Strawberries", "Gold Flakes", "Vanilla Batter"). Whatever the Admin adds to this list is instantly synced and displayed as a toggle switch on the Kitchen PWA's Availability screen.
        *   **Image Uploader:** A drag-and-drop tool allowing managers to upload and update product photos directly from the dashboard. *UI Constraint:* The uploader will explicitly instruct the manager to upload **.AVIF** format images to guarantee ultra-high quality at the lowest possible file size, ensuring the Customer PWA loads instantly. (Images are securely pushed to Cloudflare R2).
        *   **Inventory Control (Items & Ingredients):** Toggle switches to instantly mark finished items as "Out of Stock" (reflects live on the Customer PWA). Additionally, this dashboard receives **live Ingredient Shortage Alerts** directly from the Kitchen PWA. If a chef toggles an ingredient (like "Strawberries") to 'Off', a blinking alert appears here so the Manager can instantly mark any dependent cakes as Out of Stock.
        *   **Product Details Editor:** The manager has full control over what appears on the Customer PWA's Product Details page. They can edit the Title, Description, Price, and crucially, set the **Dietary Tag (100% Veg / Contains Egg / Non-Veg)** which drives the frontend filters, plus any specific allergen warnings (e.g., *Contains Nuts*).
*   **5. Delivery Fleet Control (`/fleet`)**
    *   *Purpose:* Managing the delivery boys.
    *   *Components:* List of all delivery boys, their current status (Available vs. Delivering), and manual override buttons to assign or re-assign specific orders to specific boys.

## 4. Analytics, CRM & Configuration
*   **6. Order History & Refunds (`/history`)**
    *   *Purpose:* Customer service and records.
    *   *Components:* Searchable database of all past orders, buttons to initiate partial or full refunds (in case of damages).
*   **7. Promotion & Coupon Engine (`/promotions`)**
    *   *Purpose:* Marketing and Retention.
    *   *Components:*
        *   **Coupon Creator:** Set Discount Math (Flat vs Percentage with Max Caps), Minimum Order Value (MOV), Expiry dates, and Usage Limits.
        *   **Targeting Rules:** Restrict by Category (e.g., *Not valid on Custom Cakes*), Audience (First-Time vs All), and Time/Day (Happy Hours).
        *   **Visibility Toggle:** Set to 'Public' (shows in Customer Cart Drawer) or 'Hidden' (Secret Influencer Code).
        *   **Scratch Card Manager:** Toggle to enable/disable Post-Purchase gamified scratch cards.
        *   **Banners & Announcements Manager:** An image uploader where the manager can upload "Hero Banners" (e.g., a "Diwali Special" graphic) to display on the Customer PWA homepage carousel. They can also type a short text-based "Sticky Announcement Bar" (e.g., "Free Delivery this Weekend!") that pins to the top of the customer's screen.
*   **8. Business Analytics & Reports (`/analytics`)**
    *   *Purpose:* Enterprise-grade business intelligence to track growth and performance.
    *   *Components (High-End Tech Stack):* Built using **Recharts or D3.js** for ultra-smooth, 60fps interactive data visualization. It uses real-time WebSockets so the Daily Revenue charts, Best-Selling Item metrics, and Average Order Value (AOV) update instantly without the manager ever needing to refresh the page. Includes a secure backend microservice to generate downloadable CSV/PDF GST tax reports.
*   **9. Customer Ratings & CRM (`/reviews`)**
    *   *Purpose:* Quality control and Data Privacy.
    *   *Components:* 
        *   **Live Feedback:** See live feedback on deliveries and resolve customer complaints instantly.
        *   **Account Deletion Requests:** A queue of customers who have requested permanent account deletion. Next to every request is a **"Call Customer"** button. This allows the manager to contact the user and find out *why* they are leaving (retention strategy). If the manager successfully convinces them to stay, they click **"Cancel Request"** to restore the account. If the customer insists on leaving, the manager clicks **"Approve"** to permanently wipe their PII, wallet balance, and order history.
*   **10. Staff Roles & Permissions (`/staff`)**
    *   *Purpose:* Employee Provisioning & Security.
    *   *Components:* An "Add New Employee" form. While only the Developer can create a *Manager* account, the Manager uses this page to create accounts for **Kitchen Staff** and **Delivery Boys**. The manager inputs the employee's phone number and assigns a role. 
        *   *Kitchen Staff:* Logs into the completely isolated **Kitchen PWA (KDS)** using their phone number. They cannot access the Admin URL.
        *   *Delivery Boys:* Logs into the completely isolated **Delivery PWA** using their phone number (via OTP) to receive assignments.
*   **11. Cafe Settings (`/settings`)**
    *   *Purpose:* Global controls and Financial configurations.
    *   *Components:* 
        *   **Master Kill Switch:** "Accepting Orders" (Yes/No) - to shut down the Customer PWA during emergencies or closing time.
        *   **Billing & Taxation Configuration:** A dedicated tab to manage global pricing rules, including setting the **GST/Tax %**, defining the **Delivery Fee** (flat rate or per-km multiplier), setting a **Free Delivery Threshold** (e.g., "Free Delivery on orders over ₹1500"), and setting the default base surcharges for Custom Cakes.
        *   **Global Cafe Information:** Text fields to instantly update the cafe's public-facing **Support Phone Number**, Physical Address, and Business Hours. (Updates here reflect instantly on the Customer PWA).
        *   Notification sound settings (for incoming order alerts).
