# 🛠️ Service Management Workflow

## 📋 Overview

Service Management adalah core feature dari Laragon. User dapat start/stop/restart berbagai services (Apache, Nginx, MySQL, dll) yang bundled dalam aplikasi.

---

## 🎯 Objectives

1. **Portable Services** - Semua services bundled, tidak perlu install di system
2. **Easy Management** - Start/stop dengan 1 click
3. **Process Monitoring** - Real-time status monitoring
4. **Error Handling** - Clear error messages
5. **Cross-Platform** - Work on Linux, Windows, macOS

---

## 📦 Supported Services

### Web Servers
1. **Apache** - Popular web server
   - Binary location: `bin/laragon/apache/bin/httpd`
   - Config: `etc/apache2/httpd.conf`
   - Ports: 80, 443

2. **Nginx** - High-performance web server
   - Binary location: `bin/laragon/nginx/nginx`
   - Config: `etc/nginx/nginx.conf`
   - Ports: 80, 443

### Databases
3. **MySQL** - Relational database
   - Binary: `bin/laragon/mysql/bin/mysqld`
   - Config: `etc/mysql/my.ini`
   - Port: 3306

4. **PostgreSQL** - Advanced relational database
   - Binary: `bin/laragon/postgresql/bin/postgres`
   - Config: `etc/postgresql/postgresql.conf`
   - Port: 5432

5. **MongoDB** - NoSQL database
   - Binary: `bin/laragon/mongodb/bin/mongod`
   - Config: `etc/mongodb/mongod.conf`
   - Port: 27017

### Caching
6. **Redis** - In-memory data store
   - Binary: `bin/laragon/redis/redis-server`
   - Config: `etc/redis/redis.conf`
   - Port: 6379

7. **Memcached** - Distributed memory caching
   - Binary: `bin/laragon/memcached/memcached`
   - Port: 11211

---

## 💻 Architecture

### Class Structure

```typescript
// Base Service Class
abstract class BaseService {
  name: string;
  binaryPath: string;
  configPath: string;
  port: number;
  process: ChildProcess | null;
  status: 'stopped' | 'starting' | 'running' | 'stopping' | 'error';

  abstract start(): Promise<void>;
  abstract stop(): Promise<void>;
  abstract restart(): Promise<void>;
  abstract checkHealth(): Promise<boolean>;
  abstract getLogs(): string[];
}

// Example: Apache Service
class ApacheService extends BaseService {
  constructor() {
    super();
    this.name = 'apache';
    this.binaryPath = path.join(APP_PATH, 'bin/laragon/apache/bin/httpd');
    this.configPath = path.join(APP_PATH, 'etc/apache2/httpd.conf');
    this.port = 80;
  }

  async start() {
    // Implementation
  }

  async stop() {
    // Implementation
  }

  // ...
}
```

### Service Registry

```typescript
class ServiceRegistry {
  private services: Map<string, BaseService> = new Map();

  register(service: BaseService) {
    this.services.set(service.name, service);
  }

  get(name: string): BaseService | undefined {
    return this.services.get(name);
  }

  getAll(): BaseService[] {
    return Array.from(this.services.values());
  }

  startAll(): Promise<void[]> {
    return Promise.all(
      this.getAll().map(service => service.start())
    );
  }

  stopAll(): Promise<void[]> {
    return Promise.all(
      this.getAll().map(service => service.stop())
    );
  }
}
```

---

## 🔄 Workflows

### 1. Start Service Workflow

```
User clicks "Start Apache"
         │
         ↓
[UI] Send IPC: 'service:start:apache'
         │
         ↓
[Main Process] Receive IPC call
         │
         ↓
[Service Registry] Get ApacheService
         │
         ↓
[ApacheService] Check if already running
         │
         ├─── Yes → Return "Already running"
         │
         └─── No → Continue
              │
              ↓
Check port availability (80)
         │
         ├─── In use → Error: "Port 80 already in use"
         │
         └─── Available → Continue
              │
              ↓
Validate config file exists
         │
         ├─── Missing → Generate from template
         │
         └─── Exists → Continue
              │
              ↓
Update status: 'starting'
Emit IPC: 'service:status:changed'
         │
         ↓
Spawn process:
  child_process.spawn(
    binaryPath,
    ['-f', configPath],
    { cwd: appPath }
  )
         │
         ↓
Wait 2 seconds for process to initialize
         │
         ↓
Check health: 
  - Process still running?
  - Port listening?
  - HTTP response OK?
         │
         ├─── Healthy → Status: 'running'
         │              Emit IPC: 'service:started'
         │
         └─── Unhealthy → Status: 'error'
                          Kill process
                          Emit IPC: 'service:error'
         │
         ↓
[UI] Update service card
  - Show green status
  - Enable Stop/Restart buttons
  - Disable Start button
```

