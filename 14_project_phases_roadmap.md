# Project Phases & Roadmap

This document breaks down the entire platform development into logical, manageable phases. Use these checklists to maintain a strict record of work progress and report milestones to your client.

---

## Phase 1: Infrastructure & Foundation
**Goal:** Establish the servers, domains, and base codebase.

- [ ] Purchase Hostinger KVM 2 VPS and Domain (Namecheap/GoDaddy).
- [ ] Connect Domain to Cloudflare (Setup DNS, Proxy, and Universal SSL).
- [ ] Generate and install Cloudflare Origin Certificate on VPS for strict End-to-End Encryption.
- [ ] Install Database (PostgreSQL or MongoDB) on VPS.
- [ ] Initialize Git repositories (Frontend & Backend) and basic CI/CD pipeline.
- [ ] Review and import existing UI/UX components from **Stitch**.

---

## Phase 2: Core Backend & APIs
**Goal:** Build the "Brain" of the operation.

- [ ] Setup Authentication (Firebase Auth / Auth0).
- [ ] Create Database Schemas (Users, Products, Orders, Delivery Boys).
- [ ] Build Menu & Product APIs (Support for standard items and Custom Cakes).
- [ ] Build Checkout API with strict server-side Regex validation (Phone, Name, Address).
- [ ] Implement Order State Machine (`PENDING_VERIFICATION` -> `PREPARING` -> `OUT_FOR_DELIVERY` -> `DELIVERED`).
- [ ] Generate 4-digit `delivery_otp` upon assigning a delivery boy.

---

## Phase 3: Customer Web App (PWA)
**Goal:** The customer-facing storefront and checkout experience.

- [ ] Build Hero Section & Menu browsing interface.
- [ ] Build Custom Cake Wizard (Step-by-step UI).
- [ ] Build Checkout Form ensuring Name, Phone, and Address are strictly mandatory.
- [ ] Integrate Google Maps Places Autocomplete for precise address input.
- [ ] Build Live Order Tracking page featuring the bold **4-digit Delivery OTP**.
- [ ] Implement Service Workers for Offline Resilience (Caching static assets and menu).

---

## Phase 4: Admin Dashboard & Delivery PWA
**Goal:** Tools for the cafe staff and delivery fleet.

- [ ] **Admin Dashboard:** Create real-time incoming order view (Alerting staff for Phone Verification).
- [ ] **Admin Dashboard:** Order management UI (Approve, Assign Delivery Boy, Cancel).
- [ ] **Delivery App (PWA):** Build mobile PWA for delivery boys showing customer details, offline caching, and map routing.
- [ ] **Delivery App:** Build OTP input screen.
- [ ] Connect OTP input to Backend API (Ensure brute-force lockout after 5 wrong attempts).

---

## Phase 5: Notifications, Security & Backups
**Goal:** Bulletproof the platform and automate communications.

- [ ] Integrate **OneSignal** Web-Push for Customer and Delivery Boy alerts.
- [ ] Configure Hostinger Business Email (`hello@cafe.com`).
- [ ] Write and schedule the 3:00 AM Cron Job + `rclone` script for automated **Google Drive Backups**.
- [ ] Conduct security audit (Check Rate Limiting, CSRF protection, and Cloudflare WAF).

---

## Phase 6: QA, Testing & Launch
**Goal:** Final checks before handing over to the client.

- [ ] End-to-End (E2E) testing of a complete order (Customer -> Admin -> Delivery Boy -> Delivered).
- [ ] Simulate offline drops to ensure the PWA handles it gracefully.
- [ ] Client Demo and Walkthrough.
- [ ] **Go Live!**
