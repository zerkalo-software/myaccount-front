# Forex Segment — AJTBD Product Audit

**Дата**: 2026-02-08
**Методология**: AJTBD (Advanced Jobs To Be Done)
**Продукт**: myaccount-front — B2B white-label trader room (личный кабинет трейдера) для форекс-брокеров
**Фокус**: Форекс-направление, двухуровневый анализ (B2B брокер + B2C трейдер)

---

## Бизнес-модель (ВАЖНО)

- **myaccount-front** — B2B white-label frontend, продаётся форекс-брокерам
- Каждый брокер разворачивает под своим брендом для своих трейдеров
- Трейдер работает с ОДНИМ брокером — мультиброкерной агрегации НЕТ
- Backend: xCritical CRM. Торговые платформы: MT4, MT5, DTS v2, cTrader
- Текущие клиенты: MaxiMarkets, UMarkets, LimeFX, DowMarkets, ForexBY, GWG, 1Prime и др.

---

## Файлы отчёта

### Исследования (исходные данные)

| Файл | Описание |
|------|----------|
| [`research-web-market.md`](./research-web-market.md) | Веб-исследование рынка B2B trader room, конкурентов, трендов (599 строк) |
| [`research-confluence-jira.md`](./research-confluence-jira.md) | Внутренний контент из Confluence и Jira (50+ страниц, 100+ тикетов) |

### AJTBD Deep Dive (основной анализ, 8 частей)

| Файл | Описание |
|------|----------|
| [`forex-deep-dive-overview.md`](./forex-deep-dive-overview.md) | Обзор, модель продукта, методология, оглавление |
| [`forex-deep-dive-jobs-graph.md`](./forex-deep-dive-jobs-graph.md) | Jobs Graph — двухуровневый (B2B брокер + B2C трейдер) |
| [`forex-deep-dive-outcomes.md`](./forex-deep-dive-outcomes.md) | Desired Outcomes Analysis (Ulwick ODI) — B2B + B2C |
| [`forex-deep-dive-segments.md`](./forex-deep-dive-segments.md) | Глубинные профили сегментов (B2B брокер + B2C трейдер) |
| [`forex-deep-dive-competition.md`](./forex-deep-dive-competition.md) | Конкурентный анализ через AJTBD (8 конкурентов) |
| [`forex-deep-dive-risks.md`](./forex-deep-dive-risks.md) | Риски — summary + AJTBD-специфичные |
| [`forex-deep-dive-vpc.md`](./forex-deep-dive-vpc.md) | Value Proposition Canvas (B2B + B2C + мост ценности) |
| [`forex-deep-dive-gtm.md`](./forex-deep-dive-gtm.md) | GTM-стратегия, метрики, North Star, PMF-индикаторы |

### Аналитические отчёты

| Файл | Описание |
|------|----------|
| [`forex-b2c-segments.md`](./forex-b2c-segments.md) | **5 B2C-сегментов** трейдеров с полным AJTBD-профилем (731 строка) |
| [`forex-rat-risks.md`](./forex-rat-risks.md) | **RAT-анализ** — 10 B2B + 7 B2C рисков, матрица валидации, GO/NO-GO (1012 строк) |
| [`forex-strategic-recommendations.md`](./forex-strategic-recommendations.md) | **Стратегические рекомендации** — позиционирование, ценообразование, GTM, roadmap, финмодель (1122 строки) |

---

## Ключевые выводы

### 1. Двухуровневая ценность: B2C → B2B
- B2C-ценность (лучший опыт трейдера) = главный дифференциатор в B2B-продажах
- "Выберите наш trader room, потому что ВАШИ клиенты будут довольны"
- Количественно: +$25,900/мес дополнительной ценности для среднего брокера (500 трейдеров)

### 2. Топ-3 зоны возможностей (Opportunity Score)
1. **Встроенная аналитика трейдера** (средний OS = 17.3) — ни один конкурент не решает в trader room
2. **Оптимизация онбординга / FTD** (средний OS = 15.7) — прямой impact на доход брокера
3. **Прозрачность финансовых операций** (средний OS = 14.7) — скорость вывода = #1 B2C-фича

### 3. Целевой сегмент: средний брокер (500-5000 клиентов)
- Бюджет $2,000-$8,000/мес, потребность в модульности и масштабируемости
- Sweet spot между стартапами (низкий бюджет) и enterprise (сложные требования)

### 4. Конкурентная позиция
- Ценовой gap на рынке: B2Core $200K/мес → Match-Trader $2.5K/мес
- myaccount-front позиционируется в $1,500-$4,000/мес с лучшим B2C-опытом
- Уникальное преимущество: современный стек (Next.js 16) + глубокая интеграция xCritical CRM

### 5. Критические риски (проверить ДО инвестиций)
- **F10**: Зависимость от xCritical CRM (P×I = 20) — экзистенциальный риск
- **F2**: Взрыв кастомизации (P×I = 20) — уже происходит
- **F1**: Барьер переключения (P×I = 20) — определяет модель роста

### 6. Ценовая модель
- B2B тарифы: Starter $1,500/мес → Professional $4,000/мес → Enterprise $12,000+/мес
- Модульные дополнения: $500-$1,500/мес
- ROI для брокера: 7:1

### 7. North Star Metric
**Monthly Active Transacting Traders (MATT)** — кол-во трейдеров, совершивших хотя бы одну транзакцию через myaccount-front за последние 30 дней

### 8. Roadmap
- Фаза 1 (месяцы 1-3): Завершение TypeScript-рерайта, core UX
- Фаза 2 (месяцы 3-6): B2C-дифференциаторы (аналитика, mobile-first, современный UX)
- Фаза 3 (месяцы 6-12): AI-инсайты, расширенная аналитика, advanced KYC
- Фаза 4 (месяцы 12-18): Масштабирование, мульти-юрисдикция, API marketplace

---

## Рыночные данные

| Метрика | Значение |
|---------|---------|
| Forex CRM рынок | $516-530M (2025) → $909-950M (2033) |
| Активных forex-брокеров | 6,500+ глобально |
| Используют CRM | ~72% |
| Retail forex трейдеров | ~9.6 млн |
| MT5 обогнал MT4 | 2025 |
| Mobile-first трейдеров | 55% |
| B2Core клиентов | 100+ брокеров, 1.5M MAU |

## Внутренний контекст (из Confluence/Jira)

- **CRM-42322** — Рефакторинг Forex MyAccount Stage 1 (In Development)
- Существующий репо: `myaccount-front-react-forex` (миграция JS → TS)
- 4 торговые платформы: MT4, MT5, DTS v2, cTrader
- 50+ страниц Confluence, 100+ Jira-тикетов по форексу
- Forex — основной тип инстанса в xCritical CRM
- 15+ production-окружений для разных брокеров

## Проверка качества

Все файлы прошли автоматическую проверку на корректность бизнес-модели (5 ревью-агентов). Ошибок не обнаружено.
