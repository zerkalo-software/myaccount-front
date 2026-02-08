# Forex Segment — Confluence & Jira Research

**Дата**: 2026-02-08
**Источник**: Atlassian (Confluence + Jira), workspace `maxiproject`
**Cloud ID**: `16622baa-680c-4526-8e66-734dd6b757cd`

---

## Executive Summary

Atlassian workspace содержит **обширную** документацию по форекс-сегменту в CRM Confluence space и нескольких Jira-проектах (CRM, MISS, WDA, WB, YA). Форекс — **основной и наиболее зрелый** торговый поток в платформе xCritical CRM, с глубокой документацией по регистрации, торговле, платежам, комплаенсу, отчётности и интеграциям.

---

## 1. BUSINESS / PRODUCT CONTEXT

### 1.1 Архитектура продукта — Forex как основной поток

Платформа работает с несколькими "типами инстансов":
- **Forex** (основной) — полная торговля с MT4/MT5/DTS/cTrader
- **Forex + Binary Options** (напр., 1Prime)
- **Crypto** (Tokenexus, Beaxy, Vahetus)
- **Prop Trading** (отдельный поток на базе forex-инфраструктуры)
- **Non-Trading** (RemoteMode)

**Ключевые страницы Confluence:**
- **"CRM & MyAccount functions"** (page 2267119684) — Полная матрица функций CRM дашбордов, отчётов и вкладок MyAccount по Forex/Crypto/Binary
- **"Environment features"** (page 2331115617) — Доступность фич по production окружениям (maxi-prod = forex, 1prime-prod = forex+binary, tamambrokerage-prod = forex, и т.д.)
- **"CRM Features"** (page 2329739675) — Гайд по включению всех CRM-фич

### 1.2 Forex MyAccount — Клиентские функции

Forex MyAccount — личный кабинет клиента со следующими вкладками:
- **My Account** — Торговые счета, логин к платформе, переводы баланса
- **Deposit** — Платёжные системы и карточные процессоры
- **Withdrawal** — Запросы и история выводов
- **Personal Data** — KYC/персональная информация
- **Change Password** — Управление паролями с синхронизацией торговых платформ
- **Account Status** — Отчёты по транзакциям (экспорт PDF)
- **Promo/Offers** — Бонусные акции
- **Documents** — Загрузка документов KYC/комплаенс
- **Appropriateness Test** — Оценка знаний трейдера
- **Economic Profile** — Профилирование финансовых рисков
- **Copy Trading** — Интеграция MT4 Social Trading
- **PAMM** — Управление PAMM-аккаунтами
- **Onboarding** — Мультишаговая регистрация (page 3982721069)

**Ключевые страницы:**
- **"Forex MyAccount Texts"** (page 1470234879) — Все настраиваемые тексты UI
- **"MyAccount Settings"** (page 3018883739) — Полный справочник настроек
- **"MyAccount Forex Patch-Smoke file"** (page 3875045410) — Чеклист smoke-тестов
- **"Forex Onboarding"** (page 3982721069) — Stepper-based onboarding
- **"Forex Economic Profile"** (page 3982032963) — Анкета оценки рисков
- **"Forex Copy Trading"** (page 3981967404) — MT4 Social Trading iframe

### 1.3 Регистрация Forex

- **"Forex Registration methods"** (page 1416397053) — Полная документация потока регистрации (email, phone, full/second methods), создание лида, авто-создание торгового счёта, TTL
- **"Forex instances"** (page 3314680459) — KYC use cases по брендам (ForexBY, GWG и т.д.)
- **"ForexBY (Minsk)"** (page 3315008131) — Brand-specific KYC потоки

### 1.4 Конвергенция Crypto + Forex

- **"Crypto + Forex trading account for the one client"** (page 1779990591) — Архитектурное решение с 3 концепциями для единого Forex+Crypto MyAccount:
  - Концепция 1: Main Wallet (общий баланс)
  - Концепция 2: Quick solution через Crypto MyAccount с forex контроллерами
  - Концепция 3: Встроенная forex-вкладка в Crypto MyAccount
  - Решение: Все фиатные депозиты/выводы через Crypto CRM; внутренние переводы между crypto и forex

