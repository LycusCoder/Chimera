# 🗺️ Phase 2 Roadmap - Service Management

## 📋 Phase Overview

**Phase:** 2 - Service Management

**Status:** 📅 Planned

**Duration:** 3-4 days

**Goal:** Implement actual service management - start, stop, restart, monitor Apache, Nginx, MySQL, PostgreSQL, MongoDB, Redis, Memcached.

---

## 🎯 Objectives

### 1. Service Architecture
- [ ] Create BaseService abstract class
- [ ] Implement service-specific classes
- [ ] Create ServiceRegistry for coordination
- [ ] Setup service configuration
- [ ] Implement service health monitoring

### 2. Process Management
- [ ] Implement process spawning (child_process)
- [ ] Setup process monitoring
- [ ] Implement graceful shutdown
- [ ] Handle process crashes
- [ ] Setup auto-restart logic

### 3. Service Implementations
- [ ] Apache service
- [ ] Nginx service
- [ ] MySQL service
- [ ] PostgreSQL service
- [ ] MongoDB service
- [ ] Redis service
- [ ] Memcached service

### 4. UI Integration
- [ ] Update service status cards
- [ ] Add start/stop/restart buttons
- [ ] Show real-time status
- [ ] Display service logs
- [ ] Add error notifications

### 5. Terminal Integration
- [ ] Embed terminal in app (xterm.js)
- [ ] Setup shell integration
- [ ] Auto-cd to project directory
- [ ] Environment variables support
- [ ] Command history

---

## 📝 Tasks Breakdown

### Task 1: Service Architecture

**Status:** 📅 Planned

**Files to Create:**
```
electron/services/
├── BaseService.ts           # Abstract service class
├── ApacheService.ts
├── NginxService.ts
├── MySQLService.ts
├── PostgreSQLService.ts
├── MongoDBService.ts
├── RedisService.ts
├── MemcachedService.ts
├── ServiceRegistry.ts       # Service coordination
└── types.ts                 # Service types
```

**BaseService Abstract Class:**
```typescript
abstract class BaseService {
  abstract name: string;
  abstract binary: string;
  abstract configPath: string;
  abstract defaultPort: number;
  
  protected process: ChildProcess | null = null;
  protected status: ServiceStatus = 'stopped';
  
  abstract start(): Promise<void>;
  abstract stop(): Promise<void>;
  abstract restart(): Promise<void>;
  abstract isRunning(): Promise<boolean>;
  abstract getStatus(): ServiceStatus;
  abstract getLogs(): string[];
}
```

**ServiceRegistry:**
```typescript
class ServiceRegistry {
  private services: Map<string, BaseService>;
  
  register(service: BaseService): void;
  unregister(name: string): void;
  get(name: string): BaseService | undefined;
  startAll(): Promise<void>;
  stopAll(): Promise<void>;
  getStatuses(): ServiceStatus[];
}
```

**Deliverables:**
- Service architecture implemented
- All service classes created
- Service registry functional

---

### Task 2: Apache Service

**Status:** 📅 Planned

**Implementation:**
```typescript
class ApacheService extends BaseService {
  name = 'apache';
  binary = path.join(app.getAppPath(), 'bin/laragon/apache/bin/httpd.exe');
  configPath = path.join(app.getAppPath(), 'etc/apache2/httpd.conf');
  defaultPort = 80;
  
  async start(): Promise<void> {
    // 1. Check if port 80 is available
    // 2. Spawn Apache process
    // 3. Monitor process status
    // 4. Emit status change event
  }
  
  async stop(): Promise<void> {
    // 1. Send graceful shutdown signal
    // 2. Wait for process to exit
    // 3. Force kill if timeout
    // 4. Emit status change event
  }
  
  async restart(): Promise<void> {
    await this.stop();
    await this.start();
  }
}
```

**Features:**
- Start/Stop/Restart Apache
- Port conflict detection
- Config validation
- Log capture
- Process monitoring

**Deliverables:**
- Apache can be started/stopped
- Logs are captured
- Status is accurate

---

### Task 3: Nginx Service

**Status:** 📅 Planned

**Similar to Apache but with Nginx specifics:**
- Binary: `bin/laragon/nginx/nginx.exe`
- Config: `etc/nginx/nginx.conf`
- Port: 80 (or 8080 if Apache is using 80)
- Commands: `nginx`, `nginx -s stop`, `nginx -s reload`

**Deliverables:**
- Nginx service working
- Can run alongside or instead of Apache

---

### Task 4: Database Services

**Status:** 📅 Planned

**MySQL Implementation:**
```typescript
class MySQLService extends BaseService {
  name = 'mysql';
  binary = 'bin/laragon/mysql/bin/mysqld.exe';
  configPath = 'etc/mysql/my.ini';
  defaultPort = 3306;
  
  // MySQL specific initialization
  // Handle data directory
  // Root password setup
}
```

**PostgreSQL Implementation:**
- Binary: `bin/laragon/postgresql/bin/pg_ctl`
- Data dir: `data/postgresql/`
- Port: 5432

