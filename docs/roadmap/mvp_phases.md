# MyAccount MVP Phased Roadmap

> **Version**: 2.0
> **Date**: 2026-02-11
> **Team**: Product Manager (JTBD) | Business Analyst (Legacy) | Affiliate Manager (CRO) | UX/UI Lead
> **Source**: AJTBD Research (`docs/ajtbd/`), Legacy Audit, Flow Variant Analysis, Competitor Research, Emotional Journey Mapping
> **Product**: myaccount-front -- B2B white-label Forex Trader Room on xCritical CRM

---

## Executive Summary

This roadmap defines the **minimum viable path** to redesign and launch the MyAccount Forex Trader's Cabinet across 3 phases. Every feature is justified by 4 converging perspectives and qualified across **3 flow variants** and **4 platform types**.

**Perspectives**:
- **PM**: JTBD Job ID + Opportunity Score from `docs/ajtbd/`
- **BA**: AS-IS logic, status transitions, xCritical API dependencies, logic flags
- **Affiliate**: Tracking events, S2S postbacks, anti-fraud rules, conversion benchmarks per flow
- **UX**: Mobile-first design, trust signals, emotional journey, first-time states

**Key Strategic Insights**:

1. **83% Universal Core**: PM analysis shows ~83% of Phase 1 dev effort is universal across all flow variants and platforms. Only ~12% is flow-specific and ~5% is platform-specific. This means one codebase with config-driven behavior, not 3 separate products.

2. **Config-Driven Architecture**: All flow behavior is controlled by `BrokerFlowConfig` -- a broker-level JSON configuration object. Features 1.1, 1.2, 1.3, 1.4, 1.5, 1.6 all branch behavior based on this config. No flow logic is hardcoded.

3. **Flow B (Hybrid/Demo-First) as MVP Default**: Cross-team consensus: Flow B offers the best risk-adjusted ROI ($30K-$100K revenue per 1,000 registrations) with moderate compliance and highest scalability. Build and test Flow B first, then extend to A and C.

4. **First Withdrawal Is Everything**: UX emotional journey mapping shows anxiety peaks at 9/10 during first withdrawal. A fast, transparent first withdrawal converts skeptics into loyal traders (3x more likely to re-deposit). Disproportionate UX investment here yields outsized returns.

**Phasing Logic**:
- **Phase 1** = Revenue enablement (no revenue without core funnel)
- **Phase 2** = Revenue scaling (no growth without traffic tools)
- **Phase 3** = Revenue retention (no moat without engagement/analytics)

---

## Flow Variants & Platform Types

### Three Registration/KYC Flow Variants

Every feature in this roadmap must support all three flows via `BrokerFlowConfig.registrationFlow`:

| Attribute | Flow A: Licensed (CySEC/FCA) | Flow B: Hybrid (Default MVP) | Flow C: Offshore (FSC/SVG) |
|-----------|------------------------------|------------------------------|----------------------------|
| **Registration** | Full KYC before any real trading | Quick registration, demo-first | Minimal registration, no KYC |
| **KYC Gating** | Before first deposit | Before first real account only | Deferred to first withdrawal |
| **Compliance Forms** | Appropriateness Test + Economic Profile required | Appropriateness Test only | None at registration |
| **Demo Access** | After KYC OR auto-created at registration | Auto-created at registration (primary path) | Auto-created at registration |
| **Deposit Access** | After full KYC approval | After KYC approval (prompted when switching to real) | Immediate (no KYC needed) |
| **Withdrawal Access** | Standard (already KYC verified) | Standard (already KYC verified) | Triggers KYC requirement ("surprise KYC") |
| **Regulatory** | CySEC, FCA, ASIC -- strictest | Mixed jurisdictions | FSC, SVG, Comoros -- lightest |
| **Target Market Share** | ~35% of broker deployments | ~45% of broker deployments | ~20% of broker deployments |

### Conversion Benchmarks by Flow

| Metric | Flow A: Licensed | Flow B: Hybrid | Flow C: Offshore |
|--------|-----------------|---------------|-----------------|
| Landing-to-Registration | 5.3-18.2% | 5.3-18.2% | 8.3-24.5% |
| Registration-to-FTD | 7.5-21.5% | 6.4-17.0% | 38.6-46.0% |
| Overall Landing-to-FTD | 0.4-3.9% | 0.3-3.1% | 3.2-11.3% |
| **Fraud-adjusted Landing-to-FTD** | 0.4-3.9% | 0.3-3.1% | **2.4-8.5%** |
| Revenue per 1,000 registrations | $24K-$75K | **$30K-$100K** | $30K-$105K (high fraud) |
| Fraud rate (FTDs) | <2% | 3-5% | **15-25%** |

**Cross-team consensus**: Flow B provides the best risk-adjusted ROI due to higher volume with manageable fraud.

### Four Trading Platform Types

Features 1.6 (Dashboard) and some aspects of 1.1 (Registration) vary by platform. Controlled by `BrokerFlowConfig.platformType`:

| Capability | DTS v2 (Web) | MT4 | MT5 | cTrader |
|-----------|-------------|-----|-----|---------|
| Trading in-app | Full (web-based) | External terminal | External terminal | External terminal |
| Position management | Inline (close, modify, alerts) | Summary only | Summary only | Summary only |
| Account creation | Async (CRM adapter) | Sync (Manager API) | Sync (CRM integration) | Async (REST/OAuth) |
| Leverage change | In-app | Via terminal/CRM | Via terminal/CRM | Via terminal/CRM |
| Real-time P&L | Direct | Via CRM sync (latency) | Via CRM sync (latency) | Via CRM sync (latency) |
| Card CTA | "Trade Now" (opens in-app) | "Open MT4 Terminal" (external) | "Open MT5 Terminal" (external) | "Open cTrader" (external) |

**Platform distribution**: Only 3/13 Phase 1 features have platform-specific behavior (Registration: account creation sync/async, Dashboard: card rendering, Withdrawal: method filtering). The rest are 100% universal.

### BrokerFlowConfig (Governs All Behavior)

```typescript
interface BrokerFlowConfig {
  // Flow variant (core branching)
  registrationFlow: 'licensed' | 'hybrid' | 'offshore';

  // Platform
  platformType: 'dts_v2' | 'mt4' | 'mt5' | 'ctrader';
  platformConfig: {
    webTerminalUrl?: string;
    downloadLinks?: { desktop?: string; mobile?: { ios?: string; android?: string } };
    ssoEnabled: boolean;
  };

  // KYC gating
  kycGating: {
    requiredBeforeDeposit: boolean;    // true for Flow A, true for Flow B (real only), false for Flow C
    requiredBeforeWithdrawal: boolean; // true for Flow C
    requiredBeforeRealAccount: boolean;// true for Flow A & B
    tiersEnabled: boolean;             // true for Flow A
    appropriatenessTestRequired: boolean; // true for Flow A & B (EU)
    economicProfileRequired: boolean;  // true for Flow A (EU only)
  };

  // Demo account
  demoAccount: {
    autoCreateOnRegistration: boolean; // false for Flow A, true for B, configurable for C (real account is primary in C)
    initialBalance: number;
    leverage: string;
    platform: string;
  };

  // Feature toggles
  features: {
    complianceForms: boolean;
    bonusEngine: boolean;
    copyTradingIframe: boolean;
    ibPortal: boolean;
    contestsEnabled: boolean;
  };

  // White-label
  branding: {
    tier: 1 | 2 | 3;
    logo: string;
    colorTokens: Record<string, string>;  // OKLCH values
    translations: Record<string, string>;
  };
}
```

---

## Phase 1: "The Core" (Survival Mode)

**Goal**: Enable the critical path across all 3 flow variants: **Registration -> (KYC) -> Deposit -> Trade -> Withdraw**. Migrate all 15+ existing broker deployments. Enable traffic buying with minimum viable tracking.

**Timeline**: Months 1-3 (~97 dev-days)
**Constraint**: A broker cannot sell this product until every step works. Missing ANY step = zero revenue.

**Build Order** (per PM cross-analysis):
1. Universal core infrastructure (white-label engine, mobile layout, i18n, auth) -- 33 dev-days
2. Financial operations (deposit, withdrawal) -- 15 dev-days
3. Dashboard + account management -- 15 dev-days
4. Registration + KYC (flow-variant-specific) -- 21 dev-days
5. Profile + migration + tracking -- 16 dev-days

---

