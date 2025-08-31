# 🦊 GitLab - Self-Hosted Git Repository Management

[![GitLab](https://img.shields.io/badge/GitLab-FC6D26?style=for-the-badge&logo=gitlab&logoColor=white)](https://gitlab.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![Cloudflare](https://img.shields.io/badge/Cloudflare-F38020?style=for-the-badge&logo=cloudflare&logoColor=white)](https://cloudflare.com)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Redis](https://img.shields.io/badge/Redis-DC382D?style=for-the-badge&logo=redis&logoColor=white)](https://redis.io/)

## 📋 Overview

GitLab is a comprehensive DevOps platform that provides Git repository management, CI/CD pipelines, and project management tools. This repository provides a complete Docker-based setup for GitLab CE with PostgreSQL, Redis, and Cloudflare Tunnel integration for secure remote access.

## 🛠️ Features

- 🦊 **Git Repository Management** with full Git workflow support
- 🔄 **CI/CD Pipelines** for automated testing and deployment
- 🗄️ **PostgreSQL Database** for persistent data storage
- 🚀 **Redis Caching** for improved performance
- 🌐 **Cloudflare Tunnel Integration** for secure remote access
- 🐳 **Docker-based Deployment** for easy setup and management
- 🔐 **Self-hosted Option** with full control over your data
- 📱 **Responsive Interface** for desktop and mobile devices
- 🚀 **High Performance** with optimized container configurations
- 🔧 **Automated Systemd Service** for auto-start on boot

## 🚀 Quick Start

### Automated Installation

This repository includes a comprehensive installation script that supports multiple Linux distributions:

```bash
# Download and run the installation script
curl -sSL https://raw.githubusercontent.com/hungnguyen1503/gitlab/main/install_gitlab.sh | bash
```

The script will automatically:
- ✅ Detect your operating system and architecture
- ✅ Update system packages
- ✅ Install Docker and Docker Compose if not present
- ✅ Create necessary directories with proper permissions
- ✅ Download and configure the docker-compose file
- ✅ Start GitLab and supporting services
- ✅ Handle existing installations gracefully

### Manual Installation

If you prefer manual installation:

1. **Clone the repository:**
   ```bash
   git clone https://github.com/hungnguyen1503/gitlab.git
   cd gitlab
   ```

2. **Create environment file:**
   ```bash
   cp .env.example .env
   # Edit .env with your configuration
   nano .env  # or use your preferred editor
   ```

3. **Start the services:**
   ```bash
   docker-compose up -d
   ```

## 🔧 Configuration

### Environment Setup

1. **Copy the environment template:**
   ```bash
   cp .env.example .env
   ```

2. **Edit the `.env` file with your actual values:**
   ```bash
   nano .env  # or use your preferred editor
   ```

3. **Required variables to configure:**
   - `CLOUDFLARE_TUNNEL_TOKEN`: Get from [Cloudflare Dashboard](https://dash.cloudflare.com/cloudflare-one/tunnels)
   - `GITLAB_ROOT_EMAIL`: Admin email address
   - `GITLAB_ROOT_PASSWORD`: Admin password
   - `GITLAB_EXTERNAL_URL`: Your domain or localhost URL
   - `POSTGRES_USER`: Database username
   - `POSTGRES_PASSWORD`: Database password
   - `POSTGRES_DB`: Database name

### Environment Variables

Create a `.env` file in the project root with the following variables:

> **⚠️ Security Note:** Never commit your actual `.env` file to version control. Use `.env.example` as a template and keep your real credentials secure.

| Variable                   | Description                              | Default                | Required |
|----------------------------|------------------------------------------|------------------------|----------|
| `CLOUDFLARE_TUNNEL_TOKEN`  | Cloudflare Tunnel token for remote access| -                      | Yes      |
| `GITLAB_ROOT_EMAIL`        | GitLab admin email address               | (your email)           | Yes      |
| `GITLAB_ROOT_PASSWORD`     | GitLab admin password                    | (your password)        | Yes      |
| `GITLAB_EXTERNAL_URL`      | GitLab external URL                      | http://your-domain.com | Yes      |
| `POSTGRES_DB`              | PostgreSQL database name                 | gitlab                 | Yes      |
| `POSTGRES_USER`            | PostgreSQL username                      | gitlab                 | Yes      |
| `POSTGRES_PASSWORD`        | PostgreSQL password                      | (your password)        | Yes      |

### Docker Compose Services

The setup includes four main services:

#### GitLab Server (`gitlab-server`)
- **Image:** `gitlab/gitlab-ce:latest`
- **Ports:** `2424:2424`, `2222:22`
- **Volume:** `/data/gitlab:/var/opt/gitlab`
- **Features:**
  - Git repository management
  - CI/CD pipelines
  - Project management tools
  - User management and authentication
  - Built-in monitoring and health checks

#### PostgreSQL Database (`postgresql`)
- **Image:** `postgres:16-alpine`
- **Port:** `5432:5432`
- **Volume:** `/data/gitlab/postgresql:/var/lib/postgresql/data`
- **Features:**
  - Persistent data storage
  - Automatic health checks
  - Secure user authentication

#### Redis Caching (`redis`)
- **Image:** `redis:7-alpine`
- **Port:** `6379:6379`
- **Volume:** `/data/gitlab/redis:/data`
- **Features:**
  - Session storage
  - Cache management
  - Performance optimization

#### Cloudflare Tunnel (`cloudflared`)
- **Image:** `cloudflare/cloudflared:latest`
- **Network:** Service mode for optimal performance
- **Features:**
  - Secure remote access
  - Automatic tunnel management
  - DNS optimization

## 📁 Project Structure

```
gitlab/
├── docker-compose.yml     # Docker Compose configuration
├── install_gitlab.sh     # Automated installation script
├── gitlab.service        # Systemd service file for auto-start
├── README.md             # This file
├── .gitignore           # Git ignore rules
└── /data/gitlab/        # Data directory structure
    ├── postgresql/       # PostgreSQL database files
    ├── redis/            # Redis data files
    ├── config/           # GitLab configuration
    ├── logs/             # GitLab logs
    ├── data/             # GitLab application data
    └── artifacts/        # GitLab artifacts storage
```

## 🌐 Access

After installation, access GitLab through:

- **Local access:** http://localhost:2424/
- **Remote access:** Via Cloudflare Tunnel (configured in your tunnel settings)
- **SSH access:** Port 2222 (configured for Git operations)

## 🔧 Management Commands

### Start Services
```bash
docker-compose up -d
```

### Stop Services
```bash
docker-compose down
```

### View Logs
```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f gitlab-server
docker-compose logs -f postgresql
docker-compose logs -f redis
docker-compose logs -f cloudflared
```

### Update Services
```bash
docker-compose pull
docker-compose up -d
```

### Backup Data
```bash
# Backup PostgreSQL data
docker exec gitlab-postgresql pg_dump -U gitlab gitlab > backup_$(date +%Y%m%d_%H%M%S).sql

# Backup all data
tar -czf gitlab_backup_$(date +%Y%m%d_%H%M%S).tar.gz /data/gitlab/
```

## 🔄 Auto-start on Boot

### Systemd Service Setup

For automatic startup on boot:

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

### Systemd Service Management

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

## 🛡️ Security Considerations

- **Cloudflare Tunnel:** Provides secure remote access without exposing ports
- **File Permissions:** Enforced settings file permissions for security
- **Volume Isolation:** GitLab data is isolated in dedicated volumes
- **Environment Variables:** Sensitive data is stored in `.env` files (excluded from version control)
- **Database Security:** PostgreSQL with secure authentication
- **Redis Security:** Configured with memory limits and access controls

### Advanced Security Options

For additional security, consider these encryption methods:

#### Option 1: Docker Secrets (Production)
```bash
# Create encrypted secrets
echo "your_cloudflare_token" | docker secret create cloudflare_tunnel_token -
```

#### Option 2: GPG Encryption
```bash
# Encrypt your .env file
gpg -c .env
# This creates .env.gpg (encrypted) - commit this instead
```

#### Option 3: Ansible Vault
```bash
# Encrypt with Ansible Vault
ansible-vault encrypt .env
# Use: ansible-vault decrypt .env
```

## 📚 Documentation

For detailed GitLab documentation, visit:
- [Official GitLab Documentation](https://docs.gitlab.com)
- [GitLab CE Documentation](https://docs.gitlab.com/ce/)
- [Community Forum](https://forum.gitlab.com)
- [Cloudflare Tunnel Documentation](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/)

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 🐛 Troubleshooting

### Common Issues

1. **Port 2424 already in use:**
   ```bash
   sudo lsof -i :2424
   sudo kill -9 <PID>
   ```

2. **Permission issues with data directories:**
   ```bash
   sudo chown -R 1000:1000 /data/gitlab
   ```

3. **Docker service not running:**
   ```bash
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

4. **GitLab startup issues:**
   ```bash
   # Check GitLab logs
   docker-compose logs gitlab-server
   
   # Check database connection
   docker exec gitlab-postgresql pg_isready -U gitlab
   ```

5. **Cloudflare Tunnel issues:**
   ```bash
   # Check tunnel logs
   docker-compose logs cloudflared
   
   # Verify tunnel configuration
   docker exec gitlab-cloudflared tunnel info
   ```

### Logs and Debugging

Check service logs:
```bash
# Docker logs
docker-compose logs gitlab-server

# Systemd logs (if using service)
sudo journalctl -u gitlab.service -f
```

### Health Checks

```bash
# Check all services status
docker-compose ps

# Check specific service health
docker exec gitlab-postgresql pg_isready -U gitlab
docker exec gitlab-redis redis-cli ping
```

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Thanks to the [GitLab team](https://gitlab.com) for creating an amazing DevOps platform
- Thanks to [Cloudflare](https://cloudflare.com) for providing secure tunnel services
- Thanks to [PostgreSQL](https://www.postgresql.org/) for excellent database support
- Thanks to [Redis](https://redis.io/) for high-performance caching
- Thanks to all contributors who have helped improve this setup

## 📞 Support

- [GitLab Issues](https://gitlab.com/gitlab-org/gitlab/-/issues)
- [GitLab Community Forum](https://forum.gitlab.com)
- [GitLab Documentation](https://docs.gitlab.com)
- [Cloudflare Support](https://support.cloudflare.com/)

---

Made with ❤️ by the GitLab community