### 1.5 Конвергенция Forex + Binary Options

- **MISS-4556** / **"Forex & BO trading within the same instance"** — Эпик для торговли forex и BO на одном торговом счёте
- **"[CRM-37357] Forex & BO trading within the same instance"** (page 4575330692) — Детальный технический анализ
- **"AMS - Forex & BO"** (page 6231457797) — Адаптация партнёрской системы для forex+BO

### 1.6 Prop Trading (на базе Forex-инфраструктуры)

- **"Prop Trading"** (page 4816175152) — Полный PRD с бизнес-потоком, типами аккаунтов (PurchaseAccount, ChallengeAccount, FundedAccount), правилами транзакций, UI спеками, Figma-ссылками
- **"PROP Trading SRS"** (page 4929486856) — SRS от WebApp команды
- **"PropTrading - dorabotki"** (page 5591695362) — Бизнес-доработки
- **"Project DatasetPros.ai"** (page 5758091346) — Новая модель "Pay per Data" на замену традиционному проп-трейдингу

### 1.7 Бренды на Forex

Production forex-окружения: MaxiMarkets, UMarkets, LimeFX, DowMarkets, ForexBY, GWG, Ucapitals, 1Prime, TamamBrokerage, Gracexfx, Doratrade, Worldtradex, QuantTekel, ZFMarkets и другие.

---

## 2. TECHNICAL CONTEXT

### 2.1 Интеграции торговых платформ

Система поддерживает **четыре торговые платформы** для forex:

| Платформа | Интеграция | Ключевые доки |
|-----------|-----------|--------------|
| **DTS (Deltix Trading Server) v2** | Forex-specific; .NET Framework SDK | Page 4386979859, Page 5899550774 |
| **MetaTrader 4 (MT4)** | Manager API Wrapper | Page 2398290045, Page 130908905 |
| **MetaTrader 5 (MT5)** | Полная интеграция | Page 2424177197, Page 3052109940 |
| **cTrader** | REST API | Page 4234346497, Page 4221403217 |

**Ключевые технические страницы:**
- **"MT5 Forex Integration"** (page 2424177197) — Создание аккаунтов, транзакции, переводы, торговые позиции, SSO, отчётность
- **"MT5 Forex configuration"** (page 3052109940) — Все настройки MT5 (группы валют, кредитное плечо, учётные данные)
- **"MetaTrader 4 vs. MetaTrader 5"** (page 2274231045) — Сравнение платформ
- **"MT5: Hedging and Netting System"** (page 3733258351) — Режимы учёта позиций MT5
- **"Trading Platforms"** (page 130941744) — Родительская страница всех торговых платформ
- **"Trading Settings"** (page 4290674805) — Справочник настроек cTrader, MT4, MT5
- **"Trading Accounts"** (page 2960064530) — Потоки создания аккаунтов
- **"Formulas - forex"** (page 28154862) — Формулы расчёта торговых комиссий
- **"Containerizing trading platform adapters"** (page 5899550774) — Проблемы контейнеризации (DTS = .NET Framework, MT4 = unmanaged DLLs)

### 2.2 SSO и аутентификация платформ

- **"[CRM-34785] MT5-CRM-WebApp integration"** (pages 4277633039, 4283924557, 4283924481) — V1 и V2 SSO между CRM/MyAccount и MT5 WebApp, token-based auth
- **"MT5. S2S with CRM"** (page 4185620535) — Server-to-server интеграция

### 2.3 Интеграции платёжных систем

Обширная документация:
- **"PS Forex configuration"** (page 3415244850) — Конфигурация десятков платёжных систем
- **"PS Forex/Crypto configuration"** (page 3431956505) — Общая конфигурация
- **"List of Payment Systems"** (page 291864588) — Мастер-список всех forex PS

