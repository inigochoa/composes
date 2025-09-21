# Diun (Docker Image Update Notifier)

## Overview

This stack deploys **Diun** (by crazy-max), a lightweight service that watches
your Docker images and **notifies** you when new tags are published. It does
**not** auto-update containers; it only alerts you so you can plan safe
upgrades.

---

## Configuration

Environment is provided through `.env` (loaded with `env_file: .env`). Below are
the variables from your `.env.example`:

| Variable                | Type   | Description                                             | Example                       | Required |
| ----------------------- | ------ | ------------------------------------------------------- | ----------------------------- | -------- |
| `DIUN_PROVIDERS_DOCKER` | Config | Enables Docker provider to scan local images/containers | `true`                        | Yes      |
| `DIUN_WATCH_SCHEDULE`   | Config | Cron expression defining scan frequency                 | `0 */6 * * *`                 | Yes      |
| `SERVICE_FOLDER_DATA`   | Volume | Host path for persistent Diun data                      | `/srv/diun/data`              | Yes      |
| `SERVICE_LIMIT_CPU`     | Config | CPU limit for the container                             | `0.50`                        | Yes      |
| `SERVICE_LIMIT_MEMORY`  | Config | Memory limit for the container                          | `256M`                        | Yes      |
| `TZ`                    | Config | Container timezone                                      | `Europe/Madrid`               | Yes      |

**Typical `.env`**

```dotenv
# Diun behavior
DIUN_PROVIDERS_DOCKER=true
DIUN_WATCH_SCHEDULE=0 */6 * * *

# Service
SERVICE_FOLDER_DATA=/srv/diun/data
SERVICE_LIMIT_CPU=0.50
SERVICE_LIMIT_MEMORY=256M

# Other
TZ=Europe/Madrid
```

**Volume mapping:**
- `SERVICE_FOLDER_DATA` → `/data` (Persistent Diun data, bolt DB, cache, state)
- `/var/run/docker.sock` → `/var/run/docker.sock` (Docker API access for
  discovery)

**Usage notes:**
- Cron format is `min hour day month weekday` (UTC unless TZ is set)
- Add `diun.enable=true` labels to containers for fine-grained control
- Notifications require additional `DIUN_NOTIF_*` environment variables

---

## Security Considerations

- **Docker socket access**: Service has read access to Docker daemon - ensure
  container security
- **File permissions**: Ensure `SERVICE_FOLDER_DATA` directory is writable by
  container
- **Network isolation**: Service only needs access to Docker socket, no external
  network required for basic operation
- **Notification credentials**: Store notification service credentials securely
  if using external services

---

## Troubleshooting

**Service won't start:**
- Verify `SERVICE_FOLDER_DATA` path exists and is writable
- Check Docker socket permissions: `ls -la /var/run/docker.sock`
- Review container logs: `docker-compose logs diun`

**No images being discovered:**
- Confirm `DIUN_PROVIDERS_DOCKER=true` is set
- Check if containers have proper labels for discovery
- Verify Docker socket is accessible from container

**Scheduled scans not running:**
- Validate cron expression format in `DIUN_WATCH_SCHEDULE`
- Check timezone settings with `TZ` variable
- Monitor logs during expected scan times

**Performance issues:**
- Adjust `SERVICE_LIMIT_CPU` and `SERVICE_LIMIT_MEMORY` if needed
- Check resource usage: `docker stats diun`
- Consider reducing scan frequency if system load is high

---

## Links

- [Diun GitHub](https://github.com/crazy-max/diun)
- [Documentation](https://crazymax.dev/diun/)
- [Docker Hub](https://hub.docker.com/r/crazymax/diun)
