# Project Context

## Project Overview

- **Problem being solved**: Forex brokers need a modern, white-label trader room (client portal) that increases client retention by 12-18% and reduces support costs by 30-40% through superior end-user experience — deposit, withdrawal, KYC, account management, and performance tracking
- **Product model**: B2B white-label frontend sold to individual forex brokers. Each broker deploys under their brand for their own traders. One broker per deployment. NOT a multi-broker aggregator.
- **B2B customers** (buyers of the product):
  - Small forex brokers (10-500 active clients, budget $500-$2,000/month)
  - Medium forex brokers (500-5,000 active clients, budget $2,000-$8,000/month) — primary target
  - Large forex brokers (5,000+ active clients, budget $8,000-$50,000+/month)
  - Prop trading firms requiring client-facing challenge/funded account portals
- **B2C end-users** (broker's clients, 9.6M retail forex traders globally):
  - Beginner traders (40-50% of broker base, deposits $200-$500)
  - Active day traders (15-20%, deposits $5K-$25K, generate 50-70% of commission revenue)
  - Copy/social traders (10-15%, fastest growing at 17.8% CAGR)
  - High-value/VIP traders (3-5%, deposits $25K-$500K+, 30-40% of broker revenue)
  - Dormant traders (25-35%, reactivation target)
- **Usage scenarios**:
  - Broker deploys branded trader room for their clients within 2-4 weeks
  - Traders deposit/withdraw funds, manage accounts, complete KYC within a single broker's ecosystem
  - Brokers customize features, payment integrations, onboarding flows, languages per brand via configuration
  - 55% of end-users access via mobile — all flows must be mobile-first
- **Current broker clients**: MaxiMarkets, UMarkets, LimeFX, DowMarkets, ForexBY, GWG, 1Prime, TamamBrokerage, Gracexfx, Doratrade, Worldtradex (15+ production deployments)
- **Market**: Forex CRM market $516-530M (2025) → $909-950M (2033), 6,500+ active brokers globally

## Development Structure

- **Team composition**: Solo developer (individual development)
- **Development phase**: Rewrite — migrating existing JavaScript codebase (`myaccount-front-react-forex`) to TypeScript/Next.js (Jira: CRM-42322)

## Business Constraints

1. **White-label per-broker customization**: Every UI element (logo, colors, features, languages, payment methods, onboarding steps) must be configurable per broker brand without code changes — via configuration/theming system
2. **xCritical CRM backend dependency**: All data operations (accounts, transactions, KYC, trading) flow through xCritical CRM APIs. Frontend never accesses trading platforms (MT4/MT5/cTrader/DTS) directly.
3. **Regulatory KYC/AML compliance**: Support multi-jurisdiction KYC flows (document upload, appropriateness tests, economic profiles) with audit trails. Broker-specific compliance rules configurable per deployment.
