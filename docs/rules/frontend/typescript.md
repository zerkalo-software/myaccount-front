# TypeScript Development Rules (Frontend)

## Prerequisites

- React 19+, Next.js 15+, TypeScript 5+ (strict mode), Node.js 18+, pnpm
- tsconfig: `strict: true`, `noImplicitAny: true`, `strictNullChecks: true`, `noUncheckedIndexedAccess: true`
- Tools: ESLint (Next.js config), Biome (planned), Vitest (planned)

## Basic Principles

✅ **Aggressive Refactoring** - Prevent technical debt and maintain health
❌ **Unused "Just in Case" Code** - Violates YAGNI principle (Kent Beck)

## Comment Writing Rules
- **Function Description Focus**: Describe what the code "does"
- **No Historical Information**: Do not record development history
- **Timeless**: Write only content that remains valid whenever read
- **Conciseness**: Keep explanations to necessary minimum

## Type Safety

**Absolute Rule**: any type is completely prohibited. It disables type checking and becomes a source of runtime errors.

**any Type Alternatives (Priority Order)**
1. **unknown Type + Type Guards**: Use for validating external input (API responses, localStorage, URL parameters)
2. **Generics**: When type flexibility is needed
3. **Union Types・Intersection Types**: Combinations of multiple types
4. **Type Assertions (Last Resort)**: Only when type is certain

**Type Guard Implementation Pattern**
```typescript
function isUser(value: unknown): value is User {
  return typeof value === 'object' && value !== null && 'id' in value && 'name' in value
}
```

**Modern Type Features**
- **satisfies Operator**: `const config = { apiUrl: '/api' } satisfies Config` - Preserves inference
- **const Assertion**: `const ROUTES = { HOME: '/' } as const satisfies Routes` - Immutable and type-safe
- **Branded Types**: `type UserId = string & { __brand: 'UserId' }` - Distinguish meaning
- **Template Literal Types**: `type EventName = \`on\${Capitalize<string>}\`` - Express string patterns with types

**Type Safety in Frontend Implementation**
- **React Props/State**: TypeScript manages types, unknown unnecessary
- **External API Responses**: Always receive as `unknown`, validate with type guards
- **localStorage/sessionStorage**: Treat as `unknown`, validate
- **URL Parameters**: Treat as `unknown`, validate
- **Form Input (Controlled Components)**: Type-safe with React synthetic events

**Type Safety in Data Flow**
- **Frontend → Backend**: Props/State (Type Guaranteed) → API Request (Serialization)
- **Backend → Frontend**: API Response (`unknown`) → Type Guard → State (Type Guaranteed)

**Type Complexity Management**
- **Props Design**:
  - Props count: 3-7 props ideal
  - Optional Props: 50% or less
  - Nesting: Up to 2 levels (flatten deeper structures)
- Type Assertions: Review design if used 3+ times

**Component Splitting Triggers** (if ANY met → split):
- Props count ≥ 10
- Optional props > 50%
- File > 300 lines
- Render time > 16ms (React DevTools)

**Strategies** (priority order):
1. Presentational/container (if business logic exists)
2. Atoms/molecules (if duplication exists)
3. Separate optionals (if optional props > 50%)
4. Composition with children (if wrapper pattern)

**External API Type Conversion**:

Process: Receive as `unknown` → Validate with Zod → Convert to strict internal types

```typescript
// Pattern
const api: unknown = await fetch('/api').then(r => r.json())
const validated = APISchema.safeParse(api)
if (!validated.success) return { ok: false, error: validated.error }
return { ok: true, value: toInternalType(validated.data) }
```

| Conversion Type | Rule |
|----------------|------|
| Optional → Required | Provide default or generate |
| Loose → Strict | Validate with Zod + convert |
| any → Specific | Parse + type-guard |

## Coding Conventions

**Component Design Criteria**
- **Function Components (Mandatory)**: Official React recommendation, optimizable by modern tooling
- **Classes Prohibited**: Class components completely deprecated (Exception: Error Boundary)
- **Custom Hooks**: Standard pattern for logic reuse

**Function Design**
- **0-2 parameters maximum**: Use object for 3+ parameters
  ```typescript
  // ✅ Object parameter
  function createUser({ name, email, role }: CreateUserParams) {}
  ```

**Props Design (Props-driven Approach)**
- Props are the interface: Define all necessary information as props
- Avoid implicit dependencies: Do not depend on global state or context without necessity
- Type-safe: Always define Props type explicitly

**Environment Variables**
- **Use Next.js environment variable system**:
  - Client: `process.env.NEXT_PUBLIC_*` (statically inlined at build)
  - Server (Server Components/Actions): `process.env.*` (private)
