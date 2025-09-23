# Beszel Agent

## Overview

Beszel Agent is the monitoring component that collects system metrics and Docker
container statistics for the Beszel monitoring system. It runs on each server
you want to monitor and sends data to a Beszel hub. This standalone agent setup
is perfect for monitoring remote servers or when running the hub separately.

---

## Configuration

Environment is provided through `.env` (loaded with `env_file: .env`). Below are
the variables from your `.env.example`:

| Variable                | Type   | Description                              | Example                      | Required |
| ----------------------- | ------ | ---------------------------------------- | ---------------------------- | -------- |
| `HUB_URL`               | Config | URL of the Beszel hub server             | `https://beszel.example.com` | Yes      |
| `KEY`                   | Config | Authentication key for hub communication | `your-secure-key-here`       | Yes      |
| `LISTEN`                | Config | Agent listen address and port            | `0.0.0.0:45876`              | Yes      |
| `TOKEN`                 | Config | Authentication token for API access      | `your-secure-token-here`     | Yes      |
| `SERVICE_FOLDER_DATA`   | Volume | Host path for Beszel agent data storage  | `/srv/beszel/agent`          | Yes      |
| `SERVICE_FOLDER_SOCKET` | Volume | Host path for socket communication       | `/srv/beszel/socket`         | Yes      |
| `SERVICE_LIMIT_CPU`     | Config | CPU limit for the container              | `0.25`                       | Yes      |
| `SERVICE_LIMIT_MEMORY`  | Config | Memory limit for the container           | `128M`                       | Yes      |

**Typical `.env`**

```dotenv
# Beszel configuration
HUB_URL=https://beszel.example.com
KEY=your-very-secure-authentication-key
LISTEN=0.0.0.0:45876
TOKEN=your-secure-api-token

# Service
SERVICE_FOLDER_DATA=/srv/beszel/agent
SERVICE_FOLDER_SOCKET=/srv/beszel/socket
SERVICE_LIMIT_CPU=0.25
SERVICE_LIMIT_MEMORY=128M
```

**Volume mapping:**
- `SERVICE_FOLDER_DATA` → `/var/lib/beszel-agent` (Agent configuration and local
  data)
- `SERVICE_FOLDER_SOCKET` → `/beszel_socket` (Socket for local communication)
- `/var/run/docker.sock` → `/var/run/docker.sock:ro` (Docker monitoring -
  read-only)

**Network:** Uses host network mode for comprehensive system monitoring

---

## Security Considerations

- **Authentication credentials**: Use strong, unique values for `KEY` and `TOKEN`
- **Network exposure**: Agent uses host network mode - monitor network security
- **Docker socket access**: Agent has read-only access to Docker daemon
- **Hub communication**: Ensure `HUB_URL` uses HTTPS for secure data transmission
- **System access**: Agent collects sensitive system information
- **Firewall rules**: Configure appropriate rules if `LISTEN` port is exposed
- **Regular updates**: Keep container updated for security patches

---

## Troubleshooting

**Service won't start:**
- Verify all volume directories exist and are writable
- Check container logs: `docker-compose logs beszel_agent`
- Ensure host network mode is supported

**Can't connect to hub:**
- Verify `HUB_URL` is accessible from this server
- Check `KEY` and `TOKEN` match hub configuration
- Test network connectivity: `curl -I ${HUB_URL}`
- Review connection logs for specific errors

**No metrics being collected:**
- Confirm agent has proper system access in host network mode
- Check Docker socket permissions: `ls -la /var/run/docker.sock`
- Verify Docker daemon is running: `docker ps`
- Monitor logs for collection errors

**Docker monitoring not working:**
- Ensure Docker socket is accessible: `docker-compose exec beszel_agent ls -la /var/run/docker.sock`
- Check Docker daemon status on host
- Verify read-only mount is working correctly
- Test Docker API access from container

**High resource usage:**
- Adjust `SERVICE_LIMIT_CPU` and `SERVICE_LIMIT_MEMORY` as needed
- Check collection frequency settings
- Monitor for memory leaks in logs
- Review resource usage: `docker stats beszel_agent`

**Network connectivity issues:**
- Verify `LISTEN` address and port configuration
- Check firewall settings if agent needs to accept connections
- Test network connectivity between agent and hub
- Review network configuration in host mode

**Authentication failures:**
- Confirm credentials match exactly with hub configuration
- Check for special characters or encoding issues
- Verify token hasn't expired or been revoked
- Test authentication manually if possible

---

## Links

- [Beszel Agent Docker Hub](https://hub.docker.com/r/henrygd/beszel-agent)
- [Beszel GitHub](https://github.com/henrygd/beszel)
