# Technical Design Rules (Frontend)

## Basic Technology Stack Policy
TypeScript-based React application implementation. Architecture patterns should be selected according to project requirements and scale.

## Environment Variable Management and Security

### Environment Variable Management
- **Use Next.js environment variables**: `process.env.NEXT_PUBLIC_*` values are statically inlined at build and safe for client use
- Centrally manage environment variables through a configuration layer
- Implement proper type safety with TypeScript
- Properly implement default value settings and mandatory checks

```typescript
// ✅ Next.js environment variables (public values only with NEXT_PUBLIC_)
const config = {
  apiUrl: process.env.NEXT_PUBLIC_API_URL ?? 'http://localhost:3000',
  appName: process.env.NEXT_PUBLIC_APP_NAME ?? 'My App',
}

// ❌ Private variables on client
// Use only on the server (Server Components / Server Actions), not in client components
const privateKey = process.env.SECRET_API_KEY
```

### Security (Client-side Constraints)
- **CRITICAL**: All frontend code is public and visible in browser
- **Never store secrets client-side**: No API keys, tokens, or secrets in environment variables
- Do not include `.env` files in Git (same as backend)
- Prohibit logging of sensitive information (passwords, tokens, personal data)
- Do not include sensitive information in error messages

**Correct Approach for Secrets**:
```typescript
// ❌ Security risk: API key exposed in browser
const apiKey = process.env.NEXT_PUBLIC_API_KEY
const response = await fetch(`https://api.example.com/data?key=${apiKey}`)

// ✅ Correct: Backend manages secrets, frontend accesses via proxy
const response = await fetch('/api/data') // Backend handles API key authentication
```

## Architecture Design

### Frontend Architecture Patterns
Strictly adhere to selected project patterns. Project-specific details reference `docs/rules/architecture/`.

**React Component Architecture**:
- **Function Components**: Mandatory, class components deprecated
- **Custom Hooks**: For logic reuse and dependency injection
- **Component Hierarchy**: Atoms → Molecules → Organisms → Templates → Pages
- **Props-driven**: Components receive all necessary data via props
- **Co-location**: Place tests, styles, and related files alongside components

**State Management Patterns**:
- **Local State**: `useState` for component-specific state
- **Context API**: For sharing state across component tree (theme, auth, etc.)
- **Custom Hooks**: Encapsulate state logic and side effects
- **Server State**: React Query or SWR for API data caching

## Unified Data Flow Principles

### Client-side Data Flow
Maintain consistent data flow throughout the React application:

- **Single Source of Truth**: Each piece of state has one authoritative source
  - UI state: Component state or Context
  - Server data: API responses cached in React Query/SWR
  - Form data: Controlled components with React Hook Form

- **Unidirectional Flow**: Data flows top-down via props
  ```
  API Response → State → Props → Render → UI
  User Input → Event Handler → State Update → Re-render
  ```

- **Immutable Updates**: Use immutable patterns for state updates
  ```typescript
  // ✅ Immutable state update
  setUsers(prev => [...prev, newUser])

  // ❌ Mutable state update
  users.push(newUser)
  setUsers(users)
  ```

### Type Safety in Data Flow
- **Frontend → Backend**: Props/State (Type Guaranteed) → API Request (Serialization)
- **Backend → Frontend**: API Response (`unknown`) → Type Guard → State (Type Guaranteed)

```typescript
// ✅ Type-safe data flow
async function fetchUser(id: string): Promise<User> {
  const response = await fetch(`/api/users/${id}`)
  const data: unknown = await response.json()

  if (!isUser(data)) {
    throw new Error('Invalid user data')
  }

  return data // Type guaranteed as User
}
```

## Build and Testing

### Build Commands
```bash
# Development server
pnpm run dev

# Production build
pnpm run build

# Start production build (Next.js)
pnpm run start

```

### Linting
```bash
pnpm run lint
```

### Quality Check Requirements
Quality checks are mandatory upon implementation completion:

**Basic Checks**
```bash
pnpm run lint         # ESLint
pnpm run build        # Next.js build
```

**Optional (if test scripts exist in the future)**
Add test and coverage commands once configured.

### Coverage Requirements
- **Mandatory**: Unit test coverage must be 60% or higher
- **Component-specific targets**:
  - Atoms: 70% or higher
  - Molecules: 65% or higher
  - Organisms: 60% or higher
  - Custom Hooks: 65% or higher
  - Utils: 70% or higher

### Non-functional Requirements
- **Browser Compatibility**: Chrome/Firefox/Safari/Edge (latest 2 versions)
- **Rendering Time**: Within 5 seconds for major pages
