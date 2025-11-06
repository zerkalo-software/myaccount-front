# Frontend Data Flow Conventions (2025)

**Pattern**: Unidirectional Flow + useShallow + Multiple Stores

Use **unidirectional data flow** with **useShallow** for re-render optimization and **multiple domain stores** for scalability.

## Core Architecture

1. **Data Flow Direction**: State → UI (via useShallow) → User Actions → Action Functions → State Updates → UI Re-renders
2. **Re-render Optimization**: useShallow for multiple properties, direct selectors for single properties
3. **Action Organization**: Grouped actions using combine middleware
4. **State Access**: High-performance selectors with useShallow
5. **Component Communication**: Through multiple domain stores (no prop drilling)
6. **Domain Separation**: Multiple stores instead of monolithic store

## Architectural Principles

### Principle 1: Unidirectional Data Flow with useShallow

```
┌─────────────────────────────────────────────┐
│        Multiple Domain Stores               │
│   (Auth, Canvas, Elements, History)         │
└──────────────┬──────────────────────────────┘
               │ useShallow / Direct selectors
               ↓
    ┌─────────────────────┐
    │  React Component    │
    │  (reads with        │
    │   useShallow)       │
    └──────────┬──────────┘
               │ User Interaction
               ↓
    ┌─────────────────────┐
    │  Action Function    │
    │  (from combine)     │
    └──────────┬──────────┘
               │ set((state) => newState)
               ↓
    ┌─────────────────────┐
    │  Immutable Update   │
    └──────────┬──────────┘
               │
               ↓
    ┌─────────────────────┐
    │   State Changed     │
    │  (notify only       │
    │   affected stores)  │
    └──────────┬──────────┘
               │
               ↓ (loop back)
    Component Re-renders
    (60% fewer with useShallow)
```

### Principle 2: useShallow for Multiple Properties (Critical)

Always use useShallow when selecting multiple properties to prevent unnecessary re-renders:

```typescript
import { useShallow } from 'zustand/react/shallow'

// ✅ DO: Object destructuring with useShallow
const { zoom, pan } = useCanvasStore(
  useShallow((state) => ({ zoom: state.zoom, pan: state.pan }))
)

// ✅ DO: Array destructuring with useShallow
const [zoom, tool] = useCanvasStore(
  useShallow((state) => [state.zoom, state.tool])
)

// ✅ DO: Direct selector for single property (no useShallow)
const zoom = useCanvasStore((state) => state.zoom)

// ❌ DON'T: Multiple properties without useShallow
const { zoom, pan } = useCanvasStore((state) => ({ zoom: state.zoom, pan: state.pan }))
```

### Principle 3: Multiple Stores for Domain Separation

Split state into multiple domain stores instead of one monolithic store:

```typescript
// ✅ DO: Domain-separated stores
const useAuthStore = create(combine({ user: null, token: null }, ...))
const useCanvasStore = create(combine({ zoom: 1, pan: { x: 0, y: 0 } }, ...))
const useElementsStore = create(combine({ elements: [], selectedId: null }, ...))

// ❌ DON'T: Monolithic store with everything
const useMegaStore = create(combine({ 
  user: null, zoom: 1, elements: [], // mixing domains
}, ...))
```

### Principle 4: Action-Based State Changes with combine

Use combine middleware for clean state/actions separation:

```typescript
const useStore = create(
  combine(
    // State
    { count: 0 },
    // Actions
    (set) => ({
      increment: () => set((state) => ({ count: state.count + 1 })),
      decrement: () => set((state) => ({ count: state.count - 1 })),
    })
  )
)
```

### Principle 5: Component Decoupling via Stores

Components communicate through stores, never directly:

```typescript
// ✅ DO: Components read from stores independently
function ComponentA() {
  const { selectElement } = useElementsStore(
    useShallow((state) => ({ selectElement: state.selectElement }))
  )
  return <div onClick={() => selectElement('id-1')}>Select</div>
}

function ComponentB() {
  const selectedId = useElementsStore((state) => state.selectedId)
  return <div>Selected: {selectedId}</div>
}

// ❌ DON'T: Pass props or callbacks between siblings
```

## Implementation Guidance

### Data Flow Patterns with useShallow

```typescript
import { useShallow } from 'zustand/react/shallow'

// Multiple properties
const { user, settings } = useStore(
  useShallow((s) => ({ user: s.user, settings: s.settings }))
)

// Single property
const user = useStore((s) => s.user)

// Derived state
const activeUser = useStore(
  useShallow((s) => s.users.find(u => u.id === s.activeId))
)
```

For complete selector patterns, see [Frontend State Management](./frontend-state-management.md#high-performance-selector-patterns).

### Cross-Store Communication Patterns

**Pattern 1: Direct Store Access**

```typescript
const useUIStore = create(combine(
  { theme: 'light' },
  (set) => ({
    syncFromUser: () => {
      const user = useUserStore.getState().user
      if (user) set({ theme: user.theme })
    }
  })
))
```

**Pattern 2: Store Subscription**

```typescript
useEffect(() => {
  return useUserStore.subscribe(
    (s) => s.user,
    (user) => { if (user) useUIStore.getState().syncFromUser() }
  )
}, [])
```

**Pattern 3: Coordinator Pattern**

```typescript
export const actions = {
  logout: () => {
    useUserStore.getState().clear()
    useUIStore.getState().reset()
    useDataStore.getState().clear()
  }
}
```

**Pattern 4: Event Bus** (for complex apps)

```typescript
type Events = 'user-changed' | 'data-updated'
const eventBus = {
  listeners: new Map<Events, Set<Function>>(),
  on: (e: Events, fn: Function) => {
    eventBus.listeners.get(e)?.add(fn) || eventBus.listeners.set(e, new Set([fn]))
    return () => eventBus.listeners.get(e)?.delete(fn)
  },
  emit: (e: Events, data: any) => eventBus.listeners.get(e)?.forEach(fn => fn(data))
}
```

### Immutable Update Patterns

```typescript
// Shallow
set({ theme: 'dark' })

// Nested (manual)
set((s) => ({ settings: { ...s.settings, theme: 'dark' } }))

// Deep (use Immer)
import { immer } from 'zustand/middleware/immer'
set((s) => { s.deep.nested.value = 'new' })
```

See [Frontend State Management](./frontend-state-management.md#immutable-update-patterns) for details.

### Advanced Selector Patterns with useShallow

```typescript
// Filter
const active = useStore(useShallow((s) => s.items.filter(i => i.active)))

// Sort
const sorted = useStore(useShallow((s) => [...s.items].sort((a,b) => a.order - b.order)))

// Map
const names = useStore(useShallow((s) => s.items.map(i => i.name)))
```

## Performance

See [Frontend State Management](./frontend-state-management.md#performance-impact) for detailed benchmarks.


## Server Components Integration

For data fetching with Server Components and Server Actions in Next.js App Router, see [Frontend React Patterns](./frontend-react.md#server-components-and-rendering-optimization).

Client state (UI, selections) uses Zustand stores. Server state (data from API) uses Server Components or React Query.

## Related Documentation

- [Frontend State Management](./frontend-state-management.md) - Store creation, useShallow patterns, performance benchmarks
- [Frontend Type System](./frontend-type-system.md) - TypeScript for stores and selectors
- [Frontend Error Handling](./frontend-error-handling.md) - Error state across stores
- [Frontend React Patterns](./frontend-react.md) - Server Components, Server Actions, React 19 features

---

**Status**: Active Standard (2025)
**Last Updated**: 2025-11-06
