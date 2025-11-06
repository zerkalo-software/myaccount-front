# High-Performance React 19 Code with Next.js: Complete Pattern Guide

This document is a comprehensive guide to developing high-performance web applications using React 19 and Next.js, covering modern optimization patterns, architectural solutions, and best practices for 2025.

## State Management Integration

For client-side state management, use **Zustand with useShallow** for optimal performance. See [Frontend State Management](./architecture/frontend-state-management.md) for complete patterns.

```typescript
import { useShallow } from 'zustand/react/shallow'

// Multiple properties - 60% fewer re-renders
const { user, settings } = useStore(
  useShallow((s) => ({ user: s.user, settings: s.settings }))
)
```

Server state (API data) uses Server Components or Server Actions (see section 2).

## 1. Automatic Optimization with React Compiler

### 1.1 Compiler Principles

React 19 introduces the revolutionary **React Compiler** — a static analysis tool that automatically optimizes components through compilation-time memoization. The compiler transforms React code into optimized JavaScript, significantly improving performance without manual developer intervention.

**Key Capabilities:**
- Automatic dependency tracking for components
- Compilation-level memoization
- Elimination of manual `React.memo`, `useMemo`, and `useCallback` usage
- Granular value memoization, including conditional memoization



### 1.3 The "use memo" Directive

To explicitly instruct the compiler to optimize a specific function, use the `"use memo"` directive:

```javascript
function OptimizedComponent() {
  "use memo";
  // Compiler automatically optimizes this component
  return <div>...</div>;
}
```

**Compilation Modes:**
- `annotation` — only functions with `"use memo"` directive are optimized
- `infer` — compiler uses heuristics to determine which components to optimize
- `all` — everything is optimized by default

## 2. Server Components and Rendering Optimization

### 2.1 Server Components Architecture

**React Server Components (RSC)** execute exclusively on the server, providing direct access to server resources without shipping code to the client.

**Advantages:**
- Zero JavaScript on the client for server components
- Simplified data fetching without client-side state management
- Improved performance through reduced bundle size
- Direct access to databases and APIs

### 2.2 Combining Server and Client Components

```javascript
// ServerComponent.js (server component)
import ClientButton from './ClientButton';

async function ProductList() {
  const products = await db.products.findMany();
  
  return (
    <div>
      {products.map(product => (
        <div key={product.id}>
          <h3>{product.name}</h3>
          <ClientButton productId={product.id} />
        </div>
      ))}
    </div>
  );
}
```

```javascript
// ClientButton.js (client component)
'use client';

export default function ClientButton({ productId }) {
  return <button onClick={() => addToCart(productId)}>Add to Cart</button>;
}
```

### 2.3 Server Actions

Server Actions eliminate the need for creating separate API endpoints for data mutations:

```javascript
// actions.js
'use server';

export async function updateProfile(formData) {
  const name = formData.get('name');
  const email = formData.get('email');
  
  await db.user.update({
    where: { id: userId },
    data: { name, email }
  });
}
```

```javascript
// ClientForm.js
'use client';
import { updateProfile } from './actions';

export default function ProfileForm() {
  return (
    <form action={updateProfile}>
      <input name="name" />
      <input name="email" />
      <button type="submit">Save</button>
    </form>
  );
}
```

## 3. Next.js Rendering Strategies

### 3.1 Static Site Generation (SSG)

Generate static pages at build time for maximum performance:

```javascript
export async function generateStaticParams() {
  const posts = await fetchPosts();
  return posts.map(post => ({ id: post.id.toString() }));
}

export const dynamic = "force-static";

export default function BlogPost({ params }) {
  return <article>...</article>;
}
```

### 3.2 Incremental Static Regeneration (ISR)

ISR combines the speed of static generation with the freshness of dynamic data:

```javascript
export async function getStaticProps({ params }) {
  const post = await fetchPost(params.id);
  
  return {
    props: { post },
    revalidate: 60, // Regenerate every 60 seconds
  };
}

export async function getStaticPaths() {
  const posts = await fetchPosts();
  
  return {
    paths: posts.map(post => ({ params: { id: post.id } })),
    fallback: 'blocking',
  };
}
```

**On-Demand ISR** allows programmatic cache invalidation:

```javascript
// pages/api/revalidate.js
export default async function handler(req, res) {
  if (req.query.secret !== process.env.MY_SECRET_TOKEN) {
    return res.status(401).json({ message: 'Invalid token' });
  }
  
  try {
    await res.revalidate('/posts/1');
    return res.json({ revalidated: true });
  } catch (err) {
    return res.status(500).json({ message: 'Error revalidating' });
  }
}
```

### 3.3 Streaming SSR with Suspense

React 18/19 enables HTML streaming and selective hydration:

```javascript
import { Suspense } from 'react';

export default async function Page() {
  return (
    <div>
      <Header />
      <Suspense fallback={<LoadingSkeleton />}>
        <Comments />
      </Suspense>
      <Footer />
    </div>
  );
}

async function Comments() {
  const comments = await fetchComments(); // Slow request
  return <CommentList comments={comments} />;
}
```

**Advantages:**
- HTML is sent to the client before all data is loaded
- Hydration begins before all JavaScript is loaded
- Prioritizes hydration for components the user interacts with

## 4. Concurrent Features and Interactivity Optimization

### 4.1 useTransition for Non-Blocking Updates

`useTransition` allows marking updates as non-critical, preventing UI blocking:

```javascript
import { useTransition } from 'react';

function TabContainer() {
  const [isPending, startTransition] = useTransition();
  const [tab, setTab] = useState('home');
  
  function selectTab(nextTab) {
    startTransition(() => {
      setTab(nextTab); // Non-critical update
    });
  }
  
  return (
    <div>
      <TabButton onClick={() => selectTab('projects')} 
                 isLoading={isPending}>
        Projects
      </TabButton>
      {tab === 'projects' && <HeavyProjectsComponent />}
    </div>
  );
}
```

**Async Transitions in React 19:**

```javascript
function TabButton({ action }) {
  const [isPending, startTransition] = useTransition();
  
  return (
    <button onClick={() => {
      startTransition(async () => {
        await action(); // Async server operation
      });
    }}>
      {isPending ? 'Loading...' : 'Submit'}
    </button>
  );
}
```

### 4.2 Automatic Batching

React 19 automatically groups multiple state updates into a single render:

```javascript
function UpdateComponent() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState('');
  
  function handleUpdate() {
    setCount(c => c + 1);
    setName('Updated');
    // Both updates are batched in a single render
  }
  
  return <button onClick={handleUpdate}>Update</button>;
}
```

## 5. Code Splitting and Lazy Loading

### 5.1 Dynamic Imports with React.lazy

```javascript
import { lazy, Suspense } from 'react';

const HeavyChart = lazy(() => import('./HeavyChart'));

export default function Dashboard() {
  return (
    <Suspense fallback={<div>Loading chart...</div>}>
      <HeavyChart />
    </Suspense>
  );
}
```

### 5.2 Next.js Dynamic Imports

```javascript
import dynamic from 'next/dynamic';

const DynamicComponent = dynamic(() => import('../components/Chart'), {
  loading: () => <p>Loading...</p>,
  ssr: false, // Disable SSR for this component
});

export default function Page() {
  return <DynamicComponent />;
}
```

### 5.3 Route-based Code Splitting

Route-based splitting is automatic in Next.js App Router:

```javascript
// app/dashboard/page.js
import dynamic from 'next/dynamic';

const AdminTools = dynamic(() => import('@/components/AdminTools'));

export default function DashboardPage() {
  return (
    <div>
      <h1>Dashboard</h1>
      <AdminTools />
    </div>
  );
}
```

## 6. Image Optimization

### 6.1 Next.js Image Component

The `next/image` component automatically optimizes images:

```javascript
import Image from 'next/image';

export default function ProductImage() {
  return (
    <Image
      src="/product.jpg"
      alt="Product"
      width={800}
      height={600}
      priority // For above-the-fold images
      quality={85}
      placeholder="blur"
      blurDataURL="data:image/jpeg;base64,..."
    />
  );
}
```

**Best Practices:**
- Always specify `width` and `height` to prevent Layout Shift
- Use `priority` for critical images (above-the-fold)
- Apply modern formats (WebP, AVIF) automatically
- Implement lazy loading for below-the-fold images

### 6.2 Responsive Images

```javascript
<Image
  src="/hero.jpg"
  alt="Hero"
  fill
  sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
  style={{ objectFit: 'cover' }}
/>
```

## 7. Memoization and Render Optimization

