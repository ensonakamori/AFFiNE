# AFFiNE Learning Path 🚀

**Welcome to AFFiNE!** This comprehensive learning guide will help you understand, contribute to, and master the AFFiNE codebase.

**Last Updated:** November 18, 2025
**Tech Stack Research:** [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md)

---

## 📋 Quick Start

**New to AFFiNE?** Start here:
1. [Getting Started Guide](./GETTING_STARTED.md) - Set up your dev environment (30 mins)
2. [Architecture Overview](./ARCHITECTURE_OVERVIEW.md) - Understand the big picture (1 hour)
3. [First Contributions Guide](./FIRST_CONTRIBUTIONS.md) - Make your first PR (1-2 hours)

---

## 🎯 What is AFFiNE?

**AFFiNE** is an open-source, all-in-one workspace that combines:
- ✍️ **Document editing** (like Notion) - Rich text, markdown, blocks
- 🎨 **Canvas/whiteboard** (like Miro) - Infinite canvas for visual thinking
- 📊 **Database views** - Kanban, Table, Gallery views
- 🤝 **Real-time collaboration** - CRDT-based (Yjs) multiplayer editing
- 🤖 **AI capabilities** - Multimodal AI partner for productivity
- 🔒 **Local-first** - Your data stays on your device, with optional cloud sync
- 🌍 **Cross-platform** - Web, Desktop (Electron), Mobile (Capacitor)

**Tech Stack:** React 19, TypeScript 5.7, NestJS 11, Prisma 6, GraphQL, Rust (Edition 2024), Yjs, Socket.IO

---

## 🗺️ Learning Paths

Choose your path based on your role and goals:

### Path 1: Frontend Developer

Perfect for those working on the React-based UI.

```
1. 📘 Getting Started        → 30 mins
2. 🔧 Tech Stack Guide       → 1 hour (focus: React, Jotai, Vite)
3. ⚛️  Frontend Architecture → 2 hours
4. 📐 Patterns & Conventions → 1 hour
5. 🛠️  Development Workflow  → 30 mins
6. ✅ Testing Guide          → 1 hour
7. 🎯 First Contributions    → practice time
```

**Total Time:** ~6-8 hours to productive contributor

[Start Frontend Path →](./GETTING_STARTED.md)

---

### Path 2: Backend Developer

Perfect for those working on the NestJS server and APIs.

```
1. 📘 Getting Started        → 30 mins
2. 🔧 Tech Stack Guide       → 1 hour (focus: NestJS, Prisma, GraphQL)
3. 🗄️  Backend Architecture  → 2 hours
4. 🗂️  Database Schema       → 1 hour
5. 📡 API Documentation      → 1 hour
6. ✅ Testing Guide          → 1 hour
7. 🎯 First Contributions    → practice time
```

**Total Time:** ~7-9 hours to productive contributor

[Start Backend Path →](./GETTING_STARTED.md)

---

### Path 3: Full-Stack Developer

Perfect for those working across frontend and backend.

```
1. 📘 Getting Started           → 30 mins
2. 🏗️  Architecture Overview    → 1.5 hours
3. 🔄 Data Flow Guide           → 1 hour
4. 🔧 Tech Stack Guide          → 2 hours (all sections)
5. ⚛️  Frontend Architecture    → 2 hours
6. 🗄️  Backend Architecture     → 2 hours
7. 🤝 Collaboration System      → 1.5 hours
8. 📐 Patterns & Conventions    → 1 hour
9. 📖 How-To Guide              → as needed
10. 🎯 First Contributions      → practice time
```

**Total Time:** ~12-15 hours to productive contributor

[Start Full-Stack Path →](./GETTING_STARTED.md)

---

### Path 4: Editor/BlockSuite Developer

Perfect for those working on the editor framework.

```
1. 📘 Getting Started           → 30 mins
2. 📝 BlockSuite Editor Guide   → 3 hours
3. 🤝 Collaboration System      → 2 hours (focus: Yjs)
4. 🗺️  Code Tours               → 2 hours (editor-focused)
5. 📐 Patterns & Conventions    → 1 hour
6. 🎯 First Contributions       → practice time
```

**Total Time:** ~9-11 hours to productive contributor

[Start Editor Path →](./GETTING_STARTED.md)

---

## 📚 Complete Documentation

### Foundation (Start Here)

| Document | Description | Time | Prerequisites |
|----------|-------------|------|---------------|
| [Getting Started](./GETTING_STARTED.md) | Set up your dev environment, run the app | 30 mins | None |
| [Architecture Overview](./ARCHITECTURE_OVERVIEW.md) | High-level system design and component interactions | 1.5 hours | Getting Started |
| [Project Structure](./PROJECT_STRUCTURE.md) | Detailed guide to folders, files, and organization | 1 hour | Getting Started |
| [Tech Stack Guide](./TECH_STACK_GUIDE.md) | Deep dive into each technology used | 2 hours | Architecture Overview |
| [Data Flow Guide](./DATA_FLOW_GUIDE.md) | How data moves through the system | 1 hour | Architecture Overview |

