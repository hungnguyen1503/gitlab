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
docker-compose up -d
```

### 3️⃣ Access GitLab
- 🌐 Web Interface: Your configured `GITLAB_EXTERNAL_URL`
- 🔑 Default Admin: Use credentials from `.env` file

## 🛠️ Services Included:
- 🦊 **GitLab CE**: Main GitLab application
- 🐘 **PostgreSQL**: Database backend
- 🚀 **Redis**: Caching and session storage
- ☁️ **Cloudflare Tunnel**: Secure external access

## 📊 Monitoring:
GitLab includes built-in health checks and monitoring endpoints for all services.

## 📄 License:
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
