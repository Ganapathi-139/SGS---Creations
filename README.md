# SGS Creations — Architecture & Technical Case Study

[![Live Website](https://img.shields.io/badge/Live_Site-sgscreations.in-00E5FF?style=flat&logo=google-chrome&logoColor=black)](https://www.sgscreations.in)
[![Frontend](https://img.shields.io/badge/Frontend-React_19_|_Vite_6-61DAFB?style=flat&logo=react&logoColor=black)](docs/TECHNOLOGY-STACK.md)
[![Backend](https://img.shields.io/badge/Backend-Node.js_|_Express_5-339933?style=flat&logo=node.js&logoColor=white)](docs/TECHNOLOGY-STACK.md)
[![Database](https://img.shields.io/badge/Database-PostgreSQL_|_Supabase-4169E1?style=flat&logo=postgresql&logoColor=white)](docs/TECHNOLOGY-STACK.md)
[![Security](https://img.shields.io/badge/Security-Hardened_|_RLS_Enabled-00C853?style=flat&logo=shield&logoColor=white)](docs/SECURITY-ARCHITECTURE.md)

---

> [!NOTE]
> **Notice:** This repository contains documentation and architectural information only. The production source code is private and is not included.

---

## 1. Project Introduction

**SGS Creations** is a production-grade Web Design and Development Studio platform providing bespoke digital experiences, transparent subscription plans, custom design consultation, and an integrated client portal.

* **Live Platform**: [https://www.sgscreations.in](https://www.sgscreations.in)
* **Domain / Business**: High-end modern web studio and digital creation agency.
* **Architecture Type**: Decoupled Single Page Application (SPA) with a serverless Node.js Express REST API, Supabase-backed PostgreSQL persistence, and edge CDN distribution.

---

## 2. Project Purpose & High-Level Capabilities

SGS Creations was designed to bridge luxury design aesthetics with robust engineering:
* **Interactive Showcase**: Cinematic presentation of design work and portfolio creations.
* **Transparent Service Plans**: Exploration and direct selection of curated web studio packages.
* **Custom Design Consultation**: Interactive intake flow for clients to submit specific brand directions, budgetary preferences, and project ideas.
* **Client Portal & Project Tracking**: Registered clients can view their active project stages (from registration through requirements, design, development, testing, to final launch).
* **Administrative Management Portal**: Studio management interface for client records, project milestone updates, custom requests, social channel presence, and administrative operations.

---

## 3. Architecture Overview

The system employs a decoupled, edge-accelerated architecture:

```
[ Browser / Client ]
       │
       ▼
[ Vercel Edge Network ] ─── (Global CDN for static Vite bundles & SPA Fallback)
       │
       ├─► Static Assets (index.html, JS, CSS)
       │
       └─► /api/* ──► [ Vercel Serverless Function ]
                             │
                             ▼
                     [ Express 5 REST API ]
                             │
              ┌──────────────┴──────────────┐
              ▼                             ▼
    [ Supabase PostgreSQL ]        [ SMTP Service (Gmail) ]
    (Connection Pool + RLS)        (Verification OTP Delivery)
```

For full details, see the [Architecture Documentation](docs/ARCHITECTURE.md).

---

## 4. Technology Stack Summary

| Domain | Core Technologies | Highlights |
| :--- | :--- | :--- |
| **Frontend** | React 19, Vite 6, TypeScript | Concurrent rendering, sub-second HMR, optimized Rollup bundle |
| **Styling & Motion** | TailwindCSS 4, Vanilla CSS, Framer Motion | Cinematic Deep Glassmorphism, cursor spotlight, spring physics |
| **Routing** | React Router DOM 7 | Client-side routing with Vercel SPA rewrite fallback |
| **Backend** | Node.js (v20+ / v22), Express 5, TypeScript | Modular REST controllers, middleware pipeline, `tsx` runner |
| **Serverless Bridge**| Vercel Functions | Root API bridge wrapping Express instance into serverless functions |
| **Database** | PostgreSQL on Supabase, `pg` driver | Connection pooling, parameterized queries, Row-Level Security |
| **Email Service** | Nodemailer / SMTP | Automated transactional HTML verification emails with 6-digit OTP |
| **Security Suite** | bcryptjs, Zod, Helmet, CORS, express-rate-limit | Layered defense across transport, sessions, routing, and data |

For a complete breakdown including versions, see [Technology Stack](docs/TECHNOLOGY-STACK.md).

---

## 5. Security & Authentication Highlights

The production platform was hardened through an extensive multi-tier security audit:
* **Stateless JWT in `httpOnly` Cookies**: Session tokens are isolated from client-side JavaScript (`document.cookie`), mitigating Cross-Site Scripting (XSS) token theft.
* **Password Hashing**: Industry-standard `bcryptjs` hashing with **12 salt rounds**.
* **Account Verification via OTP**: Cryptographically generated 6-digit one-time passcodes delivered via SMTP with a strict 10-minute expiry window.
* **Row-Level Security (RLS)**: PostgreSQL tables are protected by database-level security policies.
* **Strict Schema Validation**: 100% of input data is validated through **Zod schemas** before reaching queries.
* **SQL Injection Prevention**: 100% parameterized queries via positional placeholders (`$1, $2, ...`).
* **Multi-Tier Rate Limiting**: Dedicated rate limits for general traffic, authentication endpoints, and OTP verification requests.
* **Step-Up Verification**: Destructive administrative actions require administrator password confirmation.

For details, see [Security Architecture](docs/SECURITY-ARCHITECTURE.md) and [Security Policy](SECURITY.md).

---

## 6. UI/UX Design System: Cinematic Deep Glassmorphism

The frontend interface incorporates an original visual aesthetic:
* **Palette Foundation**: Deep void `#050608` accented by multi-layered radial lighting blooms (indigo, royal violet, amber/champagne).
* **Liquid Glass Surfaces**: Translucent cards utilizing `backdrop-filter: blur(20px - 40px)` and soft 1px border glows.
* **Dynamic Interactions**: Desktop cursor-following spotlight glow and layout-morphing navigation pills (`layoutId="nav-pill"`).
* **Typography**:
  * *Outfit* & *Cinzel* for editorial brand headlines
  * *Inter* for crisp body copy
  * *JetBrains Mono* for IDs and numerical pricing

For details, see [Design System Documentation](docs/DESIGN-SYSTEM.md).

---

## 7. Development & Production Infrastructure

* **Source Control**: Private GitHub repository with continuous integration.
* **Hosting & CDN**: Vercel Edge hosting with serverless backend routing.
* **Database Infrastructure**: Supabase cloud managed PostgreSQL.
* **Domain & DNS**: GoDaddy DNS management mapped to Vercel custom domain with automated SSL.
* **Quality Assurance**: Automated end-to-end integration tests (`db:test`), TypeScript strict type checking (`tsc --noEmit`), and Vite production builds.

For more information, see:
* [Deployment Overview](docs/DEPLOYMENT.md)
* [Development Workflow](docs/DEVELOPMENT-WORKFLOW.md)
* [Comprehensive Technical Report](docs/PROJECT-REPORT.md)

---

## 8. Documentation Index

Detailed documentation files in this repository:

* [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) — System architecture, data flow, and components.
* [`docs/TECHNOLOGY-STACK.md`](docs/TECHNOLOGY-STACK.md) — Complete technology list and library versions.
* [`docs/SECURITY-ARCHITECTURE.md`](docs/SECURITY-ARCHITECTURE.md) — Multi-tier security engineering and defenses.
* [`docs/DESIGN-SYSTEM.md`](docs/DESIGN-SYSTEM.md) — Cinematic Glassmorphism, typography, and motion design.
* [`docs/DEPLOYMENT.md`](docs/DEPLOYMENT.md) — Cloud infrastructure, Vercel serverless, and domain setup.
* [`docs/DEVELOPMENT-WORKFLOW.md`](docs/DEVELOPMENT-WORKFLOW.md) — Engineering processes, verification checks, and tooling.
* [`docs/PROJECT-REPORT.md`](docs/PROJECT-REPORT.md) — The comprehensive technical architecture report.
* [`SECURITY.md`](SECURITY.md) — Public responsible disclosure policy.

