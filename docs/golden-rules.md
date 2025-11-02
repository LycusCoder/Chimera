# 🌟 Golden Rules - Laragon Cross-Platform Migration Project

## 📋 Project Overview

**Project Name:** Laragon Cross-Platform Edition

**Original:** Laragon.exe (VS2016 - Windows Only)

**Target:** Electron + React TypeScript (Cross-Platform: Linux, Windows, macOS)

**Primary Goal:** Migrate Laragon from Windows-only .exe application to a modern, cross-platform desktop application that works seamlessly on Linux, Windows, and macOS.

---

## 🎯 Core Concept

Laragon adalah **portable/isolated local development environment** - konsep seperti venv/miniconda tapi untuk web development. Semua services (Apache, Nginx, MySQL, PostgreSQL, MongoDB, dll) bundled dalam folder project dan tidak mengganggu system installation.

**Key Differentiator vs XAMPP/WAMP:**
- ✅ Portable - tidak install ke system
- ✅ Isolated - tidak conflict dengan existing installations
- ✅ Multiple environments - bisa punya multiple Laragon instances
- ✅ Auto virtual host - www/myproject → myproject.test otomatis
- ✅ Modern stack support - Node.js, Python, Ruby, Go, dll

---

## 🚀 Why Electron + React TypeScript?

### ✅ Chosen: Electron + React TypeScript

**Advantages:**
1. **True Cross-Platform** - Single codebase untuk Linux, Windows, macOS
2. **Modern UI/UX** - React untuk beautiful, responsive interfaces
3. **Type Safety** - TypeScript reduces bugs, improves maintainability
4. **Rich Ecosystem** - npm packages untuk semua kebutuhan
5. **Desktop APIs** - Full access ke filesystem, process management, system tray
6. **Proven Technology** - VS Code, Slack, Discord, Figma menggunakan Electron
7. **Active Community** - Large community, extensive documentation
8. **Easy Updates** - Auto-update mechanisms built-in

### ❌ Rejected: PyGUI

**Why Not:**
1. Limited documentation dan community support
2. UI capabilities kurang modern
3. Sulit untuk advanced desktop features
4. Smaller ecosystem
5. Kurang mature untuk production applications

---

## 🎯 Core Features to Migrate

### Priority 1: Critical Features

1. **Auto Virtual Host** ⭐ (MOST IMPORTANT)
   - Auto-detect folders in `www/`
   - Generate virtual host configs automatically
   - Update hosts file: `www/myproject` → `myproject.test`
   - Auto-reload on folder changes

2. **Service Management**
   - Start/Stop Apache
   - Start/Stop Nginx  
   - Start/Stop MySQL
   - Start/Stop PostgreSQL
   - Start/Stop MongoDB
   - Start/Stop Redis
   - Start/Stop Memcached
   - Process monitoring & health checks

3. **System Tray Integration**
   - Status indicator (running/stopped)
   - Quick actions menu
   - Start/Stop all services
   - Open dashboard

4. **Landing Page**
   - 127.0.0.1 → Laragon dashboard
   - Show all virtual hosts
   - Quick links to projects
   - Service status overview

5. **Terminal Integration**
   - Built-in terminal
   - Auto-cd to project directory
   - Environment variables support

### Priority 2: Important Features

6. **Configuration Management**
   - Manage Apache configs
   - Manage Nginx configs
   - Manage PHP versions
   - Manage database configs

7. **SSL Certificate Management**
   - Auto-generate self-signed SSL certs
   - HTTPS support for virtual hosts

8. **Multi-Language Support**
   - 30+ languages support
   - Easy language switching

### Priority 3: Nice-to-Have Features

9. **Quick Create Menus**
   - Quick create Laravel project
   - Quick create WordPress site
   - Quick create Node.js app

10. **Database Management**
    - Quick access to phpMyAdmin
    - Quick access to Adminer

---

## 🏗️ Technical Architecture

### Tech Stack

**Frontend:**
- React 18+
- TypeScript 5+
- Tailwind CSS 3+ (untuk modern UI)
- React Router (untuk navigation)
- Zustand or Redux Toolkit (untuk state management)

**Desktop Framework:**
- Electron 28+
- electron-builder (untuk packaging)

**Backend/Services:**
- Node.js (untuk process management)
- child_process (untuk managing Apache, Nginx, etc.)
- chokidar (untuk file watching)
- node-hosts (untuk hosts file management)

**Development:**
- Vite (untuk fast development)
- ESLint + Prettier (untuk code quality)
- Jest + React Testing Library (untuk testing)

### Application Structure

