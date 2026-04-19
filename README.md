# Gadirpull

**Automated Repository Manager & Deployment Daemon**


Gadirpull is a production-grade, versatile file synchronization and continuous deployment daemon for Linux systems. It monitors, syncs, and deploys content from multiple sources, including Git repositories, local directories, and network shares. The daemon continuously manages multiple repositories simultaneously and provides a real-time web dashboard and gardirpull cli for control. Gadirpull handles auto-deployment, keeps development environments in sync, and manages microservices across distributed infrastructure.

## Features

### Multi-Source Synchronization

- **Git Repositories - HTTP/HTTPS with token/password, SSH with key authentication 
- **Local Directories - Watch any non-Git directory using `file://` protocol 
- **NFS Shares - Mount and sync from NFS exports 
- **SMB/CIFS Shares - Windows/Samba file shares with authentication 
- **SSHFS - SSH File System mounts with key authentication 
- **FTP/FTPS - FTP servers with TLS support and fallback local copy 

### Synchronization

- **Configurable polling intervals (default 60 seconds)
- **SHA256 file hashing for intelligent change detection
- **Bidirectional conflict detection
- **Multiple conflict types: modified_both, deleted_local, deleted_remote, added_both, dir_file_conflict
- **Resolution options: keep_local, keep_remote, merge (with markers), auto resolution
- **Interactive conflict resolution with file diff viewer

### Backup & Recovery

- **Automatic ZIP backups before each sync operation
- **Configurable retention (keep up to 10 backups per repository)
- **One-click restore from any backup via web dashboard
- **Specific backup version selection
- **Backup metadata tracking (timestamps, sizes, branch info)
- **Backup statistics per repository

### Build & Deployment

- **Pre-commands (`buildcmd`) after successful sync (npm install, make install, composer install, etc.)
- **Systemd service management (create, start, stop, restart)
- **Command validation before service creation
- **Working directory isolation for services
- **Environment variable support

### Security

- **PAM authentication for web dashboard login
- **Session management with inactivity timeout (10 minutes)
- **Session persistence across daemon restarts
- **Rate limiting for login attempts (3 attempts per 20 seconds)
- **Command whitelisting - **restrict service commands to allowed list
- **Systemd-run isolation for build/start commands
- **Systemd credential encryption for sensitive files
- **ProtectSystem=strict for services (read-only root)
- **Private /tmp for services
- **Credential auto-cleanup on repository deletion

### Web Dashboard

- **Real-time updates with Server-Sent Events (SSE)
- **System metrics: CPU, memory, disk I/O, network
- **Interactive Chart.js graphs for CPU and memory history
- **Repository management: view, pull, pause, resume, delete
- **Service control: start, stop, restart
- **Automatic service port detection and display
- **Backup management UI (browse, restore, delete)
- **Conflict resolution UI with file diff viewer
- **Side-by-side file diff for conflicts
- **Per-repository action logs (clone, pull, buildcmd, restart, stop, start, restore)
- **Search and filter repositories by name, path, or URL
- **Dark/light theme toggle with system preference detection
- **Session timeout warning
- **Network and local access URL display

### Monitoring & Metrics

- **CPU usage percentage, model, core count, 60-point history
- **Memory total, used, free, usage percentage, 60-point history
- **Per-partition storage usage
- **Disk I/O: read/write MB/s, total bytes
- **Network: download/upload MB/s, total bytes
- **Process: uptime, goroutines, open files, CPU%, memory MB
- **Pull statistics: totals, success rate, average time
- **Recent pull history (last 100 pulls)

### Notifications

- **SMTP email alerts
- **TLS support for secure delivery
- **Event types: pull, clone, resume, pause, buildcmd, delete, restore
- **Status filtering: success only, failure only, or both
- **Configurable event selection
- **Test email verification

### Network Share Management

- **Temporary mounts (unmount on daemon shutdown)
- **Persistent mounts via systemd units or /etc/fstab
- **Secure credential storage (0600 permissions)
- **Automatic remount on daemon startup
- **Unique mount point generation
- **FTP fallback to local copy if curlftpfs unavailable

### Command Line Interface

 Command  Description 
