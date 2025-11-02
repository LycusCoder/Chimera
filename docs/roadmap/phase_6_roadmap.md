# 🗺️ Phase 6 Roadmap - Testing & Distribution

## 📋 Phase Overview

**Phase:** 6 - Testing & Distribution (Final Phase)

**Status:** 📅 Planned

**Duration:** 3-4 days

**Goal:** Comprehensive testing, bug fixes, packaging for all platforms, and setting up distribution channels.

---

## 🎯 Objectives

### 1. Comprehensive Testing
- [ ] Unit tests for critical functions
- [ ] Integration tests for IPC
- [ ] E2E tests with Playwright
- [ ] Manual testing checklist
- [ ] Performance testing
- [ ] Security audit

### 2. Bug Fixes
- [ ] Fix all critical bugs
- [ ] Fix all high-priority bugs
- [ ] Address medium-priority bugs
- [ ] Polish UI issues
- [ ] Optimize performance

### 3. Linux Packaging
- [ ] AppImage (portable)
- [ ] .deb (Debian/Ubuntu)
- [ ] .rpm (Fedora/RedHat)
- [ ] Snap package
- [ ] Flatpak

### 4. Windows Packaging
- [ ] .exe installer
- [ ] Portable .zip
- [ ] MSI installer
- [ ] Windows Store submission (optional)

### 5. macOS Packaging
- [ ] .dmg installer
- [ ] .app bundle
- [ ] Code signing
- [ ] Notarization
- [ ] Mac App Store submission (optional)

### 6. Distribution
- [ ] Setup GitHub Releases
- [ ] Create download page
- [ ] Setup auto-update server
- [ ] Write release notes
- [ ] Create marketing materials

---

## 📝 Tasks Breakdown

### Task 1: Unit Testing

**Status:** 📅 Planned

**Setup:**
```bash
yarn add -D jest @types/jest ts-jest
yarn add -D @testing-library/react @testing-library/jest-dom
```

**Test Examples:**
```typescript
// services/ApacheService.test.ts
import { ApacheService } from './ApacheService';

describe('ApacheService', () => {
  let service: ApacheService;
  
  beforeEach(() => {
    service = new ApacheService();
  });
  
  test('should start Apache successfully', async () => {
    await service.start();
    expect(service.isRunning()).toBe(true);
  });
  
  test('should stop Apache successfully', async () => {
    await service.start();
    await service.stop();
    expect(service.isRunning()).toBe(false);
  });
  
  test('should detect port conflicts', async () => {
    // Mock port 80 already in use
    await expect(service.start()).rejects.toThrow('Port 80 is already in use');
  });
});
```

**Areas to Test:**
- Service management (start/stop/restart)
- Config generation
- Hosts file management
- VHost creation
- File watcher
- Settings persistence

**Deliverables:**
- 80%+ code coverage for critical functions
- All tests passing

---

### Task 2: Integration Testing

**Status:** 📅 Planned

**IPC Testing:**
```typescript
// ipc/ipc.test.ts
import { ipcMain, ipcRenderer } from 'electron';

describe('IPC Communication', () => {
  test('should start service via IPC', async () => {
    const response = await ipcRenderer.invoke('service:start:apache');
    expect(response.success).toBe(true);
  });
  
  test('should get service status via IPC', async () => {
    const status = await ipcRenderer.invoke('service:status:apache');
    expect(status).toHaveProperty('running');
    expect(status).toHaveProperty('pid');
  });
});
```

**Deliverables:**
- All IPC channels tested
- Main-renderer communication verified

---

### Task 3: E2E Testing

**Status:** 📅 Planned

**Setup Playwright:**
```bash
yarn add -D playwright @playwright/test
```

**E2E Test Examples:**
```typescript
// e2e/app.test.ts
import { test, expect } from '@playwright/test';
import { _electron as electron } from 'playwright';

test('should launch app', async () => {
  const app = await electron.launch({ args: ['.'] });
  const window = await app.firstWindow();
  
  // App should show dashboard
  await expect(window.locator('h1')).toContainText('Dashboard');
  
  await app.close();
});

test('should start Apache service', async () => {
  const app = await electron.launch({ args: ['.'] });
  const window = await app.firstWindow();
  
  // Navigate to Services page
  await window.click('a[href="/services"]');
  
  // Click Start Apache button
  await window.click('[data-testid="start-apache"]');
  
  // Wait for status to change
  await expect(window.locator('[data-testid="apache-status"]'))
    .toContainText('Running');
  
  await app.close();
});

test('should create virtual host', async () => {
  const app = await electron.launch({ args: ['.'] });
  const window = await app.firstWindow();
  
  // Create project folder
  await window.click('[data-testid="create-project"]');
  await window.fill('[data-testid="project-name"]', 'testproject');
  await window.click('[data-testid="create-button"]');
  
  // Wait for vhost creation
  await expect(window.locator('[data-testid="notification"]'))
    .toContainText('testproject.test is ready');
  
  // Verify in projects list
  await window.click('a[href="/projects"]');
  await expect(window.locator('[data-testid="project-list"]'))
    .toContainText('testproject');
  
  await app.close();
});
```

