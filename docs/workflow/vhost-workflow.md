# 🌐 Virtual Host Workflow

## 📋 Overview

Virtual Host Auto-Generation adalah **fitur paling penting** dari Laragon. Ketika user membuat folder di `www/`, aplikasi otomatis:

1. Detect folder baru
2. Generate virtual host config
3. Update `/etc/hosts` file
4. Reload web server
5. Domain `.test` langsung accessible!

**Example:**
```
www/myproject/ → myproject.test accessible!
```

---

## 🎯 Objectives

1. **Zero Configuration** - User hanya perlu buat folder
2. **Instant Access** - Domain langsung available
3. **Auto Reload** - Web server auto-reload
4. **Clean URLs** - Friendly `.test` domains
5. **SSL Support** - HTTPS otomatis (optional)

---

## 💻 Architecture

### Components

```
Virtual Host Manager
  ├── Watcher (chokidar)
  ├── Config Generator
  ├── Hosts Manager
  └── VHost Registry
```

### Class Structure

```typescript
// Virtual Host Model
interface VirtualHost {
  id: string;
  name: string;              // 'myproject'
  domain: string;            // 'myproject.test'
  path: string;              // '/app/www/myproject'
  server: 'apache' | 'nginx';
  ssl: boolean;
  created: Date;
}

// Watcher
class VHostWatcher {
  private watcher: FSWatcher;
  private wwwPath: string;

  watch() {
    this.watcher = chokidar.watch(this.wwwPath, {
      ignored: /(^|[\/\\])\../, // Ignore dotfiles
      persistent: true,
      ignoreInitial: false,
      depth: 1, // Only watch direct children
    });

    this.watcher
      .on('addDir', (path) => this.onFolderCreated(path))
      .on('unlinkDir', (path) => this.onFolderDeleted(path));
  }

  private onFolderCreated(folderPath: string) {
    const folderName = path.basename(folderPath);
    EventBus.emit('vhost:folder:created', folderName);
  }

  private onFolderDeleted(folderPath: string) {
    const folderName = path.basename(folderPath);
    EventBus.emit('vhost:folder:deleted', folderName);
  }
}

// Config Generator
class VHostConfigGenerator {
  generateApache(vhost: VirtualHost): string {
    const template = this.loadTemplate('apache');
    return this.processTemplate(template, vhost);
  }

  generateNginx(vhost: VirtualHost): string {
    const template = this.loadTemplate('nginx');
    return this.processTemplate(template, vhost);
  }

  private processTemplate(template: string, vhost: VirtualHost): string {
    return template
      .replace(/{{DOMAIN}}/g, vhost.domain)
      .replace(/{{PATH}}/g, vhost.path)
      .replace(/{{NAME}}/g, vhost.name);
  }
}

// Hosts Manager
class HostsManager {
  private hostsPath = '/etc/hosts';

  async addEntry(domain: string, ip: string = '127.0.0.1'): Promise<void> {
    // Read current hosts file
    const content = await fs.readFile(this.hostsPath, 'utf-8');

    // Check if already exists
    if (content.includes(domain)) {
      return; // Already exists
    }

    // Add new entry
    const newEntry = `\n# Laragon - ${domain}\n${ip} ${domain}\n`;
    
    // Request sudo (on Linux)
    await this.writeWithSudo(this.hostsPath, content + newEntry);
  }

  async removeEntry(domain: string): Promise<void> {
    const content = await fs.readFile(this.hostsPath, 'utf-8');
    
    // Remove entry and Laragon comment
    const lines = content.split('\n');
    const filtered = lines.filter((line, index) => {
      if (line.includes(domain)) return false;
      if (line.includes(`# Laragon - ${domain}`)) return false;
      return true;
    });
    
    await this.writeWithSudo(this.hostsPath, filtered.join('\n'));
  }

  private async writeWithSudo(path: string, content: string): Promise<void> {
    // On Linux, use pkexec or electron-sudo
    const sudo = require('electron-sudo');
    await sudo.exec(
      `echo "${content}" > ${path}`,
      { name: 'Laragon' }
    );
  }
}

// VHost Registry
class VHostRegistry {
  private vhosts: Map<string, VirtualHost> = new Map();
  private configPath = path.join(APP_PATH, 'usr/vhosts.json');

  load() {
    const data = fs.readFileSync(this.configPath, 'utf-8');
    const vhosts = JSON.parse(data);
    vhosts.forEach(v => this.vhosts.set(v.name, v));
  }

  save() {
    const data = JSON.stringify(
      Array.from(this.vhosts.values()),
      null,
      2
    );
    fs.writeFileSync(this.configPath, data);
  }

  add(vhost: VirtualHost) {
    this.vhosts.set(vhost.name, vhost);
    this.save();
  }

  remove(name: string) {
    this.vhosts.delete(name);
    this.save();
  }

  get(name: string): VirtualHost | undefined {
    return this.vhosts.get(name);
  }