----------------------
 **`-r <URL>`  Add repository**
 **`-delete -r <URL>`  Delete repository**
 **`-list`  List all tracked repositories**
 **`-find <URL>`  Find repository location**
 **`-d`  Start daemon with dashboard**
 **`-service`  Create/remove systemd daemon service**
 **`-auth <type>`  Authentication type (none/token/password/ssh)** 
 **`-token <value>`  Token or password**
 **`-user <username>`  Username for HTTPS auth** 
 **`-sshkey <path>`  SSH private key path**
 **`-buildcmd <cmd>`  Build command after sync** 
 **`-startcmd <cmd>`  Service start command**
 **`-s`  Create systemd service for repository** 
 **`-allowedcmd <cmds>`  Comma-separated command whitelist** 
 **`-envencrypt <files>`  Encrypt files with systemd-creds**
 **`-envunencrypt`  Remove encrypted credentials**
 **`-createfile text\file`  Create file in repository** 
 **`-filename <name>`  Destination filename**
 **`-text "<content>"`  Inline content for text mode** 
 **`-file <path>`  Source file for file mode**
 **`-c <seconds>`  Pull interval (default 60) or webhook -c webhook**
 **`-b <branches>`  Comma-separated branches**
 **`-host <addr>`  Dashboard listen address**
 **`-port <port>`  Dashboard listen port**
 
### File Operations

- **Recursive directory copy with permission preservation
- **30-second interval change detection for non-Git directories
- **SHA256 file hashing for change detection
- **Merge with conflict markers (`<<<<<<< REMOTE`, `=======`, `>>>>>>> LOCAL`)
- **Binary file detection to prevent corrupt merges
- **CreateFile feature with text or file mode
- **Overwrite confirmation for interactive mode

### Repository Management

- **Multi-branch tracking per repository
- **Branch status: last status, check time, pull time, retry count
- **Pause/resume syncing
- **Automatic stashing of local changes before pull
- **Force pull with `reset --hard` and `clean -fd`
- **Git authentication injection into remote URLs
- **SSH command customization with `GIT_SSH_COMMAND`
- **Exponential backoff retry logic (5 retries, 5-60s backoff)

### System Integration

- **Systemd daemon (`gadirpull-daemon.service`)
- **Optional auto-start on boot
- **Graceful shutdown with state saving (SIGINT, SIGTERM)
- **Service state persistence across restarts
- **Automatic restoration of previously running services
- **Configuration directory: `/etc/gadirpull/`
- **Credential directory: `/etc/gadirpull/credentials/`
- **Backup directory: `/etc/gadirpull/backups/`
- **JSON-based configuration (no external database)

### Error Handling

- **Retry on transient errors (network, timeouts, SSL)
- **Partial clone rollback on failure
- **Backup restore if post-sync operations fail
- **Conflict detection prevents data loss
- **Graceful degradation (FTP fallback to local copy)


### Developer-Friendly Features

- **No External Database - Uses JSON files for all configuration
- **Simple Configuration - Human-readable JSON in /etc/gadirpull/repos.json
- **Comprehensive Logging - Per-action logs with command output
- **API Endpoints - REST-like endpoints for all operations
- **Event Streaming - SSE for real-time UI updates
- **Cross-Platform - runs on any Linux system
    
    
### Installation

```bash
# Clone and build
git clone https://github.com/amainyebriggs/gardirpull.git
cd gardirpull
sudo chmod +x gardirpull

# Or install directly
sudo cp gardirpull /usr/local/bin/  && rehash # Run gardirpull as system wide global command

sudo ./gardirpull -d # Run gardirpull as a non system-wide global command

##  First initialization

gardirpull --service # create the gardirpull as a service and start the gardirpull service on port :8445
#or create a git -r respository clone and gardirpull ask you if you want to run gardirpull as a service 
```

<img src="dashboard.png" width="100%" alt="dashboard">

