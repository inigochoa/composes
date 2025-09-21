# Joplin Server

## Overview

Joplin Server is the synchronization server for Joplin, an open-source
note-taking and to-do application. This setup provides a self-hosted solution
for syncing notes, notebooks, and attachments across all your Joplin clients
(desktop, mobile, and web). Includes PostgreSQL database for reliable data
storage.

---

## Configuration

Environment is provided through `.env` (loaded with `env_file: .env`). Below are
the variables from your `.env.example`:

| Variable                  | Type   | Description                               | Example                      | Required |
| ------------------------- | ------ | ----------------------------------------- | ---------------------------- | -------- |
| `APP_BASE_URL`            | Config | Base URL for Joplin Server                | `https://joplin.example.com` | Yes      |
| `DB_CLIENT`               | Config | Database client type                      | `pg`                         | Yes      |
| `POSTGRES_DATABASE`       | Config | PostgreSQL database name (alternative)    | `joplin`                     | Yes      |
| `POSTGRES_DB`             | Config | PostgreSQL database name                  | `joplin`                     | Yes      |
| `POSTGRES_HOST`           | Config | PostgreSQL host (container name)          | `db`                         | Yes      |
| `POSTGRES_PASSWORD`       | Config | PostgreSQL password                       | `secure_password123`         | Yes      |
| `POSTGRES_PORT`           | Config | PostgreSQL port                           | `5432`                       | Yes      |
| `POSTGRES_USER`           | Config | PostgreSQL username                       | `joplin`                     | Yes      |
| `SERVICE_FOLDER_BACKUP`   | Volume | Host path for database backups            | `/srv/joplin/backups`        | Yes      |
| `SERVICE_FOLDER_DATA`     | Volume | Host path for PostgreSQL database data    | `/srv/joplin/postgres`       | Yes      |
| `SERVICE_LIMIT_CPU`       | Config | CPU limit for joplin container            | `0.5`                        | Yes      |
| `SERVICE_LIMIT_CPU_DB`    | Config | CPU limit for database container          | `0.5`                        | Yes      |
| `SERVICE_LIMIT_MEMORY`    | Config | Memory limit for joplin container         | `512M`                       | Yes      |
| `SERVICE_LIMIT_MEMORY_DB` | Config | Memory limit for database container       | `512M`                       | Yes      |
| `SERVICE_PORT`            | Port   | Host port for Joplin Server web interface | `22300`                      | Yes      |
| `TZ`                      | Config | Container timezone                        | `Europe/Madrid`              | Yes      |

**Typical `.env`**

```dotenv
# Core configuration
APP_BASE_URL=https://joplin.example.com
DB_CLIENT=pg

# Database
POSTGRES_DATABASE=joplin
POSTGRES_DB=joplin
POSTGRES_HOST=db
POSTGRES_PASSWORD=your-secure-password-here
POSTGRES_PORT=5432
POSTGRES_USER=joplin

# Service
SERVICE_FOLDER_DATA=/srv/joplin/postgres
SERVICE_FOLDER_BACKUP=/srv/joplin/backups
SERVICE_LIMIT_CPU=0.5
SERVICE_LIMIT_CPU_DB=0.5
SERVICE_LIMIT_MEMORY=512M
SERVICE_LIMIT_MEMORY_DB=512M
SERVICE_PORT=22300

# Other
TZ=Europe/Madrid
```

**Port mapping:**
- `SERVICE_PORT` → Container port 22300 (Joplin Server API and web interface)

**Volume mapping:**
- `SERVICE_FOLDER_DATA` → `/var/lib/postgresql/data` (PostgreSQL database files)
- `SERVICE_FOLDER_BACKUP` → `/backups` (Database backup storage)

---

## Security Considerations

- **Database password**: Use strong, unique password for `POSTGRES_PASSWORD`
- **Base URL**: Configure correct `APP_BASE_URL` for proper CORS and redirects
- **Network isolation**: Database not exposed to host network (internal only)
- **Authentication**: Joplin Server requires user accounts for access
- **Reverse proxy**: Use HTTPS reverse proxy for production deployment
- **Token duration**: Adjust `TOKEN_VALID_DURATION` based on security
  requirements
- **Database backups**: Regular backups essential for note preservation
- **File permissions**: Ensure backup directory is properly secured

---

## Troubleshooting

**Services won't start:**
- Check if port `SERVICE_PORT` is available: `netstat -tulpn | grep :22300`
- Verify all volume directories exist and are writable
- Check startup order: database must start before Joplin server

**Database connection issues:**
- Verify `POSTGRES_HOST` matches database container name
- Check database credentials match between containers
- Ensure database is fully initialized before Joplin starts
- Review database logs: `docker-compose logs db`

**Joplin Server not responding:**
- Confirm database connection is working
- Check `APP_BASE_URL` configuration
- Verify all required environment variables are set
- Monitor Joplin logs: `docker-compose logs joplin`

**Client sync issues:**
- Verify `APP_BASE_URL` is accessible from client devices
- Check user credentials and account status
- Ensure server is reachable over network
- Test API endpoints manually

**Performance issues:**
- Increase memory limits for both containers
- Monitor database performance and size
- Check disk I/O on data volumes
- Consider PostgreSQL tuning for large datasets
- Review resource usage: `docker stats joplin db`

**Backup/restore problems:**
- Verify PostgreSQL user has necessary permissions
- Check available disk space in backup directory
- Ensure proper file permissions on backup files
- Test backup integrity before relying on them

---

## Links

- [Joplin Official Site](https://joplinapp.org/)
- [Joplin Server Documentation](https://joplinapp.org/help/apps/sync/#joplin-server)
- [Joplin GitHub](https://github.com/laurent22/joplin)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
