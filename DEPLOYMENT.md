# Deployment & Production Infrastructure

## 1. Overview

The **SGS Creations** production application is deployed across a cloud-native, globally distributed infrastructure engineered for high availability, sub-second content delivery, and automated continuous deployment.

> [!NOTE]
> The production source code repository is private. This document explains the deployment pipeline and infrastructure architecture conceptually without publishing proprietary configuration files or credential values.

---

## 2. Infrastructure Architecture & Service Roles

```
[ Private GitHub Repository ]
              │ (Git Push to main)
              ▼
    [ Vercel CI/CD Pipeline ]
              │
      ┌───────┴───────┐
      ▼               ▼
[ Static CDN ]   [ Serverless Functions ]
(Vite Assets)    (Express 5 REST API)
      │               │
      │               ├─► [ Supabase Cloud ] (PostgreSQL Database + RLS)
      │               └─► [ SMTP Mail Gateway ] (Gmail TLS Relay)
      ▼
[ GoDaddy DNS Mapping ]
      │
      ▼
https://www.sgscreations.in
```

### 2.1 Private GitHub Repository
* Acts as the single source of truth for all application code, migration scripts, and documentation.
* Webhooks link the repository to Vercel for automated integration and continuous deployment.

### 2.2 Vercel Edge & Serverless Platform
* **Static Asset CDN**: Global distribution of compiled HTML, CSS, JavaScript, and image assets.
* **Serverless Functions**: Executes the backend API on demand via an Express serverless bridge.
* **SPA Routing Configuration**: Edge rewrites route client-side URLs (`/((?!api/).*)`) to `index.html` while preserving direct `/api/*` execution paths.
* **Zero-Downtime Rollouts**: Atomic deployments with instant rollback capabilities.

### 2.3 Supabase Managed PostgreSQL
* Provides resilient, cloud-managed PostgreSQL with automated daily snapshots, connection pooling, and continuous operational health monitoring.
* Hosts persistent relational data with Row-Level Security (RLS) enforcement.

### 2.4 GoDaddy Domain & DNS Configuration
* The custom apex domain (`sgscreations.in`) and `www` subdomain are registered and managed via GoDaddy.
* DNS A records and CNAME records delegate web traffic routing to Vercel's global anycast edge network.
* TLS certificates are automatically provisioned and renewed through Vercel's managed certificate authority.

### 2.5 Transactional Email Delivery (Gmail SMTP)
* Transactional messages (such as user account verification OTPs) are routed through authenticated SMTP relays over TLS.
* Nodemailer manages connection pooling and secure transport handshakes.

---

## 3. Environment Configuration Model

In production, sensitive settings are injected via encrypted environment variables configured directly inside the Vercel project settings dashboard.

Categories of configured variables include:
* **Database Parameters**: Host, port, database name, user, and secure password.
* **Authentication Cryptography**: JWT signing secrets and token expiration parameters.
* **Email Gateway Credentials**: SMTP host, port, user account, and application-specific credentials.
* **Network & Security Policies**: Whitelisted CORS origins (`ALLOWED_ORIGINS`) and deployment environment flags (`NODE_ENV=production`).

---

## 4. Continuous Deployment Workflow

1. **Local Verification**: Changes pass local TypeScript compilation (`tsc --noEmit`), automated E2E tests (`npm run db:test`), and production builds (`vite build`).
2. **Version Control Commit**: Code is committed and pushed to the private `main` branch.
3. **Automated Vercel Build**:
   * Vercel triggers an automated build worker.
   * Compiles the Vite frontend bundle into minified production assets.
   * Configures the serverless API runtime entry bridge.
4. **Edge Deployment**: The new deployment is verified by health checks and made live instantly across the global edge network.