```bash
##	Basic usage

# Add a repository with default settings (main branch, 60s interval)
gardirpull -r https://github.com/user/repo.git

# Add with specific branch
gardirpull -r https://github.com/user/repo.git -b develop

# Add with multiple branches
gardirpull -r https://github.com/user/repo.git -b main,develop,staging,feature-x

# Add with custom pull interval (120 seconds)
gardirpull -r https://github.com/user/repo.git -c 120

# Add repository in specific directory (run from that directory)
cd /var/www/ && gardirpull -r https://github.com/user/webapp.git

# Add multiple repositories in different folders
cd /home/user/projects
gardirpull -r https://github.com/user/backend.git

cd /var/services && gardirpull -r https://github.com/user/api-gateway.git

##	Authentication Usage

# HTTPS with GitHub Personal Access Token
gardirpull -r https://github.com/user/private-repo.git -auth token -token ghp_xxxxxxxxxxxx

# HTTPS with username and password
gardirpull -r https://gitlab.com/user/repo.git -auth password -user myusername -token mypassword

# SSH with default key (~/.ssh/id_rsa)
gardirpull -r git@github.com:user/repo.git

# SSH with custom private key
gardirpull -r git@github.com:user/repo.git -auth ssh -sshkey /home/user/.ssh/deploy-key

# SSH with custom key and specific branch
gardirpull -r git@github.com:user/repo.git -auth ssh -sshkey /etc/ssh/repo-key -b production

##	Service Management

# Add repository with systemd service (auto-start on boot, auto-restart on failure)
gardirpull -r https://github.com/user/node-app.git -s -cmd "node server.js"

# Add with service and custom interval
gardirpull -r https://github.com/user/python-app.git -s -cmd "python app.py" -c 30

# Add Go service with build step
gardirpull -r https://github.com/user/golang-api.git -s -cmd "./api-server" -precmd "go build -o api-server"

# Add Docker service
gardirpull -r https://github.com/user/docker-app.git -s -cmd "docker-compose up -d"

# Add systemd service for a specific branch
gardirpull -r https://github.com/user/app.git -b production -s -cmd "/usr/bin/myapp --prod"

# Update existing repo to add service
gardirpull -r https://github.com/user/app.git -s -cmd "node server.js"


##	File Creation

# Run build command after pull
gardirpull -r https://github.com/user/app.git -precmd "make build"

# Run multiple commands
gardirpull -r https://github.com/user/app.git -precmd "npm install && npm run build"

# Run with service restart
gardirpull -r https://github.com/user/app.git -precmd "systemctl restart myapp"

# Run database migrations after pull
gardirpull -r https://github.com/user/app.git -precmd "python manage.py migrate"

# Run tests after update (optional, can fail without stopping)
gardirpull -r https://github.com/user/app.git -precmd "go test ./... || true"

# Combine pre-cmd with service management
gardirpull -r https://github.com/user/webapp.git -s -cmd "gunicorn app:app" -precmd "pip install -r requirements.txt"


##	Repository Management

# Delete a repository (removes files and service)
gardirpull -r https://github.com/user/repo.git -delete

# List all respository

gardirpull -list # List all respository

gardirpull -find https://github.com/user/repo.git #find a respository

# Network share with full deployment pipeline for Git Directories
gadirpull -r smb://buildserver/releases -b main -buildcmd "npm ci && npm run build" -s -startcmd "node dist/server.js" -allowedcmd "node,npm,pm2"

# Local directory watch with encrypted credentials
gadirpull -r file:///etc/app/config -c 10 -b master -s -startcmd "./app" -envencrypt ".env,secrets.json"

# NFS share with backup and restore
gadirpull -r nfs://storage.local/database -c 10 -b dev -buildcmd "./validate.sh"

# SSHFS with allowed commands restriction
gadirpull -r sshfs://deployer@server.com/app -c 10 -b nightbuild -s -startcmd "gunicorn app:application" -allowedcmd "gunicorn,python,nginx"

# FTP watch with build automation
gadirpull -r ftp://updates.server.com/patches -c 10 -b master -buildcmd "./apply-patches.sh" -s -startcmd "systemctl reload service"


# Watch and sync a local non-Git directory
gadirpull -r file:///path/to/source/directory

gadirpull -r file:///home/user/myapp -c 10 -b 13.x -c 10 -buildcmd "npm install"  -startcmd "npm start" -allowedcmd "node, npm"


# Example: Watch application assets
gadirpull -r file:///var/www/html/assets

# With build command and service
gadirpull -r file:///home/user/myapp -buildcmd "npm install" -s -startcmd "npm start"

# With allowed commands restriction
gadirpull -r file:///opt/app -s -startcmd "php artisan serve" -allowedcmd "php,node,npm"


# Basic NFS mount and sync
gadirpull -r nfs://192.168.1.100/exported/path

# NFS with specific export path
gadirpull -r nfs://server.example.com/srv/nfs/share

# NFS with authentication (if required)
gadirpull -r nfs://server/share -user username -token password

# NFS with persistent mount (systemd)
# Will prompt: Choose [1/2/3] -> select 2 for systemd persistent
gadirpull -r nfs://192.168.1.100/exports/data

# Basic SMB share (guest access)
gadirpull -r smb://server/sharename

# SMB with username and password
gadirpull -r smb://192.168.1.50/shared -user myuser -token mypassword

# CIFS protocol (alias for SMB)
gadirpull -r cifs://server/documents -user domain\\user -token pass123

# SMB with persistent mount via fstab
# Will prompt: Choose [1/2/3] -> select 3 for fstab persistent
gadirpull -r smb://storage.local/files -user smbuser -token secret

# SMB with build command
gadirpull -r smb://buildserver/releases -buildcmd "make install" -s -startcmd "php serve"

# Basic SSHFS with default SSH key (~/.ssh/id_rsa)
gadirpull -r sshfs://user@server.com/remote/path

# SSHFS with specific SSH key
gadirpull -r sshfs://user@192.168.1.100/var/www -sshkey /home/user/.ssh/deploy_key

# SSHFS with username in URL
gadirpull -r sshfs://deployer@example.com/opt/apps

# SSHFS with persistent systemd mount
# Will prompt: Choose [1/2/3] -> select 2 for systemd persistent
gadirpull -r sshfs://git@github.com/user/repo.git -sshkey /root/.ssh/github_key

# SSHFS with build and service
gadirpull -r sshfs://builder@buildserver.internal/builds -buildcmd "docker compose build" -s -startcmd "docker compose up"

# Basic FTP (anonymous)
gadirpull -r ftp://ftp.example.com/pub/files

# FTP with username and password
gadirpull -r ftp://example.com/uploads -user myuser -token mypassword

# FTPS (FTP over TLS)
gadirpull -r ftps://secure.server.com/data -user secureuser -token securepass

# FTP with credentials in URL
gadirpull -r ftp://user:pass@ftp.server.com/directory

# FTP fallback mode (if curlftpfs not available)
# Automatically uses local copy fallback
gadirpull -r ftp://backup.server.com/archives

# FTP with build command (for non-Git content)
gadirpull -r ftp://assets.server.com/static -buildcmd "make && make install"

```

