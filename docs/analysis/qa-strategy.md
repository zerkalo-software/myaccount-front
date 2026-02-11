# QA Strategy Analysis: B2B White-Label Online Trading Platform

> **Date**: 2026-02-10
> **Analyst**: QA Architect (AI Agent)
> **Scope**: Testing and quality assurance strategy for myaccount-front

## Executive Summary

This platform presents a uniquely demanding QA challenge: it is a **financial application** handling real money where bugs directly translate to monetary loss, it is **multi-tenant** where data isolation failures have legal and business consequences, and it is **real-time** where latency and reconnection failures degrade the core value proposition. The testing strategy must reflect the severity and regulatory environment of financial software while remaining practical for a solo-developer operation.

The current project has **zero testing infrastructure** -- no Vitest, no React Testing Library, no MSW, no Biome. This is the correct time to establish the strategy before code is written.

---

## 1. Testing Pyramid for Financial Trading Applications

### Recommended Distribution

```
            /  E2E  \           ~10% of tests
           / Visual  \          ~5% of tests
          / Integration\        ~30% of tests
         / Contract Tests\      ~10% of tests
        /   Unit Tests     \    ~45% of tests
```

**Why this differs from standard web (70/20/10)**:
- Financial calculations demand integration testing across the full computation pipeline
- Multi-tenancy data isolation can only be verified at integration level
- WebSocket reconnection and real-time data flows require realistic integration scenarios
- Regulatory audit trails require end-to-end verification

### Layer Details

| Layer | Count Target | Focus | Tools |
|-------|-------------|-------|-------|
| Unit | ~45% | Pure logic: calculations, formatters, validators, type guards, state reducers | Vitest |
| Contract | ~10% | API response shape validation against xCritical CRM schema | Vitest + MSW + custom schema validators |
| Integration | ~30% | Store interactions, API flows, WebSocket lifecycle, multi-store coordination | Vitest + React Testing Library + MSW |
| Visual | ~5% | White-label theming, responsive layouts, component rendering | Storybook + Chromatic |
| E2E | ~10% | Critical user flows: login, deposit, KYC, account management | Playwright |

---

## 2. Critical Path Testing

### Priority 1: Financial Accuracy

1. **Account balance display**: Balance, equity, margin, free margin, P/L -- all must render correctly across all currency denominations
2. **Position management**: Open positions with correct lot size, entry price, current P/L, swap, commission
3. **Transaction history**: Deposits, withdrawals, internal transfers must match xCritical CRM backend data exactly
4. **KYC/AML flow**: Document submission, status tracking, rejection handling
5. **Prop trading rule compliance**: Daily loss limits, max drawdown, profit targets

**Test strategy for critical paths**:
- **Property-based testing** (fast-check or similar) for edge cases
- **Snapshot testing** of financial calculation outputs against known-correct reference datasets
- **Dual-computation verification**: Frontend calculations must be compared against backend-authoritative values
- **Boundary value analysis**: Test at exact limits (e.g., drawdown at exactly 5.00%, at 4.99%, at 5.01%)

### Priority 2: Authentication and Session Management

- Token refresh during active trading sessions
- Session timeout behavior
- Multi-tab session handling

### Priority 3: Payment Processing

- Deposit initiation and callback handling
- Withdrawal request flow and status updates
- Currency conversion display accuracy

---

## 3. Financial Calculation Accuracy

### Floating-Point Hazards

```typescript
// Classic floating-point failure in JavaScript:
0.1 + 0.2 === 0.3  // false (0.30000000000000004)
```

**Mandatory rules**:

1. **Never use native floating-point arithmetic for financial values**. Use a decimal arithmetic library (decimal.js, big.js, or dinero.js).
2. **Define precision per instrument type**:
   - Forex pairs: 5 decimal places for pricing, 2 for P/L in account currency
   - Crypto: 8 decimal places for BTC-denominated, 2 for USD-denominated
   - Indices/CFDs: 1-2 decimal places
   - Prop trading metrics (drawdown %): 2 decimal places
