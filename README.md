# GitLab Self-Hosted Docker Setup

This repository contains a Docker Compose configuration for running GitLab Community Edition with external PostgreSQL and Redis in a self-hosted environment.

## Features

- **Separate Database Services**: External PostgreSQL 16 and Redis 7 containers
- **Performance Optimized**: Optimized for faster startup and better resource utilization
- **Health Checks**: Comprehensive container health monitoring with fast intervals
- **Resource Limits**: Memory and CPU constraints for stability
- **Auto Migration**: Automatic database migration on startup
- **Service Dependencies**: Proper service dependency management
- **Network Isolation**: Dedicated Docker network for GitLab services

## Prerequisites

- Docker and Docker Compose installed
- Domain name pointing to your server (gitlab.hungngquang.xyz)
- Ports 2224 (SSH) and 2424 (HTTP) available on your server
- At least 2GB RAM and 1 CPU core recommended (optimized configuration)

## Architecture

This setup uses a **3-container architecture**:
- **gitlab-server**: GitLab CE application server
- **postgresql**: PostgreSQL 16 database server
- **redis**: Redis 7 caching server

## Directory Structure

```
gitlab/
├── docker-compose.yml          # Main configuration
├── README.md                   # This file

├── gitlab/                     # GitLab application data
│   ├── config/                 # GitLab configuration files
│   ├── logs/                   # GitLab log files
│   └── data/                   # GitLab data (repositories, uploads, etc.)
├── postgresql/                 # PostgreSQL database
│   └── data/                   # Database files
└── redis/                      # Redis cache
    └── data/                   # Redis persistence files
```

## Quick Start

1. **Clone and navigate to directory**:
   ```bash
   cd /path/to/gitlab
   ```

2. **Start services**:
   ```bash
   docker-compose up -d
   ```

3. **Monitor startup** (GitLab takes 3-5 minutes to fully start):
   ```bash
   docker-compose logs -f gitlab-server
   ```

4. **Check service health**:
   ```bash
   docker-compose ps
   ```

5. **Access GitLab**:
   - URL: http://gitlab.hungngquang.xyz:2424
   - SSH: gitlab.hungngquang.xyz:2224
   - Default user: root
   - Password: Xitrumcute15030410@@

## Configuration Details

### Database Configuration
- **PostgreSQL 16**: External database with optimized settings
- **Database**: gitlab
- **User**: gitlab
- **Encoding**: UTF-8 with C locale for performance
- **Health Checks**: 10s intervals with 15s start period

### Cache Configuration
- **Redis 7**: External cache with persistence
- **Memory Limit**: 256MB with LRU eviction policy
- **Persistence**: AOF enabled for data durability
- **Health Checks**: 10s intervals with 10s start period

### GitLab Application Settings
- **Puma Workers**: 1 worker with 1-4 threads (optimized for startup)
- **Memory Settings**: 
  - PostgreSQL: 128MB shared buffers, 512MB cache
  - Redis: 128MB max memory
- **Disabled Services**: KAS, usage ping, Sentry, email (for faster startup)
- **Auto Migration**: Enabled for seamless updates

### Network Configuration
- **HTTP Port**: 2424 (mapped from container port 80)
- **SSH Port**: 2224 (mapped from container port 22)
- **Internal Network**: gitlab-network (bridge driver)
- **Database Port**: 5432 (exposed for administration)
- **Redis Port**: 6379 (exposed for monitoring)

### Performance Optimizations
- **Startup Time**: Optimized for 30-50% faster startup
- **Health Checks**: Fast intervals for quick failure detection
- **Memory Usage**: Reduced memory footprint for smaller servers
- **Service Dependencies**: PostgreSQL and Redis must be healthy before GitLab starts

## Services

### GitLab Server
- **Image**: gitlab/gitlab-ce:latest
- **Container**: gitlab-server
- **Ports**: 2424 (HTTP), 2224 (SSH)
- **URL**: http://gitlab.hungngquang.xyz:2424
- **Health Check**: 30s intervals, 5-minute startup grace period

### PostgreSQL Database
- **Image**: postgres:16-alpine
- **Container**: gitlab-postgresql
- **Port**: 5432
- **Database**: gitlab
- **Health Check**: 10s intervals, 15s startup grace period

### Redis Cache
- **Image**: redis:7-alpine
- **Container**: gitlab-redis
- **Port**: 6379
- **Memory**: 256MB with persistence
- **Health Check**: 10s intervals, 10s startup grace period

## Maintenance

### Service Management
```bash
# View service status
docker-compose ps

# View logs
docker-compose logs -f [service-name]

# Restart services
docker-compose restart [service-name]

# Stop all services
docker-compose down

# Start all services
docker-compose up -d
```

### GitLab Administration
```bash
# Access GitLab console
docker-compose exec gitlab-server gitlab-rails console

# Reconfigure GitLab
docker-compose exec gitlab-server gitlab-ctl reconfigure

# Check GitLab status
docker-compose exec gitlab-server gitlab-ctl status

# Create backup
docker-compose exec gitlab-server gitlab-backup create
```