### 1.1 Registration & Progressive Onboarding

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | **T-SJ1** "Register and complete onboarding." B-O5 (OS=16): Minimize onboarding abandonment. T-O4 (OS=14): Minimize fields before trading. 25-40% dropout at this step -- every lost lead = $0 broker revenue. Time to Value: #1 (first interaction). |
| **BA (Legacy)** | AS-IS: Email/phone + password -> OTP -> Lead auto-created in xCritical -> Trading account auto-created. Registration methods vary by brand (Confluence 1416397053). **FLAG-REG-2**: Account creation sync (MT4) vs async (cTrader). **FLAG-REG-3**: "Second method" means progressive profile completion required. Stepper config: page 3982721069. |
| **Affiliate (CRO)** | **BLOCKER**: Registration S2S postback (Facebook CAPI `Lead`, Google `sign_up`, TikTok `CompleteRegistration`, generic S2S). UTM capture on page load (store in cookie + localStorage). Anti-fraud: disposable email block, CAPTCHA, velocity check (>3/IP/hr). Target: 80%+ form completion rate, <2 min. |
| **UX** | Progressive disclosure: Email + password + country (auto-detect) -> Basic info post-login. Mobile-first: 48px tap targets, `inputmode` hints, one field per viewport. Trust signals: regulatory badge, encryption micro-copy, no-spam guarantee. Skeleton: horizontal progress bar "Step 1 of 2". |

#### Flow Variant Behavior

| Step | Flow A: Licensed | Flow B: Hybrid (Default) | Flow C: Offshore |
|------|-----------------|--------------------------|------------------|
| **Form fields** | Email + password + country | Email + password + country | Email + password + country |
| **Post-registration** | Redirect to KYC stepper (mandatory) | Redirect to dashboard with auto-demo | Redirect to dashboard with auto-real-account (+ optional demo) |
| **Demo account** | Not auto-created (or optional) | Auto-created (DTS/MT5, $10K virtual, 1:100) | Optional (real account is primary) |
| **First CTA** | "Verify Your Identity" (primary) | "Trade Now" on demo (primary), "Go Live" (secondary) | "Deposit Now" (primary), "Trade Demo" (secondary) |
| **KYC prompt** | Mandatory before any trading | Soft-gated: prompted when user clicks "Open Live Account" | None (deferred to withdrawal) |
| **Emotional state** | Anxiety 6/10 -> Cautious | Anxiety 5/10 -> Excited (demo reduces barrier) | Anxiety 3/10 -> Eager (zero barriers) |

**Config fields**: `registrationFlow`, `demoAccount.autoCreateOnRegistration`, `kycGating.requiredBeforeRealAccount`

#### Platform-Specific Notes

| Platform | Account Creation | Notes |
|----------|-----------------|-------|
| DTS v2 | Asynchronous (CRM adapter) | Show "Account being created..." with polling. .NET SDK integration |
| MT4 | Synchronous (Manager API) | Account created via sync call, may timeout under load |
| MT5 | Synchronous (CRM integration) | Hedging/netting mode selected at creation |
| cTrader | Asynchronous (REST callback) | Show "Account being created..." with polling. FLAG-REG-2 |

**JTBD Score Adjustment by Flow**: B-O5 (Onboarding abandonment) stays at OS=16 for Flow A (highest friction, most opportunity), drops to OS=14 for Flow B (demo access reduces risk), and OS=12 for Flow C (minimal steps, low risk).

**Success Metrics**: Registration-to-KYC rate >75% (Flow A/B) | Mobile completion parity with desktop | <2 min completion time
**B2B Value**: +35% conversion to FTD when optimized (AJTBD)
**Effort**: ~10 dev-days (70% universal, 30% flow-specific, 0% platform-specific)

---

### 1.2 KYC Document Upload & Verification

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | **T-SJ6** + **B-SJ4**: "Manage documents and KYC" / "Ensure KYC/Compliance." B-O4 (OS=15): KYC verification speed. T-O23 (OS=13): Minimize document rejections. 15-30% dropout -- second largest funnel leak. |
| **BA (Legacy)** | States: NotStarted -> DocumentsUploaded -> UnderReview -> Approved/Rejected -> Resubmit. Tiers: Tier 0 (unverified), Tier 1 (ID), Tier 2 (ID+PoA), Tier 3 (Enhanced). Brand variations: ForexBY (Belarus), 1Prime (Forex+BO), GWG custom. **FLAG-KYC-1**: Review is manual in CRM; frontend is display-only. **FLAG-KYC-2**: Appropriateness Test failure handling varies by jurisdiction. **FLAG-KYC-3**: Economic Profile only for EU-regulated brands. |
| **Affiliate (CRO)** | **HIGH**: KYC milestone tracking (kyc_started, kyc_document_uploaded, kyc_approved, kyc_rejected). Facebook custom event `SubmitApplication`. Anti-fraud: document reuse detection, OCR tampering, face mismatch. Improving KYC completion by 10% = +$6,500/month per broker. |
| **UX** | 5-step stepper: Personal Info -> ID Document -> Address Proof -> Selfie (optional) -> Review. Camera-first on mobile with frame overlay and quality pre-check. Real-time status (blue->amber->green). Error recovery: specific rejection reasons with visual guidance. Auto-save every 2 seconds. Emotional state: Anxiety 8/10 at ID upload (highest in KYC flow). Design interventions: "Why we need this" expandable, security messaging, "Usually less than 24 hours" ETA. |

#### Flow Variant Behavior

| Aspect | Flow A: Licensed | Flow B: Hybrid (Default) | Flow C: Offshore |
|--------|-----------------|--------------------------|------------------|
| **When triggered** | Immediately after registration | When user clicks "Open Live Account" | At first withdrawal request |
| **Required tiers** | Tier 2+ (ID + Address Proof) | Tier 1-2 (configurable) | Tier 1 minimum |
| **Appropriateness Test** | Required (MiFID II) | Required for EU brands, optional otherwise | Not required |
| **Economic Profile** | Required (EU only) | Optional | Not required |
| **Emotional context** | User expects it (regulatory) | User wants to "go live" (motivated) | User wants money back (angry/anxious) |
| **UX framing** | "Regulatory requirement for your protection" | "One more step to start real trading" | **CRITICAL**: "To protect your funds, verification is required for withdrawals. Your withdrawal is SAVED." |
| **Abandonment risk** | 15-30% (expected friction) | 10-20% (motivated to go live) | **25-40%** (surprise + anger) |
| **Drop-off prevention** | Progress stepper, auto-save, "Takes ~5 minutes" | Same + "Your demo account is ready while you wait" | Preserve withdrawal request, show KYC inline, auto-process withdrawal after approval |

**Flow C "Surprise KYC" -- Critical UX Pattern**:
The offshore flow creates the highest-anxiety moment in the platform when KYC is triggered at withdrawal. The UX must:
1. **Pre-emptively warn**: Before ANY deposit, show subtle banner: "Withdrawals require identity verification"
2. **Preserve the withdrawal**: Never cancel the pending request
3. **Show inline progress**: "Complete KYC -> Your $200 withdrawal processes automatically"
4. **Frame as protection**: "Regulatory requirements mandate verification to protect YOUR funds"
5. **Never imply broker is blocking**: Frame as external/regulatory, not broker choice

**Config fields**: `kycGating.requiredBeforeDeposit`, `kycGating.requiredBeforeWithdrawal`, `kycGating.tiersEnabled`, `kycGating.appropriatenessTestRequired`, `kycGating.economicProfileRequired`

**Success Metrics**: KYC completion >65% (from ~45%) | First-attempt approval >70% | Cycle time <24h
**B2B Value**: -40% manual compliance reviews, 72h->24h KYC cycle
**Effort**: ~8 dev-days

---

### 1.3 Compliance Forms (Appropriateness Test + Economic Profile)

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | **B-SJ4** (B-MJ4.3, B-MJ4.4). B-O10 (OS=14): Minimize regulatory violations. Hard blocker for ~35% of target market (CySEC/FCA). Not a UX differentiator but a survival requirement. |
| **BA (Legacy)** | Appropriateness Test (MiFID II): quiz on leverage, margin, risk. Score determines CFD access. Economic Profile: income, employment, experience, risk tolerance. Configured per broker/jurisdiction via xCritical. |
| **Affiliate (CRO)** | No direct tracking impact. Ensure forms don't add excessive friction (target: >80% completion rate). |
| **UX** | Integrated into KYC stepper. Clear progress. Simple language for complex regulatory questions. Mobile-friendly radio/select inputs. |

#### Flow Variant Behavior

| Aspect | Flow A: Licensed | Flow B: Hybrid | Flow C: Offshore |
|--------|-----------------|---------------|-----------------|
| **Appropriateness Test** | Required | Required for EU brands only | Not required |
| **Economic Profile** | Required (EU) | Optional | Not required |
| **Effort impact** | Full 3 dev-days | 2 dev-days (conditional) | 0 dev-days (skipped) |

**JTBD Score Adjustment**: B-O10 (Compliance) stays at OS=14 for Flow A (mission-critical), drops to OS=12 for Flow B (relevant for regulated entity), and OS=0 for Flow C (irrelevant -- no compliance requirement).

**Config fields**: `kycGating.appropriatenessTestRequired`, `kycGating.economicProfileRequired`

**Success Metrics**: 100% MiFID II compliance | >80% completion rate
**Effort**: ~3 dev-days (forms are CRM-config-driven)

---

