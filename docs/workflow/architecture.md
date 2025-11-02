# 🏛️ System Architecture - Laragon Cross-Platform

## 📋 Overview

Laragon Cross-Platform menggunakan **Electron** sebagai framework utama dengan **React + TypeScript** untuk UI dan **Node.js** untuk service management.

---

## 🏗️ High-Level Architecture

```
┌──────────────────────────────────────────────────┐
│                                                  │
│              Laragon Cross-Platform              │
│                                                  │
└──────────────────────────────────────────────────┘
                       │
                       │
         ┌─────────────┼─────────────┐
         │              │              │
         │              │              │
    ┌────┴────┐   ┌────┴────┐   ┌────┴────┐
    │   Main   │   │ Renderer │   │  System  │
    │ Process  │   │  Process  │   │   Tray   │
    │ (Node.js)│   │  (React)  │   │          │
    └──────────┘   └──────────┘   └──────────┘
         │              │              │
         │              │              │
         └─────────────┼─────────────┘
                       │
         ┌─────────────┼─────────────┐
         │              │              │
    ┌────┴────┐   ┌────┴────┐   ┌────┴────┐
    │ Service  │   │  VHost   │   │  Config  │
    │ Manager  │   │ Manager  │   │ Manager  │
    └──────────┘   └──────────┘   └──────────┘
         │              │              │
         │              │              │
         └─────────────┼─────────────┘
                       │
         ┌─────────────┼─────────────┐
         │              │              │
    ┌────┴────┐   ┌────┴────┐   ┌────┴────┐
    │  Apache  │   │  Nginx   │   │  MySQL   │
    │ (Binary) │   │ (Binary) │   │ (Binary) │
    └──────────┘   └──────────┘   └──────────┘
```

---

## 📦 Components

### 1. Main Process (Electron)

**Location:** `electron/main.ts`

**Responsibilities:**
- Initialize application
- Create browser windows
- Manage system tray
- Handle IPC communication
- Coordinate all managers

**Key Functions:**
```typescript
- createWindow(): Create main app window
- createTray(): Setup system tray
- handleIPC(): Handle renderer process requests
- setupAutoLaunch(): Setup auto-start on boot
```

---

### 2. Renderer Process (React + TypeScript)

**Location:** `src/`

**Responsibilities:**
- Display UI
- User interactions
- State management
- Communicate with main process via IPC

**Component Structure:**
```
src/
├── components/
│   ├── Dashboard/           # Main dashboard
│   ├── Services/            # Service controls
│   ├── VirtualHosts/        # VHost management
│   ├── Terminal/            # Integrated terminal
│   ├── Settings/            # App settings
│   └── common/              # Shared components
│
├── pages/
│   ├── Dashboard.tsx        # Dashboard page
│   ├── Services.tsx         # Services page
│   ├── Projects.tsx         # Projects page
│   └── Settings.tsx         # Settings page
│
├── store/
│   ├── servicesStore.ts     # Services state
│   ├── vhostStore.ts        # VHost state
│   └── settingsStore.ts     # Settings state
│
├── hooks/
│   ├── useServices.ts       # Services hook
│   ├── useVHosts.ts         # VHost hook
│   └── useIPC.ts            # IPC communication hook
│
└── App.tsx                  # Root component
```

---

### 3. System Tray

**Location:** `electron/tray.ts`

**Responsibilities:**
- Show app status (running/stopped)
- Quick actions menu
- Context menu

**Features:**
- Start/Stop all services
- Show/Hide main window
- Quick access to projects
- Exit application

---

### 4. Service Manager

**Location:** `electron/services/`

**Responsibilities:**
- Start/Stop services
- Monitor service health
- Manage service processes
- Handle service logs

**Services Supported:**
```
- Apache (bin/laragon/apache/)
- Nginx (bin/laragon/nginx/)
- MySQL (bin/laragon/mysql/)
- PostgreSQL (bin/laragon/postgresql/)
- MongoDB (bin/laragon/mongodb/)
- Redis (bin/laragon/redis/)
- Memcached (bin/laragon/memcached/)
```

**Key Files:**
```typescript
services/
├── BaseService.ts           # Abstract service class
├── ApacheService.ts         # Apache management
├── NginxService.ts          # Nginx management
├── MySQLService.ts          # MySQL management
├── PostgreSQLService.ts     # PostgreSQL management
├── MongoDBService.ts        # MongoDB management
├── RedisService.ts          # Redis management
└── ServiceRegistry.ts       # Service registry & coordination
```

---

### 5. Virtual Host Manager

**Location:** `electron/vhost/`

**Responsibilities:**
- Watch `www/` folder for changes
- Auto-generate virtual host configs
- Update `/etc/hosts` file
- Reload web server

**Key Files:**
```typescript
vhost/
├── Watcher.ts               # File system watcher
├── ConfigGenerator.ts       # VHost config generator
├── HostsManager.ts          # /etc/hosts management
└── VHostRegistry.ts         # VHost registry
```

**Workflow:**
```
1. User creates folder: www/myproject/
2. Watcher detects new folder
3. ConfigGenerator creates vhost config
4. HostsManager adds: 127.0.0.1 myproject.test
5. Web server reloaded
6. myproject.test accessible!
```