```
laragon-cross-platform/
├── electron/                 # Electron main process
│   ├── main.ts              # Main entry point
│   ├── tray.ts              # System tray management
│   ├── services/            # Service management
│   │   ├── apache.ts
│   │   ├── nginx.ts
│   │   ├── mysql.ts
│   │   └── ...
│   ├── vhost/               # Virtual host management
│   │   ├── generator.ts
│   │   ├── watcher.ts
│   │   └── hosts.ts
│   └── utils/
│
├── src/                     # React frontend
│   ├── components/
│   ├── pages/
│   ├── hooks/
│   ├── store/
│   └── App.tsx
│
├── bin/                     # Bundled services (Apache, Nginx, etc.)
├── etc/                     # Configuration templates
├── www/                     # Web projects directory
└── docs/                    # Documentation
```

---

## 📁 Documentation Structure

### 1. `docs/golden-rules.md` (This File)
Core document yang menjelaskan:
- Project overview dan goals
- Architectural decisions
- Feature priorities
- Technical stack choices

### 2. `docs/phase/`
Dokumentasi untuk phases yang **SUDAH SELESAI**:
- `phase_0_planning.md` - Documentation & planning setup
- `phase_1_setup.md` - Electron + React TypeScript setup
- `phase_2_services.md` - Service management implementation
- etc...

### 3. `docs/progress/`
Dokumentasi untuk phase yang **SEDANG DIKERJAKAN**:
- `phase_X_current.md` - Work in progress
- Dipindah ke `docs/phase/` setelah selesai

### 4. `docs/workflow/`
Workflow dan design decisions:
- `architecture.md` - System architecture
- `ui-design.md` - UI/UX design guidelines
- `service-management.md` - How services are managed
- `vhost-workflow.md` - Virtual host creation workflow

### 5. `docs/roadmap/`
Roadmap untuk setiap phase:
- `phase_0_roadmap.md` - Planning phase roadmap
- `phase_1_roadmap.md` - Setup phase roadmap
- etc...

---

## 🎯 Development Principles

### 1. Linux First
- **Primary target:** Linux (current development environment)
- **Secondary:** Windows compatibility
- **Tertiary:** macOS support

### 2. Portable & Isolated
- All services bundled in application folder
- No system installation required
- Multiple instances support

### 3. Modern & Maintainable
- TypeScript for type safety
- Component-based architecture
- Comprehensive documentation
- Unit tests for critical features

### 4. User Experience First
- Intuitive UI/UX
- Fast performance
- Clear error messages
- Helpful documentation

### 5. Incremental Development
- Work in phases
- Each phase is independently testable
- Document as we go

---

## 🚦 Phase Breakdown

### Phase 0: Planning & Documentation ✅ (Complete)
- Setup documentation structure
- Define architecture
- Create detailed roadmaps
- Technology decisions

### Phase 1: Core Setup 📅 (Next)
- Electron + React + TypeScript boilerplate
- System tray implementation
- Basic UI/dashboard
- Settings management

### Phase 2: Service Management
- Start/Stop Apache
- Start/Stop Nginx
- Start/Stop MySQL/PostgreSQL/MongoDB
- Process monitoring
- Terminal integration

### Phase 3: Auto Virtual Host ⭐
- File watcher for www/ folder
- Virtual host config generation
- Hosts file management
- Landing page implementation

### Phase 4: Configuration Management
- Apache/Nginx config editor
- PHP version switching
- Database configuration
- SSL certificate generation

### Phase 5: Polish & Features
- Multi-language support
- Quick create menus
- Database tools integration
- Auto-update mechanism

### Phase 6: Testing & Distribution
- Comprehensive testing
- Package for Linux (AppImage, .deb, .rpm)
- Package for Windows (.exe, portable)
- Package for macOS (.dmg)

---

## 🔐 Security Considerations

1. **Sudo/Root Access:** Required for hosts file modification
2. **Service Ports:** Ensure no conflicts with system services
3. **SSL Certificates:** Proper self-signed cert generation
4. **File Permissions:** Proper permissions for config files

---

## 📝 Notes

- **Current Laragon Version:** Windows-only .exe built with VS2016
- **Migration Start Date:** 2025
- **Primary Developer Environment:** Linux
- **Target Release:** Cross-platform desktop app

---

## 🎯 Success Criteria

1. ✅ Works on Linux without any system installation
2. ✅ Auto virtual host working perfectly
3. ✅ All core services can be started/stopped
4. ✅ Terminal integration functional
5. ✅ Modern, intuitive UI
6. ✅ Portable - can be copied to USB and used on any machine
7. ✅ Complete documentation

---

**Last Updated:** Phase 0 Complete ✅ - Ready for Phase 1 📅

**Status:** 🟢 Active Development - Phase 1 Ready to Start