# Forex Deep Dive: Go-To-Market + Метрики (AJTBD-линза)

**Часть 8 из 8** | [Обзор](./forex-deep-dive-overview.md) | [Jobs Graph](./forex-deep-dive-jobs-graph.md) | [Стратегические рекомендации](./forex-strategic-recommendations.md)

**Продукт**: myaccount-front -- B2B white-label trader room
**Методология**: AJTBD-driven GTM (Go-To-Market через призму Jobs To Be Done)
**Дата**: 2026-02-08

> Этот документ дополняет [стратегические рекомендации](./forex-strategic-recommendations.md), фокусируясь на **AJTBD-специфичных** GTM-инсайтах: какие Jobs продавать первыми, какие метрики отслеживать, как определить product-market fit.

---

## 1. AJTBD-Driven GTM Strategy

### 1.1 Какие Jobs вести в маркетинг первыми

Ранжирование B2B Jobs по Opportunity Score (Importance x (10 - Satisfaction)):

| Ранг | B2B Small Job | Importance | Satisfaction | Opportunity Score | GTM-приоритет |
|------|--------------|------------|-------------|-------------------|---------------|
| **1** | B-SJ7: Удерживать клиентов | 10 | 3 | **70** | Лидирующий message |
| **2** | B-SJ5: Анализировать клиентскую активность | 9 | 2 | **72** | Лидирующий message |
| **3** | B-SJ6: Снизить расходы на поддержку | 9 | 4 | **54** | Сильный аргумент |
| **4** | B-SJ1: Развернуть брендированный портал | 10 | 5 | **50** | Table stakes |
| **5** | B-SJ3: Интегрировать платежные системы | 10 | 6 | **40** | Table stakes |
| **6** | B-SJ4: Обеспечить KYC/Compliance | 10 | 5 | **50** | Compliance selling |

**Вывод**: Маркетинг должен лидировать с **retention** и **аналитикой** -- именно здесь брокеры наименее удовлетворены. White-labeling и платежи -- необходимые, но не дифференцирующие.

### 1.2 Sales Messaging по топ-3 недообслуженным исходам

| # | Недообслуженный Outcome (B2B) | Sales Message | Доказательство |
|---|-------------------------------|---------------|----------------|
| 1 | **"Минимизировать отток трейдеров при сохранении активной торговли"** | "Ваш текущий trader room теряет 25% трейдеров в первые 3 месяца. Наш -- удерживает через персонализацию, push-уведомления и встроенную аналитику. Результат: +32% retention у существующих клиентов." | B-SJ7, in-app messaging = 61-74% retention |
| 2 | **"Видеть полную воронку конверсии от регистрации до первого депозита"** | "Вы не знаете, где теряете трейдеров. myaccount-front показывает: 100 зарегистрировались --> 65 прошли KYC --> 38 сделали FTD. Оптимизируем каждый шаг." | B-SJ5, типичная FTD-конверсия 14% --> целевая 29% |
| 3 | **"Снизить количество обращений в поддержку без ухудшения качества сервиса"** | "Withdrawal status tracker + smart KYC = минус 40% тикетов. При 800 тикетах/мес это $3,200/мес экономии -- больше, чем стоимость нашего решения." | B-SJ6, -40-60% KYC workload, -50-60% withdrawal tickets |

### 1.3 Конкурентное позиционирование через Jobs, которые конкуренты НЕ решают

| Job, которую конкуренты игнорируют | Наш ответ | Конкурентный контекст |
|--------------------------------------|-----------|----------------------|
| **B2C-аналитика внутри trader room** | Performance dashboard: equity curve, win rate, drawdown | B2Core, Leverate, Match-Trader -- показывают только историю сделок. Трейдеры уходят на MyFXBook (7.2M визитов/мес) |
| **Prop Trading challenge flows** | Встроенный prop-модуль: challenges, evaluation, funding | Кризис MetaQuotes: 80-100 фирм без технологии. Match-Trader захватывает, но CRM-решения не покрывают |
| **Data-driven UX-оптимизация** | A/B testing framework для брокеров | Ни один конкурент не дает брокеру инструменты для тестирования вариантов UI |
| **Mobile-first как архитектура, не адаптация** | Next.js SSR + responsive-first, bottom nav, 44px targets | Конкуренты делают "responsive" как desktop-first + media queries |