---

### 6. Configuration Manager

**Location:** `electron/config/`

**Responsibilities:**
- Manage application settings
- Manage service configurations
- Template processing
- Config file generation

**Key Files:**
```typescript
config/
├── AppConfig.ts             # App settings
├── ServiceConfig.ts         # Service configs
├── TemplateEngine.ts        # Config template engine
└── ConfigManager.ts         # Config coordination
```

---

## 🔄 Data Flow

### Service Start/Stop Flow

```
User clicks "Start Apache"
         │
         ↓
 React Component (Services.tsx)
         │
         ↓
 IPC Call: 'service:start:apache'
         │
         ↓
 Main Process receives IPC
         │
         ↓
 Service Manager -> ApacheService.start()
         │
         ↓
 child_process.spawn(apache binary)
         │
         ↓
 Monitor process status
         │
         ↓
 IPC Event: 'service:status:apache' -> 'running'
         │
         ↓
 React updates UI (green indicator)
```

### Virtual Host Creation Flow

```
User creates: www/myproject/
         │
         ↓
chokidar detects folder creation
         │
         ↓
Watcher.ts emits 'folder:created'
         │
         ↓
ConfigGenerator.generate('myproject')
         │
         └────────────────────────────┐
         │                              │
         ↓                              ↓
Generate Apache VHost          Generate Nginx VHost
  /etc/apache2/sites/            /etc/nginx/sites/
  myproject.conf                 myproject.conf
         │                              │
         └──────────────┬──────────────┘
                       │
                       ↓
HostsManager.addEntry('myproject.test')
         │
         ↓
Add to /etc/hosts:
  127.0.0.1 myproject.test
         │
         ↓
Reload web server
         │
         ↓
IPC Event: 'vhost:created'
         │
         ↓
React updates VHost list
         │
         ↓
myproject.test accessible! ✅
```

---

## 🔒 IPC Communication

### Channels

**Service Management:**
```typescript
// Renderer → Main
'service:start:apache'
'service:stop:apache'
'service:restart:apache'
'service:status:apache'
'service:logs:apache'

// Main → Renderer
'service:status:changed'
'service:log:output'
'service:error'
```

**Virtual Host Management:**
```typescript
// Renderer → Main
'vhost:list'
'vhost:create'
'vhost:delete'
'vhost:reload'

// Main → Renderer
'vhost:created'
'vhost:deleted'
'vhost:list:updated'
```

**Configuration:**
```typescript
// Renderer → Main
'config:get'
'config:set'
'config:service:get'
'config:service:set'

// Main → Renderer
'config:updated'
```

---

## 💾 State Management (Zustand)

### Services Store

```typescript
interface ServicesStore {
  services: {
    apache: ServiceStatus;
    nginx: ServiceStatus;
    mysql: ServiceStatus;
    // ...
  };
  startService: (name: string) => Promise<void>;
  stopService: (name: string) => Promise<void>;
  restartService: (name: string) => Promise<void>;
}
```

### VHost Store

```typescript
interface VHostStore {
  vhosts: VirtualHost[];
  fetchVHosts: () => Promise<void>;
  createVHost: (name: string) => Promise<void>;
  deleteVHost: (name: string) => Promise<void>;
}
```

### Settings Store

```typescript
interface SettingsStore {
  theme: 'light' | 'dark';
  language: string;
  defaultServer: 'apache' | 'nginx';
  autoStart: boolean;
  updateSettings: (settings: Partial<Settings>) => void;
}
```

---

## 📁 File Structure

```
laragon-cross-platform/
├── electron/                     # Electron main process
│   ├── main.ts
│   ├── preload.ts
│   ├── tray.ts
│   ├── services/
│   ├── vhost/
│   ├── config/
│   └── utils/
│
├── src/                         # React frontend
│   ├── components/
│   ├── pages/
│   ├── store/
│   ├── hooks/
│   ├── types/
│   ├── App.tsx
│   └── main.tsx
│
├── bin/                         # Bundled binaries
│   └── laragon/
│       ├── apache/
│       ├── nginx/
│       ├── mysql/
│       └── ...
│
├── etc/                         # Configuration templates
│   ├── apache2/
│   ├── nginx/
│   └── php/
│
├── www/                         # Web projects
│   └── index.php                 # Landing page
│
├── docs/                        # Documentation
└── package.json
```

---

## 🔑 Security Considerations

### 1. Elevated Privileges

**Problem:** `/etc/hosts` modification requires root

**Solution:**
- Use `electron-sudo` for Linux
- Prompt user when needed
- Cache authentication temporarily

### 2. Process Isolation

- Each service runs as separate process
- Crash isolation
- Resource monitoring

### 3. File System Access

- Validate all paths
- Prevent path traversal
- Proper file permissions

---

## 🚀 Performance

### Optimization Strategies

1. **Lazy Loading:** Load services on-demand
2. **Process Pooling:** Reuse service processes
3. **Config Caching:** Cache parsed configs
4. **Debounced Watchers:** Debounce file system events

---

**Last Updated:** Phase 0 - Architecture Design