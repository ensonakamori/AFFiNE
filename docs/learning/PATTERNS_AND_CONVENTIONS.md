# Patterns & Conventions

> **Mental Model**: Think of conventions as the **shared language** of a codebase. Just like React has conventions (`useState`, `useEffect`), AFFiNE has patterns that make code predictable and maintainable across 100+ packages.

**For React Developers**: You're familiar with React conventions (props down, events up, hooks start with `use`). AFFiNE extends these with its own patterns for modules, services, atoms, and file organization.

---

## Table of Contents

1. [Code Style & Formatting](#code-style--formatting)
2. [Naming Conventions](#naming-conventions)
3. [File Organization](#file-organization)
4. [TypeScript Patterns](#typescript-patterns)
5. [React Patterns](#react-patterns)
6. [Jotai Patterns](#jotai-patterns)
7. [Service Patterns](#service-patterns)
8. [Error Handling](#error-handling)
9. [Testing Conventions](#testing-conventions)
10. [Git & PR Conventions](#git--pr-conventions)

---

## Code Style & Formatting

### Tooling

**AFFiNE uses**:
- **ESLint 9** (flat config) - Linting
- **Prettier 3** - Formatting
- **TypeScript 5.7** - Type checking

**Configuration**:
```json
// .prettierrc
{
  "semi": true,
  "singleQuote": true,
  "trailingComma": "es5",
  "arrowParens": "avoid",
  "printWidth": 80
}
```

**Auto-format on save**:
```json
// .vscode/settings.json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```

### Import Order

**Convention**: Organize imports in this order:

```typescript
// 1. External libraries
import { useState, useEffect } from 'react';
import { atom, useAtom } from 'jotai';
import { clsx } from 'clsx';

// 2. Internal absolute imports (@affine/*)
import { WorkspaceService } from '@affine/core/modules/workspace';
import { useService } from '@toeverything/infra';
import type { Workspace } from '@affine/graphql';

// 3. Relative imports
import { Header } from './header';
import type { PageProps } from './types';
import * as styles from './index.css';

// 4. Side effects (last)
import './polyfills';
```

**ESLint rule**:
```javascript
{
  'import/order': ['error', {
    groups: ['builtin', 'external', 'internal', 'parent', 'sibling', 'index'],
    'newlines-between': 'always',
    alphabetize: { order: 'asc' },
  }],
}
```

### Line Length

**Guideline**: 80 characters (enforced by Prettier)

```typescript
// ❌ Too long
const workspace = await this.workspaceService.getWorkspaceWithMembersAndPermissions(workspaceId);

// ✅ Break into multiple lines
const workspace =
  await this.workspaceService.getWorkspaceWithMembersAndPermissions(
    workspaceId
  );

// ✅ Or extract to variable
const getWorkspace =
  this.workspaceService.getWorkspaceWithMembersAndPermissions;

const workspace = await getWorkspace(workspaceId);
```

---

## Naming Conventions

### Variables & Functions

**Pattern**: `camelCase`

```typescript
// ✅ Good
const workspaceId = 'abc123';
const currentUser = getCurrentUser();

function loadWorkspace() { }
function handleClick() { }

// ❌ Bad
const WorkspaceID = 'abc123';
const current_user = getCurrentUser();

function LoadWorkspace() { }
function handle_click() { }
```

### Classes & Interfaces

**Pattern**: `PascalCase`

```typescript
// ✅ Good
class WorkspaceService { }
interface UserProfile { }
type WorkspaceMetadata = { };

// ❌ Bad
class workspaceService { }
interface userProfile { }
type workspace_metadata = { };
```

### Constants

**Pattern**: `UPPER_SNAKE_CASE` for true constants

```typescript
// ✅ Good - Values that never change
const MAX_FILE_SIZE = 100 * 1024 * 1024; // 100MB
const API_BASE_URL = 'https://api.affine.pro';
const DEFAULT_WORKSPACE_NAME = 'My Workspace';

// ❌ Bad - Not true constants
const USER_ID = getCurrentUserId(); // This changes!
const WORKSPACE_LIST = []; // Mutable!
```

**Pattern**: `camelCase` for config objects

```typescript
// ✅ Good - Config objects
const apiConfig = {
  baseUrl: 'https://api.affine.pro',
  timeout: 5000,
};

const editorConfig = {
  autosave: true,
  spellcheck: false,
};
```

### React Components

**Pattern**: `PascalCase` files and components

```typescript
// ✅ Good
// File: WorkspaceCard.tsx
export function WorkspaceCard() { }

// File: PageHeader.tsx
export function PageHeader() { }

// ❌ Bad
// File: workspace-card.tsx
export function workspaceCard() { }
```

### Hooks

**Pattern**: `use` prefix

```typescript
// ✅ Good
function useWorkspace() { }
function useCurrentUser() { }
function useDebounce() { }

// ❌ Bad
function getWorkspace() { } // Not a hook!
function currentUser() { } // Not a hook!
```

### Services

**Pattern**: `*Service` suffix

```typescript
// ✅ Good
class WorkspaceService { }
class AuthService { }
class StorageService { }

// ❌ Bad
class WorkspaceManager { } // Use Service
class Auth { } // Too short, use AuthService
```

### Jotai Atoms

**Pattern**: `*Atom` suffix

```typescript
// ✅ Good
const workspaceAtom = atom<Workspace | null>(null);
const activeWorkspaceIdAtom = atom<string | null>(null);
const workspaceListAtom = atom<Workspace[]>([]);

// ❌ Bad
const workspace = atom(null); // Missing "Atom" suffix
const activeId = atom(null); // Not descriptive enough
```

### Yjs Variables

**Pattern**: Prefix with `y` or `Y`

```typescript
// ✅ Good
const yDoc = new Y.Doc();
const yText = doc.getText('content');
const yMap = doc.getMap('meta');
const yArray = doc.getArray('blocks');

// ❌ Bad
const doc = new Y.Doc(); // Conflicts with DOM Document
const text = doc.getText('content'); // Confusing
```

### Test Files

**Pattern**: `*.spec.ts` or `*.test.ts`

```
workspace.ts
workspace.spec.ts        ✅ Unit tests

workspace-card.tsx
workspace-card.test.tsx  ✅ Component tests

e2e/workspace.e2e.ts     ✅ E2E tests
```

---

## File Organization

### Module Structure

**Standard module layout**:

```
packages/frontend/core/src/modules/workspace/
├── index.ts                # Public API
├── module.ts               # Module definition
├── services/
│   ├── workspace.ts        # WorkspaceService
│   ├── list.ts             # WorkspacesService
│   └── engine.ts           # WorkspaceEngineService
├── entities/
│   ├── workspace.ts        # Workspace entity
│   └── engine.ts           # WorkspaceEngine entity
├── scopes/
│   └── workspace.ts        # WorkspaceScope (DI container)
├── atoms.ts                # Jotai atoms (if needed)
├── views/
│   ├── workspace-card.tsx
│   ├── workspace-list.tsx
│   └── workspace-header.tsx
└── __tests__/
    └── workspace.spec.ts
```

### Public API (`index.ts`)

**Pattern**: Only export what's needed

```typescript
// packages/frontend/core/src/modules/workspace/index.ts

// ✅ Export public API
export { WorkspaceModule } from './module';
export { WorkspaceService, WorkspacesService } from './services';
export { Workspace } from './entities/workspace';

// ❌ Don't export internals
// export { WorkspaceImpl } from './impls/workspace';  // Internal!
// export { workspaceAtom } from './atoms';           // Internal!
```

### Component Co-location

**Pattern**: Keep related files together

```
workspace-card/
├── index.tsx               # Component export
├── workspace-card.tsx      # Component implementation
├── workspace-card.css.ts   # Styles (Vanilla Extract)
├── workspace-card.test.tsx # Tests
└── use-workspace-card.ts   # Custom hook (if complex)
```

**Or flat** (for simple components):

```
components/
├── workspace-card.tsx
├── workspace-card.css.ts
└── workspace-list.tsx
```

### Shared Code

**Location**: `packages/common/`

```
packages/common/
├── infra/          # DI framework
├── graphql/        # GraphQL types
├── nbstore/        # Storage layer
└── y-octo/         # CRDT (Rust)
```

---

## TypeScript Patterns

### Prefer `type` Over `interface`

**Convention**: Use `type` for most cases

```typescript
// ✅ Preferred
type User = {
  id: string;
  name: string;
  email: string;
};

// ✅ OK for extendable APIs
interface Plugin {
  name: string;
  init(): void;
}

// ❌ Avoid for simple objects
interface User {
  id: string;
  name: string;
}
```

**Why?** Types are more flexible (unions, intersections), interfaces are for inheritance.

### Explicit Return Types

**Convention**: Always type function returns

```typescript
// ✅ Good
function getWorkspace(id: string): Promise<Workspace | null> {
  return this.models.workspace.findUnique({ where: { id } });
}

// ❌ Bad - Inferred return type
function getWorkspace(id: string) {
  return this.models.workspace.findUnique({ where: { id } });
}
```

### Avoid `any`

**Convention**: Use `unknown` or proper types

```typescript
// ❌ Bad
function parseJson(str: string): any {
  return JSON.parse(str);
}

// ✅ Better
function parseJson<T = unknown>(str: string): T {
  return JSON.parse(str) as T;
}

// ✅ Best (with validation)
import { z } from 'zod';

const UserSchema = z.object({
  id: z.string(),
  name: z.string(),
});

function parseUser(str: string): z.infer<typeof UserSchema> {
  return UserSchema.parse(JSON.parse(str));
}
```

### Utility Types

**Common patterns in AFFiNE**:

```typescript
// Make all properties optional
type PartialUser = Partial<User>;

// Make all properties required
type RequiredUser = Required<Partial<User>>;

// Pick specific properties
type UserPreview = Pick<User, 'id' | 'name' | 'avatar'>;

// Omit specific properties
type PublicUser = Omit<User, 'password' | 'email'>;

// Extract function return type
type WorkspaceData = Awaited<ReturnType<typeof getWorkspace>>;
```

### Branded Types

**Pattern**: Prevent type confusion

```typescript
// ✅ Branded types
type WorkspaceId = string & { __brand: 'WorkspaceId' };
type UserId = string & { __brand: 'UserId' };
type DocId = string & { __brand: 'DocId' };

function getWorkspace(id: WorkspaceId) { }
function getUser(id: UserId) { }

const workspaceId = 'ws-123' as WorkspaceId;
const userId = 'user-456' as UserId;

getWorkspace(workspaceId); // ✅ OK
getWorkspace(userId);      // ❌ Type error!
```

---

## React Patterns

### Component Structure

**Convention**: Functional components with hooks

```typescript
import { useState, useEffect } from 'react';
import { useService } from '@toeverything/infra';
import { WorkspaceService } from '@affine/core/modules/workspace';
import * as styles from './workspace-card.css';

interface WorkspaceCardProps {
  workspaceId: string;
  onClick?: (id: string) => void;
}

export function WorkspaceCard({ workspaceId, onClick }: WorkspaceCardProps) {
  // 1. Hooks (services, state, effects)
  const workspaceService = useService(WorkspaceService);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    // Load workspace data
  }, [workspaceId]);

  // 2. Event handlers
  const handleClick = () => {
    onClick?.(workspaceId);
  };

  // 3. Early returns
  if (loading) {
    return <Skeleton />;
  }

  // 4. Render
  return (
    <div className={styles.card} onClick={handleClick}>
      <h3>{workspace.name}</h3>
    </div>
  );
}
```

### Props Interface

**Convention**: Name with `Props` suffix, define inline

```typescript
// ✅ Good - Inline interface
interface WorkspaceCardProps {
  workspace: Workspace;
  selected?: boolean;
  onSelect?: (id: string) => void;
}

export function WorkspaceCard(props: WorkspaceCardProps) { }

// ❌ Bad - Exported props type (unless reused)
export type WorkspaceCardProps = { ... };

// ❌ Bad - Anonymous props
export function WorkspaceCard({ workspace, selected }: {
  workspace: Workspace;
  selected?: boolean;
}) { }
```

### Children Prop

**Convention**: Use `ReactNode` type

```typescript
import type { ReactNode } from 'react';

interface CardProps {
  children: ReactNode;
  header?: ReactNode;
}

export function Card({ children, header }: CardProps) {
  return (
    <div>
      {header && <div className="header">{header}</div>}
      <div className="body">{children}</div>
    </div>
  );
}
```

### Event Handlers

**Convention**: `handle*` for handlers, `on*` for props

```typescript
interface ButtonProps {
  onClick?: (e: MouseEvent) => void; // Prop
  onSubmit?: () => void;
}

export function Button({ onClick, onSubmit }: ButtonProps) {
  // Handler
  const handleClick = (e: MouseEvent) => {
    console.log('Button clicked');
    onClick?.(e);
  };

  const handleSubmit = () => {
    console.log('Submitting');
    onSubmit?.();
  };

  return <button onClick={handleClick}>Click Me</button>;
}
```

### Conditional Rendering

**Pattern**: Use early returns and `&&`

```typescript
// ✅ Good - Early return
function UserProfile({ user }: { user: User | null }) {
  if (!user) {
    return <div>Please sign in</div>;
  }

  return <div>Hello, {user.name}!</div>;
}

// ✅ Good - Short circuit
function Notification({ message }: { message?: string }) {
  return (
    <div>
      {message && <div className="notification">{message}</div>}
    </div>
  );
}

// ❌ Bad - Nested ternaries
function Status({ loading, error, data }) {
  return loading ? <Spinner /> : error ? <Error /> : data ? <Content /> : null;
}
```

---

## Jotai Patterns

### Atom Naming

**Convention**: Descriptive names with `Atom` suffix

```typescript
// ✅ Good
const workspacesAtom = atom<Workspace[]>([]);
const activeWorkspaceIdAtom = atom<string | null>(null);
const isLoadingAtom = atom(false);

// ❌ Bad
const ws = atom([]); // Too short
const activeId = atom(null); // Missing "Atom" suffix
const loading = atom(false); // Could conflict with useState
```

### Derived Atoms

**Pattern**: Read-only derived atoms

```typescript
// Base atoms
const workspacesAtom = atom<Workspace[]>([]);
const activeWorkspaceIdAtom = atom<string | null>(null);

// ✅ Derived atom (read-only)
const activeWorkspaceAtom = atom(get => {
  const workspaces = get(workspacesAtom);
  const id = get(activeWorkspaceIdAtom);
  return workspaces.find(w => w.id === id) ?? null;
});

// ✅ Derived with async
const currentUserAtom = atom(async get => {
  const auth = get(authServiceAtom);
  return await auth.getCurrentUser();
});
```

### Write-Only Atoms (Actions)

**Pattern**: `null` for read, action for write

```typescript
// ✅ Action atom
const setActiveWorkspaceAtom = atom(
  null, // No read
  (get, set, workspaceId: string) => {
    set(activeWorkspaceIdAtom, workspaceId);

    // Side effect
    localStorage.setItem('lastWorkspace', workspaceId);
  }
);

// Usage
const setActive = useSetAtom(setActiveWorkspaceAtom);
setActive('workspace-123');
```

### Atom Families

**Pattern**: Scoped atoms (one per ID)

```typescript
import { atomFamily } from 'jotai/utils';

// ✅ Atom family - one atom per workspace
const workspaceFamily = atomFamily((workspaceId: string) =>
  atom(async () => {
    const service = get(workspaceServiceAtom);
    return await service.getWorkspace(workspaceId);
  })
);

// Usage
function WorkspaceView({ workspaceId }: { workspaceId: string }) {
  const workspace = useAtomValue(workspaceFamily(workspaceId));
  return <div>{workspace.name}</div>;
}
```

---

## Service Patterns

### Service Structure

**Convention**: Single responsibility, injected dependencies

```typescript
import { Service } from '@toeverything/infra';
import { Models } from '../../models';
import { QuotaService } from '../quota';

export class WorkspaceService extends Service {
  constructor(
    private readonly models: Models,
    private readonly quota: QuotaService,
  ) {
    super();
  }

  async createWorkspace(userId: string, name: string): Promise<Workspace> {
    // Check quota
    await this.quota.checkWorkspaceQuota(userId);

    // Create workspace
    return await this.models.workspace.create({
      data: { name, ownerId: userId },
    });
  }

  async getWorkspace(id: string): Promise<Workspace | null> {
    return await this.models.workspace.findUnique({
      where: { id },
    });
  }
}
```

### Service Methods

**Convention**: `async` for I/O, synchronous for logic

```typescript
export class WorkspaceService extends Service {
  // ✅ Async for database/network
  async loadWorkspace(id: string): Promise<Workspace> {
    return await this.models.workspace.findUnique({ where: { id } });
  }

  // ✅ Sync for pure logic
  validateWorkspaceName(name: string): boolean {
    return name.length >= 1 && name.length <= 100;
  }

  // ❌ Don't make sync methods async unnecessarily
  async validateWorkspaceName(name: string): Promise<boolean> {
    return name.length >= 1 && name.length <= 100; // No I/O!
  }
}
```

---

## Error Handling

### Custom Errors

**Pattern**: Extend `Error` with context

```typescript
// ✅ Custom error class
export class WorkspaceNotFoundError extends Error {
  constructor(public readonly workspaceId: string) {
    super(`Workspace ${workspaceId} not found`);
    this.name = 'WorkspaceNotFoundError';
  }
}

// Usage
async function getWorkspace(id: string) {
  const workspace = await db.workspace.findUnique({ where: { id } });

  if (!workspace) {
    throw new WorkspaceNotFoundError(id);
  }

  return workspace;
}

// Catch
try {
  const workspace = await getWorkspace('123');
} catch (error) {
  if (error instanceof WorkspaceNotFoundError) {
    console.error('Workspace not found:', error.workspaceId);
  }
}
```

### Error Boundaries (React)

**Pattern**: Wrap components in error boundaries

```typescript
import { ErrorBoundary } from 'react-error-boundary';

function App() {
  return (
    <ErrorBoundary
      fallback={<ErrorView />}
      onError={(error, info) => {
        logger.error('React error:', error, info);
      }}
    >
      <AppContent />
    </ErrorBoundary>
  );
}
```

---

## Testing Conventions

### Test Structure

**Pattern**: Arrange-Act-Assert

```typescript
import { describe, it, expect } from 'vitest';

describe('WorkspaceService', () => {
  it('should create a new workspace', async () => {
    // Arrange
    const service = new WorkspaceService();
    const userId = 'user-123';
    const name = 'My Workspace';

    // Act
    const workspace = await service.createWorkspace(userId, name);

    // Assert
    expect(workspace.name).toBe(name);
    expect(workspace.ownerId).toBe(userId);
  });
});
```

### Test Naming

**Convention**: `should` or `when`/`then` pattern

```typescript
// ✅ Good - Clear intent
it('should throw error when workspace not found', () => { });
it('should create workspace with default settings', () => { });

// ✅ Alternative - when/then
it('when user is not authenticated, then throws error', () => { });

// ❌ Bad - Unclear
it('test workspace creation', () => { });
it('workspace error', () => { });
```

---

## Git & PR Conventions

### Commit Messages

**Convention**: Conventional Commits

```
type(scope): subject

body (optional)

footer (optional)
```

**Types**:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `refactor`: Code refactoring
- `test`: Adding tests
- `chore`: Build/tooling changes

**Examples**:

```
feat(workspace): add workspace sharing functionality

- Add ShareWorkspaceDialog component
- Implement share permissions (view/edit)
- Add backend API for sharing

Closes #123
```

```
fix(editor): resolve cursor position bug in collaborative editing

When two users edited the same block simultaneously, cursor positions
were incorrectly calculated.

Fixed by using Yjs awareness protocol for cursor tracking.
```

### Branch Naming

**Convention**: `type/short-description`

```
feat/workspace-sharing
fix/editor-cursor-bug
docs/architecture-guide
refactor/storage-layer
```

### Pull Request Template

**Structure**:

```markdown
## Summary
Brief description of changes

## Changes
- Added X
- Fixed Y
- Refactored Z

## Test Plan
- [ ] Unit tests pass
- [ ] E2E tests pass
- [ ] Manually tested feature

## Screenshots (if applicable)
[images]

## Breaking Changes
None / Describe if any
```

---

## Summary

**Key Takeaways**:

1. **Consistency matters** - Follow existing patterns
2. **TypeScript strict mode** - No `any`, explicit types
3. **Naming conventions** - PascalCase classes, camelCase variables, UPPER_SNAKE constants
4. **File organization** - Co-locate related code
5. **React patterns** - Functional components, hooks, early returns
6. **Jotai atoms** - Descriptive names with `Atom` suffix
7. **Services** - Single responsibility, DI
8. **Testing** - Arrange-Act-Assert, clear names
9. **Git** - Conventional commits, descriptive PRs

**Next**: See `HOW_TO_GUIDE.md` for step-by-step implementation guides.

---

*This guide helps maintain consistency across the AFFiNE codebase.*
