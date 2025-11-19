# Debugging Guide

> **Purpose**: Advanced debugging techniques for frontend, backend, and collaboration issues.

---

## Frontend Debugging

### Chrome DevTools

**Console**:
```javascript
// Inspect Yjs document
const doc = workspace.rootYDoc;
console.log('Doc state:', encodeStateAsUpdate(doc));

// Inspect Jotai atoms
window.__JOTAI_DEVTOOLS__ = true; // Enable devtools

// Monitor re-renders
import { whyDidYouUpdate } from '@welldone-software/why-did-you-render';
whyDidYouUpdate(React);
```

**Sources Panel**:
- Add breakpoints
- Step through code
- Watch expressions
- Call stack inspection

**Performance Panel**:
- Record interaction
- Analyze flame chart
- Find bottlenecks

### React DevTools

- **Components tab** - Inspect props/state
- **Profiler tab** - Measure render performance
- **Hooks tab** - Debug hook state

---

## Backend Debugging

### Node.js Inspector

```bash
node --inspect packages/backend/server/dist/index.js

# Chrome: chrome://inspect
```

### Logging

```typescript
import { Logger } from '@nestjs/common';

export class MyService {
  private readonly logger = new Logger(MyService.name);

  async doSomething() {
    this.logger.log('Starting operation');
    this.logger.debug('Debug info', { data });
    this.logger.error('Error occurred', error.stack);
  }
}
```

### Database Queries

```typescript
// Enable query logging
const prisma = new PrismaClient({
  log: ['query', 'info', 'warn', 'error'],
});

// Log slow queries
prisma.$on('query', (e) => {
  if (e.duration > 1000) {
    console.warn('Slow query:', e.query, `${e.duration}ms`);
  }
});
```

---

## Collaboration Debugging

### Yjs Debugging

```typescript
import * as Y from 'yjs';

// Enable Yjs logging
Y.enableLogging(true);

// Inspect document
const doc = new Y.Doc();
console.log('State vector:', encodeStateVector(doc));
console.log('Full state:', encodeStateAsUpdate(doc));

// Monitor updates
doc.on('update', (update, origin) => {
  console.log('Update from:', origin);
  console.log('Update size:', update.length, 'bytes');
  console.log('Update hex:', Array.from(update).map(b => b.toString(16)));
});
```

### WebSocket Debugging

```typescript
// Monitor all events
socket.onAny((event, ...args) => {
  console.log('[WebSocket]', event, args);
});

// Track connection state
socket.on('connect', () => console.log('✅ Connected'));
socket.on('disconnect', (reason) => console.log('❌ Disconnected:', reason));
socket.on('connect_error', (err) => console.error('Connection error:', err));
```

---

## Common Issues

### Issue: Changes Not Syncing

**Symptoms**: Edit in one client, doesn't appear in another

**Debug**:
```typescript
// 1. Check WebSocket connection
console.log('Socket connected:', socket.connected);

// 2. Check update generation
doc.on('update', (update) => {
  console.log('Update generated:', update.length, 'bytes');
});

// 3. Check permission
await this.ac.user(userId).workspace(wsId).can('Doc.Write');
```

### Issue: Memory Leak

**Symptoms**: Memory usage increases over time

**Debug**:
```typescript
// Take heap snapshot
// Chrome: Memory tab → Heap snapshot → Take snapshot

// Find leaks
// Look for detached DOM nodes
// Check event listeners not cleaned up

// Fix: Clean up in useEffect
useEffect(() => {
  const handler = () => { };
  element.addEventListener('click', handler);

  return () => {
    element.removeEventListener('click', handler);
  };
}, []);
```

---

## Tools

- **Chrome DevTools** - Frontend debugging
- **React DevTools** - Component inspection
- **Redux DevTools** - State debugging (Jotai)
- **Prisma Studio** - Database browser
- **Postman** - API testing

---

*Advanced debugging techniques for AFFiNE development.*
