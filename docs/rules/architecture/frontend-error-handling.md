# Frontend Error Handling Standards

**Pattern**: Try-Catch + Typed Errors + Error State + User Feedback

Use **try-catch blocks** with **typed error handling**, **error state management**, and **user-facing error UI** for all error scenarios.

## Core Architecture

1. **Try-Catch Blocks**: Wrap all async operations, external calls, and risky operations
2. **Error State Management**: Track error state in Zustand store (per-slice error state)
3. **Error Typing**: Use `Error` type, `instanceof Error` checks, custom error classes
4. **User Feedback**: Display error messages, loading states, retry buttons
5. **Error Boundaries**: Wrap React components for rendering error handling
6. **Logging Strategy**: Console logging with prefixed tags (`[Service]`, `[Component]`)
7. **Environment Handling**: Detailed errors in development, user-friendly in production
8. **Recovery Mechanisms**: Retry buttons, reset actions, fallback UI

## Architectural Principles

### Principle 1: Try-Catch at Boundaries
- Wrap all async operations (API calls, file I/O, storage access)
- Wrap external library calls that may throw
- Wrap JSON parsing, URL validation, complex computations
- Don't wrap simple synchronous operations (getters, setters)

### Principle 2: Error State Management

```typescript
State Structure:
{
  api: {
    error: string | null,           // General error
    isLoading: boolean,              // Loading state
    isSaving: boolean,               // Saving state
    autoSaveError: string | null,    // Separate auto-save error
  }
}

Action Pattern:
1. Set loading state + clear previous error
2. Try operation
3. On success: Update state + clear error
4. On failure: Set error state + update UI
```

### Principle 3: Typed Error Handling
- Use `error instanceof Error` to narrow type
- Extract error message: `error instanceof Error ? error.message : 'Fallback message'`
- Create custom error classes for specific domains
- Include error codes for programmatic handling

### Principle 4: User Feedback Flow

```
Error Occurs
  → Catch in try-catch
  → Update error state in store
  → UI displays error message
  → Provide recovery action (retry, reset, dismiss)
  → Log error for debugging
```

### Principle 5: Error Boundaries for Component Errors
- Wrap feature components in error boundaries
- Provide fallback UI with error details (development only)
- Offer retry/reset button to recover
- Log error with `componentDidCatch` for monitoring
- Pass errors to parent via `onError` callback

### Principle 6: Logging Best Practices
- Prefix logs with component/service name: `[API Slice]`, `[Persistence]`
- Use log levels: `console.error()` for errors, `console.warn()` for warnings
- Log errors with context (operation name, parameters)
- Don't log sensitive data (passwords, tokens, personal information)
- Consider error reporting service in production (Sentry, Rollbar)

### Principle 7: Graceful Degradation
- Non-critical errors should not block UI (e.g., persistence failure)
- Provide safe defaults (empty string, null, default value)
- Continue execution when possible (log warning, degrade feature)
- Critical errors should prevent invalid state (throw, update error state)

## Implementation Guidance

### Try-Catch Pattern for Async Operations (2025)

**Pattern 1: Error Handling with combine Middleware**

```typescript
import { create } from 'zustand'
import { combine } from 'zustand/middleware'

// ✅ DO: Use combine for clean error state management
export const useDataStore = create(
  combine(
    // State
    {
      data: null as MyData | null,
      isLoading: false,
      error: null as string | null,
    },
    // Actions
    (set, get) => ({
      saveData: async (data: MyData) => {
        // 1. Set loading state + clear error
        set({ isLoading: true, error: null })

        try {
          // 2. Perform async operation
          const result = await apiCall(data)

          // 3. Check result
          if (!result.success) {
            throw new Error(result.error.message)
          }

          // 4. Update state on success
          set({
            data: result.data,
            isLoading: false,
            error: null,
          })
        } catch (error) {
          // 5. Extract error message
          const errorMessage =
            error instanceof Error ? error.message : 'Failed to save data'

          // 6. Update error state
          set({
            isLoading: false,
            error: errorMessage,
          })

          // 7. Re-throw for caller handling (optional)
          throw error
        }
      },
      
      clearError: () => set({ error: null }),
      reset: () => set({ data: null, isLoading: false, error: null }),
    })
  )
)

// Usage in component with useShallow
import { useShallow } from 'zustand/react/shallow'

function MyComponent() {
  const { data, isLoading, error, saveData } = useDataStore(
    useShallow((state) => ({
      data: state.data,
      isLoading: state.isLoading,
      error: state.error,
      saveData: state.saveData,
    }))
  )

  if (error) return <ErrorMessage message={error} />
  if (isLoading) return <Loading />
  return <div>{data?.name}</div>
}
```

