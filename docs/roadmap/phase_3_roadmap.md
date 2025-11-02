# 🗺️ Phase 3 Roadmap - Auto Virtual Host ⭐

## 📋 Phase Overview

**Phase:** 3 - Auto Virtual Host (MOST IMPORTANT FEATURE)

**Status:** 📅 Planned

**Duration:** 2-3 days

**Goal:** Implement Laragon's signature feature - automatic virtual host creation. When user creates `www/myproject/`, automatically make it accessible at `myproject.test`.

---

## 🎯 Objectives

### 1. File System Watcher
- [ ] Setup chokidar to watch www/ folder
- [ ] Detect folder creation/deletion
- [ ] Debounce events to avoid spam
- [ ] Handle nested folders
- [ ] Ignore hidden folders

### 2. Virtual Host Config Generation
- [ ] Create Apache vhost template
- [ ] Create Nginx vhost template
- [ ] Parse template variables
- [ ] Generate config files
- [ ] Validate generated configs

### 3. Hosts File Management
- [ ] Read /etc/hosts (Linux) or C:\Windows\System32\drivers\etc\hosts (Windows)
- [ ] Add entries: 127.0.0.1 myproject.test
- [ ] Remove entries on folder deletion
- [ ] Request sudo/admin privileges
- [ ] Handle errors gracefully

### 4. Web Server Reload
- [ ] Reload Apache config
- [ ] Reload Nginx config
- [ ] Validate before reload
- [ ] Handle reload errors

### 5. Landing Page
- [ ] Create dashboard at 127.0.0.1
- [ ] List all virtual hosts
- [ ] Quick links to projects
- [ ] Show service status
- [ ] Add quick actions

### 6. UI Integration
- [ ] Projects page listing
- [ ] Add project manually
- [ ] Delete project
- [ ] Open project in browser
- [ ] Open project folder

---

## 📝 Tasks Breakdown

### Task 1: File System Watcher

**Status:** 📅 Planned

**File:** `electron/vhost/Watcher.ts`

**Implementation:**
```typescript
import chokidar from 'chokidar';

class VHostWatcher {
  private watcher: FSWatcher;
  private wwwPath: string;
  
  start(): void {
    this.watcher = chokidar.watch(this.wwwPath, {
      ignored: /^\./, // Ignore hidden files/folders
      persistent: true,
      ignoreInitial: false,
      depth: 1, // Only watch top-level folders
      awaitWriteFinish: true // Wait for write to complete
    });
    
    this.watcher
      .on('addDir', this.handleFolderAdded)
      .on('unlinkDir', this.handleFolderDeleted);
  }
  
  private handleFolderAdded(path: string): void {
    // 1. Get folder name
    // 2. Validate folder name (alphanumeric, hyphens)
    // 3. Check if index.php or index.html exists
    // 4. Trigger vhost creation
  }
  
  private handleFolderDeleted(path: string): void {
    // 1. Get folder name
    // 2. Trigger vhost deletion
  }
}
```

**Features:**
- Watch www/ folder continuously
- Detect new project folders
- Detect deleted project folders
- Debounce events (500ms)
- Ignore .git, node_modules, etc.

**Deliverables:**
- Watcher detects folder changes
- Events emitted correctly
- No performance issues

---

### Task 2: Config Templates

**Status:** 📅 Planned

**Apache VHost Template:**
```apache
# /etc/apache2/sites-available/{{PROJECT_NAME}}.conf

<VirtualHost *:80>
    ServerName {{PROJECT_NAME}}.test
    ServerAlias www.{{PROJECT_NAME}}.test
    DocumentRoot "{{PROJECT_PATH}}"
    
    <Directory "{{PROJECT_PATH}}">
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Require all granted
    </Directory>
    
    ErrorLog "{{LOG_PATH}}/{{PROJECT_NAME}}-error.log"
    CustomLog "{{LOG_PATH}}/{{PROJECT_NAME}}-access.log" combined
</VirtualHost>
```

**Nginx VHost Template:**
```nginx
# /etc/nginx/sites-available/{{PROJECT_NAME}}.conf

server {
    listen 80;
    server_name {{PROJECT_NAME}}.test www.{{PROJECT_NAME}}.test;
    root {{PROJECT_PATH}};
    
    index index.php index.html index.htm;
    
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
    
    location ~ \.php$ {
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
    
    access_log {{LOG_PATH}}/{{PROJECT_NAME}}-access.log;
    error_log {{LOG_PATH}}/{{PROJECT_NAME}}-error.log;
}
```

**Deliverables:**
- Templates created
- Variable substitution working
- Configs generated correctly

---

### Task 3: Config Generator

**Status:** 📅 Planned

**File:** `electron/vhost/ConfigGenerator.ts`

