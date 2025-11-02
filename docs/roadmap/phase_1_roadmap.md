# 🗺️ Phase 1 Roadmap - Core Setup (Electron + React + TypeScript)

## 📋 Phase Overview

**Phase:** 1 - Core Setup

**Status:** 📅 Planned

**Duration:** 2-3 days

**Goal:** Setup Electron + React + TypeScript boilerplate dengan system tray dan basic dashboard.

---

## 🎯 Objectives

### 1. Project Initialization
- [ ] Initialize npm/yarn project
- [ ] Setup TypeScript configuration
- [ ] Setup Vite for React
- [ ] Setup Electron with Vite
- [ ] Configure electron-builder

### 2. Electron Main Process Setup
- [ ] Create main process entry point (electron/main.ts)
- [ ] Setup IPC communication
- [ ] Create preload script (electron/preload.ts)
- [ ] Implement window management
- [ ] Setup auto-launch configuration

### 3. System Tray Implementation
- [ ] Create system tray (electron/tray.ts)
- [ ] Add tray icon (running/stopped states)
- [ ] Implement context menu
- [ ] Add quick actions:
  - Start/Stop All Services
  - Show/Hide Window
  - Open Dashboard
  - Exit Application

### 4. React Frontend Setup
- [ ] Create React app structure
- [ ] Setup Tailwind CSS
- [ ] Setup React Router
- [ ] Create basic layout components
- [ ] Setup Zustand state management

### 5. Basic Dashboard UI
- [ ] Create Dashboard page
- [ ] Add header with app info
- [ ] Add navigation sidebar
- [ ] Create service status cards
- [ ] Add quick action buttons

### 6. Settings Management
- [ ] Create settings store (Zustand)
- [ ] Create settings page UI
- [ ] Implement theme switching (light/dark)
- [ ] Add language selector (placeholder)
- [ ] Add auto-start toggle

---

## 📝 Tasks Breakdown

### Task 1: Project Initialization

**Status:** 📅 Planned

**Steps:**
1. Create package.json with required dependencies
2. Setup TypeScript config (tsconfig.json)
3. Configure Vite for React (vite.config.ts)
4. Configure Vite for Electron (vite.electron.config.ts)
5. Setup electron-builder (electron-builder.json)
6. Create .gitignore

**Key Dependencies:**
```json
{
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.20.0",
    "zustand": "^4.4.7",
    "electron-store": "^8.1.0"
  },
  "devDependencies": {
    "electron": "^28.0.0",
    "vite": "^5.0.0",
    "typescript": "^5.3.0",
    "@vitejs/plugin-react": "^4.2.0",
    "vite-plugin-electron": "^0.28.0",
    "electron-builder": "^24.9.0",
    "tailwindcss": "^3.4.0"
  }
}
```

**Deliverables:**
- Working package.json
- TypeScript configuration
- Vite build configuration
- Development environment ready

---

### Task 2: Electron Main Process

**Status:** 📅 Planned

**Files to Create:**
```
electron/
├── main.ts              # Main entry point
├── preload.ts           # Preload script
└── types.ts             # TypeScript types
```

**Main Process Features:**
```typescript
// electron/main.ts
- createWindow(): Create browser window
- setupIPC(): Setup IPC handlers
- onReady(): Application ready handler
- onActivate(): macOS activate handler
- onWindowAllClosed(): Close handler
```

**IPC Channels (Initial):**
```typescript
// Basic IPC for Phase 1
'app:get-version'
'app:get-path'
'settings:get'
'settings:set'
'window:minimize'
'window:maximize'
'window:close'
```

**Deliverables:**
- Working Electron window
- IPC communication functional
- Preload script security setup

---

### Task 3: System Tray

**Status:** 📅 Planned

**File:** `electron/tray.ts`

**Features:**
1. **Tray Icon States:**
   - Green: All services running
   - Yellow: Some services running
   - Red: All services stopped

2. **Context Menu:**
   ```
   ┌─────────────────────────┐
   │ Laragon - Running       │
   ├─────────────────────────┤
   │ Start All Services      │
   │ Stop All Services       │
   ├─────────────────────────┤
   │ Show Dashboard          │
   │ Projects ▶              │
   ├─────────────────────────┤
   │ Settings                │
   │ About                   │
   ├─────────────────────────┤
   │ Exit                    │
   └─────────────────────────┘
   ```

3. **Click Actions:**
   - Left Click: Show/Hide window
   - Right Click: Show context menu

**Deliverables:**
- System tray icon working
- Context menu functional
- Tray status updates

---

### Task 4: React Frontend Setup

**Status:** 📅 Planned

**Structure:**
```
src/
├── components/
│   ├── Layout/
│   │   ├── Header.tsx
│   │   ├── Sidebar.tsx
│   │   └── Footer.tsx
│   └── common/
│       ├── Button.tsx
│       ├── Card.tsx
│       └── StatusBadge.tsx
│
├── pages/
│   ├── Dashboard.tsx
│   ├── Services.tsx
│   ├── Projects.tsx
│   └── Settings.tsx
│
├── store/
│   ├── settingsStore.ts
│   └── appStore.ts
│
├── hooks/
│   ├── useIPC.ts
│   └── useSettings.ts
│
├── types/
│   └── index.ts
│
├── App.tsx
├── main.tsx
└── index.css
```