**Документированные PS интеграции:** AstroPay, AnyMoney, PayPros, Globepay, Ezeebill, Xpate, Texcent, RealDeposit, VPayApp, FXPAYZ, KoraPay, iPay, Interkassa, DragonTech, Yeezypay, Qiwi, Alfa-Bank, Stripe, Connpay, PayCly, pspARK, AvePay, TransactaPayOK, Epayme, T365, BetPayRabbit, Paymaxis и многие другие.

### 2.4 Партнёрская система (AMS)

- **"AMS: Forex"** (page 3399713409) — Родительская страница
- **"AMS - Forex & BO"** (page 6231457797) — AMS для forex+BO
- **"CPA (Forex & BO)"** (page 5982388226) — Тип сделки CPA
- **"PnL RevShare Forex & BO"** (page 5982748680) — Revenue sharing по PnL
- **"Trading Commission RevShare"** (page 3801776172) — Commission-based revenue sharing
- **MISS-5223** — Эпик: "[1Prime][AMS 1.0] AMS Forex & BO" (In Progress, 2025-2026)

### 2.5 Архитектура фронтенда MyAccount

- **Существующий репо**: `myaccount-front-react-forex` (Bitbucket) — текущая React-реализация
- **CRM-42322** (Story, In Development) — **"[Forex][Front]Refactoring of My Account 1 stage"** — Миграция Forex MyAccount с JavaScript на TypeScript. Родительская задача для нового проекта `myaccount-front` на Next.js.
- Активные дочерние задачи (январь-февраль 2026):
  - CRM-42609 — Рефакторинг deposit в TypeScript
  - CRM-42608 — Удаление старого модуля account status
  - CRM-42589 — Рефакторинг theme в TypeScript
  - CRM-42579 — Рефакторинг onboarding page
  - CRM-42569 — Рефакторинг withdrawal модулей (закрыт)
  - CRM-42568 — Рефакторинг глобальных компонентов
  - CRM-42567 — Рефакторинг глобальных модальных окон
  - CRM-42553 — Обновление complete-registration формы
  - CRM-42503 — Рефакторинг сервисов

- **"How to run MyAccount for Development"** (page 2128249411) — Гайд по запуску `myaccount-front-react-forex`
- **"[CRM-30416] Forex My Account Front-end integration with PAMM service"** (page 3733423049) — Оценка PAMM-интеграции (5 дней)

### 2.6 Архитектура API

- **Public API** — Регистрация, логин/логофф, управление паролями (page 1416298819)
- **Affiliates API** — Отчёты по клиентам/транзакциям/сделкам для партнёров (page 30809190)
- **CRM API** — Внутренние операции бэкофиса (page 1457225862)
- **Internal API** — Сервис-к-сервису коммуникация (page 1457520772)
- **Report API** — Отчёты для партнёров (page 1401159852)
- **MyAccount API** — Клиентский API (документирован по фичам)

### 2.7 Регуляторная отчётность

- **"Settings for ST Forex Reports (Regulatory Reports)"** (page 3498475601) — Отчёты для белорусского регулятора
- **"F7003 (Forex Orders Regulatory Report)"** (page 1924989313) — Формат регуляторного отчёта

---

## 3. PLANNING CONTEXT

### 3.1 Активные работы (январь-февраль 2026)

**Рефакторинг фронтенда (ВЫСОКИЙ ПРИОРИТЕТ):**
- CRM-42322 — Этап 1 рефакторинга Forex MyAccount в TypeScript/Next.js
- 10+ активных технических задач по модульной миграции

**AMS Forex & BO (В РАБОТЕ):**
- MISS-5315 / CRM-42545 — Внедрение AMS для forex + BO
- CRM-42546 — CRM-42549 — Отдельные типы сделок (CPA, PnL RevShare, Volume RevShare, Hybrid)

