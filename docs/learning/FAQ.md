# Frequently Asked Questions (FAQ)

> **Purpose**: Answers to common questions about AFFiNE development.

---

## General Questions

### Q: What is AFFiNE?

**A:** AFFiNE is an open-source, local-first knowledge management system. Think Notion + Obsidian - block-based editing with local-first data storage and real-time collaboration.

### Q: What makes AFFiNE different from Notion?

**A:**
- ✅ **Open source** - Fully transparent codebase
- ✅ **Local-first** - Data lives on your device
- ✅ **Offline-first** - Works without internet
- ✅ **Self-hostable** - Deploy on your own servers
- ✅ **Privacy-focused** - You own your data

---

## Getting Started

### Q: What do I need to install?

**A:**
- Node.js 22+ (or Node 24 LTS)
- Yarn 4.9+
- Rust (for native modules)
- Docker (optional, for backend)

See: `GETTING_STARTED.md`

### Q: Can I just run the frontend?

**A:** Yes! Run `yarn dev` for frontend-only mode with mock data. No backend/database needed.

### Q: How long does initial setup take?

**A:** ~20-30 minutes (including dependency installation and building native modules).

---

## Architecture Questions

### Q: Why BlockSuite instead of ProseMirror/Slate?

**A:** BlockSuite was built specifically for:
- Block-based editing (like Notion)
- Yjs CRDT integration (real-time collaboration)
- Framework-agnostic (Web Components, not React-specific)

### Q: Why Lit instead of React for the editor?

**A:** Lit (Web Components) offers:
- Better performance (no virtual DOM)
- Smaller bundle size (~5KB vs 40KB React)
- Framework-agnostic (works with React, Vue, Angular)
- Native browser APIs

### Q: Why both Jotai AND services?

**A:**
- **Jotai** - UI state (selections, modals, form state)
- **Services** - Business logic (API calls, data transformation)
- Clean separation of concerns

### Q: Why PostgreSQL instead of MongoDB?

**A:**
- Strong relational data (users, workspaces, permissions)
- ACID transactions
- Better query performance for complex joins
- Mature ecosystem (Prisma ORM)

---

## Development Questions

### Q: How do I add a new feature?

**A:**
1. Create module in `packages/frontend/core/src/modules/my-feature/`
2. Add service + entities
3. Register module in `modules/index.ts`
4. Use in components

See: `HOW_TO_GUIDE.md`

### Q: How do I test my changes?

**A:**
```bash
yarn test:unit # Unit tests
yarn test:e2e # E2E tests
yarn typecheck # Type checking
yarn lint # Linting
```

### Q: How do I debug?

**A:**
- **Frontend**: Chrome DevTools, React DevTools
- **Backend**: Node.js Inspector, logging
- **WebSocket**: Network tab (WS filter)

See: `DEBUGGING_GUIDE.md`

---

## Collaboration Questions

### Q: How does real-time collaboration work?

**A:**
1. User types → Yjs generates update (binary diff)
2. Update saved to IndexedDB (instant, offline-first)
3. Update sent to server via WebSocket
4. Server broadcasts to other clients
5. Clients apply update (CRDT merge, no conflicts)

See: `COLLABORATION_SYSTEM.md`

### Q: What happens if two users edit the same text?

**A:** Yjs CRDTs automatically merge edits. No manual conflict resolution needed. Both changes preserved.

### Q: Can I work offline?

**A:** Yes! All changes saved locally (IndexedDB/SQLite). When reconnected, changes sync automatically.

---

## Deployment Questions

### Q: How do I deploy AFFiNE?

**A:**
- **Frontend**: Build with `yarn build:web`, deploy to Vercel/Netlify
- **Backend**: Docker container, deploy to any cloud provider
- **Database**: PostgreSQL on AWS RDS, Azure Database, etc.

### Q: Is there a managed hosting option?

**A:** Yes! https://affine.pro offers managed hosting.

### Q: Can I self-host?

**A:** Yes! AFFiNE is fully self-hostable. See deployment guides.

---

## Contribution Questions

### Q: How do I contribute?

**A:** See `FIRST_CONTRIBUTIONS.md` for step-by-step guide.

### Q: Where do I find issues to work on?

**A:** Look for `good first issue` label: https://github.com/toeverything/AFFiNE/labels/good%20first%20issue

### Q: Do I need to sign a CLA?

**A:** No CLA required. Just submit a PR!

### Q: How long until my PR is reviewed?

**A:** Usually 1-3 days. Maintainers review regularly.

---

## Troubleshooting

### Q: `yarn install` fails - what do I do?

**A:**
1. Check Node.js version: `node -v` (should be 22+)
2. Enable Corepack: `corepack enable`
3. Clear cache: `yarn cache clean`
4. Try again: `yarn install`

### Q: Build fails with Rust errors

**A:**
1. Install Rust: `curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`
2. Restart terminal
3. Build native modules: `yarn affine @affine/native build`

### Q: App won't start - blank screen

**A:**
1. Check console for errors (F12)
2. Clear browser cache
3. Try incognito mode
4. Check if backend is running (if using full-stack mode)

### Q: Tests fail with "cannot find module"

**A:**
1. Run `yarn install` first
2. Build dependencies: `yarn build:packages`
3. Run tests again

---

## Performance Questions

### Q: App feels slow - how do I optimize?

**A:**
1. Profile with React DevTools Profiler
2. Check for unnecessary re-renders
3. Use `React.memo` for expensive components
4. Implement virtual scrolling for long lists
5. Debounce frequent operations

See: `HOW_TO_GUIDE.md#how-to-optimize-performance`

### Q: Large documents are slow

**A:**
- BlockSuite uses virtual scrolling for 1000+ blocks
- Consider lazy-loading heavy blocks (database, PDF)
- Profile with Chrome Performance tab

---

## Security Questions

### Q: How is my data protected?

**A:**
- Passwords hashed with bcrypt (12 rounds)
- HTTP-only cookies for sessions
- HTTPS enforced in production
- Regular security audits

See: `SECURITY_GUIDE.md`

### Q: Can AFFiNE see my data?

**A:**
- **Self-hosted**: No, all data on your servers
- **Cloud (affine.pro)**: E2E encryption planned (not yet implemented)

### Q: Is AFFiNE GDPR compliant?

**A:** Yes, when self-hosted. For managed hosting, see privacy policy.

---

## Still Have Questions?

- 💬 **Discord**: https://discord.gg/affine
- 📧 **Email**: contact@toeverything.so
- 🐛 **GitHub Issues**: https://github.com/toeverything/AFFiNE/issues
- 📖 **Docs**: https://docs.affine.pro

---

*Answers to frequently asked questions about AFFiNE development.*
