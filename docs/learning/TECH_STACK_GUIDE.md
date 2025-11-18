# AFFiNE Tech Stack Deep Dive

**Documented:** November 2025
**Tech Stack Research:** [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md)
**Prerequisites:** [Architecture Overview](./ARCHITECTURE_OVERVIEW.md)

> **Goal:** Master every technology in the AFFiNE stack with practical examples and best practices.

---

## Table of Contents

**Note:** This is a comprehensive guide. Use the table of contents to jump to specific technologies.

- [Stack Overview](#stack-overview)
- [Frontend Technologies](#frontend-technologies)
  - [React 19](#react-19)
  - [TypeScript 5.7](#typescript-57)
  - [Jotai (State Management)](#jotai-state-management)
  - [React Router](#react-router)
  - [Radix UI](#radix-ui)
  - [Vanilla Extract](#vanilla-extract)
  - [Vite 7](#vite-7)
- [Editor Technologies](#editor-technologies)
  - [Lit (Web Components)](#lit-web-components)
  - [Yjs (CRDT)](#yjs-crdt)
  - [BlockSuite Framework](#blocksuite-framework)
- [Backend Technologies](#backend-technologies)
  - [NestJS 11](#nestjs-11)
  - [Prisma 6](#prisma-6)
  - [GraphQL](#graphql)
  - [Socket.IO](#socketio)
  - [BullMQ](#bullmq)
- [Database & Storage](#database--storage)
  - [PostgreSQL](#postgresql)
  - [Redis](#redis)
  - [IndexedDB](#indexeddb)
- [Native & Performance](#native--performance)
  - [Rust (Edition 2024)](#rust-edition-2024)
  - [NAPI-RS](#napi-rs)
- [Build & Development](#build--development)
  - [Yarn 4 (Berry)](#yarn-4-berry)
  - [Vitest](#vitest)
  - [Playwright](#playwright)
  - [ESLint 9 & Prettier](#eslint-9--prettier)
- [Platform Technologies](#platform-technologies)
  - [Electron](#electron)
  - [Capacitor](#capacitor)
- [Learning Resources](#learning-resources)

---

## Stack Overview

AFFiNE uses a modern, full-stack TypeScript architecture with Rust for performance-critical operations.

**Philosophy:**
- ✅ **Type Safety Everywhere** - TypeScript + Rust
- ✅ **Latest Stable Versions** - Stay current with ecosystem
- ✅ **Proven Technologies** - Battle-tested in production
- ✅ **Developer Experience** - Fast builds, great tooling

---

## Frontend Technologies

### React 19

**Version in Project:** 19.1.0 (⚠️ 19.2.0 latest, upgrade recommended)
**Official Docs:** https://react.dev/

#### Why React 19?

React 19 is a major release with significant improvements:
- **React Compiler** (experimental) - Automatic optimization
- **Actions** - Simplified form handling and mutations
- **`use` Hook** - For promises and context
- **Document Metadata** - Built-in `<title>`, `<meta>` support
- **Asset Loading** - Preload/preconnect APIs
- **Web Components Support** - Better interop with BlockSuite

✅ **CURRENT (Nov 2025):** React 19 is stable and production-ready.

---

#### Key React 19 Features Used in AFFiNE

##### 1. **Server Components** (Not used yet)

⚠️ **Note:** AFFiNE currently uses client-side rendering. Server Components are not yet implemented.

##### 2. **Actions Pattern**

```typescript
// Modern form handling with useActionState
import { useActionState } from 'react'

function LoginForm() {
  const [state, formAction] = useActionState(async (prevState, formData) => {
    const email = formData.get('email')
    const password = formData.get('password')

    try {
      await login(email, password)
      return { success: true }
    } catch (error) {
      return { success: false, error: error.message }
    }
  }, { success: null })

  return (
    <form action={formAction}>
      <input name="email" type="email" />
      <input name="password" type="password" />
      <button type="submit">Login</button>
      {state.error && <p>{state.error}</p>}
    </form>
  )
}
```

**Where used:** Form submissions, mutations
**Benefit:** Automatic pending states, error handling

---

##### 3. **`use` Hook**

```typescript
import { use } from 'react'

function UserProfile({ userPromise }) {
  // Suspend until promise resolves
  const user = use(userPromise)

  return <div>{user.name}</div>
}
```

**Where used:** Data fetching components
**Benefit:** Simpler async data handling

---

##### 4. **Optimistic Updates**

```typescript
import { useOptimistic } from 'react'

function TodoList({ todos }) {
  const [optimisticTodos, addOptimisticTodo] = useOptimistic(
    todos,
    (state, newTodo) => [...state, newTodo]
  )

  async function handleAdd(todo) {
    // Show immediately (optimistic)
    addOptimisticTodo(todo)

    // Sync to server
    await saveTodo(todo)
  }

  return optimisticTodos.map(todo => <TodoItem key={todo.id} {...todo} />)
}
```

**Where used:** Document edits, workspace operations
**Benefit:** Instant UI feedback

---

#### React Best Practices in AFFiNE

1. **Functional Components Only** (no class components)
2. **Hooks for State & Effects**
3. **Composition over Inheritance**
4. **Controlled Components** for forms
5. **Error Boundaries** for error handling

**Example:**
```typescript
// ✅ Good: Functional component with hooks
function DocumentList() {
  const [docs, setDocs] = useState<Doc[]>([])
  const [loading, setLoading] = useState(true)

  useEffect(() => {
    loadDocs().then(setDocs).finally(() => setLoading(false))
  }, [])

  if (loading) return <Spinner />
  return <div>{docs.map(doc => <DocCard key={doc.id} doc={doc} />)}</div>
}

// ❌ Bad: Class component (legacy)
class DocumentList extends React.Component {
  // Don't use this pattern
}
```

---

#### 🌉 Bridge from React 18

**What changed from React 18 → 19:**
- **Automatic batching** improved (already in 18, enhanced in 19)
- **New hooks:** `use`, `useOptimistic`, `useActionState`
- **Better TypeScript** support
- **Ref cleanup** - ref callbacks receive cleanup function
- **`<Context>` instead of `<Context.Provider>`** (simpler syntax)

**Migration:**
Most React 18 code works unchanged in React 19. New features are opt-in.

---

### TypeScript 5.7

**Version in Project:** 5.7.2 (⚠️ 5.9.3 latest)
**Official Docs:** https://www.typescriptlang.org/

#### Why TypeScript?

- ✅ **Catch errors at compile time**
- ✅ **Better IDE autocomplete**
- ✅ **Self-documenting code** (types as documentation)
- ✅ **Refactoring confidence**
- ✅ **GraphQL codegen integration**

✅ **CURRENT (Nov 2025):** TypeScript 5.7 is recent and well-supported.

---

#### TypeScript Features Used

##### 1. **Strict Mode** (Enabled)

```typescript
// tsconfig.json
{
  "compilerOptions": {
    "strict": true,  // All strict checks enabled
    "noUncheckedIndexedAccess": true,  // Array access safety
    "exactOptionalPropertyTypes": true  // Precise optional properties
  }
}
```

**Impact:**
- `null` and `undefined` must be explicitly handled
- Function parameters are strictly checked
- Array access returns `T | undefined`

---

##### 2. **Utility Types**

```typescript
// Commonly used in AFFiNE

// Partial - make all properties optional
type PartialUser = Partial<User>

// Pick - select specific properties
type UserCredentials = Pick<User, 'email' | 'password'>

// Omit - exclude specific properties
type UserWithoutPassword = Omit<User, 'password'>

// Record - create object type
type WorkspaceMap = Record<string, Workspace>

// Awaited - unwrap Promise type
type User = Awaited<ReturnType<typeof fetchUser>>
```

---

##### 3. **Template Literal Types**

```typescript
// Type-safe string patterns
type EventName = `on${Capitalize<string>}`

const event: EventName = 'onClick'  // ✅
const invalid: EventName = 'click'   // ❌ Error
```

---

##### 4. **Const Type Parameters** (TypeScript 5.0+)

```typescript
function createAtom<const T>(value: T) {
  return atom(value)
}

const userAtom = createAtom({ id: 1, name: 'Alice' })
// Type: Atom<{ readonly id: 1; readonly name: "Alice" }>
// Not: Atom<{ id: number; name: string }>
```

**Benefit:** More precise types, better inference

---

#### 🎯 TypeScript Tips for AFFiNE

**Tip 1:** Use `unknown` instead of `any`
```typescript
// ❌ Bad
function process(data: any) {
  return data.value  // No type checking
}

// ✅ Good
function process(data: unknown) {
  if (typeof data === 'object' && data !== null && 'value' in data) {
    return data.value  // Type-safe
  }
}
```

**Tip 2:** Use `satisfies` operator (TypeScript 4.9+)
```typescript
const config = {
  apiUrl: 'https://api.affine.pro',
  timeout: 5000,
} satisfies Config  // Check type without widening

config.apiUrl  // Still string literal, not string
```

**Tip 3:** Leverage GraphQL codegen
```typescript
// Auto-generated from GraphQL schema
import type { UserQuery, UserQueryVariables } from '@affine/graphql'

// Fully typed!
const { data } = useQuery<UserQuery, UserQueryVariables>(UserDocument)
```

---

### Jotai (State Management)

**Version in Project:** 2.10.3 ✅ (current)
**Official Docs:** https://jotai.org/

#### Why Jotai?

🧠 **Mental Model:** Jotai is like `useState` for global state, but atomic and composable.

**Comparison:**
| Feature | Jotai | Redux | Zustand | Context |
|---------|-------|-------|---------|---------|
| **Boilerplate** | Minimal | High | Low | Minimal |
| **Performance** | Excellent | Good | Excellent | Poor (re-renders) |
| **DevTools** | Yes | Yes | Yes | No |
| **Learning Curve** | Easy | Hard | Easy | Easy |
| **Use Case** | Atomic state | Large apps | Simple stores | Small state |

**AFFiNE's Choice:** Jotai for fine-grained reactivity and minimal boilerplate.

---

#### Jotai Fundamentals

##### 1. **Primitive Atoms**

```typescript
import { atom } from 'jotai'

// Basic atom
export const userAtom = atom<User | null>(null)

// Atom with default value
export const themeAtom = atom<'light' | 'dark'>('light')

// Usage in component
import { useAtom } from 'jotai'

function UserProfile() {
  const [user, setUser] = useAtom(userAtom)

  return <div>{user?.name}</div>
}
```

---

##### 2. **Derived Atoms** (Computed State)

```typescript
import { atom } from 'jotai'

// Base atoms
const workspacesAtom = atom<Workspace[]>([])
const activeWorkspaceIdAtom = atom<string | null>(null)

// Derived atom (read-only)
export const activeWorkspaceAtom = atom((get) => {
  const workspaces = get(workspacesAtom)
  const id = get(activeWorkspaceIdAtom)
  return workspaces.find(w => w.id === id) ?? null
})

// Usage (read-only)
const [activeWorkspace] = useAtom(activeWorkspaceAtom)
```

**Benefit:** Automatic recomputation when dependencies change.

---

##### 3. **Async Atoms**

```typescript
import { atom } from 'jotai'

// Async data fetching
export const userDocsAtom = atom(async (get) => {
  const user = get(userAtom)
  if (!user) return []

  // Fetch data
  const docs = await fetchUserDocs(user.id)
  return docs
})

// Usage with Suspense
function DocList() {
  const [docs] = useAtom(userDocsAtom)  // Suspends until loaded

  return (
    <div>
      {docs.map(doc => <DocCard key={doc.id} doc={doc} />)}
    </div>
  )
}

// Wrap in Suspense boundary
function App() {
  return (
    <Suspense fallback={<Spinner />}>
      <DocList />
    </Suspense>
  )
}
```

---

##### 4. **Writable Derived Atoms**

```typescript
const userAtom = atom<User | null>(null)

// Writable derived atom
const userNameAtom = atom(
  (get) => get(userAtom)?.name ?? '',  // Read
  (get, set, newName: string) => {      // Write
    const user = get(userAtom)
    if (user) {
      set(userAtom, { ...user, name: newName })
    }
  }
)

// Usage
const [name, setName] = useAtom(userNameAtom)
setName('New Name')  // Updates userAtom
```

---

#### Jotai Best Practices

**Practice 1:** Keep atoms focused (single responsibility)
```typescript
// ✅ Good: Separate concerns
const userAtom = atom<User | null>(null)
const userSettingsAtom = atom<Settings>({})

// ❌ Bad: Too much in one atom
const appAtom = atom({
  user: null,
  settings: {},
  theme: 'light',
  // ... everything
})
```

**Practice 2:** Use `atomFamily` for dynamic atoms
```typescript
import { atomFamily } from 'jotai/utils'

// Create atoms on demand
const docAtomFamily = atomFamily((docId: string) =>
  atom(async () => await fetchDoc(docId))
)

// Usage
const docAtom = docAtomFamily('doc-123')
const [doc] = useAtom(docAtom)
```

**Practice 3:** Organize atoms by feature
```
src/modules/workspace/
  ├── atoms/
  │   ├── workspace.atoms.ts
  │   ├── workspace-list.atoms.ts
  │   └── index.ts
  ├── services/
  └── views/
```

---

**Real Example from AFFiNE:**
```typescript
// Simplified from packages/frontend/core/src/modules/workspace/

import { atom } from 'jotai'

// Base atoms
export const workspacesAtom = atom<Workspace[]>([])
export const currentWorkspaceIdAtom = atom<string | null>(null)

// Derived atoms
export const currentWorkspaceAtom = atom((get) => {
  const workspaces = get(workspacesAtom)
  const id = get(currentWorkspaceIdAtom)
  return workspaces.find(w => w.id === id)
})

export const workspaceDocsAtom = atom(async (get) => {
  const workspace = get(currentWorkspaceAtom)
  if (!workspace) return []
  return await loadDocs(workspace.id)
})
```

---

### React Router

**Version:** 6.28.0 ✅
**Official Docs:** https://reactrouter.com/

#### Why React Router v6?

- ✅ **Data Loading** - `loader` functions
- ✅ **Actions** - Form submissions
- ✅ **Nested Routes** - Layout composition
- ✅ **Code Splitting** - `lazy()` routes
- ✅ **Type Safety** - TypeScript support

---

#### Route Structure in AFFiNE

```typescript
// Simplified from packages/frontend/core/src/

const routes = [
  {
    path: '/',
    element: <AppLayout />,
    children: [
      {
        index: true,
        element: <HomePage />,
      },
      {
        path: 'workspace/:workspaceId',
        element: <WorkspaceLayout />,
        children: [
          {
            index: true,
            element: <WorkspaceOverview />,
          },
          {
            path: 'doc/:docId',
            element: <DocPage />,
          },
          {
            path: 'trash',
            element: <TrashPage />,
          },
        ],
      },
      {
        path: 'settings',
        element: <SettingsPage />,
      },
    ],
  },
]
```

**URL Examples:**
- `/` - Home page
- `/workspace/abc123` - Workspace overview
- `/workspace/abc123/doc/doc456` - Document page
- `/settings` - Settings

---

#### Navigation Patterns

```typescript
import { useNavigate, useParams } from 'react-router-dom'

function DocCard({ docId }: { docId: string }) {
  const navigate = useNavigate()
  const { workspaceId } = useParams()

  const openDoc = () => {
    navigate(`/workspace/${workspaceId}/doc/${docId}`)
  }

  return <button onClick={openDoc}>Open</button>
}
```

---

### Radix UI

**Version:** Various (latest primitives)
**Official Docs:** https://www.radix-ui.com/

#### Why Radix UI?

🧠 **Mental Model:** Radix provides **unstyled, accessible** component primitives. You add the styling.

**Benefits:**
- ✅ **Accessibility** - ARIA, keyboard navigation, focus management
- ✅ **Unstyled** - Full control over appearance
- ✅ **Composable** - Flexible component composition
- ✅ **TypeScript** - Fully typed

---

#### Radix Components Used

**Common Components:**
- `@radix-ui/react-dialog` - Modals
- `@radix-ui/react-dropdown-menu` - Dropdowns
- `@radix-ui/react-popover` - Popovers
- `@radix-ui/react-scroll-area` - Custom scrollbars
- `@radix-ui/react-toolbar` - Toolbars
- `@radix-ui/react-context-menu` - Right-click menus

**Example - Modal:**
```typescript
import * as Dialog from '@radix-ui/react-dialog'

function DeleteConfirmModal({ open, onOpenChange, onConfirm }) {
  return (
    <Dialog.Root open={open} onOpenChange={onOpenChange}>
      <Dialog.Portal>
        <Dialog.Overlay className="modal-overlay" />
        <Dialog.Content className="modal-content">
          <Dialog.Title>Delete Document?</Dialog.Title>
          <Dialog.Description>
            This action cannot be undone.
          </Dialog.Description>
          <button onClick={onConfirm}>Delete</button>
          <Dialog.Close asChild>
            <button>Cancel</button>
          </Dialog.Close>
        </Dialog.Content>
      </Dialog.Portal>
    </Dialog.Root>
  )
}
```

**Where Styled:**
- Classes applied with Vanilla Extract (see next section)
- Full control over appearance
- Radix handles accessibility & behavior

---

### Vanilla Extract

**Version:** 1.17.0 ✅
**Official Docs:** https://vanilla-extract.style/

#### Why Vanilla Extract?

🧠 **Mental Model:** Type-safe CSS-in-JS that generates **static CSS** at build time (not runtime).

**vs other solutions:**
| Solution | Type Safe | Runtime | Performance |
|----------|-----------|---------|-------------|
| **Vanilla Extract** | ✅ Yes | ❌ Build-time | ⚡ Fast (static CSS) |
| Styled Components | ❌ No | ✅ Runtime | 🐌 Slower |
| Emotion | ❌ No | ✅ Runtime | 🐌 Slower |
| Tailwind | ❌ No | ❌ Build-time | ⚡ Fast |
| CSS Modules | ❌ No | ❌ Build-time | ⚡ Fast |

**AFFiNE's Choice:** Type safety + performance.

---

#### Vanilla Extract Usage

```typescript
// button.css.ts
import { style } from '@vanilla-extract/css'

export const button = style({
  padding: '8px 16px',
  borderRadius: '4px',
  backgroundColor: 'var(--affine-primary-color)',
  color: 'white',
  border: 'none',
  cursor: 'pointer',

  ':hover': {
    backgroundColor: 'var(--affine-primary-hover)',
  },

  selectors: {
    '&[disabled]': {
      opacity: 0.5,
      cursor: 'not-allowed',
    },
  },
})

export const buttonVariants = {
  primary: style({ /* ... */ }),
  secondary: style({ /* ... */ }),
}
```

```typescript
// button.tsx
import { button, buttonVariants } from './button.css'

export function Button({ variant = 'primary', ...props }) {
  return (
    <button
      className={`${button} ${buttonVariants[variant]}`}
      {...props}
    />
  )
}
```

**Benefits:**
- ✅ Autocomplete for CSS properties
- ✅ Type errors for invalid values
- ✅ Zero runtime overhead
- ✅ Works with Vite HMR

---

### Vite 7

**Version in Project:** 7.0.0 ✅ (latest!)
**Official Docs:** https://vite.dev/

#### Why Vite 7?

⚡ **Blazing Fast** - ESM-based dev server, instant HMR

**Benchmarks:**
- **Dev server start:** < 1 second
- **HMR:** < 50ms (instant)
- **Production build:** Optimized with Rollup

✅ **CURRENT (Nov 2025):** Vite 7 is the latest major version with significant improvements.

---

#### Vite 7 Features

##### 1. **ESM-First Development**

```javascript
// No bundling in dev!
import { Button } from './components/Button'
// Browser loads this directly as ESM
```

**Benefits:**
- ⚡ Instant server start
- ⚡ Fast HMR (no rebuild)
- 📦 Only bundle for production

---

##### 2. **Optimized Dependencies**

Vite pre-bundles dependencies (esbuild) for faster loading:

```
node_modules/react → .vite/deps/react.js (pre-bundled)
```

**Result:** 100+ npm packages → 1 optimized file

---

##### 3. **Plugin System**

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import { vanillaExtractPlugin } from '@vanilla-extract/vite-plugin'

export default defineConfig({
  plugins: [
    react(),
    vanillaExtractPlugin(),
  ],
})
```

**Common Plugins:**
- `@vitejs/plugin-react` - React support (Fast Refresh)
- `@vanilla-extract/vite-plugin` - Vanilla Extract
- `vite-plugin-pwa` - Progressive Web App

---

#### Vite Config Example

```typescript
// packages/frontend/apps/web/vite.config.ts (simplified)
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import tsconfigPaths from 'vite-tsconfig-paths'

export default defineConfig({
  plugins: [
    react(),
    tsconfigPaths(),  // Resolve TypeScript paths
  ],

  server: {
    port: 8080,
    host: '0.0.0.0',
  },

  build: {
    target: 'es2020',
    outDir: 'dist',
    sourcemap: true,
  },

  optimizeDeps: {
    include: ['react', 'react-dom', 'jotai'],
  },
})
```

---

## Editor Technologies

### Lit (Web Components)

**Version in Project:** 3.2.1 ✅
**Official Docs:** https://lit.dev/

#### Why Lit for BlockSuite?

🧠 **Mental Model:** Lit is like React, but for **Web Components** (native browser standard).

**Comparison:**
| React | Lit |
|-------|-----|
| Virtual DOM | Real DOM + reactive properties |
| JSX | Tagged template literals |
| Ecosystem-dependent | Web standards |
| Large bundle | Small bundle (~5KB) |

**BlockSuite's Choice:** Web Components for framework-agnostic editor.

---

#### Lit Basics

```typescript
import { LitElement, html, css } from 'lit'
import { customElement, property } from 'lit/decorators.js'

@customElement('my-element')
export class MyElement extends LitElement {
  @property({ type: String })
  name = 'World'

  static styles = css`
    :host {
      display: block;
      padding: 16px;
    }
  `

  render() {
    return html`
      <div>Hello, ${this.name}!</div>
    `
  }
}
```

**Usage:**
```html
<my-element name="AFFiNE"></my-element>
```

---

**Real Example from BlockSuite:**
```typescript
// Simplified paragraph block
@customElement('affine-paragraph')
export class ParagraphBlock extends BlockElement {
  @property({ attribute: false })
  model!: ParagraphBlockModel

  render() {
    return html`
      <div class="affine-paragraph-block">
        <rich-text .model=${this.model}></rich-text>
      </div>
    `
  }
}
```

See: [BlockSuite Editor Guide](./BLOCKSUITE_EDITOR.md)

---

### Yjs (CRDT)

**Version in Project:** 13.6.21 ✅
**Official Docs:** https://docs.yjs.dev/

#### What is Yjs?

🧠 **Mental Model:** Yjs is like "Git for real-time collaboration" - automatic, conflict-free merging.

**CRDT = Conflict-free Replicated Data Type**

**Key Concept:**
```
User A: Insert "Hello" at position 0
User B: Insert "World" at position 0

Traditional: Conflict! Who wins?
Yjs (CRDT): Both succeed! "WorldHello" or "HelloWorld" (deterministic)
```

---

#### Yjs Data Types

##### 1. **Y.Text** - Collaborative string

```typescript
import * as Y from 'yjs'

const ydoc = new Y.Doc()
const ytext = ydoc.getText('content')

// User A
ytext.insert(0, 'Hello ')

// User B (simultaneously)
ytext.insert(6, 'World')

// Result (eventually consistent)
console.log(ytext.toString())  // "Hello World"
```

---

##### 2. **Y.Map** - Collaborative object

```typescript
const ymap = ydoc.getMap('metadata')

ymap.set('title', 'My Document')
ymap.set('createdAt', Date.now())

console.log(ymap.get('title'))  // "My Document"
```

---

##### 3. **Y.Array** - Collaborative array

```typescript
const yarray = ydoc.getArray('blocks')

yarray.push([{ type: 'paragraph', content: 'Hello' }])
yarray.insert(0, [{ type: 'heading', content: 'Title' }])
```

---

#### Yjs Synchronization

```typescript
// User A's document
const docA = new Y.Doc()

// User B's document
const docB = new Y.Doc()

// Generate update from A's changes
const update = Y.encodeStateAsUpdate(docA)

// Apply to B's document
Y.applyUpdate(docB, update)

// Now docA and docB are in sync!
```

**In AFFiNE:**
- Updates sent via Socket.IO
- Binary format (efficient)
- Guaranteed convergence

See: [Collaboration System Guide](./COLLABORATION_SYSTEM.md)

---

### BlockSuite Framework

**Custom Framework** built on Lit + Yjs
**Docs:** https://blocksuite.io/

BlockSuite is covered extensively in [BlockSuite Editor Guide](./BLOCKSUITE_EDITOR.md).

**Quick Overview:**
- **Block-based architecture** - Everything is a composable block
- **Yjs integration** - Real-time collaboration built-in
- **Rendering modes** - Page mode & Edgeless (canvas) mode
- **Extensible** - Custom blocks, widgets, extensions

---

## Backend Technologies

### NestJS 11

**Version in Project:** 11.0.12 ✅ (latest)
**Official Docs:** https://docs.nestjs.com/

#### Why NestJS?

🧠 **Mental Model:** NestJS is like "Angular for the backend" - opinionated, structured, enterprise-ready.

**vs Express:**
- ✅ **Dependency Injection** - Testable, modular
- ✅ **Decorators** - Clean, declarative code
- ✅ **TypeScript-first** - Full type safety
- ✅ **Built-in features** - Guards, interceptors, pipes
- ✅ **GraphQL support** - First-class

---

#### NestJS Architecture

```typescript
// Core concepts

// 1. Module - Organize related code
@Module({
  imports: [AuthModule, UserModule],
  controllers: [WorkspaceController],
  providers: [WorkspaceService, WorkspaceRepository],
  exports: [WorkspaceService],
})
export class WorkspaceModule {}

// 2. Controller - Handle HTTP requests
@Controller('workspaces')
export class WorkspaceController {
  constructor(private workspaceService: WorkspaceService) {}

  @Get()
  async findAll() {
    return this.workspaceService.findAll()
  }

  @Post()
  async create(@Body() dto: CreateWorkspaceDto) {
    return this.workspaceService.create(dto)
  }
}

// 3. Service - Business logic
@Injectable()
export class WorkspaceService {
  constructor(
    private prisma: PrismaService,
    private authService: AuthService,
  ) {}

  async findAll() {
    return this.prisma.workspace.findMany()
  }

  async create(dto: CreateWorkspaceDto) {
    // Business logic here
  }
}
```

---

#### NestJS Features Used

##### 1. **Dependency Injection**

```typescript
// Automatic injection
@Injectable()
export class UserService {
  constructor(
    private prisma: PrismaService,      // Injected
    private emailService: EmailService,  // Injected
  ) {}
}
```

**Benefits:**
- ✅ Testable (mock dependencies)
- ✅ Loose coupling
- ✅ Single source of truth

---

##### 2. **Guards** (Authorization)

```typescript
@Injectable()
export class AuthGuard implements CanActivate {
  canActivate(context: ExecutionContext): boolean {
    const request = context.switchToHttp().getRequest()
    const token = request.headers.authorization

    return validateToken(token)
  }
}

// Usage
@Controller('workspaces')
@UseGuards(AuthGuard)  // Protect all routes
export class WorkspaceController {
  // Routes here require authentication
}
```

---

##### 3. **Interceptors** (Request/Response transformation)

```typescript
@Injectable()
export class LoggingInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler) {
    console.log('Before...')

    const now = Date.now()
    return next
      .handle()
      .pipe(
        tap(() => console.log(`After... ${Date.now() - now}ms`))
      )
  }
}
```

---

##### 4. **Pipes** (Validation)

```typescript
import { z } from 'zod'

const CreateWorkspaceSchema = z.object({
  name: z.string().min(1).max(100),
})

type CreateWorkspaceDto = z.infer<typeof CreateWorkspaceSchema>

@Controller('workspaces')
export class WorkspaceController {
  @Post()
  async create(
    @Body(new ZodValidationPipe(CreateWorkspaceSchema))
    dto: CreateWorkspaceDto
  ) {
    // dto is validated!
  }
}
```

---

### Prisma 6

**Version in Project:** 6.6.0 (⚠️ 6.19+ recommended)
**Official Docs:** https://www.prisma.io/docs

#### Why Prisma?

🧠 **Mental Model:** Prisma is a **type-safe ORM** - database access with TypeScript guarantees.

**vs other ORMs:**
| Feature | Prisma | TypeORM | Sequelize |
|---------|--------|---------|-----------|
| **Type Safety** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐ |
| **Developer Experience** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ |
| **Performance** | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ |
| **Migrations** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ |

---

#### Prisma Schema

```prisma
// packages/backend/server/prisma/schema.prisma (simplified)

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model User {
  id        String   @id @default(uuid())
  email     String   @unique
  name      String?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  // Relations
  workspaces WorkspaceMember[]
  docs       Doc[]
}

model Workspace {
  id        String   @id @default(uuid())
  name      String
  createdAt DateTime @default(now())

  // Relations
  members   WorkspaceMember[]
  docs      Doc[]
}

model WorkspaceMember {
  id          String @id @default(uuid())
  userId      String
  workspaceId String
  role        String // 'owner' | 'admin' | 'member'

  user      User      @relation(fields: [userId], references: [id])
  workspace Workspace @relation(fields: [workspaceId], references: [id])

  @@unique([userId, workspaceId])
}

model Doc {
  id          String   @id @default(uuid())
  workspaceId String
  title       String
  content     Bytes?   // Yjs binary data
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt

  workspace Workspace @relation(fields: [workspaceId], references: [id])
  author    User      @relation(fields: [authorId], references: [id])
  authorId  String

  @@index([workspaceId])
  @@index([authorId])
}
```

---

#### Prisma Client Usage

```typescript
import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient()

// Create
const user = await prisma.user.create({
  data: {
    email: 'user@example.com',
    name: 'John Doe',
  },
})

// Read (with relations)
const userWithDocs = await prisma.user.findUnique({
  where: { id: userId },
  include: {
    docs: true,      // Include related docs
    workspaces: {
      include: {
        workspace: true,
      },
    },
  },
})

// Update
await prisma.doc.update({
  where: { id: docId },
  data: { title: 'New Title' },
})

// Delete
await prisma.doc.delete({
  where: { id: docId },
})

// Complex query
const recentDocs = await prisma.doc.findMany({
  where: {
    workspace: {
      members: {
        some: {
          userId: currentUserId,
        },
      },
    },
  },
  orderBy: { updatedAt: 'desc' },
  take: 10,
})
```

**Type Safety:**
```typescript
// ✅ TypeScript knows the shape
userWithDocs.docs[0].title  // string
userWithDocs.email          // string

// ❌ TypeScript error
userWithDocs.invalidField   // Error: Property doesn't exist
```

---

#### Prisma Migrations

```bash
# Create migration
yarn prisma migrate dev --name add_tags_to_docs

# Apply migrations (production)
yarn prisma migrate deploy

# Generate client (after schema changes)
yarn prisma generate

# Reset database (dev only!)
yarn prisma migrate reset

# Open Prisma Studio (DB GUI)
yarn prisma studio
```

See: [Database Schema Guide](./DATABASE_SCHEMA.md)

---

### GraphQL

**Version:** 16.9.0 (⚠️ 16.12.0 latest)
**Official Docs:** https://graphql.org/

#### Why GraphQL?

🧠 **Mental Model:** GraphQL is like "SQL for APIs" - clients request exactly the data they need.

**vs REST:**
```
REST:
  GET /users/123          → { id, name, email, ... }
  GET /users/123/posts    → [{ id, title, ... }, ...]

GraphQL:
  POST /graphql
  query {
    user(id: "123") {
      name
      posts {
        title
      }
    }
  }
  → { user: { name: "...", posts: [{ title: "..." }] } }
```

**Benefits:**
- ✅ **No over-fetching** - Get only what you need
- ✅ **No under-fetching** - Get related data in one request
- ✅ **Strongly typed** - Schema as contract
- ✅ **Introspection** - Self-documenting API

---

#### GraphQL in AFFiNE

**Schema Example:**
```graphql
# Generated schema (packages/backend/server/src/schema.gql)

type User {
  id: ID!
  email: String!
  name: String
  workspaces: [Workspace!]!
  docs: [Doc!]!
}

type Workspace {
  id: ID!
  name: String!
  members: [WorkspaceMember!]!
  docs: [Doc!]!
}

type Query {
  user(id: ID!): User
  workspace(id: ID!): Workspace
  workspaces: [Workspace!]!
}

type Mutation {
  createWorkspace(name: String!): Workspace!
  updateDoc(id: ID!, title: String!): Doc!
  deleteDoc(id: ID!): Boolean!
}

type Subscription {
  docUpdated(docId: ID!): Doc!
}
```

**Resolver Example:**
```typescript
// Backend resolver
@Resolver(() => Workspace)
export class WorkspaceResolver {
  constructor(private prisma: PrismaService) {}

  @Query(() => [Workspace])
  async workspaces(@CurrentUser() user: User) {
    return this.prisma.workspace.findMany({
      where: {
        members: {
          some: { userId: user.id },
        },
      },
    })
  }

  @Mutation(() => Workspace)
  async createWorkspace(
    @Args('name') name: string,
    @CurrentUser() user: User,
  ) {
    return this.prisma.workspace.create({
      data: {
        name,
        members: {
          create: {
            userId: user.id,
            role: 'owner',
          },
        },
      },
    })
  }
}
```

**Frontend Query:**
```typescript
import { gql, useQuery } from '@apollo/client'

const WORKSPACES_QUERY = gql`
  query Workspaces {
    workspaces {
      id
      name
      members {
        user {
          name
        }
      }
    }
  }
`

function WorkspaceList() {
  const { data, loading } = useQuery(WORKSPACES_QUERY)

  if (loading) return <Spinner />

  return (
    <div>
      {data.workspaces.map(ws => (
        <div key={ws.id}>{ws.name}</div>
      ))}
    </div>
  )
}
```

---

#### GraphQL Codegen

AFFiNE uses **GraphQL Code Generator** to auto-generate TypeScript types:

```bash
yarn graphql-codegen
```

**Generated Types:**
```typescript
// Auto-generated from schema
export type WorkspacesQuery = {
  __typename?: 'Query'
  workspaces: Array<{
    __typename?: 'Workspace'
    id: string
    name: string
    members: Array<{
      __typename?: 'WorkspaceMember'
      user: {
        __typename?: 'User'
        name: string | null
      }
    }>
  }>
}
```

**Usage:**
```typescript
const { data } = useQuery<WorkspacesQuery>(WORKSPACES_QUERY)
//                        ^^^^^^^^^^^^^^^^ Fully typed!

data?.workspaces[0].name  // TypeScript knows this is string
```

See: [API Documentation](./API_DOCUMENTATION.md)

---

### Socket.IO

**Version:** 4.8.1 ✅ (latest)
**Official Docs:** https://socket.io/

#### Why Socket.IO?

🧠 **Mental Model:** Socket.IO = WebSocket + fallbacks + rooms + events

**vs plain WebSocket:**
- ✅ **Automatic reconnection**
- ✅ **HTTP long-polling fallback** (works everywhere)
- ✅ **Rooms & namespaces** (group connections)
- ✅ **Event-based** (not just string messages)
- ✅ **Acknowledgments** (request/response pattern)

---

#### Socket.IO Usage in AFFiNE

**Backend (Server):**
```typescript
import { WebSocketGateway, WebSocketServer } from '@nestjs/websockets'
import { Server, Socket } from 'socket.io'

@WebSocketGateway()
export class SyncGateway {
  @WebSocketServer()
  server: Server

  // Handle connection
  handleConnection(client: Socket) {
    console.log('Client connected:', client.id)

    // Join document room
    client.on('doc:join', (docId: string) => {
      client.join(`doc:${docId}`)
    })

    // Broadcast Yjs update
    client.on('doc:update', ({ docId, update }) => {
      // Broadcast to all clients in the room (except sender)
      client.to(`doc:${docId}`).emit('doc:update', { update })

      // Save to database
      this.saveDocUpdate(docId, update)
    })
  }
}
```

**Frontend (Client):**
```typescript
import { io } from 'socket.io-client'

const socket = io('http://localhost:3010')

// Join document
socket.emit('doc:join', docId)

// Listen for updates
socket.on('doc:update', ({ update }) => {
  // Apply Yjs update
  Y.applyUpdate(ydoc, update)
})

// Send update
ydoc.on('update', (update, origin) => {
  if (origin !== 'remote') {
    socket.emit('doc:update', { docId, update })
  }
})
```

---

### BullMQ

**Version:** Latest
**Official Docs:** https://docs.bullmq.io/

#### Why BullMQ?

🧠 **Mental Model:** BullMQ = Redis-based job queue for background tasks.

**Use Cases:**
- Email sending
- Document export (PDF, DOCX)
- Image processing
- Scheduled tasks
- Retry failed operations

---

#### BullMQ Example

```typescript
import { Queue, Worker } from 'bullmq'

// Create queue
const emailQueue = new Queue('email', {
  connection: { host: 'localhost', port: 6379 },
})

// Add job
await emailQueue.add('welcome', {
  to: 'user@example.com',
  template: 'welcome',
})

// Process jobs
const worker = new Worker('email', async (job) => {
  const { to, template } = job.data

  await sendEmail(to, template)

  return { sent: true }
}, {
  connection: { host: 'localhost', port: 6379 },
})

// Handle completion
worker.on('completed', (job) => {
  console.log(`Email sent to ${job.data.to}`)
})

// Handle failure
worker.on('failed', (job, err) => {
  console.error(`Failed to send email:`, err)
})
```

**Where Used:**
- Email notifications
- Async operations
- Scheduled cleanup tasks

---

## Database & Storage

### PostgreSQL

**Version:** 16 (pgvector)
**Official Docs:** https://www.postgresql.org/docs/

#### Why PostgreSQL?

- ✅ **ACID compliance** - Data integrity
- ✅ **JSON support** - Flexible schemas
- ✅ **Full-text search** - Built-in search
- ✅ **pgvector extension** - Vector embeddings (AI features)
- ✅ **Performance** - Indexes, query optimization

---

#### PostgreSQL Usage

**Through Prisma:**
```typescript
// All database access via Prisma Client
await prisma.user.create({ data: { email: 'test@example.com' } })
```

**Direct SQL (rare):**
```typescript
await prisma.$queryRaw`
  SELECT * FROM "User"
  WHERE email LIKE ${pattern}
`
```

See: [Database Schema Guide](./DATABASE_SCHEMA.md)

---

### Redis

**Version:** Latest
**Official Docs:** https://redis.io/docs/

#### Why Redis?

🧠 **Mental Model:** Redis = In-memory key-value store (super fast cache).

**Use Cases in AFFiNE:**
1. **Cache** - API response caching
2. **Session store** - User sessions
3. **Queue** - BullMQ job storage
4. **Rate limiting** - API throttling

---

### IndexedDB

**Browser API** (standard)
**Official Docs:** https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API

#### Why IndexedDB?

🧠 **Mental Model:** IndexedDB = "SQL database in the browser"

**vs LocalStorage:**
| Feature | IndexedDB | LocalStorage |
|---------|-----------|--------------|
| **Size** | Unlimited* | 5-10MB |
| **Async** | ✅ Yes | ❌ No (blocking) |
| **Types** | Any (objects, blobs) | Strings only |
| **Queries** | Indexes, ranges | Key lookup only |

*Practically ~50GB, browser-dependent

---

#### IndexedDB Usage (via abstraction)

AFFiNE uses custom abstractions over IndexedDB:

```typescript
// Simplified usage
import { db } from '@affine/core/modules/db'

// Store Yjs document
await db.docs.put({
  id: docId,
  data: Y.encodeStateAsUpdate(ydoc),
  updatedAt: Date.now(),
})

// Retrieve document
const doc = await db.docs.get(docId)
const ydoc = new Y.Doc()
Y.applyUpdate(ydoc, doc.data)
```

**Where Used:**
- Local document storage
- Workspace metadata
- Blob storage (images, files)
- Offline cache

---

## Native & Performance

### Rust (Edition 2024)

**Version:** Edition 2024 (Rust 1.85+) ✅
**Official Docs:** https://www.rust-lang.org/

#### Why Rust?

🧠 **Mental Model:** Rust = "C++ performance + memory safety + modern features"

**Benefits:**
- ⚡ **Performance** - As fast as C/C++
- 🔒 **Memory safety** - No segfaults, no data races
- 🦀 **Zero-cost abstractions** - High-level code, low-level performance
- 🌍 **Cross-platform** - Compile to native code for each OS

**Use Cases in AFFiNE:**
- CRDT operations (y-octo)
- File parsing (PDF, DOCX)
- Local database (SQLite)
- Image processing
- Encryption/decryption

✅ **CURRENT (Nov 2025):** Rust Edition 2024 is the latest with async improvements.

---

#### Rust in AFFiNE

**Example - Native Function:**
```rust
// packages/backend/native/src/lib.rs

use napi::bindgen_prelude::*;
use napi_derive::napi;

#[napi]
pub fn parse_pdf(file_path: String) -> Result<String> {
  // Rust implementation
  let content = pdf_extract::extract_text(&file_path)
    .map_err(|e| Error::from_reason(e.to_string()))?;

  Ok(content)
}
```

**JavaScript Usage:**
```typescript
import { parsePdf } from '@affine/server-native'

const content = parsePdf('/path/to/file.pdf')
console.log(content)  // Extracted text
```

---

### NAPI-RS

**Version:** 3.0.0-beta.3
**Official Docs:** https://napi.rs/

#### What is NAPI-RS?

🧠 **Mental Model:** NAPI-RS = Bridge between Node.js (JavaScript) and Rust

**How it works:**
```
JavaScript ←→ Node-API (C ABI) ←→ Rust
```

**Benefits:**
- ✅ **Type-safe bindings** - Generate TypeScript types
- ✅ **Async support** - Rust async → JavaScript Promise
- ✅ **Performance** - No serialization overhead
- ✅ **Cross-platform** - Single codebase, compile for each OS

---

#### NAPI-RS Example

**Rust Side:**
```rust
#[napi]
pub struct Document {
  pub id: String,
  pub title: String,
}

#[napi]
impl Document {
  #[napi(constructor)]
  pub fn new(id: String, title: String) -> Self {
    Document { id, title }
  }

  #[napi]
  pub fn get_summary(&self) -> String {
    format!("{}: {}", self.id, self.title)
  }
}

#[napi]
pub async fn fetch_document(id: String) -> Result<Document> {
  // Async Rust code
  tokio::time::sleep(std::time::Duration::from_secs(1)).await;

  Ok(Document {
    id: id.clone(),
    title: format!("Document {}", id),
  })
}
```

**Generated TypeScript:**
```typescript
// Auto-generated
export class Document {
  constructor(id: string, title: string)
  id: string
  title: string
  getSummary(): string
}

export function fetchDocument(id: string): Promise<Document>
```

**JavaScript Usage:**
```typescript
import { Document, fetchDocument } from '@affine/native'

const doc = new Document('123', 'My Doc')
console.log(doc.getSummary())  // "123: My Doc"

const fetchedDoc = await fetchDocument('456')
```

---

## Build & Development

### Yarn 4 (Berry)

**Version in Project:** 4.9.1 (⚠️ 4.11.0 latest)
**Official Docs:** https://yarnpkg.com/

#### Why Yarn 4 (Berry)?

🧠 **Mental Model:** Yarn Berry = "Modern package manager with workspaces superpowers"

**vs Yarn 1.x:**
- ✅ **Plug'n'Play (PnP)** - No `node_modules` (optional)
- ✅ **Workspaces 2.0** - Better monorepo support
- ✅ **Zero-Installs** - Commit dependencies (optional)
- ✅ **Plugin system** - Extensible

**vs npm/pnpm:**
- ✅ **Faster installs** (parallel + cache)
- ✅ **Better workspace support**
- ✅ **Constraints** (enforce rules)

---

#### Yarn Commands

```bash
# Install all dependencies
yarn install

# Add dependency to specific workspace
yarn workspace @affine/core add react

# Run script in workspace
yarn workspace @affine/core dev

# Run script in all workspaces
yarn workspaces foreach run build

# Update dependencies
yarn up react react-dom

# Check for outdated deps
yarn outdated

# Dedupe dependencies
yarn dedupe
```

---

### Vitest

**Version in Project:** 3.1.3 (🚨 v4.0+ available)
**Official Docs:** https://vitest.dev/

#### Why Vitest?

🧠 **Mental Model:** Vitest = "Jest, but faster and with Vite"

**vs Jest:**
- ⚡ **Faster** - Vite's transform pipeline
- ✅ **ES modules** - Native ESM support
- ✅ **TypeScript** - No config needed
- ✅ **Vite integration** - Same config
- ✅ **Watch mode** - Instant

---

#### Vitest Usage

```typescript
// sum.test.ts
import { describe, it, expect } from 'vitest'
import { sum } from './sum'

describe('sum', () => {
  it('adds two numbers', () => {
    expect(sum(1, 2)).toBe(3)
  })

  it('handles negative numbers', () => {
    expect(sum(-1, 1)).toBe(0)
  })
})
```

**Run Tests:**
```bash
yarn test              # Run all tests
yarn test:watch        # Watch mode
yarn test:ui           # UI mode (browser)
yarn test:coverage     # Coverage report
```

See: [Testing Guide](./TESTING_GUIDE.md)

---

### Playwright

**Version in Project:** 1.52.0 (⚠️ 1.56.0 latest)
**Official Docs:** https://playwright.dev/

#### Why Playwright?

🧠 **Mental Model:** Playwright = "Selenium, but modern and reliable"

**vs Selenium:**
- ✅ **Faster** - Direct browser control
- ✅ **More reliable** - Auto-wait
- ✅ **Better DX** - TypeScript, codegen
- ✅ **Multi-browser** - Chromium, Firefox, WebKit

---

#### Playwright Example

```typescript
// e2e/workspace.spec.ts
import { test, expect } from '@playwright/test'

test('create workspace', async ({ page }) => {
  // Navigate
  await page.goto('http://localhost:8080')

  // Click new workspace button
  await page.click('button:has-text("New Workspace")')

  // Fill form
  await page.fill('input[name="name"]', 'My Workspace')

  // Submit
  await page.click('button:has-text("Create")')

  // Assert
  await expect(page.locator('text=My Workspace')).toBeVisible()
})
```

**Run E2E Tests:**
```bash
yarn e2e               # Run all E2E tests
yarn e2e:ui            # Interactive mode
yarn e2e:debug         # Debug mode
```

See: [Testing Guide](./TESTING_GUIDE.md)

---

### ESLint 9 & Prettier

**ESLint:** 9.16.0 ✅
**Prettier:** 3.4.2 ✅
**Docs:** https://eslint.org/, https://prettier.io/

#### Why ESLint + Prettier?

- **ESLint** - Code quality (bugs, best practices)
- **Prettier** - Code formatting (consistent style)

**Together:** Catch bugs + enforce style automatically.

---

#### ESLint 9 (Flat Config)

✅ **CURRENT (Nov 2025):** ESLint 9 uses new flat config format.

```javascript
// eslint.config.mjs
import eslint from '@eslint/js'
import tseslint from 'typescript-eslint'
import react from 'eslint-plugin-react'

export default [
  eslint.configs.recommended,
  ...tseslint.configs.recommended,
  react.configs.flat.recommended,
  {
    rules: {
      'no-console': 'warn',
      '@typescript-eslint/no-unused-vars': 'error',
      'react/react-in-jsx-scope': 'off',  // Not needed in React 19
    },
  },
]
```

**Run Linting:**
```bash
yarn lint              # Check all files
yarn lint:fix          # Auto-fix issues
```

---

## Platform Technologies

### Electron

**Version in Project:** 36.0.0 (🚨 39.0.0 latest - UPGRADE URGENT)
**Official Docs:** https://www.electronjs.org/

#### Why Electron?

🧠 **Mental Model:** Electron = "Chromium + Node.js = Desktop apps with web tech"

**Architecture:**
```
┌─────────────────┐
│  Main Process   │ (Node.js - access to OS)
│  (Electron API) │
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
┌───▼──┐  ┌──▼───┐
│Render│  │Render│  (Chromium - web content)
│  #1  │  │  #2  │
└──────┘  └──────┘
```

⚠️ **OUTDATED:** Electron 36 is no longer supported. Upgrade to 39+ recommended.

See: [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md#electron---v3600)

---

### Capacitor

**Version in Project:** 7.0.0 ✅ (latest)
**Official Docs:** https://capacitorjs.com/

#### Why Capacitor?

🧠 **Mental Model:** Capacitor = "Web → Native iOS/Android"

**vs Cordova/PhoneGap:**
- ✅ **Native APIs** - Direct access, no plugins needed
- ✅ **Modern** - Built for modern web frameworks
- ✅ **Live reload** - During development
- ✅ **Native UI** - Mix web + native components

---

#### Capacitor Architecture

```
┌──────────────────────┐
│   Web App (React)    │
│   (Same as web!)     │
└──────────┬───────────┘
           │
    ┌──────┴──────┐
    │             │
┌───▼───┐    ┌───▼───┐
│  iOS  │    │Android│
│Native │    │Native │
│ View  │    │ View  │
└───────┘    └───────┘
```

**Capacitor Plugins Used:**
- `@capacitor/app` - App lifecycle
- `@capacitor/filesystem` - File access
- `@capacitor/camera` - Camera/photos
- `@capacitor/share` - Native sharing

---

## Learning Resources

### Official Documentation

| Technology | Documentation | Version |
|------------|---------------|---------|
| **React** | https://react.dev/ | 19.x |
| **TypeScript** | https://www.typescriptlang.org/docs/ | 5.7+ |
| **NestJS** | https://docs.nestjs.com/ | 11.x |
| **Prisma** | https://www.prisma.io/docs | 6.x |
| **Yjs** | https://docs.yjs.dev/ | 13.x |
| **Vite** | https://vite.dev/ | 7.x |
| **Lit** | https://lit.dev/ | 3.x |
| **Jotai** | https://jotai.org/ | 2.x |
| **Rust** | https://doc.rust-lang.org/ | 2024 |

### Recommended Learning Paths

**Frontend Track:**
1. React 19 → TypeScript → Jotai → Vite
2. Practice: Build a simple app with all four

**Backend Track:**
1. NestJS → Prisma → GraphQL → PostgreSQL
2. Practice: Build a REST/GraphQL API

**Editor Track:**
1. Lit → Yjs → Web Components → BlockSuite
2. Practice: Build a simple collaborative editor

**Full-Stack Track:**
Combine all of the above + Socket.IO for real-time

---

## Next Steps

### Dive Deeper

- **Frontend Focus** → [Frontend Architecture](./FRONTEND_ARCHITECTURE.md)
- **Backend Focus** → [Backend Architecture](./BACKEND_ARCHITECTURE.md)
- **Editor Focus** → [BlockSuite Editor](./BLOCKSUITE_EDITOR.md)
- **Collaboration** → [Collaboration System](./COLLABORATION_SYSTEM.md)

### Practical Application

- **Code Patterns** → [Patterns & Conventions](./PATTERNS_AND_CONVENTIONS.md)
- **How-To Guides** → [How-To Guide](./HOW_TO_GUIDE.md)
- **Testing** → [Testing Guide](./TESTING_GUIDE.md)

---

## Key Takeaways

✅ **Frontend:** React 19 + TypeScript 5.7 + Jotai + Vite 7
✅ **Editor:** Lit 3 + Yjs 13 + BlockSuite (custom)
✅ **Backend:** NestJS 11 + Prisma 6 + GraphQL + PostgreSQL
✅ **Real-time:** Socket.IO 4.8 for collaboration
✅ **Native:** Rust (Edition 2024) via NAPI-RS for performance
✅ **Build:** Yarn 4 + Vitest + Playwright + ESLint 9
✅ **Platform:** Electron 36 (desktop) + Capacitor 7 (mobile)

**Philosophy:** Latest stable versions, type safety everywhere, performance when needed.

---

**🎉 You now understand the entire AFFiNE tech stack!**

Continue to [Data Flow Guide](./DATA_FLOW_GUIDE.md) to see how these technologies work together.

---

*Last updated: November 18, 2025*
*See [README.md](./README.md) for the complete learning path.*
