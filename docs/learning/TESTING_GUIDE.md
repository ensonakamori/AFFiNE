# Testing Guide

> **Purpose**: Comprehensive testing strategies for AFFiNE - unit, integration, E2E, and best practices.

---

## Testing Stack

- **Vitest 3.1** - Unit/integration tests
- **Playwright 1.52** - E2E tests
- **Testing Library** - Component tests
- **MSW** - API mocking

---

## Unit Testing

### Example Test

```typescript
import { describe, it, expect, vi } from 'vitest';
import { WorkspaceService } from './workspace';

describe('WorkspaceService', () => {
  it('should create workspace', async () => {
    const service = new WorkspaceService();
    const workspace = await service.create('My Workspace');

    expect(workspace.name).toBe('My Workspace');
    expect(workspace.id).toBeDefined();
  });

  it('should throw if name too long', async () => {
    const service = new WorkspaceService();
    const longName = 'a'.repeat(101);

    await expect(service.create(longName)).rejects.toThrow('Name too long');
  });
});
```

### Mocking

```typescript
// Mock service
const mockWorkspaceService = {
  create: vi.fn().mockResolvedValue({ id: '123', name: 'Test' }),
  get: vi.fn(),
};

framework.override(WorkspaceService, mockWorkspaceService);
```

---

## Component Testing

```typescript
import { render, screen, fireEvent } from '@testing-library/react';
import { WorkspaceCard } from './workspace-card';

describe('WorkspaceCard', () => {
  it('should render workspace name', () => {
    const workspace = { id: '1', name: 'My Workspace' };
    render(<WorkspaceCard workspace={workspace} />);

    expect(screen.getByText('My Workspace')).toBeInTheDocument();
  });

  it('should call onClick when clicked', () => {
    const onClick = vi.fn();
    const workspace = { id: '1', name: 'Test' };

    render(<WorkspaceCard workspace={workspace} onClick={onClick} />);
    fireEvent.click(screen.getByText('Test'));

    expect(onClick).toHaveBeenCalledWith(workspace);
  });
});
```

---

## E2E Testing

```typescript
import { test, expect } from '@playwright/test';

test('create workspace and page', async ({ page }) => {
  await page.goto('http://localhost:5173');

  // Create workspace
  await page.click('button:has-text("New Workspace")');
  await page.fill('input[name="name"]', 'Test Workspace');
  await page.click('button:has-text("Create")');

  // Verify created
  await expect(page.locator('h1')).toHaveText('Test Workspace');

  // Create page
  await page.click('button:has-text("New Page")');
  await page.fill('.editor-title', 'My Page');

  // Verify page in sidebar
  await expect(page.locator('.sidebar')).toContainText('My Page');
});
```

---

## Best Practices

1. **Arrange-Act-Assert** structure
2. **Descriptive test names** (`should X when Y`)
3. **One assertion per test** (or related assertions)
4. **Clean up** after tests (mocks, state)
5. **Test edge cases** (empty, null, errors)
6. **Use factories** for test data
7. **Mock external dependencies** (APIs, services)

---

*Comprehensive testing guide for AFFiNE.*