**Баг-фиксы (свежие):**
- CRM-42612 — Отсутствующие переводы/локализация
- CRM-42605 — Баг настроек onboarding
- CRM-42600/42602 — Проблемы с login-by-client для менеджеров
- CRM-42594 — Промо-параметр не отправляется при депозите

### 3.2 Завершённые работы (2025)

- Множество новых платёжных интеграций: PayCly, pspARK, AvePay, TransactaPayOK, Epayme, T365, BetPayRabbit, Connpay, SnelPay, Jenapay, Paymaxis
- Развитие PropTrading (бесплатные челленджи, оплата картой, Stripe)
- DatasetPros.ai (эволюция PropTrading)
- Кастомизации Gracexfx MyAccount
- Onboarding поток 1Prime

### 3.3 Инфраструктура окружений и тестирования

- **QA1** — Forex Development
- **QA2** — Forex Feature
- **QA3** — Forex Release
- **maxi-uat** — UAT Forex
- Smoke-тест планы: "Trading Smoke Forex" (page 3536945356), "Patch_Smoke Forex Trading" (page 1470464222), "Patch_Smoke for Forex Payments" (page 3505586331)
- Автотесты: тег `@forex` для разделения сценариев
- TeamCity: запуски по воскресеньям для CRM-MaxiMarkets (~5 часов)

### 3.4 Ключевые архитектурные решения

1. **Forex — основной тип инстанса** — фичи разрабатываются forex-first, затем адаптируются
2. **MyAccount переписывается** на TypeScript/Next.js (текущий проект `myaccount-front`)
3. **Торговые платформы Windows-зависимы** — DTS = .NET Framework, MT4 = unmanaged DLLs
4. **Forex и Crypto сосуществуют** — через внутренние переводы
5. **PropTrading — отдельный тип окружения** на базе MT5

---

## 4. INVENTORY SUMMARY

### Confluence: 50+ forex-specific страниц

| Категория | Кол-во | Ключевые Page IDs |
|-----------|--------|-------------------|
| Forex MyAccount UI/UX | 12+ | 3982721069, 3982032963, 3981967404, 1470234879, 1084293261 |
| Интеграции торговых платформ | 15+ | 2424177197, 3052109940, 2398290045, 4234346497, 4386979859 |
| Платёжные системы | 10+ | 3415244850, 3431956505, 3879207021, 291864588 |
| Регистрация и Auth | 5+ | 1416397053, 3314680459, 3315008131 |
| AMS (Партнёрка) | 6+ | 3399713409, 6231457797, 5982388226, 5982748680 |
| Тестирование и QA | 8+ | 3536945356, 1470464222, 3505586331, 3875045410 |
| Конфигурация и настройки | 5+ | 3018883739, 4290674805, 2329739675, 2331115617 |
| Архитектура и окружения | 5+ | 29048916, 4202004481, 4014211097 |

### Jira: 100+ forex-related тикетов

| Проект | Всего | Распределение статусов |
|--------|-------|----------------------|
| CRM | 50+ | Open, In Progress, Closed |
| MISS | 50+ | To be implemented, In Progress, Closed |
| WDA | ~15 | Тестирование/автоматизация |
| WB | ~20 | Деплой/релиз WebApp |
| YA | ~20 | Контент/SEO для forex-сайтов |

### Ключевые Jira-тикеты

| Key | Title | Status | Значимость |
|-----|-------|--------|-----------|
| CRM-42322 | [Forex][Front]Refactoring of My Account 1 stage | In Development | **Ключевая инициатива** для `myaccount-front` |
| MISS-5223 | [1Prime][AMS 1.0] AMS Forex & BO | To be implemented | Крупная AMS фича |
| MISS-4556 | [CRM] Forex & BO trading within the same instance | — | Стратегический эпик конвергенции |
| CRM-34056 | [MyAccount][Forex] refactoring front min/max validation | — | Рефакторинг фронтенда |
| MISS-4799 | [Levels][AMS 1.0] New DealType PnL Revshare for Forex | To be implemented | Расширение AMS |