3. **Rounding mode must be explicit**: Use `ROUND_HALF_EVEN` (banker's rounding) unless matching xCritical CRM convention.
4. **Currency precision table**:
   | Currency | Minor units | Example |
   |----------|------------|---------|
   | USD, EUR, GBP | 2 | $1,234.56 |
   | JPY | 0 | ¥1,235 |
   | BTC | 8 | 0.00123456 |
   | ETH | 8 | 0.12345678 |

---

## 4. Real-Time Data Testing

### WebSocket Testing Scenarios

| Scenario | Priority |
|----------|----------|
| Initial connection + auth handshake | Critical |
| Price update rendering | Critical |
| Connection drop + reconnect (exponential backoff + state recovery) | Critical |
| Stale data detection (show "connection lost" after N seconds) | High |
| Message ordering (out-of-order timestamps) | High |
| Burst of updates (100+ rapid succession, no memory leaks) | Medium |
| Server-side disconnect (different close codes) | Medium |
| Tab backgrounding (pause/resume WS) | Medium |
| Multiple simultaneous connections | Medium |

---

## 5. Multi-Tenancy Data Isolation Testing

Since the architecture is "one broker per deployment," isolation is at the deployment level. Testing must verify:

1. **No hardcoded broker-specific values** in the codebase
2. **API endpoint isolation**: Frontend connects exclusively to configured xCritical CRM instance
3. **Local storage/cookie namespace**: No data leaks between deployments on shared domains
4. **White-label asset isolation**: Visual regression across deployments

---

## 6. White-Label Theming Verification

1. **CSS Custom Properties verification**: All themeable values use CSS variables, not hardcoded colors
2. **Visual regression testing**: Storybook with Chromatic across 3+ theme variants
3. **Feature flag rendering**: Conditional rendering based on broker configuration
4. **RTL layout testing**: For Arabic/Hebrew support
5. **Accessibility across themes**: WCAG 2.1 AA contrast ratios maintained

---

## 7. Regulatory Compliance Testing

### KYC/AML Test Matrix

| Jurisdiction | Required Documents | Appropriateness Test | Additional |
|-------------|-------------------|---------------------|------------|
| EU (MiFID II) | ID + Proof of address | Required | Risk warnings mandatory |
| UK (FCA) | ID + Proof of address | Required | Leverage caps |
| Offshore | ID only (varies) | Optional | Minimal requirements |

### Critical KYC Tests
1. Document upload file type/size validation
2. Status transitions: Pending -> Under Review -> Approved/Rejected
3. Rejected documents show clear reason and re-upload path
4. Partially completed KYC blocks restricted actions
5. Audit trail: every KYC action logged with timestamp

---

## 8. Performance and Load Testing

### Frontend Performance Budgets

| Metric | Target | Rationale |
|--------|--------|-----------|
| LCP | < 2.0s | Traders are impatient |
| INP | < 150ms | Interactions must feel instant |
| CLS | < 0.05 | Financial data MUST NOT shift |
| TTI | < 3.0s | Full interactivity |
| Bundle size (initial) | < 200KB gzipped | Mobile-first |
| WebSocket message processing | < 16ms | Must not block rendering (60fps) |

### Performance Test Scenarios

1. **100+ open positions** with live P/L updates
2. **50+ instruments** updating at 100ms intervals
3. **10,000+ transaction history** (virtual scrolling)
4. **10+ parallel API calls** on dashboard initialization

---

## 9. Security Testing

### OWASP Top 10 for Trading Platform

| Risk | Relevance | Testing Approach |
|------|-----------|-----------------|
| A01: Broken Access Control | CRITICAL | API responses scoped to authenticated user |
| A02: Cryptographic Failures | HIGH | HTTPS-only, HSTS headers, no sensitive data in URLs |
| A03: Injection | HIGH | CSP headers, input sanitization, React XSS protection |
| A05: Security Misconfiguration | HIGH | Automated security header checks |
| A07: Auth Failures | CRITICAL | Token expiry, refresh flow, CSRF, secure cookies |
| A09: Logging & Monitoring | HIGH | No PII in logs, audit trail completeness |

### Security-Specific Tests
1. Content Security Policy headers
2. XSS prevention for user-controlled data
3. CSRF protection for state-mutating operations
4. No tokens/passwords/PII in console, URLs, localStorage, error messages
5. Dependency vulnerability scanning in CI

---

## 10. Cross-Browser and Device Testing

### Browser Matrix

| Browser | Priority |
|---------|----------|
| Chrome (desktop) Latest 2 | Critical |
| Safari (mobile) Latest 2 | Critical |
| Chrome (mobile) Latest 2 | Critical |
| Firefox (desktop) Latest 2 | High |
| Edge (desktop) Latest 2 | Medium |
| Samsung Internet Latest | Medium |

### Device Testing

| Device Type | Priority |
|------------|----------|
| Mobile 375x667 (iPhone SE) | Critical |
| Mobile 390x844 (iPhone 14) | Critical |
| Desktop 1440x900 | Critical |
| Tablet 768x1024 (iPad) | High |
| Desktop 1920x1080 | High |

---

## 11. Chaos Engineering Scenarios

### Network Failure Scenarios

| Scenario | Expected Behavior |
|----------|-------------------|
| Total network loss | "Connection Lost" banner, queue pending actions, auto-reconnect |
| Intermittent connectivity | Retry with backoff, don't show error for brief blips (<3s) |
| API returns 500 | Error message, offer retry, don't corrupt state |
| API returns 502/503 | Automatic retry, maintenance message after 3 failures |
| WebSocket drops silently | Detect via heartbeat timeout, reconnect, re-subscribe |
| Slow API responses (>10s) | Loading indicators, allow cancellation |

### State Corruption Scenarios

| Scenario | Expected Behavior |
|----------|-------------------|
| LocalStorage corrupted | Detect, clear, re-initialize from server |
| Zustand impossible state | Validate invariants, reset to last known good |
| Race condition: two API responses | Last-write-wins with timestamp comparison |

---

## 12. CI/CD Pipeline

```
PR Created
  |
  [Stage 1: Fast Feedback] (~2 min)
    TypeScript checking, Biome lint, Unit tests (--changed)
  |
  [Stage 2: Integration] (~5 min)
    Full test suite, Contract tests, Component tests, Bundle size check
  |
  [Stage 3: Quality Gate] (~8 min)
    Coverage (60%+), Lighthouse CI, Security audit, Visual regression
  |
  [Stage 4: E2E] (~10 min)
    Playwright critical paths, Multi-theme verification, Accessibility audit
  |
  [Merge to main]
  |
  [Stage 5: Deploy to Staging]
    Deploy with broker config, Full E2E, Smoke test against CRM API
  |
  [Stage 6: Production Deploy] (manual approval)
    Blue-green/canary, Post-deploy smoke tests, Monitoring alerts
```

---

## 13. Monitoring and Alerting

### Critical Alerts

| Alert | Condition | Severity |
|-------|-----------|----------|
| Error rate spike | > 5% errors in 5-min window | P1 |
| WebSocket disconnection rate | > 10% users disconnected | P1 |
| LCP degradation | P75 > 4s for > 10 min | P2 |
| Payment API failure | Any 5xx from PSP | P1 |
| CRM API unreachable | 3 consecutive health check failures | P1 |
| Certificate expiry | < 14 days | P3 |

---

## 14. Disaster Recovery

### RTO/RPO Targets

| Component | RTO | RPO |
|-----------|-----|-----|
| Frontend application | 5 minutes (rollback) | 0 (stateless) |
| Client-side state | 0 (auto-recover from server) | Last sync point |
| User session | 15 minutes (re-authenticate) | Current session |

---

## 15. Recommended Tools and Setup

### Immediate Installation (Before First Feature)

```bash
pnpm add -D vitest @vitest/coverage-v8 @vitest/ui
pnpm add -D @testing-library/react @testing-library/jest-dom @testing-library/user-event
pnpm add -D msw
pnpm add -D @playwright/test
pnpm add -D @biomejs/biome
pnpm add -D axe-core @axe-core/playwright
pnpm add -D @lhci/cli
pnpm add -D audit-ci
```

### Package.json Scripts

```json
{
  "scripts": {
    "test": "vitest run",
    "test:watch": "vitest",
    "test:coverage": "vitest run --coverage",
    "test:e2e": "playwright test",
    "check": "biome check --write .",
    "check:ci": "biome check .",
    "check:all": "pnpm check:ci && pnpm build && pnpm test:coverage && pnpm test:e2e",
    "lighthouse": "lhci autorun"
  }
}
```

---

## 16. Priority Risks and Recommendations

### Top 5 QA Risks

| # | Risk | Mitigation |
|---|------|------------|
| 1 | Financial calculation errors reaching production | Decimal arithmetic library mandatory, property-based testing |
| 2 | No testing infrastructure in place | Install Vitest + RTL + MSW BEFORE first business logic |
| 3 | Solo developer cannot maintain coverage | Enforce 60% minimum in CI, focus on financial logic |
| 4 | WebSocket reliability untested | Robust WS mock infrastructure early |
| 5 | White-label theming regression | Visual regression in CI with 3+ theme variants |

### Implementation Priority

1. **Week 1**: Install Vitest, RTL, MSW, Biome. Configure CI stages 1-2.
2. **Week 2**: Set up Playwright, visual regression baseline, accessibility testing.
3. **Week 3**: Create test data factories, MSW handlers for xCritical CRM API.
4. **Week 4**: Configure Sentry, Lighthouse CI, security audit in pipeline.
5. **Ongoing**: Write tests alongside features. Never merge without tests for financial logic.

### Solo Developer Pragmatics

Prioritize ruthlessly:
- **Always test**: Financial calculations, authentication, data isolation
- **Usually test**: API integrations, state management, error handling
- **Test when time allows**: Visual regression, accessibility, performance
- **Defer to pre-release**: Full E2E suite, cross-browser matrix, penetration testing

**The single most important thing**: Install the testing infrastructure NOW before any business logic is written. Adding tests to an untested codebase is 3-5x more expensive.
