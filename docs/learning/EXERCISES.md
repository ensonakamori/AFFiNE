# Learning Exercises

> **Purpose**: Hands-on exercises to practice AFFiNE development - from beginner to advanced.

---

## Beginner Exercises

### Exercise 1: Add a "Star" Feature to Workspaces

**Goal**: Add ability to star/favorite workspaces

**Steps**:
1. Add `starred` boolean to Prisma schema
2. Create migration
3. Add GraphQL mutation `toggleWorkspaceStar`
4. Add backend resolver
5. Create frontend star button component
6. Test

**Estimated time**: 2 hours

**Solution**: `docs/learning/solutions/star-workspace.md`

---

### Exercise 2: Create a Custom Block Type

**Goal**: Create a "Callout" block (like Notion)

**Steps**:
1. Define block schema in BlockSuite
2. Create Lit component
3. Add to slash menu
4. Style with Vanilla Extract
5. Test rendering and editing

**Estimated time**: 3 hours

**Solution**: See `HOW_TO_GUIDE.md#how-to-create-a-custom-block`

---

## Intermediate Exercises

### Exercise 3: Add Tag System

**Goal**: Implement tagging for documents

**Steps**:
1. Design database schema (tags table)
2. Add Prisma models
3. Create GraphQL types and resolvers
4. Add backend service
5. Create frontend tag picker component
6. Integrate with doc metadata

**Estimated time**: 6 hours

---

### Exercise 4: Implement Undo/Redo

**Goal**: Add undo/redo for document edits

**Steps**:
1. Use Yjs undo manager
2. Add keyboard shortcuts (Cmd+Z, Cmd+Shift+Z)
3. Add UI buttons
4. Handle edge cases (collaborative edits)
5. Test thoroughly

**Estimated time**: 4 hours

---

## Advanced Exercises

### Exercise 5: Build a Plugin System

**Goal**: Allow third-party plugins

**Steps**:
1. Design plugin API
2. Create plugin loader
3. Add plugin discovery mechanism
4. Implement sandboxing
5. Create example plugin
6. Document plugin API

**Estimated time**: 2 days

---

### Exercise 6: Optimize Large Document Performance

**Goal**: Handle 10,000+ block documents

**Steps**:
1. Profile current performance
2. Implement virtual scrolling
3. Lazy load blocks
4. Optimize Yjs structure
5. Add performance metrics
6. Benchmark improvements

**Estimated time**: 3 days

---

## Self-Check Questions

After completing exercises, test your knowledge:

**Frontend**:
1. How does Jotai differ from Redux?
2. When should you use `useMemo` vs `useCallback`?
3. How does Lit's reactivity work?

**Backend**:
4. Explain the permission system flow
5. How does Prisma prevent SQL injection?
6. What's the difference between `@Query` and `@ResolveField`?

**Collaboration**:
7. How do CRDTs resolve conflicts?
8. What's the difference between Y.Doc updates and awareness?
9. Explain offline-first architecture

---

*Hands-on exercises to master AFFiNE development.*
