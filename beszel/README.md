# Beszel

## Overview

Beszel is a lightweight server monitoring solution with a web-based dashboard
and agent system. It provides real-time system metrics, Docker container
monitoring, and alerting capabilities. The setup includes the main hub (web
interface).

---

## Configuration

Environment is provided through `.env` (loaded with `env_file: .env`). Below are
the variables from your `.env.example`:

| Variable                | Type   | Description                               | Example              | Required |
| ----------------------- | ------ | ----------------------------------------- | -------------------- | -------- |
| `SERVICE_FOLDER_DATA`   | Volume | Host path for Beszel hub data storage     | `/srv/beszel/hub`    | Yes      |
| `SERVICE_FOLDER_SOCKET` | Volume | Host path for shared socket communication | `/srv/beszel/socket` | Yes      |
| `SERVICE_LIMIT_CPU`     | Config | CPU limit for hub container               | `0.5`                | Yes      |
| `SERVICE_LIMIT_MEMORY`  | Config | Memory limit for hub container            | `256M`               | Yes      |
| `SERVICE_PORT`          | Port   | Host port for Beszel web interface        | `8090`               | Yes      |
| `TZ`                    | Config | Container timezone                        | `Europe/Madrid`      | Yes      |

**Typical `.env`**

```dotenv
# Service
SERVICE_FOLDER_DATA=/srv/beszel/hub
SERVICE_FOLDER_SOCKET=/srv/beszel/socket
SERVICE_LIMIT_CPU=0.5
SERVICE_LIMIT_MEMORY=256M
SERVICE_PORT=8090

# Other
TZ=Europe/Madrid
```

**Port mapping:**
- `SERVICE_PORT` → Container port 8090 (Beszel hub web interface)

**Volume mapping:**
- `SERVICE_FOLDER_DATA` → `/beszel_data` (Hub configuration and database)
- `SERVICE_FOLDER_SOCKET` → `/beszel_socket` (Shared socket for hub-agent
  communication)
- `/var/run/docker.sock` → `/var/run/docker.sock:ro` (Docker monitoring -
  read-only)

---

## Security Considerations

- **Hub access**: Secure web interface with proper authentication
- **Socket permissions**: Ensure shared socket directory has proper permissions
- **Regular updates**: Keep containers updated for security patches
- **Monitoring data**: System metrics may reveal sensitive infrastructure
  information

---

## Troubleshooting

**Services won't start:**
- Check if port `SERVICE_PORT` is available: `netstat -tulpn | grep :8090`
- Verify all volume directories exist and are writable
- Check container logs: `docker-compose logs beszel beszel_agent`

**Agent can't connect to hub:**
- Verify `HUB_URL` is accessible from agent container
- Check `KEY` matches between hub and agent configuration
- Ensure hub is running and accepting connections
- Review agent logs: `docker-compose logs beszel_agent`

**No system metrics displayed:**
- Confirm agent is running with host network mode
- Check Docker socket permissions and accessibility
- Verify agent has proper system access for metrics collection
- Monitor agent logs for collection errors

**Web interface issues:**
- Test hub connectivity: `curl http://localhost:${SERVICE_PORT}`
- Check for authentication problems with hub
- Verify browser cache isn't causing display issues
- Review hub logs for specific errors

**Docker monitoring not working:**
- Ensure Docker socket is mounted read-only
- Verify Docker daemon is running and responsive
- Check agent permissions to access Docker socket
- Test Docker access: `docker-compose exec beszel_agent ls -la /var/run/docker.sock`

**Performance issues:**
- Increase resource limits for hub or agent as needed
- Monitor collection frequency and adjust if necessary
- Check disk space for metrics storage
- Review resource usage: `docker stats beszel beszel_agent`

**Socket communication problems:**
- Verify shared socket directory exists and is accessible
- Check permissions on socket directory
- Ensure both containers can access shared volume
- Review logs for socket connection errors

---

## Links

- [Beszel Docker Hub](https://hub.docker.com/r/henrygd/beszel)
- [Beszel GitHub](https://github.com/henrygd/beszel)
