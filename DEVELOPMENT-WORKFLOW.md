# Engineering & Development Workflow

## 1. Overview

The development of **SGS Creations** followed a disciplined, quality-focused engineering workflow emphasizing continuous type safety, automated verification, security auditing, and smooth cloud integration.

---

## 2. Integrated Tooling Ecosystem

| Tool / Service | Category | Role in Workflow |
| :--- | :--- | :--- |
| **Antigravity** | AI Pair Programming Agent | Accelerated feature implementation, complex refactoring, and security audits |
| **GitHub** | Version Control System | Source code versioning, change tracking, and automated CI triggers |
| **Vercel** | Hosting & Serverless Platform | Preview deployments, production builds, and edge routing |
| **Supabase** | Cloud Database Host | PostgreSQL database management, schema inspection, and RLS validation |
| **GoDaddy** | DNS Management | Domain routing and nameserver configuration for `sgscreations.in` |
| **Gmail SMTP** | Email Delivery Gateway | Transactional email verification and OTP integration testing |

---

## 3. Systematic Development Lifecycle

The engineering workflow follows an iterative four-phase cycle:

```
[ 1. Implementation & Typing ] ──► [ 2. Automated Validation ]
                                              │
[ 4. Deployment Verification ] ◄── [ 3. CI/CD Release ]
```

### Phase 1: Implementation & Type Integrity
* **Concurrent Local Environment**: Local execution leverages `concurrently` to run the frontend Vite dev server on port `3000` alongside the backend Express server via `tsx watch`.
* **Full-Stack Type Coherence**: Shared TypeScript interfaces ensure that API payloads, database models, and React component props remain synchronized.

### Phase 2: Automated Validation & Quality Assurance
Before committing any feature or bugfix, the code undergoes three rigorous quality verification steps:
1. **Static Type Checking**: `tsc --noEmit` verifies strict type correctness across the entire codebase with zero tolerance for unresolved types.
2. **Production Bundle Compilation**: `vite build` verifies that all modules transform, bundle, and minify cleanly without unresolved imports or Rollup errors.
3. **End-to-End Integration Testing**: An automated test suite (`npm run db:test`) connects to the Supabase database under Row-Level Security, running real HTTP assertions through all domains:
   * Health endpoints
   * Plan retrieval and selection
   * Client registration, email OTP dispatch, and verification
   * JWT session cookie generation and authentication guards
   * Custom design intake flows
   * Project status progression and dashboard updates
   * Social media channel management
   * Administrator privileges and step-up deletion controls

### Phase 3: Security Review
* Routine security audits inspect dependency trees, query parameterization, authentication cookies, CORS headers, and rate limiter limits.
* All data mutations are confirmed to validate payloads against Zod schemas prior to SQL query execution.

### Phase 4: Production Deployment & Live Verification
* Changes committed to the `main` branch trigger an automated build on Vercel.
* Post-deployment verification tests:
  * SPA route refreshes (e.g. `/home`, `/plans`, `/auth/login`) to ensure fallback rules operate properly.
  * Live SMTP OTP email delivery to confirm mail gateway functionality.
  * Database read/write operations under live Supabase connection pooling.
  * Visual inspection across desktop and mobile devices to verify responsive styling and animation performance.
