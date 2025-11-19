# Code Tours

> **Purpose**: Guided tours through specific features to understand how they work end-to-end.

---

## Table of Contents

1. [Tour 1: Creating a Page](#tour-1-creating-a-page)
2. [Tour 2: Real-Time Collaboration](#tour-2-real-time-collaboration)
3. [Tour 3: GraphQL Request Flow](#tour-3-graphql-request-flow)
4. [Tour 4: Image Upload](#tour-4-image-upload)

---

## Tour 1: Creating a Page

**Goal**: Understand what happens when a user creates a new page.

### Entry Point

```typescript
// File: packages/frontend/core/src/components/workspace-header.tsx:45
function NewPageButton() {
  const docsService = useService(DocsService);

  const handleClick = () => {
    const newDoc = docsService.createDoc(); // ← START HERE
    navigate(`/workspace/${workspaceId}/${newDoc.id}`);
  };

  return <button onClick={handleClick}>New Page</button>;
}
```

### Step 1: DocsService.createDoc()

```typescript
// File: packages/frontend/core/src/modules/doc/services/docs.ts:42
export class DocsService extends Service {
  createDoc(options?: { id?: string; skipInit?: boolean }) {
    const workspace = this.workspace.docCollection; // Get BlockSuite workspace

    // Generate or use provided ID
    const docId = options?.id ?? nanoid();

    // Create Y.Doc for this page
    const yDoc = new Y.Doc({ guid: docId });

    // Initialize page structure
    if (!options?.skipInit) {
      this.initializePageStructure(yDoc);
    }

    // Add to workspace
    workspace.pages.set(docId, yDoc);

    return { id: docId };
  }
}
```

**What happens**:
1. Generates unique ID (`nanoid()`)
2. Creates Yjs document
3. Initializes page structure (root block, title)
4. Adds to workspace collection

### Step 2: Initialize Page Structure

```typescript
// File: packages/frontend/core/src/modules/doc/services/docs.ts:67
private initializePageStructure(yDoc: Y.Doc) {
  const blocks = yDoc.getMap('blocks');

  // Create root page block
  const pageBlockId = `page:${yDoc.guid}`;
  blocks.set(pageBlockId, new Y.Map([
    ['type', 'page'],
    ['props', new Y.Map([
      ['title', new Y.Text()], // Empty title
    ])],
    ['children', new Y.Array()],
  ]));

  // Create first paragraph
  const paragraphId = nanoid();
  blocks.set(paragraphId, new Y.Map([
    ['type', 'paragraph'],
    ['text', new Y.Text()],
    ['children', new Y.Array()],
  ]));

  // Add paragraph to page children
  const pageBlock = blocks.get(pageBlockId);
  pageBlock.get('children').push([paragraphId]);
}
```

**What happens**:
1. Creates page block (root)
2. Creates empty paragraph
3. Links paragraph as child of page

### Step 3: Save to IndexedDB

```typescript
// File: packages/common/nbstore/src/impls/idb/doc.ts:38
async pushDocUpdate(update: DocUpdate, origin?: string): Promise<DocClock> {
  const db = await this.connection.inner.db;

  // Encode Yjs document as binary
  const bin = encodeStateAsUpdate(yDoc);

  // Store in IndexedDB
  await db.transaction('updates', 'readwrite')
    .objectStore('updates')
    .put({
      docId: update.docId,
      bin: update.bin,
      timestamp: new Date(),
      editor: getCurrentUserId(),
    });

  return { docId: update.docId, timestamp: new Date() };
}
```

**What happens**:
1. Yjs document encoded as binary
2. Saved to IndexedDB immediately (offline-first)
3. Timestamp recorded

### Step 4: Sync to Cloud (Async)

```typescript
// File: packages/common/nbstore/src/impls/cloud/doc.ts:55
async pushDocUpdate(update: DocUpdate): Promise<DocClock> {
  // Send via WebSocket
  this.socket.emit('doc:update', {
    workspaceId: this.workspaceId,
    docId: update.docId,
    bin: Array.from(update.bin),
  });

  return { docId: update.docId, timestamp: new Date() };
}
```

**What happens**:
1. Binary update sent via WebSocket
2. Server stores in PostgreSQL
3. Broadcast to other clients

### Step 5: Navigate to Page

```typescript
// File: packages/frontend/core/src/modules/navigation/services/navigator.ts:45
export class NavigatorService extends Service {
  navigateToPage(workspaceId: string, pageId: string) {
    this.router.navigate(`/workspace/${workspaceId}/${pageId}`);
  }
}
```

**What happens**:
1. React Router navigates to new URL
2. Page component loads
3. Editor renders

### Timeline

```
User clicks "New Page"
    ↓
DocsService.createDoc()          (< 1ms)
    ↓
Create Y.Doc + initialize        (< 1ms)
    ↓
Save to IndexedDB                (< 10ms)
    ↓
Navigate to page                 (< 1ms)
    ↓
[Background] Sync to cloud       (100-500ms, async)
```

**Total time to show new page**: ~12ms (feels instant)

---

## Tour 2: Real-Time Collaboration

**Goal**: Understand how changes from User A appear on User B's screen.

### User A Types "Hello"

```typescript
// File: packages/frontend/core/src/blocksuite/blocks/paragraph/paragraph.ts:78
contentElement.addEventListener('input', () => {
  const text = contentElement.textContent ?? '';

  // Update Y.Text
  yText.delete(0, yText.length);
  yText.insert(0, text);
  // This triggers Y.Doc 'update' event
});
```

### Y.Doc Generates Update

```typescript
// File: packages/frontend/core/src/modules/workspace/entities/workspace.ts:65
doc.on('update', (update: Uint8Array, origin: string) => {
  console.log('Update generated:', update.length, 'bytes');

  // Save locally
  this.localDocStorage.pushDocUpdate({ docId: doc.guid, bin: update });

  // Send to cloud
  this.cloudDocStorage.pushDocUpdate({ docId: doc.guid, bin: update });
});
```

### WebSocket Sends Update

```typescript
// File: packages/common/nbstore/src/impls/cloud/doc.ts:82
async pushDocUpdate(update: DocUpdate) {
  this.socket.emit('doc:update', {
    workspaceId: this.workspaceId,
    docId: update.docId,
    bin: Array.from(update.bin),
  });
}
```

### Server Receives & Broadcasts

```typescript
// File: packages/backend/server/src/core/sync/gateway.ts:45
@SubscribeMessage('doc:update')
async handleDocUpdate(
  @ConnectedSocket() socket: Socket,
  @MessageBody() data: { workspaceId: string; docId: string; bin: number[] }
) {
  // Store in database
  await this.docStorage.pushDocUpdate({
    docId: data.docId,
    bin: new Uint8Array(data.bin),
  });

  // Broadcast to all other clients
  socket.to(data.workspaceId).emit('doc:update', data);
}
```

### User B Receives Update

```typescript
// File: packages/common/nbstore/src/impls/cloud/doc.ts:95
this.socket.on('doc:update', ({ docId, bin }) => {
  const update = new Uint8Array(bin);

  // Apply to Y.Doc (CRDT merge)
  Y.applyUpdate(this.doc, update);

  // UI updates automatically via Lit reactivity
});
```

### UI Updates

```typescript
// File: packages/frontend/core/src/blocksuite/blocks/paragraph/paragraph.ts:55
yText.observe(() => {
  if (document.activeElement !== contentElement) {
    // Update DOM only if user is not typing
    contentElement.textContent = yText.toString();
  }
});
```

### Timeline

```
User A types "H"
    ↓
Y.Text.insert(0, "H")               (< 1ms)
    ↓
Y.Doc emits update event            (< 1ms)
    ↓
Save to IndexedDB (User A)          (< 10ms)
    ↓
Send via WebSocket                  (< 1ms)
    ↓
[Network latency]                   (20-50ms)
    ↓
Server receives                     (< 1ms)
    ↓
Server broadcasts                   (< 1ms)
    ↓
[Network latency]                   (20-50ms)
    ↓
User B receives                     (< 1ms)
    ↓
Y.applyUpdate (CRDT merge)          (< 1ms)
    ↓
DOM updates (User B)                (< 1ms)
```

**Total latency**: ~50-100ms (User B sees "H" appear)

---

## Tour 3: GraphQL Request Flow

**Goal**: Trace a GraphQL query from React component to database and back.

### Frontend: useQuery Hook

```typescript
// File: packages/frontend/core/src/components/workspace-list.tsx:15
function WorkspaceList() {
  const { data } = useQuery({
    query: getWorkspacesQuery,
  });

  return (
    <ul>
      {data.workspaces.map(ws => <li key={ws.id}>{ws.name}</li>)}
    </ul>
  );
}
```

### SWR Fetcher

```typescript
// File: packages/frontend/core/src/components/hooks/use-query.ts:64
return useSWR(
  ['cloud', options.query.id, options.variables],
  () => graphqlService.gql(options),
  { suspense: true }
);
```

### GraphQL Service

```typescript
// File: packages/frontend/core/src/modules/cloud/services/graphql.ts:35
async gql<Query>(options: QueryOptions<Query>): Promise<QueryResponse<Query>> {
  const response = await fetch('/graphql', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    credentials: 'include', // Send cookies
    body: JSON.stringify({
      query: options.query.query,
      variables: options.variables,
    }),
  });

  return await response.json();
}
```

### Backend: NestJS GraphQL

```typescript
// File: packages/backend/server/src/core/workspaces/resolvers/workspace.ts:200
@Query(() => [WorkspaceType], { name: 'workspaces' })
async getWorkspaces(@CurrentUser() user: CurrentUser): Promise<WorkspaceType[]> {
  return await this.workspaceService.listWorkspaces(user.id);
}
```

### Service → Prisma

```typescript
// File: packages/backend/server/src/core/workspaces/service.ts:67
async listWorkspaces(userId: string): Promise<Workspace[]> {
  return await this.models.workspace.findMany({
    where: {
      members: {
        some: { userId },
      },
    },
    include: {
      members: {
        include: { user: true },
      },
    },
    orderBy: { createdAt: 'desc' },
  });
}
```

### Prisma → PostgreSQL

```sql
SELECT
  w.id,
  w.name,
  w.avatar,
  w.created_at,
  json_agg(json_build_object(
    'id', m.id,
    'role', m.role,
    'user', json_build_object('id', u.id, 'name', u.name)
  )) as members
FROM workspaces w
INNER JOIN workspace_members m ON m.workspace_id = w.id
INNER JOIN users u ON u.id = m.user_id
WHERE m.user_id = $1
GROUP BY w.id
ORDER BY w.created_at DESC;
```

### Response Flow

```
PostgreSQL result
    ↓
Prisma transforms to objects
    ↓
WorkspaceService returns
    ↓
WorkspaceResolver formats for GraphQL
    ↓
NestJS serializes to JSON
    ↓
HTTP response
    ↓
GraphQLService parses JSON
    ↓
SWR caches result
    ↓
React component re-renders
```

---

## Tour 4: Image Upload

**Goal**: Follow an image from file picker to S3 and editor.

### User Selects Image

```typescript
// File: packages/frontend/core/src/components/image-upload.tsx:23
function ImageUpload() {
  const handleFileSelect = async (file: File) => {
    const blobKey = await uploadImage(file);
    insertImageBlock(blobKey);
  };

  return <input type="file" onChange={e => handleFileSelect(e.target.files[0])} />;
}
```

### Calculate Blob Key (Hash)

```typescript
// File: packages/frontend/core/src/modules/blob/utils.ts:12
async function calculateBlobKey(file: File): Promise<string> {
  const arrayBuffer = await file.arrayBuffer();
  const hashBuffer = await crypto.subtle.digest('SHA-256', arrayBuffer);

  return Array.from(new Uint8Array(hashBuffer))
    .map(b => b.toString(16).padStart(2, '0'))
    .join('');
}
```

### Save to IndexedDB

```typescript
// File: packages/common/nbstore/src/impls/idb/blob.ts:38
async set(blob: BlobRecord) {
  const db = await this.connection.inner.db;

  await db.transaction(['blobs', 'blobData'], 'readwrite')
    .objectStore('blobs')
    .put({
      key: blob.key,
      mime: blob.mime,
      size: blob.data.byteLength,
      createdAt: new Date(),
    });

  await db.objectStore('blobData').put({
    key: blob.key,
    data: blob.data,
  });
}
```

### Upload to Server

```typescript
// File: packages/common/nbstore/src/impls/cloud/blob.ts:67
async upload(key: string) {
  const blob = await this.localBlobStorage.get(key);

  const formData = new FormData();
  formData.append('blob', new Blob([blob.data], { type: blob.mime }));

  await fetch(`/api/workspaces/${this.workspaceId}/blobs/${key}`, {
    method: 'POST',
    body: formData,
  });
}
```

### Server Uploads to S3

```typescript
// File: packages/backend/server/src/core/workspaces/controller.ts:38
@Post('/:id/blobs/:name')
async uploadBlob(
  @Param('id') workspaceId: string,
  @Param('name') name: string,
  @Body() buffer: Buffer,
) {
  await this.storage.put(workspaceId, name, buffer);
}

// File: packages/backend/server/src/core/storage/s3.ts:45
async put(workspaceId: string, key: string, data: Buffer) {
  await this.s3Client.send(
    new PutObjectCommand({
      Bucket: this.bucket,
      Key: `${workspaceId}/${key}`,
      Body: data,
    })
  );
}
```

### Insert Image Block

```typescript
// File: packages/frontend/core/src/blocksuite/image-block.ts:88
function insertImageBlock(blobKey: string) {
  const page = getCurrentPage();

  page.addBlock('affine:image', {
    sourceId: blobKey, // References blob by key
    width: null,       // Auto width
    height: null,      // Auto height
  });
}
```

### Render Image

```typescript
// File: packages/frontend/core/src/blocksuite/blocks/image/image.ts:55
render() {
  const workspace = this.page.workspace;

  // Load blob from storage
  workspace.blobSource.get(this.model.sourceId).then(blob => {
    const url = URL.createObjectURL(blob);
    this.imgElement.src = url;
  });

  return html`<img>`;
}
```

### Timeline

```
User selects image
    ↓
Calculate SHA-256 hash           (10-100ms, depends on size)
    ↓
Save to IndexedDB                (10-50ms)
    ↓
Insert image block               (< 1ms)
    ↓
Render image (from IndexedDB)    (< 10ms)
    ↓
[Background] Upload to S3        (500-2000ms, async)
```

**Image appears immediately** (from IndexedDB), uploads in background.

---

## Summary

These tours show how data flows through AFFiNE for common operations. Use them as a starting point to explore the codebase.

**See Also**:
- `DATA_FLOW_GUIDE.md` - Data flow patterns
- `ARCHITECTURE_OVERVIEW.md` - High-level architecture

---

*Guided code tours through key AFFiNE features.*
