# Technology Stack

This document details the technologies, libraries, and frameworks utilized across the SGS Creations application, strictly based on the technical architecture and audit reports.

---

## 1. Frontend Technologies

| Technology | Version / Spec | Category | Role & Purpose |
| :--- | :--- | :--- | :--- |
| **React** | `^19.0.1` | UI Library | Modern component-based view rendering with concurrent UI capabilities |
| **Vite** | `^6.2.3` | Build Tool & Bundler | Ultra-fast local development server, Hot Module Replacement (HMR), and production Rollup bundling |
| **TypeScript** | `~5.8.2` | Language | Strong static typing for UI state, components, and API payload definitions |
| **TailwindCSS** | `^4.1.14` | CSS Framework | Modern utility-first styling foundation |
| **Vanilla CSS** | Standard CSS3 | Styling System | Custom CSS design tokens for liquid glassmorphism, atmospheric gradient orbs, and cursor spotlight |
| **React Router DOM** | `^7.18.3` | Client-Side Routing | Browser-based navigation, protected route guards, and nested admin routing |
| **Framer Motion** | `^12.23.24` | Animation Library | Physics-based spring animations, layout morphing (`layoutId`), and page transition sequences |
| **Lucide React** | `^0.546.0` | Vector Icons | Lightweight, consistent SVG icon set for navigation and interactive elements |

---

## 2. Backend Technologies

| Technology | Version / Spec | Category | Role & Purpose |
| :--- | :--- | :--- | :--- |
| **Node.js** | `v20+` / `v22` | Runtime Environment | High-performance asynchronous JavaScript server runtime |
| **Express** | `^5.2.1` | Web Framework | Lightweight HTTP server engine, middleware pipeline, and modular REST API routing |
| **TypeScript** | `~5.8.2` | Language | Type safety across backend routes, schemas, database interfaces, and middleware |
| **`tsx`** | `^4.21.0` | Execution Engine | Zero-config TypeScript execution and watcher for development and operational scripts |
| **Concurrently** | `^10.0.5` | Process Orchestrator | Parallel execution of frontend Vite dev server and backend watcher processes |
| **Vercel Functions** | Node Runtime | Serverless Deployment | Serverless execution layer executing the Express application through a root API bridge |

---

## 3. Database & Persistence Layer

| Technology | Category | Role & Purpose |
| :--- | :--- | :--- |
| **PostgreSQL** | Relational Database | ACID-compliant relational persistence for user profiles, projects, plans, requests, and creations |
| **Supabase** | Cloud Database Host | Managed PostgreSQL hosting with automated backups, scaling, and dashboard management |
| **`pg` (`node-postgres`)** | Client Driver (`^8.23.0`) | High-throughput connection pooling with strictly parameterized query execution |
| **Row-Level Security (RLS)**| Database Security | Fine-grained security policies enforced directly inside PostgreSQL tables |
| **Migration Tooling** | Custom Scripts | Idempotent, programmatic schema migrations (`server/db/migrate.ts`), constraint updates, and indexing |

---

## 4. Security & Networking Technologies

| Technology | Version / Spec | Role & Purpose |
| :--- | :--- | :--- |
| **`bcryptjs`** | `^3.0.3` | Secure password hashing using salt cost factor of 12 |
| **`zod`** | `^4.6.1` | Strict schema declaration and runtime validation for all API request bodies and route parameters |
| **`helmet`** | `^8.3.0` | HTTP security headers enforcement (HSTS, nosniff, frameguard, X-Powered-By suppression) |
| **`cors`** | `^2.8.6` | Cross-Origin Resource Sharing control with strict origin whitelisting and credentials support |
| **`express-rate-limit`** | `^8.7.0` | Multi-tier rate limiting against brute-force attacks, OTP enumeration, and API traffic flooding |
| **`jsonwebtoken`** | `^9.0.3` | Stateless signed cryptographic JWT tokens stored in `httpOnly` secure cookies |
| **`nodemailer`** | `^10.0.0` | SMTP client library delivering transactional HTML verification emails containing 6-digit OTPs |
