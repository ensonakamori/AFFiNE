# AFFiNE Project Structure Guide

**Documented:** November 2025
**Tech Stack Research:** [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md)
**Prerequisites:** [Getting Started](./GETTING_STARTED.md), [Architecture Overview](./ARCHITECTURE_OVERVIEW.md)

> **Goal:** Understand where everything is in the codebase and how to navigate it efficiently.

---

## Table of Contents

- [Overview](#overview)
- [Repository Root](#repository-root)
- [Packages Directory](#packages-directory)
- [BlockSuite Directory](#blocksuite-directory)
- [Tests Directory](#tests-directory)
- [Tools & Scripts](#tools--scripts)
- [Configuration Files](#configuration-files)
- [Finding What You Need](#finding-what-you-need)
- [Navigation Tips](#navigation-tips)
- [Next Steps](#next-steps)

---

## Overview

AFFiNE is organized as a **monorepo** using **Yarn Workspaces**. This means multiple packages live in one repository, sharing dependencies and build tools.

**Total Packages:** 100+ workspaces
**Lines of Code:** ~500k+ (TypeScript, Rust, CSS)
**Main Languages:** TypeScript (90%), Rust (8%), CSS (2%)

### Monorepo Structure

```
AFFiNE/
├── packages/          # Application code
│   ├── frontend/     # Web, desktop, mobile apps
│   ├── backend/      # Server and native modules
│   └── common/       # Shared code
├── blocksuite/        # Editor framework (separate project)
├── tests/             # E2E and integration tests
├── tools/             # Build tools and utilities
├── scripts/           # Automation scripts
└── docs/              # Documentation
```

🧠 **Mental Model:** Think of this as multiple npm packages in one repo. Each `package.json` is a workspace.

---

## Repository Root

### Root Structure

```
AFFiNE/
├── .cargo/            # Rust configuration
├── .codesandbox/      # CodeSandbox config
├── .devcontainer/     # VS Code dev container
├── .docker/           # Docker compose files for local dev
├── .github/           # GitHub Actions CI/CD
├── .husky/            # Git hooks (pre-commit, etc.)
├── .vscode/           # VS Code workspace settings
├── .yarn/             # Yarn Berry cache and plugins
├── #claude/           # Claude Code configuration
├── blocksuite/        # [See BlockSuite section](#blocksuite-directory)
├── docs/              # [See Docs section](#docs-directory)
├── packages/          # [See Packages section](#packages-directory)
├── scripts/           # [See Scripts section](#scripts-directory)
├── tests/             # [See Tests section](#tests-directory)
├── tools/             # [See Tools section](#tools--scripts)
├── .editorconfig      # Editor formatting rules
├── .gitattributes     # Git file handling rules
├── .gitignore         # Git ignore patterns
├── .npmrc             # npm configuration
├── .nvmrc             # Node version (fnm/nvm)
├── .prettierignore    # Prettier ignore patterns
├── .prettierrc        # Prettier configuration
├── .taplo.toml        # TOML formatter config
├── Cargo.toml         # Rust workspace definition
├── eslint.config.mjs  # ESLint 9 flat config
├── package.json       # Root package.json (workspaces)
├── README.md          # Project README
├── tsconfig.json      # TypeScript root config
├── vitest.config.ts   # Vitest configuration
└── yarn.lock          # Yarn lockfile
```

---

### Key Root Files

#### `package.json` - Workspace Root

```json
{
  "name": "@affine/monorepo",
  "private": true,
  "workspaces": [
    ".",
    "blocksuite/**/*",
    "packages/*/*",
    "packages/frontend/apps/*",
    "tools/*",
    "tests/*"
  ],
  "packageManager": "yarn@4.9.1"
}
```

**What it does:**
- Defines all workspace packages
- Root-level scripts (dev, build, test, lint)
- DevDependencies shared across all packages

**Common Commands:**
```bash
yarn dev              # Start all dev servers
yarn build            # Build all packages
yarn test             # Run all tests
yarn lint             # Lint all code
```

See: [package.json](../../package.json)

---

#### `Cargo.toml` - Rust Workspace

```toml
[workspace]
members = [
  "./packages/backend/native",
  "./packages/common/native",
  "./packages/common/y-octo/core",
  "./packages/common/y-octo/node",
  "./packages/frontend/native",
  # ... more Rust packages
]
```

**What it does:**
- Defines Rust workspace members
- Shared Rust dependencies
- Build profiles (dev, release)

See: [Cargo.toml](../../Cargo.toml)

---

#### `tsconfig.json` - TypeScript Root Config

```json
{
  "files": [],
  "references": [
    { "path": "./packages/frontend/core" },
    { "path": "./packages/backend/server" },
    // ... more TypeScript projects
  ]
}
```

**What it does:**
- Project references for incremental builds
- Shared TypeScript compiler options
- Path mappings for imports

See: [tsconfig.json](../../tsconfig.json)

---

## Packages Directory

The `packages/` directory contains all application code, organized by platform and responsibility.

```
packages/
├── backend/       # Server-side code
├── frontend/      # Client-side code (web, desktop, mobile)
└── common/        # Shared code (both frontend & backend)
```

---

### Backend Packages

#### `packages/backend/server/` - NestJS Backend

**Purpose:** Main server application (GraphQL API, WebSocket, authentication, database)

**Structure:**
```
server/
├── prisma/                 # Database schema and migrations
│   ├── schema.prisma      # Prisma data model
│   └── migrations/        # Migration history
├── scripts/               # Build and deployment scripts
├── src/
│   ├── __tests__/        # Unit tests
│   ├── app.module.ts     # Root NestJS module
│   ├── base/             # Base utilities (logger, cache, etc.)
│   ├── core/             # Core business modules
│   │   ├── auth/        # Authentication (JWT)
│   │   ├── user/        # User management
│   │   ├── workspaces/  # Workspace CRUD
│   │   ├── doc/         # Document operations
│   │   ├── sync/        # Real-time sync
│   │   ├── storage/     # File storage (S3)
│   │   ├── permission/  # Access control
│   │   ├── quota/       # Usage limits
│   │   └── ...          # More modules
│   ├── data/             # Seed data
│   ├── mails/            # Email templates (React Email)
│   ├── middleware/       # Express middleware
│   ├── models/           # Shared models
│   ├── plugins/          # NestJS plugins
│   │   ├── copilot/     # AI features
│   │   ├── payment/     # Stripe integration
│   │   └── ...
│   ├── schema.gql        # Generated GraphQL schema
│   └── server.ts         # Server entry point
├── .env.example           # Environment variables template
└── package.json
```

**Key Files:**
- **GraphQL Schema:** [src/schema.gql](../../packages/backend/server/src/schema.gql) - Auto-generated from TypeScript
- **App Module:** [src/app.module.ts](../../packages/backend/server/src/app.module.ts) - Dependency injection root
- **Auth Module:** [src/core/auth/](../../packages/backend/server/src/core/auth/) - JWT, sessions, OAuth
- **Prisma Schema:** [prisma/schema.prisma](../../packages/backend/server/prisma/schema.prisma) - Database models

**Usage:**
```bash
yarn workspace @affine/server dev    # Start server
yarn workspace @affine/server build  # Build for production
yarn affine server init              # Run migrations
```

See: [Backend Architecture Guide](./BACKEND_ARCHITECTURE.md)

---

#### `packages/backend/native/` - Rust Server Modules

**Purpose:** Performance-critical server operations in Rust

**What's in Rust:**
- PDF parsing
- DOCX parsing
- Image processing
- CRDT operations (y-octo integration)

**Structure:**
```
native/
├── benches/        # Rust benchmarks
├── src/
│   ├── lib.rs     # NAPI-RS bindings
│   └── ...        # Rust implementations
└── Cargo.toml     # Rust dependencies
```

See: [packages/backend/native/](../../packages/backend/native/)

---

### Frontend Packages

#### `packages/frontend/core/` - Main React Application

**Purpose:** Core frontend logic, state management, routing, business logic

**Structure:**
```
core/src/
├── __tests__/               # Component and integration tests
├── blocksuite/              # BlockSuite integration
│   ├── ai/                 # AI features integration
│   ├── block-suite-editor/ # Editor component wrapper
│   └── store-extensions/   # Custom BlockSuite extensions
├── components/              # React components
│   ├── affine/             # AFFiNE-specific components
│   ├── blocksuite/         # BlockSuite wrappers
│   ├── cloud/              # Cloud sync UI
│   ├── doc-properties/     # Document metadata
│   ├── pure/               # Pure/presentational components
│   └── ...
├── desktop/                 # Desktop (Electron) specific
│   ├── dialogs/            # Electron native dialogs
│   └── pages/              # Desktop-only pages
├── mobile/                  # Mobile specific
│   ├── components/         # Mobile UI components
│   ├── dialogs/            # Mobile dialogs
│   └── views/              # Mobile view layouts
├── modules/                 # Feature modules (core architecture)
│   ├── ai-button/          # AI button feature
│   ├── app-sidebar/        # Sidebar module
│   ├── backup/             # Backup/restore
│   ├── cloud/              # Cloud sync
│   ├── collection/         # Collections (tags, filters)
│   ├── db/                 # Local database (IndexedDB)
│   ├── doc/                # Document management
│   ├── editor/             # Editor integration
│   ├── workspace/          # Workspace management
│   └── ...                 # 60+ modules
├── types/                   # TypeScript type definitions
├── utils/                   # Utility functions
└── index.tsx                # App entry point
```

**Module Example (workspace):**
```
modules/workspace/
├── entities/               # Data entities (models)
│   └── workspace.ts
├── services/               # Business logic
│   └── workspace.service.ts
├── repositories/           # Data access
│   └── workspace.repository.ts
├── views/                  # React components
│   └── workspace-list.tsx
├── atoms/                  # Jotai state
│   └── workspace.atoms.ts
└── index.ts                # Module exports
```

**Key Directories:**
- **Modules:** [src/modules/](../../packages/frontend/core/src/modules/) - 60+ feature modules
- **Components:** [src/components/](../../packages/frontend/core/src/components/) - Reusable UI
- **Desktop:** [src/desktop/](../../packages/frontend/core/src/desktop/) - Electron-specific code
- **Mobile:** [src/mobile/](../../packages/frontend/core/src/mobile/) - Mobile-specific code

See: [Frontend Architecture Guide](./FRONTEND_ARCHITECTURE.md)

---

#### `packages/frontend/component/` - UI Component Library

**Purpose:** Reusable UI components used across the application

**Structure:**
```
component/src/
├── components/           # Component implementations
│   ├── auth-components/ # Login, signup forms
│   ├── button/          # Button variants
│   ├── card/            # Card component
│   ├── date-picker/     # Date picker
│   ├── modal/           # Modal dialogs
│   ├── menu/            # Dropdown menus
│   ├── sidebar/         # Sidebar layout
│   ├── table/           # Data tables
│   └── ...
├── hooks/                # Custom React hooks
├── ui/                   # Radix UI wrappers
│   ├── button/
│   ├── dialog/
│   ├── dropdown/
│   └── ...
└── theme/                # Theme configuration
```

**Design System:**
- Built on **Radix UI** primitives (accessible, unstyled)
- Styled with **Vanilla Extract** (type-safe CSS-in-JS)
- Consistent design tokens (colors, spacing, typography)

**Example Component:**
```typescript
// components/button/button.tsx
import { button } from './button.css'

export const Button = ({ children, ...props }) => {
  return (
    <button className={button} {...props}>
      {children}
    </button>
  )
}
```

See: [packages/frontend/component/](../../packages/frontend/component/)

---

#### `packages/frontend/apps/` - Platform Entry Points

**Purpose:** Platform-specific application wrappers

**Structure:**
```
apps/
├── web/                  # Web app (Vite)
│   ├── public/          # Static assets
│   ├── src/
│   │   └── index.tsx    # Web entry point
│   ├── index.html       # HTML template
│   └── vite.config.ts   # Vite configuration
├── electron/             # Desktop main process
│   ├── scripts/         # Build scripts
│   ├── src/
│   │   ├── main.ts      # Electron main process
│   │   ├── helper/      # Helper processes
│   │   └── windows/     # Window management
│   └── resources/       # App icons, assets
├── electron-renderer/    # Desktop renderer
│   └── index.html       # Electron renderer HTML
├── mobile/               # Mobile app (Capacitor)
│   ├── src/
│   │   └── index.tsx    # Mobile entry point
│   └── capacitor.config.ts
├── ios/                  # iOS-specific
│   ├── App/             # Swift/Objective-C code
│   └── capacitor.config.json
└── android/              # Android-specific
    ├── app/             # Kotlin/Java code
    └── capacitor.config.json
```

**Platform Sharing:**
- **Web, Electron, Mobile:** Share 95%+ of code from `@affine/core`
- **Platform-specific:** Only entry points and native integrations differ

**Build Commands:**
```bash
# Web
yarn workspace @affine/web dev
yarn workspace @affine/web build

# Desktop
yarn workspace @affine/electron dev
yarn workspace @affine/electron build

# Mobile
yarn workspace @affine/mobile dev
```

See: [packages/frontend/apps/](../../packages/frontend/apps/)

---

#### `packages/frontend/native/` - Rust Frontend Modules

**Purpose:** Performance-critical frontend operations in Rust

**What's in Rust:**
- Local database (SQLite for mobile via `nbstore`)
- Media capture processing
- File system operations
- CRDT operations

**Structure:**
```
native/
├── __tests__/        # JavaScript tests for bindings
├── media_capture/    # Screen/audio capture (Rust)
├── nbstore/          # Local storage (Rust)
├── schema/           # Data schemas
├── sqlite_v1/        # SQLite wrapper
├── src/
│   ├── lib.rs       # NAPI-RS bindings
│   └── ...          # Rust implementations
└── Cargo.toml
```

See: [packages/frontend/native/](../../packages/frontend/native/)

---

#### `packages/frontend/i18n/` - Internationalization

**Purpose:** Translation files for all supported languages

**Structure:**
```
i18n/src/
├── resources/           # Translation JSON files
│   ├── en.json         # English
│   ├── zh-Hans.json    # Simplified Chinese
│   ├── zh-Hant.json    # Traditional Chinese
│   ├── ja.json         # Japanese
│   ├── ko.json         # Korean
│   ├── de.json         # German
│   ├── fr.json         # French
│   ├── es.json         # Spanish
│   ├── pt-BR.json      # Brazilian Portuguese
│   └── ...
└── utils/               # i18n utilities
```

**Translation Example:**
```json
// en.json
{
  "com.affine.welcome": "Welcome to AFFiNE",
  "com.affine.new-page": "New Page",
  "com.affine.settings.title": "Settings"
}
```

**Supported Languages:** 15+ languages

See: [packages/frontend/i18n/](../../packages/frontend/i18n/)

---

### Common Packages (Shared Code)

#### `packages/common/infra/` - Infrastructure Framework

**Purpose:** Shared infrastructure code (dependency injection, framework utilities)

**Structure:**
```
infra/src/
├── framework/          # DI framework
├── lifecycle/          # Lifecycle management
├── modules/            # Shared modules
└── ...
```

This is the foundation for the modular architecture used in `@affine/core`.

See: [packages/common/infra/](../../packages/common/infra/)

---

#### `packages/common/graphql/` - GraphQL Types

**Purpose:** Shared GraphQL schema and TypeScript types

**Generated from:** Backend GraphQL schema via codegen

**Usage:**
```typescript
import type { UserQuery } from '@affine/graphql'

const user: UserQuery['user'] = await fetchUser()
```

See: [packages/common/graphql/](../../packages/common/graphql/)

---

#### `packages/common/nbstore/` - Storage Abstraction

**Purpose:** Unified storage interface for local and cloud storage

**Implementations:**
- IndexedDB (browser)
- SQLite (mobile)
- Cloud sync layer

See: [packages/common/nbstore/](../../packages/common/nbstore/)

---

#### `packages/common/y-octo/` - CRDT Implementation

**Purpose:** Rust-based CRDT operations for high performance

**Structure:**
```
y-octo/
├── core/        # Core CRDT logic (Rust)
├── node/        # Node.js bindings (NAPI-RS)
└── utils/       # Utility functions
```

**Why Rust?**
- 🚀 100x faster than pure JavaScript for CRDT operations
- 💾 Lower memory footprint
- 🔒 Memory safety guarantees

See: [packages/common/y-octo/](../../packages/common/y-octo/)

---

## BlockSuite Directory

BlockSuite is AFFiNE's editor framework, maintained as a **separate project** but included in the monorepo.

**Repository:** https://github.com/toeverything/blocksuite

### BlockSuite Structure

```
blocksuite/
├── affine/              # AFFiNE-specific implementations
│   ├── blocks/         # Block implementations
│   │   ├── paragraph/ # Text blocks
│   │   ├── image/     # Image blocks
│   │   ├── database/  # Database blocks
│   │   ├── code/      # Code blocks
│   │   └── ...        # 20+ block types
│   ├── widgets/        # UI widgets
│   │   ├── toolbar/
│   │   ├── slash-menu/
│   │   └── ...
│   ├── gfx/            # Graphics/drawing
│   │   ├── brush/     # Drawing tools
│   │   ├── connector/ # Connection lines
│   │   └── shape/     # Shapes
│   ├── components/     # Shared components
│   ├── shared/         # Shared utilities
│   ├── model/          # Data models
│   └── foundation/     # Base classes
├── framework/           # Core framework
│   ├── store/          # Document store (Yjs)
│   │   └── src/
│   │       ├── doc.ts # Y.Doc wrapper
│   │       └── ...
│   ├── std/            # Standard library
│   │   ├── gfx/       # Graphics primitives
│   │   └── ...
│   └── global/         # Global configuration
├── playground/          # Development playground
└── docs/                # BlockSuite documentation
```

### Key BlockSuite Concepts

#### Blocks

Every content element is a "block":

```typescript
// Simplified block structure
class ParagraphBlock extends BlockElement {
  static tag = 'affine-paragraph'

  // Yjs data model
  static schema = {
    type: 'text',
    content: Y.Text,
  }

  render() {
    return html`
      <div class="affine-paragraph-block">
        ${this.content}
      </div>
    `
  }
}
```

#### Block Types

- **Text Blocks:** Paragraph, Heading, List, Callout
- **Media Blocks:** Image, Video, File, Bookmark
- **Embed Blocks:** Link Preview, Figma, YouTube
- **Database Blocks:** Table, Kanban, Gallery
- **Canvas Elements:** Shapes, Connectors, Text on canvas

**Total Block Types:** 20+ and extensible

See: [blocksuite/affine/blocks/](../../blocksuite/affine/blocks/)

---

#### Edgeless Mode

**Purpose:** Infinite canvas mode (like Miro/Figma)

**Features:**
- Freeform positioning
- Drawing tools
- Shapes and connectors
- Embed any block on canvas

See: [BlockSuite Editor Guide](./BLOCKSUITE_EDITOR.md)

---

## Tests Directory

**Purpose:** E2E tests, integration tests, and test utilities

```
tests/
├── affine-local/        # Local-first tests (no server)
├── affine-cloud/        # Tests with server
├── affine-mobile/       # Mobile app tests
├── blocksuite/          # BlockSuite editor tests
├── kit/                 # Test utilities and helpers
└── ...
```

**Testing Stack:**
- **E2E:** Playwright
- **Unit:** Vitest
- **Fixtures:** Shared test data

**Run Tests:**
```bash
yarn test                              # All tests
yarn workspace @affine-test/affine-local e2e   # E2E tests
yarn workspace @affine/core test       # Unit tests
```

See: [Testing Guide](./TESTING_GUIDE.md)

---

## Tools & Scripts

### `tools/` Directory

**Purpose:** Build tools, CLI utilities, code generation

```
tools/
├── cli/               # AFFiNE CLI (`yarn affine`)
├── utils/             # Shared utilities
├── commitlint/        # Commit message linting
└── ...
```

**AFFiNE CLI:**
```bash
yarn affine --help
yarn affine dev
yarn affine build
yarn affine server dev
```

See: [tools/cli/](../../tools/cli/)

---

### `scripts/` Directory

**Purpose:** Automation scripts for CI/CD and maintenance

```
scripts/
├── build.sh           # Production build
├── setup/             # Setup scripts
└── ...
```

---

## Configuration Files

### Build & Bundle

| File | Purpose |
|------|---------|
| `vite.config.ts` | Vite bundler configuration |
| `tsconfig.json` | TypeScript compiler options |
| `Cargo.toml` | Rust workspace and dependencies |

### Code Quality

| File | Purpose |
|------|---------|
| `eslint.config.mjs` | ESLint 9 flat config |
| `.prettierrc` | Prettier code formatting |
| `.taplo.toml` | TOML file formatting |

### Git & Versioning

| File | Purpose |
|------|---------|
| `.gitignore` | Git ignore patterns |
| `.gitattributes` | Git file handling (line endings, diffs) |
| `.husky/` | Git hooks (pre-commit linting) |

### Editor

| File | Purpose |
|------|---------|
| `.editorconfig` | Editor formatting (indentation, etc.) |
| `.vscode/` | VS Code workspace settings |

---

## Docs Directory

**Purpose:** Project documentation (including this learning path!)

```
docs/
├── contributing/        # Contribution guidelines
├── learning/            # 📚 Learning documentation (this guide!)
│   ├── README.md       # Learning path hub
│   ├── GETTING_STARTED.md
│   ├── ARCHITECTURE_OVERVIEW.md
│   ├── PROJECT_STRUCTURE.md  # ← You are here!
│   ├── TECH_STACK_RESEARCH.md
│   └── ...             # More guides
├── reference/           # API reference
├── BUILDING.md          # Build instructions
├── CODE_OF_CONDUCT.md
├── CONTRIBUTING.md
└── ...
```

---

## Finding What You Need

### Quick Reference Table

| I want to... | Go to... |
|-------------|----------|
| **Add a React component** | `packages/frontend/component/src/components/` |
| **Add a feature module** | `packages/frontend/core/src/modules/` |
| **Edit GraphQL schema** | `packages/backend/server/src/core/*/` (schema auto-generated) |
| **Add a database model** | `packages/backend/server/prisma/schema.prisma` |
| **Add a new block type** | `blocksuite/affine/blocks/` |
| **Edit translations** | `packages/frontend/i18n/src/resources/` |
| **Add a Rust module** | `packages/*/native/src/` |
| **Add E2E tests** | `tests/affine-local/` or `tests/affine-cloud/` |
| **Modify desktop app** | `packages/frontend/apps/electron/` |
| **Modify mobile app** | `packages/frontend/apps/mobile/` |
| **Add build tooling** | `tools/cli/` |

---

### Navigation Patterns

#### Pattern 1: Find by Feature

**Example:** Find code for "workspace creation"

```bash
# 1. Search in modules
grep -r "createWorkspace" packages/frontend/core/src/modules/

# 2. Check service layer
ls packages/frontend/core/src/modules/workspace/services/

# 3. Check backend
grep -r "createWorkspace" packages/backend/server/src/core/workspaces/
```

#### Pattern 2: Find by File Type

**TypeScript Components:**
```bash
find packages/frontend -name "*.tsx" | grep -i "component-name"
```

**Rust Modules:**
```bash
find packages -name "*.rs" | grep -i "module-name"
```

**GraphQL Resolvers:**
```bash
find packages/backend/server/src -name "*.resolver.ts"
```

#### Pattern 3: Find by Import

**Example:** Who imports `WorkspaceService`?

```bash
grep -r "WorkspaceService" packages/frontend/core/src/
```

---

### IDE Navigation Tips

#### VS Code

**Jump to Definition:** Cmd+Click (Mac) or Ctrl+Click (Windows/Linux)
**Find All References:** Shift+F12
**Go to Symbol:** Cmd+Shift+O (Mac) or Ctrl+Shift+O (Windows/Linux)
**Search Workspace:** Cmd+Shift+F (Mac) or Ctrl+Shift+F (Windows/Linux)

#### Workspace Recommendations

Install these VS Code extensions (recommended in `.vscode/extensions.json`):
- **ESLint** - Linting
- **Prettier** - Formatting
- **Rust Analyzer** - Rust language support
- **Prisma** - Prisma schema support

---

## Navigation Tips

### Tip 1: Use Glob Tool for Finding Files

```typescript
// Example: Find all atom files
packages/frontend/core/src/**/*.atom.ts

// Example: Find all service files
packages/*/src/**/services/*.service.ts
```

### Tip 2: Follow the Imports

Start at the entry point and follow imports:

```typescript
// 1. Entry point
packages/frontend/apps/web/src/index.tsx

// 2. Imports @affine/core
import { App } from '@affine/core'

// 3. Follow to core
packages/frontend/core/src/index.tsx

// 4. Follow to modules
packages/frontend/core/src/modules/
```

### Tip 3: Check Package Dependencies

Look at `package.json` to understand dependencies:

```json
{
  "dependencies": {
    "@affine/component": "workspace:*",  // → packages/frontend/component
    "@affine/graphql": "workspace:*",    // → packages/common/graphql
    "@affine/server": "workspace:*"      // → packages/backend/server
  }
}
```

The `workspace:*` means it's a local package in the monorepo.

---

## Next Steps

### Deep Dive into Specific Areas

- **Tech Stack Details** → [Tech Stack Guide](./TECH_STACK_GUIDE.md)
- **Frontend Deep Dive** → [Frontend Architecture](./FRONTEND_ARCHITECTURE.md)
- **Backend Deep Dive** → [Backend Architecture](./BACKEND_ARCHITECTURE.md)
- **Editor Deep Dive** → [BlockSuite Editor](./BLOCKSUITE_EDITOR.md)

### Understand Data Flow

- **How Data Moves** → [Data Flow Guide](./DATA_FLOW_GUIDE.md)

### Start Coding

- **Code Patterns** → [Patterns & Conventions](./PATTERNS_AND_CONVENTIONS.md)
- **How-To Tasks** → [How-To Guide](./HOW_TO_GUIDE.md)
- **First Contribution** → [First Contributions](./FIRST_CONTRIBUTIONS.md)

---

## Key Takeaways

✅ **Monorepo** with 100+ packages using Yarn Workspaces
✅ **Three main categories:** Frontend, Backend, Common (shared)
✅ **BlockSuite** is a separate project for the editor framework
✅ **Modular architecture** with 60+ feature modules in frontend
✅ **Rust native modules** for performance in `packages/*/native/`
✅ **Platform-specific code** in `packages/frontend/apps/`
✅ **Clear separation** between presentation, business logic, and data layers

---

## Quick Directory Reference

```
📁 AFFiNE/
├── 📦 packages/
│   ├── 🖥️  backend/server/     → NestJS server
│   ├── ⚙️  backend/native/     → Rust server modules
│   ├── 🎨 frontend/core/       → Main React app
│   ├── 🧩 frontend/component/  → UI components
│   ├── 🌐 frontend/apps/       → Platform entry points
│   ├── 🦀 frontend/native/     → Rust frontend modules
│   ├── 🌍 frontend/i18n/       → Translations
│   ├── 🔧 common/infra/        → DI framework
│   ├── 📡 common/graphql/      → GraphQL types
│   └── 🔗 common/y-octo/       → CRDT (Rust)
├── 📝 blocksuite/              → Editor framework
│   ├── 🧱 affine/blocks/       → Block implementations
│   ├── 🎛️  affine/widgets/     → UI widgets
│   └── ⚡ framework/           → Core framework
├── ✅ tests/                   → E2E & integration tests
├── 🛠️  tools/                  → CLI & build tools
└── 📚 docs/learning/           → Learning documentation
```

---

**🎉 You now know where everything is in the AFFiNE codebase!**

Continue to [Tech Stack Guide](./TECH_STACK_GUIDE.md) to understand the technologies used.

---

*Last updated: November 18, 2025*
*See [README.md](./README.md) for the complete learning path.*
