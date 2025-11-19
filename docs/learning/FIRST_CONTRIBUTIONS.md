# First Contributions Guide

> **Purpose**: Step-by-step guide for making your first contribution to AFFiNE.

---

## Prerequisites

- ✅ Read `GETTING_STARTED.md`
- ✅ Set up development environment
- ✅ Run app locally
- ✅ Understand basic architecture

---

## Finding Your First Issue

### Good First Issues

Visit: https://github.com/toeverything/AFFiNE/labels/good%20first%20issue

**Look for**:
- 🐛 **Bug fixes** - Clear scope, well-defined
- 📝 **Documentation** - Low risk, high value
- 🎨 **UI polish** - Visual improvements
- ♿ **Accessibility** - A11y improvements

### Claim an Issue

1. Comment: "I'd like to work on this!"
2. Wait for maintainer assignment
3. Ask questions if unclear

---

## Making Your First PR

### Step 1: Fork & Clone

```bash
# Fork on GitHub
# Then clone your fork
git clone https://github.com/YOUR_USERNAME/AFFiNE.git
cd AFFiNE

# Add upstream remote
git remote add upstream https://github.com/toeverything/AFFiNE.git
```

### Step 2: Create Branch

```bash
git checkout canary
git pull upstream canary

git checkout -b fix/issue-123-description
```

### Step 3: Make Changes

```bash
# Make your changes
# Test locally
yarn dev

# Run tests
yarn test:unit

# Check types
yarn typecheck

# Lint
yarn lint:fix
```

### Step 4: Commit

```bash
git add .
git commit -m "fix: resolve issue #123

- Fixed X
- Updated Y
- Added tests

Closes #123"
```

### Step 5: Push & PR

```bash
git push origin fix/issue-123-description

# Create PR on GitHub
# Fill out PR template
# Link to issue
```

---

## PR Checklist

Before submitting:

- [ ] Code follows style guidelines
- [ ] All tests pass
- [ ] Added tests for new features
- [ ] Updated documentation
- [ ] No console.logs or debuggers
- [ ] Commit messages follow convention
- [ ] PR description is clear
- [ ] Linked to issue

---

## After Submitting

1. **Wait for CI** - Automated checks run
2. **Respond to review** - Address feedback
3. **Make changes** - Push new commits
4. **Get approved** - Maintainer approves
5. **Merge** - PR merged! 🎉

---

## Example First Contributions

### Easy: Fix Typo in Documentation

```bash
# 1. Find typo in docs/README.md
# 2. Fix it
# 3. Commit
git commit -m "docs: fix typo in README"

# 4. Submit PR
# Time: 5 minutes
```

### Medium: Add Missing Translation

```bash
# 1. Find missing translation key
# 2. Add to packages/frontend/i18n/src/resources/en.json
# 3. Add to all language files
# 4. Test in app
# 5. Submit PR
# Time: 30 minutes
```

### Advanced: Fix Bug

```bash
# 1. Reproduce bug
# 2. Write failing test
# 3. Fix bug
# 4. Verify test passes
# 5. Submit PR with test
# Time: 2-4 hours
```

---

## Getting Help

- 💬 **Discord**: Join AFFiNE Discord
- 📧 **Email**: contact@toeverything.so
- 🐛 **Issues**: Comment on GitHub issue
- 📖 **Docs**: Read this documentation

---

## Recognition

Your contribution will be:
- ✅ Listed in CONTRIBUTORS.md
- ✅ Mentioned in release notes
- ✅ Visible on GitHub profile

---

## Next Steps

After your first PR:

1. **Tackle harder issues** - Build confidence
2. **Review others' PRs** - Learn by reviewing
3. **Propose features** - Share ideas
4. **Become maintainer** - Active contributors can become maintainers

---

**Welcome to the AFFiNE community! 🎉**

---

*Guide for making your first contribution to AFFiNE.*