---

### 2. Stop Service Workflow

```
User clicks "Stop Apache"
         │
         ↓
[UI] Send IPC: 'service:stop:apache'
         │
         ↓
[Main Process] Receive IPC call
         │
         ↓
[ApacheService] Check if running
         │
         ├─── Not running → Return "Already stopped"
         │
         └─── Running → Continue
              │
              ↓
Update status: 'stopping'
Emit IPC: 'service:status:changed'
         │
         ↓
Send SIGTERM to process
         │
         ↓
Wait 5 seconds for graceful shutdown
         │
         ├─── Process stopped → Continue
         │
         └─── Still running → Send SIGKILL (force)
              │
              ↓
Update status: 'stopped'
Emit IPC: 'service:stopped'
         │
         ↓
[UI] Update service card
  - Show red/gray status
  - Enable Start button
  - Disable Stop/Restart buttons
```

---

### 3. Restart Service Workflow

```
User clicks "Restart Apache"
         │
         ↓
[ApacheService] Stop service
         │
         ↓
Wait for stop to complete
         │
         ↓
[ApacheService] Start service
         │
         ↓
Done!
```

---

### 4. Health Check Workflow

**Periodic Health Checks** (every 30 seconds)

```
Timer triggers health check
         │
         ↓
For each service marked as 'running':
         │
         ↓
  Check 1: Process still alive?
         │
         ├─── No → Status: 'stopped' (crashed)
         │         Emit IPC: 'service:crashed'
         │
         └─── Yes → Continue
              │
              ↓
  Check 2: Port still listening?
         │
         ├─── No → Status: 'error'
         │
         └─── Yes → Continue
              │
              ↓
  Check 3: HTTP response OK? (for web servers)
         │
         ├─── No → Status: 'error'
         │
         └─── Yes → Status: 'running' (healthy)
```

---

## 📝 Implementation Details

### Port Availability Check

```typescript
import net from 'net';

function isPortAvailable(port: number): Promise<boolean> {
  return new Promise((resolve) => {
    const server = net.createServer();
    
    server.once('error', (err: any) => {
      if (err.code === 'EADDRINUSE') {
        resolve(false); // Port in use
      }
    });
    
    server.once('listening', () => {
      server.close();
      resolve(true); // Port available
    });
    
    server.listen(port);
  });
}
```

### Process Spawning

```typescript
import { spawn, ChildProcess } from 'child_process';

class ApacheService extends BaseService {
  async start(): Promise<void> {
    // Check port
    const available = await isPortAvailable(this.port);
    if (!available) {
      throw new Error(`Port ${this.port} is already in use`);
    }

    // Update status
    this.status = 'starting';
    this.emit('statusChanged', this.status);

    // Spawn process
    this.process = spawn(
      this.binaryPath,
      ['-f', this.configPath, '-D', 'FOREGROUND'],
      {
        cwd: path.dirname(this.binaryPath),
        env: { ...process.env },
        detached: false,
      }
    );

    // Handle stdout
    this.process.stdout?.on('data', (data) => {
      this.logs.push(data.toString());
      this.emit('log', data.toString());
    });

    // Handle stderr
    this.process.stderr?.on('data', (data) => {
      this.logs.push(`[ERROR] ${data.toString()}`);
      this.emit('error', data.toString());
    });

    // Handle exit
    this.process.on('exit', (code) => {
      this.status = 'stopped';
      this.emit('statusChanged', this.status);
      this.emit('exited', code);
    });

    // Wait and verify
    await this.waitForStart();
  }

  private async waitForStart(): Promise<void> {
    await sleep(2000); // Wait 2 seconds

    const healthy = await this.checkHealth();
    if (healthy) {
      this.status = 'running';
      this.emit('statusChanged', this.status);
      this.emit('started');
    } else {
      this.status = 'error';
      this.stop();
      throw new Error('Service failed to start');
    }
  }
}
```

### Health Checks