### 1.4 Entry Strategy: какой сегмент таргетировать первым

**Приоритет #1: Существующие клиенты xCritical CRM (15+ брокеров)**

AJTBD-обоснование:
- **Нулевой switching cost** -- тот же бэкенд, только frontend upgrade (Job B-SJ1 решается за часы, не недели)
- **Highest urgency** -- их Core Job триггер уже активен: "текущий trader room устарел, клиенты жалуются" (CRM-42322 = формальное подтверждение)
- **Минимальный CAC** -- не нужно доказывать xCritical CRM value, только frontend value
- **Максимальный feedback loop** -- они уже знают слабые места текущего MyAccount, дадут точные requirements

**Приоритет #2: Новые брокеры-стартапы на xCritical CRM**

AJTBD-обоснование:
- **Greenfield = нет антипаттернов привычки** (Forces: Habit of Present = 0)
- Их Core Job: "запуститься быстро и выглядеть профессионально" -- modern trader room решает оба Outcome
- Time-to-market < 2 недель = конкурентное преимущество vs Quadcode (6 мес)

---

## 2. Channel Strategy (AJTBD-линза)

### 2.1 Где брокеры ОТКРЫВАЮТ новые решения (контекст Job)

| Контекст обнаружения | Trigger Job | Канал | Эффективность |
|----------------------|-------------|-------|---------------|
| "Мой trader room выглядит как из 2015 года" | B-SJ1: Развернуть брендированный портал | **iFX EXPO, Finance Magnates** -- брокер видит демо конкурента и осознает отставание | Высокая |
| "Конкуренты переманивают трейдеров UX-ом" | B-SJ7: Удерживать клиентов | **Linkedin outbound** -- direct message с данными: "ваш bounce rate 55%, у нас -- 35%" | Высокая |
| "Стоимость поддержки растет" | B-SJ6: Снизить поддержку | **Content marketing** -- статья "How to Cut Support Costs 40% with Self-Service Trader Room" | Средняя |
| "Нужен prop trading module" | B-SJ специфическая | **Prop trading community** (ForexFactory, prop-фирмы Telegram) -- прямое решение кризиса MetaQuotes | Высокая |
| "Ищу CRM для нового брокера" | Core Job B2B | **SEO** -- "best forex CRM 2026", "white-label trader room" -- сравнительные статьи | Средняя |

### 2.2 Что триггерит ПОИСК нового решения (Push Forces)

| Push Force | Описание | Сила (1-10) | Как использовать в маркетинге |
|-----------|----------|-------------|-------------------------------|
| **Потеря трейдеров из-за UX** | Конкуренты с modern UI переманивают клиентов | 9 | Case study: "Брокер X потерял 15% трейдеров за квартал -- старый UI" |
| **Рост support costs** | 800+ тикетов/мес, $8K+ на support | 8 | ROI-калькулятор: "Посчитайте экономию от self-service" |
| **MetaQuotes кризис** | Отзыв лицензий, нужна альтернативная инфраструктура | 8 | Прямой outreach к displaced проп-фирмам |
| **Регуляторные требования** | Новые KYC/AML-нормы требуют обновления портала | 7 | Compliance checklist: "Готов ли ваш trader room к MiFID III?" |
| **Устаревший стек** | jQuery/Angular trader room -- нельзя нанять разработчиков | 6 | Technical whitepaper: "Why Modern Stack Matters for Forex" |

### 2.3 Процесс принятия решения и участники

| Этап | Кто принимает | Их Job | Что им показывать |
|------|--------------|--------|-------------------|
| **Осознание проблемы** | CEO / Founder | "Увеличить прибыль" | ROI-модель: +$24K/мес для среднего брокера |
| **Исследование** | CTO / Tech Lead | "Найти надежное решение" | Архитектурная документация, API docs, стек |
| **Shortlist** | Head of Operations | "Снизить операционные затраты" | Demo с withdrawal tracker, KYC-автоматизацией |
| **POC / Trial** | CTO + Dev Team | "Убедиться в совместимости" | Sandbox environment, integration guide |
| **Финальное решение** | CEO + CFO | "Оправдать инвестицию" | Business case: ROI 7:1, payback < 1 месяц |
| **Внедрение** | IT Team + CSM | "Запустить без простоя" | Migration toolkit, zero-downtime план |

### 2.4 Лучшие каналы для каждого лица, принимающего решение