### 1.4 Deposit Flow (One-Click Redesign)

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | **T-SJ2**: "Fund trading account." B-O12 (OS=17, #3 overall): FTD conversion. T-O1 (OS=14): Deposit speed. B-O7 (OS=16): Repeat deposits. **40-60% dropout at first deposit** -- single largest revenue leak. |
| **BA (Legacy)** | States: SelectMethod -> EnterAmount -> Confirm -> RedirectToPSP/ShowBankDetails/ShowCryptoAddress -> Success/Fail. 30+ PSPs (Stripe, Xpate, Connpay, AstroPay, KoraPay, etc.). **FLAG-DEP-1**: Frontend only initiates; CRM handles PSP communication. **FLAG-DEP-2**: Idempotency critical (disable button after click). **FLAG-DEP-3**: Promo code bug CRM-42594 (verify fix). **FLAG-DEP-4**: Balance update timing varies by PSP type. Min/max configurable per method per broker. |
| **Affiliate (CRO)** | **BLOCKER**: FTD S2S postback (Facebook CAPI `Purchase`, Google OCI, TikTok `CompletePayment`, generic S2S with `revenue={amount}`). `InitiateCheckout` client-side on form submit, `Purchase` server-side on CRM confirmation. Anti-fraud: min deposit threshold, chargeback monitoring >1%, deposit-then-withdraw-no-trade detection. |
| **UX** | Flow: Select Account -> Select Method (saved prioritized) -> Enter Amount (presets: $100/$250/$500/$1000 + custom) -> Confirm (summary) -> Success. One-click: returning users see last method + last amount pre-filled. "Repeat Last Deposit" on dashboard. Fee display BEFORE submission. Trust: PCI DSS seal, transaction ID immediately visible. Mobile: full-screen bottom sheet. Emotional state: Anxiety 7/10 at first deposit ("real money on the line"). Balance counter animates from old to new value on success (300ms). |

#### Flow Variant Behavior

| Aspect | Flow A: Licensed | Flow B: Hybrid (Default) | Flow C: Offshore |
|--------|-----------------|--------------------------|------------------|
| **Access** | After full KYC approval | After KYC approval | Immediate (no KYC) |
| **First deposit emotional context** | User is KYC-verified, somewhat trusted | User is KYC-verified, motivated to trade live | User has zero verification -- highest fraud risk |
| **UX framing** | "Your account is ready for funding" | "Your live account is funded -- start trading" | "Fund your account to start trading" |
| **Trust signals** | Standard (already KYC-verified) | Standard + "Funds segregated in tier-1 banks" | **Enhanced**: regulatory badges, fund safety messaging, "Withdrawals require verification" pre-warning |
| **Anti-fraud** | Standard | Standard | **Enhanced**: min deposit threshold, velocity limits, device fingerprinting |
| **FTD conversion benchmark** | 7.5-21.5% (from reg) | 6.4-17.0% (from reg) | 38.6-46.0% (from reg, but 15-25% fraud) |

**JTBD Score Adjustment**: B-O12 (FTD conversion) is highest for Flow A (OS=17) where KYC gates create the most optimization opportunity, OS=15 for Flow B (demo-to-real is the real barrier), and OS=13 for Flow C (no gate, high natural conversion -- less opportunity to improve).

**Config fields**: `kycGating.requiredBeforeDeposit`

**Success Metrics**: Deposit abandonment <20% | FTD conversion >38% (from ~30%) | <3 clicks for returning users
**B2B Value**: +15-25% FTD rate = +$4-8K/month per broker
**Effort**: ~10 dev-days

---

### 1.5 Withdrawal Flow with Status Tracker

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | **T-SJ4**: "Withdraw profits." T-O3 (OS=17, #4 overall): Minimize withdrawal status uncertainty. B-O6 (OS=14): Minimize support tickets. **#1 emotional moment for traders**. "Where is my money?" = 25-30% of all support tickets. |
| **BA (Legacy)** | States: Pending -> Processing -> Approved -> Sent -> Completed / Rejected / Cancelled. AML rule: withdrawal method matches deposit method. Balance validation: Available = Balance - Margin - Bonus Lock. **FLAG-WDR-1**: Frontend cannot approve/reject (CRM only). **FLAG-WDR-2**: Bonus lock calculations come from CRM API only. **FLAG-WDR-3**: Available methods filtered by CRM (AML). **FLAG-WDR-4**: 2FA is broker-configurable. Cancellation only before Processing state. |
| **Affiliate (CRO)** | **MEDIUM**: Withdrawal S2S postback (withdrawal_request, withdrawal_completed). Anti-fraud: no-trade withdrawal hold, amount > deposits review, rapid deposit-withdraw cycle detection. Successful first withdrawal = trader 3x more likely to re-deposit. |
| **UX** | 5-step visual status timeline: Requested -> Processing -> Approved -> Sent -> Completed. Mobile: vertical timeline with timestamps + ETA. Desktop: horizontal. Status colors: completed=green, current=blue+pulse, upcoming=gray, rejected=red. Push notification integration points at each transition. ETA based on historical data per method. Overdue warning in amber. **Emotional state: Anxiety 9/10 -- HIGHEST in entire journey**. "Will they actually give me my money?" This is THE trust-building moment. |

#### Flow Variant Behavior

| Aspect | Flow A: Licensed | Flow B: Hybrid (Default) | Flow C: Offshore |
|--------|-----------------|--------------------------|------------------|
| **KYC status at withdrawal** | Already verified | Already verified | **NOT verified -- triggers KYC flow** |
| **Available methods** | Full (card, bank, e-wallet) | Full | May be restricted until KYC |
| **AML matching** | Strict (withdrawal method must match deposit method) | Strict | Strict post-KYC |
| **Processing time** | 1-3 business days (standard) | 1-3 business days | **Delayed by KYC review time** |
| **UX pattern** | Standard status tracker | Standard status tracker | **Special "KYC + Withdrawal" combined flow**: KYC steps shown inline, withdrawal auto-processes after approval |
| **Emotional design** | Standard transparency | Standard transparency | **Maximum anxiety reduction**: "Your withdrawal is saved. Complete verification and it processes automatically." |
| **Fraud risk** | Low (<2%) | Low-moderate (3-5%) | **High (15-25%)**: no-trade withdrawal detection, rapid deposit-withdraw cycle |

**Critical UX for Flow C Withdrawal**:
```
User clicks "Withdraw" -> System detects no KYC ->
  Modal: "To process your withdrawal, we need to verify your identity.
         Your withdrawal of $200 is SAVED and will process automatically
         after verification. This usually takes less than 24 hours."
  [Start Verification] [Learn Why]
  -> KYC stepper (inline, not a separate page)
  -> On KYC approval: "Verified! Your $200 withdrawal is now processing."
```

**Trust inflection point**: Successful first withdrawal = anxiety drops from 9/10 to 3/10. Trust jumps from 6/10 to 8/10. User becomes 3x more likely to re-deposit. This single moment determines long-term retention.

**Config fields**: `kycGating.requiredBeforeWithdrawal`

**Success Metrics**: Withdrawal support tickets -50-60% | Satisfaction >4/5 | 100% status visibility
**B2B Value**: -$1,000-$3,000/month support savings per broker
**Effort**: ~5 dev-days

---

### 1.6 Trading Account Dashboard

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | **T-SJ3**: "Manage trading accounts." T-O6 (OS=14): Account clarity on one screen. T-O21 (OS=13): Quick status overview. Daily touchpoint for all segments. |
| **BA (Legacy)** | Platforms: MT4 (Manager API, sync), MT5 (full integration, hedging/netting modes), cTrader (REST/OAuth), DTS v2 (.NET SDK). Account types: live, demo, Islamic/swap-free, cent. Internal transfers validated against available balance. Cross-currency transfers use CRM exchange rate. **FLAG-ACC-1**: Sync vs async account creation per platform. **FLAG-ACC-2**: Leverage options platform-dependent. **FLAG-ACC-3**: Cross-currency transfers need rate display. |
| **Affiliate (CRO)** | No direct tracking on dashboard. First Trade S2S postback triggers from CRM when trader executes on platform. |
| **UX** | Mobile: Total Balance (prominent) -> Quick Actions (Deposit/Withdraw/More) -> Account Cards (horizontal scroll) -> Pending Actions (KYC, withdrawals) -> Recent Activity (last 5 transactions). Desktop: Sidebar + main content with multi-panel layout. Account cards: platform icon + number + balance + daily P&L. Tap to expand: leverage, margin, equity, free margin. Balance: tabular figures, "Last updated: Xs ago." |

#### Platform-Specific Account Card Rendering

The dashboard is the **primary feature where platform type matters**. Cards render differently based on `BrokerFlowConfig.platformType`:

| Card Element | DTS v2 ("Full Control") | MT4/MT5/cTrader ("Monitor + External") |
|-------------|------------------------|----------------------------------------|
| **Positions** | Inline list with close/modify buttons | Summary count + aggregate P&L only |
| **Primary CTA** | "Trade Now" (opens DTS in-app) | "Open [Platform] Terminal" (external link) |
| **Position management** | Close, modify, set alerts -- all in-app | "To manage positions, open [Platform] terminal" |
| **Credentials shown** | None (SSO) | Login number + Server (password in Details page) |
| **Platform launcher** | Single button (opens web app) | Multi-option: WebTerminal / Desktop / Mobile App |

**Multi-Account Dashboard Scenarios** (from UX research):

| Scenario | Account Count | Display Pattern | Header |
|----------|--------------|-----------------|--------|
| Single account | 1 | Expanded single card | Balance: $X |
| Same type pair | 2 (e.g., 2x MT5) | Side-by-side cards | Total: $X |
| Mixed platforms | 2+ (e.g., DTS + MT5) | Cards with platform-specific rendering | Total: $X |
| Many accounts | 5+ | Compact card grid + header dropdown pill | Total: $X + account selector |
| Demo + Real mix | Any | Grouped: "LIVE" section first, "DEMO" section below | Real: $X | Demo: $Y (separate, never aggregated) |

**State-Aware Dashboard** (from UX first-time states research):

| User State | Dashboard Variant | Primary CTA | Key Components |
|-----------|-------------------|-------------|----------------|
| State 1: Just registered, no accounts | Welcome + onboarding | "Verify Identity" or "Try Demo" | Progress stepper, getting started guide |
| State 2: Has demo only (Flow B/C) | Demo-first dashboard | "Trade Now" (demo) + "Open Live Account" | Demo card, market highlights, "Go Live" CTA |
| State 3: KYC done, $0 balance | "Ready to Trade" | "Make Your First Deposit" | Account card ($0 in neutral gray), deposit CTA, payment logos |
| State 4: First deposit confirmed | "You're In" celebration | "Start Trading" | Confirmation banner, platform launcher, tutorial link |
| State 5: Returning after 30+ days | "Welcome Back" | Contextual (check positions / deposit / update docs) | "While you were away" summary, open positions, document alerts |
| Active user | Standard dashboard | Dynamic | All sections, full feature access |

**Design rule**: $0 balance is framed as "Ready to fund," never "Empty." Demo accounts use blue styling, live accounts use green. Demo balance never shows P&L color coding.

#### Flow Variant Dashboard Differences

| Dashboard Aspect | Flow A: Licensed | Flow B: Hybrid (Default) | Flow C: Offshore |
|-----------------|-----------------|--------------------------|------------------|
| **Post-registration dashboard** | KYC-gated: sidebar dimmed, "Verify Identity" dominant | Demo-first: demo card prominent, "Go Live" secondary | Full access: deposit CTA dominant, demo available |
| **Progress stepper** | Register [done] > Verify > Fund > Trade | Register [done] > (Verify when ready) > Fund > Trade | Register [done] > Fund > Trade > (Verify at withdrawal) |
| **Sidebar items dimmed** | Most items until KYC | Wallet/Withdraw until real account | None (full access from start) |
| **Deposit CTA visibility** | After KYC only | After KYC or for real account | Immediate |

**Config fields**: `platformType`, `platformConfig`, `demoAccount`, `features`

**Success Metrics**: Account status comprehension <5 seconds | Account creation >95% completion | Mobile-desktop parity
**B2B Value**: +20% DAU/MAU target
**Effort**: ~12 dev-days

---

### 1.7 Personal Data & Profile Management

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | **T-SJ6** (maintenance). B-O16 (OS=14): Maximize self-service operations. Every profile change without a support ticket saves $10. |
| **BA (Legacy)** | Personal data stored in xCritical CRM. Frontend edits submitted via API. Change history tracked. Document re-upload for expired KYC. |
| **UX** | Clean form layout. Change history visible. Self-service document re-upload. Mobile-optimized. |

**Flow impact**: Minimal. Profile management is 100% universal across all flows. The only variant is that Flow C users may have sparser profiles (no KYC data until withdrawal triggers it).

**Effort**: ~3 dev-days

---

### 1.8 White-Label Theme Engine

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | **B-SJ1**: "Deploy branded portal." B-O1 (OS=12): Table stakes. Without branding, zero broker sales. Enables entire B2B model. |
| **BA (Legacy)** | 15+ existing brands with custom logos, colors, fonts. Configurable texts per broker (Confluence 1470234879). Per-broker feature toggles (Confluence 3018883739). |
| **UX** | 3-tier system: **Tier 1 Cosmetic** (colors via OKLCH tokens, logo, fonts, border-radius -- zero-code). **Tier 2 Structural** (module visibility, onboarding steps, payment methods -- JSON config, maps to `BrokerFlowConfig`). **Tier 3 Behavioral** (custom PSP flows, compliance rules -- requires testing). CSS custom properties for instant theming. Dark mode readiness from Day 1 (tokens defined, ships light-only in Phase 1). |

**White-label + flow variant integration**: Tier 2 (Structural) is where `BrokerFlowConfig` lives. A broker choosing "Licensed" flow gets different module visibility, onboarding steps, and compliance forms than one choosing "Hybrid" or "Offshore." All controlled by the same JSON config.

**Competitor benchmark** (from UX competitor research):
- B2Core: Full widget customization (drag/resize) -- overkill for Phase 1
- Match-Trader: PWA with minimal customization -- too limited
- **Our position**: Tier 1+2 in Phase 1 covers 90% of broker needs; Tier 3 in Phase 2+

**Effort**: ~8 dev-days

---

### 1.9 Mobile-First Responsive Layout

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | Cross-cutting. T-O13 (OS=15): Mobile page load. T-O22 (OS=15): Cross-device adaptiveness. **55% of traders are mobile-first.** |
| **BA (Legacy)** | Legacy frontend not mobile-optimized. |
| **UX** | 5 breakpoints: xs(320) -> sm(375) -> md(768) -> lg(1024) -> xl(1440). Mobile: bottom nav (56px, 5 items: Home, Accounts, Deposit [elevated], History, Profile), cards over tables, full-screen modals, bottom sheets, pull-to-refresh. Tablet: collapsed sidebar (64px icons), 2-column hybrid. Desktop: full sidebar (240px, max 8 top-level items), multi-panel, data tables. All touch targets >= 44px. |

**Mobile navigation** (5 items):
1. Dashboard (Home icon)
2. Accounts (Wallet icon)
3. Deposit (+ in circle, brand primary, elevated)
4. History (Clock icon)
5. Profile (Person icon)

**Desktop sidebar** (8 max):
1. Dashboard, 2. Accounts, 3. Wallet, 4. Deposit, 5. Withdraw, 6. Transactions, 7. Trading, 8. Support

**Platform impact**: 100% universal. No flow or platform variance in layout system.

**Effort**: ~15 dev-days (cross-cutting, spans all components)

---

### 1.10 i18n Framework + RTL Support

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | **B-SJ8**: "Scale to new markets." B-O8 (OS=11): Table stakes for migration. Required for Arabic/Hebrew brokers (UAE/MENA ~15% of market). |
| **BA (Legacy)** | Multi-language per deployment. RTL for Arabic, Hebrew, Farsi. Translation overrides per broker (e.g., "Deposit" -> "Fund Account"). Bug CRM-42612: missing translations in legacy. |
| **UX** | CSS logical properties (`margin-inline-start`, not `margin-left`) from Day 1. Text expansion: German/Russian 30-40% longer (flex containers). Locale-aware number formatting (`Intl.NumberFormat`), date formatting (`Intl.DateTimeFormat`). Currency positioning varies by locale. |

**Flow impact**: 100% universal. Translation overrides are part of `BrokerFlowConfig.branding.translations`.

**Effort**: ~5 dev-days

---

### 1.11 Legacy User Migration Toolkit

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | Strategic risk mitigation. B-O5 (OS=16) in migration context. 15+ brokers = captive audience, zero-CAC revenue base. Kill criteria: <50% adoption after 3 months. |
| **BA (Legacy)** | **Zero data loss**: All data stays in xCritical CRM; migration is frontend-only. **Feature parity** per brand: Core (deposit, withdrawal, KYC, accounts) + optional modules (copy trading iframe, PAMM, stepper, promo). Parallel deployment (old + new simultaneously). Rollback plan: DNS/config switch within 5 minutes. Smoke test checklist (Confluence 3875045410). **FLAG-MIG-1**: Copy Trading remains iframe in Phase 1. |
| **Affiliate (CRO)** | Migration must preserve all UTM-to-CRM attribution data. No tracking data loss during cutover. |
| **UX** | Visual continuity: broker branding preserved via `BrokerFlowConfig`. No "re-learn" moment for existing traders. Parallel deployment means gradual rollout. |

**Flow variant migration**: Each existing broker already operates under one of the 3 flow variants (even if not explicitly labeled). Migration maps existing brand config to `BrokerFlowConfig` format. The universal core (83%) requires no per-brand customization.

**Effort**: ~5 dev-days (config migration tooling)

---

### 1.12 Auth, Security & Password Management

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | T-O12 (OS=12): Account security confidence. VIP segment rates security CRITICAL (OS=15). |
| **BA (Legacy)** | JWT tokens from xCritical CRM. **Contradiction C1**: Legacy uses localStorage (insecure); new system MUST use HttpOnly cookies. Password change syncs CRM + trading platforms. "Login by client" feature has active bugs (CRM-42600/42602). |
| **UX** | 2FA opt-in flow. Password management with strength indicator. Session timeout with "Your progress is saved" message. |

**Flow impact**: 100% universal. Auth is the same regardless of flow or platform.

**Effort**: ~5 dev-days

---

### 1.13 Tracking Infrastructure (Phase 1 CRO Foundation)

| Perspective | Details |
|-------------|---------|
| **Affiliate (CRO)** | **7 BLOCKER features** before spending $1 on traffic: (1) UTM capture + cookie persistence (30-day), (2) Registration S2S postback, (3) FTD S2S postback, (4) Facebook CAPI integration, (5) Google Ads Offline Conversions, (6) UTM-to-CRM mapping via xCritical custom fields, (7) Postback queue + logging + retry (Redis/SQS). Client-side pixels (FB/Google/TikTok) as backup. Conversion event taxonomy: PageView, Lead, SubmitApplication, InitiateCheckout, Purchase, FirstTrade, Withdrawal. |
| **BA (Legacy)** | UTM parameters must map to xCritical CRM lead/client custom fields. Postback Worker is a new backend service (not part of legacy). CRM webhook/callback infrastructure exists for deposit/withdrawal status. |
| **UX** | No visible UI (backend infrastructure). Cookie consent banner required for EU (GDPR). |

#### S2S Postback Timing by Flow Variant

| Event | Flow A: Licensed | Flow B: Hybrid | Flow C: Offshore |
|-------|-----------------|---------------|-----------------|
| `Lead` (Registration) | On registration | On registration | On registration |
| `SubmitApplication` (KYC) | During registration flow | When user starts "Go Live" | **At withdrawal** (delayed) |
| `InitiateCheckout` (Deposit start) | After KYC approval | After KYC approval | Immediately |
| `Purchase` (FTD) | After deposit confirmed | After deposit confirmed | After deposit confirmed |
| `FirstTrade` | On first trade execution | On first trade execution | On first trade execution |

**Flow C tracking challenge**: The `SubmitApplication` event fires much later (at withdrawal), which breaks standard funnel attribution timing. Solution: use `Purchase` as the primary optimization event for Flow C campaigns, not `SubmitApplication`.

**Traffic source optimization by flow** (from affiliate research):
- Flow A: Best for Google Ads, LinkedIn (intent-based, high-quality)
- Flow B: Best for Facebook, TikTok, YouTube (volume-based, demo as hook)
- Flow C: Best for push notifications, native ads, email (aggressive, low-barrier)

**CPA rates by flow and GEO tier**:
| Flow | Tier 1 (US/UK/EU) | Tier 2 (MENA/LATAM) | Tier 3 (SEA/Africa) |
|------|-------------------|---------------------|---------------------|
| Flow A | $800-$1,850 | $400-$800 | $100-$300 |
| Flow B | $600-$1,400 | $300-$600 | $80-$200 |
| Flow C | N/A (not legally operable) | $200-$500 | $30-$120 |

**Effort**: ~8 dev-days (backend Postback Worker + pixel integration)

---

### Phase 1 Summary

| # | Feature | Primary Outcome (OS) | Effort | Universal % | Priority |
|---|---------|---------------------|--------|-------------|----------|
| 1.1 | Registration & Onboarding | B-O5 (16), T-O4 (14) | 10d | 70% | Critical |
| 1.2 | KYC Document Upload | B-O4 (15), T-O23 (13) | 8d | 80% | Critical |
| 1.3 | Compliance Forms | B-O10 (14) | 3d | 85% | Critical (EU) |
| 1.4 | One-Click Deposit | B-O12 (17), T-O1 (14) | 10d | 90% | Critical |
| 1.5 | Withdrawal Tracker | T-O3 (17), B-O6 (14) | 5d | 85% | Critical |
| 1.6 | Account Dashboard | T-O6 (14), T-O21 (13) | 12d | 50% | Critical |
| 1.7 | Personal Data | B-O16 (14) | 3d | 100% | High |
| 1.8 | White-Label Engine | B-O1 (12) | 8d | 100% | Critical (B2B) |
| 1.9 | Mobile-First Layout | T-O13 (15), T-O22 (15) | 15d | 85% | Critical |
| 1.10 | i18n + RTL | B-O8 (11) | 5d | 100% | Critical (migration) |
| 1.11 | Migration Toolkit | B-O5 (16, migration) | 5d | 90% | Critical |
| 1.12 | Auth + Security | T-O12 (12) | 5d | 90% | Critical |
| 1.13 | Tracking Infrastructure | B-O12 (17) | 8d | 85% | BLOCKER (traffic) |
| | **Total Phase 1** | | **~97d** | **~83% avg** | |

**Phase 1 Exit Criteria**:
- [ ] End-to-end funnel works for all 3 flow variants: Register -> (KYC) -> Deposit -> View Account -> Withdraw
- [ ] At least 3 existing brokers migrated successfully (ideally 1 per flow variant)
- [ ] S2S postbacks fire for registration + FTD events (verified in Facebook Events Manager + Google Ads)
- [ ] BrokerFlowConfig correctly drives all flow-specific behavior
- [ ] Platform-specific account cards render correctly for DTS v2 and MT5 (minimum 2 platforms)
- [ ] State-aware dashboard shows correct variant for all 5 user states
- [ ] Smoke test checklist passes for each migrated brand
- [ ] Mobile LCP < 2.5s, CLS < 0.1

---

## Phase 2: "Growth" (Traffic Mode)

**Goal**: Enable broker revenue growth through traffic acquisition tools. 30-60% of forex clients come through IBs/affiliates. A trader room without traffic tools is a "beautiful empty store."

**Timeline**: Months 3-6 (~56 dev-days)
**Prerequisite**: Phase 1 complete with at least 3 migrated brokers.

---

### 2.1 IB (Introducing Broker) Portal

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | **B-SJ7** (B-MJ7.5): "Set up IB/affiliate program." B-O12 (OS=17): FTD conversion (IB-referred clients have +20-30% higher FTD). B-O9 (OS=15): Analytics completeness. B-O7 (OS=16): Repeat deposits. |
| **BA (Legacy)** | AMS 1.0 under development (MISS-5223). Affiliates API (page 30809190) + Report API (page 1401159852). Partner states: Registration -> PendingApproval -> Active/Rejected -> Suspended. **FLAG-AMS-1**: AMS API may be unstable (under development). **FLAG-REP-1**: IB portal may be embedded section or separate frontend (architectural decision needed). |
| **Affiliate (CRO)** | **BLOCKER for Phase 2**: Dynamic tracking links, real-time reporting dashboard, IB S2S postback for conversions. Commission models: CPA (fixed per FTD), CPL (per lead), RevShare (% of spread/PnL), Volume RevShare ($/lot), Hybrid, Multi-Tier. Anti-fraud: self-referral detection, incentivized traffic, cookie stuffing, brand bidding, fake traffic (>90% bounce). |
| **UX** | Mobile partner dashboard: commission summary card -> key metrics (referrals, clicks, conversions, CR) -> quick actions (generate link, get promo). Desktop: multi-panel with commission charts (line/bar, 12 months). Tracking link generator with auto-copy + QR code. Promo code management with status badges (Active/Expired/Exhausted). |

**Success Metrics**: 80%+ IB portal adoption | Commission accuracy 100% | IB onboarding <1 day
**B2B Value**: Enables 30-60% of client acquisition channel
**Effort**: ~15 dev-days

---

### 2.2 Dynamic Tracking Links & Attribution

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | **B-SJ5**: "Analyze client activity." B-O18 (OS=14): Data accuracy. B-O9 (OS=15): Analytics completeness. |
| **BA (Legacy)** | First-touch attribution model. Cookie duration: 30-90 days (configurable). Tracking params: partner_id, campaign_id, sub_id, UTM parameters. |
| **Affiliate (CRO)** | Each IB gets unique links with customizable sub-parameters. 100% registration attribution to source. Click-to-registration tracking <1s latency. Postback/webhook support for affiliate networks (HasOffers, Everflow, Affise). |

**Effort**: ~5 dev-days

---

### 2.3 Promo Codes & Campaign Management

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | **B-SJ7** (B-MJ7.1): "Set up promotions." B-O7 (OS=16): Repeat deposits. Welcome bonuses +10-20% FTD rate. |
| **BA (Legacy)** | Promo/Offers module in xCritical CRM. Promo code attached to deposit (Bug CRM-42594: verify fix). |
| **Affiliate (CRO)** | Promo codes as attribution mechanism for offline/social referrals. Track redemption rate, ROI per campaign. |

**Effort**: ~8 dev-days

---

### 2.4 Affiliate Reporting Dashboard

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | **B-SJ5**: B-O9 (OS=15), B-O18 (OS=14). Top IBs (80% of referred clients) will switch to competitors without transparent reporting. |
| **BA (Legacy)** | Reports: Client, Transaction, Trading, Commission, Sub-Partner, Payout. Partners see ONLY aggregated data for their referred clients (no PII). Data served through Affiliates API. Export: CSV/PDF. **FLAG-REP-2**: Real-time commission display requires WebSocket or frequent polling. |
| **Affiliate (CRO)** | Conversion rate dashboard segmented by source, geo, device, campaign. Real-time funnel metrics. Commission dispute rate target <1%. |

**Effort**: ~10 dev-days

---

### 2.5 Push Notification System

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | **B-SJ7** (B-MJ7.2): "Configure push notifications." Connects T-O3 (OS=17) via withdrawal status updates. In-app messaging = 61-74% retention. |
| **BA (Legacy)** | Notification types: transactional (deposit/withdrawal status, KYC) + promotional (promos, re-engagement). |
| **Affiliate (CRO)** | Re-engagement notifications drive dormant trader reactivation (+3-5%). |

**Effort**: ~8 dev-days

---

### 2.6 Campaign A/B Testing & Optimization Tools

| Perspective | Details |
|-------------|---------|
| **Affiliate (CRO)** | Registration flow A/B testing, landing page variant tracking, geo-targeting rules, traffic source whitelisting/blacklisting. Typically yields 5-15% conversion improvement per quarter. |
| **UX** | Heatmap/session recording integration (Hotjar/FullStory). |

**Effort**: ~10 dev-days

---

### Phase 2 Summary

| # | Feature | Primary Outcome (OS) | Effort | Priority |
|---|---------|---------------------|--------|----------|
| 2.1 | IB Portal | B-O12 (17), B-O9 (15) | 15d | Critical |
| 2.2 | Dynamic Tracking Links | B-O18 (14), B-O9 (15) | 5d | Critical |
| 2.3 | Promo Codes / Campaigns | B-O7 (16) | 8d | High |
| 2.4 | Affiliate Reporting | B-O9 (15), B-O18 (14) | 10d | High |
| 2.5 | Push Notifications | T-O3 (17), B-O7 (16) | 8d | High |
| 2.6 | A/B Testing & Optimization | B-O12 (17) | 10d | Medium |
| | **Total Phase 2** | | **~56d** | |

**Phase 2 Exit Criteria**:
- [ ] IB portal operational with at least 1 commission model (CPA)
- [ ] Dynamic tracking links generating and attributing correctly
- [ ] At least 1 broker actively recruiting IBs through the portal
- [ ] Conversion rate dashboard showing funnel by source/geo/device
- [ ] Push notifications firing on withdrawal status changes

---

## Phase 3: "Retention" (Loyalty Mode)

**Goal**: Maximize trader LTV through engagement, analytics, social trading, and gamification. Addresses the **2 highest Opportunity Scores** in the entire AJTBD analysis (T-O8=18, T-O14=18). Creates the deepest competitive moat.

**Timeline**: Months 6-12 (~68+ dev-days)
**Prerequisite**: Phase 2 complete, active traffic flowing.

---

### 3.1 Configurable Bonus Engine

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | **B-SJ7** (B-MJ7.1): B-O7 (OS=16): Repeat deposits. Bonuses are #1 retention tool in forex. 90%+ of brokers offer deposit bonuses. |
| **BA (Legacy)** | Types: Deposit Bonus (% match), No-Deposit ($50 fixed), Cashback ($X/lot), Loyalty (volume tiers), Welcome (first deposit). Lot requirement: `Required Lots = Bonus Amount / Lot Coefficient`. Bonus-withdrawal interaction: 3 variants (reject withdrawal > available, allow but cancel bonus, cancel + remove profits). **FLAG-BON-1**: Rules vary by broker; frontend must NOT hardcode logic. **FLAG-BON-2**: Near-real-time lot progress needed. **FLAG-BON-3**: CySEC/FCA may restrict bonus types. |
| **Affiliate (CRO)** | Bonus conversion tracking: claimed -> conditions_met -> converted -> expired. Anti-fraud: multi-account bonus, hedging for bonus, rapid cycling, VPN geo-bypass. Bonus cost must factor into true CAC calculation. |
| **UX** | Bonus status cards with progress bar: traded volume vs requirement, expiry countdown. Full conditions accessible in one tap (no hidden terms). Celebration on conversion: brief golden glow (no confetti -- financial platform). |

**Flow variant note**: CySEC/FCA (Flow A) brokers may have bonus restrictions or outright bans. `BrokerFlowConfig.features.bonusEngine` toggles availability. Flow C brokers use bonuses most aggressively.

**Effort**: ~8 dev-days

---

### 3.2 Trading Contests

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | **B-SJ7** (Social Jobs). T-O8 (OS=18) -- contests require performance tracking. B-O7 (OS=16) -- contests drive deposits. +15-20% dormant reactivation, +8% weekly active users. |
| **BA (Legacy)** | Types: Demo, Live, Deposit, Volume. Rankings: Profit-based (% return), Volume-based (lots), Consistency (Sharpe). Prize distribution: top N traders, configurable %. **FLAG-CON-1**: Managed in CRM; frontend displays. **FLAG-CON-2**: Leaderboard polling 30-60s during live contests. **FLAG-CON-3**: Terms acceptance required. |
| **Affiliate (CRO)** | Contest engagement metrics: joined, ranking, final position. Contests drive deposits (+entry fees, +competitive deposits). |
| **UX** | Leaderboard card: timer + ranked list + user's own position pinned at bottom. Real-time position updates with slide animation. Anonymization options. Prize display with clear conditions. **Regulatory caution**: frame around engagement, NOT encouraging risky trading. |

**Effort**: ~10 dev-days

---

### 3.3 Advanced Analytics (P&L, Equity Curves, Win Rate)

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | **T-SJ5**: "Track trading results." **T-O8 (OS=18, #1 OVERALL)**: P&L completeness without external tools. **T-O14 (OS=18, #1 tied)**: Key trading metrics. **T-O20 (OS=16)**: Equity curve visualization. Currently, traders leave broker portal for MyFXBook (7.2M monthly visits) -- every external visit is a retention risk. |
| **BA (Legacy)** | Available from CRM: balance, equity, margin, trade history, open positions, commissions/swaps. **Requires computation**: Win rate, avg win/loss, profit factor, Sharpe ratio, max drawdown, equity curve. **FLAG-ANA-1**: Requires new backend computation service. **FLAG-ANA-2**: This is THE primary differentiator feature. |
| **Affiliate (CRO)** | Analytics engagement drives trading volume. Traders who use analytics trade 10-15% more. LTV-based lookalike audience building: "Active Traders" (10+ trades/30d) for Facebook Custom Audiences. |
| **UX** | Equity curve: interactive line chart, zoomable, time range selector (1W/1M/3M/6M/1Y/All), balance + equity overlay, crosshair on hover/touch. P&L breakdown: by instrument (bar chart with % contribution) + by period (line chart). Metrics: Win Rate (donut), Profit Factor (large number + trend), Avg Win vs Avg Loss (comparison bar), Max Drawdown (red area on equity curve), Sharpe Ratio (with benchmark context). Calendar heatmap: green/red intensity per day. Mobile: full-width cards, pinch-to-zoom charts. |

**Platform note**: Analytics data quality varies by platform. DTS v2 provides the richest real-time data. MT4/MT5/cTrader data comes through CRM with potential latency. JTBD scores: T-O8 is OS=18 for DTS (in-app analytics), OS=16 for external platforms (delayed data requires backend computation).

**Effort**: ~15 dev-days (frontend) + backend computation service

---

### 3.4 Copy/Social Trading (Native Module)

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | **T-SJ8**: "Use copy/social trading." Copy trading market $4.27B, 17.8% CAGR. Copy traders show **61-74% retention** vs 25-32% for regular traders. Currently: MT4 iframe (slow, disconnected). |
| **BA (Legacy)** | Current: MT4 Social Trading iframe (page 3981967404). PAMM integration: 5 person-days (page 3733423049). Phase 1 maintains iframe (FLAG-MIG-1); Phase 3 builds native. |
| **Affiliate (CRO)** | Copy trading adoption as engagement metric. Higher LTV traders -> better lookalike audiences. |
| **UX** | Provider selection with transparent statistics, one-click connect, risk limit setting, real-time copying results. Replace iframe with native UI. |

**Effort**: ~15 dev-days

---

### 3.5 Performance Reports (Export, Tax, Audit)

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | **T-SJ5** (T-MJ5.5): Data export. VIP segment (3-5% of base, 30-40% of revenue) -- tax-ready reports. T-O16 (OS=11). |
| **BA (Legacy)** | Account Status reports with PDF export exist (page 3875045410). New: CSV export, tax-format templates per jurisdiction. ForexBY regulatory reports (page 3498475601). |

**Effort**: ~5 dev-days

---

### 3.6 Re-Engagement & Welcome-Back Flow

| Perspective | Details |
|-------------|---------|
| **PM (JTBD)** | Dormant segment (25-35% of base). Dormant OS=16: "Minimize repeating past mistakes." Reactivation is **7x cheaper** than new acquisition. |
| **Affiliate (CRO)** | Dormant return events + reactivation deposit events. LTV-based lookalike audience uploads (Facebook Custom Audiences with value, Google Value-Based Bidding). Cohort-based LTV postbacks: Day 7, Day 30, Day 90. |
| **UX** | State 5 dashboard: "Welcome back, [Name]" with last login date (factual, not judgmental). "While You Were Away" summary card: balance changes, open position P&L, market movements since last login, new features, document expiration warnings. Primary CTA is contextual based on account state. All welcome-back elements clear after first interaction. **Anti-patterns**: Never "We missed you!", never show "money you could have made" (FOMO manipulation), never pressure with countdown timers. |

**Effort**: ~5 dev-days

---

### 3.7 LTV Optimization Infrastructure

| Perspective | Details |
|-------------|---------|
| **Affiliate (CRO)** | Multi-touch attribution models (last-click, first-click, linear, time-decay, data-driven). LTV postbacks to traffic sources at Day 7/30/90. Facebook Custom Audiences with LTV values. Google Ads Value-Based Bidding. Bonus abuse detection system. LTV-based optimization improves ROAS by 30-60% over 12 months. |

**Effort**: ~10 dev-days (backend + integrations)

---

### Phase 3 Summary

| # | Feature | Primary Outcome (OS) | Effort | Priority |
|---|---------|---------------------|--------|----------|
| 3.1 | Bonus Engine | B-O7 (16) | 8d | High |
| 3.2 | Trading Contests | T-O8 (18), B-O7 (16) | 10d | High |
| 3.3 | Advanced Analytics | T-O8 (18), T-O14 (18), T-O20 (16) | 15d+ | Critical (differentiator) |
| 3.4 | Copy/Social Trading (Native) | B-O7 (16), retention 61-74% | 15d | High |
| 3.5 | Performance Reports | T-O16 (11) | 5d | Medium |
| 3.6 | Re-Engagement Flow | Dormant (16) | 5d | High |
| 3.7 | LTV Optimization | B-O12 (17) | 10d | High |
| | **Total Phase 3** | | **~68d+** | |

**Phase 3 Exit Criteria**:
- [ ] Trader analytics dashboard operational (equity curve, P&L, win rate)
- [ ] At least 1 broker running a trading contest
- [ ] Bonus engine active with at least 2 bonus types
- [ ] LTV postbacks firing to Facebook/Google
- [ ] MyFXBook usage reduction >30% among analytics users (survey)

---

## Cross-Phase Architecture

### Design System (Spans All Phases)

| Token System | Phase 1 | Phase 2 | Phase 3 |
|-------------|---------|---------|---------|
| OKLCH color palette | Implemented | Extended | Extended |
| Dark mode tokens | Defined (ships light only) | Activatable | Default option |
| White-label 3-tier | Tier 1 + 2 | Tier 3 | Full |
| Component library | 21 components | +5 components | +8 components |
| Responsive system | 5 breakpoints | Same | Same |

### Phase 1 UI Component Library (21 Components)

| Component | Purpose | Mobile Behavior | Desktop Behavior | White-Label Level |
|-----------|---------|-----------------|-----------------|-------------------|
| `BalanceCard` | Display total/account balance | Full-width card | Compact header widget | Cosmetic |
| `AccountCard` | Trading account details (platform-specific) | Horizontal scroll card | Grid card | Cosmetic |
| `QuickActions` | Deposit/Withdraw/Transfer buttons | Fixed bottom sheet trigger | Inline button row | Cosmetic |
| `DepositForm` | Amount + method selection | Full-screen bottom sheet | Modal/inline form | Structural (methods) |
| `WithdrawalForm` | Withdrawal request form | Full-screen view | Modal form | Structural (methods) |
| `StatusTracker` | Multi-step status timeline | Vertical timeline | Horizontal timeline | Cosmetic |
| `KYCStepper` | Multi-step KYC wizard | Full-screen, one-step-per-view | Sidebar stepper | Structural (steps) |
| `DocumentUpload` | Camera/file upload for KYC | Camera-first with overlay | Drag-drop zone | Structural (doc types) |
| `TransactionList` | Recent transactions feed | Card list with status badges | Table with filters | Cosmetic |
| `BottomNav` | Primary navigation (mobile) | Fixed bottom, 5 items | Hidden (sidebar instead) | Structural (items) |
| `SidebarNav` | Primary navigation (desktop) | Hidden | Fixed left sidebar | Structural (items) |
| `FormStepper` | Progress indicator for multi-step forms | Compact bar | Full stepper with labels | Cosmetic |
| `AmountInput` | Currency-aware numeric input | Full-width, preset pills | Standard input + pills | Cosmetic |
| `PaymentMethodCard` | Saved payment method display | Full-width card | Compact card | Cosmetic |
| `AlertBanner` | System alerts, KYC reminders | Full-width top banner | Dismissable banner | Cosmetic |
| `EmptyState` | No-data placeholder | Centered illustration + CTA | Same | Cosmetic |
| `SkeletonLoader` | Content loading placeholder | Full-width blocks | Layout-matching blocks | N/A |
| `Toast` | Success/error notifications | Bottom-center | Top-right | Cosmetic |
| `ConfirmDialog` | Action confirmation | Full-screen bottom sheet | Center modal | Cosmetic |
| `LanguageSwitcher` | Locale selection | Bottom sheet list | Dropdown | Structural (locales) |
| `ThemeProvider` | White-label theming wrapper | N/A (wrapper) | N/A (wrapper) | Core infrastructure |

### Logic Flags Registry (BA Cross-Check)

| Flag | Severity | Phase | Resolution Required Before |
|------|----------|-------|--------------------------|
| FLAG-API-1 | **CRITICAL** | 1 | Frontend NEVER talks to trading platforms directly. All via xCritical CRM API. |
| FLAG-API-2 | **CRITICAL** | 1 | CRM downtime = total failure. Circuit breaker in BFF. |
| FLAG-DEP-2 | HIGH | 1 | Idempotency: disable button + CRM API guarantee. |
| FLAG-WDR-2 | HIGH | 1 | Bonus lock: CRM API returns available amount. |
| FLAG-BON-1 | HIGH | 3 | Bonus-withdrawal rules from CRM, never hardcoded. |
| FLAG-REG-2 | HIGH | 1 | Sync vs async account creation per platform. |
| FLAG-AMS-1 | MEDIUM | 2 | AMS 1.0 API may change. Treat as unstable. |
| FLAG-MATH-1 | HIGH | 1 | Frontend NEVER calculates financial values. Display only. |

### Contradictions Resolved

| # | Contradiction | Resolution |
|---|-------------|-----------|
| C1 | Legacy localStorage for auth tokens | New: HttpOnly cookies only. Zero client-side token storage. |
| C2 | KYC gating varies (EU vs offshore) | `BrokerFlowConfig.kycGating` specifies rules per flow variant. Three onboarding flows. |
| C3 | EU bonus restrictions vs bonus features | `BrokerFlowConfig.features.bonusEngine` gates availability by jurisdiction. |
| C4 | Copy Trading iframe vs React architecture | Phase 1: iframe wrapper. Phase 3: native React module. |
| C5 | "Login by client" bugs | Wait for CRM fixes (CRM-42600/42602). Track dependency. |
| C6 | Flow C "surprise KYC" at withdrawal | Pre-emptive banner before deposits + inline KYC at withdrawal + auto-process pending withdrawal. |
| C7 | Demo balance aggregation with real | NEVER aggregate. Header shows "Real: $X" and "Demo: $Y" separately. |

---

## Emotional Journey Summary

### Anxiety Curve Across Trader Lifecycle

```
ANXIETY (1-10):

10 |
 9 |                                           *  <- First Withdrawal (#1 trust moment)
 8 |            *                    *            <- KYC ID Upload / First Trade
 7 |                           *                  <- First Deposit (real money)
 6 | *                    *                       <- Registration / $0 moment
 5 |       *                                      <- First Dashboard
 4 |
 3 |                                         *    <- Second Deposit (trust established)
 2 |                                            * <- Deposits 3-4
 1 |                                              * <- Fifth Deposit (loyal trader)
 0 +--+----+----+----+----+----+----+----+----+----+-->
   Reg  1st  KYC  KYC   $0   1st  1st  1st  2nd  3-4  5th
        View      Sub  Appr  Dep  Trade WD   Dep  Dep  Dep
```

### Trust Inflection Points

1. **KYC Approval**: +2 trust (regulated, professional)
2. **First Deposit Success**: +1 trust (money went in, balance updated)
3. **First Withdrawal Paid**: +2 trust (**THE pivotal moment** -- 3x re-deposit likelihood)
4. **Second Deposit**: +1 trust (choosing to trust again)

### Flow C Inverted Anxiety Curve

Flow C (Offshore) has a fundamentally different emotional arc:
- Anxiety is LOW during onboarding (no barriers: 1-2/10)
- Anxiety SPIKES at withdrawal when "surprise KYC" triggers (9-10/10)
- Trust may DROP from 6 to 3 if poorly handled
- **Critical**: UX must transform "surprise" into "expected" via pre-deposit warnings

---

## Opportunity Score Heat Map (All Phases)

```
OS=18  T-O8 (P&L completeness) ............... Phase 3.3  [HIGHEST]
OS=18  T-O14 (Trading metrics) ............... Phase 3.3  [HIGHEST]
OS=17  B-O12 (FTD conversion) ................ Phase 1.4
OS=17  T-O3 (Withdrawal transparency) ........ Phase 1.5
OS=16  B-O5 (Onboarding abandonment) ......... Phase 1.1
OS=16  B-O7 (Repeat deposits) ................ Phase 2-3
OS=16  T-O20 (Equity curve) .................. Phase 3.3
OS=15  B-O4 (KYC speed) ...................... Phase 1.2
OS=15  B-O9 (Analytics completeness) ......... Phase 2.4
OS=15  T-O13 (Mobile load speed) ............. Phase 1.9
OS=15  T-O22 (Cross-device) .................. Phase 1.9
OS=14  B-O6 (Support tickets) ................ Phase 1.5
OS=14  B-O10 (Compliance) .................... Phase 1.3
OS=14  B-O16 (Self-service) .................. Phase 1.7
OS=14  B-O18 (Data accuracy) ................. Phase 2.2
OS=14  T-O1 (Deposit speed) .................. Phase 1.4
OS=14  T-O6 (Account clarity) ................ Phase 1.6
```

### JTBD Score Adjustments by Flow Variant

Absolute OS values per flow (source: `_pm_flow_variants.md`). Flow B is the default MVP target.

| Outcome | Global OS | Flow A (Licensed) | Flow B (Hybrid) | Flow C (Offshore) | Variance Reason |
|---------|----------|-------------------|-----------------|-------------------|-----------------|
| B-O4 (KYC speed) | 15 | 15 (blocks trading) | 13 (blocks real only) | 10 (deferred to withdrawal) | KYC urgency differs |
| B-O5 (Onboarding abandonment) | 16 | 16 (most friction) | 14 (demo reduces risk) | 12 (minimal steps) | Onboarding friction differs |
| B-O10 (Compliance) | 14 | 14 (mission-critical) | 12 (if regulated entity) | 0 (irrelevant) | Offshore has no compliance requirement |
| B-O12 (FTD conversion) | 17 | 17 (most gates to optimize) | 15 (demo-to-real barrier) | 13 (no gate, high natural conversion) | More gates = more optimization opportunity |
| T-O4 (Fields before trading) | 14 | 14 (tension with compliance) | 14 (minimized at reg) | 12 (already minimal) | Flow C already appropriately served |
| T-O5 (KYC cycle time) | 14 | 14 (impacts time-to-trade) | 12 (deferred, less urgency) | 8 (only relevant at withdrawal) | KYC timing differs dramatically |
| T-O23 (Document rejections) | 13 | 13 (rejections = extended block) | 12 (rejections delay upgrade) | 10 (rejections delay withdrawal) | Impact of rejection varies by urgency |

### JTBD Score Adjustments by Platform Type

Absolute OS values per platform (source: `_pm_flow_variants.md`). MT4/MT5 is the default (90-95% of accounts).

| Outcome | Global OS | DTS v2 | MT4/MT5 | Variance Reason |
|---------|----------|--------|---------|-----------------|
| T-O6 (Account clarity) | 14 | 16 (full in-app visibility) | 13 (limited data, dashboard less central) | DTS shows everything in-app |
| T-O8 (P&L completeness) | 18 | 18 (real-time in-app) | 16 (CRM latency, requires backend computation) | DTS provides richer real-time data |
| T-O17 (TR <-> platform transitions) | 13 | 8 (overserved -- no transitions needed) | 15 (underserved -- constant transitions) | DTS eliminates transitions; MT requires them |
| T-O21 (Quick overview) | 13 | 15 (everything in one place) | 12 (must open MT for full picture) | DTS provides richer real-time data |

---

## Cross-Team Validation Log

| Topic | Teams | Alignment Status |
|-------|-------|-----------------|
| 3 flow variants (Licensed/Hybrid/Offshore) | PM + BA + Affiliate + UX | **Aligned** -- All 4 teams validated terminology, behavior, and conversion benchmarks |
| BrokerFlowConfig as single source of truth | PM + BA | **Aligned** -- BA validated JSON schema against legacy config patterns |
| Flow B (Hybrid) as MVP default | PM + Affiliate | **Aligned** -- Best risk-adjusted ROI; build/test first |
| Flow C "surprise KYC" UX | BA + UX + Affiliate | **Aligned** -- Pre-deposit warning + preserved withdrawal + inline KYC |
| Platform-specific card rendering | BA + UX | **Aligned** -- DTS "full control" vs MT4/MT5 "monitor + external" |
| Demo balance never aggregated with real | UX + BA | **Aligned** -- Separate display, blue vs green styling |
| S2S postback timing differs by flow | Affiliate + BA | **Aligned** -- Flow C `SubmitApplication` fires at withdrawal, not registration |
| Emotional journey as design driver | UX + PM | **Aligned** -- Anxiety/trust curves inform all micro-interactions |
| 83% universal core | PM + BA | **Aligned** -- Only registration, KYC, and dashboard have significant flow variance |

### Standardized Terminology

| Term | Definition | Used By |
|------|-----------|---------|
| Flow A / Licensed | Full KYC before trading (CySEC/FCA) | All teams |
| Flow B / Hybrid | Demo-first, KYC for real account | All teams |
| Flow C / Offshore | No-KYC, deferred to withdrawal | All teams |
| FTD | First-Time Deposit | Affiliate, PM |
| S2S Postback | Server-to-server conversion callback | Affiliate, BA |
| BrokerFlowConfig | JSON config controlling all flow behavior | PM, BA |
| Universal Core | ~83% of dev effort shared across all flows | PM |
| State-Aware Dashboard | Dashboard rendering based on user lifecycle state | UX |

---

## Team Verdicts

| Role | Phase 1 | Phase 2 | Phase 3 |
|------|---------|---------|---------|
| **PM (JTBD)** | APPROVED -- Covers critical job sequence T-SJ1-SJ4 + top underserved B2B outcomes (OS 15-17). 83% universal core validated. Flow B as default confirmed. | APPROVED -- Enables IB channel (30-60% of acquisition) | APPROVED -- Addresses #1 unmet needs (OS=18) |
| **BA (Legacy)** | APPROVED with flags -- 6 critical logic flags must be resolved before development. BrokerFlowConfig validated against legacy brand configs. Platform feature gap matrix confirmed. | APPROVED with caution -- AMS 1.0 API instability (FLAG-AMS-1). Recommend mock API development in parallel. | APPROVED -- Analytics requires backend computation service (new dependency). |
| **Affiliate (CRO)** | APPROVED -- 7 BLOCKER tracking features included in 1.13. S2S timing per flow variant validated. Conversion benchmarks integrated per flow. Cannot buy traffic without them. | APPROVED -- IB Portal is THE growth engine. Commission models validated. | APPROVED -- LTV optimization (Day 7/30/90 postbacks) will improve ROAS by 30-60%. |
| **UX** | APPROVED -- Mobile-first, trust architecture, emotional journey insights, state-aware dashboard, platform-specific card rendering. 20% polish ratio ensures MVP doesn't look unfinished. | APPROVED -- Partner dashboard follows same design system. | APPROVED -- Analytics dashboard is THE differentiator. Calendar heatmap + equity curve replace MyFXBook dependency. |

---

## Next Steps

1. **Resolve critical logic flags** (FLAG-API-2, FLAG-DEP-2, FLAG-WDR-2, FLAG-REG-2) via xCritical API alignment
2. **Create `BrokerFlowConfig` reference implementation** with 3 preset configs (licensed, hybrid, offshore)
3. **User selects a feature** from this roadmap for detailed spec
4. **Team generates DELIVERABLE B** -- a feature folder at `docs/features/<feature_name>/` with:
   - `01_product_requirements.md` (PM + BA + Affiliate)
   - `02_ux_design.md` (UX + user flows + wireframes)
   - `00_approval_matrix.md` (team verdicts)
5. **Install testing infrastructure** (Vitest + RTL + MSW + Biome) per cross-team synthesis recommendation
6. **Build order**: Universal core first (1.8, 1.9, 1.10, 1.12) -> Financial ops (1.4, 1.5) -> Dashboard (1.6) -> Registration/KYC (1.1, 1.2, 1.3) -> Profile/Migration/Tracking (1.7, 1.11, 1.13)

---

*Generated by myaccount-redesign team: Product Manager (JTBD), Business Analyst (Legacy), Affiliate Manager (CRO), UX/UI Lead. Synthesized from 11 analysis documents including flow variant matrices, competitor research, emotional journey mapping, and cross-team validation. All JTBD references sourced from `docs/ajtbd/`. All legacy logic sourced from Confluence/Jira research.*