  getAll(): VirtualHost[] {
    return Array.from(this.vhosts.values());
  }
}
```

---

## 🔄 Complete Workflow

### Auto Virtual Host Creation

```
User creates folder: www/myproject/
         │
         ↓
[chokidar] Detects 'addDir' event
         │
         ↓
[VHostWatcher] Emit event: 'vhost:folder:created'
         │
         ↓
[VHostManager] Receive event
         │
         ↓
Validate folder:
  - Not a dotfolder (.git, .vscode)
  - Not 'index.php' (landing page)
  - Is a directory
         │
         ├─── Invalid → Ignore
         │
         └─── Valid → Continue
              │
              ↓
Create VirtualHost object:
  {
    id: uuid(),
    name: 'myproject',
    domain: 'myproject.test',
    path: '/app/www/myproject',
    server: 'apache', // or nginx
    ssl: false,
    created: new Date()
  }
         │
         ↓
[ConfigGenerator] Generate config
         │
         ├────────────────────────────────┐
         │                                │
         ↓                                ↓
Generate Apache VHost           Generate Nginx VHost
         │                                │
Write to:                       Write to:
  etc/apache2/sites/              etc/nginx/sites/
  myproject.conf                  myproject.conf
         │                                │
         └────────────┬────────────────┘
                      │
                      ↓
[HostsManager] Add entry to /etc/hosts
         │
         ↓
Request sudo permission
         │
         ↓
User enters password
         │
         ↓
Add to /etc/hosts:
  # Laragon - myproject.test
  127.0.0.1 myproject.test
         │
         ↓
[VHostRegistry] Save vhost to registry
         │
         ↓
[ServiceManager] Reload web server
         │
         ├─── Apache → httpd -k graceful
         │
         └─── Nginx → nginx -s reload
         │
         ↓
Emit IPC: 'vhost:created'
         │
         ↓
[UI] Show notification:
  "✅ Virtual host created!
   myproject.test is now accessible"
         │
         ↓
[UI] Update virtual hosts list
         │
         ↓
Done! myproject.test accessible! 🎉
```

---

### Virtual Host Deletion

```
User deletes folder: www/myproject/
         │
         ↓
[chokidar] Detects 'unlinkDir' event
         │
         ↓
[VHostWatcher] Emit: 'vhost:folder:deleted'
         │
         ↓
[VHostManager] Receive event
         │
         ↓
Find vhost in registry: 'myproject'
         │
         ├─── Not found → Ignore
         │
         └─── Found → Continue
              │
              ↓
Delete config files:
  - etc/apache2/sites/myproject.conf
  - etc/nginx/sites/myproject.conf
         │
         ↓
[HostsManager] Remove from /etc/hosts
         │
         ↓
Request sudo
         │
         ↓
Remove lines:
  # Laragon - myproject.test
  127.0.0.1 myproject.test
         │
         ↓
[VHostRegistry] Remove from registry
         │
         ↓
[ServiceManager] Reload web server
         │
         ↓
Emit IPC: 'vhost:deleted'
         │
         ↓
[UI] Show notification:
  "Virtual host removed: myproject.test"
         │
         ↓
[UI] Update virtual hosts list
         │
         ↓
Done!
```

---

## 📝 Config Templates

### Apache VirtualHost Template

```apache
# Laragon - {{NAME}}
<VirtualHost *:80>
    ServerName {{DOMAIN}}
    DocumentRoot "{{PATH}}"
    
    <Directory "{{PATH}}">
        AllowOverride All
        Require all granted
        
        # Enable .htaccess
        Options Indexes FollowSymLinks MultiViews
        
        # PHP support
        <FilesMatch \.php$>
            SetHandler "proxy:fcgi://127.0.0.1:9000"
        </FilesMatch>
    </Directory>
    
    # Logging
    ErrorLog "${SRVROOT}/logs/{{NAME}}-error.log"
    CustomLog "${SRVROOT}/logs/{{NAME}}-access.log" common
</VirtualHost>
```

### Apache VirtualHost Template (SSL)

```apache
# Laragon - {{NAME}} (SSL)
<VirtualHost *:443>
    ServerName {{DOMAIN}}
    DocumentRoot "{{PATH}}"
    
    # SSL Configuration
    SSLEngine on
    SSLCertificateFile "${SRVROOT}/conf/ssl/{{NAME}}.crt"
    SSLCertificateKeyFile "${SRVROOT}/conf/ssl/{{NAME}}.key"
    
    <Directory "{{PATH}}">
        AllowOverride All
        Require all granted
        Options Indexes FollowSymLinks MultiViews
        
        <FilesMatch \.php$>
            SetHandler "proxy:fcgi://127.0.0.1:9000"
        </FilesMatch>
    </Directory>
    
    ErrorLog "${SRVROOT}/logs/{{NAME}}-error.log"
    CustomLog "${SRVROOT}/logs/{{NAME}}-access.log" common
