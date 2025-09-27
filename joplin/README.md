# Joplin Server (11notes)

## Overview

Joplin Server is the synchronization server for Joplin, an open-source
note-taking and to-do application. This setup uses hardened 11notes images with
enhanced security features including read-only containers, tmpfs mounts, and
Docker managed volumes for reliable data storage and automatic PostgreSQL
backups.

---

## Configuration

Environment is provided through `.env` (loaded with `env_file: .env`). Below are
the variables from your `.env.example`:

| Variable                   | Type   | Description                               | Example                      | Required |
| -------------------------- | ------ | ----------------------------------------- | ---------------------------- | -------- |
| `APP_BASE_URL`             | Config | Base URL for Joplin Server                | `https://joplin.example.com` | Yes      |
| `POSTGRES_BACKUP_SCHEDULE` | Config | Cron schedule for automatic backups       | `0 2 * * *`                  | Yes      |
| `POSTGRES_PASSWORD`        | Config | PostgreSQL database password              | `secure_password123`         | Yes      |
| `SERVICE_FOLDER_BACKUP`    | Volume | Host path for PostgreSQL backups          | `/srv/joplin/backups`        | No       |
| `SERVICE_LIMIT_CPU`        | Config | CPU limit for Joplin container            | `1.0`                        | No       |
| `SERVICE_LIMIT_CPU_DB`     | Config | CPU limit for database container          | `0.5`                        | No       |
| `SERVICE_LIMIT_MEMORY`     | Config | Memory limit for Joplin container         | `1G`                         | No       |
| `SERVICE_LIMIT_MEMORY_DB`  | Config | Memory limit for database container       | `512M`                       | No       |
| `SERVICE_PORT`             | Port   | Host port for Joplin Server web interface | `22300`                      | No       |
| `TZ`                       | Config | Container timezone                        | `Europe/Madrid`              | Yes      |

**Typical `.env`**

```dotenv
# Core configuration
APP_BASE_URL=https://joplin.example.com

# Database
POSTGRES_BACKUP_SCHEDULE=0 2 * * *
POSTGRES_PASSWORD=your-very-secure-database-password

# Service
SERVICE_FOLDER_BACKUP=/srv/joplin/backups
SERVICE_LIMIT_CPU=1.0
SERVICE_LIMIT_MEMORY=1G
SERVICE_LIMIT_CPU_DB=0.5
SERVICE_LIMIT_MEMORY_DB=512M
SERVICE_PORT=22300

# Other
TZ=Europe/Madrid
```

**Port mapping:**
- `SERVICE_PORT` → Container port 22300 (Joplin Server API and web interface)

**Volume mapping:**
- `SERVICE_FOLDER_BACKUP` → `/postgres/backup` (Database backup storage)
- `joplin.etc` → `/joplin/etc` (Joplin configuration - Docker managed)
- `joplin.var` → `/joplin/var` (Joplin data - Docker managed)
- `postgres.etc` → `/postgres/etc` (PostgreSQL configuration - Docker managed)
- `postgres.var` → `/postgres/var` (PostgreSQL data - Docker managed)

**Security features:**
- Read-only containers with tmpfs for runtime directories
- Non-privileged execution with user 1000:1000
- Docker managed volumes for data persistence

---

## Security Considerations

- **Hardened containers**: Read-only filesystem with tmpfs for runtime data
- **Non-privileged execution**: Containers run as user 1000:1000
- **Database password**: Use strong, unique password for `POSTGRES_PASSWORD`
- **Base URL**: Configure correct `APP_BASE_URL` for proper CORS and redirects
- **Network isolation**: Database not exposed to host network
- **Automatic backups**: Scheduled backups with `POSTGRES_BACKUP_SCHEDULE`
- **Docker managed volumes**: Enhanced data persistence and security
- **No new privileges**: Security option prevents privilege escalation
- **Reverse proxy**: Use HTTPS reverse proxy for production deployment

---

## Troubleshooting

**Services won't start:**
- Check if port `SERVICE_PORT` is available: `netstat -tulpn | grep :22300`
- Verify backup directory exists and is writable
- Allow extra time for read-only container initialization

**Database dependency issues:**
- Note: Compose file references `db2` but service is named `db` - this will
  cause startup failure
- Check database container health status
- Ensure database initialization completes before Joplin starts
- Review database logs: `docker-compose logs db`

**Read-only filesystem errors:**
- Containers use read-only mode - check tmpfs mounts are working
- Verify tmpfs directories have correct uid/gid (1000:1000)
- Check if application tries to write to read-only areas

**Docker managed volumes issues:**
- List volumes: `docker volume ls | grep joplin`
- Inspect volume: `docker volume inspect joplin_joplin.var`
- Check volume permissions and data integrity

**Backup failures:**
- Verify `POSTGRES_BACKUP_SCHEDULE` cron format
- Check backup directory permissions and disk space
- Review database logs for backup errors
- Test manual backup to isolate issues

**Performance with security features:**
- Read-only containers may have slight performance impact
- Increase resource limits if needed for secure containers
- Monitor tmpfs usage for runtime directories
- Check resource usage: `docker stats joplin db`

**Client sync issues:**
- Verify `APP_BASE_URL` is accessible from client devices
- Check user credentials and account status
- Ensure server is reachable over network
- Test API endpoints manually

---

## Links

- [11notes PostgreSQL](https://hub.docker.com/r/11notes/postgres)
- [11notes Joplin](https://hub.docker.com/r/11notes/joplin)
- [Joplin Official Site](https://joplinapp.org/)
- [Joplin Server Documentation](https://joplinapp.org/help/apps/sync/#joplin-server)