**Implementation:**
```typescript
class ConfigGenerator {
  generateApache(projectName: string, projectPath: string): string {
    const template = this.loadTemplate('apache');
    return template
      .replace(/{{PROJECT_NAME}}/g, projectName)
      .replace(/{{PROJECT_PATH}}/g, projectPath)
      .replace(/{{LOG_PATH}}/g, this.getLogPath());
  }
  
  generateNginx(projectName: string, projectPath: string): string {
    const template = this.loadTemplate('nginx');
    return template
      .replace(/{{PROJECT_NAME}}/g, projectName)
      .replace(/{{PROJECT_PATH}}/g, projectPath)
      .replace(/{{LOG_PATH}}/g, this.getLogPath());
  }
  
  async writeConfig(server: 'apache' | 'nginx', content: string, filename: string): Promise<void> {
    const configPath = this.getConfigPath(server, filename);
    await fs.promises.writeFile(configPath, content);
  }
  
  validateConfig(server: 'apache' | 'nginx', configPath: string): Promise<boolean> {
    // Test config syntax before enabling
    // Apache: httpd -t
    // Nginx: nginx -t
  }
}
```

**Deliverables:**
- Config generation working
- Both Apache and Nginx supported
- Validation before applying

---

### Task 4: Hosts File Manager

**Status:** 📅 Planned

**File:** `electron/vhost/HostsManager.ts`

**Implementation:**
```typescript
import sudo from 'electron-sudo';

class HostsManager {
  private hostsPath: string;
  
  constructor() {
    // Linux/Mac: /etc/hosts
    // Windows: C:\Windows\System32\drivers\etc\hosts
    this.hostsPath = this.getHostsPath();
  }
  
  async addEntry(domain: string, ip: string = '127.0.0.1'): Promise<void> {
    // 1. Read current hosts file
    const content = await this.readHosts();
    
    // 2. Check if entry already exists
    if (this.hasEntry(content, domain)) {
      return; // Already exists
    }
    
    // 3. Add new entry
    const newEntry = `\n${ip}\t${domain}\t# Added by Laragon`;
    
    // 4. Write with sudo/admin privileges
    await this.writeHostsWithSudo(content + newEntry);
  }
  
  async removeEntry(domain: string): Promise<void> {
    // 1. Read current hosts file
    const content = await this.readHosts();
    
    // 2. Remove lines containing domain
    const lines = content.split('\n');
    const filtered = lines.filter(line => !line.includes(domain));
    
    // 3. Write back with sudo
    await this.writeHostsWithSudo(filtered.join('\n'));
  }
  
  private async writeHostsWithSudo(content: string): Promise<void> {
    // Use electron-sudo to write to protected file
    const options = { name: 'Laragon' };
    await sudo.exec(`echo "${content}" > ${this.hostsPath}`, options);
  }
}
```

**Challenges:**
1. **Requires Root/Admin:**
   - Linux: Use `pkexec` or `sudo`
   - Windows: Request UAC elevation
   - macOS: Use `sudo`

2. **Solutions:**
   - Use `electron-sudo` package
   - Cache authentication for 5 minutes
   - Show password prompt to user

**Deliverables:**
- Hosts file modification working
- Sudo/admin elevation working
- Entries added/removed correctly

---

### Task 5: VHost Creation Workflow

**Status:** 📅 Planned

**Complete Workflow:**
```
1. User creates folder: www/myproject/
          ↓
2. Watcher detects new folder
          ↓
3. Validate folder name (alphanumeric, no spaces)
          ↓
4. Check if index.php or index.html exists
          ↓
5. Generate Apache/Nginx config
          ↓
6. Write config to sites-available/
          ↓
7. Enable site (symlink to sites-enabled/)
          ↓
8. Validate config syntax
          ↓
9. Add entry to /etc/hosts: 127.0.0.1 myproject.test
          ↓
10. Reload web server
          ↓
11. Verify site is accessible
          ↓
12. Notify user: "myproject.test is ready!"
          ↓
13. Update UI with new project
```

**Error Handling:**
- Invalid folder name → Show error notification
- No index file → Create default index.html
- Config invalid → Show validation errors
- Hosts file locked → Request sudo again
- Web server fails → Show server logs

**Deliverables:**
- Complete workflow implemented
- Error handling robust
- User notifications clear

---

### Task 6: Landing Page

**Status:** 📅 Planned

**Location:** `www/index.php` (or create React app)

**Features:**
```php
<!DOCTYPE html>
<html>
<head>
    <title>Laragon Dashboard</title>
    <style>
        /* Beautiful modern dashboard styles */
    </style>
</head>
<body>
    <h1>Laragon Development Environment</h1>
    
    <section class="services">
        <h2>Services Status</h2>
        <div class="service-grid">
            <?php
            // Check service status
            // Show green/red indicators
            ?>
        </div>
    </section>
    
    <section class="projects">
        <h2>Your Projects</h2>
        <div class="project-list">
            <?php
            // Scan www/ folder
            // List all projects with links
            foreach (scandir('.') as $project) {
                if ($project === '.' || $project === '..') continue;
                echo "<a href='http://{$project}.test'>{$project}</a>";
            }
            ?>
        </div>
    </section>
    
    <section class="quick-actions">
        <h2>Quick Actions</h2>
        <button onclick="openApp()">Open Laragon App</button>
        <button onclick="createProject()">Create New Project</button>
    </section>
