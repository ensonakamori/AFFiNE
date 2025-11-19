# Security Guide

> **Purpose**: Security best practices and vulnerability prevention for AFFiNE.

---

## Authentication

### Session Security

✅ **DO**:
```typescript
// Use HTTP-only cookies
res.cookie('session_id', sessionId, {
  httpOnly: true,    // Prevents XSS access
  secure: true,      // HTTPS only
  sameSite: 'strict',// CSRF protection
  maxAge: 7 * 24 * 60 * 60 * 1000, // 7 days
});
```

❌ **DON'T**:
```typescript
// Don't store tokens in localStorage
localStorage.setItem('token', token); // ❌ Vulnerable to XSS
```

### Password Handling

✅ **DO**:
```typescript
import bcrypt from 'bcrypt';

const SALT_ROUNDS = 12;

async function hashPassword(password: string): Promise<string> {
  return await bcrypt.hash(password, SALT_ROUNDS);
}

async function verifyPassword(password: string, hash: string): Promise<boolean> {
  return await bcrypt.compare(password, hash);
}
```

❌ **DON'T**:
```typescript
// Don't store plaintext passwords
await db.user.create({ password }); // ❌

// Don't use weak hashing
import crypto from 'crypto';
const hash = crypto.createHash('md5').update(password).digest('hex'); // ❌
```

---

## Authorization

### Permission Checks

✅ **DO**:
```typescript
@Mutation(() => WorkspaceType)
async deleteWorkspace(
  @CurrentUser() user: CurrentUser,
  @Args('id') id: string,
) {
  // Always check permissions server-side
  await this.ac
    .user(user.id)
    .workspace(id)
    .assert('Workspace.Delete');

  return await this.workspaceService.delete(id);
}
```

❌ **DON'T**:
```typescript
// Don't rely on client-side checks
if (canDelete) { // ❌ Client can bypass this
  deleteWorkspace(id);
}
```

---

## Input Validation

### Sanitize Inputs

✅ **DO**:
```typescript
import { IsString, MinLength, MaxLength, IsEmail } from 'class-validator';

export class CreateUserInput {
  @IsEmail()
  email!: string;

  @IsString()
  @MinLength(8)
  @MaxLength(100)
  password!: string;

  @IsString()
  @MinLength(1)
  @MaxLength(50)
  name!: string;
}
```

### Prevent SQL Injection

✅ **DO**:
```typescript
// Use Prisma (parameterized queries)
await prisma.user.findMany({
  where: { email: userInput }, // ✅ Safe
});
```

❌ **DON'T**:
```typescript
// Don't use raw SQL with user input
await prisma.$executeRaw`SELECT * FROM users WHERE email = ${userInput}`; // ❌
```

### Prevent XSS

✅ **DO**:
```typescript
// React auto-escapes by default
<div>{userInput}</div> // ✅ Safe

// Use dangerouslySetInnerHTML only with sanitized HTML
import DOMPurify from 'dompurify';

const clean = DOMPurify.sanitize(userInput);
<div dangerouslySetInnerHTML={{ __html: clean }} />
```

❌ **DON'T**:
```typescript
// Don't inject raw HTML
<div dangerouslySetInnerHTML={{ __html: userInput }} /> // ❌
```

---

## CSRF Protection

✅ **DO**:
```typescript
// Use SameSite cookies
res.cookie('session_id', sessionId, {
  sameSite: 'strict', // ✅ CSRF protection
});

// Use CSRF tokens for forms
import csurf from 'csurf';
app.use(csurf());
```

---

## Rate Limiting

✅ **DO**:
```typescript
import { ThrottlerGuard } from '@nestjs/throttler';

@UseGuards(ThrottlerGuard)
@Mutation(() => Boolean)
async login(@Args('email') email: string, @Args('password') password: string) {
  // Limited to X requests per minute
}
```

---

## Data Privacy

### Sensitive Data

✅ **DO**:
```typescript
// Exclude sensitive fields
export type PublicUser = Omit<User, 'password' | 'email'>;

// Don't return passwords
@Query(() => UserType)
async user(@Args('id') id: string) {
  const user = await this.models.user.findUnique({ where: { id } });

  const { password, ...publicUser } = user;
  return publicUser;
}
```

---

## Checklist

- [ ] Use HTTPS in production
- [ ] HTTP-only cookies for sessions
- [ ] Hash passwords with bcrypt
- [ ] Validate all inputs
- [ ] Check permissions server-side
- [ ] Sanitize HTML output
- [ ] Rate limit sensitive endpoints
- [ ] Keep dependencies updated
- [ ] Enable CSP headers
- [ ] Log security events

---

*Security best practices for AFFiNE development.*