### Database Management
```bash
# Access PostgreSQL
docker-compose exec postgresql psql -U gitlab -d gitlab

# Database backup
docker-compose exec postgresql pg_dump -U gitlab gitlab > backup.sql

# View database size
docker-compose exec postgresql psql -U gitlab -d gitlab -c "SELECT pg_size_pretty(pg_database_size('gitlab'));"
```

### Cache Management
```bash
# Access Redis CLI
docker-compose exec redis redis-cli

# View cache info
docker-compose exec redis redis-cli info memory

# Clear cache (if needed)
docker-compose exec redis redis-cli flushall
```

## Updates

```bash
# Pull latest images
docker-compose pull

# Update services (with backup first!)
docker-compose down
docker-compose up -d

# Check health after update
docker-compose ps
docker logs gitlab-server --tail 50
```

## Backup Strategy

### GitLab Application Backup
```bash
# Create backup
docker-compose exec gitlab-server gitlab-backup create

# Backup with custom name
docker-compose exec gitlab-server gitlab-backup create BACKUP=backup_$(date +%Y%m%d_%H%M%S)

# List backups
docker-compose exec gitlab-server ls -la /var/opt/gitlab/backups/
```

### Database Backup
```bash
# PostgreSQL backup
docker-compose exec postgresql pg_dump -U gitlab gitlab > "gitlab_db_$(date +%Y%m%d_%H%M%S).sql"

# Automated backup script
echo "docker-compose exec postgresql pg_dump -U gitlab gitlab" | crontab -e
```

### Full System Backup
```bash
# Stop services
docker-compose down

# Backup data directories
tar -czf gitlab_full_backup_$(date +%Y%m%d_%H%M%S).tar.gz gitlab/ postgresql/ redis/

# Restart services
docker-compose up -d
```

## Monitoring

### Health Monitoring
```bash
# Check all service health
docker-compose ps

# Watch logs in real-time
docker-compose logs -f

# Monitor resource usage
docker stats gitlab-server gitlab-postgresql gitlab-redis
```

### Performance Monitoring
```bash
# GitLab metrics (if Prometheus enabled)
curl http://localhost:2424/-/metrics

# Database performance
docker-compose exec postgresql psql -U gitlab -d gitlab -c "SELECT * FROM pg_stat_activity;"

# Redis performance
docker-compose exec redis redis-cli info stats
```

## Troubleshooting

### Common Issues

1. **GitLab takes long to start**: Normal behavior, wait 3-5 minutes
   ```bash
   docker-compose logs -f gitlab-server
   ```

2. **Database connection issues**: Check PostgreSQL health
   ```bash
   docker-compose exec postgresql pg_isready -U gitlab
   ```

3. **Redis connection issues**: Check Redis connectivity
   ```bash
   docker-compose exec redis redis-cli ping
   ```

4. **Port conflicts**: Check if ports 2224, 2424, 5432, 6379 are available
   ```bash
   netstat -tuln | grep -E "(2224|2424|5432|6379)"
   ```

5. **Permission issues**: Check file ownership
   ```bash
   ls -la gitlab/ postgresql/ redis/
   ```

### Performance Issues

1. **High memory usage**: Monitor and adjust limits
   ```bash
   docker stats --no-stream
   ```

2. **Slow startup**: Check optimized settings are applied
   ```bash
   docker-compose exec gitlab-server gitlab-ctl status
   ```

3. **Database performance**: Monitor PostgreSQL
   ```bash
   docker-compose exec postgresql psql -U gitlab -d gitlab -c "SELECT * FROM pg_stat_database;"
   ```

### Service Recovery

```bash
# Restart individual services
docker-compose restart postgresql
docker-compose restart redis
docker-compose restart gitlab-server

# Force recreate containers
docker-compose down
docker-compose up -d --force-recreate

# Clean restart (removes containers)
docker-compose down --volumes
docker-compose up -d
```

## Security Notes

- **Change default password**: Root password is hardcoded in config
- **Database security**: PostgreSQL is exposed on port 5432
- **Redis security**: Redis is exposed on port 6379
- **Network isolation**: Services communicate through dedicated network
- **Regular updates**: Keep GitLab and database images updated
- **Backup encryption**: Encrypt backups before storing offsite



## Support

For issues specific to this setup:
- [GitLab Docker Documentation](https://docs.gitlab.com/omnibus/docker/)
- [PostgreSQL Docker Documentation](https://hub.docker.com/_/postgres)
- [Redis Docker Documentation](https://hub.docker.com/_/redis)
- [GitLab Troubleshooting Guide](https://docs.gitlab.com/ee/administration/troubleshooting/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)

## Configuration Summary

| Component | Version | Port | Health Check | Memory Limit |
|-----------|---------|------|--------------|--------------|
| GitLab CE | latest | 2424, 2224 | 30s intervals | Optimized |
| PostgreSQL | 16-alpine | 5432 | 10s intervals | 128MB shared |
| Redis | 7-alpine | 6379 | 10s intervals | 256MB max |

**Total recommended resources**: 2GB RAM, 1 CPU core, 10GB+ storage