| Persona | Канал #1 | Канал #2 | Формат контента |
|---------|----------|----------|-----------------|
| **CEO / Founder** | Industry events (iFX EXPO) | LinkedIn (thought leadership) | ROI one-pager, case study с цифрами |
| **CTO** | GitHub / Dev communities | Technical webinars | Architecture doc, OpenAPI spec, benchmark |
| **Head of Ops** | Email outreach | Forex podcasts | "Support Cost Calculator", demo-видео |
| **Compliance Officer** | Regulatory newsletters | LinkedIn | Compliance template library, audit checklist |

---

## 3. Ценообразование через AJTBD

### 3.1 Какие Jobs оправдывают premium pricing

| Job | Willingness to Pay | Обоснование |
|-----|-------------------|-------------|
| **B-SJ7: Удержание клиентов** | Высокая ($3-5K/мес) | Удержание 1 трейдера = $200/мес avg revenue. Удержание 35 дополнительных = $7,000/мес. Окупаемость мгновенная |
| **B-SJ4: KYC/Compliance** | Высокая ($1.5K+/мес модуль) | Штраф за non-compliance = $100K-$1M. Автоматизация KYC = -40% workload compliance team |
| **B-SJ5: Аналитика** | Средняя ($500-1K/мес модуль) | Данные есть, но insight'ов нет. Брокер не знает, почему 60% трейдеров уходят после 2 месяцев |
| **Prop Trading module** | Высокая ($1-2K/мес) | Рынок prop растет на 1,264%, FTMO = $329M revenue. Проп-фирмы готовы платить за скорость запуска |
| **B-SJ1: White-label** | Низкая (table stakes) | Ожидается по умолчанию. Не дифференциатор -- не может быть premium |

### 3.2 Alignment метрики ценности

| Модель | Метрика ценности | Pros | Cons | AJTBD-соответствие |
|--------|-----------------|------|------|-------------------|
| **Per-broker tier (рекомендуемая)** | Количество активных трейдеров | Простая, предсказуемая, масштабируется с ростом брокера | Не привязана к конкретному value | Соответствует B-SJ7: чем больше retained traders, тем выше тир |
| Per-FTD revenue share | Количество первых депозитов | Прямая привязка к Core Job outcome | Сложно отследить, брокер не хочет делиться данными | Идеально для B-SJ5, но friction высокий |
| Per-module | Используемые модули | Гибкость, платишь за нужное | Тормозит adoption новых модулей | Соответствует модульной Job-структуре |
| Flat fee | N/A | Простота | Не масштабируется | Не соответствует AJTBD |

**Рекомендация**: Tiered per-broker + модульные надбавки. Тир определяется количеством активных трейдеров -- это прямая прокси-метрика для решения Core Job брокера ("увеличить удержание и депозиты").

### 3.3 WTP по сегментам (корреляция с Job Importance)

| Сегмент брокера | Core Job urgency | WTP/мес | Обоснование |
|----------------|-----------------|---------|-------------|
| Existing xCritical (upgrade) | Высокая -- уже страдают от legacy UI | $1,500-3,500 | Знают проблему, видят ценность; но привыкли к "бесплатно" |
| Новые брокеры-стартапы | Критическая -- без trader room нет бизнеса | $1,500-3,500 | Time-to-market = главный фактор; чувствительны к цене |
| Проп-фирмы без технологии | Экстремальная -- MetaQuotes отзывает лицензии | $2,500-5,000 | Платят за скорость решения проблемы, не за фичи |
| Mid-tier брокеры (новые) | Средняя -- ищут upgrade существующего решения | $3,500-6,500 | Выбирают по ROI; switching costs = основной барьер |
| Enterprise брокеры | Низкая urgency -- имеют работающие решения | $12,000+ | Длинный цикл продажи; покупают надежность, не инновации |

---

## 4. North Star Metric

### 4.1 Определение

> **North Star Metric: "Количество трейдеров, успешно совершивших транзакцию (депозит или вывод) через myaccount-front за последние 30 дней"**

### 4.2 Почему именно эта метрика

Связь с Core Job обоих слоев:

- **B2B Core Job** ("современный trader room, который увеличивает удержание и объем депозитов") -- каждая успешная транзакция = брокер зарабатывает (спред, комиссия, объем торговли)
- **B2C Core Job** ("гладкий кабинет для управления торговой деятельностью") -- если трейдер совершает транзакцию, значит UX не блокирует его основную задачу
- **Aggregatable**: суммируется через всех брокеров = здоровье платформы
- **Leading indicator**: сегодняшние транзакции --> завтрашний trading volume --> послезавтрашний retention
- **Actionable**: разлагается на воронку (registration --> KYC --> first deposit --> repeat deposit --> withdrawal)

**Почему не FTD (First Time Deposit)?**
FTD -- отличная метрика, но она ловит только вход в воронку. Транзакционная активность ловит и вход (FTD), и здоровье (repeat deposits), и доверие (withdrawals). Это более полная прокси для Core Job.

### 4.3 Как измерять

```
Monthly Active Transacting Traders (MATT) =
  COUNT(DISTINCT trader_id WHERE
    (deposit_completed OR withdrawal_completed)
    AND date >= NOW() - 30 days
  )
  ACROSS all broker instances
```

### 4.4 Целевые значения

| Период | Target MATT | Расчет | Предпосылки |
|--------|-------------|--------|-------------|
| **Месяц 3** | 750 | 15 брокеров x ~50 transacting traders | Existing base мигрирована; 60% трейдеров совершают транзакцию/мес |
| **Месяц 6** | 2,500 | 25 брокеров x ~100 transacting traders | +10 новых брокеров; FTD-конверсия выросла на 15% |
| **Месяц 12** | 8,000 | 50 брокеров x ~160 transacting traders | Phase 2 модули повышают engagement; prop-фирмы добавляют объем |
| **Месяц 18** | 20,000 | 100 брокеров x ~200 transacting traders | Enterprise-клиенты с крупными базами |

---

## 5. B2B KPIs (Broker-level)

| # | Метрика | Определение | Target (Year 1) | Метод измерения | Connected Job |
|---|---------|-------------|-----------------|-----------------|---------------|
| 1 | **Active Broker Count** | Брокеры с >= 10 transacting traders/мес | 25 | CRM dashboard | Core Job: количество брокеров, для которых продукт решает задачу |
| 2 | **MRR (Monthly Recurring Revenue)** | Сумма всех подписок | $100K | Billing system | B-SJ1: масштаб adoption |
| 3 | **Net Revenue Retention (NRR)** | (MRR start + expansion - contraction - churn) / MRR start | 110% | Billing + cohort | B-SJ7: удержание + upsell = рост внутри базы |
| 4 | **Gross Broker Churn** | % потерянного MRR / Total MRR в месяц | < 2% | Billing | Inverse B-SJ7: если Jobs не решаются, брокер уходит |
| 5 | **Time-to-Go-Live** | Дни от подписания контракта до первого live трейдера | < 14 дней | Onboarding tracker | B-SJ1: скорость развертывания = ключевой Outcome |
| 6 | **Broker NPS** | Net Promoter Score от B2B-клиентов | 40+ | Квартальный опрос | Overall satisfaction: решает ли продукт Core Job |
| 7 | **Module Attach Rate** | Среднее количество платных модулей на брокера | 1.5 | Billing | B-SJ2, B-SJ3: глубина настройки = глубина решения Jobs |
| 8 | **Support Escalation Rate** | % проблем трейдеров, эскалированных брокером к нам | < 5% | Ticket system | B-SJ6: если self-service работает, эскалаций нет |
| 9 | **Avg FTD Rate across brokers** | Средний % FTD от регистраций по всем брокерам | 25%+ | Analytics aggregate | B-SJ5: если аналитика показывает рост -- Job решается |
| 10 | **Deal Pipeline Velocity** | Средние дни от first touch до closed deal | < 45 дней (SMB) | CRM | GTM efficiency: как быстро Job resonance конвертируется в revenue |

---

## 6. B2C KPIs (Trader-level -- Leading Indicators)

Эти метрики -- **ранние сигналы** B2B-ценности. Если B2C-метрики растут, B2B-результаты последуют через 1-3 месяца.

