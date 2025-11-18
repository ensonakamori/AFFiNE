# Technology Stack Research (November 2025)

Research conducted: November 18, 2025

This document provides comprehensive research on all major technologies used in the AFFiNE project, comparing the versions currently in use with the latest available versions as of November 2025.

---

## Technologies Used in This Project

### React - v19.1.0

**Current Status (Nov 2025):**
- Latest stable: v19.2.0
- Project uses: v19.1.0
- Status: ⚠️ Slightly outdated (one minor version behind)

**Important Updates Since Jan 2025:**
- React 19.2 released October 1, 2025
- **New `<Activity>` API**: Hide and restore UI and internal state of children
- **useEffectEvent Hook**: Extract non-reactive logic into Effect Events
- **cacheSignal** for React Server Components (RSCs): Know when cache() lifetime is over
- **React Performance tracks**: Now appear in browser DevTools Performance panel
- **Partial Pre-rendering**: Added resume APIs for partial pre-rendering with Web Streams
- **Default useId prefix updated**: Changed from `:r:` to `_r_` for View Transitions support
- **Batching Suspense Boundaries for SSR**
- **Web Streams support for Node** in SSR
- **eslint-plugin-react-hooks v6** released

**What This Means for Learning:**
- AFFiNE uses React 19.1, which includes all major React 19 features (Actions, use() hook, etc.)
- The patterns in this project are current and represent modern React best practices
- React 19 is a stable production release with significant improvements over React 18

**Official Resources:**
- Docs: https://react.dev/
- React 19.2 Release Notes: https://react.dev/blog/2025/10/01/react-19-2
- Migration Guide: https://react.dev/blog/2024/12/05/react-19
- GitHub Releases: https://github.com/facebook/react/releases

---

### TypeScript - v5.7.2

**Current Status (Nov 2025):**
- Latest stable: v5.9.3
- Project uses: v5.7.2
- Status: ⚠️ Slightly outdated (two minor versions behind)

**Important Updates Since Jan 2025:**
- **TypeScript 5.7** (November 2024): Improved error reporting
- **TypeScript 5.8** (March 2025): Enhanced type inference and performance improvements
- **TypeScript 5.9** (August 2025): Latest stable with further type system enhancements
- TypeScript 6.0 planned as transition point for TypeScript 7.0 (native port in development)

**What This Means for Learning:**
- AFFiNE uses TypeScript 5.7.2, which is relatively current
- All modern TypeScript features are available (decorators, satisfies, const type parameters, etc.)
- The codebase demonstrates current TypeScript best practices for large-scale applications
- Minor version updates (5.8, 5.9) are primarily optimizations and minor enhancements

**Official Resources:**
- Docs: https://www.typescriptlang.org/docs/
- TypeScript 5.9 Announcement: https://devblogs.microsoft.com/typescript/announcing-typescript-5-9/
- Release Notes: https://github.com/microsoft/typescript/releases
- Handbook: https://www.typescriptlang.org/docs/handbook/intro.html

---

### Vite - v7.0.0

**Current Status (Nov 2025):**
- Latest stable: v7.0.0
- Project uses: v7.0.0
- Status: ✅ Current (latest version)

**Important Updates Since Jan 2025:**
- **Vite 7.0** released November 2025 - major version
- **Breaking: Node.js 20.19+, 22.12+ required** (Node 18 support dropped after EOL)
- **ESM-only distribution**: Allows use of `require(esm)` without flags
- **Browser target changed**: From 'modules' to 'baseline-widely-available' (pre-Nov 2022 browsers)
- **Sass legacy API removed**: Only modern Sass API supported
- **Hook-level changes**: `order` instead of `enforce`, `handler` instead of `transform` for transformIndexHtml
- Performance and build optimizations

**What This Means for Learning:**
- AFFiNE is using the latest Vite with all cutting-edge features
- Modern build tooling with best-in-class DX (developer experience)
- Fast HMR (Hot Module Replacement) and optimized production builds
- This represents current best practices for build tools in 2025

**Official Resources:**
- Docs: https://vite.dev/
- Vite 7.0 Announcement: https://vite.dev/blog/announcing-vite7
- Migration Guide: https://vite.dev/guide/migration
- Breaking Changes: https://vite.dev/changes/

---

### Node.js - <23.0.0 (engine constraint)

