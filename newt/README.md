# Newt

## Overview

Newt is a lightweight Docker container monitoring and management agent that
connects to the Pangolin monitoring system. It monitors container status,
resource usage, and provides remote management capabilities through a secure
connection.

---

## Configuration

Environment is provided through `.env` (loaded with `env_file: .env`). Below are
the variables from your `.env.example`:

| Variable               | Type   | Description                                   | Example                        | Required |
| ---------------------- | ------ | --------------------------------------------- | ------------------------------ | -------- |
| `DOCKER_SOCKET`        | Config | Path to Docker socket                         | `unix:///var/run/docker.sock`  | Yes      |
| `NEWT_ID`              | Config | Unique identifier for this Newt instance      | `server-01`                    | Yes      |
| `NEWT_SECRET`          | Config | Authentication secret for Pangolin connection | `your-secret-key`              | Yes      |
| `PANGOLIN_ENDPOINT`    | Config | Pangolin server endpoint URL                  | `https://pangolin.example.com` | Yes      |
| `SERVICE_LIMIT_CPU`    | Config | CPU limit for the container                   | `0.25`                         | Yes      |
| `SERVICE_LIMIT_MEMORY` | Config | Memory limit for the container                | `128M`                         | Yes      |

**Typical `.env`**

```dotenv
# Newt configuration
DOCKER_SOCKET=unix:///var/run/docker.sock
NEWT_ID=server-01
NEWT_SECRET=your-very-secure-secret-key
PANGOLIN_ENDPOINT=https://pangolin.example.com

# Service
SERVICE_LIMIT_CPU=0.25
SERVICE_LIMIT_MEMORY=128M
```

**Volume mapping:**
- `/var/run/docker.sock` → `/var/run/docker.sock:ro` (Docker API access -
  read-only)

---

## Security Considerations

- **Docker socket access**: Service has read-only access to Docker daemon -
  monitor for any privilege escalation attempts
- **Authentication secret**: Use a strong, unique `NEWT_SECRET` and rotate
  regularly
- **Network security**: Ensure `PANGOLIN_ENDPOINT` uses HTTPS for encrypted
  communication
- **Instance identification**: Use descriptive but not sensitive information
  for `NEWT_ID`
- **Container isolation**: Runs with `no-new-privileges` security option
- **Minimal permissions**: Docker socket mounted read-only to prevent container
  manipulation

---

## Troubleshooting

**Service won't start:**
- Verify Docker socket is accessible: `ls -la /var/run/docker.sock`
- Check container logs: `docker-compose logs newt`
- Ensure all required environment variables are set

**Can't connect to Pangolin:**
- Verify `PANGOLIN_ENDPOINT` URL is correct and accessible
- Test network connectivity: `curl -I https://pangolin.example.com`
- Check firewall and DNS resolution
- Validate `NEWT_SECRET` matches Pangolin configuration

**Authentication failures:**
- Confirm `NEWT_ID` is registered in Pangolin system
- Verify `NEWT_SECRET` matches the one configured in Pangolin
- Check for any special characters or encoding issues in credentials

**Docker monitoring issues:**
- Confirm Docker socket permissions: `docker ps` should work from host
- Check if Docker daemon is running and responsive
- Monitor logs for Docker API errors
- Verify container can read Docker socket: `docker exec newt ls -la /var/run/docker.sock`

**Performance issues:**
- Increase `SERVICE_LIMIT_CPU` if monitoring many containers
- Adjust `SERVICE_LIMIT_MEMORY` based on actual usage
- Check resource usage: `docker stats newt`
- Monitor network latency to Pangolin endpoint

---

## Links

- [Newt Docker Hub](https://hub.docker.com/r/fosrl/newt)
- [FOSR Labs](https://fosrlabs.com/)
