# Customer PWA: Sitemap & Pages Architecture

## Overview
To maintain a premium, Concu-style aesthetic while keeping the app fast and lightweight (PWA standard), the customer app will follow a streamlined route structure. The focus is on a high-conversion ordering funnel.

## 1. Primary Pages (The Core Funnel)

*   **1. Home / Landing Page (`/`)**
    *   *Purpose:* Visual hook and primary marketing surface.
    *   *Components:* 
        *   **Sticky Announcement Bar:** A thin banner at the absolute top of the app (managed dynamically via the Admin Dashboard) for urgent text promos (e.g., "Free Delivery this Weekend!").
        *   **Hero Promo Carousel:** An auto-scrolling image slider displaying high-res promotional banners (e.g., "Diwali Special").
        *   **Featured Items & CTAs:** Quick links to top-selling items and two clear Call-to-Actions (CTAs): "Order Now" and "Design Custom Cake".
*   **2. Standard Menu (`/menu`)**
    *   *Purpose:* The primary shopping catalog.
    *   *Components:* (Designed explicitly following the high-conversion Concu.in UI layout)
        *   **Dietary Filters (Top Bar):** Instant toggle switches to filter the entire menu by "100% Veg" (Green Dot) or "Contains Egg / Non-Veg" (Brown/Red Dot).
        *   **Category Sidebar (Left Side):** A sticky sidebar allowing users to jump instantly between Collections (e.g., "Signature Cakes", "Pastries", "Breads") without endless scrolling.
        *   **Product Grid (Right Side):** High-res grid of products displaying the respective dietary dot next to every title.
*   **3. Product Details Page (`/product/:id`)**
    *   *Purpose:* In-depth view.
    *   *Components:* Large image gallery, allergen information, description, and quantity selector.

## 2. Specialized Pages (The Premium Features)

*   **4. Custom Cake Wizard (`/custom-cake`)**
    *   *Purpose:* A guided, step-by-step builder to prevent user overwhelm.
    *   *Components:* 
        *   Step 1: Base & Flavor Selection.
        *   Step 2: Size & Weight (1kg, 2kg, etc.).
        *   Step 3: Text Input (Message on cake).
        *   Step 4: Image Reference Upload (Saved to Cloudflare R2 / AWS S3).
*   **5. The Cart (`/cart`)**
    *   *Purpose:* Review and Promotion application.
    *   *Components:* 
        *   **Item Review:** List of added items.
        *   **Coupon Engine UI:** An "Apply Coupon" manual input field, plus a "View Available Offers" button that opens a bottom-sheet drawer showing all active, eligible Public Coupons.
        *   **Calculations:** Strict server-validated price calculation (including dynamic recalculation when a coupon is applied) and a "Proceed to Checkout" button.

## 3. Security & Checkout Pages

*   **6. Checkout (`/checkout`)**
    *   *Purpose:* The final conversion step.
    *   *Components:*
        *   Mandatory fields: Name and Regex-validated Phone Number.
        *   **Saved Address Book:** A horizontal slider showing previously saved addresses (e.g., "Home", "Office"). Includes small "Edit" and "Delete" icons for quick adjustments. If selected, it skips the Google Maps setup entirely.
        *   **New Address Module (Two-Step):** 
            1. *Google Maps API:* An autocomplete search bar (or a "Locate Me" GPS button) to pinpoint the exact street/building on a map.
            2. *Manual Details:* A secondary set of text boxes for the customer to manually type their "Flat/House No.", "Floor", and "Nearby Landmark". Users can then save this address as 'Home' or 'Work'.
        *   **Payment Methods:** Razorpay/Stripe (Online), Cash on Delivery (COD) toggle, and an option to automatically deduct from their **Cafe Wallet Balance**.