**Current Status (Nov 2025):**
- Latest LTS: v24.x "Krypton"
- Project constraint: <23.0.0 (likely uses Node.js 22.x)
- Status: ⚠️ Slightly outdated (one LTS version behind)

**Important Updates Since Jan 2025:**
- **Node.js 24 "Krypton"**: Entered LTS October 28, 2025 (maintained through April 2028)
- **Node.js 22 "Jod"**: Active LTS (what AFFiNE likely uses)
- **Node.js 20 "Iron"**: Maintenance mode
- **Node.js 25**: Current non-LTS release (not recommended for production)

**What This Means for Learning:**
- AFFiNE targets Node.js 22 (latest before 23), which is solid and actively supported
- All modern Node.js features available (native test runner, watch mode, fetch API, etc.)
- The <23 constraint may be due to testing/compatibility, not a technical limitation
- Node.js 22 receives security updates and is production-ready

**Official Resources:**
- Docs: https://nodejs.org/docs/latest/api/
- Node.js 24 LTS Announcement: https://nodejs.org/en/blog/release/v24.11.0
- Node.js 22 LTS: https://nodejs.org/en/blog/release/v22.11.0
- Release Schedule: https://endoflife.date/nodejs

---

### Vitest - v3.1.3

**Current Status (Nov 2025):**
- Latest stable: v4.0+ (possibly v4.x)
- Project uses: v3.1.3
- Status: 🚨 Major version behind

**Important Updates Since Jan 2025:**
- **Vitest 3.1.0** (March 2025): Performance improvements, expanded test setup flexibility
- **Vitest 3.2** (June 2025): Browser Mode improvements, TypeScript support enhancements
- **Vitest 4.0**: Released (major new version with breaking changes)
- Enhanced workspace/project configuration
- Deprecation of workspace config in favor of projects

**What This Means for Learning:**
- AFFiNE uses Vitest 3.1.3, which is functional but behind the latest
- Vitest 3.x patterns are still valid and widely used
- Consider upgrading to Vitest 4.x for new features when stable
- The testing patterns demonstrated are still current best practices

**Official Resources:**
- Docs: https://vitest.dev/
- Vitest 3.0 Release: https://vitest.dev/blog/vitest-3
- Vitest 4.0 Release: https://vitest.dev/blog/vitest-4
- Guide: https://vitest.dev/guide/

---

### Prisma - v6.6.0

**Current Status (Nov 2025):**
- Latest stable: v6.19.0+
- Project uses: v6.6.0
- Status: ⚠️ Moderately outdated (13+ minor versions behind)

**Important Updates Since Jan 2025:**
- **Prisma 6.6.0** introduced ESM support, D1 migrations, MCP server
- **Prisma 6.19.0** (November 5, 2025): Latest with bug fixes and enhancements
- **New `prisma-client` generator** (Early Access): More flexible, ESM support
- **Cloudflare D1 and Turso migration support** (Early Access)
- **Driver adapter improvements**: Better DX for JS-native drivers
- **MCP Server support**: AI development environment integration

**What This Means for Learning:**
- AFFiNE uses Prisma 6.6 which has major features (ESM, D1 support)
- Prisma 6.x is the current major version with active development
- Upgrade to 6.19+ recommended for latest bug fixes
- Prisma patterns in this project are current (v6 best practices)

**Official Resources:**
- Docs: https://www.prisma.io/docs
- Prisma 6.6.0 Release: https://www.prisma.io/blog/prisma-orm-6-6-0-esm-support-d1-migrations-and-prisma-mcp-server
- Prisma 6.19.0 Release: https://www.prisma.io/blog/announcing-prisma-6-19-0
- Changelog: https://www.prisma.io/changelog

---

### NestJS - v11.0.12

**Current Status (Nov 2025):**
- Latest stable: v11.0.12+ (likely v11.x)
- Project uses: v11.0.12
- Status: ✅ Current (latest major version)

**Important Updates Since Jan 2025:**
- **NestJS 11** released January 2025 - major update
- **Logger Enhancements**: Built-in JSON logging support
- **Microservice Improvements**: Better NATS, Kafka, Redis support with `unwrap()` method
- **Performance Optimization**: Faster startup with new module opaque key generation
- **Platform Updates**: Express v5 and Fastify v5 support
- **New `ParseDatePipe`**: Easier date handling
- **CacheModule updated**: Now uses cache-manager v6 with Keyv
- **IntrinsicException**: Throw exceptions without auto-logging
- **@nestjs/cqrs**: Request-scoped providers and strongly-typed commands/events/queries
- **Lifecycle hook order reversed**: OnModuleDestroy, OnApplicationShutdown

