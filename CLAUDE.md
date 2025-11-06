# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is **myaccount-front**, a Next.js 16 frontend application for a **forex trading platform** with a modular system designed to support multiple trading flows: **prop trading, forex, crypto, and binary options**. The project uses an AI-first development approach powered by Claude Code with autonomous agents and systematic design workflows.

**Current Status**: Fresh Next.js installation. Business logic, modular trading flows, and component library are not yet implemented.

## Development Commands

**Package Manager**: This project uses **pnpm** exclusively.

```bash
# Development server (http://localhost:3000)
pnpm dev

# Production build
pnpm build

# Start production server
pnpm start

# Lint code
pnpm lint
```

**Planned Quality Commands** (not yet configured):
```bash
pnpm check              # Biome lint + format
pnpm test               # Run tests with Vitest
pnpm test:coverage:fresh # Coverage measurement (60%+ required)
pnpm check:all          # Integrated quality check (all phases)
```

## Architecture

### Core Technologies

- **Next.js 16.0.1** - App Router architecture
- **React 19.2.0** - Function components only (class components deprecated)
- **TypeScript 5** - Strict mode, `any` type completely prohibited
- **Tailwind CSS v4** - PostCSS plugin configuration
- **ESLint 9** - Next.js config (core-web-vitals + TypeScript)

**Planned Testing Stack** (not yet installed):
- **Vitest** - Unit testing framework
- **React Testing Library** - Component testing
- **MSW (Mock Service Worker)** - API mocking
- **Biome** - Code quality (lint + format)

### TypeScript Configuration

- **Path alias**: `@/*` → `./src/*`
- **Target**: ES2017
- **Strict mode**: Enabled
- **Critical Rule**: `any` type is completely prohibited. Use `unknown` + type guards for external data.

### Component Architecture

**Hierarchy**: Atoms → Molecules → Organisms → Templates → Pages

**Mandatory Patterns**:
- Function components only (hooks-based)
- Props-driven design
- Custom hooks for logic reuse and dependency injection
- Co-location: tests and styles alongside components

**Test Coverage Requirements**:
- Atoms: 70%+
- Molecules: 65%+
- Organisms: 60%+
- Custom Hooks: 65%+
- Utils: 70%+

### State Management Strategy

- **Local State**: `useState` for component-specific state
- **Context API**: Theme, auth, and cross-component shared state
- **Custom Hooks**: Encapsulate state logic and side effects
- **Server State**: React Query or SWR for API data caching (planned)

### Data Flow Pattern

```
API Response → Type Validation → State → Props → Render → UI
User Input → Event Handler → State Update → Re-render
```

**Critical**: All API responses must be validated with type guards before use. No `any` type allowed.

### Modular System Architecture

**Planned Trading Flows** (not yet implemented):
- **Prop Trading** - Proprietary trading accounts and challenges
- **Forex** - Foreign exchange trading
- **Crypto** - Cryptocurrency trading
- **Binary Options** - Binary options trading

Each flow will be implemented as a modular system with shared components and flow-specific business logic.

## AI-Driven Development Workflow

This project uses **Claude Code** with autonomous agents for systematic, high-quality implementation.

### Available Slash Commands

```bash
/implement           # Full automation: requirements → design → implementation → commit
/task               # Single task with rule-based precision
/design             # Create design documents only (no implementation)
/plan               # Create work plan from design docs
/build              # Execute implementation from work plan
/review             # Code review and compliance check
/project-inject     # Set project context (interactive)
/sync-rules         # Sync rule metadata
/refine-rule        # Rule improvement assistance
```

### Scale-Based Documentation Requirements

| Scale | Files Changed | Required Documents |
|-------|--------------|-------------------|
| Small | 1-2 files | None (direct implementation) |
| Medium | 3-5 files | Design Doc + Work Plan |
| Large | 6+ files | PRD + ADR + Design Doc + Work Plan |

### When to Create ADRs (Architecture Decision Records)

Create an ADR when making changes to:
- **Type system** (3+ levels nesting or used in 3+ locations)
- **Data flow** (storage method, processing order, passing method)
- **Architecture** (new layer, responsibility changes)
- **External dependencies** (library, framework, or API changes)
- **Complex logic** (3+ states or 5+ async processes)

### Implementation Strategy Selection

Use the **5-phase meta-cognitive framework**:
1. Current State Analysis
2. Strategy Exploration
3. Risk Assessment
4. Constraint Compatibility Check
5. Implementation Approach Decision (vertical/horizontal/hybrid)

**Verification Levels**:
- L1: Functional (operates as end-user feature)
- L2: Test (new tests added and passing)
- L3: Build (no compile errors)

## Quality Assurance

### 6-Step Quality Check Process

1. **Biome Check** - Lint and format validation
2. **TypeScript Build** - Type checking (`pnpm build`)
3. **Unit Tests** - Test execution (`pnpm test`)
4. **Coverage Check** - 60%+ verification
5. **Unused Export Detection**
6. **Integration Tests** - E2E verification

### Core Development Principles

**Do**:
- Use LLM-driven implementation (Claude Code as primary implementer)
- Prioritize quality over speed
- Follow YAGNI (don't implement until necessary)
- Use systematic design (ADR → Design Doc → Work Plan)
- Fail fast (no silent failures or unconditional fallbacks)
- Follow Rule of Three (refactor on 3rd repetition)

**Don't**:
- Use `any` type (use `unknown` + type guards)
- Write similar code 3+ times without refactoring
- Mix multiple responsibilities in single file
- Define same content in multiple files
- Suppress errors without logging
- Leave commented-out code (use version control)
- Think "make it work for now" (implement properly or not at all)

## Project Structure

```
src/app/              # Next.js App Router
  layout.tsx          # Root layout (Geist fonts)
  page.tsx            # Home page
  globals.css         # Tailwind + global styles
  favicon.ico

docs/                 # Documentation system
  rules/              # Development rules & guidelines
    architecture/     # Implementation strategies
    frontend/         # Frontend-specific rules
  guides/             # Development guides
  adr/                # Architecture Decision Records
  design/             # Design documents
  prd/                # Product Requirements Documents
  plans/              # Work plans

.claude/              # Claude Code configuration
  agents/             # 14 sub-agents for autonomous development
  commands/           # 9 slash commands for workflow automation
  settings.json       # Claude permissions/hooks
```

## Important Documentation Files

- `docs/rules/project-context.md` - Project nature and principles
- `docs/rules/technical-spec.md` - Architecture and tech stack
- `docs/rules/frontend/technical-spec.md` - Frontend design rules
- `docs/rules/ai-development-guide.md` - Anti-patterns and debugging
- `docs/rules/architecture/implementation-approach.md` - Strategy selection
- `docs/guides/quickstart.md` - AI development workflow
- `docs/guides/use-cases.md` - Command reference and examples

## Frontend Security Rules

- **No secrets client-side**: Never expose API keys, tokens, or sensitive data in frontend code
- **Environment variables**: Use build tool's environment system (not `process.env` directly)
- **Type validation**: All external data must be validated with type guards
- **Input sanitization**: Sanitize all user inputs to prevent XSS
- **.env files**: Gitignored by default, never commit

## Getting Started with Development

1. **Set project context**: Run `/project-inject` to configure Claude Code with trading platform context
2. **Install testing tools**: Add Vitest, React Testing Library, MSW
3. **Install Biome**: For code quality automation
4. **First feature**: Use `/implement` command with full AI workflow
5. **Define domains**: Create type definitions for trading flows (prop, forex, crypto, binary)
