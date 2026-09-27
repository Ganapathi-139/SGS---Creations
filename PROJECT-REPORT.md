# SGS Creations — Comprehensive Technical Architecture & Security Report

---

## 1. Executive Summary

**SGS Creations** is a production-grade Web Design & Development Agency platform engineered with a high-performance decoupled architecture:

* **Frontend**: React 19 Single Page Application (SPA) powered by Vite 6, styled with TailwindCSS 4 and custom Vanilla CSS design tokens featuring Cinematic Liquid Glassmorphism and Framer Motion spring physics.
* **Backend**: Node.js REST API with Express 5, written in TypeScript, deployed serverlessly on Vercel via a root API bridge.
* **Database & Persistence**: Managed PostgreSQL on Supabase with connection pooling (`pg`) and database-level Row-Level Security (RLS).
* **Identity & Authentication**: Stateless JWT stored in `httpOnly` secure cookies, bcrypt password hashing (cost factor 12), and time-limited, single-use 6-digit Email OTPs via SMTP (Nodemailer).

---

## 2. Technology Stack

### 2.1 Backend & Runtime
| Component | Technology | Version | Purpose |
| :--- | :--- | :--- | :--- |
| **Runtime Environment** | Node.js | v20+ / v22 | Core asynchronous JavaScript runtime |
| **Language** | TypeScript | ~5.8.2 | End-to-end static typing across routes, services, and models |
| **Execution Engine** | `tsx` | ^4.21.0 | Fast zero-config TypeScript execution & dev watching |
| **Web Server Framework**| Express | ^5.2.1 | HTTP routing, middleware pipeline, REST API endpoints |
| **Process Manager** | Concurrently | ^10.0.5 | Parallel frontend & backend development runners |
| **Serverless Deployment**| Vercel Functions | Node runtime | Root `api/index.ts` bridge mapping Express app to Vercel |

### 2.2 Database & Data Access
| Component | Technology | Purpose |
| :--- | :--- | :--- |
| **Database Engine** | PostgreSQL (Supabase) | Relational database hosting users, profiles, projects, plans, requests, creations, and social media |
| **Client Driver** | `pg` (`node-postgres` ^8.23.0) | High-throughput connection pooling with parameterized queries |
| **Access Security** | Supabase Row-Level Security (RLS) | Restricts direct database operations to authenticated client roles |
| **Migration Tools** | Custom TypeScript Migration Scripts | Idempotent schema migrations (`server/db/migrate.ts`), constraints, indexes |

### 2.3 Frontend & Client-Side
| Component | Technology | Version | Purpose |
| :--- | :--- | :--- | :--- |
| **Core Framework** | React | ^19.0.1 | Modern concurrent UI rendering |
| **Build & Bundler** | Vite | ^6.2.3 | Ultra-fast HMR and Rollup production bundling |
| **Routing** | React Router DOM | ^7.18.3 | Client-side SPA routing (`BrowserRouter`, nested admin routes, protected guards) |
| **Animations & Physics** | Framer Motion | ^12.23.24 | Smooth page transitions, layout morphing (`layoutId`), modal micro-interactions |
| **Icons** | Lucide React | ^0.546.0 | Clean, lightweight SVG vector icon system |

---

## 3. Frontend Architecture

* **Decoupled Single Page Application**: Client application hydrates in the browser and handles view navigation locally via `react-router-dom`.
* **Guarded Route Hierarchy**:
  * **Public Routes**: Welcome, Explore/Home, Service Plans, Creations Showcase, Social Links.
  * **Protected Client Routes**: Authenticated user dashboard, project milestone tracking, and custom design request intake.
  * **Role-Protected Admin Routes**: Nested under `/admin`, requiring verified `admin` role claims in the authentication token.
* **State & Authentication Context**: Global `AuthContext` coordinates session state, user role, and authenticated navigation across views.
* **SPA Routing Fallback**: Edge rewrite rules on Vercel forward non-API paths to `index.html`, ensuring deep links and browser refreshes load properly without 404 errors.

---

## 4. Backend Architecture

* **Modular REST API**: Structured Express application organized cleanly into middleware, route handlers, database utilities, and schema validators.
* **Serverless Entry Bridge**: The root `api/index.ts` re-exports the Express instance to Vercel Serverless Functions, enabling serverless execution without modifying internal server architecture.
* **Standardized JSON Responses**: Consistent API responses for success payloads and structured error messages formatted from validation failures.

---

