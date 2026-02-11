# Technical Architecture Analysis: B2B White-Label Trading Platform (Next.js 16)

> **Date**: 2026-02-10
> **Analyst**: Software Architect (AI Agent)
> **Scope**: Technical architecture for myaccount-front on Next.js 16 / React 19 / TypeScript 5

## Executive Summary

This is a **B2B white-label trader room** (personal cabinet) sold to individual forex brokers. Each broker deploys the frontend under their brand for their own traders. The backend is **xCritical CRM** -- not developed in this repo. The project is a **rewrite from JavaScript to TypeScript/Next.js 16** (Jira: CRM-42322) with 15+ existing production deployments (MaxiMarkets, UMarkets, LimeFX, etc.).

The architecture must support: per-broker customization without code changes, 4 trading flows (prop, forex, crypto, binary options) as modular systems, xCritical CRM API integration, mobile-first responsive design (55% mobile users), and regulatory compliance (KYC/AML).

---

## 1. Multi-Tenancy Architecture

### Recommendation: Build-Time Per-Broker Configuration (NOT Runtime Multi-Tenant)

**Critical clarification from project docs**: This is **one broker per deployment**, NOT a multi-broker aggregator. Each broker gets their own deployment with their own configuration. This fundamentally simplifies the multi-tenancy model.

**Pattern: Configuration-Driven Builds**

```
Broker Config (JSON/YAML)
    |
    v
Build Pipeline (pnpm build)
    |
    v
Deployed Instance (per broker)
```

Each broker has a configuration file that drives:
- Branding (logo, colors, fonts, favicon)
- Enabled features/modules (which trading flows are active)
- Payment integrations
- KYC/onboarding flows
- Language packs
- xCritical CRM API endpoint

**Implementation approach**:

```typescript
// src/config/broker-config.ts
interface BrokerConfig {
  readonly brandId: string
  readonly branding: BrandingConfig
  readonly features: FeatureFlags
  readonly locale: LocaleConfig
  readonly api: ApiConfig
  readonly kyc: KycConfig
  readonly payments: PaymentConfig[]
}
```

Configuration loaded at build time via environment variables or a config file that is injected per deployment. Next.js `publicRuntimeConfig` or environment variables (`NEXT_PUBLIC_*`) expose safe values to the client.

**Trade-offs**:
- (+) Simplest architecture, best performance (no runtime config resolution)
- (+) Each broker's build is fully optimized (tree-shaking unused modules)
- (+) No shared database, no tenant isolation concerns
- (-) Requires a build per broker (CI/CD pipeline must handle this)
- (-) Updates require redeployment per broker

**Risk**: With 15+ brokers, deployment automation becomes critical. A robust CI/CD pipeline that builds and deploys per-broker configurations from a single codebase is essential.

### Alternative Considered: Runtime Multi-Tenant (Rejected)

A single deployment serving all brokers with runtime tenant resolution (via subdomain or header) was considered and **rejected** because:
1. The project-context.md explicitly states "one broker per deployment"
2. Runtime multi-tenancy adds complexity (tenant isolation, shared infra risk)
3. Brokers want **complete isolation** for compliance and data sovereignty
4. Current deployment model already works for 15+ brokers

---

## 2. Next.js App Router Patterns for Modular Trading

### Recommended App Router Structure

```
src/app/
  (auth)/                    # Auth group (login, register, password reset)
    login/page.tsx
    register/page.tsx
    forgot-password/page.tsx
  (dashboard)/               # Authenticated area (layout with sidebar, header)
    layout.tsx               # Shared dashboard layout (sidebar + header)
    page.tsx                 # Dashboard home (overview)
    accounts/                # Trading accounts management
      page.tsx
      [accountId]/page.tsx
    deposit/page.tsx
    withdrawal/page.tsx
    kyc/page.tsx
    profile/page.tsx
    documents/page.tsx
    (trading)/               # Trading flow modules (conditionally enabled)
      prop/                  # Prop trading module
        challenges/page.tsx
        funded/page.tsx
      copy-trading/page.tsx
      pamm/page.tsx
  api/                       # BFF API routes (proxy to xCritical CRM)
    auth/route.ts
    accounts/route.ts
    ...
```

