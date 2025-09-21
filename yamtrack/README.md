# YamTrack

## Overview

YamTrack is a self-hosted media tracking application that helps you keep track
of your watched movies, TV shows, and other media. It provides a clean web
interface for managing your media library, tracking progress, and discovering
new content.

---

## Configuration

Environment is provided through `.env` (loaded with `env_file: .env`). Below are
the variables from your `.env.example`:

| Variable                     | Type   | Description                             | Example                    | Required |
| ---------------------------- | ------ | --------------------------------------- | -------------------------- | -------- |
| `SECRET`                     | Config | Secret key for application security     | `your-very-secure-secret`  | Yes      |
| `SERVICE_FOLDER_DATA`        | Volume | Host path for YamTrack database storage | `/srv/yamtrack/db`         | Yes      |
| `SERVICE_LIMIT_CPU`          | Config | CPU limit for YamTrack container        | `0.5`                      | Yes      |
| `SERVICE_LIMIT_CPU_REDIS`    | Config | CPU limit for Redis container           | `0.25`                     | Yes      |
| `SERVICE_LIMIT_MEMORY`       | Config | Memory limit for YamTrack container     | `256M`                     | Yes      |
| `SERVICE_LIMIT_MEMORY_REDIS` | Config | Memory limit for Redis container        | `128M`                     | Yes      |
| `SERVICE_PORT`               | Port   | Host port for YamTrack web interface    | `8000`                     | Yes      |
| `TZ`                         | Config | Container timezone                      | `Europe/Madrid`            | Yes      |

**Typical `.env`**

```dotenv
# Core configuration
SECRET=your-very-secure-secret-key-here

# Service
SERVICE_FOLDER_DATA=/srv/yamtrack/db
SERVICE_LIMIT_CPU=0.5
SERVICE_LIMIT_CPU_REDIS=0.25
SERVICE_LIMIT_MEMORY=256M
SERVICE_LIMIT_MEMORY_REDIS=128M
SERVICE_PORT=8000

# Other
TZ=Europe/Madrid
```

**Port mapping:**
- `SERVICE_PORT` → Container port 8000 (Web interface)

**Volume mapping:**
- `SERVICE_FOLDER_DATA` → `/yamtrack/db` (Application database)
- `redis_data` → `/data` (Redis cache data - Docker managed volume)

---

## Security Considerations

- **Secret key**: Use a strong, unique `SECRET` for application security and
  sessions
- **Network isolation**: Services communicate through isolated Docker network
- **Redis security**: Redis not exposed to host network (internal only)
- **Authentication**: Configure user authentication within YamTrack application
- **Reverse proxy**: Use HTTPS reverse proxy for secure external access
- **Data privacy**: Media tracking data contains personal viewing habits
- **Regular updates**: Keep containers updated for security patches

---

## Troubleshooting

**Services won't start:**
- Check if port `SERVICE_PORT` is available: `netstat -tulpn | grep :8000`
- Verify `SERVICE_FOLDER_DATA` directory exists and is writable
- Ensure startup order: Redis must start before YamTrack

**Can't access web interface:**
- Confirm both services are running: `docker-compose ps`
- Test connectivity: `curl http://localhost:${SERVICE_PORT}`
- Check application logs: `docker-compose logs yamtrack`

**Redis connection issues:**
- Verify Redis container is running and healthy
- Check network connectivity between containers
- Test Redis: `docker-compose exec redis redis-cli ping`
- Review Redis logs: `docker-compose logs redis`

**Database errors:**
- Check available disk space in `SERVICE_FOLDER_DATA`
- Verify directory permissions for database files
- Ensure database isn't corrupted
- Review application database logs

**Performance issues:**
- Increase `SERVICE_LIMIT_CPU` and `SERVICE_LIMIT_MEMORY` for main application
- Adjust Redis limits with `SERVICE_LIMIT_CPU_REDIS` and `SERVICE_LIMIT_MEMORY_REDIS`
- Monitor resource usage: `docker stats yamtrack redis`
- Check Redis memory usage and consider persistence settings

**Media lookup failures:**
- Verify internet connectivity from container
- Check external API rate limits or service availability
- Review application logs for API errors
- Ensure media database services are accessible

---

## Links

- [YamTrack GitHub](https://github.com/FuzzyGrim/yamtrack)
- [Redis Documentation](https://redis.io/documentation)