<img src="Screenshot_20260403_223815.png" width="100%" alt="gardirpull">

```bash

##	Daemon Management- Daemon can be used if Gardirpull is not running as a systemd service 

# Start daemon in foreground (for testing)
sudo gardirpull -d

# Start daemon on specific network interface (local only - default)
sudo gardirpull -d -host 127.0.0.1

# Start daemon accessible from network (all interfaces)
sudo gardirpull -d -host 0.0.0.0

# Start daemon with custom folders config
sudo gardirpull -d -f /etc/myconfig/folders.txt

# Enable webhook API endpoint for the daemon and gadirpull must be started with a webhook daemon before a webhook repository can be added 
sudo gardirpull -d -webhook

# Create systemd service (interactive)
sudo gardirpull -service

# Remove systemd service and config (interactive)
sudo gardirpull -service

# Check service status
sudo systemctl status gardirpull-daemon

# Restart daemon
sudo systemctl restart gardirpull-daemon

# View gadirpull daemon service logs
sudo journalctl -u gardirpull-daemon -f

# Stop daemon
sudo systemctl stop gardirpull-daemon

## Security-Command restriction##
   
sudo gardirpull -r https://github.com/company/auth.git -s -cmd "node server" -precmd "npm install && mkdir foldername" -allowedcmd "node,npm,mkdir" ## this only expose the service to the allowed commands
sudo gardirpull -r https://github.com/company/auth.git -s -cmd "node server" -precmd "npm install && mkdir foldername" -envencrypt "myfile.env,myfile.txt,myfile1.cert" ## encrypt files and only decrypt during runtome and send to memory then make it available via memory path to the os.env($CREDENTIALS_DIRECTORY/yourfilename). This approach combines hardware locking, file system isolation, and memory protection

    # DON'T allow dangerous wildcards
-allowedcmd "*"                    # Defeats the purpose

# DON'T allow shell access
-allowedcmd "sh,bash,dash"         # Allows arbitrary command execution

# DON'T allow system modification
-allowedcmd "systemctl,chmod,chown,useradd"
    
##	Docker Microservices Stack

# Service 1: API Gateway
cd /opt/services && gardirpull -r https://github.com/company/gateway.git -s -cmd "docker-compose up -d gateway"

# Service 2: Auth Service  
gardirpull -r https://github.com/company/auth.git -s -cmd "docker-compose up -d auth"

# Service 3: Worker Service
gardirpull -r https://github.com/company/worker.git -s -cmd "docker-compose up -d worker"

# Pull static site and reload nginx
cd /var/www/html && gardirpull -r https://github.com/company/website.git -b main -precmd "chown -R www-data:www-data . && systemctl reload nginx"

#	Auto-Deploy Node.js Application
# Add repository with auto-restart

cd /var/www && gardirpull -r https://github.com/company/webapp.git -b production -s -cmd "node server.js" -precmd "npm install && npm run build" -createfile text -filename .env -text "NODE_ENV=production\nPORT=4000"
  
#CI/CD Pipeline Integration

# Build and test on pull
gardirpull -r https://github.com/company/app.git -precmd "make test && make build && systemctl restart app" -createfile text -filename .version -text "v1.0.0"
```
