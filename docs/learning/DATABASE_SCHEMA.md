# Database Schema

> **Purpose**: Complete database schema reference for AFFiNE's PostgreSQL database.

---

## Tables

### users

| Column | Type | Description |
|--------|------|-------------|
| id | UUID | Primary key |
| name | VARCHAR | User name |
| email | VARCHAR | Email (unique) |
| password | VARCHAR | Hashed password |
| created_at | TIMESTAMP | Creation time |
| updated_at | TIMESTAMP | Last update |

**Indexes**:
- `users_email_idx` on `email` (unique)

---

### workspaces

| Column | Type | Description |
|--------|------|-------------|
| id | UUID | Primary key |
| name | VARCHAR | Workspace name |
| avatar | TEXT | Avatar URL |
| public | BOOLEAN | Public workspace |
| created_at | TIMESTAMP | Creation time |
| updated_at | TIMESTAMP | Last update |

**Indexes**:
- `workspaces_created_at_idx` on `created_at`

---

### workspace_members

| Column | Type | Description |
|--------|------|-------------|
| id | UUID | Primary key |
| workspace_id | UUID | Foreign key → workspaces |
| user_id | UUID | Foreign key → users |
| role | ENUM | Owner, Admin, Member, External |
| created_at | TIMESTAMP | Join time |

**Indexes**:
- `workspace_members_workspace_id_idx` on `workspace_id`
- `workspace_members_user_id_idx` on `user_id`
- Unique constraint on `(workspace_id, user_id)`

---

### docs

| Column | Type | Description |
|--------|------|-------------|
| id | VARCHAR | Primary key (docId) |
| workspace_id | UUID | Foreign key → workspaces |
| title | TEXT | Document title |
| created_at | TIMESTAMP | Creation time |
| updated_at | TIMESTAMP | Last update |
| deleted_at | TIMESTAMP | Soft delete time |

**Indexes**:
- `docs_workspace_id_idx` on `workspace_id`
- `docs_updated_at_idx` on `updated_at`

---

### doc_snapshots

| Column | Type | Description |
|--------|------|-------------|
| id | UUID | Primary key |
| doc_id | VARCHAR | Foreign key → docs |
| bin | BYTEA | Binary Yjs snapshot |
| timestamp | TIMESTAMP | Snapshot time |

**Indexes**:
- `doc_snapshots_doc_id_idx` on `doc_id`

---

### doc_updates

| Column | Type | Description |
|--------|------|-------------|
| id | UUID | Primary key |
| doc_id | VARCHAR | Foreign key → docs |
| bin | BYTEA | Binary Yjs update |
| timestamp | TIMESTAMP | Update time |
| editor | UUID | User who made update |

**Indexes**:
- `doc_updates_doc_id_timestamp_idx` on `(doc_id, timestamp)`

---

### sessions

| Column | Type | Description |
|--------|------|-------------|
| id | VARCHAR | Session ID (primary key) |
| user_id | UUID | Foreign key → users |
| expires_at | TIMESTAMP | Expiration time |
| created_at | TIMESTAMP | Creation time |

**Indexes**:
- `sessions_user_id_idx` on `user_id`
- `sessions_expires_at_idx` on `expires_at`

---

## Relationships

```
users
  ├── workspace_members (one-to-many)
  ├── sessions (one-to-many)
  └── doc_updates (one-to-many)

workspaces
  ├── workspace_members (one-to-many)
  └── docs (one-to-many)

docs
  ├── doc_snapshots (one-to-many)
  └── doc_updates (one-to-many)
```

---

## Migrations

**Location**: `packages/backend/server/prisma/migrations/`

**Create migration**:
```bash
yarn prisma migrate dev --name add_new_table
```

**Apply migrations**:
```bash
yarn prisma migrate deploy
```

---

*Complete database schema reference for AFFiNE.*