**MongoDB Implementation:**
- Binary: `bin/laragon/mongodb/bin/mongod`
- Data dir: `data/mongodb/`
- Port: 27017

**Deliverables:**
- All database services working
- Data persistence
- Connection testing

---

### Task 5: Cache Services

**Status:** 📅 Planned

**Redis:**
- Binary: `bin/laragon/redis/redis-server`
- Port: 6379

**Memcached:**
- Binary: `bin/laragon/memcached/memcached.exe`
- Port: 11211

**Deliverables:**
- Redis service working
- Memcached service working

---

### Task 6: UI Integration

**Status:** 📅 Planned

**Services Page Update:**
```tsx
<ServiceCard
  name="Apache"
  status={status} // 'running' | 'stopped' | 'starting' | 'stopping'
  port={80}
  onStart={handleStart}
  onStop={handleStop}
  onRestart={handleRestart}
  onViewLogs={handleViewLogs}
/>
```

**Features:**
- Real-time status updates
- Loading states (starting/stopping)
- Error handling and notifications
- Log viewer modal
- Bulk actions (start all, stop all)

**Deliverables:**
- Services page fully functional
- Real-time status updates working
- Logs viewable in UI

---

### Task 7: Terminal Integration

**Status:** 📅 Planned

**Terminal Component:**
```tsx
import { Terminal } from 'xterm';
import 'xterm/css/xterm.css';

<TerminalComponent
  cwd={currentProjectPath}
  env={laravelEnvVars}
/>
```

**Features:**
1. **Embedded Terminal:**
   - Use xterm.js for terminal emulation
   - Full ANSI color support
   - Resizable terminal

2. **Shell Integration:**
   - Windows: cmd.exe or PowerShell
   - Linux: bash or zsh
   - macOS: zsh or bash

3. **Environment Variables:**
   - PHP_PATH
   - NODE_PATH
   - PYTHON_PATH
   - Project-specific vars

4. **Quick Commands:**
   - Predefined commands menu
   - Command history
   - Copy/paste support

**Deliverables:**
- Terminal integrated in app
- Shell working correctly
- Environment variables set
- Can execute commands

---

## 📊 Success Metrics

### Phase 2 Complete When:
- [✅] All services can be started/stopped
- [✅] Service status is accurate and real-time
- [✅] Process monitoring is working
- [✅] Service logs are captured and viewable
- [✅] Terminal is integrated and functional
- [✅] Multiple services can run simultaneously
- [✅] Graceful shutdown working
- [✅] Port conflict detection working
- [✅] Error handling robust
- [✅] UI updates in real-time

---

## 🚀 Next Steps (After Phase 2)

### Phase 3: Auto Virtual Host
1. File watcher for www/ folder
2. Virtual host config generation
3. Hosts file management
4. Landing page

**Expected Duration:** 2-3 days

---

## 🐛 Potential Issues

### Issue 1: Port Already in Use

**Problem:** Service can't start because port is occupied

**Solutions:**
- Check port before starting
- Show clear error message
- Suggest alternative port
- Option to kill process using port

### Issue 2: Service Binary Not Found

**Problem:** Binary doesn't exist or wrong path

**Solutions:**
- Validate binary path on startup
- Show helpful error message
- Guide user to download service

### Issue 3: Permission Issues

**Problem:** Can't start service due to permissions

**Solutions:**
- Request elevated privileges when needed
- Check file permissions
- Set proper permissions on install

### Issue 4: Service Crashes

**Problem:** Service process crashes unexpectedly

**Solutions:**
- Capture crash logs
- Implement auto-restart
- Notify user of crash
- Limit restart attempts

---

## 🎯 Phase 2 Checklist

### Architecture
- [ ] Create BaseService class
- [ ] Create ServiceRegistry
- [ ] Define service types
- [ ] Setup service configuration

### Service Implementations
- [ ] Implement ApacheService
- [ ] Implement NginxService
- [ ] Implement MySQLService
- [ ] Implement PostgreSQLService
- [ ] Implement MongoDBService
- [ ] Implement RedisService
- [ ] Implement MemcachedService

### Process Management
- [ ] Implement process spawning
- [ ] Setup process monitoring
- [ ] Handle graceful shutdown
- [ ] Implement auto-restart
- [ ] Setup log capture

### UI
- [ ] Update Services page
- [ ] Add service cards
- [ ] Implement start/stop buttons
- [ ] Show real-time status
- [ ] Create log viewer
- [ ] Add notifications

### Terminal
- [ ] Integrate xterm.js
- [ ] Setup shell integration
- [ ] Configure environment
- [ ] Add command history
- [ ] Test terminal commands

### Testing
- [ ] Test starting all services
- [ ] Test stopping all services
- [ ] Test restart functionality
- [ ] Test port conflicts
- [ ] Test service crashes
- [ ] Test log viewing
- [ ] Test terminal commands

---

**Phase 2 Status:** 📅 Waiting for Phase 1

**Expected Completion:** 3-4 days after Phase 1

**Blocker:** Phase 1 must be completed first