**Pattern 2: Error Handling Across Multiple Stores**

```typescript
// Auth store with error handling
export const useAuthStore = create(
  combine(
    {
      user: null as User | null,
      token: null as string | null,
      error: null as string | null,
      isLoading: false,
    },
    (set) => ({
      login: async (email: string, password: string) => {
        set({ isLoading: true, error: null })

        try {
          const { user, token } = await api.login(email, password)
          set({ user, token, isLoading: false, error: null })
          
          // Initialize other stores on successful login
          useCanvasStore.getState().reset()
          useElementsStore.getState().loadUserData()
        } catch (error) {
          const errorMessage = error instanceof Error ? error.message : 'Login failed'
          set({ isLoading: false, error: errorMessage })
          
          // Clear other stores on error
          useCanvasStore.getState().reset()
          useElementsStore.getState().clearData()
        }
      },
      
      logout: () => {
        set({ user: null, token: null, error: null })
        // Clear all stores on logout
        useCanvasStore.getState().reset()
        useElementsStore.getState().clearData()
      },
      
      clearError: () => set({ error: null }),
    })
  )
)

// Elements store with error handling
export const useElementsStore = create(
  combine(
    {
      elements: [] as Element[],
      isLoading: false,
      error: null as string | null,
    },
    (set) => ({
      loadUserData: async () => {
        // Check auth first (cross-store dependency)
        const { token } = useAuthStore.getState()
        if (!token) {
          set({ error: 'Not authenticated' })
          return
        }

        set({ isLoading: true, error: null })

        try {
          const elements = await api.getElements(token)
          set({ elements, isLoading: false, error: null })
        } catch (error) {
          const errorMessage = error instanceof Error ? error.message : 'Failed to load elements'
          set({ isLoading: false, error: errorMessage })
        }
      },
      
      clearData: () => set({ elements: [], error: null }),
      clearError: () => set({ error: null }),
    })
  )
)
```

**Pattern 2: Non-Critical Operation with Graceful Degradation**

```typescript
// Storage access (non-critical)
try {
  const storedValue = localStorage.getItem('key');
  if (storedValue) {
    return JSON.parse(storedValue);
  }
} catch (error) {
  // Log warning (don't block UI)
  console.warn('[Storage] Failed to load data:', error);
  // Return safe default
  return null;
}
```

**Pattern 3: Validation with Try-Catch**

```typescript
export function validateUrl(url: string): boolean {
  try {
    const parsed = new URL(url); // May throw
    return ['http:', 'https:'].includes(parsed.protocol);
  } catch {
    return false; // Safe default on error
  }
}
```

### Error State Management (2025)

**State Structure with combine:**

```typescript
import { create } from 'zustand'
import { combine } from 'zustand/middleware'

// ✅ DO: Use combine for clean error state
export const useMyFeatureStore = create(
  combine(
    // State (with defaults)
    {
      data: null as MyData | null,
      isLoading: false,
      isSaving: false,
      error: null as string | null,
      saveError: null as string | null,
    },
    // Actions
    (set) => ({
      clearError: () => set({ error: null }),
      clearSaveError: () => set({ saveError: null }),
      clearAllErrors: () => set({ error: null, saveError: null }),
      reset: () => set({ 
        data: null, 
        isLoading: false, 
        isSaving: false, 
        error: null, 
        saveError: null 
      }),
    })
  )
)

// Usage with useShallow
import { useShallow } from 'zustand/react/shallow'

function ErrorDisplay() {
  const { error, saveError, clearError, clearSaveError } = useMyFeatureStore(
    useShallow((state) => ({
      error: state.error,
      saveError: state.saveError,
      clearError: state.clearError,
      clearSaveError: state.clearSaveError,
    }))
  )

  return (
    <>
      {error && <ErrorBanner message={error} onDismiss={clearError} />}
      {saveError && <SaveErrorToast message={saveError} onDismiss={clearSaveError} />}
    </>
  )
}
```

**Multiple Stores Error Coordination:**

