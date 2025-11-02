# 🗺️ Phase 4 Roadmap - Configuration Management

## 📋 Phase Overview

**Phase:** 4 - Configuration Management

**Status:** 📅 Planned

**Duration:** 2-3 days

**Goal:** Implement configuration management for Apache, Nginx, PHP, databases, and SSL certificates.

---

## 🎯 Objectives

### 1. Config Editor UI
- [ ] Create config editor component
- [ ] Syntax highlighting
- [ ] Validation before saving
- [ ] Backup on edit
- [ ] Restore from backup

### 2. Apache Configuration
- [ ] Edit httpd.conf
- [ ] Manage modules (enable/disable)
- [ ] Port configuration
- [ ] Virtual host templates
- [ ] .htaccess support

### 3. Nginx Configuration
- [ ] Edit nginx.conf
- [ ] Manage sites
- [ ] Port configuration
- [ ] SSL configuration
- [ ] Reverse proxy setup

### 4. PHP Management
- [ ] Switch PHP versions
- [ ] Enable/disable extensions
- [ ] Edit php.ini
- [ ] Memory limit, max_upload, etc.
- [ ] PHP-FPM configuration

### 5. Database Configuration
- [ ] MySQL configuration
- [ ] PostgreSQL configuration
- [ ] MongoDB configuration
- [ ] User management
- [ ] Backup/restore

### 6. SSL Certificate Management
- [ ] Generate self-signed certificates
- [ ] Import certificates
- [ ] Enable HTTPS for projects
- [ ] Auto-redirect HTTP to HTTPS
- [ ] Certificate viewer

---

## 📝 Tasks Breakdown

### Task 1: Config Editor Component

**Status:** 📅 Planned

**Implementation:**
```tsx
import MonacoEditor from '@monaco-editor/react';

<ConfigEditor
  file="/etc/apache2/httpd.conf"
  language="apache"
  onSave={handleSave}
  onValidate={validateConfig}
  onBackup={createBackup}
/>
```

**Features:**
1. **Monaco Editor Integration:**
   - Syntax highlighting
   - Code completion
   - Error detection
   - Search and replace

2. **File Operations:**
   - Read config file
   - Edit in place
   - Save changes
   - Create backup before save
   - Restore from backup

3. **Validation:**
   - Syntax check before save
   - Show errors inline
   - Prevent saving invalid config

**Deliverables:**
- Config editor working
- Syntax highlighting active
- Validation functional

---

### Task 2: Apache Configuration

**Status:** 📅 Planned

**Apache Config Manager:**
```typescript
class ApacheConfigManager {
  async editHttpdConf(): Promise<void>;
  async enableModule(module: string): Promise<void>;
  async disableModule(module: string): Promise<void>;
  async listModules(): Promise<string[]>;
  async setPort(port: number): Promise<void>;
  async validateConfig(): Promise<boolean>;
}
```

**UI Features:**
1. **Main Config:**
   - Edit httpd.conf
   - Set port (80, 8080, etc.)
   - Set document root
   - Set server name

2. **Modules Management:**
   ```
   ┌────────────────────────────┐
   │ Apache Modules             │
   ├────────────────────────────┤
   │ ☑ mod_rewrite (enabled)   │
   │ ☑ mod_ssl (enabled)       │
   │ ☐ mod_proxy (disabled)    │
   │ ☐ mod_headers (disabled)  │
   └────────────────────────────┘
   ```

**Deliverables:**
- Apache config editable
- Modules can be enabled/disabled
- Port configuration working

---

### Task 3: Nginx Configuration

**Status:** 📅 Planned

**Similar to Apache:**
```typescript
class NginxConfigManager {
  async editNginxConf(): Promise<void>;
  async listSites(): Promise<string[]>;
  async enableSite(site: string): Promise<void>;
  async disableSite(site: string): Promise<void>;
  async validateConfig(): Promise<boolean>;
}
```

**Deliverables:**
- Nginx config editable
- Sites can be enabled/disabled
- Validation working

---

### Task 4: PHP Version Switching

**Status:** 📅 Planned

**PHP Manager:**
```typescript
class PHPManager {
  async listVersions(): Promise<string[]>;
  async switchVersion(version: string): Promise<void>;
  async listExtensions(): Promise<Extension[]>;
  async enableExtension(ext: string): Promise<void>;
  async disableExtension(ext: string): Promise<void>;
  async editPhpIni(): Promise<void>;
}
```

**UI:**
```tsx
<PHPSettings>
  <VersionSelector
    versions={['7.4', '8.0', '8.1', '8.2']}
    current="8.1"
    onChange={switchPHPVersion}
  />
  
  <ExtensionsList
    extensions={[
      { name: 'curl', enabled: true },
      { name: 'mbstring', enabled: true },
      { name: 'gd', enabled: false },
    ]}
    onToggle={toggleExtension}
  />
  
  <ConfigEditor
    file="php.ini"
    settings={[
      { key: 'memory_limit', value: '256M' },
      { key: 'upload_max_filesize', value: '64M' },
      { key: 'post_max_size', value: '64M' },
    ]}
  />
</PHPSettings>
```