| # | Метрика | Определение | Target | Метод измерения | Connected Job |
|---|---------|-------------|--------|-----------------|---------------|
| 1 | **Trader NPS** | Net Promoter Score трейдеров (in-app опрос) | 50+ | Микро-опрос после 5-й сессии | Core Job B2C: "гладкий кабинет" = NPS > 50 |
| 2 | **FTD Conversion Rate** | % от регистрации до первого депозита | 29% | Funnel analytics | T-SJ1 + T-SJ2: онбординг + депозит -- первые 2 критических Jobs |
| 3 | **Withdrawal Satisfaction** | % трейдеров, оценивших withdrawal >= 4/5 | 80%+ | Post-withdrawal micro-survey | T-SJ4: вывод = Job с максимальной эмоциональной нагрузкой |
| 4 | **Feature Adoption Breadth** | % трейдеров, использующих >= 3 фичи | 55% | Event tracking | Multi-Job engagement: чем больше Jobs решается, тем выше stickiness |
| 5 | **Mobile Transaction Rate** | % транзакций с мобильных устройств | 55%+ | Device analytics | RAT C2: mobile-first experience = отсутствие барьера |
| 6 | **Support Ticket Volume** | Тикетов на 100 active traders / мес | < 15 | Ticket system per broker | T-SJ7: если self-service работает, тикетов мало |
| 7 | **Onboarding Completion Rate** | % завершивших полный KYC от начавших | 65%+ | Funnel per step | T-SJ1: каждый шаг = Micro Job; drop-off = failed Micro Job |
| 8 | **Repeat Deposit Rate** | % трейдеров, сделавших 2+ депозитов за 30 дней | 40%+ | Transaction analytics | T-SJ2: повторный депозит = подтверждение решения Job |
| 9 | **DAU/MAU Ratio** | Daily Active Users / Monthly Active Users | 25%+ | Session analytics | Общий engagement: трейдер возвращается, потому что Jobs решаются |
| 10 | **Time-to-First-Deposit** | Медиана времени от регистрации до FTD | < 4 часа | Timestamp analysis | T-SJ1 --> T-SJ2: скорость прохождения Critical Job Sequence |

---

## 7. Product-Market Fit Indicators

### 7.1 Sean Ellis Test (адаптация для B2B)

**Вопрос для брокеров**: "Как бы вы себя чувствовали, если бы myaccount-front перестал быть доступен?"

| Ответ | Pre-PMF | Approaching PMF | PMF Achieved |
|-------|---------|----------------|-------------|
| "Очень разочарован" | < 25% | 25-39% | **>= 40%** |
| "Немного разочарован" | любой | любой | любой |
| "Безразлично" | > 50% | 30-50% | < 20% |
| "Использовал бы альтернативу" | > 25% | 15-25% | < 15% |

**Адаптация для двухслойного продукта**: проводить тест **дважды**:
1. Опрос брокеров (B2B) -- "Что если myaccount-front исчезнет?"
2. Опрос трейдеров (B2C) через брокера -- "Что если ваш личный кабинет вернется к старой версии?"

PMF подтвержден, когда >= 40% в **обоих** слоях.

### 7.2 Cohort Analysis Framework

| Когорта | Что отслеживать | PMF-сигнал |
|---------|----------------|-----------|
| **Broker cohort (month of signup)** | Retention через 3/6/12 мес, MRR expansion | 85%+ retention M3, NRR > 100% |
| **Broker by segment** | SMB vs Mid-tier vs Enterprise retention | Хотя бы 1 сегмент с 90%+ retention |
| **Trader cohort (per broker)** | FTD rate, 30-day retention, repeat deposits | Устойчивое улучшение vs старый MyAccount |
| **Feature cohort** | Adoption кривая новых модулей | > 30% adoption за 60 дней от launch |

### 7.3 PMF Milestones

#### Pre-PMF (Месяцы 1-3)

| Индикатор | Значение | Статус |
|-----------|---------|--------|
| Existing xCritical клиенты мигрированы | 10+ из 15 | Must-hit |
| A/B тест: новый vs старый MyAccount | Новый >= старому по FTD | Must-hit |
| Broker NPS | > 20 | On-track |
| Sean Ellis B2B | > 15% "очень разочарован" | Early signal |
| Трейдеры не просят вернуть старый UI | < 5% rollback requests | Hygiene |

#### Approaching PMF (Месяцы 3-6)

| Индикатор | Значение | Статус |
|-----------|---------|--------|
| Новые брокеры подписали контракт | 5+ (не xCritical-existing) | Growth signal |
| Sean Ellis B2B | 25-39% "очень разочарован" | Strong signal |
| FTD rate improvement | Measurable +10-15% vs baseline | Value confirmation |
| Inbound leads from referrals | > 20% от total pipeline | Organic pull |
| Module attach rate | > 1.2 | Expansion signal |