**What This Means for Learning:**
- AFFiNE uses the latest NestJS with all modern features
- Represents current best practices for Node.js server frameworks
- Excellent for learning enterprise-grade backend architecture
- Strong TypeScript support and dependency injection patterns

**Official Resources:**
- Docs: https://docs.nestjs.com/
- NestJS 11 Announcement: https://trilon.io/blog/announcing-nestjs-11-whats-new
- Migration Guide: https://docs.nestjs.com/migration-guide
- GitHub: https://github.com/nestjs/nest

---

### Yarn - v4.9.1

**Current Status (Nov 2025):**
- Latest stable: v4.11.0
- Project uses: v4.9.1
- Status: ⚠️ Slightly outdated (two minor versions behind)

**Important Updates Since Jan 2025:**
- **Yarn 4.0** released October 2023 as first stable 4.x
- **Yarn 4.9.x**: Recent stable series with incremental improvements
- **Yarn 4.11.0**: Current latest (published November 2025)
- Yarn 1.x is frozen; all development in Berry (Yarn 2+)
- Modern features: Plug'n'Play (PnP), zero-installs, improved workspaces

**What This Means for Learning:**
- AFFiNE uses Yarn 4.9.1 (Berry/modern Yarn)
- Workspaces are extensively used for monorepo management
- PnP mode may or may not be enabled (check .yarnrc.yml)
- Modern package management best practices

**Official Resources:**
- Docs: https://yarnpkg.com/
- Getting Started: https://yarnpkg.com/getting-started/install
- Changelog: https://yarnpkg.com/advanced/changelog
- Berry Repository: https://github.com/yarnpkg/berry

---

### GraphQL - v16.9.0

**Current Status (Nov 2025):**
- Latest stable: v16.12.0
- Project uses: v16.9.0
- Status: ⚠️ Slightly outdated (three minor versions behind)

**Important Updates Since Jan 2025:**
- **GraphQL 16.12.0**: Latest stable (published November 2025)
- **GraphQL v17.0.0-alpha.9**: Pre-release available (not production-ready)
- Incremental improvements and bug fixes in v16.x series
- Stable API, no major breaking changes

**What This Means for Learning:**
- AFFiNE uses GraphQL 16.9, which is current and stable
- GraphQL is used with Apollo Server and NestJS GraphQL module
- The patterns demonstrated are industry-standard
- v16 → v16.12 is a safe upgrade (bug fixes only)

**Official Resources:**
- Docs: https://graphql.org/learn/
- GraphQL JS: https://github.com/graphql/graphql-js
- Specification: https://spec.graphql.org/
- Best Practices: https://graphql.org/learn/best-practices/

---

### Jotai - v2.10.3

**Current Status (Nov 2025):**
- Latest stable: v2.x (exact version TBD)
- Project uses: v2.10.3
- Status: ✅ Likely current (v2 is latest major version)

**Important Updates Since Jan 2025:**
- Jotai continues as a leading atomic state management solution
- Positioned alongside Zustand and Redux in 2025 ecosystem
- Excellent for complex state interdependencies and fine-grained reactivity
- Strong TypeScript support with improved type inference

**What This Means for Learning:**
- AFFiNE uses Jotai for atomic state management
- ✅ **CURRENT (Nov 2025)**: Jotai is actively maintained and recommended
- Perfect for learning modern React state patterns
- Demonstrates when to use atomic state vs. other solutions (Context, Zustand, Redux)

**Official Resources:**
- Docs: https://jotai.org/
- GitHub: https://github.com/pmndrs/jotai
- Best Practices: https://jotai.org/docs/guides/core
- Comparison: https://jotai.org/docs/basics/comparison

---

### Socket.IO - v4.8.1

**Current Status (Nov 2025):**
- Latest stable: v4.8.1
- Project uses: v4.8.1
- Status: ✅ Current (latest version)

**Important Updates Since Jan 2025:**
- **Socket.IO 4.8.1** (October 2024): Latest stable
- Fixed production bundle binary data support (issue in 4.8.0)
- WebSocket transport when possible, HTTP long-polling fallback
- Not a pure WebSocket implementation (adds metadata: packet type, namespace, ack id)

