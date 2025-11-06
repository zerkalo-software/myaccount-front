# Frontend Type System Standards

**Pattern**: Strict TypeScript + Runtime Validation + Type Guards

Use **TypeScript strict mode** with **runtime validation** and **type guards** for comprehensive type safety across frontend code.

## Core Architecture

1. **TypeScript Configuration**: Strict mode enabled with all strict flags
2. **Runtime Validation**: Zod schemas for API responses, user input, and external data
3. **Type Guards**: Custom guards for discriminated unions and complex types
4. **Type Definitions**: Centralized types in `@litskills/types` library
5. **Validation Strategy**: Validate at boundaries (API, user input, storage)

## Architectural Principles

### Principle 1: Strict TypeScript Configuration
- Enable all strict flags: `strict: true`, `noUncheckedIndexedAccess: true`
- Disallow implicit any: `noImplicitAny: true`
- Strict null checks: `strictNullChecks: true`
- Module resolution: `"bundler"` for frontend, `"nodenext"` for Node.js

### Principle 2: Runtime Validation at Boundaries
- Validate all external data: API responses, localStorage, user input
- Use Zod for schema definition and validation
- Parse and validate before using data in application
- Handle validation errors gracefully with user feedback

### Principle 3: Type Guards for Discriminated Unions
- Define discriminated unions with `type` property
- Create type guard functions using `is` type predicate
- TypeScript narrows types within guard blocks
- Use guards for safe property access

### Principle 4: Centralized Type Definitions
- Core types in `@litskills/types` library
- Feature-specific types colocated with features
- Export all public types for consumer usage
- Use branded types for IDs and special values

### Principle 5: Avoid `any` and `unknown`
- Replace `any` with proper types or generic constraints
- Use `unknown` for truly unknown data, then validate
- Type assertion only after validation
- Prefer union types over broad types

## Implementation Guidance

### Zustand Store Type Patterns (2025)

For basic store patterns with `combine` middleware, see [Frontend State Management](./frontend-state-management.md).

**Advanced: StateCreator for Slice Patterns**

```typescript
import { create, StateCreator } from 'zustand'

type UserSlice = { user: User | null; setUser: (u: User) => void }
type UISlice = { theme: string; setTheme: (t: string) => void }

const createUserSlice: StateCreator<
  UserSlice & UISlice,  // Combined
  [],                   // Middleware
  [],                   // Out mutators
  UserSlice            // This slice
> = (set) => ({
  user: null,
  setUser: (user) => set({ user })
})

const useStore = create<UserSlice & UISlice>()((...a) => ({
  ...createUserSlice(...a),
  ...createUISlice(...a),
}))
```

### Strict TypeScript Configuration


### Runtime Validation with Zod

**Pattern 1: Define Schema**

```typescript
import { z } from 'zod';

export const StoryDraftSchema = z.object({
  id: z.string().uuid(),
  title: z.string().min(1),
  content: z.object({
    screens: z.array(
      z.object({
        id: z.string().uuid(),
        elements: z.array(z.unknown()), // Validated separately
      })
    ),
  }),
  createdAt: z.string().datetime(),
  updatedAt: z.string().datetime(),
});

export type StoryDraft = z.infer<typeof StoryDraftSchema>;
```

**Pattern 3: Validate User Input**

```typescript
const UserInputSchema = z.object({
  title: z.string().min(1, 'Title is required').max(100, 'Title too long'),
  description: z.string().max(500, 'Description too long').optional(),
});

function handleSubmit(formData: unknown) {
  const result = UserInputSchema.safeParse(formData);

  if (!result.success) {
    // Show validation errors to user
    const errors = result.error.flatten().fieldErrors;
    setErrors(errors);
    return;
  }

  // Type-safe data
  const validData = result.data;
  submitData(validData);
}
```

### Type Guards for Discriminated Unions

**Pattern 1: Define Discriminated Union**

```typescript
export type StoryElement =
  | {
      type: 'image';
      id: string;
      src: string;
      alt?: string;
    }
  | {
      type: 'video';
      id: string;
      src: string;
      poster?: string;
    }
  | {
      type: 'text';
      id: string;
      content: string;
      fontSize: number;
    };
```

**Pattern 2: Create Type Guard**

```typescript
export function isImageElement(element: StoryElement): element is Extract<StoryElement, { type: 'image' }> {
  return element.type === 'image';
}

export function isVideoElement(element: StoryElement): element is Extract<StoryElement, { type: 'video' }> {
  return element.type === 'video';
}

export function isTextElement(element: StoryElement): element is Extract<StoryElement, { type: 'text' }> {
  return element.type === 'text';
}
```

**Pattern 3: Use Type Guard**

```typescript
function renderElement(element: StoryElement) {
  if (isImageElement(element)) {
    // TypeScript knows element is image
    return <img src={element.src} alt={element.alt} />;
  }

  if (isVideoElement(element)) {
    // TypeScript knows element is video
    return <video src={element.src} poster={element.poster} />;
  }

  if (isTextElement(element)) {
    // TypeScript knows element is text
    return <p style={{ fontSize: element.fontSize }}>{element.content}</p>;
  }

  // Exhaustiveness check
  const _exhaustive: never = element;
  return _exhaustive;
}
```