**Test Scenarios:**
1. App launches successfully
2. System tray appears
3. Can navigate between pages
4. Can start/stop services
5. Can create projects
6. Virtual host creation works
7. Settings can be changed
8. Theme switching works
9. Terminal works
10. Can view logs

**Deliverables:**
- All critical user flows tested
- E2E tests passing on all platforms

---

### Task 4: Manual Testing Checklist

**Status:** 📅 Planned

**Checklist:**
```markdown
## Installation
- [ ] App installs without errors
- [ ] First launch shows welcome screen
- [ ] Settings are initialized
- [ ] System tray icon appears

## Services
- [ ] Can start Apache
- [ ] Can stop Apache
- [ ] Can restart Apache
- [ ] Can start Nginx
- [ ] Can start MySQL
- [ ] Can start PostgreSQL
- [ ] Can start MongoDB
- [ ] Can start Redis
- [ ] Can start Memcached
- [ ] Multiple services can run simultaneously
- [ ] Service status is accurate
- [ ] Logs are viewable

## Virtual Hosts
- [ ] Creating folder in www/ creates vhost
- [ ] Domain is accessible (projectname.test)
- [ ] Hosts file is updated
- [ ] Can delete vhost
- [ ] SSL works for projects

## Terminal
- [ ] Terminal opens
- [ ] Can execute commands
- [ ] Environment variables are set
- [ ] Can cd to project directories

## Configuration
- [ ] Can edit Apache config
- [ ] Can edit Nginx config
- [ ] Can switch PHP versions
- [ ] Can toggle PHP extensions
- [ ] Can edit php.ini
- [ ] Can manage databases
- [ ] Can generate SSL certificates

## UI/UX
- [ ] All pages load correctly
- [ ] Navigation works
- [ ] Animations are smooth
- [ ] No console errors
- [ ] Theme switching works
- [ ] Language switching works
- [ ] Keyboard shortcuts work
- [ ] Tooltips appear

## System Tray
- [ ] Tray icon appears
- [ ] Context menu works
- [ ] Can start/stop all services from tray
- [ ] Can show/hide window
- [ ] Status indicator updates

## Performance
- [ ] App starts quickly (<3 seconds)
- [ ] UI is responsive
- [ ] No memory leaks
- [ ] Services start quickly
- [ ] File watcher doesn't cause lag

## Cross-Platform
- [ ] Works on Linux (Ubuntu, Fedora)
- [ ] Works on Windows 10/11
- [ ] Works on macOS (Intel & Apple Silicon)
```

**Deliverables:**
- All checklist items verified
- Issues documented and fixed

---

### Task 5: Linux Packaging

**Status:** 📅 Planned

**electron-builder Configuration:**
```json
// electron-builder.json
{
  "appId": "org.laragon.app",
  "productName": "Laragon",
  "directories": {
    "output": "dist"
  },
  "linux": {
    "target": ["AppImage", "deb", "rpm", "snap"],
    "category": "Development",
    "icon": "build/icon.icns",
    "synopsis": "The Dev Environment for Entrepreneurs",
    "description": "Portable, isolated, fast & powerful development environment"
  },
  "appImage": {
    "license": "MIT"
  },
  "deb": {
    "depends": [],
    "priority": "optional"
  },
  "rpm": {
    "depends": []
  },
  "snap": {
    "confinement": "classic",
    "grade": "stable"
  }
}
```

**Build Commands:**
```bash
# AppImage (portable - works on all distros)
yarn build:linux:appimage

# .deb (Debian, Ubuntu, Mint)
yarn build:linux:deb

# .rpm (Fedora, RedHat, CentOS)
yarn build:linux:rpm

# Snap (Ubuntu)
yarn build:linux:snap

# All formats
yarn build:linux
```

**Deliverables:**
- AppImage builds and runs
- .deb installs on Ubuntu
- .rpm installs on Fedora
- Snap package working

---

### Task 6: Windows Packaging

**Status:** 📅 Planned