**What This Means for Learning:**
- AFFiNE uses the latest Socket.IO for real-time communication
- Used for collaborative features (CRDT synchronization via Yjs)
- Represents current best practices for WebSocket/real-time apps
- Robust fallback mechanisms for various network conditions

**Official Resources:**
- Docs: https://socket.io/docs/v4/
- Client Installation: https://socket.io/docs/v4/client-installation/
- Changelog: https://socket.io/docs/v4/changelog/
- GitHub: https://github.com/socketio/socket.io

---

### Playwright - v1.52.0

**Current Status (Nov 2025):**
- Latest stable: v1.56.0
- Project uses: v1.52.0
- Status: ⚠️ Slightly outdated (four minor versions behind)

**Important Updates Since Jan 2025:**
- **Playwright 1.52** (April 30, 2025): New locator.describe(), expect().toContainClass()
- **Playwright 1.56** (November 11, 2025): Latest with additional features
- Aria snapshots enhancements
- Worker configuration improvements
- Snapshot update modes (all vs. changed)

**What This Means for Learning:**
- AFFiNE uses Playwright 1.52 for E2E testing
- Playwright is the industry-leading E2E testing tool in 2025
- The testing patterns demonstrated are current
- Upgrade to 1.56 recommended for latest features

**Official Resources:**
- Docs: https://playwright.dev/
- Release Notes: https://playwright.dev/docs/release-notes
- Best Practices: https://playwright.dev/docs/best-practices
- GitHub: https://github.com/microsoft/playwright

---

### Electron - v36.0.0

**Current Status (Nov 2025):**
- Latest stable: v39.0.0
- Project uses: v36.0.0
- Status: 🚨 Three major versions behind (likely unsupported)

**Important Updates Since Jan 2025:**
- **Electron 39.0.0**: Latest (published 2 days ago)
- **Electron 36.5.0**: Last version in v36 series
- Only latest 3 major versions supported (currently 37, 38, 39)
- New major version every 8 weeks
- Electron 36 features: before-mouse-event, net.request() priority options, win.isContentProtected()

**What This Means for Learning:**
- ⚠️ **OUTDATED**: AFFiNE should upgrade to Electron 37+ for security support
- Electron 36 patterns still work but may lack security updates
- Desktop app architecture is still relevant for learning
- Recommend studying Electron 39 documentation for new projects

**Official Resources:**
- Docs: https://www.electronjs.org/docs/latest/
- Releases: https://github.com/electron/electron/releases
- Support Policy: https://endoflife.date/electron
- Upgrade Guide: https://www.electronjs.org/docs/latest/breaking-changes

---

### Capacitor - v7.0.0

**Current Status (Nov 2025):**
- Latest stable: v7.4.2+
- Project uses: v7.0.0
- Status: ⚠️ Slightly outdated (minor versions behind)

**Important Updates Since Jan 2025:**
- **Capacitor 7.0**: Major release with Android 15 and iOS 18 support
- **Capacitor 7.4.2**: Current patch version
- Swift Package Manager (SPM) support (CocoaPods still supported)
- Minimal breaking changes from v6
- Enterprise adoption (OutSystems, low-code platforms)

**What This Means for Learning:**
- AFFiNE uses Capacitor 7 for mobile (iOS/Android) support
- ✅ **CURRENT (Nov 2025)**: Represents modern mobile development practices
- Cross-platform with native capabilities
- Excellent for learning web-to-native mobile development

**Official Resources:**
- Docs: https://capacitorjs.com/docs
- Capacitor 7 Release: https://ionic.io/blog/capacitor-7-has-hit-ga
- Migration Guide: https://capacitorjs.com/docs/updating/7-0
- GitHub: https://github.com/ionic-team/capacitor

---

### Rust - Edition 2024

**Current Status (Nov 2025):**
- Latest edition: 2024
- Latest compiler: v1.85.0+ (shipped with Edition 2024)
- Project uses: Edition 2024
- Status: ✅ Current (latest edition)

**Important Updates Since Jan 2025:**
- **Rust 1.85.0** (February 20, 2025): Stabilized Rust 2024 Edition
- **Most expansive Rust release yet**
- **Async closures**: `async || {}` syntax for native concurrency
- **RPIT lifetime capture improvements**
- **if let and tail expression temporary scopes** improved
- **Unsafe extern blocks and attributes**
- **Reserved keywords** for future features
- **#[diagnostic::do_not_recommend]**: Control compiler diagnostic suggestions
- **cargo fix**: Automated migration tooling
- Improvements to Cargo, Rustfmt, dependency resolution