**Features:**
1. **Version Switching:**
   - Detect installed PHP versions
   - Switch between versions
   - Update Apache/Nginx config
   - Restart services

2. **Extension Management:**
   - List all extensions
   - Enable/disable extensions
   - One-click toggle

3. **php.ini Editor:**
   - Common settings quick edit
   - Full editor for advanced users
   - Validation before save

**Deliverables:**
- PHP version switching working
- Extensions can be toggled
- php.ini editable

---

### Task 5: Database Configuration

**Status:** 📅 Planned

**MySQL Config:**
```typescript
class MySQLManager {
  async editMyIni(): Promise<void>;
  async createDatabase(name: string): Promise<void>;
  async dropDatabase(name: string): Promise<void>;
  async listDatabases(): Promise<string[]>;
  async createUser(username: string, password: string): Promise<void>;
  async grantPermissions(user: string, db: string): Promise<void>;
  async backup(database: string): Promise<string>;
  async restore(database: string, file: string): Promise<void>;
}
```

**UI Features:**
1. **Database Management:**
   - List all databases
   - Create new database
   - Delete database
   - Backup/restore

2. **User Management:**
   - List users
   - Create user
   - Delete user
   - Grant/revoke permissions

3. **Config Editor:**
   - Edit my.ini / my.cnf
   - Set max_connections
   - Set buffer sizes
   - Set log settings

**Deliverables:**
- Database CRUD operations
- User management
- Backup/restore functional

---

### Task 6: SSL Certificate Management

**Status:** 📅 Planned

**SSL Manager:**
```typescript
class SSLManager {
  async generateSelfSigned(domain: string): Promise<Certificate>;
  async importCertificate(certPath: string, keyPath: string): Promise<void>;
  async enableSSL(project: string): Promise<void>;
  async disableSSL(project: string): Promise<void>;
  async listCertificates(): Promise<Certificate[]>;
  async renewCertificate(domain: string): Promise<void>;
}
```

**Self-Signed Cert Generation:**
```bash
openssl req -x509 -newkey rsa:4096 \
  -keyout myproject.key \
  -out myproject.crt \
  -days 365 \
  -nodes \
  -subj "/CN=myproject.test"
```

**Features:**
1. **Generate Certificates:**
   - One-click self-signed cert generation
   - Specify domain name
   - Set expiry (default 1 year)
   - Auto-install to system trust store

2. **Import Certificates:**
   - Import existing .crt and .key
   - Validate certificate
   - Install for project

3. **Enable HTTPS:**
   - Update vhost config
   - Add SSL directives
   - Setup redirect HTTP → HTTPS
   - Reload web server

**UI:**
```tsx
<SSLManager>
  <CertificateList>
    <CertCard
      domain="myproject.test"
      issuer="Self-signed"
      expiresIn="364 days"
      actions={['Renew', 'Delete', 'View']}
    />
  </CertificateList>
  
  <GenerateCert
    domain="newproject.test"
    validDays={365}
    onGenerate={handleGenerate}
  />
</SSLManager>
```

**Deliverables:**
- Self-signed certs generated
- HTTPS working for projects
- Certificate management UI

---

## 📊 Success Metrics

### Phase 4 Complete When:
- [✅] Can edit Apache config
- [✅] Can edit Nginx config
- [✅] Can switch PHP versions
- [✅] Can toggle PHP extensions
- [✅] Can edit php.ini
- [✅] Can manage databases
- [✅] Can generate SSL certificates
- [✅] HTTPS works for projects
- [✅] Config validation working
- [✅] Backups created before changes

---

## 🚀 Next Steps (After Phase 4)

### Phase 5: Polish & Features
1. Multi-language support (30+ languages)
2. Quick create menus (Laravel, WordPress, etc.)
3. Database tools integration
4. Auto-update mechanism

**Expected Duration:** 3-4 days

---

## 🎯 Phase 4 Checklist

### Config Editor
- [ ] Integrate Monaco Editor
- [ ] Syntax highlighting
- [ ] Validation
- [ ] Backup/restore

### Apache
- [ ] Edit httpd.conf
- [ ] Module management
- [ ] Port configuration
- [ ] Test config validation

### Nginx
- [ ] Edit nginx.conf
- [ ] Site management
- [ ] Test config validation

### PHP
- [ ] Version detection
- [ ] Version switching
- [ ] Extension management
- [ ] php.ini editor

### Database
- [ ] MySQL config editor
- [ ] Database CRUD
- [ ] User management
- [ ] Backup/restore

### SSL
- [ ] Self-signed cert generation
- [ ] Certificate import
- [ ] Enable HTTPS
- [ ] Certificate list UI

---

**Phase 4 Status:** 📅 Waiting for Phase 3

**Expected Completion:** 2-3 days after Phase 3
