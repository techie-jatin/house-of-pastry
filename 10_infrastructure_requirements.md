# Infrastructure Requirements

## 1. Hosting & Servers
*   **VPS / Cloud Provider:** Hostinger VPS (Specifically the **KVM 2 Plan**: 2 vCPU cores, 8 GB RAM, 100 GB NVMe, 8 TB bandwidth). This provides the perfect "sweet spot" of RAM and CPU to run the database and backend simultaneously.
*   **Database Hosting & Backups:** PostgreSQL or MongoDB installed directly on the Hostinger VPS to save costs. An automated Custom Cron Job will run daily at 3:00 AM to zip the database and securely upload it to **Google Drive (15GB Free)** using a tool called `rclone`, guaranteeing 100% free, off-site daily backups.

## 2. Domains & Networking
*   **Domain Registrar:** Namecheap or GoDaddy to acquire the domain.
*   **DNS, CDN & SSL:** Cloudflare for DNS management, Frontend SSL provisioning, and Content Delivery Network (CDN). We will strictly use a **Cloudflare Origin Certificate** installed on the VPS Backend to ensure End-to-End Encryption (Full Strict Mode) at zero cost.

## 3. Third-Party Services
*   **Authentication:** Firebase Auth or Auth0 (simplifies secure login via OTP/Phone Number).
*   **Business Email:** Hostinger Email (Included free or at a very low cost with the Hostinger account, e.g., `hello@cafe.com`) for professional communication.
*   **Push Notifications:** OneSignal (Free Version) to send automated web-push order updates to customers, completely eliminating SMS costs.
*   **Mapping/Location:** Google Maps API (Places Autocomplete & Geocoding) to ensure customers input accurate addresses for the delivery boys. (Effectively free due to the $200 monthly free tier credit, covering ~70,000 autocomplete requests).
*   **Storage:** AWS S3 for storing high-res product images and custom cake reference uploads.
