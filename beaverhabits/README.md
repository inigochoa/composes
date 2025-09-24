# BeaverHabits

## Overview

BeaverHabits is a self-hosted habit tracking application that helps you build
and maintain daily routines. It features a clean, minimalist interface for
tracking your habits, visualizing progress with streaks and statistics, and
staying motivated on your personal development journey.

---

## Configuration

Environment is provided through `.env` (loaded with `env_file: .env`). Below are
the variables from your `.env.example`:

| Variable               | Type   | Description                              | Example                  | Required |
| ---------------------- | ------ | ---------------------------------------- | ------------------------ | -------- |
| `SERVICE_FOLDER_DATA`  | Volume | Host path for BeaverHabits data storage  | `/srv/beaverhabits/data` | Yes      |
| `SERVICE_LIMIT_CPU`    | Config | CPU limit for the container              | `0.25`                   | Yes      |
| `SERVICE_LIMIT_MEMORY` | Config | Memory limit for the container           | `128M`                   | Yes      |
| `SERVICE_PORT`         | Port   | Host port for BeaverHabits web interface | `8080`                   | Yes      |

**Typical `.env`**

```dotenv
# Service
SERVICE_FOLDER_DATA=/srv/beaverhabits/data
SERVICE_LIMIT_CPU=0.25
SERVICE_LIMIT_MEMORY=128M
SERVICE_PORT=8080
```

**Port mapping:**
- `SERVICE_PORT` → Container port 8080 (Web interface)

**Volume mapping:**
- `SERVICE_FOLDER_DATA` → `/app/.user/` (User data, habits, progress tracking)

---

## Security Considerations

- **File permissions**: Container runs as user 1000:1000 for security
- **Data privacy**: Habit data contains personal behavioral information
- **Authentication**: Configure proper access control within the application
- **Reverse proxy**: Use HTTPS reverse proxy for secure external access
- **Regular updates**: Keep container updated for security patches
- **Backup privacy**: Personal habit data should be securely backed up

---

## Troubleshooting

**Service won't start:**
- Check if port `SERVICE_PORT` is available: `netstat -tulpn | grep :8080`
- Verify `SERVICE_FOLDER_DATA` directory exists and is accessible
- Check container logs: `docker-compose logs beaverhabits`

**Permission errors:**
- Ensure data directory is owned by user 1000:1000
- Check directory permissions: `ls -la ${SERVICE_FOLDER_DATA}`
- Verify container can write to data directory

**Can't access web interface:**
- Confirm service is running: `docker-compose ps beaverhabits`
- Test connectivity: `curl http://localhost:${SERVICE_PORT}`
- Check for application startup errors in logs

**Data not persisting:**
- Verify volume mapping is correct in docker-compose
- Check if data directory is properly mounted
- Ensure sufficient disk space in data directory
- Confirm container user has write permissions

**Habit tracking issues:**
- Check for JavaScript errors in browser console
- Verify data is being saved correctly to storage
- Test with simple habit entries first
- Review application logs for specific errors

**Performance issues:**
- Increase `SERVICE_LIMIT_CPU` and `SERVICE_LIMIT_MEMORY` if needed
- Check resource usage: `docker stats beaverhabits`
- Monitor disk space usage in data directory
- Clear browser cache if interface is slow

**Database/storage errors:**
- Check available disk space in `SERVICE_FOLDER_DATA`
- Verify data files aren't corrupted
- Ensure proper file permissions on storage files
- Consider backup restore if data integrity issues occur

---

## Links

- [BeaverHabits Docker Hub](https://hub.docker.com/r/daya0576/beaverhabits)
- [BeaverHabits GitHub](https://github.com/daya0576/beaverhabits)
