# Wallabag

## Overview

Wallabag is a self-hosted read-it-later application that allows you to save web
articles for offline reading. It extracts article content, removes ads and
distractions, and provides a clean reading experience across all your devices.
Think of it as your personal Pocket or Instapaper alternative.

---

## Configuration

Environment is provided through `.env` (loaded with `env_file: .env`). Below are the variables from your `.env.example`:

| Variable                    | Type   | Description                             | Example                        | Required |
| --------------------------- | ------ | --------------------------------------- | ------------------------------ | -------- |
| `SYMFONY__ENV__DOMAIN_NAME` | Config | Domain name for proper URL generation   | `https://wallabag.example.com` | Yes      |
| `SERVICE_FOLDER_DATA`       | Volume | Host path for Wallabag data storage     | `/srv/wallabag/data`           | Yes      |
| `SERVICE_FOLDER_IMAGES`     | Volume | Host path for article images and assets | `/srv/wallabag/images`         | Yes      |
| `SERVICE_LIMIT_CPU`         | Config | CPU limit for the container             | `1.0`                          | Yes      |
| `SERVICE_LIMIT_MEMORY`      | Config | Memory limit for the container          | `512M`                         | Yes      |
| `SERVICE_PORT`              | Port   | Host port for Wallabag web interface    | `8080`                         | Yes      |
| `TZ`                        | Config | Container timezone                      | `Europe/Madrid`                | Yes      |

**Typical `.env`**

```dotenv
# Core configuration
SYMFONY__ENV__DOMAIN_NAME=https://wallabag.example.com

# Service
SERVICE_FOLDER_DATA=/srv/wallabag/data
SERVICE_FOLDER_IMAGES=/srv/wallabag/images
SERVICE_LIMIT_CPU=1.0
SERVICE_LIMIT_MEMORY=512M
SERVICE_PORT=8080

# Other
TZ=Europe/Madrid
```

**Port mapping:**
- `SERVICE_PORT` → Container port 80 (Web interface and API)

**Volume mapping:**
- `SERVICE_FOLDER_DATA` → `/var/www/wallabag/data` (Database, cache, logs)
- `SERVICE_FOLDER_IMAGES` → `/var/www/wallabag/web/assets/images` (Article
  images and assets)

---

## Security Considerations

- **Domain configuration**: Set correct `SYMFONY__ENV__DOMAIN_NAME` for proper
  CSRF protection
- **Reverse proxy**: Use HTTPS reverse proxy for production deployment
- **User management**: Create strong passwords for Wallabag user accounts
- **API access**: Secure API keys if using mobile apps or external integrations
- **File permissions**: Ensure volume directories have appropriate permissions
- **Regular updates**: Keep container updated for security patches
- **Backup encryption**: Consider encrypting backups containing personal reading
  data

---

## Troubleshooting

**Service won't start:**
- Check if port `SERVICE_PORT` is available: `netstat -tulpn | grep :8080`
- Verify all volume directories exist and are writable
- Check container logs: `docker-compose logs wallabag`

**Database initialization issues:**
- Ensure data directory is writable by container
- Check for sufficient disk space
- Allow extra time for initial database setup (first run)

**Can't access web interface:**
- Confirm service is running: `docker-compose ps wallabag`
- Test connectivity: `curl http://localhost:${SERVICE_PORT}`
- Check for proxy configuration errors

**Article parsing/saving fails:**
- Verify internet connectivity from container
- Check if target websites block automated requests
- Review parsing errors in application logs
- Ensure sufficient memory allocation for content processing

**Images not loading:**
- Check `SERVICE_FOLDER_IMAGES` directory permissions
- Verify image assets are being downloaded correctly
- Confirm volume mapping is correct
- Check disk space in images directory

**Performance issues:**
- Increase `SERVICE_LIMIT_CPU` and `SERVICE_LIMIT_MEMORY` for better performance
- Check database size and consider cleanup of old articles
- Monitor resource usage: `docker stats wallabag`
- Consider adjusting PHP memory limits if needed

**Mobile app sync issues:**
- Verify `SYMFONY__ENV__DOMAIN_NAME` is accessible from mobile devices
- Check API endpoint accessibility
- Confirm user credentials and API configuration
- Test HTTPS certificate validity if using SSL

---

## Links

- [Wallabag Official Site](https://wallabag.org/)
- [Wallabag Documentation](https://doc.wallabag.org/)
- [Wallabag GitHub](https://github.com/wallabag/wallabag)
- [Mobile Apps](https://wallabag.org/en/download)