**Deliverables:**
- React app running in Electron
- Tailwind CSS working
- Routing functional
- Basic components created

---

### Task 5: Basic Dashboard UI

**Status:** 📅 Planned

**Dashboard Features:**
1. **Header:**
   - App logo and name
   - Status indicator (running/stopped)
   - Window controls (minimize, maximize, close)

2. **Sidebar Navigation:**
   - Dashboard (active)
   - Services
   - Projects
   - Terminal (placeholder)
   - Settings

3. **Main Content:**
   - Welcome message
   - Service status cards (Apache, Nginx, MySQL, etc.)
   - Quick action buttons
   - System info (placeholder)

4. **Status Cards:**
   ```
   ┌───────────────────────┐
   │  Apache               │
   │  Status: Stopped      │
   │  Port: 80             │
   │  [Start]              │
   └───────────────────────┘
   ```

**Deliverables:**
- Beautiful, modern dashboard
- Responsive layout
- Status cards for all services
- Navigation working

---

### Task 6: Settings Management

**Status:** 📅 Planned

**Settings Page Features:**
1. **General Settings:**
   - Theme: Light/Dark
   - Language: English (placeholder for 30+ languages)
   - Auto-start on boot
   - Start minimized to tray

2. **Service Settings (Placeholder):**
   - Default web server: Apache/Nginx
   - Default ports

3. **Storage:**
   - Use electron-store for persistence
   - Settings saved to user data directory

**Settings Store (Zustand):**
```typescript
interface SettingsStore {
  theme: 'light' | 'dark';
  language: string;
  autoStart: boolean;
  startMinimized: boolean;
  defaultServer: 'apache' | 'nginx';
  updateSettings: (settings: Partial<Settings>) => void;
  loadSettings: () => Promise<void>;
  saveSettings: () => Promise<void>;
}
```

**Deliverables:**
- Settings page UI
- Theme switching working
- Settings persisted
- Auto-start functional

---

## 📊 Success Metrics

### Phase 1 Complete When:
- [✅] Electron app launches successfully
- [✅] System tray icon appears and works
- [✅] Dashboard UI is beautiful and responsive
- [✅] Navigation between pages works
- [✅] Settings can be changed and persisted
- [✅] Theme switching works (light/dark)
- [✅] Window can be minimized to tray
- [✅] IPC communication is functional
- [✅] No console errors
- [✅] TypeScript compiles without errors

---

## 🚀 Next Steps (After Phase 1)

### Phase 2: Service Management
1. Implement actual service start/stop
2. Process monitoring
3. Terminal integration
4. Service logs viewer

**Expected Duration:** 3-4 days

---

## 📌 Important Notes

### Development Setup

**Scripts in package.json:**
```json
{
  "scripts": {
    "dev": "vite",
    "dev:electron": "electron-vite dev",
    "build": "vite build && electron-builder",
    "preview": "vite preview",
    "type-check": "tsc --noEmit"
  }
}
```

### Design System

**Colors (Tailwind):**
- Primary: Blue (for actions)
- Success: Green (service running)
- Warning: Yellow (some issues)
- Danger: Red (service stopped/error)
- Neutral: Gray (backgrounds, text)

**Typography:**
- Font: Inter or System UI
- Sizes: xs, sm, base, lg, xl, 2xl, 3xl

### Testing Strategy

**Phase 1 Testing:**
- Manual testing of UI
- Test all navigation routes
- Test settings persistence
- Test theme switching
- Test tray menu actions

**Automated Testing (Later):**
- Unit tests for stores
- Integration tests for IPC
- E2E tests with Playwright

---

## 🐛 Potential Issues

### Issue 1: Electron Window Not Showing

**Problem:** Window created but not visible

**Solutions:**
- Check window.show() is called
- Verify bounds are within screen
- Check if window is behind other windows

### Issue 2: IPC Not Working

**Problem:** Renderer can't communicate with main

**Solutions:**
- Verify preload script is loaded
- Check contextIsolation settings
- Ensure contextBridge is setup correctly

### Issue 3: Vite Hot Reload Issues

**Problem:** Changes not reflecting

**Solutions:**
- Restart dev server
- Clear Vite cache
- Check file watcher limits on Linux

---

## 🎯 Phase 1 Checklist

### Setup
- [ ] Initialize project with package.json
- [ ] Install all dependencies
- [ ] Configure TypeScript
- [ ] Configure Vite
- [ ] Configure Electron Builder

### Electron
- [ ] Create main process
- [ ] Create preload script
- [ ] Setup IPC handlers
- [ ] Implement window management
- [ ] Create system tray

### React
- [ ] Setup React with Vite
- [ ] Configure Tailwind CSS
- [ ] Setup React Router
- [ ] Create layout components
- [ ] Setup Zustand stores

### UI
- [ ] Create Dashboard page
- [ ] Create Services page skeleton
- [ ] Create Projects page skeleton
- [ ] Create Settings page
- [ ] Implement theme switching

### Testing
- [ ] Test app launch
- [ ] Test system tray
- [ ] Test navigation
- [ ] Test settings persistence
- [ ] Test theme switching

---

**Phase 1 Status:** 📅 Ready to Start

**Expected Completion:** 2-3 days after Phase 0

**Blocker:** Phase 0 must be completed first
