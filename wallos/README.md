# Wallos

## Overview

Wallos is a self-hosted subscription tracking and expense management application
that helps you keep track of your recurring payments, subscriptions, and
financial commitments. It provides a clean web interface to monitor your
spending patterns and manage subscription renewals.

---

## Configuration

Environment is provided through `.env` (loaded with `env_file: .env`). Below are
the variables from your `.env.example`:

| Variable               | Type   | Description                                 | Example             | Required |
| ---------------------- | ------ | ------------------------------------------- | ------------------- | -------- |
| `SERVICE_FOLDER_DATA`  | Volume | Host path for Wallos database storage       | `/srv/wallos/data`  | Yes      |
| `SERVICE_FOLDER_LOGOS` | Volume | Host path for subscription logos and images | `/srv/wallos/logos` | Yes      |
| `SERVICE_LIMIT_CPU`    | Config | CPU limit for the container                 | `0.25`              | Yes      |
| `SERVICE_LIMIT_MEMORY` | Config | Memory limit for the container              | `128M`              | Yes      |
| `SERVICE_PORT`         | Port   | Host port for Wallos web interface          | `8080`              | Yes      |
| `TZ`                   | Config | Container timezone                          | `Europe/Madrid`     | Yes      |

**Typical `.env`**

```dotenv
# Service
SERVICE_FOLDER_DATA=/srv/wallos/data
SERVICE_FOLDER_LOGOS=/srv/wallos/logos
SERVICE_LIMIT_CPU=0.25
SERVICE_LIMIT_MEMORY=128M
SERVICE_PORT=8080

# Other
TZ=Europe/Madrid
```

**Port mapping:**
- `SERVICE_PORT` → Container port 80 (Web interface)

**Volume mapping:**
- `SERVICE_FOLDER_DATA` → `/var/www/html/db` (Database and application data)
- `SERVICE_FOLDER_LOGOS` → `/var/www/html/images/uploads/logos` (Subscription
  logos and brand images)

---

## Security Considerations

- **Authentication**: Set up strong passwords for Wallos user accounts
- **Financial data**: Contains sensitive subscription and spending information
- **Reverse proxy**: Use HTTPS reverse proxy for secure access over internet
- **File permissions**: Ensure volume directories have appropriate permissions
- **Data privacy**: Subscription data reveals personal spending habits
- **Logo uploads**: Monitor uploaded image files for security
- **Regular updates**: Keep container updated for security patches
- **Backup encryption**: Consider encrypting backups containing financial data

---

## Troubleshooting

**Service won't start:**
- Check if port `SERVICE_PORT` is available: `netstat -tulpn | grep :8080`
- Verify all volume directories exist and are writable
- Check container logs: `docker-compose logs wallos`

**Can't access web interface:**
- Confirm service is running: `docker-compose ps wallos`
- Test connectivity: `curl http://localhost:${SERVICE_PORT}`
- Check for web server configuration errors in logs

**Database errors:**
- Check available disk space in `SERVICE_FOLDER_DATA`
- Verify directory permissions for database files
- Ensure database files aren't corrupted
- Review application logs for specific database errors

**Logo upload issues:**
- Check available disk space in `SERVICE_FOLDER_LOGOS`
- Verify directory permissions: `ls -la ${SERVICE_FOLDER_LOGOS}`
- Ensure proper file permissions (www-data ownership)
- Test with smaller image files

**Data not persisting:**
- Confirm volume mappings are correct in docker-compose
- Check if directories are properly mounted
- Verify file permissions allow write access
- Ensure container isn't being recreated without volumes

**Performance issues:**
- Increase `SERVICE_LIMIT_CPU` and `SERVICE_LIMIT_MEMORY` if needed
- Check resource usage: `docker stats wallos`
- Monitor database size and consider cleanup of old data
- Optimize logo image sizes to reduce storage usage

**Subscription tracking problems:**
- Verify timezone (`TZ`) is correctly set for accurate dates
- Check for date calculation errors in logs
- Ensure subscription data is properly formatted
- Test with simple subscription entries first

---

## Links

- [Wallos Docker Hub](https://hub.docker.com/r/bellamy/wallos)
- [Wallos GitHub](https://github.com/ellite/Wallos)