*   **7. Live Order Tracking (`/track/:orderId`)**
    *   *Purpose:* Reduce anxiety and customer support calls.
    *   *Components:* A real-time, granular timeline (Order Received -> Preparing -> Picked Up -> Reached Location -> Delivered). Includes the Delivery Boy's name and a "Call Rider" button once picked up.
    *   *Crucial Feature:* Once the rider marks "Picked Up", a massive, bold **4-Digit Delivery OTP** appears on screen.
    *   *Instant Review UX:* The exact millisecond the delivery boy validates the OTP and the order hits `Delivered`, the tracking screen instantly morphs into a **"Rate Your Order"** modal. This captures feedback immediately while the customer is still holding their phone.
*   **8. Digital Invoice & Billing (`/bill/:orderId`)**
    *   *Purpose:* Post-delivery receipt and tax compliance.
    *   *Components:* A clean, digital breakdown of the final charges (Base price, Custom Cake surcharges, GST/Tax, Delivery Fee) and the cafe's FSSAI number. (View-only receipt; no download button provided).

## 4. User Account & Legal Pages

*   **9. Auth / Login & Register (`/login`)**
    *   *Purpose:* Frictionless entry and Legal Compliance. 
    *   *Components:* To maximize conversion rates, **Login and Registration are merged into a single flow**. The customer simply enters their phone number to receive an OTP. 
        *   If it's a new number, the system automatically registers them and asks for their Name.
        *   If it's an existing number, it instantly logs them into their profile.
        *   **Legal Consent:** Right below the "Send OTP" button, the UI will display standard compliance text: *"By continuing, you agree to our Terms of Service and Privacy Policy"*, with hyperlinks to the respective legal pages.
*   **10. Profile & Order History (`/profile`)**
    *   *Purpose:* Retention and Gamification.
    *   *Components:* 
        *   **Edit Profile:** Customers can update their Name and Email. The Phone Number is strictly locked to prevent auth hijacking.
        *   **Account Deletion:** A "Request Account Deletion" button. Clicking this triggers a severe warning prompt explaining that their Wallet Balance, Order History, and Saved Addresses will be permanently wiped. The request is sent to the Admin for approval.
        *   **Cafe Wallet:** Displays their current Store Credit balance (earned via COD refunds or special compensations). All credits are tracked via the `WalletTransaction` entity to ensure ledger integrity.
        *   **Order History & Reviews:** A list of past orders with a "One-Click Reorder" button. Next to every delivered order, there is a **"Rate Order"** button where customers can submit a 1-5 star rating and text feedback.
        *   **Address Book Management:** A dedicated section where users can view, edit (e.g., change the Flat number), or delete their saved addresses, or set a new "Default" address.
        *   **Scratch Card Center:** A section where users can view and "scratch" their post-purchase reward cards to reveal hidden coupons for their next order.
*   **11. Legal Footer Pages (`/terms`, `/privacy`, `/refunds`)**
    *   *Purpose:* Compliance and trust.
    *   *Components:* Static markdown-rendered pages. Includes standard FSSAI compliance info, privacy policy, and strict refund terms. 
    *   *Wallet Legalities:* The Terms & Conditions explicitly state that "Cafe Wallet Balances" are promotional Store Credits. They hold no real-world fiat value, cannot be withdrawn to a bank account, cannot be transferred to another user, and can only be used for in-app purchases.

## 5. Brand & Utility Pages (The Missing Pieces)

*   **12. About Us (`/about`)**
    *   *Purpose:* Premium bakeries rely on storytelling. This page builds immense trust by showing the history of the cafe, the chefs, and high-quality photos of the kitchen.
*   **13. Contact & Support (`/contact`)**
    *   *Purpose:* Fast customer service resolution.
    *   *Components:* A massive **"Call Us"** button (which instantly opens the customer's phone dialer to call the cafe directly), alongside the cafe's physical address (embedded Google Map) and operating hours. There is no contact form, eliminating the need for a complex Admin Inbox.
*   **14. 404 Not Found (`/404`)**
    *   *Purpose:* Graceful error handling. 
    *   *Components:* A friendly message ("Oops, this cake was already eaten!") and a button redirecting them back to the Menu.