</VirtualHost>
```

### Nginx Server Block Template

```nginx
# Laragon - {{NAME}}
server {
    listen 80;
    server_name {{DOMAIN}};
    root "{{PATH}}";
    index index.php index.html index.htm;
    
    # Charset
    charset utf-8;
    
    # Logging
    access_log /var/log/nginx/{{NAME}}-access.log;
    error_log /var/log/nginx/{{NAME}}-error.log;
    
    # PHP handling
    location ~ \.php$ {
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
    
    # Deny access to hidden files
    location ~ /\. {
        deny all;
    }
    
    # Try files
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
}
```

### Nginx Server Block Template (SSL)

```nginx
# Laragon - {{NAME}} (SSL)
server {
    listen 443 ssl;
    server_name {{DOMAIN}};
    root "{{PATH}}";
    index index.php index.html index.htm;
    
    # SSL Configuration
    ssl_certificate /etc/nginx/ssl/{{NAME}}.crt;
    ssl_certificate_key /etc/nginx/ssl/{{NAME}}.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    
    charset utf-8;
    
    access_log /var/log/nginx/{{NAME}}-access.log;
    error_log /var/log/nginx/{{NAME}}-error.log;
    
    location ~ \.php$ {
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
    
    location ~ /\. {
        deny all;
    }
    
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
}

# HTTP to HTTPS redirect
server {
    listen 80;
    server_name {{DOMAIN}};
    return 301 https://$server_name$request_uri;
}
```

---

## 🔐 Sudo/Root Access

### Problem
`/etc/hosts` requires root privileges to modify.

### Solutions

#### Option 1: electron-sudo (Recommended)
```typescript
import sudo from 'electron-sudo';

async function updateHosts(content: string) {
  const options = {
    name: 'Laragon',
    icns: '/path/to/icon.icns'
  };
  
  await sudo.exec(
    `echo "${content}" > /etc/hosts`,
    options
  );
}
```

#### Option 2: pkexec (Linux)
```typescript
import { exec } from 'child_process';
import { promisify } from 'util';

const execAsync = promisify(exec);

async function updateHosts(content: string) {
  // Write to temp file first
  await fs.writeFile('/tmp/hosts-temp', content);
  
  // Copy with pkexec
  await execAsync(
    'pkexec cp /tmp/hosts-temp /etc/hosts'
  );
  
  // Clean up
  await fs.unlink('/tmp/hosts-temp');
}
```

#### Option 3: Sudoers Rule (Advanced)
Create rule: `/etc/sudoers.d/laragon`
```
username ALL=(ALL) NOPASSWD: /usr/bin/tee /etc/hosts
```

Then:
```typescript
await execAsync(
  `echo "${content}" | sudo tee /etc/hosts`
);
```

---

## 📡 IPC Communication

### Channels

```typescript
// Get all virtual hosts
ipcRenderer.invoke('vhost:list')
  .then(vhosts => console.log(vhosts));

// Create virtual host manually
ipcRenderer.invoke('vhost:create', {
  name: 'myproject',
  server: 'apache',
  ssl: false
});

// Delete virtual host
ipcRenderer.invoke('vhost:delete', 'myproject');

// Open virtual host in browser
ipcRenderer.invoke('vhost:open', 'myproject.test');

// Listen for changes
ipcRenderer.on('vhost:created', (event, vhost) => {
  console.log('New vhost:', vhost);
});

ipcRenderer.on('vhost:deleted', (event, name) => {
  console.log('Deleted:', name);
});
```

---

## ⚠️ Edge Cases

### 1. Folder Name with Spaces
```
www/my project/ → my-project.test
```
Replace spaces with hyphens.

### 2. Folder Name with Special Characters
```
www/my@project$/ → myproject.test
```
Remove special characters.

### 3. Duplicate Domain Names
```
www/myproject/
www/MyProject/
```
Both would become `myproject.test`.

**Solution:** Append number
```
myproject.test
myproject-2.test
```

### 4. Domain Already Exists in /etc/hosts
Check before adding, skip if exists.

### 5. Web Server Offline
Queue vhost creation, apply when server starts.

---

## 🚀 Performance

### Optimizations

1. **Debounce File Events**
   - Wait 500ms after folder creation
   - Batch multiple creations

2. **Cache Templates**
   - Load templates once
   - Reuse for all vhosts

3. **Async Everything**
   - Don't block UI
   - Show progress notifications

4. **Graceful Reload**
   - Use `graceful` reload (no downtime)
   - Apache: `httpd -k graceful`
   - Nginx: `nginx -s reload`

---

## 🏆 Success Criteria

- ✅ Folder created → domain accessible within 5 seconds
- ✅ Works with Apache and Nginx
- ✅ SSL support (optional)
- ✅ No manual config needed
- ✅ Clean error messages
- ✅ Works on Linux (primary target)

---

**Last Updated:** Phase 0 - Virtual Host Workflow Planning