#### PMF Achieved (Месяцы 6-12)

| Индикатор | Значение | Статус |
|-----------|---------|--------|
| Sean Ellis B2B | >= 40% "очень разочарован" | **PMF confirmed** |
| Sean Ellis B2C | >= 40% "очень разочарован" (vs rollback) | **Dual-layer PMF** |
| Net Revenue Retention | > 120% | Expansion-driven growth |
| Broker churn | < 2%/мес | Stickiness confirmed |
| Referral-driven new clients | > 30% | Product sells itself |
| Competitive displacement | 2+ clients switched from B2Core/Leverate | Competitive edge |

### 7.4 Kill Criteria (когда пивотить)

| Сигнал | Threshold | Дедлайн | Действие |
|--------|-----------|---------|----------|
| Existing xCritical клиенты отказываются мигрировать | < 50% adoption через 3 мес | Месяц 3 | Пересмотреть value prop; возможно, feature parity недостаточен |
| FTD rate не улучшается | Дельта < 5% через 3 мес | Месяц 6 | Пересмотреть UX; возможно, проблема не в UI, а в backend/PSP |
| Zero новых клиентов за пределами xCritical | 0 подписок за 6 мес | Месяц 9 | Продукт не дифференцирован; pivot к component library / SDK |
| Broker churn > 5%/мес | 3+ месяца подряд | Любой | Экзистенциальная проблема; провести exit interviews |
| Sean Ellis стагнирует < 15% после 9 мес | < 15% через 9 мес | Месяц 9 | Jobs не решаются; глубокий customer development |
| xCritical анонсирует конкурирующий frontend | Любой момент | Немедленно | Активировать contingency: API abstraction layer + multi-CRM |

---

## 8. Перекрестные ссылки

### Связь с другими документами AJTBD

| Документ | Что содержит | Как связан с GTM |
|----------|-------------|------------------|
| [forex-strategic-recommendations.md](./forex-strategic-recommendations.md) | Полная стратегия: позиционирование, роудмап, финмодель, ценообразование | GTM-детали (Phase 1-4, sales process, event strategy) -- **не дублируются здесь** |
| [forex-rat-risks.md](./forex-rat-risks.md) | 17 рисков с kill conditions | Метрики из секции 5-6 привязаны к рискам: F1 (switching cost) --> Time-to-Go-Live; F2 (кастомизация) --> Module Attach Rate; F3 (WTP) --> MRR; C1 (UX) --> Trader NPS; C2 (mobile) --> Mobile Transaction Rate |
| [forex-deep-dive-jobs-graph.md](./forex-deep-dive-jobs-graph.md) | B2B + B2C Jobs иерархия | Opportunity Scores из секции 1.1 основаны на Jobs из этого графа |
| [forex-deep-dive-overview.md](./forex-deep-dive-overview.md) | Двухслойная модель продукта | PMF Framework (секция 7) использует двухслойный подход из overview |
| [research-web-market.md](./research-web-market.md) | 47 источников рыночных данных | Все цифры в sales messaging (секция 1.2) подкреплены данными из research |

### Карта метрик --> RAT-риски

| RAT Risk | Метрика-индикатор | Что отслеживать |
|----------|-------------------|-----------------|
| F1: Switching Cost | Time-to-Go-Live, Deal Pipeline Velocity | Если T2GL > 30 дней -- switching cost слишком высок |
| F2: Кастомизация | Module Attach Rate, Support Escalation Rate | Если attach < 1.0 -- конфигурация не покрывает кастомизации |
| F3: WTP | MRR, NRR | Если NRR < 100% -- брокеры не видят ценности для upsell |
| F10: xCritical dependency | Active Broker Count (non-xCritical) | Если 0 не-xCritical клиентов через 12 мес -- зависимость критична |
| C1: UX-ожидания | Trader NPS, DAU/MAU | Если NPS < 30 -- UX не соответствует ожиданиям |
| C2: Mobile | Mobile Transaction Rate | Если < 40% -- mobile experience провален |
| C7: Onboarding friction | Onboarding Completion Rate, Time-to-First-Deposit | Если completion < 50% -- трение убивает конверсию |
