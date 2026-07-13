# Security Workflow

## 1. Anti-Hack Mechanisms
*   **Input Sanitization:** All user inputs (especially Name and Address during checkout) will be strictly sanitized to prevent SQL Injection (SQLi) and Cross-Site Scripting (XSS).
*   **CSRF Protection:** Anti-CSRF tokens for all state-changing requests (like adding to cart or checking out).
*   **Rate Limiting:** Protect APIs against brute-force attacks and spamming (especially the checkout and login endpoints).
*   **Authentication Security:** JWT with short expirations, securely stored in HttpOnly cookies, not LocalStorage.

## 2. Anti-Crash & High Availability
*   **DDoS Protection:** Routing traffic through Cloudflare or AWS Shield to absorb volumetric attacks.
*   **Load Balancing:** Distributing traffic across multiple server instances during peak times (e.g., Valentine's Day, Mother's Day).
*   **Database Replication:** Primary database for writes, read-replicas for faster querying of menus without locking the main DB.

## 3. Data Protection
*   **Encryption:** All customer data (Name, Phone, Address) is encrypted at rest in the database.
*   **HTTPS Only:** Strict Transport Security (HSTS) ensuring all connections are encrypted via TLS/SSL.
*   **Automated Off-Site Backups:** A custom server script will dump, compress, and securely upload the entire database to a private Google Drive account every night at 3:00 AM, ensuring zero data loss even in a catastrophic server failure.
