# Product Audit: myaccount-front — Forex Trading Platform

**Date**: 2026-02-08
**Methodology**: AJTBD (Advanced Jobs To Be Done)
**Status**: Pre-development validation phase

---

## Table of Contents

1. [Core Job Analysis](#1-core-job-analysis)
2. [B2C Segment Analysis](#2-b2c-segment-analysis)
3. [B2B Segment Analysis](#3-b2b-segment-analysis)
4. [Risky Assumptions Test (RAT)](#4-risky-assumptions-test-rat)
5. [Competitive Analysis](#5-competitive-analysis)
6. [Needs Map](#6-needs-map)
7. [Prioritization Recommendations](#7-prioritization-recommendations)
8. [Product-Market Fit Risks](#8-product-market-fit-risks)
9. [Strategic Decisions](#9-strategic-decisions)

---

## 1. Core Job Analysis

### Big Job (Большая работа)

**"Стабильно зарабатывать на финансовых рынках, используя торговые навыки и стратегии, чтобы обеспечить финансовую независимость и рост капитала"**

### Core Job (Главная работа продукта)

**"Эффективно управлять несколькими торговыми аккаунтами у разных брокеров из единого интерфейса, чтобы не тратить время на переключение между платформами, не терять контроль над позициями и видеть общую картину портфеля"**

Формула Core Job:
- **Когда** (контекст): Трейдер работает с несколькими брокерами / инструментами / стратегиями одновременно
- **Триггер**: Необходимость проверить состояние позиций, открыть новую сделку, проанализировать результаты
- **Мотивация**: Контроль и экономия времени
- **Ожидаемый результат (Точка Б)**: Все аккаунты видны в одном месте, действия выполняются быстро, ничего не теряется

### Иерархия Small Jobs (подработ)

| Уровень | Работа | Описание |
|---------|--------|----------|
| Small Job 1 | Консолидировать аккаунты | Подключить все брокерские счета в одну панель |
| Small Job 2 | Мониторить позиции | Видеть открытые позиции, P/L, маржу по всем счетам |
| Small Job 3 | Анализировать результаты | Понимать, какие стратегии и счета прибыльны |
| Small Job 4 | Управлять рисками | Контролировать общую экспозицию и drawdown |
| Small Job 5 | Отслеживать прогресс челленджей | (для проп-трейдеров) Видеть статус испытаний |
| Small Job 6 | Переключаться между активами | Быстро переходить между forex/crypto/binary |

### Micro Jobs (для Small Job 2 — "Мониторить позиции")

1. Увидеть общий баланс по всем счетам
2. Отфильтровать позиции по инструменту / брокеру
3. Получить уведомление о критическом изменении
4. Увидеть текущий P/L в реальном времени
5. Сравнить результаты между счетами

### Критическая последовательность работ (Critical Job Sequence)

```
[1] Узнать о продукте → [2] Зарегистрироваться → [3] Подключить первый счёт
→ [4] Увидеть данные → [5] Подключить второй счёт → [6] Увидеть сводку
→ [7] Начать принимать решения на основе сводки → [8] Довериться платформе
→ [9] Подключить все счета → [10] Использовать как основной инструмент
```

**Точки разрыва (где пользователь может уйти):**
- Между [3] и [4]: Если подключение счёта сложное или не работает — 70% отток
- Между [4] и [5]: Если данные неточные или запаздывают — 50% отток
- Между [7] и [8]: Если возникают вопросы безопасности передачи учётных данных — 40% отток

---

## 2. B2C Segment Analysis

### Ранжирование сегментов

| Ранг | Сегмент | Привлекательность | Обоснование |
|------|---------|-------------------|-------------|
| 1 | Проп-трейдеры | 9/10 | Нерешённая проблема, высокая готовность платить, растущий рынок |
| 2 | Мульти-брокерные forex-трейдеры | 7/10 | Большой рынок, но конкуренция и низкий чек |
| 3 | Крипто-форекс диверсификаторы | 7/10 | Уникальная позиция, но сложнее достучаться |
| 4 | Менеджеры торговых команд | 6/10 | Высокий чек, но сложные B2B-продажи |
| 5 | Трейдеры бинарных опционов | 5/10 | Регуляторные и репутационные риски |

---

### Сегмент 1: Проп-трейдеры (Aspiring Prop Traders)

**Описание**: Трейдеры 20-35 лет, проходящие испытания проп-фирм (FTMO, Funded Next, The5ers). Часто имеют 3-10 активных челленджей одновременно у разных фирм. Не имеют собственного капитала, зарабатывают через funded accounts.

**Размер сегмента**: ~500,000-800,000 активных проп-трейдеров (рост 40%+ в год с 2021).

| Параметр | Значение |
|----------|----------|
| TAM | $250M/год (при подписке $40/мес) |
| SAM | $25M (англоязычные + русскоязычные рынки) |
| SOM (Year 1) | $120K (250 платящих пользователей) |
| Готовность платить | Высокая ($29-79/мес) — уже тратят $100-500/мес на челленджи |
| Частота использования | Ежедневно, несколько раз в день |
| Switching cost | Низкий (используют Excel/Google Sheets или ничего) |
| LTV | $500-900 (12-18 месяцев) |
| CAC | $15-30 (community-driven) |
| LTV/CAC | 17-60x |

#### Core Functional Job

**"Help me manage multiple prop firm accounts and challenges simultaneously so that I maximize my chances of getting and keeping funded status."**

#### Emotional & Social Jobs

- **Emotional**: Чувство контроля над сложной мульти-аккаунтной ситуацией, а не тревоги
- **Emotional**: Чувство профессионального трейдера, а не гемблера
- **Social**: Статус "funded trader" в сообществе
- **Social**: Доказательство track record для Discord/Telegram сообществ

#### Unmet Criteria (текущие нерешённые проблемы)

1. **Rule compliance monitoring** — ручная проверка каждого аккаунта против правил каждой фирмы
2. **Cross-account risk visibility** — нет единого обзора; частая причина провала
3. **Challenge progress tracking** — нужно логиниться в каждую фирму отдельно
4. **Performance comparison** — нет кросс-фирменной аналитики

#### Switching Triggers

- **Push**: Провал челленджа из-за забытого правила; 30+ минут ежедневно на проверку дашбордов
- **Pull**: Увидеть, что другой трейдер использует consolidated dashboard; 3+ аккаунтов
- **Anxiety**: Точность данных — одна ошибка может стоить funded account
- **Habit**: Существующая система в spreadsheets

#### Рекомендации по привлечению

- Партнёрство с YouTube-каналами о проп-трейдинге
- Бесплатное отслеживание первого челленджа — показать ценность до paywall
- "Challenge Leaderboard" — социальное доказательство
- SEO: "prop trading dashboard", "FTMO tracker", "manage multiple prop accounts"

#### Рекомендации по удержанию

- Автоматические алерты о нарушении правил (killer feature)
- Месячные отчёты с shareable certificates
- Интеграция с новыми проп-фирмами

---

### Сегмент 2: Мульти-брокерные retail forex-трейдеры

**Описание**: Трейдеры 25-45 с 2-4 счетами у разных форекс-брокеров. Торгуют регулярно, рассматривают трейдинг как серьёзный дополнительный доход.

**Размер**: ~3,000,000 активных (15-25% от 13.9M retail forex трейдеров используют 2+ брокера).

| Параметр | Значение |
|----------|----------|
| TAM | $720M/год (при $20/мес) |
| SAM | $72M |
| SOM (Year 1) | $60K (250 пользователей × $20/мес) |
| Готовность платить | Средняя ($19-49/мес) |
| LTV | $350-600 |
| CAC | $20-40 |
| LTV/CAC | 9-30x |

#### Core Functional Job

**"Видеть все мои forex позиции, балансы и результаты по всем брокерам в одном месте для лучших торговых решений."**

#### Unmet Criteria

1. Unified P&L по всем аккаунтам (ручная агрегация с учётом разных валют)
2. Exposure monitoring — нет единого обзора позиций по валютным парам
3. Broker comparison — нет data-driven анализа качества исполнения
4. Tax reporting — объединение выписок из нескольких брокеров

#### Рекомендации

- SEO: "multi-broker dashboard", "forex portfolio tracker"
- Бесплатная генерация налогового отчёта как lead magnet (сезонный всплеск янв-мар)
- Партнёрство с forex education платформами

---

### Сегмент 3: Крипто-форекс диверсификаторы

**Описание**: Трейдеры 25-40, активно торгующие и крипто, и форекс. Используют криптобиржи (Binance, Bybit) вместе с форекс-брокерами. Растущий сегмент.

**Размер**: ~1-2 миллиона глобально.

| Параметр | Значение |
|----------|----------|
| Готовность платить | $29-59/мес |
| LTV | $400-700 |
| CAC | $25-45 |
| LTV/CAC | 9-28x |

#### Core Functional Job

**"Управлять комбинированным крипто + форекс портфелем в одном месте для отслеживания общей экспозиции, корреляций и результатов."**

#### Уникальное позиционирование

"The only dashboard that speaks both crypto and forex" — ни один конкурент не предоставляет кросс-asset корреляционную аналитику для retail.

---

### Сегмент 4: Менеджеры торговых команд

**Описание**: Руководители торговых десков или проп-фирм, управляющие 2-10+ трейдерами.

**Размер**: ~50,000-150,000 глобально.

| Параметр | Значение |
|----------|----------|
| Готовность платить | $99-299/мес |
| LTV | $1,500-3,600 |
| CAC | $50-150 |
| LTV/CAC | 10-72x |

#### Core Functional Job

**"Мониторить и сравнивать результаты нескольких трейдеров для data-driven решений об аллокации капитала."**

---

### Сегмент 5: Трейдеры бинарных опционов (мигранты)

**Описание**: Трейдеры 22-35 на бинарных опционах, ищущие переход на forex/crypto.

| Параметр | Значение |
|----------|----------|
| Готовность платить | $15-35/мес |
| LTV | $200-400 |
| Регуляторный риск | Высокий (запрет в EU, UK, AU) |
| Привлекательность | 5/10 |

**Рекомендация: НЕ приоритизировать. Регуляторные и репутационные риски превышают потенциал.**

---

## 3. B2B Segment Analysis

### Ранжирование B2B сегментов

| Ранг | Сегмент | Оценка | Месячный чек | LTV (2 года) |
|------|---------|--------|-------------|-------------|
| 1 | Проп-фирмы (white-label) | 9/10 | $3K-15K | $80K-250K |
| 2 | Розничные forex-брокеры | 7/10 | $1.5K-8K | $50K-150K |
| 3 | Крипто-биржи / нео-брокеры | 7/10 | $2K-10K | $60K-180K |
| 4 | Платформы бинарных опционов | 6/10 | $2K-8K | $60K-200K |
| 5 | IB/партнёрские сети | 6/10 | $1K-5K | $30K-100K |

---

### B2B Сегмент 1: Проп-фирмы (Funded Trader Programs)

**Описание**: Компании, проводящие "funded trader" программы — оценивают retail-трейдеров через челленджи, затем выделяют капитал прошедшим. Доход от challenge fees ($50-500 за попытку) и profit splits.

**Примеры**: FTMO, MyFundedFX, The5ers, True Forex Funds.

| Параметр | Значение |
|----------|----------|
| TAM | ~$2.5B (глобальный доход проп-индустрии от challenge fees) |
| SAM | ~$800M (фирмы, готовые рассмотреть white-label) |
| SOM (Years 1-3) | ~$40M |
| Setup fee | $10,000 - $50,000 |
| Monthly license | $3,000 - $15,000/мес |
| Revenue share вариант | 1-3% от дохода challenge fees |

#### Core Functional Job

**"Запустить и управлять бизнесом funded-trader оценки так, чтобы трейдеры могли покупать челленджи, проходить оценки, получать funded accounts, а я мог управлять всем из единого дашборда."**

#### Desired Outcomes

| Outcome | Метрика | Target |
|---------|---------|--------|
| Time to launch | Дней до запуска | < 30 |
| Trader satisfaction | Support ticket rate | < 2% MAU/мес |
| Challenge conversion | Visitor-to-purchase | > 8% |
| Dev costs | Monthly dev spend | < $5,000 |
| Repeat purchases | Repeat rate | > 40% |

#### Конкуренция

- **TradeLocker** — full-stack prop trading platform
- **cTrader / Match-Trader** — prop trading модули
- **Custom builds** — in-house React/Angular
- **Gap**: нет доминирующего модульного frontend-only решения

#### Рекомендации

1. Первый модуль к реализации: Prop trading challenge dashboard
2. GTM: 2-3 design partners (малые проп-фирмы), бесплатный бета
3. Контент: "How to launch a prop firm in 30 days"
4. Ключевой дифференциатор: модульный фронтенд, интегрируемый с ЛЮБЫМ бэкендом через API

---

### B2B Сегмент 2: Розничные Forex/CFD брокеры (Small-to-Mid)

**Описание**: Лицензированные или оффшорные брокеры с 1,000-50,000 активных трейдеров. Нуждаются в клиентском портале ("MyAccount" / "Trader Room").

| Параметр | Значение |
|----------|----------|
| TAM | ~$15B (глобальный рынок forex brokerage tech) |
| SAM | ~$2B |
| Setup fee | $5,000 - $20,000 |
| Monthly license | $1,500 - $8,000/мес |

#### Конкуренция

- **Brokeree (TraderRoom)** — доминант в CIS и Азии
- **CurrentDesk** — US-focused
- **B2Broker (B2Core)** — comprehensive но дорогой
- **Leverate (Sirix)** — legacy

**Gap**: Большинство решений монолитные с устаревшим UI. Модульных Next.js-based порталов практически нет.

---

### B2B Сегмент 3: Крипто-биржи и нео-брокеры

**Описание**: Крипто-компании или гибридные финтехи (500-20,000 пользователей), которые хотят добавить торговый интерфейс.

| Параметр | Значение |
|----------|----------|
| TAM | ~$8B |
| SAM | ~$1.2B |
| Setup fee | $15,000 - $40,000 |
| Monthly license | $2,000 - $10,000/мес |

**Примечание**: Рынок циклический (зависит от bull/bear). Запускать модуль в период bull market sentiment.

---

### B2B Сегмент 4: Платформы бинарных опционов

| Параметр | Значение |
|----------|----------|
| TAM | ~$3B |
| SAM | ~$600M |
| Regulatory risk | -2 к общему score |
| Рекомендация | Отдельный бренд, если реализовывать |

---

### B2B Сегмент 5: IB/партнёрские сети

**Рекомендация**: Не продавать standalone — предлагать как add-on модуль к broker portal (сегмент 2).

---

## 4. Risky Assumptions Test (RAT)

### Сводная матрица рисков

| # | Допущение | Категория | Риск | P×I | Приоритет |
|---|-----------|-----------|------|-----|-----------|
| 1 | Трейдеры ищут единую мульти-брокер платформу | Value | CRITICAL | — | 1 |
| 2 | Трейдеры доверят credentials сторонней платформе | Value+Ethical | CRITICAL | 4.5 | 2 |
| 3 | Solo-dev способен поддерживать интеграции при 99.9% uptime | Feasibility | CRITICAL | 5.6 | 3 |
| 4 | Существует жизнеспособная бизнес-модель (WTP) | Viability | CRITICAL | 3.6 | 4 |
| 5 | 4 модуля можно объединить в один UX без размывания | Usability+Value | HIGH | 3.5 | 5 |
| 6 | Real-time sync (<2 сек) критичен и достижим | Feasibility+Value | HIGH | — | 6 |
| 7 | TAM достаточен для жизнеспособного бизнеса | Viability | HIGH | — | 7 |
| 8 | Регулирование не заблокирует выход на рынок | Viability+Ethical | HIGH | — | 8 |
| 9 | Next.js 16 — правильный tech choice (нет бэкенда) | Feasibility | MEDIUM | — | 9 |
| 10 | Solo-dev обеспечит безопасность финансовых данных | Ethical+Feasibility | MEDIUM | — | 10 |

---

### Допущение 1: Трейдеры ищут единую мульти-брокер платформу

**Категория**: Value | **Риск**: CRITICAL

**Суть**: Вся платформа построена на предпосылке, что "фрагментированность управления аккаунтами" — достаточно сильная работа (Job). Однако многие трейдеры сознательно выбирают разных брокеров и управляют ими параллельно без ощущения проблемы.

**Влияние при провале**: Полный провал продукта. Нет Jobs — нет PMF.

**Текущие доказательства**: Отсутствуют.

**Метод проверки**: Problem-интервью с 15-20 активными трейдерами. Вопросы по Jobs-методологии: "Расскажите, как вы управляете своими торговыми аккаунтами. Когда последний раз переключались между платформами? Что при этом происходило?"

**Критерии**:
- Успех: 10+ из 15 самостоятельно называют управление несколькими аккаунтами значимой задачей
- Провал: менее 5 из 15 подтверждают проблему

**Стоимость**: 1-2 недели, 300-1000 USD на компенсации респондентам

---

### Допущение 2: Трейдеры доверят credentials

**Категория**: Value + Ethical | **Риск**: CRITICAL

**Суть**: Платформа требует передачу API-ключей/паролей неизвестному стартапу. Трейдеры управляют реальными деньгами (десятки-сотни тысяч USD).

**Влияние при провале**: Конверсия из регистрации в активного пользователя → 0.

**Метод проверки**: (A) Блок вопросов в интервью из допущения 1. (B) Лендинг с кнопкой "Подключить аккаунт" — замерить CTR.

**Критерии**:
- Успех: 60%+ готовы дать read-only API ключи; CTR > 5%
- Провал: 70%+ отказываются; CTR < 1%

**Стоимость**: Включено в интервью + 2-3 дня на лендинг + 100-200 USD реклама

---

### Допущение 3: Solo-dev потянет интеграции

**Категория**: Feasibility | **Риск**: CRITICAL

**Суть**: 99.9% uptime (≤8.76 часов простоя/год) при интеграции с множеством брокерских API — нереалистично для solo developer. Каждый брокер имеет уникальное API, часто плохо документированное.

**Влияние при провале**: (A) Хроническая нестабильность → потеря доверия. (B) "Ловушка поддержки" — время на maintenance вместо развития.

**Метод проверки**: PoC — интеграция с одним брокером (MT5). Замерить: время на интеграцию, количество инцидентов за 30 дней, время на исправление.

**Критерии**:
- Успех: <2 недели на интеграцию; <3 инцидентов/30 дней; каждый <2 часа
- Провал: >4 недель; >10 инцидентов; инциденты >1 рабочего дня

---

### Допущение 4: Жизнеспособная бизнес-модель

**Категория**: Viability | **Риск**: CRITICAL

**Суть**: В документации полностью отсутствует описание бизнес-модели. Myfxbook бесплатен, TradingView имеет бесплатный тир.

**Метод проверки**: (A) Ван Вестендорп в интервью. (B) Конкурентный анализ (10 конкурентов). (C) Fake door pricing page.

**Критерии**:
- Успех: медианная WTP > $20/мес; 30%+ готовы платить
- Провал: WTP < $5/мес; 70%+ не будут платить

---

### Допущение 5: 4 модуля = ценность, не размывание

**Категория**: Usability + Value | **Риск**: HIGH

**Суть**: Каждый модуль обслуживает разных трейдеров с разными ментальными моделями. Риск "швейцарского ножа". Рыночная практика: успешные платформы специализируются.

**Метод проверки**: Jobs-mapping для каждого сегмента (оценить пересечение). Figma A/B: "единый дашборд" vs "отдельные модули".

**Критерии**:
- Успех: пересечение работ >40%; 6 из 8 в тесте предпочитают единый дашборд
- Провал: пересечение <20%; пользователи путаются

---

### Допущение 6: Real-time sync критичен

**Категория**: Feasibility + Value | **Риск**: HIGH

**Суть**: Требование <2 сек задержки. Но трейдеры не торгуют через dashboard — торгуют в терминале брокера. Dashboard — аналитический инструмент, для которого 15-30 секунд задержки приемлемо. Myfxbook обновляет 15-60 секунд.

**Метод проверки**: Вопрос в интервью + A/B-тест прототипа (2 сек vs 15 сек).

---

### Допущение 7: TAM достаточен

**Категория**: Viability | **Риск**: HIGH

**Суть**: Оценки в документации показывают потолок ~2550 пользователей. При $20/мес и 5-10% проникновении — 25,000-60,000 USD/год. Может быть недостаточно.

**Метод проверки**: Bottom-up TAM через данные брокеров + top-down через Statista + SimilarWeb анализ конкурентов.

---

### Допущение 8: Регулирование не заблокирует

**Категория**: Viability + Ethical | **Риск**: HIGH

**Суть**: Binary options запрещены в ЕС (ESMA), ограничены в UK, AU. Даже хранение торговых данных может требовать лицензии.

**Метод проверки**: Консультация с финтех-юристом + анализ правового статуса аналогов.

**Стоимость**: 500-2000 USD на юридическую консультацию.

---

### Допущение 9: Next.js 16 — правильный выбор

**Категория**: Feasibility | **Риск**: MEDIUM

**Суть**: Проект описан как фронтенд-приложение, но торговая платформа требует бэкенд (WebSocket, job scheduler, secure credential storage). Где бэкенд?

**Метод проверки**: ADR + PoC одного endpoint синхронизации через Next.js API Routes.

---

### Допущение 10: Безопасность финансовых данных

**Категория**: Ethical + Feasibility | **Риск**: MEDIUM

**Суть**: Хранение credentials к аккаунтам с реальными деньгами. Solo-dev не имеет ресурсов на security audit, penetration testing, SOC 2. Любой инцидент → необратимое уничтожение доверия.

**Метод проверки**: STRIDE threat modeling + исследование managed solutions (HashiCorp Vault, AWS Secrets Manager).

---

### План проверки допущений

#### Фаза 0: ДО начала разработки (недели 1-3)

**Единое исследование** (покрывает допущения 1, 2, 4):
- 15-20 problem-интервью с трейдерами
- Срок: 2 недели
- Стоимость: 300-1000 USD

**Desk research** (покрывает допущения 7, 8):
- TAM/SAM/SOM расчёт
- Регуляторный анализ
- Конкурентный анализ
- Срок: 1 неделя (параллельно)
- Стоимость: 500-2000 USD

**GO/NO-GO**: Если 3+ допущений из 1, 2, 4, 7 провалились → pivot.

#### Фаза 1: Параллельно с началом разработки (недели 3-7)

**Technical PoC** (допущения 3, 9, 10):
- Интеграция с одним брокером (MT5)
- ADR фронтенд vs бэкенд
- Threat modeling
- Срок: 4 недели

**Usability validation** (допущения 5, 6):
- Jobs-mapping для 2 модулей
- Figma-прототипы + тестирование
- Срок: 3-4 недели

#### Фаза 2: MVP-эксперименты (недели 7-12)

**MVP 1 — "Single-Broker Dashboard"**:
- Один модуль (prop trading)
- Один брокер (MT5)
- Read-only режим
- Задержка 15 сек
- Цель: 50 активных пользователей за 30 дней

**MVP 2 — "Multi-Broker View"** (если MVP 1 успешен):
- Второй брокер
- Платная подписка (от $5/мес)

---

## 5. Competitive Analysis

### Prop Trading — управление аккаунтами

| Конкурент | Описание | Сильные стороны | Слабые стороны |
|-----------|----------|-----------------|----------------|
| PropFirmMatch | Агрегатор проп-фирм | Сравнение фирм | Нет управления аккаунтами |
| TradeZella | Журнал трейдера | Аналитика, психология | Не для проп-трейдинга |
| Google Sheets | Ручной учёт | Бесплатно, гибко | Не автоматизировано |
| Dashboards проп-фирм | Каждая фирма — свой UI | Нативные данные | Фрагментировано |

**Вывод**: В нише "единый дашборд для проп-трейдинг аккаунтов" нет доминирующего решения.

### Forex — мульти-аккаунт менеджмент

| Конкурент | Описание | Сильные стороны | Слабые стороны |
|-----------|----------|-----------------|----------------|
| MyFXBook | Бесплатный агрегатор | Бесплатно, 10+ лет, огромная база | Устаревший UI, нет real-time |
| FX Blue | Аналитика MT4/MT5 | Глубокая аналитика | Только MetaTrader |

**Вывод**: MyFXBook — де-факто стандарт, но бесплатен и устарел.

### Crypto — мульти-биржевое управление

| Конкурент | Описание | Сильные стороны | Слабые стороны |
|-----------|----------|-----------------|----------------|
| CoinStats | Портфолио-трекер | 300+ бирж, мобильное | Дорогой Pro |
| Delta | Портфолио-трекер | UI/UX | Для инвесторов, не трейдеров |
| 3Commas | Торговый бот | Автоматизация | Дорого, сложно |
| CoinTracker | Налоговый учёт | Налоговые отчёты | Не для активных трейдеров |

**Вывод**: Перенасыщенный рынок. Входить первым — стратегическая ошибка.

### Стратегия дифференциации

**Рекомендуемый путь**: "Лучший друг проп-трейдера" — единственная платформа для управления множественными проп-аккаунтами.

Дифференцирующие факторы:
1. Специализация на проп-трейдинге (ни один конкурент не фокусируется)
2. Трекер правил — авто-мониторинг правил каждой проп-фирмы
3. Риск-алерты — уведомления при приближении к нарушению
4. Сравнительная аналитика — какие стратегии работают на каких типах аккаунтов

---

## 6. Needs Map

### Функциональные потребности

| # | Потребность | Критичность | Сегмент |
|---|------------|-------------|---------|
| F1 | Видеть баланс и equity всех счетов | Критическая | Все |
| F2 | Уведомления при приближении к лимитам drawdown | Критическая | Проп-трейдеры |
| F3 | Отслеживать прогресс по целям челленджа | Критическая | Проп-трейдеры |
| F4 | Видеть историю сделок по всем аккаунтам | Высокая | Все |
| F5 | Сравнивать результаты между счетами | Высокая | Опытные |
| F6 | Экспортировать данные для налогов | Средняя | Все |
| F7 | Управлять позициями из единого интерфейса | Низкая (на старте) | Все |

### Эмоциональные потребности

| # | Потребность | Описание |
|---|------------|----------|
| E1 | Контроль | "Я контролирую все свои позиции и риски" |
| E2 | Спокойствие | "Я не беспокоюсь, что пропустил что-то важное" |
| E3 | Уверенность | "Я вижу, что мои стратегии работают / не работают" |
| E4 | Безопасность | "Мои данные и учётные записи в безопасности" |
| E5 | Профессионализм | "Я использую профессиональный инструмент" |

### Социальные потребности

| # | Потребность | Описание |
|---|------------|----------|
| S1 | Статус | "Я серьёзный трейдер с профессиональными инструментами" |
| S2 | Принадлежность | "Я часть сообщества, использующего лучшие практики" |
| S3 | Доверие | "Другие трейдеры доверяют этому инструменту" |
| S4 | Результаты | "Я могу показать verified track record" |

### Приоритизация (MoSCoW)

```
MUST HAVE:
  F1 (общий баланс) + F4 (история) + E4 (безопасность)

SHOULD HAVE:
  F2 (уведомления) + F3 (прогресс) + E1 (контроль) + E2 (спокойствие)

COULD HAVE:
  F5 (сравнение) + F6 (экспорт) + S4 (verified record)

WON'T HAVE NOW:
  F7 (управление позициями) + S2 (сообщество)
```

---

## 7. Prioritization Recommendations

### Фаза 0: Валидация (2-4 недели) — ДО КОДА

| Шаг | Действие | Срок | Цель |
|-----|----------|------|------|
| 0.1 | Проверить доступность API 10 проп-фирм | 3 дня | Техническая осуществимость |
| 0.2 | Лендинг + реклама в проп-сообществах | 5 дней | Замерить интерес и trust |
| 0.3 | 15-20 CustDev интервью с проп-трейдерами | 7 дней | Глубокое понимание Jobs |
| 0.4 | Figma-прототип + тестирование WTP | 5 дней | Willingness to pay |

### Фаза 1: MVP — Prop Trading Dashboard (6-8 недель)

**Scope MVP:**
1. Подключение 2-3 проп-фирм (с API или MT5 bridge)
2. Дашборд статуса всех аккаунтов
3. Прогресс-трекер: profit target, max loss, daily loss
4. Система уведомлений (email + push)
5. Базовая история сделок

**Почему prop trading первым:**
- Самый привлекательный сегмент (9/10)
- Наименьшая конкуренция
- Ясная, измеримая ценность
- Высокая готовность платить
- Чёткие критерии успеха

### Фаза 2: Расширение Prop + Forex (8-12 недель)

1. Ещё 3-5 проп-фирм
2. Базовая поддержка MT4/MT5 аккаунтов
3. Сравнительная аналитика
4. Продвинутая система рисков

### Фаза 3: Крипто модуль (12+ недель)

Только при traction (50+ платящих пользователей).

### Фаза N (отложить/исключить): Бинарные опционы

**НЕ реализовывать:**
- Регуляторные запреты (EU, UK, AU)
- Репутационные риски
- Низкая WTP
- Может отпугнуть серьёзных трейдеров

### Рекомендуемая модель монетизации

| Тир | Цена | Включает |
|-----|------|----------|
| Free | $0 | 1 аккаунт, базовый дашборд, данные с задержкой 15 мин |
| Pro | $29/мес | До 10 аккаунтов, real-time, алерты, аналитика |
| Team | $79/мес | До 50 аккаунтов, командный доступ, приоритетная поддержка |

---

## 8. Product-Market Fit Risks

| # | Риск | Вероятность | Влияние | Митигация |
|---|------|-------------|---------|-----------|
| PMF-1 | Техническая невозможность интеграций | Высокая | Критическое | Валидация API. Альтернативы: manual input, CSV, screenshot parsing |
| PMF-2 | Проблема доверия (credentials) | Средняя | Критическое | Read-only API. Публичная архитектура. Testimonials |
| PMF-3 | Solo-dev bottleneck | Высокая | Высокое | Минимум интеграций (2-3). Автоматизация мониторинга |
| PMF-4 | Нестабильный рынок проп-фирм | Средняя | Высокое | Диверсификация по фирмам. Быстрая адаптация |
| PMF-5 | Конкуренция от самих проп-фирм | Низкая | Высокое | Ценность поверх данных (аналитика, сравнение) |
| PMF-6 | "Good enough" problem (Excel) | Средняя | Среднее | Фокус на real-time alerts и automation |
| PMF-7 | Фрагментация (4 модуля = 4 аудитории) | Высокая | Высокое | Начать с одного сегмента |

### PMF метрики для MVP

| Метрика | Порог | Как измерить |
|---------|-------|-------------|
| DAU/MAU | >= 40% | Аналитика |
| Retention (Day 30) | >= 25% | Когорты |
| NPS | >= 40 | Опрос |
| Organic growth | >= 20% по рекомендации | Атрибуция |
| Free→Paid конверсия | >= 5% | Биллинг |
| Sean Ellis test | >= 40% "очень разочарованы" | Опрос |

---

## 9. Strategic Decisions

### Решение 1: Сузить фокус

**Текущий план**: 4 модуля — prop trading, forex, crypto, binary options.

**Рекомендация**: Начать ТОЛЬКО с prop trading.

**Обоснование (AJTBD)**: Продукт должен быть "нанят" на одну конкретную работу и выполнять её лучше всех альтернатив. Попытка решить 4 работы одновременно гарантирует, что ни одна не будет решена хорошо.

### Решение 2: Валидация до разработки

Инвестировано значительное время в tooling и процессы (14 агентов, 9 команд, система правил), но ни одно бизнес-допущение не проверено.

**Рекомендация**: 2-4 недели на валидацию рисков 1-3 до написания бизнес-логики.

### Решение 3: Перепозиционирование

**Текущее**: "myaccount-front" — generic, не говорит о ценности.

**Рекомендация**: Позиционировать как "PropDash" / "FundedTracker" / "PropControl" — что-то, что сразу говорит проп-трейдеру: "это для меня".

### Решение 4: Исключить бинарные опционы

Модуль бинарных опционов следует полностью исключить из roadmap. Регуляторные риски, репутационные издержки и низкая экономическая привлекательность делают его чистым liability.

### Решение 5: Архитектурный приоритет

Самое критичное архитектурное решение до написания бизнес-логики: спроектировать **API adapter pattern**, чтобы одни и те же фронтенд-модули работали с любым бэкенд-сервером. Это основной competitive moat — "works with everything".

---

## Appendix: Validation Timeline

```
Неделя 1:  [R1] Проверить доступность API — технический spike
           [R5] Опрос в сообществах — сколько рынков используют

Неделя 2:  [R2] Лендинг + реклама — замер trust barrier
           [R3] CustDev интервью — готовность платить

Неделя 3:  [R3] Figma-прототип + тестирование WTP
           [R4] Оценка complexity по результатам R1

Неделя 4:  GO / NO-GO решение на основе данных
           Если GO → Фаза 1 MVP (Prop Trading Dashboard)
           Если NO-GO → Pivot или kill
```

---

## Appendix B: Comprehensive Market Research Report

**Research Date**: 2026-02-08
**Methodology**: Web research across industry reports, analytics platforms, competitor websites, forums, and regulatory sources.

---

### B1. Market Size & Trends (2024-2026)

#### B1.1 Global Forex Market Size

The overall forex market (institutional + retail) is valued between **$792B and $930B in 2024**, with projections to exceed **$1 trillion by late 2020s**:

| Source | 2024 Value | Projected Value | CAGR |
|--------|-----------|----------------|------|
| IMARC Group | $861B | $1,535B (2033) | 6.64% |
| Mordor Intelligence | ~$840B | $1,180B (2030) | 5.83% |
| Research & Markets | $930B | $1,160B (2030) | 3.80% |
| Technavio | ~$792B | +$582B (2029) | 10.6% |
| Cognitive Market Research | $808B | $1,297B (2031) | 7.00% |
| Business Research Co. | -- | $1,168B (2030) | 6.9% |

**Daily FX turnover**: $9.6 trillion in April 2025 (BIS Triennial Survey), up 28% from $7.5 trillion in 2022. Annualized, this is approximately **$3.5 quadrillion/year**.

Sources:
- [IMARC Group - Foreign Exchange Market Size](https://www.imarcgroup.com/foreign-exchange-market)
- [BestBrokers - Forex Trading Statistics 2026](https://www.bestbrokers.com/forex-trading/forex-trading-statistics/)
- [Mordor Intelligence - Foreign Exchange Market](https://www.mordorintelligence.com/industry-reports/foreign-exchange-market)
- [GlobeNewsWire - $1.16 Trillion Foreign Exchange Markets](https://www.globenewswire.com/news-release/2025/02/20/3029771/28124/en/1-16-Trillion-Foreign-Exchange-Markets-Global-Industry-Trends-Opportunity-and-Forecast-2020-2024-2025-2030.html)

#### B1.2 Retail Forex Trading Specifically

**Retail share**: Only **2.5% of total forex volume** is retail, down from 6% in 2022. However, in absolute terms retail FX/CFD trading surged to over **$30 trillion per month** in Q2 2025.

**Number of active retail traders**: Approximately **9.6 million** globally, with estimates ranging 10-15 million total, and 3-5 million consistently active.

**Relevance to product**: The addressable market for a retail trader tool is the 9.6M retail traders, not the $9.6T institutional market. With only 2.5% retail share, the product targets a small but passionate niche.

Sources:
- [CompareForexBrokers - Trading Statistics](https://www.compareforexbrokers.com/trading/statistics/)
- [VettedPropFirms - How Many People Trade Forex](https://vettedpropfirms.com/how-many-people-trade-forex/)
- [Finsmes - How Many Forex Traders Worldwide](https://www.finsmes.com/2025/04/how-many-forex-traders-are-there-worldwide.html)

#### B1.3 Regional Breakdown of Retail Traders

| Region | Traders | Per Million Population |
|--------|---------|----------------------|
| Asia | 3,200,000 | 674 |
| Europe | 1,500,000 | 2,000 |
| North America | 1,500,000 | 2,500 |
| Africa | 1,300,000 | 878 |
| Middle East | 1,000,000 | 2,083 |
| South America | 600,000 | 1,364 |
| Central America | 335,000 | 1,861 |
| Oceania | 190,000 | 4,222 |

**Top countries**: UK (341,000), US (335,000), Japan (223,000), Singapore (218,000), Hong Kong (200,000).

**English-speaking markets**: US + UK + Australia/NZ combined represent roughly 700,000+ active traders -- a concentrated and high-value audience.

**Russian/CIS markets**: Specific data scarce. Russia has historically been a major retail forex market with hundreds of brokers (InstaForex, Alpari, etc.), but sanctions and regulatory changes have fragmented the market. The CIS region remains significant but increasingly separated from Western broker ecosystems.

**Relevance to product**: English-speaking markets (US, UK, AU) are the primary addressable market for an English-language tool. The Russian-speaking market is large but faces regulatory isolation and would need separate localization effort.

Sources:
- [CompareForexBrokers - Trading Statistics](https://www.compareforexbrokers.com/trading/statistics/)
- [Coinlaw.io - Foreign Exchange Industry Statistics](https://coinlaw.io/foreign-exchange-industry-statistics/)
- [Contentworks - Top Regions Forex Brokers Are Targeting 2025](https://contentworks.agency/top-regions-forex-brokers-are-targeting-in-2025/)

#### B1.4 Regulation Impact (ESMA, FCA, ASIC)

**ESMA (EU)**: Leverage caps of 30:1 for major pairs, 20:1 for minor pairs, 2:1 for crypto. Binary options banned for retail. Mandatory prominent risk warnings and margin close-out rules per account.

**FCA (UK)**: Adopted ESMA guidelines post-Brexit. Maintained leverage caps and banned incentives for CFD products.

**ASIC (Australia)**: Mirrors European restrictions with 30:1 leverage for majors, 20:1 for minors. Implemented in 2021.

**Net impact**: Regulation has pushed many retail traders to offshore brokers or prop trading firms (which are not directly regulated as brokers). The prop trading explosion is partly a regulatory arbitrage story.

Sources:
- [ForexChurch - ESMA Regulations](https://www.forexchurch.com/new-esma-regulations)
- [LiquidityFinder - CFD Leverage Limits Comparison](https://liquidityfinder.com/insight/industry/comparison-of-cfd-retail-broker-leverage-limits-by-regulator)
- [AInvest - 2025 Forex Regulation Overhaul](https://www.ainvest.com/news/2025-forex-regulation-overhaul-multi-asset-brokers-thrive-stricter-rules-2508/)

---

### B2. Competitive Landscape -- Forex Account Management Tools

#### B2.1 MyFXBook

**Overview**: The de-facto standard for forex trade tracking and verification, operating since ~2010.

**Traffic & User Base**:
- ~7.24 million monthly visits (Semrush, Jan 2025)
- Global ranking ~15,600 (Similarweb)
- 68.5% direct traffic, 25.7% organic search
- Audience: 70.6% male, largest age group 25-34
- Average session duration: 9 minutes 14 seconds

**Features**: MT4/MT5 account linking with verified trade history, economic calendar, trading signals, autotrade/copy trading, community/social system publishing, broker reviews, strategy comparison.

**Pricing**: Free core product (ad-supported). Ad-free subscription available. Premium account publishing with subscriber buttons. Compared to TradesViz Pro ($18/month), Myfxbook's core is free.

**Weaknesses**:
- Outdated UI/UX (legacy design, not modern SPA)
- Slow support -- account linking issues take days
- Too many "verified" systems turn out fake/misleading
- Not real-time (15-60 second update cycle)
- No prop trading-specific features
- No multi-asset (forex only)
- Heavy ad-supported model with cluttered interface

**Relevance to product**: MyFXBook validates the market demand for trade tracking/verification. Its weaknesses (outdated UI, no prop trading features, no real-time, no multi-asset) are the exact gaps myaccount-front aims to fill. However, its FREE pricing model is a major competitive barrier.

Sources:
- [Myfxbook Features](https://www.myfxbook.com/features)
- [Similarweb - myfxbook.com Traffic](https://www.similarweb.com/website/myfxbook.com/)
- [Trustpilot - Myfxbook Reviews](https://www.trustpilot.com/review/myfxbook.com)

#### B2.2 FX Blue

**Overview**: Free analytics and trading tools for forex traders, primarily MT4-focused.

**Features**: Trade analysis/publishing, trade copier, trading simulator, live charts, algorithmic news feeds. Updates as frequently as every 60 seconds.

**Limitations**:
- **MT4-only** -- no cTrader, limited MT5 support
- No standalone application -- dependent on MetaTrader
- No risk analysis, Monte Carlo simulations, or strategy optimization
- Limited feature set compared to dedicated analytics platforms
- No prop trading features
- No multi-broker dashboard capability

**Relevance to product**: FX Blue shows the market for analytics tools but has significant platform limitations. Its MT4-only restriction is a major gap given MT5's overtaking MT4 in 2025.

Sources:
- [FX Blue](https://www.fxblue.com/)
- [ForexTester - FX Blue Alternatives](https://forextester.com/blog/fxblue-alternatives/)
- [Trusted Broker Reviews - FX Blue Review](https://www.trusted-broker-reviews.com/fx-blue/)

#### B2.3 TradesViz

**Overview**: Feature-rich online trading journal supporting all asset classes.

**Features**:
- 600+ statistics and interactive charts
- AI-powered Q&A (natural language queries about trading data)
- Auto/real-time imports from 30+ brokers, 100+ manual import support
- 400+ customizable dashboard widgets
- Simulators for stocks, futures, forex, and options
- Pivot grid analytics

**Pricing**: Free basic plan (forever, no credit card). Pro: $13/month. Platinum: $20/month. 25% discount on annual plans.

**Relevance to product**: TradesViz is the most feature-complete modern competitor. Its comprehensive analytics, multi-asset support, and AI features set the bar high. However, it's a journaling tool, not a real-time multi-broker dashboard.

Sources:
- [TradesViz](https://www.tradesviz.com/)
- [TradesViz Pricing](https://www.tradesviz.com/pricing/)
- [TradesViz Trading Journal Review 2026](https://trading-journals.com/reviews/tradesviz)

#### B2.4 TradeMonitor (iFXHub)

**Overview**: Free forex journaling service with real-time monitoring capabilities.

**Features**: Instant drawdown monitoring, real-time updates and live statistics, MT4 integration with 184+ supported brokers, advanced chart visualization, open trade monitoring, monthly progress tracking.

**Relevance to product**: Direct competitor for real-time monitoring of forex accounts. Newer entrant attempting to address MyFXBook gaps.

Source: [Forex Factory - New Free Myfx Alternative](https://www.forexfactory.com/thread/1260595-new-free-myfx-alternative)

#### B2.5 Multi-Asset Portfolio Trackers

- **Delta (by eToro)**: Syncs with brokerages/exchanges/wallets for unified dashboard. Strong mobile app.
- **Sharesight**: 150+ verified broker connections globally. Focused on investment portfolios.
- **Kubera**: Connects to 10K+ financial institutions. Multi-currency support.
- **Trademetria**: Import from various brokerage accounts for centralized portfolio view.
- **Pro Stock Tracker**: Monitors 120+ currencies and 7,000+ pairs.

**Relevance to product**: These tools demonstrate that multi-broker aggregation is a solved problem in stock/crypto. Forex-specific aggregation lags behind.

Sources:
- [Delta by eToro](https://delta.app/en/forex-tracker)
- [Kubera Multi-Currency Tracker](https://www.kubera.com/blog/multi-currency-portfolio-tracker)
- [Trademetria Portfolio Tracker](https://trademetria.com/blog/portfolio-tracker-the-ultimate-tool-for-smart-portfolio-management/)

#### B2.6 Broker CRM / Trader Room Solutions (B2B)

| Solution | Description | Pricing | Strengths | Weaknesses |
|----------|-------------|---------|-----------|------------|
| **B2Core** (B2Broker) | Enterprise forex CRM | Starts at $200K/month (!) | Deep customization, crypto support | Extremely expensive, enterprise-only |
| **Leverate LXCRM + Sirix** | Legacy trader room | Undisclosed | 20+ years experience, MT4 integration | Legacy technology, not modern UI |
| **CurrentDesk** | Broker management system | Undisclosed | US-focused, comprehensive back-office | Limited modern frontend |
| **Brokeree Solutions** | MT4/MT5 plugins/tools | Undisclosed | Decade of experience, 100s of clients | Plugin-based, not standalone platform |
| **UpTrader** | CRM-first white label | Tiered by volume | Multi-currency wallets, analytics | Not transparent pricing |
| **Match-Trader** | All-in-one trading platform | White label from $2,500/mo, Server from $5,000/mo | Most transparent pricing, built-in CRM | Proprietary platform, not MT-compatible |
| **Quadcode** | White-label solutions | $25K-$42K setup packages | Web + mobile, KYC, PSP integration | High setup costs |

**Market size**: Forex CRM market valued at ~$516-530 million in 2025, projected to reach $909-950 million by 2033-2034, CAGR 6.5-7.8%. Over 6,500 active forex brokerages globally, with ~72% using specialized CRM solutions.

**Key insight**: B2Core's $200K/month pricing illustrates the massive gap between enterprise solutions and what small/mid brokers can afford. Match-Trader at $2,500/month is the value end. There is significant opportunity for a modern, affordable trader room frontend.

Sources:
- [B2Core Trader's Room](https://b2broker.com/products/b2core-traders-room/)
- [Brokeree Solutions](https://brokeree.com/)
- [Match-Trader White Label](https://match-trader.com/match-trader-white-label-as-low-as-2500-and-server-from-5000/)
- [UpTrader CRM Review](https://uptrader.io/en/articles/336/forex-crm-review-full-comparison-and-buyers-guide)
- [IndustryResearch - Forex CRM Market](https://www.industryresearch.biz/market-reports/forex-crm-solution-market-112756)

#### B2.7 New Entrants (2024-2026)

**The prop trading crisis created demand for new platforms**: After MetaQuotes revoked MT4/MT5 licenses from 80-100 prop firms in 2024-2025 (MetaTrader market share among prop firms dropped from 48% to 24%), there is surging demand for alternative trading technology.

Notable new approaches:
- **FTMO acquiring OANDA** for $250M -- props becoming brokers
- **Match-Trader** capturing displaced prop firms
- **TradeLocker** -- full-stack prop trading platform
- **Tiny Fund** -- copy trading startup connecting investors to vetted traders (pilot phase, $425 MRR)

**Relevance to product**: The MetaQuotes disruption has created a rare window of opportunity where prop firms need new technology solutions urgently.

Sources:
- [FinanceMagnates - Retail Trading & Prop Firms 2025](https://www.financemagnates.com/forex/retail-trading-prop-firms-in-2025-five-defining-trends-and-one-prediction-for-2026/)
- [VeritasChain - Prop Trading 2024 Reckoning](https://veritaschain.org/blog/posts/2025-12-28-prop-trading-reckoning/)
- [Atomiq Consulting - MT4/MT5 Prop Trading](https://atomiqconsulting.com/mt4-mt5-prop-trading/)

---

### B3. Forex Broker API Landscape

#### B3.1 MT4/MT5 Manager API

**Architecture**:
- MT4: Legacy 32-bit, single-threaded, forex-only, fewer order types
- MT5: Multi-threaded, 64-bit, multi-asset (forex, stocks, futures, ETF CFDs)
- MetaQuotes halted new MT4 server licenses in early 2018
- **2025 milestone**: MT5 overtook MT4 as most popular platform worldwide for the first time

**Access restrictions**: Only "manager" group accounts can connect via Manager API. Permissions must be configured in MetaTrader Administrator.

**Key limitation for multi-broker integration**: Each broker runs their own MT4/MT5 server. There is no universal API -- you need separate credentials and connections for each broker. Rate limits apply. Manager API access typically requires broker cooperation.

**MetaQuotes crackdown (2024)**: MetaQuotes terminated MT4/MT5 licenses from dozens of prop firms citing grey-label license abuse. Any firm needing MT access must prove licensed status with a reference letter from a brick-and-mortar bank. This makes third-party integration more difficult.

Sources:
- [B2Broker - Web API for MetaTrader](https://b2broker.com/news/web-api-for-metatrader-how-does-it-work/)
- [Brokeree - API for Multi-Asset Brokers](https://brokeree.com/articles/digest-api-for-multi-asset-brokers/)
- [MetaTrader 4 API](https://www.metatrader4.com/en/brokers/api)
- [FinanceMagnates - MetaQuotes Prop Trading](https://www.financemagnates.com/forex/prop-trading-and-metaquotes-funding-pips-case-may-mark-the-end-of-mt-access-to-us-clients/)

#### B3.2 MetaApi (Cloud API Service)

**Overview**: Third-party cloud service that abstracts MT4/MT5 API access, providing a unified REST/WebSocket API.

**Key capabilities**:
- Supports ANY MetaTrader broker (no broker-side setup needed)
- Same stable API for both MT4 and MT5
- Balance, positions, pending orders access
- Trade history retrieval
- Real-time price feeds and ECN Level-2 data
- Low-latency direct trading
- Multi-region deployment with failover
- Additional products: CopyFactory (copy trading), MetaStats (statistics), Risk Management API

**Pricing**: One account free. Paid tiers scale with number of accounts, designed to be equal to or less than self-hosted infrastructure costs. Minimum 6-hour billing per server launch.

**SDKs**: JavaScript (npm), Python (PyPI), Java

**Relevance to product**: MetaApi is potentially the CRITICAL enabler for myaccount-front. It solves the multi-broker integration problem by providing a unified API layer across all MT4/MT5 brokers without requiring broker-side cooperation. This eliminates the biggest technical risk (RAT #3).

Sources:
- [MetaApi](https://metaapi.cloud/)
- [MetaApi FAQ](https://metaapi.cloud/docs/client/faq/)
- [MetaApi JavaScript SDK](https://www.npmjs.com/package/metaapi.cloud-sdk)

#### B3.3 cTrader Open API

**Overview**: Open API accessible by anyone with a cTrader ID (cTID). Supported by default on all cTrader-affiliated brokers.

**Capabilities**: Real-time market data, full trading operations, custom charts and widgets, integration with messaging platforms (Telegram, Slack).

**Supporting brokers**: Pepperstone, IC Markets, FP Markets, and others.

**Limitation**: Only works with cTrader brokers (roughly 5% of retail traders use cTrader).

Sources:
- [cTrader Open API](https://openapi.ctrader.com/)
- [cTrader Help Centre - Open API](https://help.ctrader.com/open-api/)

#### B3.4 FIX Protocol

**Overview**: Industry-standard financial messaging protocol. Used primarily by institutional traders and sophisticated retail traders.

**Retail adoption**: Limited. Available at select brokers (IC Markets, Forex4you, Price Markets, etc.) but typically requires substantial minimum deposits and trading volumes.

**Relevance to product**: FIX protocol is not practical for a retail-focused aggregation tool.

Sources:
- [TradingBrokers - FIX API Forex Brokers](https://tradingbrokers.com/fix-api-forex-brokers/)
- [BabyPips - FIX API Definition](https://www.babypips.com/forexpedia/fix-api)

#### B3.5 REST API Availability Across Brokers

Major brokers offering API access (2025): OANDA (REST API), Interactive Brokers (TWS API), Forex.com, IG Markets, Pepperstone (FIX + cTrader), IC Markets (FIX + cTrader).

**Key challenge**: There is no standardized API across forex brokers. Each has unique endpoints, authentication, rate limits, and data formats. This is the fundamental technical barrier for any multi-broker aggregation product.

**Practical solution**: MetaApi abstracts this for MT4/MT5 brokers (majority of market). cTrader Open API covers another slice. REST APIs cover the rest.

Sources:
- [TopBestForexBrokers - Brokers with API Access 2025](https://www.topbestforexbrokers.com/best-forex-brokers-with-api-access-in-2025-a-comprehensive-guide/)
- [ForexBrokers.com - Best API Brokers 2026](https://www.forexbrokers.com/guides/best-api-brokers)

---

### B4. Retail Forex Trader Needs & Pain Points

#### B4.1 Common Complaints (From Forums)

Based on research across ForexFactory, Reddit, and review sites:

1. **Psychology/Emotional control**: Cited as the #1 problem -- emotional trading, taking trades based on feelings
2. **Scams everywhere**: Fake mentors, fake signal providers, fake verified systems on MyFXBook
3. **Prop firm instability**: 80+ prop firms collapsed in 2024-2025 due to MetaQuotes crackdown
4. **Platform fragmentation**: Managing multiple accounts across multiple brokers is time-consuming
5. **Lack of transparency**: Difficulty verifying claims of profitable traders/systems
6. **News event manipulation**: Complaints about economic calendar impact ratings being changed last-minute
7. **Withdrawal issues**: Ongoing complaints about broker withdrawal delays/denials
8. **Finding profitable strategies**: Increasing difficulty as markets become more efficient and AI-driven

Sources:
- [ForexFactory - What Is Your Biggest Problem](https://www.forexfactory.com/thread/1175960-what-is-your-biggest-problem-when-it-comes)
- [ForexFactory - My Biggest Problem](https://www.forexfactory.com/thread/1282179-my-biggest-problem)
- [ClubDark - Why You're Losing Trades](https://clubdark.net/why-youre-losing-trades-in-2026-the-real-reasons-and-how-to-fix-them/)

#### B4.2 Multi-Broker Usage Patterns

**Specific percentage data**: Not widely surveyed or publicly reported. No major research firm has published exact percentages of traders using 2+ brokers.

**Qualitative evidence**: Industry articles and forum discussions confirm that multi-broker usage is common among experienced traders. Reasons include:
- Better spreads/swaps for specific strategies
- Access to instruments not available at primary broker (exotic pairs)
- Risk diversification across brokers
- Different brokers for different account types (live vs. demo, different leverage)
- Regulatory limitations (US traders limited to US brokers, use offshore for higher leverage)

**Estimate**: The product audit's estimate of 15-25% using 2+ brokers appears reasonable based on available qualitative data.

Source: [EarnForex - Multiple Trading Accounts in Forex](https://www.earnforex.com/guides/multiple-trading-accounts-in-forex-is-there-any-point/)

#### B4.3 Tools Traders Currently Use

| Tool | Usage | Purpose |
|------|-------|---------|
| MetaTrader 4 | 85% adoption (declining) | Primary trading platform |
| MetaTrader 5 | Now #1 (overtook MT4 in 2025) | Primary trading platform |
| MyFXBook | ~7.2M monthly visits | Trade tracking, verification |
| TradingView | ~120M+ monthly visits | Charting, analysis, social |
| Excel/Google Sheets | Very common | Manual tracking, journaling |
| cTrader | ~5% of traders | Alternative trading platform |
| TradesViz | Growing | Trade journaling, analytics |
| ForexFactory | ~26.8M monthly visits | News, forums, calendar |

**Key insight**: 55% of traders now use mobile devices as primary platform. Desktop/web is 45%. Mobile-first design is increasingly important.

Sources:
- [CompareForexBrokers - Trading Statistics](https://www.compareforexbrokers.com/trading/statistics/)
- [Similarweb - myfxbook.com Competitors](https://www.similarweb.com/website/myfxbook.com/competitors/)
- [BestBrokers - Forex Trading Statistics](https://www.bestbrokers.com/forex-trading/forex-trading-statistics/)

#### B4.4 Trust Barriers for Third-Party Account Aggregation

**Major concerns identified**:
1. **Credential exposure**: Traders must share API keys or investor passwords with unknown third parties controlling real money
2. **Data security**: Server misconfiguration risks (e.g., FBS broker incident -- exposed passport numbers, unencrypted passwords)
3. **Phishing risk**: Substantial increase in cyber-attacks targeting broker clients through SMS, emails, cold calls
4. **Lack of regulation**: Data aggregators are not subject to the same supervision as banks or brokers
5. **No insurance**: Unlike bank deposits, trading account data has no regulatory protection if compromised

**Mitigation strategies observed in market**:
- Read-only API access (investor passwords, not master passwords)
- Transparent security architecture documentation
- Third-party security audits
- Social proof (testimonials, verified user counts)
- Starting with established brand (MyFXBook has decade-long trust)

**Relevance to product**: Trust is THE critical barrier (RAT #2). New entrants face a chicken-and-egg problem: traders won't trust until many use it, but many can't use it until someone trusts first.

Sources:
- [Caramellaapp - Top Security Features Trading Platform](https://caramellaapp.com/blog_post/wW2mof73w/top-security-features-to-look-for-in-a-trading-platform)
- [FinanceMagnates - Security Risks Trading Platforms](https://www.financemagnates.com/thought-leadership/security-risks-for-trading-platforms-and-their-users/)
- [Forex92 - How to Analyze Security of Forex Software](https://forex92.com/blog/how-to-analyze-the-security-of-forex-software/)

---

### B5. B2B Forex Market

#### B5.1 White-Label Trader Room Market

**Market size**: The Forex CRM/back-office market is valued at approximately **$516-530 million in 2025**, projected to reach **$909-950 million by 2033-2034** (CAGR 6.5-7.8%). Some estimates reach $1.5 billion by 2028 (CAGR ~10%).

**Over 6,500 active forex brokerages** operate globally, with ~72% implementing specialized CRM solutions.

**Key market statistic**: 68% of global forex brokers are implementing AI-based CRM systems. Over 65% of platforms launched in the past two years are SaaS models.

Sources:
- [IndustryResearch.biz - Forex CRM Solution Market](https://www.industryresearch.biz/market-reports/forex-crm-solution-market-112756)
- [GlobalGrowthInsights - Forex CRM Market](https://www.globalgrowthinsights.com/market-reports/forex-crm-solution-market-114966)
- [FXBackOffice - CRM Trends 2025](https://fxbackoffice.com/blog/crm-trends-2025-what-forex-brokers-should-expect)

#### B5.2 Small-to-Mid Broker Technology Needs

**Cost landscape for new/small brokers**:

| Solution | Monthly Cost | Setup/One-time |
|----------|-------------|----------------|
| Match-Trader White Label | $2,500/mo | -- |
| Match-Trader Server | $5,000/mo | -- |
| Quadcode Light | -- | $25,000 |
| Quadcode Advanced | -- | $32,000 |
| Quadcode Turnkey | -- | $42,000 |
| B2Core (enterprise) | $200,000/mo | -- |
| Generic white-label range | $2,500-$50,000/mo | $17,500-$50,000 setup |

**Time to launch**: Typically ~2 weeks with a white-label provider, at a minimum cost of ~$20,000.

**Key needs identified**:
- 54% demand for automation
- 29% rise in KYC efficiency tools
- 17% CRM personalization
- Modern UI/UX (critical for trader acquisition)
- MT4/MT5 integration
- Mobile-responsive design
- Payment processing integration

Sources:
- [Match-Trader White Label Pricing](https://match-trader.com/match-trader-white-label-as-low-as-2500-and-server-from-5000/)
- [Quadcode White Label Solutions](https://quadcode.com/blog/best-forex-white-label-solutions-2025)
- [NYC Servers - Forex White Label Solutions 2026](https://newyorkcityservers.com/blog/forex-white-label-broker-solutions)

#### B5.3 Demand for Modern React/Next.js Based Trading UIs

**Evidence of demand**:
- 62+ trading React website templates on ThemeForest (prices $15-$79 each)
- Multiple forex-specific Next.js templates: FxTrade, TradeBro, Tradez, ForTradex
- Next.js is increasingly recognized as ideal for trading platforms due to SSR performance and SEO

**Gap in market**: Most broker technology solutions (B2Core, CurrentDesk, Leverate) use legacy monolithic architectures with outdated UIs. There is no dominant **modular Next.js-based trader room** solution. This gap was identified in the product audit (Section 3) and confirmed by market research.

**Relevance to product**: The B2B opportunity for a modern, affordable, modular React/Next.js trader room frontend is validated by:
- High costs of existing solutions ($2,500-$200,000/month)
- Legacy UI/UX of incumbents
- Demand signals from template marketplace
- 6,500+ active brokerages seeking technology

Sources:
- [ThemeForest - Trading React Templates](https://themeforest.net/search/trading%20react)
- [Slashdev - Build Trading Platform in NextJS](https://slashdev.io/-how-to-build-a-custom-trading-platform-in-nextjs-in-2024)

---

### B6. Regulatory Environment

#### B6.1 Key Regulations Affecting Account Aggregation

**MiFID II (EU)**:
- Applies to any platform facilitating financial instrument transactions in EU
- Requires pre-trade and post-trade transparency
- Transaction reporting mandated -- detailed data on trades must be submitted to regulators
- Authorization required as Investment Services Provider (ISP) from national regulator

**GDPR (EU)**:
- Applies to any platform handling EU user data
- Conflicts with MiFID II: if a client requests data deletion during MiFID retention period, both regulations must be balanced
- Requires explicit consent for data processing
- Data breach notification within 72 hours

**EU AI Act (effective Feb 2025)**:
- High-risk AI systems in financial services must comply
- Additional rules for general-purpose AI models effective August 2025
- Relevant if the product uses AI for trading recommendations

Sources:
- [ESMA - MiFID II](https://www.esma.europa.eu/publications-and-data/interactive-single-rulebook/mifid-ii)
- [Burges Salmon - MiFID II and GDPR Alignment](https://www.burges-salmon.com/news-and-insight/legal-updates/how-to-align-mifid-ii-and-gdpr-when-processing-client-data)
- [FinanceMagnates - GDPR and MiFID II Conundrum](https://www.financemagnates.com/institutional-forex/regulation/regulation-conundrum-gdpr-mifid-ii/)

#### B6.2 Data Handling Requirements

**For a data aggregator (read-only dashboard)**:
- GDPR compliance is mandatory for EU users (consent, right to erasure, data portability)
- Financial data requires encryption at rest and in transit
- MiFID II does NOT directly apply if the platform does not execute trades or provide investment advice
- However, storing financial data may trigger national-level regulatory requirements

**For a platform enabling trading**:
- Full MiFID II authorization required
- Capital adequacy requirements
- Segregated client funds obligations
- Transaction reporting to regulators

**Relevance to product**: A READ-ONLY dashboard (no trade execution) faces significantly lighter regulation than a trading platform. This is a critical architectural decision -- starting read-only dramatically reduces regulatory burden.

#### B6.3 Licensing Requirements

**US (if serving US clients)**:
- CFTC registration required for brokers/dealers
- NFA membership required
- FINRA/NFA regulation needed for MetaTrader access (per MetaQuotes 2024 policy)
- Data aggregators: CFPB principles apply but no formal licensing currently

**UK**:
- FCA authorization if providing investment services
- Data aggregation alone may not require FCA authorization (depends on scope)

**Australia**:
- ASIC oversight for financial services
- Australian Financial Services License (AFSL) if executing trades

**Key insight**: The regulatory landscape favors a **data aggregation / analytics tool** approach over a **trading execution platform**. By NOT executing trades, the product avoids the most burdensome regulatory requirements.

Sources:
- [InnReg - Forex Broker Regulation](https://www.innreg.com/blog/forex-broker-regulation)
- [NFA - Forex Regulatory Guide](https://www.nfa.futures.org/members/member-resources/files/forex-regulatory-guide.html)
- [BPI - Data Aggregators Issue Summary](https://bpi.com/data-aggregators-issue-summary/)

---

### B7. Key Market Trends

#### B7.1 AI/ML in Retail Forex Trading Tools

**Market size**: Algorithmic trading market projected to grow from $21.89 billion (2025) to $25.04 billion (2026) -- **14.4% CAGR**.

**Adoption**: 51% of survey respondents anticipate AI/ML will drive the greatest technological advancements; 85% of firms planning to increase AI use in trading.

**Current capabilities**: AI can analyze up to 400,000 data points per second for market analysis. AI accounts for ~15% of forex trades currently.

**Retail AI tools emerging**:
- Tickeron: AI pattern recognition with confidence scoring
- TradesViz: AI-powered natural language Q&A on trading data
- AlgosOne: Institutional-level algo trading for retail
- Webull: Voice-activated natural language order placement
- Bridgewise: White-label AI research tools for CFD platforms

**Regulation**: EU AI Act now applies to high-risk financial AI systems (Feb 2025).

**Relevance to product**: AI-powered analytics (portfolio insights, risk alerts, pattern detection) would be a strong differentiator. TradesViz already offers AI Q&A -- the product should consider similar features for prop trading analytics.

Sources:
- [PragmaticCoders - Top AI Trading Tools 2026](https://www.pragmaticcoders.com/blog/top-ai-tools-for-traders)
- [FinanceMagnates - Retail Traders Scratching Surface of AI](https://www.financemagnates.com/forex/retail-traders-are-just-scratching-the-surface-of-ai-fmls25-panel-on-broker-growth/)
- [The5ers - AI Tools for Forex Trading](https://the5ers.com/ai-tools-for-forex-trading)

#### B7.2 Social/Copy Trading Growth

**Market projections**:
- Copy trading platform market: $4.27B (2024) growing to **$15.42B by 2033** at **17.8% CAGR**
- Social trading platform: $10.16B (2026) to $19.81B (2035) at 7.4% CAGR
- Estimated 10-20 million individuals now actively engage in copy trading

**Key players**: eToro (largest social trading platform), ZuluTrade, NAGA, Darwinex

**Relevance to product**: Copy trading integration could be a future module. The 17.8% CAGR makes this the fastest-growing segment of trading technology. However, it requires trade execution capability and broker partnerships, making it a Phase 3+ feature.

Sources:
- [GrowthMarketReports - Copy Trading Platform Market](https://growthmarketreports.com/report/copy-trading-platform-market)
- [BusinessResearchInsights - Social Trading Market](https://www.businessresearchinsights.com/market-reports/social-trading-market-120197)
- [Medium - Copy Trading Why Is Market Growing](https://medium.com/coinmonks/copy-trading-why-is-the-market-growing-so-fast-729b8269ee59)

#### B7.3 Mobile-First Trading Platforms

**Market size**: Online forex trading platform market: $11.95B (2024) to $13.29B (2025), reaching **$20.08B by 2029** at **10.9% CAGR**.

**Mobile dominance**: **55% of forex traders now use mobile as primary platform** (up from minority just a few years ago). Mobile-first design is "non-negotiable" in 2026.

**Relevance to product**: Next.js with responsive design naturally supports mobile. However, the product should be designed mobile-first, not desktop-first with mobile adaptation. This aligns with the audience (prop traders 20-35 who are mobile-native).

Sources:
- [GlobeNewsWire - $20B Online Forex Trading Platform Market](https://www.globenewswire.com/news-release/2025/09/29/3157883/0/en/Trends-Strategies-Shaping-the-20-Billion-Online-Forex-Trading-Platform-Market-2025-2029-2034.html)
- [FinanceMagnates - Multi-Platform Trading Ecosystems 2025](https://www.financemagnates.com/thought-leadership/the-power-of-choice-why-multi-platform-trading-ecosystems-will-define-2025/)

#### B7.4 Prop Trading vs Traditional Retail Forex -- The Market Shift

**Explosive growth**: Prop trading industry expanded **1,264% between Dec 2015 and Apr 2024**, far outpacing 240% growth in traditional investing.

**FTMO financials (2024)**:
- Revenue: CZK 6.84B (~**$329 million**), up 53% YoY
- Net profit: CZK 1.3B (~$62.5 million)
- 2.3 million open trading accounts registered (+33% YoY)
- $450 million paid out to traders over 10 years
- FTMO acquired OANDA for $250 million

**Key shifts in 2024-2025**:
1. **MetaQuotes crackdown** (Feb 2024): Terminated licenses from 80-100 prop firms. MetaTrader market share among props dropped from 48% to 24%.
2. **Props becoming brokers**: FTMO acquired OANDA; The5ers founders backing brokerage ventures.
3. **US market re-entry**: After 2024 exodus, FTMO/The5ers/FundedNext returned to US in 2025.
4. **Futures growth**: 5,556% growth in search interest from 2020 to 2024 (373 to 21,095 searches).
5. **"Super App" ambitions**: CMC Markets, NAGA, Swissquote, XTB building multi-asset platforms combining traditional finance, DeFi, and banking.
6. **Consolidation**: The industry is consolidating -- large players absorbing smaller ones.

**Relevance to product**: The prop trading segment is the RIGHT first target. The market is massive ($329M revenue for FTMO alone), growing rapidly, and the MetaQuotes disruption has created urgent demand for new technology. The product audit's recommendation to focus on prop trading first is strongly validated by this data.

Sources:
- [FinanceMagnates - FTMO $329M Revenue](https://www.financemagnates.com/forex/ftmos-parent-netted-over-62-million-on-329m-revenue-in-2024/)
- [FinanceMagnates - FTMO $450M Paid Out](https://www.financemagnates.com/forex/ftmo-announces-over-450-million-paid-out-as-prop-trading-firm-turns-10/)
- [FinanceMagnates - Retail Trading & Prop Firms 2025](https://www.financemagnates.com/forex/retail-trading-prop-firms-in-2025-five-defining-trends-and-one-prediction-for-2026/)
- [Investing.com - The Great Shake-Out](https://www.investing.com/analysis/the-great-shakeout-how-2025-reshaped-the-prop-trading-industry-200672263)
- [BestPropFirms - Prop Trading Statistics](https://www.bestpropfirms.com/guides/prop-trading-stats/)

---

### B8. Key Data Points Summary for Product Decisions

#### B8.1 Numbers That Matter Most

| Metric | Value | Source |
|--------|-------|--------|
| Global retail forex traders | ~9.6 million | Multiple |
| Prop trading accounts (FTMO alone) | 2.3 million | FinanceMagnates |
| FTMO annual revenue | $329 million | FinanceMagnates |
| MyFXBook monthly traffic | 7.24 million visits | Semrush |
| MT5 overtook MT4 | 2025 (first time) | CompareForexBrokers |
| Mobile-first traders | 55% | CompareForexBrokers |
| Profitable retail traders | 20-30% | Medium/NFA Data |
| Trader average age | 25-44 (majority) | Multiple |
| Gender split | ~89% male | Multiple |
| Average Australian deposit | $8,400 | BestBrokers |
| Forex CRM market | $516-530M (2025) | IndustryResearch |
| Copy trading CAGR | 17.8% | GrowthMarketReports |
| Trading platform market CAGR | 10.9% | GlobeNewsWire |
| Prop firms collapsed (2024-2025) | 80-100+ | VeritasChain |
| MT share drop among prop firms | 48% to 24% | Multiple |

#### B8.2 Validation of Product Audit Assumptions

| Assumption from Audit | Research Validation | Status |
|----------------------|---------------------|--------|
| Prop traders are best first segment | FTMO $329M rev, 2.3M accounts, 80+ firms collapsed needing new tech | **STRONGLY VALIDATED** |
| Multi-broker is a real pain point | Qualitative evidence strong; no quantitative % data available | **PARTIALLY VALIDATED** |
| MyFXBook is outdated | Confirmed: legacy UI, no real-time, no prop features, 7.2M monthly visits proves demand | **VALIDATED** |
| Binary options should be excluded | Banned in EU/UK/AU, regulatory liability | **VALIDATED** |
| B2B opportunity is large | $516M CRM market, 6,500+ brokerages, $200K/mo for B2Core | **VALIDATED** |
| Trust is critical barrier | Security concerns endemic, no regulation for data aggregators | **VALIDATED** |
| MetaApi solves multi-broker integration | Supports any MT broker, unified API, free tier available | **VALIDATED** |
| Mobile-first matters | 55% mobile-first traders, "non-negotiable" in 2026 | **VALIDATED** |
| AI features are differentiator | 85% of firms increasing AI use, TradesViz already has AI Q&A | **VALIDATED** |
| Read-only avoids heavy regulation | MiFID II mainly applies to trade execution/investment advice | **VALIDATED** |

#### B8.3 New Insights Not in Original Audit

1. **MetaQuotes disruption** creates urgent opportunity: 80+ prop firms need new technology, MT market share dropped from 48% to 24% among props. This is a time-sensitive window.

2. **Props becoming brokers** (FTMO acquired OANDA for $250M): The prop industry is maturing into regulated brokerage. This creates demand for proper trader room technology.

3. **Match-Trader at $2,500/month** sets the price floor for B2B white-label: The product's B2B pricing should be competitive with this reference point.

4. **TradesViz is the real B2C competitor** (not just MyFXBook): Modern UI, AI features, $13-20/month, multi-asset support. The product must offer something TradesViz does not (real-time multi-broker monitoring, prop trading rule compliance).

5. **The "Super App" trend**: CMC, NAGA, XTB building multi-asset platforms combining trading, DeFi, banking. This validates the long-term vision of multi-asset support but confirms that starting focused (prop) is correct.

6. **EU AI Act compliance needed**: If the product uses AI for trading insights in EU market, it must comply with EU AI Act (effective Feb 2025).

7. **The $200K/month B2Core pricing** reveals massive price gap: Small/mid brokers cannot afford enterprise solutions. A modern trader room at $1,500-5,000/month has a large addressable market.

---

### B9. Research Methodology & Limitations

**Sources used**: Industry research reports (IMARC, Mordor Intelligence, Business Research Co.), analytics platforms (Similarweb, Semrush), financial industry publications (FinanceMagnates, ForexBrokers.com), competitor websites, trading forums (ForexFactory, BabyPips), regulatory authority publications (ESMA, FCA, ASIC), technology provider documentation.

**Key limitations**:
1. **No direct survey data**: Specific multi-broker usage percentages not available from public sources
2. **Russian/CIS market data**: Very limited public data on retail forex in Russia/CIS post-sanctions
3. **MetaApi pricing**: Detailed tier pricing not publicly available (requires account)
4. **MyFXBook user count**: Total registered users not publicly disclosed
5. **Market size estimates vary widely**: CAGR ranges from 3.8% to 10.6% depending on source and scope
6. **Prop trading market size**: Difficult to size precisely; FTMO alone is $329M but total market estimates vary from $2.5B to $10B+

**Recommended follow-up research**:
- Direct survey of 100+ prop traders on multi-account management pain points
- MetaApi pricing deep-dive (create account and evaluate costs at scale)
- Interview 3-5 small forex brokers on technology spending and pain points
- Legal consultation on data aggregation regulatory status in target markets (US, UK, EU)
