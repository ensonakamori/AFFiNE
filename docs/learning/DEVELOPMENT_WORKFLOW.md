# Development Workflow

> **Purpose**: Daily development workflow, tooling setup, and best practices for AFFiNE development.

---

## Table of Contents

1. [Daily Workflow](#daily-workflow)
2. [IDE Setup](#ide-setup)
3. [Running the App](#running-the-app)
4. [Testing Workflow](#testing-workflow)
5. [Debugging](#debugging)
6. [Code Review](#code-review)
7. [Deployment](#deployment)

---

## Daily Workflow

### Morning Routine

```bash
# 1. Pull latest changes
git checkout canary
git pull origin canary

# 2. Create feature branch
git checkout -b feat/my-feature

# 3. Install dependencies (if package.json changed)
yarn install

# 4. Build native modules (if Rust code changed)
yarn affine @affine/native build
yarn affine @affine/server-native build

# 5. Start development server
yarn dev
```

### Making Changes

```bash
# 1. Make code changes
# 2. See changes hot-reload in browser (http://localhost:5173)
# 3. Run type checking
yarn typecheck

# 4. Run linter
yarn lint

# 5. Fix auto-fixable issues
yarn lint:fix

# 6. Run affected tests
yarn test:unit -- packages/frontend/core/src/modules/my-feature
```

### End of Day

```bash
# 1. Commit changes
git add .
git commit -m "feat(my-feature): add new functionality"

# 2. Push to remote
git push -u origin feat/my-feature

# 3. Create pull request (if ready)
gh pr create --title "feat: My Feature" --body "Description"
```

---

## IDE Setup

### VS Code (Recommended)

**Extensions**:

```json
// .vscode/extensions.json
{
  "recommendations": [
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode",
    "bradlc.vscode-tailwindcss",
    "prisma.prisma",
    "graphql.vscode-graphql",
    "rust-lang.rust-analyzer"
  ]
}
```

**Settings**:

```json
// .vscode/settings.json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "typescript.tsdk": "node_modules/typescript/lib",
  "typescript.enablePromptUseWorkspaceTsdk": true,
  "files.associations": {
    "*.css.ts": "typescript"
  }
}
```

**Keyboard Shortcuts**:

- `Cmd+P` - Quick file open
- `Cmd+Shift+F` - Search across files
- `F12` - Go to definition
- `Cmd+Click` - Go to definition
- `Shift+F12` - Find all references
- `F2` - Rename symbol

### WebStorm

**Configuration**:

1. Enable Prettier: Preferences → Languages & Frameworks → JavaScript → Prettier → On save
2. Enable ESLint: Preferences → Languages & Frameworks → JavaScript → Code Quality Tools → ESLint → Automatic
3. Set Node version: Preferences → Languages & Frameworks → Node.js → 22+
4. Enable TypeScript: Preferences → Languages & Frameworks → TypeScript → Use TypeScript service

---

## Running the App

### Development Modes

**1. Frontend Only** (no backend):

```bash
yarn dev
# Opens http://localhost:5173
# Uses mock data, no database required
```

**2. Full Stack** (frontend + backend):

```bash
# Terminal 1: Start backend
cd packages/backend/server
yarn dev

# Terminal 2: Start frontend
yarn dev
```

**3. Desktop App** (Electron):

```bash
yarn affine @affine/electron dev
# Opens Electron window
```

**4. Mobile App** (iOS simulator):

```bash
cd packages/frontend/apps/ios
yarn dev
# Opens iOS simulator
```

### Environment Variables

```bash
# .env.local (create in root)
DATABASE_URL="postgresql://postgres:password@localhost:5432/affine"
REDIS_URL="redis://localhost:6379"
NODE_ENV="development"
```

### Ports

| Service | Port | URL |
|---------|------|-----|
| Frontend | 5173 | http://localhost:5173 |
| Backend | 3000 | http://localhost:3000 |
| GraphQL Playground | 3000 | http://localhost:3000/graphql |
| Prisma Studio | 5555 | http://localhost:5555 |

---

## Testing Workflow

### Unit Tests

```bash
# Run all unit tests
yarn test:unit

# Run specific file
yarn test:unit packages/frontend/core/src/modules/workspace/workspace.spec.ts

# Watch mode
yarn test:unit --watch

# Coverage report
yarn test:unit --coverage
```

### Component Tests

```bash
# Run component tests
yarn test:component

# Specific component
yarn test:component packages/frontend/core/src/components/workspace-card.test.tsx
```

### E2E Tests

```bash
# Run all E2E tests
yarn test:e2e

# Specific test
yarn test:e2e packages/frontend/core/e2e/workspace.e2e.ts

# Headed mode (see browser)
yarn test:e2e --headed

# Debug mode
yarn test:e2e --debug
```

### Pre-Commit Checks

```bash
# Run all checks before committing
yarn precommit

# This runs:
# 1. Type checking
# 2. Linting
# 3. Unit tests (affected files)
# 4. Format check
```

---

## Debugging

### Frontend Debugging

**Chrome DevTools**:

1. Open DevTools (`F12`)
2. Sources tab → Add breakpoint
3. Trigger code path
4. Inspect variables

**React DevTools**:

1. Install React DevTools extension
2. Open Components tab
3. Select component
4. Inspect props/state

**Redux DevTools** (for Jotai):

```typescript
import { useAtomDevtools } from 'jotai-devtools';

function MyComponent() {
  const [state] = useAtom(myAtom);
  useAtomDevtools(myAtom);

  return <div>{state}</div>;
}
```

### Backend Debugging

**Node.js Inspector**:

```bash
# Start with inspector
node --inspect packages/backend/server/dist/index.js

# In Chrome: chrome://inspect
# Click "inspect" on target
```

**VS Code Debugger**:

```json
// .vscode/launch.json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Debug Backend",
      "program": "${workspaceFolder}/packages/backend/server/src/index.ts",
      "preLaunchTask": "tsc: build",
      "outFiles": ["${workspaceFolder}/packages/backend/server/dist/**/*.js"]
    }
  ]
}
```

### Database Debugging

**Prisma Studio**:

```bash
yarn prisma studio
# Opens http://localhost:5555
# Visual database browser
```

**Raw SQL**:

```bash
psql postgresql://postgres:password@localhost:5432/affine

# Or
yarn prisma db execute --stdin < query.sql
```

### Network Debugging

**GraphQL Requests**:

1. Open DevTools → Network
2. Filter: `graphql`
3. Click request
4. View Payload (query + variables)
5. View Response

**WebSocket**:

1. Network tab → Filter: `WS`
2. Click WebSocket connection
3. View Messages tab
4. Inspect frames

---

## Code Review

### Before Creating PR

**Checklist**:

- [ ] Run `yarn lint` - No linting errors
- [ ] Run `yarn typecheck` - No type errors
- [ ] Run `yarn test:unit` - All tests pass
- [ ] Run `yarn format` - Code formatted
- [ ] Update documentation (if needed)
- [ ] Add tests for new features
- [ ] Remove console.logs and debuggers
- [ ] Check for sensitive data (API keys, passwords)

### Creating PR

```bash
# Push branch
git push -u origin feat/my-feature

# Create PR
gh pr create \
  --title "feat: My Feature" \
  --body "## Summary

  Brief description

  ## Changes
  - Added X
  - Fixed Y

  ## Test Plan
  - Unit tests added
  - Manually tested

  ## Screenshots
  [images]"
```

### PR Review Process

1. **Automated checks run**:
   - Type checking
   - Linting
   - Tests
   - Build

2. **Code review**:
   - Team member reviews code
   - Leaves comments/suggestions
   - Approves or requests changes

3. **Address feedback**:
   - Make requested changes
   - Push new commits
   - Respond to comments

4. **Merge**:
   - Squash and merge (preferred)
   - Or rebase and merge

### After Merge

```bash
# Switch back to main branch
git checkout canary

# Pull latest (includes your changes)
git pull origin canary

# Delete feature branch
git branch -d feat/my-feature
git push origin --delete feat/my-feature
```

---

## Deployment

### Backend Deployment

**1. Build**:

```bash
cd packages/backend/server
yarn build
```

**2. Run migrations**:

```bash
yarn prisma migrate deploy
```

**3. Start server**:

```bash
NODE_ENV=production node dist/index.js
```

### Frontend Deployment

**1. Build**:

```bash
yarn build:web
# Output: packages/frontend/apps/web/dist
```

**2. Deploy to Vercel**:

```bash
vercel deploy packages/frontend/apps/web/dist
```

**3. Or Docker**:

```bash
docker build -t affine-web .
docker run -p 3000:3000 affine-web
```

### Desktop App Release

**1. Build**:

```bash
yarn affine @affine/electron build
```

**2. Create installer**:

```bash
yarn affine @affine/electron package
# Output: packages/frontend/apps/electron/out/
```

**3. Sign & notarize** (macOS):

```bash
yarn affine @affine/electron sign
```

**4. Upload to GitHub Releases**:

```bash
gh release create v1.0.0 \
  packages/frontend/apps/electron/out/AFFiNE-1.0.0.dmg \
  packages/frontend/apps/electron/out/AFFiNE-1.0.0.exe
```

---

## Summary

**Daily Workflow**:
1. Pull latest → Create branch
2. Make changes → Test locally
3. Commit → Push → Create PR
4. Review → Merge

**Key Commands**:
- `yarn dev` - Start development
- `yarn test:unit` - Run tests
- `yarn lint` - Check code style
- `yarn typecheck` - Check types
- `yarn build` - Production build

**Tools**:
- VS Code - IDE
- Chrome DevTools - Frontend debugging
- Prisma Studio - Database browser
- React DevTools - Component inspection

---

*Daily development workflow for AFFiNE contributors.*