### 7.1 React.memo

**Note**: With React Compiler (enabled in your config), manual memoization is often unnecessary. Use sparingly.

Use `React.memo` to prevent unnecessary re-renders:

```javascript
import { memo } from 'react';

const ExpensiveComponent = memo(function ExpensiveComponent({ data }) {
  // Component only re-renders when data changes
  return <div>{data.map(item => <Item key={item.id} {...item} />)}</div>;
});
```

**When to Use React.memo:**
- Component re-renders frequently with identical props
- Component rendering is computationally expensive
- Component is a pure function

**When NOT to Use:**
- Props change constantly
- Component is lightweight and renders quickly
- Adds complexity without measurable benefit

### 7.2 useMemo for Expensive Computations

```javascript
import { useMemo } from 'react';

function DataTable({ items, filter }) {
  const filteredItems = useMemo(() => {
    return items.filter(item => item.category === filter);
  }, [items, filter]);
  
  return <table>{filteredItems.map(renderRow)}</table>;
}
```

### 7.3 useCallback for Function Stability

```javascript
import { useCallback, memo } from 'react';

const ChildComponent = memo(({ onClick }) => {
  return <button onClick={onClick}>Click</button>;
});

function ParentComponent() {
  const handleClick = useCallback(() => {
    console.log('Clicked');
  }, []); // Function maintains reference between renders
  
  return <ChildComponent onClick={handleClick} />;
}
```

## 8. Next.js Caching Strategies

### 8.1 Caching Layers

Next.js provides multiple caching layers:

| Layer | Description | Storage | Duration |
|-------|-------------|---------|----------|
| Request Memoization | Request deduplication | Server | Single request |
| Data Cache | Data caching | Server | Persistent (revalidatable) |
| Full Route Cache | HTML and RSC payload | Server | Persistent (revalidatable) |
| Router Cache | RSC Payload | Client | User session |

### 8.2 Revalidation Strategies

**Time-based Revalidation:**

```javascript
// Revalidate every 3600 seconds (1 hour)
fetch('https://api.example.com/data', {
  next: { revalidate: 3600 }
});
```

**On-Demand Revalidation:**

```javascript
import { revalidatePath, revalidateTag } from 'next/cache';

export async function action() {
  'use server';
  
  // Revalidate specific path
  revalidatePath('/posts');
  
  // Revalidate by tag
  revalidateTag('posts');
}
```

### 8.3 Cache Control Headers

```javascript
export async function GET() {
  return new Response(JSON.stringify({ data }), {
    headers: {
      'Cache-Control': 'public, max-age=3600, stale-while-revalidate=59',
    },
  });
}
```

## 9. Middleware and Edge Functions

### 9.1 Next.js Middleware

Middleware executes on Edge Runtime before each request:

```javascript
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  const isAuthenticated = checkAuth(request);
  
  if (!isAuthenticated && request.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', request.url));
  }
  
  return NextResponse.next();
}

export const config = {
  matcher: ['/dashboard/:path*', '/api/:path*'],
};
```

**Use Cases for Middleware:**
- Authentication and authorization
- Internationalization (i18n)
- A/B testing
- URL rewriting and redirects
- Feature flags

### 9.2 Edge Functions

```javascript
// app/api/edge-function/route.ts
export const runtime = 'edge';

export async function GET(request: Request) {
  const userLocation = request.headers.get('x-vercel-ip-country');
  
  return new Response(
    JSON.stringify({ location: userLocation }),
    { headers: { 'content-type': 'application/json' } }
  );
}
```

## 10. Error Handling

### 10.1 Error Boundaries

```javascript
'use client';

import { Component } from 'react';

class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }
  
  static getDerivedStateFromError(error) {
    return { hasError: true };
  }
  
  componentDidCatch(error, errorInfo) {
    console.error('Error caught:', error, errorInfo);
    // Send to logging service
  }
  
  render() {
    if (this.state.hasError) {
      return <h2>Something went wrong.</h2>;
    }
    
    return this.props.children;
  }
}
```

### 10.2 Using react-error-boundary

```javascript
import { ErrorBoundary } from 'react-error-boundary';

function ErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div role="alert">
      <p>An error occurred:</p>
      <pre>{error.message}</pre>
      <button onClick={resetErrorBoundary}>Try again</button>
    </div>
  );
}

export default function App() {
  return (
    <ErrorBoundary
      FallbackComponent={ErrorFallback}
      onReset={() => {
        // Reset application state
      }}
    >
      <MainApp />
    </ErrorBoundary>
  );
}
```

