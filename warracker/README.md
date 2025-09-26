# Warracker

## Overview

Warracker is an open source, self-hostable warranty tracker to monitor
expirations, store receipts, files. You own the data, your rules!

---

## Configuration

Environment is provided through `.env` (loaded with `env_file: .env`). Below are
the variables from your `.env.example`:

| Variable                    | Type   | Description                             | Example                         | Required |
| --------------------------- | ------ | --------------------------------------- | ------------------------------- | -------- |
| `APP_BASE_URL`              | Config | Base URL for Warracker application      | `https://warracker.example.com` | Yes      |
| `FRONTEND_URL`              | Config | Frontend URL for CORS configuration     | `https://warracker.example.com` | Yes      |
| `SECRET_KEY`                | Config | Secret key for JWT token generation     | `your-very-secure-secret`       | Yes      |
| `DB_HOST`                   | Config | Database host (container name)          | `db`                            | Yes      |
| `DB_NAME`                   | Config | Database name                           | `postgres`                      | Yes      |
| `DB_PASSWORD`               | Config | Database user password                  | `secure_password123`            | Yes      |
| `DB_USER`                   | Config | Database username                       | `postgres`                      | Yes      |
| `POSTGRES_BACKUP_SCHEDULE`  | Config | Cron schedule for automatic backups     | `0 2 * * *`                     | Yes      |
| `POSTGRES_HOST`             | Config | PostgreSQL host (container name)        | `db`                            | Yes      |
| `POSTGRES_PASSWORD`         | Config | PostgreSQL password                     | `secure_password123`            | Yes      |
| `SERVICE_FOLDER_BACKUP`     | Volume | Host path for database backups          | `/srv/warracker/backups`        | Yes      |
| `SERVICE_FOLDER_UPLOADS`    | Volume | Host path for uploaded files and assets | `/srv/warracker/uploads`        | Yes      |
| `SERVICE_LIMIT_CPU`         | Config | CPU limit for main container            | `0.5`                           | Yes      |
| `SERVICE_LIMIT_CPU_DB`      | Config | CPU limit for database container        | `0.1`                           | Yes      |
| `SERVICE_LIMIT_MEMORY`      | Config | Memory limit for main container         | `512M`                          | Yes      |
| `SERVICE_LIMIT_MEMORY_DB`   | Config | Memory limit for database container     | `128M`                          | Yes      |
| `SERVICE_PORT`              | Port   | Host port for Warracker web interface   | `3000`                          | Yes      |
| `TZ`                        | Config | Container timezone                      | `Europe/Madrid`                 | Yes      |

**Typical `.env`**

```dotenv
# Core configuration
APP_BASE_URL=https://warracker.example.com
FRONTEND_URL=https://warracker.example.com
SECRET_KEY=your-very-secure-secret-key-here

# Database
DB_HOST=db
DB_NAME=warracker
DB_PASSWORD=your-db-password-here
DB_USER=warracker
POSTGRES_BACKUP_SCHEDULE=0 2 * * *
POSTGRES_HOST=db
POSTGRES_PASSWORD=your-db-password-here

# Service
SERVICE_FOLDER_BACKUP=/srv/warracker/backups
SERVICE_FOLDER_UPLOADS=/srv/warracker/uploads
SERVICE_LIMIT_CPU=0.5
SERVICE_LIMIT_CPU_DB=0.1
SERVICE_LIMIT_MEMORY=512M
SERVICE_LIMIT_MEMORY_DB=128M
SERVICE_PORT=3000

# Other
TZ=Europe/Madrid
```

**Port mapping:**
- `SERVICE_PORT` → Container port 80 (Web interface)

**Volume mapping:**
- `SERVICE_FOLDER_BACKUP` → `/backups` (Database backup storage)
- `SERVICE_FOLDER_UPLOADS` → `/data/uploads` (User uploaded files and assets)
- `joplin.etc` → `/joplin/etc` (Joplin configuration - Docker managed)
- `joplin.var` → `/joplin/var` (Joplin data - Docker managed)

---

## Security Considerations

- **Secret key**: Use strong, unique `SECRET_KEY` for JWT token security
- **Database passwords**: Use different strong passwords for user and admin
  accounts
- **URL configuration**: Set correct `APP_BASE_URL` and `FRONTEND_URL` for CORS
  protection
- **File uploads**: Limit `MAX_UPLOAD_MB` to prevent abuse
- **Network isolation**: Database not exposed to host network
- **JWT expiration**: Adjust `JWT_EXPIRATION_HOURS` based on security
  requirements
- **Admin access**: Secure database admin credentials separately
- **Reverse proxy**: Use HTTPS reverse proxy for production deployment
- **Data privacy**: Habit tracking data contains personal behavioral information
- **Automatic backups**: Scheduled backups with `POSTGRES_BACKUP_SCHEDULE`
- **Docker managed volumes**: Enhanced data persistence and security

---

## Troubleshooting

**Services won't start:**
- Check if port `SERVICE_PORT` is available: `netstat -tulpn | grep :3000`
- Verify all volume directories exist and are writable
- Wait for database health check to pass before Warracker starts

**Database connection issues:**
- Verify database credentials match between `DB_*` and `POSTGRES_*` variables
- Check database container health: `docker-compose ps db`
- Ensure database initialization completed successfully
- Review database logs: `docker-compose logs db`

**Application won't start:**
- Confirm database is healthy and accepting connections
- Check `SECRET_KEY` is properly set
- Verify URL configurations are correct
- Monitor application logs: `docker-compose logs warracker`

**File upload issues:**
- Check `MAX_UPLOAD_MB` and `NGINX_MAX_BODY_SIZE_VALUE` settings
- Verify uploads directory permissions and available space
- Ensure upload path is correctly mapped
- Test with smaller files to isolate size issues

**Authentication problems:**
- Verify `JWT_EXPIRATION_HOURS` setting
- Check `SECRET_KEY` hasn't changed (invalidates existing tokens)
- Confirm `FRONTEND_URL` matches client access URL
- Clear browser cache and cookies

**Performance issues:**
- Enable `WARRACKER_MEMORY_MODE` for memory optimization
- Increase resource limits for both containers
- Monitor database performance and size
- Check resource usage: `docker stats warracker db`

**Backup failures:**
- Verify `POSTGRES_BACKUP_SCHEDULE` cron format
- Check backup directory permissions and disk space
- Review database logs for backup errors
- Test manual backup to isolate issues

---

## Links

- [Warracker GitHub](https://github.com/Sassanix/warracker)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
