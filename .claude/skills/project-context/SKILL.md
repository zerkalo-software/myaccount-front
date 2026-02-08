---
name: project-context
description: Inject or update project context for myaccount-front. Use when starting work, onboarding, or after business model changes.
argument-hint: "[setup|update|validate]"
---

# Project Context — myaccount-front

## Product Model

**myaccount-front** — B2B white-label trader room (личный кабинет трейдера) для форекс-брокеров.

- Продаётся форекс-БРОКЕРАМ (B2B). Каждый брокер разворачивает под своим брендом.
- Трейдер работает с ОДНИМ брокером — мультиброкерной агрегации НЕТ.
- Backend: xCritical CRM (отдельный продукт). Frontend НИКОГДА не обращается к торговым платформам напрямую.
- Торговые платформы: MT4, MT5, DTS v2, cTrader — интеграция через xCritical CRM.
- 15+ production-деплоев: MaxiMarkets, UMarkets, LimeFX, DowMarkets, ForexBY, GWG, 1Prime и др.

## B2B Customers (покупатели)

| Сегмент | Клиентов | Бюджет/мес |
|---------|----------|-----------|
| Small broker | 10-500 | $500-$2,000 |
| **Medium broker** (target) | 500-5,000 | $2,000-$8,000 |
| Large broker | 5,000+ | $8,000-$50,000+ |
| Prop trading firms | varies | varies |

## B2C End-Users (трейдеры брокера)

| Сегмент | % базы | Депозит | Revenue impact |
|---------|--------|---------|---------------|
| Beginner | 40-50% | $200-$500 | Lowest |
| Active day trader | 15-20% | $5K-$25K | 50-70% комиссий |
| Copy/social trader | 10-15% | $1K-$5K | Fastest growing (17.8% CAGR) |
| VIP | 3-5% | $25K-$500K+ | 30-40% revenue |
| Dormant | 25-35% | — | Reactivation target |

## Existing Features

Deposit, Withdrawal, Personal Data, KYC/Documents, Account Status, Copy Trading, PAMM, Onboarding, Change Password, Promo/Offers, Appropriateness Test, Economic Profile.

## Business Constraints

1. **White-label customization** — UI полностью настраивается per broker (лого, цвета, фичи, языки, PSP) через конфигурацию, без изменений кода
2. **xCritical CRM dependency** — все данные через CRM API. Frontend = presentation layer only.
3. **KYC/AML compliance** — мультиюрисдикционные KYC-потоки с аудит-трейлами

## Development Phase

Rewrite: миграция `myaccount-front-react-forex` (JS) → TypeScript/Next.js 16 (Jira: CRM-42322)

## Key Metrics

- Forex CRM рынок: $516-530M (2025) → $909-950M (2033)
- 6,500+ активных брокеров глобально, ~72% используют CRM
- 55% трейдеров — mobile-first
- North Star: Monthly Active Transacting Traders (MATT)

## Task: $ARGUMENTS

If no argument or `setup`:
1. Read `docs/rules/project-context.md` and verify it reflects the B2B model above
2. Read `CLAUDE.md` and verify consistency
3. Report what is configured and suggest next steps

If `update`:
1. Read current `docs/rules/project-context.md`
2. Ask user what changed (new broker clients, new features, model changes)
3. Update the file with changes
4. Update this SKILL.md if business model changed

If `validate`:
1. Read all files in `docs/rules/` and `docs/ajtbd/`
2. Check for any references to "multi-broker aggregator" or incorrect model assumptions
3. Report any inconsistencies found

## AJTBD Research

Full product audit available in `docs/ajtbd/` (13 files, 5700+ lines):
- Deep Dive: 8 parts (`forex-deep-dive-*.md`)
- B2C Segments: `forex-b2c-segments.md`
- RAT Risks: `forex-rat-risks.md`
- Strategic Recommendations: `forex-strategic-recommendations.md`
- Research: `research-web-market.md`, `research-confluence-jira.md`