```typescript
// Coordinator for cross-store error handling
export const errorCoordinator = {
  // Clear all errors across all stores
  clearAllErrors: () => {
    useAuthStore.getState().clearError()
    useCanvasStore.getState().clearError()
    useElementsStore.getState().clearError()
  },
  
  // Reset all stores (used on logout or critical errors)
  resetAllStores: () => {
    useAuthStore.getState().reset()
    useCanvasStore.getState().reset()
    useElementsStore.getState().reset()
  },
  
  // Collect all errors from all stores
  getAllErrors: () => {
    return {
      auth: useAuthStore.getState().error,
      canvas: useCanvasStore.getState().error,
      elements: useElementsStore.getState().error,
    }
  },
}
```

### Custom Error Classes

```typescript
export class ApiError extends Error {
  constructor(public code: string, message: string, public status?: number) {
    super(message)
    this.name = 'ApiError'
  }
}

// Usage
throw new ApiError('AUTH_FAILED', 'Auth failed', 401)

// Handle
catch (e) {
  if (e instanceof ApiError && e.code === 'AUTH_FAILED') {
    // redirect to login
  }
}
```

### React Error Boundaries

For React Error Boundary implementation, see [Frontend React Patterns](./frontend-react.md#error-handling).

```typescript
// Usage
<ErrorBoundary fallback={<ErrorUI />}>
  <MyFeatureComponent />
</ErrorBoundary>
```

### Error Hook Pattern

**Error Hook Pattern:**

```typescript
export const useErrorHandler = () => {
  const [error, setError] = useState<Error | null>(null)
  const [isRetrying, setIsRetrying] = useState(false)

  const retry = async (fn: () => Promise<void>) => {
    setIsRetrying(true)
    try {
      await fn()
      setError(null)
    } catch (err) {
      setError(err as Error)
    } finally {
      setIsRetrying(false)
    }
  }

  return { error, isRetrying, setError, retry }
}
```

### Exponential Backoff Retry Pattern

**Use Case**: Automatically retry failed operations with increasing delays to handle transient errors (network issues, rate limits, temporary server errors).

**Problem**: Simple immediate retries can:
- Overwhelm failing services (thundering herd)
- Waste resources on repeated failures
- Hit rate limits faster
- Not give time for transient issues to resolve

**Solution**: Exponential backoff with jitter and configurable retry policies.

**Implementation**:

```typescript
interface RetryOptions {
  maxRetries: number;           // Maximum retry attempts (default: 3)
  initialDelayMs: number;       // Initial delay in milliseconds (default: 1000)
  maxDelayMs: number;           // Maximum delay cap (default: 30000)
  backoffMultiplier: number;    // Delay multiplier per retry (default: 2)
  jitter: boolean;              // Add randomness to prevent thundering herd (default: true)
  shouldRetry?: (error: Error, attempt: number) => boolean;  // Custom retry condition
}

export async function retryWithBackoff<T>(
  fn: () => Promise<T>,
  options: Partial<RetryOptions> = {}
): Promise<T> {
  const {
    maxRetries = 3,
    initialDelayMs = 1000,
    maxDelayMs = 30000,
    backoffMultiplier = 2,
    jitter = true,
    shouldRetry = () => true,
  } = options;

  let lastError: Error;

  for (let attempt = 0; attempt <= maxRetries; attempt++) {
    try {
      return await fn();
    } catch (error) {
      lastError = error as Error;

      // Don't retry on last attempt
      if (attempt === maxRetries) {
        throw lastError;
      }

      // Check custom retry condition
      if (!shouldRetry(lastError, attempt)) {
        throw lastError;
      }

      // Calculate delay with exponential backoff
      const exponentialDelay = Math.min(
        initialDelayMs * Math.pow(backoffMultiplier, attempt),
        maxDelayMs
      );

      // Add jitter (random ±25%) to prevent thundering herd
      const delay = jitter
        ? exponentialDelay * (0.75 + Math.random() * 0.5)
        : exponentialDelay;

      console.warn(
        `[Retry] Attempt ${attempt + 1}/${maxRetries} failed. Retrying in ${Math.round(delay)}ms...`,
        { error: lastError.message }
      );

      // Wait before next retry
      await new Promise((resolve) => setTimeout(resolve, delay));
    }
  }

  throw lastError!;
}
```

**Usage Pattern 1: Simple Retry**

```typescript
// Retry API call with default options
async function fetchUserData(userId: string) {
  return retryWithBackoff(
    () => fetch(`/api/users/${userId}`).then(res => res.json())
  );
}

// Retry behavior:
// Attempt 1: Immediate
// Attempt 2: Wait ~1000ms (0.75-1.25s with jitter)
// Attempt 3: Wait ~2000ms (1.5-2.5s with jitter)
// Attempt 4: Wait ~4000ms (3-5s with jitter)
```

**Usage Pattern 2: Custom Retry Condition**

```typescript
// Only retry on network errors, not validation errors
async function saveData(data: MyData) {
  return retryWithBackoff(
    async () => {
      const response = await fetch('/api/save', {
        method: 'POST',
        body: JSON.stringify(data),
      });

      if (!response.ok) {
        const error = await response.json();
        throw new Error(error.message);
      }

      return response.json();
    },
    {
      maxRetries: 5,
      shouldRetry: (error, attempt) => {
        // Don't retry client errors (400-499)
        if (error.message.includes('400') || error.message.includes('Invalid')) {
          return false;
        }

        // Retry network errors and server errors (500-599)
        return true;
      },
    }
  );
}
```

**Usage Pattern 3: Integration with Zustand Action**

```typescript
// Zustand action with exponential backoff retry
saveDataWithRetry: async (data: MyData) => {
  const state = get();

  set({
    myFeature: {
      ...state.myFeature,
      isLoading: true,
      error: null,
      retryCount: 0,
    }
  });

  try {
    const result = await retryWithBackoff(
      () => apiCall(data),
      {
        maxRetries: 3,
        initialDelayMs: 1000,
        shouldRetry: (error, attempt) => {
          // Update retry count in state
          set({
            myFeature: {
              ...get().myFeature,
              retryCount: attempt + 1,
            }
          });

          // Only retry on network/server errors
          return !error.message.includes('validation');
        },
      }
    );

    // Success
    set({
      myFeature: {
        ...state.myFeature,
        data: result.data,
        isLoading: false,
        error: null,
        retryCount: 0,
      },
    });
  } catch (error) {
    // All retries exhausted
    const errorMessage =
      error instanceof Error ? error.message : 'Failed to save data';

    set({
      myFeature: {
        ...state.myFeature,
        isLoading: false,
        error: errorMessage,
      },
    });

    throw error;
  }
}
```

**Retry Decision Matrix**:

| Error Type | Retry? | Max Retries | Initial Delay | Reason |
|------------|--------|-------------|---------------|--------|
| Network timeout | ✅ Yes | 3-5 | 1000ms | Transient network issue |
| Server error (500-599) | ✅ Yes | 3 | 1000ms | Server might recover |
| Rate limit (429) | ✅ Yes | 3 | 5000ms | Need longer delay |
| Authentication error (401) | ❌ No | 0 | N/A | User action required |
| Validation error (400) | ❌ No | 0 | N/A | Bad request data |
| Not found (404) | ❌ No | 0 | N/A | Resource doesn't exist |

**Configurable Retry Policies**:

```typescript
// Conservative: Small API with low load
const CONSERVATIVE_POLICY: RetryOptions = {
  maxRetries: 2,
  initialDelayMs: 500,
  maxDelayMs: 5000,
  backoffMultiplier: 2,
  jitter: true,
};

// Standard: Default for most operations
const STANDARD_POLICY: RetryOptions = {
  maxRetries: 3,
  initialDelayMs: 1000,
  maxDelayMs: 30000,
  backoffMultiplier: 2,
  jitter: true,
};

// Aggressive: Critical operations, robust service
const AGGRESSIVE_POLICY: RetryOptions = {
  maxRetries: 5,
  initialDelayMs: 2000,
  maxDelayMs: 60000,
  backoffMultiplier: 2,
  jitter: true,
};

// Usage
await retryWithBackoff(() => criticalOperation(), AGGRESSIVE_POLICY);
```

**Testing Retry Logic**:

```typescript
describe('retryWithBackoff', () => {
  it('should succeed on first attempt', async () => {
    const fn = jest.fn().mockResolvedValue('success');
    const result = await retryWithBackoff(fn);

    expect(result).toBe('success');
    expect(fn).toHaveBeenCalledTimes(1);
  });

  it('should retry on failure and eventually succeed', async () => {
    const fn = jest
      .fn()
      .mockRejectedValueOnce(new Error('Fail 1'))
      .mockRejectedValueOnce(new Error('Fail 2'))
      .mockResolvedValue('success');

    const result = await retryWithBackoff(fn, { maxRetries: 3 });

    expect(result).toBe('success');
    expect(fn).toHaveBeenCalledTimes(3);
  });

  it('should throw after max retries exhausted', async () => {
    const fn = jest.fn().mockRejectedValue(new Error('Always fails'));

    await expect(
      retryWithBackoff(fn, { maxRetries: 2, initialDelayMs: 10 })
    ).rejects.toThrow('Always fails');

    expect(fn).toHaveBeenCalledTimes(3); // Initial + 2 retries
  });

  it('should respect shouldRetry condition', async () => {
    const fn = jest.fn().mockRejectedValue(new Error('validation error'));

    await expect(
      retryWithBackoff(fn, {
        maxRetries: 3,
        shouldRetry: (error) => !error.message.includes('validation'),
      })
    ).rejects.toThrow('validation error');

    expect(fn).toHaveBeenCalledTimes(1); // No retries
  });
});
```

**Performance Characteristics**:

| Scenario | Total Time (3 retries) | Network Requests | Success Rate |
|----------|------------------------|------------------|--------------|
| Immediate success | 50ms | 1 | 100% |
| Success on retry 2 | ~1050ms | 2 | 100% |
| Success on retry 3 | ~3050ms | 3 | 100% |
| All retries fail | ~7050ms | 4 | 0% (throws) |

**When to Use**:
- ✅ Network requests (fetch, WebSocket)
- ✅ External API calls
- ✅ Auto-save operations
- ✅ Background sync operations
- ❌ User-initiated actions (show manual retry button instead)
- ❌ Real-time operations (use exponential backoff for reconnection)

### Production Error Monitoring with Sentry

**Use Case**: Track and debug production errors with stack traces, breadcrumbs, and user context.

**Installation**:

```bash
pnpm add @sentry/nextjs --filter @litskills/frontend
```

**Configuration**:

```typescript
// apps/app/sentry.client.config.ts
import * as Sentry from '@sentry/nextjs';

Sentry.init({
  dsn: process.env.NEXT_PUBLIC_SENTRY_DSN,

  // Environment
  environment: process.env.NODE_ENV,
  enabled: process.env.NODE_ENV === 'production',

  // Performance Monitoring
  tracesSampleRate: 0.1, // 10% of transactions

  // Session Replay
  replaysOnErrorSampleRate: 1.0, // 100% of errors
  replaysSessionSampleRate: 0.01, // 1% of sessions

  // Filter sensitive data
  beforeSend(event, hint) {
    // Remove sensitive headers
    if (event.request?.headers) {
      delete event.request.headers['authorization'];
      delete event.request.headers['cookie'];
    }

    // Sanitize user data
    if (event.user?.email) {
      event.user.email = event.user.email.replace(/(.{3}).*(@.*)/, '$1***$2');
    }

    return event;
  },

  // Ignore common errors
  ignoreErrors: [
    'ResizeObserver loop limit exceeded',
    'Non-Error promise rejection captured',
  ],
});
```

**Error Boundary with Sentry**:

```typescript
// In Error Boundary componentDidCatch
Sentry.captureException(error, {
  contexts: { react: { componentStack: errorInfo.componentStack } },
  tags: { errorBoundary: 'FeatureName' }
})
```

**Store Action Integration (2025)**:

```typescript
// Store with Sentry error tracking using combine
export const useDataStore = create(
  combine(
    {
      data: null as MyData | null,
      isLoading: false,
      error: null as string | null,
    },
    (set, get) => ({
      saveData: async (data: MyData) => {
        // Set loading state
        set({ isLoading: true, error: null })

        try {
          const result = await apiCall(data)

          if (!result.success) {
            throw new Error(result.error.message)
          }

          // Update state on success
          set({
            data: result.data,
            isLoading: false,
            error: null,
          })
        } catch (error) {
          const errorMessage =
            error instanceof Error ? error.message : 'Failed to save data'

          // Capture error in Sentry with breadcrumbs
          Sentry.captureException(error, {
            tags: {
              action: 'saveData',
              store: 'dataStore',
            },
            extra: {
              data,
              currentState: get(),  // Get current state
            },
          })

          // Update error state
          set({
            isLoading: false,
            error: errorMessage,
          })

          throw error
        }
      },
    })
  )
)
```

**Breadcrumb Tracking**:

```typescript
// Track user actions as breadcrumbs
function handleUserAction(action: string, data: any) {
  Sentry.addBreadcrumb({
    category: 'user-action',
    message: action,
    level: 'info',
    data,
  });
}

// Usage
handleUserAction('element-selected', { elementId: 'el-123', type: 'image' });
handleUserAction('zoom-changed', { from: 1.0, to: 1.5 });
```

**User Context**:

```typescript
// Set user context after authentication
Sentry.setUser({
  id: user.id,
  email: user.email, // Will be sanitized by beforeSend
  username: user.name,
});

// Clear user context on logout
Sentry.setUser(null);
```

**Environment Variables** (`.env.local`):

```bash
# Sentry Configuration
NEXT_PUBLIC_SENTRY_DSN=https://your-project-id@sentry.io/your-app-id
SENTRY_AUTH_TOKEN=your-auth-token  # For uploading source maps
```

**Benefits**:
- **Stack Traces**: Full source-mapped stack traces in production
- **Breadcrumbs**: User action history leading up to error
- **User Context**: Identify affected users and patterns
- **Session Replay**: Watch user session when error occurred
- **Performance Monitoring**: Identify slow operations
- **Alerts**: Notify team of critical errors via Slack/email

**When to Use**:
- ✅ Production environments (enabled via environment check)
- ✅ Critical errors (API failures, state corruption)
- ✅ Unexpected errors (caught by error boundaries)
- ❌ Expected validation errors (user input errors)
- ❌ Development environment (use console.error)

### Logging Best Practices

**Prefixed Logging:**

```typescript
// Prefix with service/component name
console.log('[API Slice] Story saved successfully:', { storyId });
console.warn('[Persistence] Failed to load state from storage:', error);
console.error('[Auto-Save] Failed to auto-save story:', errorMessage);
```

**Log Levels:**

```typescript
// Error: Critical issues that need attention
console.error('[Service] Failed to save:', error);

// Warn: Non-critical issues, degraded functionality
console.warn('[Storage] Local storage unavailable, using memory');

// Info: Important events
console.log('[Auth] User logged in:', userId);

// Debug: Detailed debugging info (development only)
if (process.env.NODE_ENV === 'development') {
  console.debug('[State] Canvas zoom updated:', zoom);
}
```

**Avoid Logging Sensitive Data:**

```typescript
// ❌ DON'T: Log sensitive data
console.log('[Auth] Login attempt:', { email, password });

// ✅ DO: Log sanitized data
console.log('[Auth] Login attempt:', { email: email.substring(0, 3) + '***' });
```

### User-Facing Error Messages

**Error Message Guidelines:**

```typescript
// ❌ DON'T: Use technical error messages
error: 'ECONNREFUSED: Connection refused at 127.0.0.1:3000'

// ✅ DO: Use user-friendly messages
error: 'Unable to connect to server. Please check your internet connection and try again.'

// ❌ DON'T: Use generic messages
error: 'An error occurred'

// ✅ DO: Provide specific, actionable messages
error: 'Failed to upload image. Please ensure the file is less than 5MB and try again.'
```

**Error Message Mapping:**

```typescript
const ERROR_MESSAGES: Record<string, string> = {
  ECONNREFUSED: 'Unable to connect to server. Please check your internet connection.',
  ETIMEDOUT: 'Request timed out. Please try again.',
  AUTH_FAILED: 'Invalid credentials. Please check your email and password.',
  FILE_TOO_LARGE: 'File size exceeds 5MB. Please choose a smaller file.',
};

function getUserFriendlyMessage(error: Error): string {
  // Check for known error codes
  if (error instanceof ApiError && ERROR_MESSAGES[error.code]) {
    return ERROR_MESSAGES[error.code];
  }

  // Fallback to generic message
  return 'Something went wrong. Please try again.';
}
```

### Testing Error Handling (2025)

**Test Error State with combine:**

```typescript
import { renderHook, act } from '@testing-library/react'
import { useDataStore } from './data-store'

describe('Error Handling with combine', () => {
  beforeEach(() => {
    // Reset store before each test
    useDataStore.setState({ data: null, isLoading: false, error: null })
  })

  it('should set error state when operation fails', async () => {
    // Mock API to fail
    mockApi.getData.mockRejectedValue(new Error('API Error'))

    // Call action
    await act(async () => {
      try {
        await useDataStore.getState().loadData()
      } catch {
        // Expected to throw
      }
    })

    // Verify error state
    const state = useDataStore.getState()
    expect(state.error).toBe('API Error')
    expect(state.isLoading).toBe(false)
  })

  it('should clear error on retry', async () => {
    // Set error state
    act(() => {
      useDataStore.setState({ error: 'Previous error' })
    })

    // Mock API to succeed
    mockApi.getData.mockResolvedValue({ data: 'success' })

    // Retry operation
    await act(async () => {
      await useDataStore.getState().loadData()
    })

    // Verify error cleared
    expect(useDataStore.getState().error).toBeNull()
  })

  it('should handle clearError action', () => {
    // Set error
    act(() => {
      useDataStore.setState({ error: 'Test error' })
    })

    // Clear error
    act(() => {
      useDataStore.getState().clearError()
    })

    expect(useDataStore.getState().error).toBeNull()
  })
})
```

**Test Cross-Store Error Handling:**

```typescript
import { useAuthStore } from './auth-store'
import { useElementsStore } from './elements-store'
import { errorCoordinator } from './error-coordinator'

describe('Cross-Store Error Handling', () => {
  beforeEach(() => {
    // Reset all stores
    useAuthStore.setState({ user: null, token: null, error: null })
    useElementsStore.setState({ elements: [], error: null })
  })

  it('should clear errors across all stores', () => {
    // Set errors in multiple stores
    act(() => {
      useAuthStore.setState({ error: 'Auth error' })
      useElementsStore.setState({ error: 'Elements error' })
    })

    // Clear all errors
    act(() => {
      errorCoordinator.clearAllErrors()
    })

    // Verify all errors cleared
    expect(useAuthStore.getState().error).toBeNull()
    expect(useElementsStore.getState().error).toBeNull()
  })

  it('should handle auth error and clear dependent stores', async () => {
    mockApi.login.mockRejectedValue(new Error('Invalid credentials'))

    await act(async () => {
      try {
        await useAuthStore.getState().login('test@test.com', 'password')
      } catch {
        // Expected to throw
      }
    })

    // Auth store has error
    expect(useAuthStore.getState().error).toBe('Invalid credentials')

    // Dependent stores were cleared
    expect(useElementsStore.getState().elements).toEqual([])
  })

  it('should collect errors from all stores', () => {
    act(() => {
      useAuthStore.setState({ error: 'Auth error' })
      useElementsStore.setState({ error: 'Elements error' })
    })

    const allErrors = errorCoordinator.getAllErrors()

    expect(allErrors).toEqual({
      auth: 'Auth error',
      canvas: null,
      elements: 'Elements error',
    })
  })
})
```

## Benefits

**User Experience:**
- Clear error messages help users understand what went wrong
- Recovery options (retry buttons) allow users to fix issues
- Loading states provide feedback during operations
- Development details aid developer debugging

**Developer Experience:**
- Console logs with prefixed tags aid debugging
- Try-catch blocks capture operation context
- TypeScript narrows error types for safe handling
- Testable error state

**Robustness:**
- Errors caught at boundaries don't crash entire app
- Graceful degradation for non-critical errors
- Error boundaries prevent component crashes
- Recovery mechanisms reduce support tickets

## Risks and Mitigations

**Risk: Inconsistent Error Handling Across Codebase**
- **Mitigation**: Code review checklist, ESLint rule for try-catch on async functions, refactoring sprint, documentation

**Risk: Error Messages Not User-Friendly**
- **Mitigation**: Error message guidelines, message mapping, code review, UX review

**Risk: Missing Recovery Mechanisms**
- **Mitigation**: Error UI checklist, reusable error components, code review, user testing

**Risk: Error State Not Cleared After Recovery**
- **Mitigation**: Clear error pattern, testing, code review

**Risk: Over-Logging in Production**
- **Mitigation**: Conditional logging (`NODE_ENV` checks), log levels, error reporting service, strip logs in production build

## Related Documentation

- [Frontend State Management](./frontend-state-management.md) - Error state with combine middleware
- [Frontend Data Flow](./frontend-data-flow.md) - Error state in cross-store communication
- [Frontend Type System](./frontend-type-system.md) - Type-safe error handling
- [Frontend React Patterns](./frontend-react.md) - Error Boundaries implementation

---

**Status**: Active Standard
**Last Updated**: 2025-11-06