**electron-builder Configuration:**
```json
{
  "win": {
    "target": [
      {
        "target": "nsis",
        "arch": ["x64", "ia32"]
      },
      {
        "target": "portable",
        "arch": ["x64"]
      },
      {
        "target": "msi",
        "arch": ["x64"]
      }
    ],
    "icon": "build/icon.ico",
    "certificateFile": "certs/cert.pfx",
    "certificatePassword": "${CERT_PASSWORD}"
  },
  "nsis": {
    "oneClick": false,
    "allowToChangeInstallationDirectory": true,
    "createDesktopShortcut": true,
    "createStartMenuShortcut": true,
    "shortcutName": "Laragon"
  }
}
```

**Build Commands:**
```bash
# NSIS Installer (.exe)
yarn build:win:nsis

# Portable (.exe - no installation required)
yarn build:win:portable

# MSI Installer
yarn build:win:msi

# All formats
yarn build:win
```

**Code Signing (Important for Windows):**
```bash
# Get code signing certificate from CA
# Sign the .exe to avoid "Unknown Publisher" warning
```

**Deliverables:**
- .exe installer works
- Portable version works
- MSI installer works
- Signed binaries (optional but recommended)

---

### Task 7: macOS Packaging

**Status:** 📅 Planned

**electron-builder Configuration:**
```json
{
  "mac": {
    "target": ["dmg", "zip"],
    "category": "public.app-category.developer-tools",
    "icon": "build/icon.icns",
    "hardenedRuntime": true,
    "gatekeeperAssess": false,
    "entitlements": "build/entitlements.mac.plist",
    "entitlementsInherit": "build/entitlements.mac.plist"
  },
  "dmg": {
    "contents": [
      {
        "x": 130,
        "y": 220
      },
      {
        "x": 410,
        "y": 220,
        "type": "link",
        "path": "/Applications"
      }
    ]
  }
}
```

**Entitlements (entitlements.mac.plist):**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>com.apple.security.cs.allow-unsigned-executable-memory</key>
  <true/>
  <key>com.apple.security.cs.allow-jit</key>
  <true/>
</dict>
</plist>
```

**Code Signing & Notarization:**
```bash
# Requires Apple Developer account ($99/year)

# Sign the app
codesign --force --deep --sign "Developer ID Application: Your Name" Laragon.app

# Notarize with Apple
xcrun notarytool submit Laragon.dmg --apple-id "your@email.com" --password "app-specific-password"

# Staple the notarization
xcrun stapler staple Laragon.dmg
```

**Build Commands:**
```bash
# DMG (drag to install)
yarn build:mac:dmg

# ZIP (extract and run)
yarn build:mac:zip

# Universal (Intel + Apple Silicon)
yarn build:mac:universal

# All formats
yarn build:mac
```

**Deliverables:**
- .dmg builds successfully
- Works on Intel Macs
- Works on Apple Silicon Macs
- Code signed (if possible)
- Notarized (if possible)

---

### Task 8: Auto-Update Setup

**Status:** 📅 Planned

**electron-updater:**
```bash
yarn add electron-updater
```

**Implementation:**
```typescript
// main.ts
import { autoUpdater } from 'electron-updater';

autoUpdater.checkForUpdatesAndNotify();

autoUpdater.on('update-available', () => {
  // Notify user that update is available
  dialog.showMessageBox({
    type: 'info',
    title: 'Update Available',
    message: 'A new version of Laragon is available. Download now?',
    buttons: ['Download', 'Later']
  }).then(result => {
    if (result.response === 0) {
      autoUpdater.downloadUpdate();
    }
  });
});

autoUpdater.on('update-downloaded', () => {
  // Prompt user to restart and install
  dialog.showMessageBox({
    type: 'info',
    title: 'Update Ready',
    message: 'Update downloaded. Restart to install?',
    buttons: ['Restart', 'Later']
  }).then(result => {
    if (result.response === 0) {
      autoUpdater.quitAndInstall();
    }
  });
});
```

**Update Server:**
- Host `latest.yml` (Linux)
- Host `latest-mac.yml` (macOS)
- Host `latest.yml` (Windows)
- Upload release files to GitHub Releases

**Deliverables:**
- Auto-update working
- Update notifications shown
- Update downloads and installs

---

### Task 9: Distribution

**Status:** 📅 Planned

**GitHub Releases:**
1. Create release tag (e.g., v1.0.0)
2. Write release notes
3. Upload all platform binaries
4. Mark as latest release

**Download Page:**
```html
<!-- https://laragon.org/download -->
<div class="downloads">
  <h1>Download Laragon Cross-Platform</h1>
  
  <section class="linux">
    <h2>Linux</h2>
    <a href="/releases/laragon-1.0.0.AppImage">AppImage (Universal)</a>
    <a href="/releases/laragon_1.0.0_amd64.deb">.deb (Ubuntu/Debian)</a>
    <a href="/releases/laragon-1.0.0.x86_64.rpm">.rpm (Fedora/RedHat)</a>
  </section>
  
  <section class="windows">
    <h2>Windows</h2>
    <a href="/releases/laragon-setup-1.0.0.exe">Installer (.exe)</a>
    <a href="/releases/laragon-portable-1.0.0.exe">Portable (.exe)</a>
  </section>
  
  <section class="macos">
    <h2>macOS</h2>
    <a href="/releases/laragon-1.0.0.dmg">Universal (.dmg)</a>
  </section>