- **No secrets on client-side**: All frontend code is public, manage secrets in backend

**Dependency Injection**
- **Custom Hooks for dependency injection**: Inject dependencies as parameters (not hard-coded imports)

**Testability Requirements**:

1. **Inject dependencies** (not hard-coded imports)
   ```typescript
   // ✅ function useData(fetcher: Fetcher)
   // ❌ import { fetcher } from './api'
   ```

2. **Coverage**: Custom hooks ≥ 65%, must test mocks + loading/error states

3. **Performance**: Unit test < 100ms, integration < 500ms

See frontend/typescript-testing.md for patterns.

**Asynchronous Processing**
- Promise Handling: Always use `async/await`
- Error Handling: Always handle with `try-catch` or Error Boundary
- Type Definition: Explicitly define return value types (e.g., `Promise<Result>`)

**Format Rules**
- Semicolon omission (follow Biome settings)
- Types in `PascalCase`, variables/functions in `camelCase`
- Imports use absolute paths (`src/`)

**Clean Code Principles**
- ✅ Delete unused code immediately
- ✅ Delete debug `console.log()`
- ❌ Commented-out code (manage history with version control)
- ✅ Comments explain "why" (not "what")

## Error Handling

**Absolute Rule**: Error suppression prohibited. All errors must have log output and proper handling.

**Error Handling Requirements**:

Required elements:
1. Log: error.name, error.message, error.stack, timestamp, context
2. State update: Set error message + isLoading = false (async ops)
3. Propagation: Error Boundary (components), state + optional throw (hooks), always throw (utils)
4. User message: Friendly message + actionable step

See architecture/frontend-error-handling.md for full patterns.

**Fail-Fast Principle**: Fail quickly on errors to prevent continued processing in invalid states
```typescript
// ❌ Prohibited: Unconditional fallback
catch (error) {
  return defaultValue // Hides error
}

// ✅ Required: Explicit failure
catch (error) {
  logger.error('Processing failed', error)
  throw error // Handle with Error Boundary or higher layer
}
```

**Result Type Pattern**: Express errors with types for explicit handling
```typescript
type Result<T, E> = { ok: true; value: T } | { ok: false; error: E }

// Example: Express error possibility with types
function parseUser(data: unknown): Result<User, ValidationError> {
  if (!isValid(data)) return { ok: false, error: new ValidationError() }
  return { ok: true, value: data as User }
}
```

**Custom Error Classes**
```typescript
export class AppError extends Error {
  constructor(message: string, public readonly code: string, public readonly statusCode = 500) {
    super(message)
    this.name = this.constructor.name
  }
}
// Purpose-specific: ValidationError(400), ApiError(502), NotFoundError(404)
```

**Layer-Specific Error Handling (React)**
- Error Boundary: Catch React component errors, display fallback UI
- Custom Hook: Detect business rule violations, propagate AppError as-is
- API Layer: Convert fetch errors to domain errors

**Structured Logging and Sensitive Information Protection**
Never include sensitive information (password, token, apiKey, secret, creditCard) in logs

**Asynchronous Error Handling in React**
- Error Boundary setup mandatory: Catch rendering errors
- Use try-catch with all async/await in event handlers
- Always log and re-throw errors or display error state

## Refactoring Techniques

**Basic Policy**
- Small Steps: Maintain always-working state through gradual improvements
- Safe Changes: Minimize the scope of changes at once
- Behavior Guarantee: Ensure existing behavior remains unchanged while proceeding

**Implementation Procedure**: Understand Current State → Gradual Changes → Behavior Verification → Final Validation

**Priority**: Duplicate Code Removal > Large Function Division > Complex Conditional Branch Simplification > Type Safety Improvement

## Performance Optimization

- React Compiler First: Compiler handles memoization; add `React.memo`/`useMemo`/`useCallback` only for measured hotspots
- Lazy Loading: Use dynamic import/React.lazy and Suspense for code splitting
- Bundle Size: Monitor with `pnpm run build` and keep under 500KB

**State Structure Optimization**:

| Pattern | Trigger | Re-render Impact |
|---------|---------|------------------|
| Separate useState | 1-3 independent values | Minimal |
| Single object useState | 4+ related values | Moderate |
| Zustand + useShallow | Shared across 3+ components | Optimized (60% fewer) |
| Context | Deep drilling (3+ levels) | All consumers |
| useReducer | Complex state (5+ actions) | Controlled |

Thresholds: Render < 16ms, re-renders < 10/interaction. See architecture/frontend-state-management.md.

