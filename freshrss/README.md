# FreshRSS

## Overview

FreshRSS is a self-hosted RSS and Atom feed aggregator that allows you to follow
your favorite websites and blogs in one place. It features a clean web
interface, mobile support, extensive customization options, and powerful
filtering capabilities to help you stay organized with your feeds.

---

## Configuration

Environment is provided through `.env` (loaded with `env_file: .env`). Below are
the variables from your `.env.example`:

| Variable                    | Type   | Description                              | Example                      | Required |
| --------------------------- | ------ | ---------------------------------------- | ---------------------------- | -------- |
| `CRON_MIN`                  | Config | Cron schedule for feed updates (minutes) | `*/13`                       | Yes      |
| `SERVICE_FOLDER_DATA`       | Volume | Host path for FreshRSS data storage      | `/srv/freshrss/data`         | Yes      |
| `SERVICE_FOLDER_EXTENSIONS` | Volume | Host path for FreshRSS extensions        | `/srv/freshrss/extensions`   | Yes      |
| `SERVICE_LIMIT_CPU`         | Config | CPU limit for the container              | `0.5`                        | Yes      |
| `SERVICE_LIMIT_MEMORY`      | Config | Memory limit for the container           | `256M`                       | Yes      |
| `SERVICE_PORT`              | Port   | Host port for FreshRSS web interface     | `8080`                       | Yes      |
| `TZ`                        | Config | Container timezone                       | `Europe/Madrid`              | Yes      |

**Typical `.env`**

```dotenv
# Core configuration
CRON_MIN=*/13

# Service
SERVICE_FOLDER_DATA=/srv/freshrss/data
SERVICE_FOLDER_EXTENSIONS=/srv/freshrss/extensions
SERVICE_LIMIT_CPU=0.5
SERVICE_LIMIT_MEMORY=256M
SERVICE_PORT=8080

# Other
TZ=Europe/Madrid
```

**Port mapping:**
- `SERVICE_PORT` → Container port 80 (Web interface)

**Volume mapping:**
- `SERVICE_FOLDER_DATA` → `/var/www/FreshRSS/data` (Database, user data,
  configuration)
- `SERVICE_FOLDER_EXTENSIONS` → `/var/www/FreshRSS/extensions` (Custom
  extensions and themes)

---

## Security Considerations

- **Authentication**: Set up strong passwords for FreshRSS user accounts
- **Reverse proxy**: Use HTTPS reverse proxy for secure access over internet
- **Feed validation**: Be cautious when adding feeds from untrusted sources
- **File permissions**: Ensure volume directories have appropriate permissions
- **API access**: Secure API tokens if using mobile apps or external readers
- **Extension security**: Only install extensions from trusted sources
- **Regular updates**: Keep container updated for security patches

---

## Troubleshooting

**Service won't start:**
- Check if port `SERVICE_PORT` is available: `netstat -tulpn | grep :8080`
- Verify all volume directories exist and are writable
- Check container logs: `docker-compose logs freshrss`

**Initial setup issues:**
- Allow extra time for first-run database initialization
- Ensure data directory is writable by container
- Check for sufficient disk space

**Feed updates not working:**
- Verify `CRON_MIN` format is correct (e.g., `*/13` for every 13 minutes)
- Check internet connectivity from container
- Monitor logs during scheduled update times
- Ensure feeds are accessible and valid

**Feeds not loading/parsing:**
- Test feed URLs manually in browser
- Check for network connectivity issues
- Verify RSS/Atom feed format validity
- Review feed-specific error messages in logs

**Extensions not working:**
- Confirm extensions are placed in correct directory structure
- Check extension compatibility with FreshRSS version
- Verify file permissions on extensions directory
- Review extension logs for specific errors

**Performance issues:**
- Increase `SERVICE_LIMIT_CPU` and `SERVICE_LIMIT_MEMORY` for many feeds
- Adjust feed update frequency with `CRON_MIN`
- Check database size and consider cleanup of old articles
- Monitor resource usage: `docker stats freshrss`

**Mobile app sync issues:**
- Verify API is enabled in FreshRSS settings
- Check authentication credentials in mobile app
- Ensure FreshRSS is accessible from mobile network
- Test API endpoints manually if needed

---

## Links

- [FreshRSS Official Site](https://freshrss.org/)
- [FreshRSS GitHub](https://github.com/FreshRSS/FreshRSS)
- [FreshRSS Documentation](https://freshrss.github.io/FreshRSS/)
- [Extensions Repository](https://github.com/FreshRSS/Extensions)
