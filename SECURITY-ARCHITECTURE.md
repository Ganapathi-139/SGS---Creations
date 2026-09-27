# Security Architecture

## 1. Overview & Security Philosophy

The **SGS Creations** platform is architected around the principle of **Defense-in-Depth**. Security mechanisms are layered across transport, networking, identity verification, session persistence, API boundaries, and database query execution.

This document describes the security model and defense mechanisms conceptually. No secret keys, credentials, internal endpoints, or private configurations are published here.

---

## 2. Authentication & Identity Integrity

### 2.1 Stateless JSON Web Tokens (JWT)
* User authentication state is encapsulated within cryptographically signed JSON Web Tokens.
* Tokens contain verified claims (such as user ID and assigned role) signed with a robust symmetric secret key.

### 2.2 Protection Against XSS via `httpOnly` Cookies
* Session tokens are stored exclusively within HTTP cookies configured with:
  * `HttpOnly`: Prevents client-side scripts from reading the cookie through `document.cookie`, neutralizing token theft via Cross-Site Scripting (XSS).
  * `Secure`: Guarantees that cookies are transmitted exclusively over encrypted HTTPS connections.
  * `SameSite=Lax`: Mitigates Cross-Site Request Forgery (CSRF) by preventing external malicious sites from transmitting authentication cookies during cross-site requests.
  * `Path=/`: Enforces site-wide availability for authenticated requests.

### 2.3 Password Hashing with Salt Cost Factor 12
* Passwords submitted during registration or password update are hashed using `bcryptjs` with a computational work factor (cost) of **12 salt rounds**.
* Plaintext passwords are never stored, logged, or retained in persistent storage.
* Passwords must satisfy strict length boundaries (minimum 8 characters, maximum 128 characters).

### 2.4 Time-Limited 6-Digit Email OTP Verification
* Account activation and critical identity verification steps require a cryptographically generated 6-digit One-Time Password (OTP).
* OTP codes are delivered out-of-band via authenticated SMTP to the user's verified email address.
* Codes have a strict **10-minute expiration window** and are invalidated immediately upon first successful use.
* Input validation ensures numeric type coercion (`z.coerce.number()`) preventing string/number injection discrepancies.

### 2.5 Step-Up Verification for Sensitive Operations
* Destructive administrative operations (such as removing an administrator account or modifying live platform records) require step-up verification.
* The initiating administrator must re-enter their current account password, which is verified against database password hashes before the action is executed.

---

## 3. Network & Transport Security

### 3.1 HTTP Header Hardening (`helmet`)
The Express API utilizes `helmet` to automatically configure defensive HTTP headers:
* **HSTS (`Strict-Transport-Security`)**: Enforces browser communication strictly over HTTPS.
* **MIME-Sniffing Prevention (`X-Content-Type-Options: nosniff`)**: Disallows browsers from attempting to override declared Content-Types.
* **Frameguard (`X-Frame-Options`)**: Prevents clickjacking by disabling unauthorized iframe embedding.
* **Header Suppression**: Suppresses `X-Powered-By: Express` to prevent technology footprint discovery.

### 3.2 Strict CORS Policy
* The Cross-Origin Resource Sharing (CORS) layer restricts access to explicitly whitelisted client origins.
* `credentials: true` is strictly bounded to approved origins, preventing unauthorized third-party websites from making authenticated credentialed requests.

### 3.3 Multi-Tier Rate Limiting
To defend against automated denial-of-service, brute-force password guessing, and OTP enumeration, rate limiters are configured across distinct operational tiers:

| Limiter Tier | Window | Threshold | Protected Target |
| :--- | :--- | :--- | :--- |
| **Global API Limiter** | 15 minutes | 100 requests per IP | General application endpoints |
| **Authentication Limiter** | 15 minutes | 5 requests per IP | Login and registration routes |
| **OTP Verification Limiter**| 15 minutes | 5 requests per IP | Code verification and resend endpoints |

---

## 4. Input Validation & Data Layer Protection

### 4.1 Strict Runtime Schema Validation (`zod`)
* 100% of user inputs across request bodies (`req.body`) and route parameters (`req.params`) are parsed through strict Zod schemas before reaching business logic or SQL queries.
* Inputs are trimmed, constrained by length, and validated against strict format regular expressions.
* Extra or unexpected fields are automatically rejected.

### 4.2 Parameterized SQL Queries
* All interaction with PostgreSQL through the `pg` client driver uses **strictly parameterized queries** with positional variables (`$1, $2, ...`).
* User-provided values are separated from SQL syntax by the database driver, eliminating the possibility of SQL Injection (SQLi) vulnerabilities.

### 4.3 Database Row-Level Security (RLS)
* PostgreSQL tables hosted on Supabase have **Row-Level Security (RLS)** enabled.
* Policies verify that operations performed against the database adhere to role constraints and tenant boundaries, preventing unauthorized data modification or horizontal privilege escalation.