**Best Practices:**
- Wrap independent application sections in separate Error Boundaries
- Provide clear fallback UI
- Log errors to monitoring systems
- Don't wrap the entire application in a single Error Boundary

## 11. Core Web Vitals Optimization

### 11.1 Largest Contentful Paint (LCP)

**Optimize LCP (target: < 2.5s):**

- Use `priority` for critical images
- Preload important resources
- Minimize CSS and JavaScript
- Apply effective caching policies
- Use CDN for static resources

```javascript
import { preload } from 'react-dom';

function App() {
  useEffect(() => {
    preload('/critical-script.js', { as: 'script' });
  }, []);
  
  return <Image src="/hero.jpg" priority />;
}
```

### 11.2 Interaction to Next Paint (INP)

**Optimize INP (target: < 200ms):**

- Use `useTransition` for non-critical updates
- Apply debouncing/throttling for frequent events
- Break up long JavaScript tasks
- Use code splitting and tree shaking
- Minimize input delay

```javascript
import { useTransition } from 'react';

function SearchComponent() {
  const [isPending, startTransition] = useTransition();
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  
  function handleSearch(value) {
    setQuery(value); // Synchronous input update
    startTransition(() => {
      setResults(performHeavySearch(value)); // Non-critical update
    });
  }
  
  return (
    <div>
      <input value={query} onChange={e => handleSearch(e.target.value)} />
      {isPending ? <Spinner /> : <Results data={results} />}
    </div>
  );
}
```

### 11.3 Cumulative Layout Shift (CLS)

**Optimize CLS (target: < 0.1):**

- Set explicit dimensions for images and videos
- Use `font-display: swap` for fonts
- Reserve space for dynamic content
- Avoid inserting content above existing content
- Use CSS `transform` instead of properties that cause layout

```javascript
// Correct
<Image 
  src="/banner.jpg" 
  width={1200} 
  height={400} 
  alt="Banner"
/>

// Incorrect - will cause layout shift
<img src="/banner.jpg" alt="Banner" />
```

## 12. Bundle Size Optimization

### 12.1 Bundle Analysis

```bash
pnpm add -D @next/bundle-analyzer

# In next.config.js
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
});

module.exports = withBundleAnalyzer({
  // ... your configuration
});

# Run analysis
ANALYZE=true pnpm run build
```

### 12.2 Tree Shaking and Import Optimization

**Optimize Lodash:**

```javascript
// Bad - imports entire Lodash
import _ from 'lodash';

// Good - imports only needed function
import debounce from 'lodash/debounce';
```

### 12.3 Performance Budget

```json
// package.json
{
  "size-limit": [
    {
      "path": ".next/static/chunks/**",
      "limit": "1024 KB"
    }
  ]
}
```

## 13. Best Practices

### 13.1 Architectural Principles

1. **Use Server Components by default** — client components only where interactivity is needed
2. **Data colocation** — load data as close as possible to where it's used
3. **Composition over inheritance** — prefer component composition
4. **Separation of concerns** — clear boundaries between server and client logic

### 13.2 Performance

1. **Measure before optimizing** — use React Profiler, Lighthouse, Chrome DevTools
2. **Optimize the critical path** — prioritize above-the-fold content
3. **Progressive loading** — use Suspense and streaming for gradual loading
4. **Cache aggressively** — utilize all Next.js caching layers

### 13.3 Developer Experience

4. **ESLint** — configure rules for performance
5. **Git hooks** — automate checks before commits
6. **Monitoring** — integrate Real User Monitoring (RUM)

## Related Documentation

- [Frontend State Management](./architecture/frontend-state-management.md) - Zustand stores, useShallow, multiple stores pattern
- [Frontend Data Flow](./architecture/frontend-data-flow.md) - Unidirectional data flow, cross-store communication
- [Frontend Type System](./architecture/frontend-type-system.md) - TypeScript patterns, type safety
- [Frontend Error Handling](./architecture/frontend-error-handling.md) - Error boundaries, error state, retry patterns

---

**Status**: Active Standard (2025)
**Last Updated**: 2025-11-06
