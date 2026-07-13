# Kitchen Display System (KDS): Sitemap & Pages Architecture

## Overview
The Kitchen PWA (KDS) is a completely isolated, locked-down application designed exclusively for the chefs. It has one purpose: show what needs to be baked right now. It contains absolutely zero financial data, zero pricing, and zero administrative controls.

## 1. Authentication
*   **1. Chef Login (`/login`)**
    *   *Purpose:* Secure entry.
    *   *Components:* Phone Number & OTP login. Only phone numbers registered by the Manager in the Admin Dashboard will be granted access.

## 2. Core Kitchen Operations
*   **2. The KDS Board (`/`)**
    *   *Purpose:* The only screen the chefs ever look at. Mounted on a tablet in the kitchen.
    *   *Components:*
        *   **Audio Alerts:** When a new order is pushed from the Admin to the Kitchen, a **soft bell sound** plays to grab the chefs' attention. A "Mute/Unmute" toggle is permanently visible on the top navigation bar.
        *   **Standard Orders Queue:** A grid of incoming standard orders. Each "Order Card" follows strict operational logic:
            *   *Color-Coded Timers:* Every card has a live timer. It starts **Green**. If an order is unfulfilled for 15+ minutes, the card turns **Yellow** (Warning). Past 25 minutes, it flashes **Red** (Late).
            *   *Content:* Massive, ultra-readable typography showing the Quantity, Item Name (e.g., "2x Chocolate Truffle"), and Dietary Tag (Veg/Egg).
            *   *Action:* When baked, the chef taps a massive **'Mark as Ready'** button. This instantly removes the card from the screen, changes the database state to `READY_FOR_PICKUP`, and pings the Delivery Boy.
        *   **Custom Cake Queue:** A highlighted section for complex orders that have passed Manager Approval. Since Custom Cakes take hours or days to prepare, they follow a completely different operational logic:
            *   *No Flashing Timers:* Custom cakes do not use the 15-minute Green/Yellow/Red timer system. Instead, they display the exact **Target Delivery Date & Time** in bold.
            *   *Content:* The card displays a high-res thumbnail of the customer's uploaded reference image alongside the Manager's exact typed instructions (e.g., "No strawberries, add gold flakes").
            *   *Action (Quality Control):* Because custom cakes are highly specialized, tapping **"Mark as Ready"** does *not* instantly ping the delivery boy. Instead, it changes the state to `READY_FOR_MANAGER_REVIEW`. This alerts the Manager to physically inspect the final cake to ensure it matches the customer's request before they manually dispatch it to the fleet.

*   **3. Sold Out / Ingredients Toggle (`/availability`)**
    *   *Purpose:* Fast communication with the front desk.
    *   *Components:* The chef navigates to this tab and sees a simple, alphabetically sorted checklist of core ingredients and pre-made bases (e.g., "Chocolate Batter", "Fresh Strawberries", "Vanilla Extract"). 
        *   **Search & Toggle UX:** To keep things fast, there is a large search bar at the top. The chef types "Straw", taps the toggle switch next to "Fresh Strawberries" from Green (In Stock) to Grey (Out of Stock).
        *   The moment they flip that switch, it instantly fires the blinking alert to the Admin Dashboard.