### Route Groups for Module Isolation

Use Next.js route groups `(groupName)` to organize trading modules without affecting URL structure. Each trading flow module is a route group that can be conditionally rendered based on broker config.

### Parallel Routes for Dashboard Panels

For the dashboard overview page, parallel routes (`@slots`) can render independent panels concurrently:

```
src/app/(dashboard)/
  @balance/page.tsx          # Account balance widget
  @positions/page.tsx        # Open positions widget
  @activity/page.tsx         # Recent activity widget
  layout.tsx                 # Combines slots
```

This enables independent loading and error handling per panel -- critical for trading dashboards where one slow API call should not block the entire page.

### Intercepting Routes for Modals

Use intercepting routes for deposit/withdrawal flows that can be opened as modals from the dashboard but also as full pages:

```
src/app/(dashboard)/
  (..)deposit/page.tsx       # Intercepted as modal
  deposit/page.tsx           # Full page fallback
```

---

## 3. Real-Time Data Handling

### Architecture: Server Components for Initial Load + Client Components for Real-Time

**The key insight**: Traders do NOT trade through this platform -- they trade in MetaTrader/cTrader. This is a **management dashboard**. The real-time requirements are for displaying account status, balance changes, and position updates -- NOT for sub-millisecond trading execution.

Based on the product audit: "Myfxbook updates 15-60 seconds" and "15-30 seconds delay is acceptable for an analytical tool."

**Recommended approach**:

```
Layer 1: Server Components (Initial Page Load)
  - Fetch account data, balances, history via xCritical CRM API
  - Render full HTML on server (fast LCP, SEO for public pages)
  - Use Next.js data caching with revalidation

Layer 2: Client Components (Real-Time Updates)
  - WebSocket connection to xCritical CRM for live data
  - Only used for components that need real-time updates:
    * Account balance
    * Open positions P/L
    * Notification badges
  - SSE (Server-Sent Events) as fallback if WebSocket is blocked
```

**WebSocket architecture**:

```typescript
// src/lib/ws/trading-socket.ts
// Client component hook for real-time data
'use client'

function useTradingSocket() {
  // Connect to xCritical CRM WebSocket endpoint
  // Managed through a singleton connection
  // Reconnect with exponential backoff
  // Dispatch updates to Zustand stores
}
```

**Important**: The WebSocket connection lives entirely in client components. Server components handle initial data fetch and HTML streaming. This cleanly separates the rendering concerns.

**Trade-offs**:
- (+) Server Components give fast initial render and reduce JS bundle
- (+) WebSocket updates only where needed (minimal client JS)
- (+) Graceful degradation: if WebSocket fails, polling fallback works fine for 15-30s latency
- (-) Requires careful boundary management between server and client components
- (-) WebSocket state must be managed outside React's server component tree

### What NOT to do

- Do NOT use WebSockets for all data. Most pages (KYC, profile, documents) are static CRUD -- server components + form actions are sufficient.
- Do NOT try to SSR real-time data. It's stale by the time it reaches the client.
- Do NOT use SSE through Next.js API routes as the primary channel. Use direct WebSocket to xCritical if available.

---

## 4. White-Label Theming System

### Recommendation: CSS Custom Properties + Tailwind CSS v4 Design Tokens

**Architecture**:

```
Broker Config (colors, fonts, logos)
    |
    v
CSS Custom Properties (injected at build time or runtime)
    |
    v
Tailwind CSS v4 (references custom properties)
    |
    v
Component Library (uses Tailwind classes)
```

**Implementation**:

```css
/* src/app/globals.css */
@theme {
  /* Brand tokens - overridden per broker */
  --color-brand-primary: var(--broker-primary, #2563eb);
  --color-brand-secondary: var(--broker-secondary, #1e40af);
  --color-brand-accent: var(--broker-accent, #3b82f6);

  /* Semantic tokens */
  --color-success: #16a34a;
  --color-danger: #dc2626;
  --color-warning: #d97706;

  /* Trading-specific tokens */
  --color-profit: #16a34a;
  --color-loss: #dc2626;
}
```

**Per-broker theme injection**:

```typescript
// src/app/layout.tsx (Server Component)
import { getBrokerConfig } from '@/config/broker'

export default function RootLayout({ children }) {
  const config = getBrokerConfig()

  return (
    <html lang={config.locale.default} style={config.branding.cssVariables}>
      <body>{children}</body>
    </html>
  )
}
```

**Why CSS Custom Properties + Tailwind v4**:
- Tailwind v4 natively supports `@theme` directive with CSS custom properties
- No runtime JS overhead for theming
- Broker logos/assets handled via config-driven asset paths
- Dark/light mode via `prefers-color-scheme` or user preference
- Components use semantic Tailwind classes (`bg-brand-primary`) -- brand-agnostic

---

## 5. Module System for Trading Flows

### Recommendation: Feature-Flag Driven Lazy Modules

Each trading flow (prop, forex, crypto, binary) is a **lazy-loaded module** gated by broker configuration:

```typescript
// src/config/features.ts
interface FeatureFlags {
  readonly propTrading: boolean
  readonly copyTrading: boolean
  readonly pamm: boolean
  readonly crypto: boolean
  readonly binaryOptions: boolean
}
```

**Module structure**:

```
src/modules/
  prop-trading/           # Prop trading module
    components/
    hooks/
    stores/
    types/
    index.ts             # Public module API
  copy-trading/
  pamm/
  crypto/
  shared/                # Shared components across modules
    trading-table/
    chart-widget/
    order-panel/
```

**Conditional rendering with Next.js dynamic imports**:

```typescript
// src/app/(dashboard)/layout.tsx
import { getBrokerConfig } from '@/config/broker'
import dynamic from 'next/dynamic'

const PropTradingNav = dynamic(() => import('@/modules/prop-trading/components/Nav'))

export default function DashboardLayout({ children }) {
  const { features } = getBrokerConfig()

  return (
    <div>
      <Sidebar>
        <CoreNav />
        {features.propTrading && <PropTradingNav />}
        {features.copyTrading && <CopyTradingNav />}
      </Sidebar>
      {children}
    </div>
  )
}
```

**Trade-offs**:
- (+) Unused modules not included in bundle (tree-shaking at build time)
- (+) Each module has its own Zustand stores, types, components (clear boundaries)
- (+) Modules can be developed and tested independently
- (-) Shared state between modules needs coordinator pattern
- (-) Cross-module navigation requires careful routing

---

## 6. Authentication Architecture

### Recommendation: xCritical CRM-Delegated Auth + Next.js Middleware

```
Trader Login Form
    |
    v
Next.js API Route (/api/auth/login)   <-- BFF proxy
    |
    v
xCritical CRM Auth API                <-- Source of truth
    |
    v
JWT/Session Token returned
    |
    v
HttpOnly Secure Cookie (set by API route)
    |
    v
Next.js Middleware (validates on every request)
```

**Key design decisions**:

1. **HttpOnly cookies** for token storage (never localStorage -- XSS risk with financial data)
2. **Next.js Middleware** for auth guard on `/(dashboard)/*` routes -- runs on Edge, fast rejection
3. **Token refresh** handled by API route, transparent to client
4. **CSRF protection** via SameSite cookie + custom header validation

**Multi-tenant auth**: Not needed since each deployment serves one broker. Auth is straightforward single-tenant.

**Broker admin vs. trader auth**: Out of scope for this frontend. Broker admins use the xCritical CRM admin panel. This frontend is exclusively for end-user traders.

---

## 7. API Layer Design

### Recommendation: BFF (Backend-for-Frontend) Pattern via Next.js API Routes

```
Client Component
    |
    v
Next.js API Route (BFF)     <-- Proxy, transform, aggregate
    |
    v
xCritical CRM REST API      <-- Single backend dependency
```

**Why BFF through Next.js API routes**:

1. **Security**: Keeps xCritical CRM credentials server-side. Client never talks to CRM directly.
2. **Transformation**: Maps CRM response shapes to frontend-optimized DTOs.
3. **Aggregation**: Dashboard overview can fetch from multiple CRM endpoints and return a single response.
4. **Rate limiting**: Protect CRM API from excessive client requests.
5. **Caching**: Server-side cache for data that changes infrequently (profile, KYC status).

