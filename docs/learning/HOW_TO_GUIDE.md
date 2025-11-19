# How-To Guide

> **Purpose**: Step-by-step instructions for common development tasks in AFFiNE.

---

## Table of Contents

1. [How to Add a New Module](#how-to-add-a-new-module)
2. [How to Create a Custom Block](#how-to-create-a-custom-block)
3. [How to Add a GraphQL Query](#how-to-add-a-graphql-query)
4. [How to Add Database Migration](#how-to-add-database-migration)
5. [How to Add Feature Flag](#how-to-add-feature-flag)
6. [How to Add Translation](#how-to-add-translation)
7. [How to Debug WebSocket Issues](#how-to-debug-websocket-issues)
8. [How to Optimize Performance](#how-to-optimize-performance)

---

## How to Add a New Module

### Step 1: Create Module Directory

```bash
mkdir -p packages/frontend/core/src/modules/my-feature
cd packages/frontend/core/src/modules/my-feature
```

### Step 2: Create Module Structure

```typescript
// module.ts
import { Module } from '@toeverything/infra';
import { MyFeatureService } from './services/my-feature';
import { MyFeature } from './entities/my-feature';

export const MyFeatureModule = Module.create({
  name: 'my-feature',
  services: [MyFeatureService],
  entities: [MyFeature],
});

// services/my-feature.ts
import { Service } from '@toeverything/infra';

export class MyFeatureService extends Service {
  async doSomething() {
    // Implementation
  }
}

// index.ts
export { MyFeatureModule } from './module';
export { MyFeatureService } from './services/my-feature';
```

### Step 3: Register Module

```typescript
// packages/frontend/core/src/modules/index.ts
import { MyFeatureModule } from './my-feature';

export function configureModules(framework: Framework) {
  // ... existing modules
  framework.module(MyFeatureModule);
}
```

### Step 4: Use in Component

```typescript
import { useService } from '@toeverything/infra';
import { MyFeatureService } from '@affine/core/modules/my-feature';

export function MyComponent() {
  const myFeature = useService(MyFeatureService);

  useEffect(() => {
    myFeature.doSomething();
  }, []);

  return <div>My Feature</div>;
}
```

---

## How to Create a Custom Block

### Step 1: Define Block Schema

```typescript
// packages/frontend/core/src/blocksuite/custom-blocks/callout.ts
import { defineBlockSchema } from '@blocksuite/store';

export const CalloutBlockSchema = defineBlockSchema({
  flavour: 'affine:callout',
  props: (internal) => ({
    type: 'info' as 'info' | 'warning' | 'error',
    text: internal.Text(),
  }),
  metadata: {
    version: 1,
    role: 'content',
    parent: ['affine:page', 'affine:note'],
  },
});
```

### Step 2: Create Component

```typescript
// callout-block.ts
import { BlockElement } from '@blocksuite/block-std';
import { html, css } from 'lit';
import { customElement } from 'lit/decorators.js';

@customElement('affine-callout')
export class CalloutBlockComponent extends BlockElement {
  static styles = css`
    .callout {
      padding: 12px;
      border-left: 4px solid;
      border-radius: 4px;
    }

    .callout.info { background: #e3f2fd; border-color: #2196f3; }
    .callout.warning { background: #fff3e0; border-color: #ff9800; }
    .callout.error { background: #ffebee; border-color: #f44336; }
  `;

  render() {
    return html`
      <div class="callout ${this.model.type}">
        <div contenteditable="true">
          ${this.model.text.toString()}
        </div>
      </div>
    `;
  }
}
```

### Step 3: Register Block

```typescript
// packages/frontend/core/src/blocksuite/index.ts
import { CalloutBlockSchema, CalloutBlockComponent } from './custom-blocks/callout';

export function registerCustomBlocks(schema: Schema) {
  schema.register([CalloutBlockSchema]);
}
```

### Step 4: Add to Slash Menu

```typescript
// slash-menu-config.ts
export const slashMenuConfig = [
  // ... existing items
  {
    type: 'affine:callout',
    label: 'Callout',
    icon: '💡',
    description: 'Highlight important information',
  },
];
```

---

## How to Add a GraphQL Query

### Step 1: Define Query in `.gql` File

```graphql
# packages/common/graphql/src/get-workspace-tags.gql
query getWorkspaceTags($workspaceId: String!) {
  workspace(id: $workspaceId) {
    id
    tags {
      id
      name
      color
    }
  }
}
```

### Step 2: Generate TypeScript Types

```bash
yarn codegen:graphql
```

**Generated**:

```typescript
// packages/common/graphql/src/graphql/index.ts
export const getWorkspaceTagsQuery = {
  id: 'getWorkspaceTagsQuery' as const,
  op: 'getWorkspaceTags',
  query: `query getWorkspaceTags($workspaceId: String!) { ... }`,
};
```

### Step 3: Create Backend Resolver

```typescript
// packages/backend/server/src/core/tags/resolver.ts
import { Query, Args, Resolver } from '@nestjs/graphql';

@Resolver()
export class TagsResolver {
  constructor(private readonly tagsService: TagsService) {}

  @Query(() => [TagType])
  async tags(@Args('workspaceId') workspaceId: string) {
    return await this.tagsService.listTags(workspaceId);
  }
}
```

### Step 4: Use in Frontend

```typescript
import { getWorkspaceTagsQuery } from '@affine/graphql';
import { useQuery } from '@affine/core/components/hooks/use-query';

function TagsList({ workspaceId }: { workspaceId: string }) {
  const { data } = useQuery({
    query: getWorkspaceTagsQuery,
    variables: { workspaceId },
  });

  return (
    <ul>
      {data.workspace.tags.map(tag => (
        <li key={tag.id}>{tag.name}</li>
      ))}
    </ul>
  );
}
```

---

## How to Add Database Migration

### Step 1: Create Migration

```bash
cd packages/backend/server
yarn prisma migrate dev --name add_tags_table
```

### Step 2: Edit Migration File

```sql
-- prisma/migrations/20250119000000_add_tags_table/migration.sql
CREATE TABLE "tags" (
  "id" TEXT NOT NULL PRIMARY KEY,
  "workspace_id" TEXT NOT NULL,
  "name" TEXT NOT NULL,
  "color" TEXT NOT NULL,
  "created_at" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,

  CONSTRAINT "tags_workspace_id_fkey" FOREIGN KEY ("workspace_id")
    REFERENCES "workspaces" ("id") ON DELETE CASCADE
);

CREATE INDEX "tags_workspace_id_idx" ON "tags"("workspace_id");
```

### Step 3: Update Prisma Schema

```prisma
// prisma/schema.prisma
model Tag {
  id          String   @id @default(uuid())
  workspaceId String   @map("workspace_id")
  name        String
  color       String
  createdAt   DateTime @default(now()) @map("created_at")

  workspace   Workspace @relation(fields: [workspaceId], references: [id], onDelete: Cascade)

  @@index([workspaceId])
  @@map("tags")
}

model Workspace {
  // ... existing fields
  tags Tag[]
}
```

### Step 4: Generate Prisma Client

```bash
yarn prisma generate
```

### Step 5: Apply in Production

```bash
yarn prisma migrate deploy
```

---

## How to Add Feature Flag

### Step 1: Define Flag

```typescript
// packages/frontend/core/src/modules/feature-flag/types.ts
export enum FeatureFlag {
  EnableNewEditor = 'enable_new_editor',
  EnableAI = 'enable_ai',
  EnableTagsFeature = 'enable_tags_feature', // ← New flag
}

export const defaultFlags: Record<FeatureFlag, boolean> = {
  [FeatureFlag.EnableNewEditor]: false,
  [FeatureFlag.EnableAI]: true,
  [FeatureFlag.EnableTagsFeature]: false, // ← Default value
};
```

### Step 2: Use in Component

```typescript
import { useService } from '@toeverything/infra';
import { FeatureFlagService, FeatureFlag } from '@affine/core/modules/feature-flag';

function TagsButton() {
  const featureFlag = useService(FeatureFlagService);
  const tagsEnabled = featureFlag.isEnabled(FeatureFlag.EnableTagsFeature);

  if (!tagsEnabled) {
    return null; // Hide feature
  }

  return <button>Manage Tags</button>;
}
```

### Step 3: Toggle via Settings

```typescript
function FeatureFlagsSettings() {
  const featureFlag = useService(FeatureFlagService);

  return (
    <div>
      <label>
        <input
          type="checkbox"
          checked={featureFlag.isEnabled(FeatureFlag.EnableTagsFeature)}
          onChange={e =>
            featureFlag.setFlag(FeatureFlag.EnableTagsFeature, e.target.checked)
          }
        />
        Enable Tags Feature
      </label>
    </div>
  );
}
```

---

## How to Add Translation

### Step 1: Add English Strings

```json
// packages/frontend/i18n/src/resources/en.json
{
  "com.affine.tags.title": "Tags",
  "com.affine.tags.create": "Create Tag",
  "com.affine.tags.delete": "Delete Tag",
  "com.affine.tags.color": "Tag Color"
}
```

### Step 2: Add Other Languages

```json
// packages/frontend/i18n/src/resources/zh-Hans.json
{
  "com.affine.tags.title": "标签",
  "com.affine.tags.create": "创建标签",
  "com.affine.tags.delete": "删除标签",
  "com.affine.tags.color": "标签颜色"
}
```

### Step 3: Use in Component

```typescript
import { useI18n } from '@affine/i18n';

function TagsList() {
  const t = useI18n();

  return (
    <div>
      <h2>{t['com.affine.tags.title']()}</h2>
      <button>{t['com.affine.tags.create']()}</button>
    </div>
  );
}
```

### Step 4: Test All Languages

```bash
# Switch language in settings
# Verify all strings render correctly
```

---

## How to Debug WebSocket Issues

### Step 1: Enable Logging

```typescript
// Browser console
localStorage.setItem('DEBUG', 'socket.io*');
location.reload();
```

### Step 2: Monitor Connection

```typescript
const socket = io('/sync');

socket.on('connect', () => {
  console.log('✅ Connected:', socket.id);
});

socket.on('disconnect', (reason) => {
  console.log('❌ Disconnected:', reason);
});

socket.on('connect_error', (error) => {
  console.error('Connection error:', error);
});
```

### Step 3: Inspect Messages

```typescript
socket.onAny((event, ...args) => {
  console.log('[WebSocket]', event, args);
});

// Log outgoing messages
const originalEmit = socket.emit;
socket.emit = function (...args) {
  console.log('[Send]', args);
  return originalEmit.apply(this, args);
};
```

### Step 4: Check Network Tab

- Open DevTools → Network
- Filter: `WS` (WebSocket)
- Click connection
- View frames (messages sent/received)

---

## How to Optimize Performance

### Step 1: Profile React Renders

```typescript
import { Profiler } from 'react';

function App() {
  return (
    <Profiler
      id="App"
      onRender={(id, phase, actualDuration) => {
        console.log(`${id} (${phase}) took ${actualDuration}ms`);
      }}
    >
      <AppContent />
    </Profiler>
  );
}
```

### Step 2: Use React DevTools

- Install React DevTools
- Open Profiler tab
- Start recording
- Interact with app
- Stop recording
- Analyze flame graph

### Step 3: Memoize Expensive Computations

```typescript
import { useMemo } from 'react';

function ExpensiveComponent({ data }) {
  const processedData = useMemo(() => {
    // Expensive computation
    return data.map(item => processItem(item));
  }, [data]);

  return <div>{processedData.length} items</div>;
}
```

### Step 4: Use Virtual Lists

```typescript
import { VirtualList } from '@affine/component';

function LongList({ items }) {
  return (
    <VirtualList
      items={items}
      itemHeight={60}
      containerHeight={600}
      renderItem={item => <ListItem key={item.id} item={item} />}
    />
  );
}
```

---

## Summary

These guides cover the most common development tasks. For more complex scenarios, consult the architecture documentation or ask the team.

**See Also**:
- `DEVELOPMENT_WORKFLOW.md` - Daily development workflow
- `CODE_TOURS.md` - Guided code tours
- `DEBUGGING_GUIDE.md` - Advanced debugging

---

*Step-by-step guides for common AFFiNE development tasks.*