</body>
</html>
```

**Deliverables:**
- Landing page created
- Shows all projects
- Service status visible
- Quick links working

---

### Task 7: Projects UI

**Status:** 📅 Planned

**Projects Page Features:**

```tsx
<ProjectCard
  name="myproject"
  path="/app/www/myproject"
  url="http://myproject.test"
  type="Laravel" // Auto-detect
  actions={[
    { label: 'Open in Browser', onClick: openBrowser },
    { label: 'Open Folder', onClick: openFolder },
    { label: 'Open Terminal', onClick: openTerminal },
    { label: 'Delete', onClick: deleteProject }
  ]}
/>
```

**Features:**
1. **Project List:**
   - Show all projects in www/
   - Display project type (Laravel, WordPress, Node.js, etc.)
   - Show domain (myproject.test)
   - Show last accessed time

2. **Actions:**
   - Open in browser
   - Open folder in file manager
   - Open terminal in project directory
   - Delete project (with confirmation)
   - Regenerate vhost

3. **Add Project:**
   - Button to create new project folder
   - Quick create templates (Laravel, WordPress, etc.)

**Deliverables:**
- Projects page fully functional
- All actions working
- Project type detection

---

## 📊 Success Metrics

### Phase 3 Complete When:
- [✅] Folder in www/ auto-creates virtual host
- [✅] Domain accessible at projectname.test
- [✅] Hosts file updated automatically
- [✅] Sudo/admin elevation working
- [✅] Both Apache and Nginx supported
- [✅] Landing page shows all projects
- [✅] Projects page lists all projects
- [✅] Can open projects in browser
- [✅] Can delete projects
- [✅] Error handling robust
- [✅] Notifications clear and helpful

---

## 🚀 Next Steps (After Phase 3)

### Phase 4: Configuration Management
1. Apache/Nginx config editor
2. PHP version switching
3. SSL certificate generation
4. Database configuration

**Expected Duration:** 2-3 days

---

## 🐛 Potential Issues

### Issue 1: Permission Denied on Hosts File

**Problem:** Can't write to /etc/hosts without sudo

**Solutions:**
- Use electron-sudo
- Request elevation on first vhost creation
- Cache sudo for session
- Show clear error if denied

### Issue 2: DNS Not Resolving

**Problem:** projectname.test not resolving to 127.0.0.1

**Solutions:**
- Flush DNS cache
- Verify hosts file entry
- Check DNS resolver order
- Use .localhost instead of .test

### Issue 3: Config Syntax Error

**Problem:** Generated config has syntax error

**Solutions:**
- Validate config before applying
- Test with apache -t or nginx -t
- Show error details to user
- Rollback on error

### Issue 4: Port 80 Already in Use

**Problem:** System Apache/Nginx using port 80

**Solutions:**
- Detect port conflict
- Suggest stopping system service
- Offer alternative port
- Update vhost to use alt port

---

## 🎯 Phase 3 Checklist

### File Watcher
- [ ] Setup chokidar
- [ ] Watch www/ folder
- [ ] Handle folder added
- [ ] Handle folder deleted
- [ ] Debounce events
- [ ] Test watcher

### Config Generation
- [ ] Create Apache template
- [ ] Create Nginx template
- [ ] Implement config generator
- [ ] Add variable substitution
- [ ] Validate generated configs

### Hosts Management
- [ ] Implement HostsManager
- [ ] Add entry method
- [ ] Remove entry method
- [ ] Setup electron-sudo
- [ ] Test sudo elevation
- [ ] Handle permission errors

### VHost Creation
- [ ] Implement complete workflow
- [ ] Generate config
- [ ] Write config file
- [ ] Update hosts file
- [ ] Reload web server
- [ ] Verify site accessible
- [ ] Send notifications

### Landing Page
- [ ] Create index.php
- [ ] Show services status
- [ ] List all projects
- [ ] Add quick links
- [ ] Style beautifully

### Projects UI
- [ ] Create Projects page
- [ ] List all projects
- [ ] Show project cards
- [ ] Implement open in browser
- [ ] Implement open folder
- [ ] Implement delete project
- [ ] Add create project button

### Testing
- [ ] Test creating project
- [ ] Test accessing projectname.test
- [ ] Test deleting project
- [ ] Test sudo elevation
- [ ] Test Apache vhost
- [ ] Test Nginx vhost
- [ ] Test error scenarios

---

**Phase 3 Status:** 📅 Waiting for Phase 2

**Expected Completion:** 2-3 days after Phase 2

**Blocker:** Phase 2 must be completed first

**Priority:** ⭐ HIGHEST - This is Laragon's killer feature!