**REST vs. GraphQL**: REST is recommended. The backend (xCritical CRM) exposes REST APIs. Adding a GraphQL layer adds complexity with no clear benefit for a single-backend architecture.

---

## 8. Performance: SSR vs CSR Trade-offs

### Recommendation: Hybrid Rendering Strategy

| Page Type | Rendering | Rationale |
|-----------|-----------|-----------|
| Login / Register | SSR (Server Components) | Fast initial load, form rendering is simple |
| Dashboard Overview | SSR + Streaming SSR | Server renders initial state, Suspense boundaries for slow data |
| Account Details | SSR + Client hydration | Server renders account info, client adds real-time P/L |
| Deposit / Withdrawal | SSR (Server Components + Server Actions) | Form submission via Server Actions, no client JS needed for the form |
| KYC / Profile | SSR (Server Components) | Pure CRUD, no real-time |
| Trading Positions | CSR (Client Components) | Real-time updates via WebSocket, heavy interactivity |
| Charts / Analytics | CSR with dynamic import | Heavy JS libraries (charting), SSR adds no value |
| Documents | SSR | Static content listing |

**Key performance targets**:
- LCP < 2.5s
- INP < 200ms
- CLS < 0.1

**Trading-specific performance considerations**:
- Price tickers: Use `requestAnimationFrame` for smooth updates, not React re-renders
- Order book: Virtualized lists for large datasets (only render visible rows)
- Charts: Canvas-based rendering (lightweight-charts or TradingView widget), NOT SVG

---

## 9. Deployment Strategy

### Recommendation: Single Codebase, Per-Broker Build & Deploy

```
Git Repository (single codebase)
    |
    v
CI/CD Pipeline (GitHub Actions / GitLab CI)
    |
    ├── Build: Broker A config → Docker image A
    ├── Build: Broker B config → Docker image B
    └── Build: Broker C config → Docker image C
    |
    v
Container Registry
    |
    v
Per-Broker Deployment (Docker / Kubernetes)
```

**Why per-broker builds (not runtime config)**:
1. Dead code elimination: Disabled modules are tree-shaken
2. Security isolation: Each broker's deployment is independent
3. Performance: Optimized builds per broker's feature set
4. Compliance: Data isolation is guaranteed (no shared infrastructure)
5. Scaling: Each broker can be scaled independently based on their trader count

**Deployment targets** (in order of recommendation):

1. **Docker + Kubernetes** -- Best for 15+ brokers. Standardized deployment, easy scaling, health checks.
2. **Docker + Docker Compose** -- Simpler alternative for smaller scale.
3. **Vercel** -- NOT recommended. Per-broker deployments would require separate Vercel projects.

---

## 10. State Management for Trading State

### Recommendation: Zustand Multiple Stores

```typescript
// Domain Store Map
useAuthStore       // User auth state, token, session
useAccountStore    // Trading accounts, balances
usePositionStore   // Open positions, real-time P/L updates
useOrderStore      // Pending orders, order history
useNotificationStore // Alerts, notifications
useUIStore         // Theme, sidebar state, modals
useKycStore        // KYC status, document uploads
useTransactionStore // Deposit/withdrawal state
```

**Real-time data flow**:

```
WebSocket Message (from xCritical CRM)
    |
    v
WebSocket Handler (client-side singleton)
    |
    v
Zustand Store Update (e.g., usePositionStore.getState().updatePosition(data))
    |
    v
Component Re-render (via useShallow selectors -- 60% fewer re-renders)
```

**Server State vs Client State boundary**:
- **Server State** (fetched from API, cacheable): Account list, KYC status, transaction history, static profile data. Use Server Components for initial fetch, React Query or SWR for client-side refetching.
- **Client State** (ephemeral, interactive): Selected account, form state, UI state, WebSocket connection. Use Zustand stores.

**Critical pattern for trading**: Optimistic updates should NOT be used for financial operations (deposit, withdrawal, trade execution). Always wait for server confirmation before updating state.

---

## 11. Security Considerations

### Frontend Security Architecture

