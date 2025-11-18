# Getting Started with AFFiNE Development

**Documented:** November 2025
**Tech Stack Research:** [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md)
**Prerequisites:** None (this is the starting point!)

> **Goal:** Get your development environment set up and run AFFiNE locally within 30 minutes.

---

## Table of Contents

- [What You'll Learn](#what-youll-learn)
- [System Requirements](#system-requirements)
- [Quick Start (TL;DR)](#quick-start-tldr)
- [Step 1: Install Prerequisites](#step-1-install-prerequisites)
- [Step 2: Clone the Repository](#step-2-clone-the-repository)
- [Step 3: Install Dependencies](#step-3-install-dependencies)
- [Step 4: Build Native Modules](#step-4-build-native-modules)
- [Step 5: Start Development](#step-5-start-development)
- [Development Modes](#development-modes)
- [Common Issues & Troubleshooting](#common-issues--troubleshooting)
- [Verification Checklist](#verification-checklist)
- [Next Steps](#next-steps)

---

## What You'll Learn

After completing this guide, you will:

✅ Have all required tools installed (Node.js, Rust, Docker)
✅ Have the AFFiNE repository cloned and configured
✅ Be able to run the **web app** locally
✅ Be able to run the **backend server** with database
✅ Understand different development modes
✅ Know how to verify your setup is working correctly

**Estimated Time:** 30-45 minutes (first time)

---

## System Requirements

### Operating Systems
- ✅ **macOS** (Intel or Apple Silicon)
- ✅ **Linux** (Ubuntu 20.04+, Debian, Fedora, Arch)
- ✅ **Windows** 10/11 (with WSL2 recommended, or Developer Mode)

### Hardware
- **RAM:** 8GB minimum, 16GB recommended
- **Disk:** 10GB free space (for dependencies and build artifacts)
- **CPU:** Modern multi-core processor

### Software Versions
See [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md) for detailed version analysis.

- **Node.js:** 20.x or 22.x LTS (project requires <23.0.0)
- **Yarn:** 4.9.1+ (Berry/modern Yarn)
- **Rust:** Latest stable (Edition 2024)
- **Docker:** 24.0+ (for backend development)
- **Git:** 2.30+

---

## Quick Start (TL;DR)

**For experienced developers who want to get started immediately:**

```bash
# 1. Install Node.js 22 LTS + Rust + Docker
# 2. Clone repo
git clone https://github.com/toeverything/AFFiNE.git
cd AFFiNE

# 3. Enable modern Yarn
corepack enable
corepack prepare yarn@stable --activate

# 4. Install dependencies
yarn install

# 5. Build native modules
yarn affine @affine/native build
yarn affine @affine/server-native build

# 6. Start web app (frontend only)
yarn dev
```

**Visit:** http://localhost:8080

**For full setup with backend**, see [Step 5: Start Development](#step-5-start-development).

---

## Step 1: Install Prerequisites

### 1.1 Install Node.js

AFFiNE requires **Node.js 20.x or 22.x** (LTS versions).

✅ **CURRENT (Nov 2025):** Node.js 22.x "Jod" is the recommended LTS version.

#### Option A: Using Node Version Manager (Recommended)

**fnm** (Fast Node Manager):

```bash
# Install fnm
curl -fsSL https://fnm.vercel.app/install | bash

# Install Node.js 22 LTS
fnm install 22
fnm use 22

# Verify
node --version  # Should show v22.x.x
```

**nvm** (Node Version Manager):

```bash
# Install nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

# Install Node.js 22 LTS
nvm install 22
nvm use 22

# Verify
node --version  # Should show v22.x.x
```

#### Option B: Manual Installation

Download from [nodejs.org/en/download](https://nodejs.org/en/download) and install Node.js 22 LTS.

#### Verify Installation

```bash
node --version  # Should show v22.x.x
npm --version   # Should show 10.x.x
```

---

### 1.2 Enable Modern Yarn (4.x)

AFFiNE uses **Yarn 4.9.1** (Berry/modern Yarn), not classic Yarn 1.x.

✅ **CURRENT (Nov 2025):** Yarn 4.11.0 is latest, project uses 4.9.1 (current)

```bash
# Enable corepack (comes with Node.js)
corepack enable

# Activate latest stable Yarn
corepack prepare yarn@stable --activate

# Verify Yarn version
yarn --version  # Should show 4.x.x
```

🧠 **Mental Model:** Corepack is Node.js's built-in package manager manager. Think of it as "the tool that manages Yarn/pnpm versions."

---

### 1.3 Install Rust Toolchain

AFFiNE has Rust modules for performance-critical operations (native bindings via NAPI-RS).

✅ **CURRENT (Nov 2025):** Rust Edition 2024 (latest)

```bash
# Install Rust via rustup (official method)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Follow prompts, accept defaults

# Reload shell or run:
source "$HOME/.cargo/env"

# Verify installation
rustc --version   # Should show 1.85.0+ (Edition 2024)
cargo --version   # Should show 1.85.0+
```

⚠️ **macOS-specific:** Use `strip` from system instead of `binutils`:
```bash
# Already default on macOS, no action needed
# If issues occur, see: https://github.com/toeverything/AFFiNE/discussions/2840
```

---

### 1.4 Install Docker (For Backend Development)

Docker is required to run PostgreSQL, Redis, and Mailhog for backend development.

✅ **CURRENT (Nov 2025):** Docker 24.0+ recommended

#### macOS
```bash
# Install Docker Desktop
# Download from: https://www.docker.com/products/docker-desktop

# Or via Homebrew
brew install --cask docker

# Verify
docker --version
docker compose version
```

#### Linux (Ubuntu/Debian)
```bash
# Install Docker
curl -fsSL https://get.docker.com | sh

# Add user to docker group (avoid sudo)
sudo usermod -aG docker $USER
newgrp docker

# Verify
docker --version
docker compose version
```

#### Windows
- Install **Docker Desktop for Windows**
- Enable **WSL2 backend** (recommended)
- Or enable **Hyper-V** (alternative)

Download from: https://www.docker.com/products/docker-desktop

---

### 1.5 Verify All Prerequisites

Run this checklist to verify everything is installed:

```bash
# Node.js
node --version    # v22.x.x

# Yarn
yarn --version    # 4.x.x

# Rust
rustc --version   # 1.85.0+
cargo --version   # 1.85.0+

# Docker
docker --version  # 24.0+
docker compose version  # 2.20.0+

# Git
git --version     # 2.30+
```

If all commands show versions, you're ready to proceed! 🎉

---

## Step 2: Clone the Repository

### 2.1 Clone via HTTPS (Recommended for most users)

```bash
git clone https://github.com/toeverything/AFFiNE.git
cd AFFiNE
```

### 2.2 Clone via SSH (If you have SSH keys set up)

```bash
git clone git@github.com:toeverything/AFFiNE.git
cd AFFiNE
```

### 2.3 Windows-Specific: Enable Symbolic Links

⚠️ **Windows Users:** AFFiNE uses symbolic links. You must enable Developer Mode.

**Method 1: Enable Developer Mode (Recommended)**
1. Open Settings → Update & Security → For Developers
2. Enable "Developer Mode"
3. Restart terminal/command prompt

**Method 2: Run as Administrator**
```bash
# Run PowerShell or Command Prompt as Administrator
git config --global core.symlinks true
git clone https://github.com/toeverything/AFFiNE.git
```

See: [Microsoft Docs - Enable Developer Mode](https://learn.microsoft.com/en-us/windows/apps/get-started/enable-your-device-for-development)

---

## Step 3: Install Dependencies

### 3.1 Install All Dependencies

From the repository root:

```bash
yarn install
```

This will:
- Install all JavaScript/TypeScript dependencies
- Set up Yarn workspaces for the monorepo
- Link internal packages (e.g., `@affine/core`, `@affine/server`)
- Download and configure additional tooling

**Time:** 3-5 minutes (depending on internet speed)

🧠 **Mental Model:** Yarn workspaces create a monorepo where all packages share dependencies. Think of it as "one big node_modules for the entire project."

### 3.2 Verify Installation

```bash
# Check that workspaces are set up
yarn workspaces list

# You should see output like:
# YN0000: @affine/monorepo
# YN0000: @affine/core
# YN0000: @affine/server
# ... (many more)
```

---

## Step 4: Build Native Modules

AFFiNE has Rust modules that must be compiled before first run.

### 4.1 Build Frontend Native Modules

```bash
yarn affine @affine/native build
```

This builds native modules at:
- `packages/frontend/native/` - Rust code
- Creates Node.js bindings via NAPI-RS

**Time:** 2-5 minutes (first build, then incremental)

⚠️ **Common Issue:** If build fails, ensure Rust is properly installed:
```bash
rustc --version  # Should show 1.85.0+
```

### 4.2 Build Backend Native Modules

```bash
yarn affine @affine/server-native build
```

This builds server-specific native modules at:
- `packages/backend/native/` - Rust server utilities

**Time:** 1-3 minutes

### 4.3 Build Reader Package (Optional but Recommended)

```bash
yarn affine @affine/reader build
```

The reader package handles document parsing and reading.

---

## Step 5: Start Development

You can run AFFiNE in different modes depending on what you're working on.

### Mode 1: Frontend Only (Quickest Start)

**Use this if:** You're working on UI/UX, don't need auth or cloud sync.

```bash
# From repository root
yarn dev
```

**What this does:**
- Starts Vite dev server (frontend)
- Runs at http://localhost:8080
- Hot Module Replacement (HMR) enabled
- Uses local-first mode (IndexedDB)
- No backend server needed

**Access:** Open http://localhost:8080

🎯 **Remember This:** `yarn dev` = frontend only, local-first

---

### Mode 2: Full Stack (Frontend + Backend)

**Use this if:** You need authentication, cloud sync, collaboration, GraphQL API.

#### Step 5.1: Set Up Docker Services

Start PostgreSQL, Redis, and Mailhog:

```bash
# Copy example configs
cp ./.docker/dev/compose.yml.example ./.docker/dev/compose.yml
cp ./.docker/dev/.env.example ./.docker/dev/.env

# Start services
docker compose -f ./.docker/dev/compose.yml up
```

**This starts:**
- **PostgreSQL** (pgvector/pg16) - Database on port 5432
- **Redis** - Cache/queue on port 6379
- **Mailhog** - Email testing on port 1025 (SMTP), 8025 (web UI)

Leave this running in a separate terminal.

🔍 **Verify Services:**
```bash
docker ps  # Should show 3 running containers
```

#### Step 5.2: Configure Backend Environment

```bash
# Copy environment variables
cp packages/backend/server/.env.example packages/backend/server/.env

# Edit .env if needed (optional for local dev)
# Default values work for most cases
```

#### Step 5.3: Initialize Database

```bash
# Run Prisma migrations and seed data
yarn affine server init
```

**This will:**
- Create database schema
- Run Prisma migrations
- Seed test users (see below)

**Test Users Created:**

| Email | Password | Type | Workspace Limit |
|-------|----------|------|-----------------|
| dev@affine.pro | dev | Default | 3 members |
| pro@affine.pro | pro | Pro | 10 members |
| team@affine.pro | team | Team | 10 members + Team Workspace |

#### Step 5.4: Start Backend Server

```bash
# In terminal 2 (Docker services running in terminal 1)
yarn affine server dev
```

**Backend will start at:**
- **GraphQL API:** http://localhost:3010/graphql
- **GraphQL Playground:** http://localhost:3010/graphql (in browser)
- **REST API:** http://localhost:3010/api/

#### Step 5.5: Start Frontend

```bash
# In terminal 3
yarn dev
```

**Frontend will start at:** http://localhost:8080

**Login with:**
- Email: `dev@affine.pro`
- Password: `dev`

🎉 **You now have the full stack running!**

---

### Mode 3: Desktop App (Electron)

**Use this if:** You're working on desktop-specific features.

```bash
# Build desktop app
yarn workspace @affine/electron dev
```

This opens the Electron app with DevTools.

⚠️ **Note:** The project uses Electron 36.0.0, which is outdated. See [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md#electron---v3600) for upgrade info.

See: [docs/building-desktop-client-app.md](../building-desktop-client-app.md) for details.

---

### Mode 4: Mobile Development

**Use this if:** You're working on iOS or Android apps.

```bash
# iOS
yarn workspace @affine/ios dev

# Android
yarn workspace @affine/android dev
```

Requires Xcode (macOS) for iOS, Android Studio for Android.

---

## Development Modes Summary

| Mode | Command | Ports | Use Case |
|------|---------|-------|----------|
| **Frontend Only** | `yarn dev` | 8080 | UI work, no auth needed |
| **Full Stack** | `yarn dev` + `yarn affine server dev` | 8080, 3010 | Backend, auth, sync |
| **Desktop** | `yarn workspace @affine/electron dev` | N/A | Electron features |
| **Mobile** | `yarn workspace @affine/ios dev` | N/A | Mobile features |

---

## Common Issues & Troubleshooting

### Issue 1: `yarn install` fails

**Symptom:** Errors during dependency installation

**Solutions:**
```bash
# Clear Yarn cache
yarn cache clean

# Delete node_modules and reinstall
rm -rf node_modules
yarn install

# Ensure Yarn version is 4.x
yarn --version  # Should be 4.x.x

# If still on 1.x, run:
corepack enable
corepack prepare yarn@stable --activate
```

---

### Issue 2: Native module build fails

**Symptom:** `yarn affine @affine/native build` errors

**Solutions:**
```bash
# Verify Rust is installed
rustc --version  # Should show 1.85.0+

# Reinstall Rust
rustup self update
rustup update stable

# Clean and rebuild
yarn affine @affine/native clean
yarn affine @affine/native build
```

**macOS-specific:**
```bash
# Use system strip, not binutils
which strip  # Should show /usr/bin/strip
```

---

### Issue 3: Docker services won't start

**Symptom:** `docker compose up` fails

**Solutions:**
```bash
# Check Docker is running
docker ps

# Stop existing containers
docker compose -f ./.docker/dev/compose.yml down

# Remove volumes and restart
docker compose -f ./.docker/dev/compose.yml down -v
docker compose -f ./.docker/dev/compose.yml up

# Check ports aren't in use
lsof -i :5432  # PostgreSQL
lsof -i :6379  # Redis
lsof -i :1025  # Mailhog
```

---

### Issue 4: `yarn dev` shows blank page

**Symptom:** Browser shows blank page at http://localhost:8080

**Solutions:**
```bash
# Check dev server is running
# Look for: "Local: http://localhost:8080"

# Clear browser cache (Cmd+Shift+R or Ctrl+Shift+R)

# Check browser console for errors (F12)

# Restart dev server
# Ctrl+C to stop, then:
yarn dev
```

---

### Issue 5: Port already in use

**Symptom:** `Error: listen EADDRINUSE: address already in use :::8080`

**Solutions:**
```bash
# Find process using port
lsof -i :8080  # macOS/Linux
netstat -ano | findstr :8080  # Windows

# Kill the process
kill -9 <PID>  # macOS/Linux

# Or change the port (edit vite.config.ts)
```

---

### Issue 6: Database migration fails

**Symptom:** `yarn affine server init` errors

**Solutions:**
```bash
# Ensure Docker services are running
docker ps  # Should show postgres container

# Reset database
yarn affine server prisma migrate reset

# Re-run init
yarn affine server init
```

---

## Verification Checklist

Use this checklist to verify your setup is complete:

### ✅ Prerequisites
- [ ] Node.js 22.x installed (`node --version`)
- [ ] Yarn 4.x installed (`yarn --version`)
- [ ] Rust 1.85+ installed (`rustc --version`)
- [ ] Docker installed (`docker --version`)

### ✅ Repository
- [ ] Repository cloned successfully
- [ ] Dependencies installed (`yarn install` completed)
- [ ] Native modules built (frontend + backend)

### ✅ Frontend Only
- [ ] `yarn dev` runs without errors
- [ ] http://localhost:8080 loads in browser
- [ ] Can create a new workspace
- [ ] Can create a new page and edit text
- [ ] Hot reload works (edit file, see changes)

### ✅ Full Stack (if needed)
- [ ] Docker services running (`docker ps` shows 3 containers)
- [ ] Database initialized (`yarn affine server init` succeeded)
- [ ] Backend running (`yarn affine server dev`)
- [ ] Frontend connected to backend
- [ ] Can login with dev@affine.pro / dev
- [ ] GraphQL playground accessible at http://localhost:3010/graphql

---

## Next Steps

🎉 **Congratulations!** You have AFFiNE running locally.

### Recommended Learning Path

1. **Explore the UI** - Create workspaces, pages, try features
2. **Read [Architecture Overview](./ARCHITECTURE_OVERVIEW.md)** - Understand the system design
3. **Browse [Project Structure](./PROJECT_STRUCTURE.md)** - Know where files are
4. **Study [Tech Stack Guide](./TECH_STACK_GUIDE.md)** - Learn the technologies

### Choose Your Path

- **Frontend Developer?** → [Frontend Architecture](./FRONTEND_ARCHITECTURE.md)
- **Backend Developer?** → [Backend Architecture](./BACKEND_ARCHITECTURE.md)
- **Full-Stack Developer?** → [Data Flow Guide](./DATA_FLOW_GUIDE.md)
- **Editor Developer?** → [BlockSuite Editor](./BLOCKSUITE_EDITOR.md)

### Ready to Contribute?

- **Find an issue:** [Good First Issues](https://github.com/toeverything/AFFiNE/labels/good%20first%20issue)
- **Read:** [First Contributions Guide](./FIRST_CONTRIBUTIONS.md)
- **Follow:** [Development Workflow](./DEVELOPMENT_WORKFLOW.md)

---

## Useful Commands Reference

```bash
# Development
yarn dev                          # Start frontend dev server
yarn affine server dev            # Start backend server
yarn workspace <package> dev      # Start specific package

# Building
yarn build                        # Build all packages
yarn affine @affine/native build  # Build native modules

# Testing
yarn test                         # Run all tests
yarn test:unit                    # Unit tests only
yarn test:e2e                     # E2E tests
npx playwright install            # Install browser binaries

# Database
yarn affine server init           # Initialize database
yarn affine server prisma studio  # Open Prisma Studio (GUI)
yarn affine server seed           # Seed database

# Linting & Formatting
yarn lint                         # Check all
yarn lint:fix                     # Auto-fix issues
yarn typecheck                    # TypeScript check
```

---

## Additional Resources

### Official Documentation
- **AFFiNE Docs:** https://docs.affine.pro
- **Building Guide:** [docs/BUILDING.md](../BUILDING.md)
- **Server Setup:** [docs/developing-server.md](../developing-server.md)
- **Desktop App:** [docs/building-desktop-client-app.md](../building-desktop-client-app.md)

### Community
- **Discord:** https://affine.pro/redirect/discord
- **GitHub Issues:** https://github.com/toeverything/AFFiNE/issues
- **GitHub Discussions:** https://github.com/toeverything/AFFiNE/discussions

### Tech Documentation
- **React 19:** https://react.dev/
- **NestJS:** https://docs.nestjs.com/
- **Prisma:** https://www.prisma.io/docs
- **Vite:** https://vite.dev/

See [TECH_STACK_RESEARCH.md](./TECH_STACK_RESEARCH.md) for all technology links.

---

## Troubleshooting Help

**Still stuck?**

1. **Check [Debugging Guide](./DEBUGGING_GUIDE.md)** (when available)
2. **Search [GitHub Issues](https://github.com/toeverything/AFFiNE/issues)**
3. **Ask on [Discord](https://affine.pro/redirect/discord)**
4. **Open a new issue** with:
   - Your OS and versions
   - Error messages
   - Steps to reproduce

---

**🎉 Happy coding! Welcome to the AFFiNE community!**

---

*Last updated: November 18, 2025*
*See [README.md](./README.md) for the complete learning path.*