---

### Deep Dives (Core Systems)

| Document | Description | Time | Prerequisites |
|----------|-------------|------|---------------|
| [Frontend Architecture](./FRONTEND_ARCHITECTURE.md) | React app structure, state management, routing | 2 hours | Tech Stack Guide |
| [Backend Architecture](./BACKEND_ARCHITECTURE.md) | NestJS server, GraphQL, database, authentication | 2 hours | Tech Stack Guide |
| [BlockSuite Editor](./BLOCKSUITE_EDITOR.md) | Editor framework, blocks, rendering pipeline | 3 hours | Frontend Architecture |
| [Collaboration System](./COLLABORATION_SYSTEM.md) | CRDTs, Yjs, real-time sync, conflict resolution | 2 hours | Architecture Overview |

---

### Practical Guides (Day-to-Day Work)

| Document | Description | Time | Prerequisites |
|----------|-------------|------|---------------|
| [Patterns & Conventions](./PATTERNS_AND_CONVENTIONS.md) | Code style, best practices, naming conventions | 1 hour | Architecture Overview |
| [How-To Guide](./HOW_TO_GUIDE.md) | Common tasks: add feature, fix bug, refactor | As needed | Patterns & Conventions |
| [Code Tours](./CODE_TOURS.md) | Guided walkthroughs of key features | 2 hours | Architecture Overview |
| [Development Workflow](./DEVELOPMENT_WORKFLOW.md) | Git workflow, PR process, code review | 30 mins | Getting Started |

---

### Quality & Reference (Advanced)

| Document | Description | Time | Prerequisites |
|----------|-------------|------|---------------|
| [Testing Guide](./TESTING_GUIDE.md) | Unit, integration, E2E testing strategies | 1.5 hours | Architecture Overview |
| [Debugging Guide](./DEBUGGING_GUIDE.md) | Debugging techniques, tools, common issues | 1 hour | Development Workflow |
| [Security Guide](./SECURITY_GUIDE.md) | Security best practices, auth, data protection | 1 hour | Backend Architecture |
| [API Documentation](./API_DOCUMENTATION.md) | GraphQL API reference and examples | 1 hour | Backend Architecture |
| [Database Schema](./DATABASE_SCHEMA.md) | Prisma schema explained, relationships | 1 hour | Backend Architecture |

---

### Hands-On Learning

| Document | Description | Time | Prerequisites |
|----------|-------------|------|---------------|
| [Exercises](./EXERCISES.md) | Hands-on coding exercises to practice | Varies | Architecture Overview |
| [First Contributions](./FIRST_CONTRIBUTIONS.md) | Good first issues, contribution workflow | 1-2 hours | How-To Guide |
| [FAQ](./FAQ.md) | Frequently asked questions | As needed | None |

---

## 🔧 Tech Stack Overview

See [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md) for detailed version analysis.

### Frontend
- **React 19.1.0** - UI framework with modern features (use hook, Actions, etc.)
- **TypeScript 5.7.2** - Type safety across the codebase
- **Vite 7.0.0** ✅ - Lightning-fast build tool (latest version)
- **Jotai 2.10.3** - Atomic state management
- **React Router v6** - Client-side routing
- **Radix UI** - Accessible component primitives
- **Vanilla Extract** - Type-safe CSS-in-JS

### Backend
- **NestJS 11.0.12** ✅ - Enterprise Node.js framework (latest)
- **Prisma 6.6.0** - Type-safe ORM (⚠️ upgrade to 6.19+ recommended)
- **GraphQL 16.9.0** - API query language
- **PostgreSQL** - Primary database
- **Socket.IO 4.8.1** ✅ - Real-time communication (latest)
- **BullMQ** - Job queue with Redis

### Editor (BlockSuite)
- **Lit 3.2.1** ✅ - Web Components framework
- **Yjs 13.6.21** ✅ - CRDT for collaboration
- **Custom rendering** - High-performance canvas/DOM hybrid

### Build & Tools
- **Yarn 4.9.1** - Package manager (Berry/modern Yarn)
- **Vitest 3.1.3** - Unit testing (⚠️ v4 available)
- **Playwright 1.52.0** - E2E testing
- **ESLint 9.16.0** ✅ - Linting
- **Prettier 3.4.2** ✅ - Code formatting

### Native
- **Rust (Edition 2024)** ✅ - Performance-critical modules (latest)
- **NAPI-RS** - Node.js ↔ Rust bindings
- **Electron 36.0.0** - Desktop app (🚨 upgrade to 39+ urgently recommended)
- **Capacitor 7.0.0** ✅ - Mobile cross-platform