```typescript
class ApacheService extends BaseService {
  async checkHealth(): Promise<boolean> {
    // Check 1: Process alive
    if (!this.process || this.process.killed) {
      return false;
    }

    // Check 2: Port listening
    const listening = await isPortListening(this.port);
    if (!listening) {
      return false;
    }

    // Check 3: HTTP response
    try {
      const response = await fetch(`http://localhost:${this.port}`);
      return response.status < 500; // Any non-5xx is OK
    } catch (error) {
      return false;
    }
  }
}

function isPortListening(port: number): Promise<boolean> {
  return new Promise((resolve) => {
    const socket = new net.Socket();
    
    socket.setTimeout(1000);
    
    socket.once('connect', () => {
      socket.destroy();
      resolve(true);
    });
    
    socket.once('error', () => {
      resolve(false);
    });
    
    socket.once('timeout', () => {
      socket.destroy();
      resolve(false);
    });
    
    socket.connect(port, '127.0.0.1');
  });
}
```

---

## 📡 IPC Communication

### Channels

**From Renderer to Main:**
```typescript
// Start service
ipcRenderer.invoke('service:start', serviceName);

// Stop service
ipcRenderer.invoke('service:stop', serviceName);

// Restart service
ipcRenderer.invoke('service:restart', serviceName);

// Get service status
ipcRenderer.invoke('service:status', serviceName);

// Get service logs
ipcRenderer.invoke('service:logs', serviceName);
```

**From Main to Renderer:**
```typescript
// Status changed
mainWindow.webContents.send('service:statusChanged', {
  service: 'apache',
  status: 'running'
});

// Service error
mainWindow.webContents.send('service:error', {
  service: 'apache',
  error: 'Port 80 already in use'
});

// Service crashed
mainWindow.webContents.send('service:crashed', {
  service: 'apache',
  exitCode: 1
});

// New log
mainWindow.webContents.send('service:log', {
  service: 'apache',
  log: '[notice] Apache started'
});
```

---

## ⚠️ Error Handling

### Common Errors

1. **Port Already in Use**
   - Message: "Port 80 is already in use by another application"
   - Solution: Show which process is using the port, offer to kill it

2. **Binary Not Found**
   - Message: "Apache binary not found. Please reinstall Laragon."
   - Solution: Check file exists, show path

3. **Permission Denied**
   - Message: "Permission denied. Ports below 1024 require sudo."
   - Solution: Offer to restart with sudo, or use alternative ports

4. **Config Error**
   - Message: "Apache config file has errors"
   - Solution: Show config validation errors, offer to reset to default

5. **Service Crashed**
   - Message: "Apache crashed unexpectedly"
   - Solution: Show crash logs, offer to restart

---

## 📊 Logging

### Log Storage
```
logs/
├── apache.log
├── nginx.log
├── mysql.log
└── ...
```

### Log Rotation
- Max file size: 10MB
- Keep last 5 files
- Format: `apache.log`, `apache.1.log`, `apache.2.log`, ...

### Log Levels
```typescript
enum LogLevel {
  DEBUG = 'debug',
  INFO = 'info',
  WARN = 'warn',
  ERROR = 'error',
}

class Logger {
  log(level: LogLevel, service: string, message: string) {
    const timestamp = new Date().toISOString();
    const logLine = `[${timestamp}] [${level.toUpperCase()}] [${service}] ${message}\n`;
    
    // Write to file
    fs.appendFileSync(
      path.join(LOG_DIR, `${service}.log`),
      logLine
    );
    
    // Send to renderer
    mainWindow.webContents.send('service:log', {
      service,
      level,
      message,
      timestamp
    });
  }
}
```

---

## 🛡️ Security

### 1. Process Isolation
- Each service runs as separate process
- Crashes don't affect other services
- Resource limits per service

### 2. Port Security
- Bind only to localhost (127.0.0.1)
- No external access by default
- User can configure if needed

### 3. File Permissions
- Config files readable only by user
- Binary files executable only by user
- Log files writable only by user

---

## 🚀 Performance

### Optimization Strategies

1. **Lazy Process Creation**
   - Don't spawn processes until user clicks Start
   - Keep processes alive when stopped (optional)

2. **Debounced Health Checks**
   - Don't check too frequently
   - Use exponential backoff for failed checks

3. **Log Buffer**
   - Buffer logs before writing
   - Write in batches

4. **Process Pooling**
   - Reuse stopped processes if possible
   - Avoid constant spawn/kill cycles

---

**Platform Support:** Linux (primary), Windows, macOS

**Dependencies:**
- Node.js `child_process`
- Node.js `net` (port checking)
- `node-fetch` (health checks)

---

**Last Updated:** Phase 0 - Service Management Planning