**What This Means for Learning:**
- AFFiNE uses the latest Rust edition with all modern features
- Native modules (NAPI-RS) demonstrate Rust/Node.js interop
- Excellent for learning systems programming and performance optimization
- Async Rust is first-class with async closures

**Official Resources:**
- Docs: https://doc.rust-lang.org/
- Edition Guide: https://doc.rust-lang.org/edition-guide/rust-2024/index.html
- Rust 1.85.0 Release: https://blog.rust-lang.org/2025/02/20/Rust-1.85.0.html
- Async Book: https://rust-lang.github.io/async-book/

---

## Additional Technologies

### Yjs - v13.6.21

**Status:** ✅ Current
- CRDT library for real-time collaboration
- Used for document synchronization
- Industry-standard for collaborative editing

**Resources:**
- Docs: https://docs.yjs.dev/
- GitHub: https://github.com/yjs/yjs

---

### Lit - v3.2.1

**Status:** ✅ Current (v3 is latest major)
- Web Components library
- Used in BlockSuite editor framework
- Modern web standards-based approach

**Resources:**
- Docs: https://lit.dev/
- GitHub: https://github.com/lit/lit

---

### Radix UI - Various versions

**Status:** ✅ Current
- Unstyled, accessible component primitives
- Used throughout the UI (@radix-ui/react-*)
- Industry-standard for accessible React components

**Resources:**
- Docs: https://www.radix-ui.com/
- Primitives: https://www.radix-ui.com/primitives

---

### ESLint - v9.16.0

**Status:** ✅ Current (v9 is latest major)
- Latest ESLint with flat config support
- Modern linting practices

**Resources:**
- Docs: https://eslint.org/
- Migration: https://eslint.org/docs/latest/use/migrate-to-9.0.0

---

### Prettier - v3.4.2

**Status:** ✅ Current
- Latest Prettier version
- Code formatting standard

**Resources:**
- Docs: https://prettier.io/

---

## Summary & Recommendations

### ✅ Current Technologies (Best Practices)
- Vite 7.0.0
- Socket.IO 4.8.1
- NestJS 11.0.12
- Rust Edition 2024
- Capacitor 7.0.0
- Lit 3.2.1
- Radix UI
- ESLint 9.16.0
- Prettier 3.4.2

### ⚠️ Slightly Outdated (Upgrade Recommended)
- React 19.1.0 → 19.2.0 (low priority)
- TypeScript 5.7.2 → 5.9.3 (low priority)
- Node.js <23 → 24 LTS (consider for future)
- Yarn 4.9.1 → 4.11.0 (low priority)
- GraphQL 16.9.0 → 16.12.0 (low priority)
- Prisma 6.6.0 → 6.19.0+ (moderate priority)
- Playwright 1.52.0 → 1.56.0 (low priority)

### 🚨 Major Updates Needed
- Vitest 3.1.3 → 4.0+ (breaking changes, test carefully)
- Electron 36.0.0 → 39.0.0 (security concern, high priority)

### Overall Assessment

**Strengths:**
- AFFiNE uses modern, well-maintained technologies
- Most dependencies are current or very close to latest
- Excellent foundation for learning modern full-stack development
- Strong TypeScript ecosystem throughout

**Areas for Improvement:**
- Electron needs urgent update for security support
- Vitest upgrade to v4 recommended when stable
- Prisma could be updated for latest bug fixes

**Learning Value:**
- ✅ **EXCELLENT**: This codebase represents 2025 best practices
- Modern React (v19), TypeScript (v5.7+), and build tools (Vite 7)
- Enterprise-grade backend (NestJS 11, Prisma 6, GraphQL)
- Cutting-edge collaboration tech (Yjs, Socket.IO, CRDTs)
- Cross-platform (Web, Desktop via Electron, Mobile via Capacitor)
- Performance-critical code in Rust (Edition 2024)

---

## Next Steps for Developers

1. **Start with the current stack** - Don't wait for updates to begin learning
2. **Focus on patterns, not versions** - The architectural patterns are more important than exact versions
3. **Check official docs** - Always refer to the official documentation linked above
4. **Understand the "why"** - Learn why each technology was chosen for its specific role
5. **Experiment safely** - Use feature branches to test upgrades
6. **Read the migration guides** - When upgrading, always read official migration documentation

---

**Last Updated:** November 18, 2025
**Next Review:** Quarterly (February 2026)
