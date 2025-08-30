# 🦊 GitLab Self-Hosted Setup

A complete GitLab CE self-hosted solution with PostgreSQL, Redis, and Cloudflare tunnel integration.

## 🔧 Environment Variables
Copy `.env.example` to `.env` and update the values:

```bash
cp .env.example .env
```

## 📋 Required Environment Variables:
- 🗄️ `POSTGRES_DB`: PostgreSQL database name (default: gitlab)
- 👤 `POSTGRES_USER`: PostgreSQL username (default: gitlab)  
- 🔐 `POSTGRES_PASSWORD`: PostgreSQL password (default: gitlab_password_secure)
- 📧 `GITLAB_ROOT_EMAIL`: GitLab admin email
- 🔑 `GITLAB_ROOT_PASSWORD`: GitLab admin password
- 🌐 `GITLAB_EXTERNAL_URL`: GitLab external URL
- ☁️ `CLOUDFLARE_TUNNEL_TOKEN`: Cloudflare tunnel token

## 📁 Data Directory Structure:
All GitLab data is centrally stored in `/data/gitlab/`:
```
/data/gitlab/
├── 🗄️ postgresql/     # PostgreSQL database files
├── 🚀 redis/          # Redis data files  
├── ⚙️ config/         # GitLab configuration
├── 📝 logs/           # GitLab logs
├── 💾 data/           # GitLab application data
└── 📦 artifacts/      # GitLab artifacts storage
```

## 🚀 Quick Start:

### 1️⃣ Prepare Environment
```bash
# Create data directory
sudo mkdir -p /data/gitlab
sudo chown -R 1000:1000 /data/gitlab

# Setup environment
cp .env.example .env
# Edit .env with your values
```

### 2️⃣ Launch Services
```bash
docker compose up -d
```

### 3️⃣ Access GitLab
- 🌐 **Local Access**: `http://localhost:2424`
- 🌐 **External Access**: Your configured `GITLAB_EXTERNAL_URL` (via Cloudflare)
- 🔑 **Default Admin**: Use credentials from `.env` file

### 4️⃣ **Auto-start on Boot (Optional)**: 
```bash
# Create systemd service for auto-start
sudo cp gitlab.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable gitlab.service

# Start the service
sudo systemctl start gitlab.service

# Check status
sudo systemctl status gitlab.service
```

## 🛠️ Services Included:
- 🦊 **GitLab CE**: Main GitLab application (port 2424)
- 🐘 **PostgreSQL**: Database backend (port 5432)
- 🚀 **Redis**: Caching and session storage (port 6379)
- ☁️ **Cloudflare Tunnel**: Secure external access

## 🔧 Configuration Details:
- **GitLab Internal Port**: 2424 (configured in nginx)
- **External Port Mapping**: `2424:2424` (host:container)
- **Network Mode**: Cloudflare uses shared network with GitLab
- **Health Checks**: All services include health monitoring
- **Restart Policy**: All services use `restart: always` for reliable auto-restart

## 📊 Monitoring:
GitLab includes built-in health checks and monitoring endpoints for all services.

## 🚨 Troubleshooting:

### Cloudflare Tunnel Issues:
If you get "Bad Gateway" errors:
1. **Check container status**: `docker compose ps`
2. **Verify network mode**: Cloudflare should use `container:...` network
3. **Check tunnel logs**: `docker compose logs cloudflared`
4. **Restart tunnel**: `docker compose restart cloudflared`

### Common Issues:
- **Port 2424 not accessible**: Ensure GitLab is healthy and nginx is listening
- **Cloudflare connection refused**: Verify shared network mode is working
- **Database connection issues**: Check PostgreSQL health status

### Useful Commands:
```bash
# Check service status
docker compose ps

# View logs
docker compose logs gitlab-server
docker compose logs cloudflared

# Restart specific service
docker compose restart gitlab-server
docker compose restart cloudflared

# Access GitLab shell
docker compose exec gitlab-server bash
```

### Systemd Service Management
If you've set up the systemd service for auto-start:

```bash
# Check service status
sudo systemctl status gitlab.service

# Start/stop the service
sudo systemctl start gitlab.service
sudo systemctl stop gitlab.service

# Restart the service
sudo systemctl restart gitlab.service

# View service logs
sudo journalctl -u gitlab.service -f
```

## 📄 License:
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