## 5. Database Architecture

* **Relational PostgreSQL Schema**:
  * `users`: Core identity, roles (`client`, `admin`), email verification status, and password hashes.
  * `client_profiles`: Extended client contact metadata (WhatsApp, city, address).
  * `organizations`: Company/brand associations linked to client profiles.
  * `plans`: Service tier offerings, pricing (stored in paise for precision), and feature lists.
  * `projects`: Active client project lifecycle records linked to plans and client profiles.
  * `custom_design_requests`: Inbound custom consultation inquiries with design direction and budget specifications.
  * `creations`: Curated portfolio works, image associations, and custom metadata fields.
  * `social_media`: Managed brand social channels with dynamic active states and display ordering.
  * `otp_verifications`: Time-limited verification codes with attempt counts and expiration timestamps.
* **Database Constraints**: Foreign key cascades, unique email and username constraints, and check constraints maintaining data integrity.
* **Row-Level Security (RLS)**: PostgreSQL-level policy enforcement safeguarding data tenancy.

---

## 6. Authentication

* **Stateless JWT Tokens**: Signed tokens containing user ID and role claims, eliminating server-side session lookup overhead.
* **`httpOnly` Cookie Isolation**: JWT tokens are transmitted via `httpOnly`, `Secure`, `SameSite=Lax` cookies, preventing script access and defending against XSS token harvesting.
* **Password Hashing**: Passwords hashed with `bcryptjs` using 12 salt rounds.
* **6-Digit One-Time Password (OTP)**:
  * Cryptographically generated 6-digit codes delivered via SMTP.
  * Strict 10-minute expiry period.
  * Single-use invalidation upon verification.
* **Step-Up Verification**: Destructive administrative actions require confirmation of the current administrator's password.

---

## 7. Security Architecture

* **HTTP Header Hardening (`helmet`)**: HSTS enforcement, MIME-sniffing prevention (`nosniff`), frameguard protection against clickjacking, and header fingerprint suppression.
* **CORS Protection (`cors`)**: Origin whitelisting restricting API access strictly to designated production and development domains.
* **Multi-Tier Rate Limiting (`express-rate-limit`)**:
  * Global API limiter (100 requests per 15 min).
  * Authentication limiter (5 attempts per 15 min).
  * OTP limiter (5 attempts per 15 min).
* **Input Validation (`zod`)**: 100% of request bodies and URL parameters validated against strict schemas before processing.
* **SQL Injection Elimination**: 100% parameterized SQL queries using `$1, $2, ...` positional arguments.

---

## 8. UI/UX Design System

* **Cinematic Deep Glassmorphism**:
  * Void dark foundation (`#050608`).
  * Multi-layer atmospheric color blooms (indigo, royal violet, amber/champagne).
  * Translucent liquid glass cards featuring `backdrop-filter: blur(20px - 40px)` and subtle 1px border glows (`border-white/5` - `border-white/15`).
* **Interactive Dynamic Visuals**:
  * Desktop mouse spotlight tracking cursor coordinates in real-time.
  * Spring-animated floating navigation capsule (`layoutId="nav-pill"`).
* **Typography System**:
  * *Outfit* & *Cinzel* for headlines and brand presentation.
  * *Inter* for legible body content.
  * *JetBrains Mono* for pricing figures and technical identifiers.

---

## 9. Deployment & Infrastructure

* **Edge CDN**: Vercel Global Edge Network hosting compiled Vite assets with caching and compression.
* **Serverless Compute**: Express REST API running serverlessly on Vercel Functions.
* **Database Host**: Supabase managed PostgreSQL with connection pooling.
* **DNS & Domain**: GoDaddy DNS routing `sgscreations.in` to Vercel with automated TLS/SSL.
* **Mail Gateway**: Authenticated SMTP transport through Nodemailer over TLS for transactional verification emails.

---

## 10. Development Workflow

* **AI-Assisted Pair Programming**: Antigravity agentic workflow for architecture refinement, rapid iteration, and security verification.
* **Static Verification**: Continuous TypeScript checks (`tsc --noEmit`) guaranteeing type correctness across the codebase.
* **Bundle Compilation**: Vite production build verification ensuring tree-shaking and minification.
* **Automated E2E Testing**: Backend integration test suite (`npm run db:test`) exercising all 12 operational domains under active database RLS policies.
* **Continuous Deployment**: Automated Vercel deployments triggered upon pushing commits to the private repository's `main` branch.