</div>
```

**Release Notes Template:**
```markdown
# Laragon Cross-Platform v1.0.0

## 🎉 First Stable Release!

We're excited to announce the first stable release of Laragon Cross-Platform!

## ✨ Features

- ✅ Auto Virtual Host - Create `www/myproject/` → accessible at `myproject.test`
- ✅ Service Management - Start/stop Apache, Nginx, MySQL, PostgreSQL, MongoDB, Redis
- ✅ Terminal Integration - Built-in terminal with environment variables
- ✅ SSL Certificates - One-click HTTPS setup
- ✅ Configuration Management - Edit configs, switch PHP versions
- ✅ Multi-Language - 30+ languages supported
- ✅ Quick Create - Laravel, WordPress, React, Vue projects
- ✅ Cross-Platform - Works on Linux, Windows, macOS

## 💾 Downloads

See assets below for downloads.

## 🐛 Bug Fixes

None (first release)

## 📝 Documentation

Full documentation: https://laragon.org/docs

## 🚀 What's Next?

See our [roadmap](https://github.com/leokhoa/laragon/wiki/Roadmap) for upcoming features.
```

**Deliverables:**
- GitHub release created
- All binaries uploaded
- Download page live
- Release notes published

---

## 📊 Success Metrics

### Phase 6 Complete When:
- [✅] All tests passing (unit, integration, E2E)
- [✅] Manual testing checklist 100% complete
- [✅] All critical bugs fixed
- [✅] Linux packages build and run
- [✅] Windows packages build and run
- [✅] macOS packages build and run
- [✅] Auto-update working
- [✅] GitHub release published
- [✅] Download page live
- [✅] Documentation complete

---

## 🎉 Project Complete!

### 🎯 All Phases Done:
- ✅ Phase 0: Planning & Documentation
- ✅ Phase 1: Core Setup
- ✅ Phase 2: Service Management
- ✅ Phase 3: Auto Virtual Host
- ✅ Phase 4: Configuration Management
- ✅ Phase 5: Polish & Features
- ✅ Phase 6: Testing & Distribution

### 🚀 Launch Checklist:
- [ ] Press release
- [ ] Social media announcement
- [ ] Reddit post
- [ ] Hacker News post
- [ ] Product Hunt launch
- [ ] Blog post
- [ ] Email newsletter

---

## 🎯 Phase 6 Checklist

### Testing
- [ ] Write unit tests
- [ ] Write integration tests
- [ ] Write E2E tests
- [ ] Manual testing
- [ ] Performance testing
- [ ] Security audit

### Bug Fixes
- [ ] Fix critical bugs
- [ ] Fix high-priority bugs
- [ ] Polish UI
- [ ] Optimize performance

### Linux
- [ ] Build AppImage
- [ ] Build .deb
- [ ] Build .rpm
- [ ] Build Snap
- [ ] Test on Ubuntu
- [ ] Test on Fedora

### Windows
- [ ] Build .exe installer
- [ ] Build portable .exe
- [ ] Build MSI
- [ ] Code signing
- [ ] Test on Windows 10
- [ ] Test on Windows 11

### macOS
- [ ] Build .dmg
- [ ] Build universal binary
- [ ] Code signing
- [ ] Notarization
- [ ] Test on Intel Mac
- [ ] Test on Apple Silicon Mac

### Distribution
- [ ] Setup auto-update
- [ ] Create GitHub release
- [ ] Upload all binaries
- [ ] Write release notes
- [ ] Create download page
- [ ] Update documentation
- [ ] Announce release

---

**Phase 6 Status:** 📅 Waiting for Phase 5

**Expected Completion:** 3-4 days after Phase 5

**Then:** 🎉 **PROJECT COMPLETE!** 🎉
