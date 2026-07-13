# Security Architecture & Hardening Blueprint

## Overview
This document outlines the strict security protocols that must be implemented across the Frontend (React) and Backend (Node.js) to protect the cafe's platform from data breaches, injection attacks, and bot spam.

## 1. Input Sanitization (Textboxes)
### Threat: Cross-Site Scripting (XSS) & SQL Injection
Hackers may attempt to input malicious `<script>` tags into text fields (e.g., "Cake Message") to steal Admin cookies, or use SQL payloads (e.g., `' OR 1=1;--`) to dump the database.

*   **Backend Protocol:** 
    *   **Strict Validation:** Use `express-validator` to reject any payload that contains illegal characters.
    *   **Database Queries:** NEVER concatenate strings in SQL. Strictly use Parameterized Queries (via an ORM like Prisma or `pg-promise`) for every single database interaction.
*   **Frontend Protocol:**
    *   All user-generated text rendered on the Admin Dashboard or Customer PWA must be passed through `DOMPurify` before being injected into the DOM to completely neutralize XSS payloads.

## 2. File Upload Security
### Threat: Malware Execution
Hackers may upload a `.php` or `.exe` file disguised as a `.jpg` to execute code on the server.

*   **Protocol:**
    *   **Magic Bytes Validation:** Use `multer` combined with a package like `file-type` to inspect the actual binary signature (Magic Bytes) of the file. If a file claims to be a `.jpg` but the binary signature is an executable, reject it instantly.
    *   **Strict Allowlist:** Only allow `image/jpeg`, `image/png`, `image/webp`, and `image/avif`.
    *   **Size Limits:** Cap uploads at a hard 5MB limit.
    *   **Isolated Storage:** The Node.js server will never save files locally. Files will be streamed directly to an isolated Cloudflare R2 bucket. This bucket must have absolutely zero execute permissions.

## 3. Account Protection
### Threat: Brute Force Bots & Fake Accounts
Bots can spam the `/login` route to request OTPs, costing the business hundreds of dollars in SMS fees, or attempt to brute-force a login.

*   **Protocol:**
    *   **Firebase Authentication:** We will exclusively use **Google Firebase Auth** for Phone Number / OTP login. Firebase securely handles the sending of OTPs, automatically rate-limits abusive IPs, and securely stores the customer's phone number identity on Google's encrypted servers. Our backend never handles raw SMS gateways.
    *   **Backend JWT Verification:** Once Firebase authenticates the user on the frontend, the React app sends a Firebase JWT to our Node.js backend. Our backend strictly verifies this token using the Firebase Admin SDK before establishing a session.
    *   **CSRF Tokens:** Implement standard Cross-Site Request Forgery (CSRF) tokens for all state-mutating requests (`POST`, `PUT`, `DELETE`).

## 4. Admin System Security
### Threat: Unauthorized Privilege Escalation
*   **Protocol:**
    *   **No Public Registration:** There are no backend endpoints to "create an admin". Manager accounts are strictly provisioned manually by the Developer (`techie.jatin`) directly into the database.
    *   **Session Management:** Admin sessions will use highly encrypted JWTs stored exclusively in **HTTP-Only, Secure cookies**. These cookies cannot be accessed by client-side Javascript, neutralizing session-hijacking attacks.

## 5. Third-Party API Protection (Budget Security)
### Threat: API Spam (Google Maps Billing Drain)
A malicious bot (or a confused user) spamming the Address Autocomplete field can result in thousands of unnecessary API calls, draining the cafe's Google Cloud billing budget.

*   **Protocol:**
    *   **Session Tokens:** The frontend will use Google Maps *Session Tokens*. This groups a user's entire search process (typing '1', '2', '3', ' Main St') into a single billable request, rather than billing for every single keystroke.
    *   **Frontend Debouncing:** The React app will implement a 500ms debounce on the address input. The API is only called *after* the user pauses typing.
    *   **Backend Proxy & Rate Limiting:** The Google Maps API key will *not* be exposed directly to the public internet. The frontend will ping our own Node.js server, which runs the request through `express-rate-limit` (e.g., max 10 searches per minute per IP) before passing it to Google.
    *   **Google Cloud Restrictions:** The API key will be strictly bound in the Google Cloud Console to only accept requests originating from the cafe's specific server IP address and production URLs.

## 6. Data Privacy & PII Protection (Saved Addresses)
### Threat: Database Breaches and IDOR Attacks
Customer addresses are highly sensitive Personally Identifiable Information (PII). Hackers actively target this data to sell. We must protect it both at rest and in transit.

*   **Protocol:**
    *   **Application-Level Encryption (AES-256):** Before saving address details (Flat, Street, Lat/Lng, Phone Number) to the database, the Node.js server will encrypt the data. If a hacker manages to steal a database dump, the addresses will appear as unreadable gibberish. Only the live backend server holds the decryption key.
    *   **IDOR Prevention (Strict Authorization):** We will implement strict authorization checks on all Address APIs. The server will verify that the `user_id` inside the decrypted JWT session exactly matches the owner of the address. A hacker cannot manipulate the URL (`/api/addresses/5`) to fetch another customer's address.
    *   **Data Masking:** In any backend logs or admin analytics, sensitive details will be partially masked (e.g., `+91 ****** 4567`) to prevent insider threats.

## 7. Digital Wallet & Payment Security
### Threat: Artificial Credit Inflation & Fund Extraction
Hackers might attempt to manipulate network requests to manually trigger the refund API and artificially inflate their Cafe Wallet balance, or they might attempt to find a way to withdraw wallet credits to a real bank account.

*   **Protocol:**
    *   **Append-Only Ledger Logic:** The wallet balance is not just a mutable number in the database. Every single credit or deduction is written to a strictly Append-Only `WalletTransaction` table. The user's live balance is mathematically calculated (or heavily audited) against this unalterable transaction log.
    *   **Idempotency Keys:** All refund and credit APIs require unique Idempotency Keys. If a network error occurs and a refund request is accidentally fired twice by the Admin, the server will recognize the duplicate key and refuse to double-credit the user's wallet.
    *   **Strict Closure (Closed-Loop System):** The system strictly forbids peer-to-peer (P2P) transfers. Wallet credits are cryptographically bound to the specific `user_id` that earned them and cannot be moved.
    *   **Legal & Technical Firewall:** There are absolutely zero API endpoints written to transfer funds from the wallet to a bank account. It is a strictly one-way, closed-loop credit system meant solely for purchasing food on the platform.
