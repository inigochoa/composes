# Vaultwarden

## Overview

Vaultwarden is an unofficial Bitwarden compatible server written in Rust. It's a
lightweight alternative to the official Bitwarden server, perfect for
self-hosting password management. Compatible with all official Bitwarden clients
and browser extensions.

---

## Configuration

Environment is provided through `.env` (loaded with `env_file: .env`). Below are the variables from your `.env.example`:

| Variable               | Type   | Description                             | Example                     | Required |
| ---------------------- | ------ | --------------------------------------- | --------------------------- | -------- |
| `ADMIN_TOKEN`          | Config | Admin panel access token (argon2 hash)  | `$argon2id$v=19$m=65540...` | Yes      |
| `DOMAIN`               | Config | Server domain for proper URL generation | `https://vault.example.com` | Yes      |
| `SERVICE_FOLDER_DATA`  | Volume | Host path for Vaultwarden data storage  | `/srv/vaultwarden/data`     | No       |
| `SERVICE_LIMIT_CPU`    | Config | CPU limit for the container             | `0.5`                       | No       |
| `SERVICE_LIMIT_MEMORY` | Config | Memory limit for the container          | `256M`                      | No       |
| `SERVICE_PORT`         | Port   | Host port for Vaultwarden web interface | `8080`                      | No       |
| `TZ`                   | Config | Container timezone                      | `Europe/Madrid`             | Yes      |

**Typical `.env`**

```dotenv
# Core configuration
ADMIN_TOKEN=your-secure-argon2-hash-here
DOMAIN=https://vault.example.com

# System
SERVICE_FOLDER_DATA=/srv/vaultwarden/data
SERVICE_LIMIT_CPU=0.5
SERVICE_LIMIT_MEMORY=256M
SERVICE_PORT=8080

# Other
TZ=Europe/Madrid
```

**Port mapping:**
- `SERVICE_PORT` → Container port 80 (Web interface and API)

**Volume mapping:**
- `SERVICE_FOLDER_DATA` → `/data` (Database, attachments, icons, logs)

---

## Security Considerations

- **Admin token**: Generate strong argon2 hash for `ADMIN_TOKEN` - never use
  plain text
- **Domain configuration**: Set correct `DOMAIN` for proper HTTPS redirect and
  CSRF protection
- **Signups**: Disable `SIGNUPS_ALLOWED` after initial setup to prevent
  unauthorized registrations
- **Rate limiting**: Configure appropriate rate limits to prevent brute force
  attacks
- **Reverse proxy**: Use HTTPS reverse proxy (Nginx/Traefik) for production
  deployment
- **Database encryption**: Enable database encryption in admin panel for
  additional security
- **Regular backups**: Critical password data requires frequent, tested backups
- **Access logs**: Monitor logs for suspicious login attempts
- **Updates**: Keep container updated for latest security patches

---

## Troubleshooting

**Service won't start:**
- Check if port `SERVICE_PORT` is available: `netstat -tulpn | grep :8080`
- Verify `SERVICE_FOLDER_DATA` directory exists and is writable
- Check container logs: `docker-compose logs vaultwarden`

**Can't access admin panel:**
- Verify `ADMIN_TOKEN` is properly set and is argon2 hash format
- Check admin rate limiting settings if locked out
- Ensure accessing correct URL: `http://localhost:${SERVICE_PORT}/admin`

**Login issues:**
- Check `DOMAIN` setting matches your actual domain/IP
- Verify rate limiting isn't blocking legitimate attempts
- Review authentication logs in admin panel

**Client sync problems:**
- Confirm `DOMAIN` is accessible from client devices
- Check HTTPS configuration if using reverse proxy
- Verify API endpoints are responding correctly

**Push notifications not working:**
- Validate `PUSH_INSTALLATION_ID` and `PUSH_INSTALLATION_KEY`
- Ensure `PUSH_ENABLED=true` is set
- Check push service connectivity in logs

**Database errors:**
- Check available disk space in data directory
- Verify SQLite database isn't corrupted: `sqlite3 db.sqlite3 "PRAGMA integrity_check;"`
- Review database permissions and file locks

**Performance issues:**
- Increase `SERVICE_LIMIT_CPU` and `SERVICE_LIMIT_MEMORY` for many users
- Monitor database size and consider maintenance
- Check resource usage: `docker stats vaultwarden`

---

## Links

- [Vaultwarden GitHub](https://github.com/dani-garcia/vaultwarden)
- [Vaultwarden Wiki](https://github.com/dani-garcia/vaultwarden/wiki)
- [Bitwarden Official](https://bitwarden.com/)