### Centralized Type Definitions

```typescript
// src/types/index.ts

// Branded types
export type UserId = string & { readonly __brand: 'UserId' }

// Domain types
export interface User {
  id: UserId
  name: string
  email: string
}

// Utility types
export type Result<T> = 
  | { success: true; data: T }
  | { success: false; error: { message: string } }
```

### Avoiding `any` and `unknown`

**❌ DON'T: Use `any`**

```typescript
function handleData(data: any) {
  return data.foo.bar; // No type safety
}
```

**✅ DO: Use Proper Types**

```typescript
interface ExpectedData {
  foo: { bar: string };
}

function handleData(data: ExpectedData) {
  return data.foo.bar; // Type-safe
}
```

**✅ DO: Use `unknown` with Validation**

```typescript
function handleData(data: unknown) {
  // Validate with Zod
  const parsed = ExpectedDataSchema.safeParse(data);
  if (!parsed.success) {
    throw new Error('Invalid data');
  }

  return parsed.data.foo.bar; // Type-safe after validation
}
```

**✅ DO: Use Generic Constraints**

```typescript
function processElement<T extends StoryElement>(element: T): T {
  // Type-safe generic function
  return { ...element, processed: true } as T;
}
```

### Type Assertion Guidelines

**❌ DON'T: Assert Without Validation**

```typescript
const data = await fetch('/api/data').then(res => res.json());
const story = data as Story; // Unsafe!
```

**✅ DO: Validate Before Asserting**

```typescript
const data = await fetch('/api/data').then(res => res.json());
const result = StorySchema.safeParse(data);
if (!result.success) {
  throw new Error('Invalid story data');
}
const story = result.data; // Type-safe
```

**✅ DO: Use Type Guards**

```typescript
function assertIsElement(value: unknown): asserts value is StoryElement {
  const result = StoryElementSchema.safeParse(value);
  if (!result.success) {
    throw new Error('Not a valid element');
  }
}

// Usage
const data: unknown = getDataFromSomewhere();
assertIsElement(data);
// TypeScript now knows data is StoryElement
```

### Testing Type Safety

**Type Test Pattern:**

```typescript
// Use @ts-expect-error to test type errors
describe('Type Safety', () => {
  it('should reject invalid element types', () => {
    const element: StoryElement = {
      type: 'image',
      id: '123',
      src: 'image.jpg',
    };

    // @ts-expect-error - video elements need poster, not alt
    const invalid: StoryElement = {
      type: 'video',
      id: '456',
      alt: 'Invalid',
    };
  });
});
```

### Zod Performance Optimization

| Scenario | Strategy | Improvement |
|----------|----------|-------------|
| List of 100+ items | Use `.pick()` for essential fields only | 16x faster |
| Repeated validation | Cache results | 50x faster (cached) |
| High-frequency (60+ fps) | Skip Zod, use type guards | 100x faster |

```typescript
// Optimize for lists
const ListSchema = FullSchema.pick({ id: true, title: true })

// Cache for repeated validation
const cache = new Map<string, ValidatedType>()

// High-frequency: use type guards instead
function isValid(d: unknown): d is Type {
  return typeof d === 'object' && d !== null && 'id' in d
}
```

## Benefits

**Type Safety:**
- Compile-time error detection prevents runtime bugs
- IDE autocomplete and type hints improve developer productivity
- Refactoring is safe with TypeScript catching breaking changes
- Discriminated unions ensure exhaustive pattern matching

**Runtime Safety:**
- Zod validation catches invalid data at boundaries
- Type guards narrow types safely at runtime
- Validation errors provide actionable feedback to users
- No runtime crashes from unexpected data shapes

**Maintainability:**
- Centralized types provide single source of truth
- Type changes propagate through codebase automatically
- Self-documenting code with type annotations
- Easier onboarding with clear type contracts

## Risks and Mitigations

**Risk: Type Assertions Bypass Type Safety**
- **Mitigation**: Code review for assertions, prefer type guards, validate before asserting, use ESLint rule

**Risk: `any` Escape Hatch Overuse**
- **Mitigation**: ESLint `no-explicit-any` rule, code review checklist, provide proper types, document exceptions

**Risk: Schema and Type Drift**
- **Mitigation**: Co-locate schemas and types, use `z.infer`, automated tests, schema versioning

**Risk: Over-Validation Performance**
- **Mitigation**: Validate at boundaries only, cache validation results, use simpler schemas, profile performance

## Related Documentation

- [Frontend State Management](./frontend-state-management.md) - Store creation with combine, automatic type inference
- [Frontend Data Flow](./frontend-data-flow.md) - Type-safe selectors and cross-store communication
- [Frontend Error Handling](./frontend-error-handling.md) - Type-safe error handling patterns
- [Frontend React Patterns](./frontend-react.md) - TypeScript with Server Components and Actions

---

**Status**: Active Standard
**Last Updated**: 2025-11-06