1. **Token Storage**: HttpOnly, Secure, SameSite=Strict cookies. NEVER localStorage.
2. **XSS Prevention**: React's built-in JSX escaping, DOMPurify for user HTML, CSP headers via Next.js middleware.
3. **CSRF Protection**: SameSite cookie + custom header validation. Server Actions have built-in CSRF protection.
4. **Input Validation**: All API responses validated with type guards (unknown + type guards, never any). Financial amounts: use string or integer cents.
5. **API Security**: xCritical CRM credentials ONLY in server-side env vars. Rate limiting on API routes.
6. **KYC/AML**: Validate file type and size server-side. Log all KYC state changes. Follow broker's jurisdiction requirements.

---

## 12. Scalability Architecture

### Per-Broker Scaling Model

```
Small Broker (10-500 traders):
  - 1-2 replicas, basic container setup

Medium Broker (500-5,000 traders):
  - 2-4 replicas behind load balancer, CDN for static assets

Large Broker (5,000+ traders):
  - 4-8+ replicas, auto-scaling, CDN + edge caching, WebSocket sticky sessions
```

---

## 13. Key Architecture Risks and Mitigations

| Risk | Severity | Mitigation |
|------|----------|------------|
| xCritical CRM API instability | HIGH | Circuit breaker pattern in BFF, graceful degradation UI, retry with backoff |
| Solo developer bandwidth | CRITICAL | Module-first architecture enables focus on one module at a time |
| WebSocket reliability | MEDIUM | Exponential backoff reconnection, polling fallback, connection health indicator |
| Per-broker deployment complexity | MEDIUM | Standardize CI/CD early. Consider infrastructure-as-code |
| Financial data accuracy | HIGH | Never use floating-point for money. Use Decimal.js or integer cents |

---

## 14. Recommended Implementation Order

### Phase 0: Foundation (Weeks 1-2)
1. Broker configuration system (JSON config + type-safe loading)
2. Theming system (CSS custom properties + Tailwind v4)
3. Auth flow (xCritical CRM login + middleware)
4. BFF API layer skeleton (proxy to xCritical CRM)
5. Core layout (dashboard shell, sidebar, responsive navigation)

### Phase 1: Core Features (Weeks 3-6)
1. Account management (list, details, switch)
2. Deposit/withdrawal flows (Server Actions + forms)
3. Profile & personal data
4. KYC flow (document upload, status tracking)
5. Transaction history

### Phase 2: First Trading Module -- Prop Trading (Weeks 7-10)
1. Challenge dashboard
2. Progress tracking (profit target, drawdown limits)
3. Rule compliance monitoring
4. Performance analytics

### Phase 3: Additional Modules (Weeks 11+)
1. Copy trading module
2. PAMM module
3. Crypto module (if broker demand)

---

## 15. Technology Additions Recommended

| Package | Purpose | When |
|---------|---------|------|
| `zustand` | State management | Phase 0 |
| `zod` | Runtime type validation (API responses) | Phase 0 |
| `@tanstack/react-query` | Server state caching + refetching | Phase 1 |
| `vitest` + `@testing-library/react` | Testing | Phase 0 |
| `msw` | API mocking for tests | Phase 0 |
| `biome` | Lint + format | Phase 0 |
| `next-intl` or `next-i18next` | i18n | Phase 1 |
| `lightweight-charts` | Trading charts | Phase 2 |
| `@sentry/nextjs` | Error monitoring | Phase 1 |
| `decimal.js` | Financial calculations | Phase 1 |

---

## 16. Summary of Key Architectural Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Multi-tenancy | Per-broker build & deploy | One broker per deployment (project requirement) |
| Rendering | Hybrid SSR + CSR | Server Components for CRUD, Client for real-time |
| State management | Zustand multiple stores | Already prescribed in architecture docs |
| Theming | CSS Custom Properties + Tailwind v4 | Zero-runtime, build-time optimized |
| API pattern | BFF via Next.js API routes | Security, transformation, single backend |
| Auth | CRM-delegated + HttpOnly cookies | Financial security requirements |
| Real-time | WebSocket to xCritical CRM + polling fallback | Acceptable 15-30s latency for dashboard |
| Module system | Feature-flagged lazy modules | Tree-shaking, independent development |
| Deployment | Docker per broker | Isolation, compliance, independent scaling |
| Type validation | Zod + type guards | `any` prohibited, runtime safety for API data |