**Legend:**
- ✅ = Current (latest version or very close)
- ⚠️ = Upgrade recommended (but working fine)
- 🚨 = Urgent update needed (security/support)

---

## 🎓 Key Concepts to Master

### 1. **Local-First Architecture**
- Data stored locally (IndexedDB)
- Cloud sync is optional enhancement
- Offline-first design
- Learn more: [Architecture Overview](./ARCHITECTURE_OVERVIEW.md#local-first)

### 2. **CRDTs & Collaboration**
- Conflict-free Replicated Data Types
- Yjs for document synchronization
- Real-time multiplayer editing
- Learn more: [Collaboration System](./COLLABORATION_SYSTEM.md)

### 3. **Block-Based Editor**
- Everything is a block (text, image, database, etc.)
- Composable block system
- Custom rendering pipeline
- Learn more: [BlockSuite Editor](./BLOCKSUITE_EDITOR.md)

### 4. **Atomic State Management**
- Jotai for fine-grained reactivity
- Atoms for state units
- Derived state patterns
- Learn more: [Frontend Architecture](./FRONTEND_ARCHITECTURE.md#state-management)

### 5. **GraphQL API**
- Schema-first API design
- Type-safe queries and mutations
- Real-time subscriptions
- Learn more: [API Documentation](./API_DOCUMENTATION.md)

### 6. **Monorepo Structure**
- Yarn workspaces
- Shared packages
- Build orchestration
- Learn more: [Project Structure](./PROJECT_STRUCTURE.md)

---

## 🚀 Quick Reference

### Common Commands

```bash
# Install dependencies
yarn install

# Start development (all apps)
yarn dev

# Start specific app
yarn workspace @affine/web dev          # Web app
yarn workspace @affine/server dev       # Backend server
yarn workspace @affine/electron dev     # Desktop app

# Testing
yarn test                               # All tests
yarn test:unit                          # Unit tests
yarn test:e2e                           # E2E tests

# Linting & Formatting
yarn lint                               # Check all
yarn lint:fix                           # Fix issues
yarn typecheck                          # TypeScript check

# Building
yarn build                              # Build all
yarn workspace @affine/web build        # Build web app
```

See [Development Workflow](./DEVELOPMENT_WORKFLOW.md) for details.

---

### Project Shortcuts

| Path | Description |
|------|-------------|
| `packages/frontend/core/` | Main frontend application logic |
| `packages/frontend/component/` | Reusable UI components |
| `packages/backend/server/` | NestJS backend server |
| `blocksuite/affine/blocks/` | Block implementations |
| `blocksuite/framework/` | Core editor framework |
| `packages/common/infra/` | Shared infrastructure code |
| `tests/` | E2E and integration tests |

See [Project Structure](./PROJECT_STRUCTURE.md) for complete map.

---

### Where to Find Things

| Looking for... | Go to... |
|----------------|----------|
| UI Components | `packages/frontend/component/` |
| React pages & routes | `packages/frontend/core/src/pages/` |
| State management | `packages/frontend/core/src/atoms/` |
| GraphQL schema | `packages/backend/server/src/graphql/` |
| Database models | `packages/backend/server/prisma/schema.prisma` |
| API resolvers | `packages/backend/server/src/resolvers/` |
| Editor blocks | `blocksuite/affine/blocks/` |
| CRDT logic | `packages/common/y-octo/` |
| Authentication | `packages/backend/server/src/auth/` |
| Tests | `tests/affine-local/`, `tests/affine-mobile/` |

See [How-To Guide](./HOW_TO_GUIDE.md) for task-based navigation.

---

## 🤝 Contributing

Ready to contribute? Here's the process:

1. **Read the guides:**
   - [Getting Started](./GETTING_STARTED.md)
   - [Development Workflow](./DEVELOPMENT_WORKFLOW.md)
   - [Patterns & Conventions](./PATTERNS_AND_CONVENTIONS.md)

2. **Find an issue:**
   - [Good first issues](https://github.com/toeverything/AFFiNE/labels/good%20first%20issue)
   - [First Contributions Guide](./FIRST_CONTRIBUTIONS.md)

3. **Write code:**
   - Follow [Patterns & Conventions](./PATTERNS_AND_CONVENTIONS.md)
   - Add tests ([Testing Guide](./TESTING_GUIDE.md))
   - Update docs if needed

4. **Submit PR:**
   - Follow PR template
   - Pass CI checks
   - Respond to code review

See [CONTRIBUTING.md](../CONTRIBUTING.md) in the root for official guidelines.

---

## ❓ FAQ & Help

- **❓ Questions?** See [FAQ.md](./FAQ.md)
- **🐛 Bug?** Check [Debugging Guide](./DEBUGGING_GUIDE.md)
- **💬 Discussion?** Join [Discord](https://affine.pro/redirect/discord)
- **📖 Docs?** Official docs at [docs.affine.pro](https://docs.affine.pro/)

---

## 📊 Learning Progress Tracker

Use this to track your learning journey:

### Foundation
- [ ] Getting Started - Environment set up, app running
- [ ] Architecture Overview - Understand system design
- [ ] Project Structure - Know where everything is
- [ ] Tech Stack Guide - Familiar with all technologies
- [ ] Data Flow Guide - Understand data movement

### Your Focus Area
**Frontend:**
- [ ] Frontend Architecture
- [ ] Patterns & Conventions
- [ ] Testing Guide
- [ ] First Contribution

**Backend:**
- [ ] Backend Architecture
- [ ] Database Schema
- [ ] API Documentation
- [ ] First Contribution

**Editor:**
- [ ] BlockSuite Editor
- [ ] Collaboration System
- [ ] Code Tours
- [ ] First Contribution

### Mastery
- [ ] Completed 3+ code tours
- [ ] Written unit tests
- [ ] Written E2E tests
- [ ] Made 1st contribution (merged PR)
- [ ] Made 5+ contributions
- [ ] Reviewed others' PRs
- [ ] Mentored new contributor

---

## 🎯 Goals & Outcomes

After completing these guides, you should be able to:

✅ **Set up the development environment** in under 30 minutes
✅ **Understand the overall architecture** and explain it to others
✅ **Navigate the codebase** confidently
✅ **Add a new feature** to frontend or backend
✅ **Fix bugs** across the stack
✅ **Write tests** for your changes
✅ **Submit quality PRs** that pass review
✅ **Collaborate effectively** with the team

---

## 📝 Documentation Status

| Phase | Status | Documents | Progress |
|-------|--------|-----------|----------|
| Phase 0 | ✅ Complete | Tech Stack Research | 100% |
| Phase 1 | ✅ Complete | Execution Plan | 100% |
| Phase 2 | 🔄 In Progress | Central README | 90% |
| Phase 3 | ⏳ Pending | Foundation Docs (5) | 0% |
| Phase 4 | ⏳ Pending | Deep-Dive Docs (4) | 0% |
| Phase 5 | ⏳ Pending | Practical Guides (4) | 0% |
| Phase 6 | ⏳ Pending | Quality & Reference (5) | 0% |
| Phase 7 | ⏳ Pending | Learning Exercises (2) | 0% |
| Phase 8 | ⏳ Pending | Finalization (3) | 0% |

**Overall Progress:** 2 of 23 documents complete

---

## 🔗 External Resources

### Official AFFiNE
- **Website:** https://affine.pro
- **Live Demo:** https://app.affine.pro
- **Documentation:** https://docs.affine.pro
- **Blog:** https://affine.pro/blog
- **Discord:** https://affine.pro/redirect/discord
- **GitHub:** https://github.com/toeverything/AFFiNE

### BlockSuite (Editor Framework)
- **Website:** https://blocksuite.io
- **Documentation:** https://blocksuite.io/docs
- **GitHub:** https://github.com/toeverything/blocksuite

### Technology Documentation
See [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md) for links to all official docs.

---

## 📅 Staying Current

**This documentation was created:** November 18, 2025

**Tech Stack Research Date:** November 18, 2025

**Next Review:** Quarterly (February 2026)

Technologies evolve quickly. For the latest information:
1. Check [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md) for version status
2. Review official documentation for each technology
3. Check the project's package.json files for current versions
4. Join Discord for community discussions

---

## 💡 Tips for Success

### 🎯 **Focus on Patterns, Not Memorization**
Understand *why* code is structured a certain way, not just *what* it does.

### 🔗 **Follow the Hyperlinks**
All concepts link to real code examples. Click through and read the actual implementations.

### ✅ **Do the Quick Checks**
Self-test questions help reinforce learning. Don't skip them.

### 🏋️ **Practice with Exercises**
Theory is important, but hands-on coding solidifies understanding.

### 🤝 **Ask Questions**
Join Discord, open discussions, ask maintainers. The community is here to help.

### 📝 **Document Your Learning**
Keep notes, write blog posts, create diagrams. Teaching others helps you learn.

### 🐛 **Start with Bugs**
Fixing bugs is one of the best ways to learn a codebase.

### 🧭 **Use Code Tours**
Follow the guided tours to see how features are implemented end-to-end.

---

**Welcome aboard! Let's build amazing things together. 🚀**

**Questions?** Open an issue or ask in [Discord](https://affine.pro/redirect/discord).

**Found an error in these docs?** Submit a PR - documentation contributions are highly valued!

---

*This learning path was created with ❤️ to help developers ramp up quickly on AFFiNE.*

*Last updated: November 18, 2025*
