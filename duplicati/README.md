# Duplicati

## Overview

Duplicati is a free backup client that securely stores encrypted, incremental,
compressed backups on cloud storage services and remote file servers. It
features a web-based interface for easy configuration and monitoring of backup
jobs.

---

## Configuration

Environment is provided through `.env` (loaded with `env_file: .env`). Below are
the variables from your `.env.example`:

| Variable                   | Type   | Description                                     | Example                    | Required |
| -------------------------- | ------ | ----------------------------------------------- | -------------------------- | -------- |
| `PGID`                     | Config | Group ID for file permissions                   | `1000`                     | Yes      |
| `PUID`                     | Config | User ID for file permissions                    | `1000`                     | Yes      |
| `SERVICE_FOLDER_BACKUPS`   | Volume | Host path where backup files will be stored     | `/srv/duplicati/backups`   | No       |
| `SERVICE_FOLDER_CONFIG`    | Volume | Host path for Duplicati configuration files     | `/srv/duplicati/config`    | No       |
| `SERVICE_FOLDER_SOURCE`    | Volume | Host path containing data to backup (read-only) | `/srv/data`                | No       |
| `SERVICE_LIMIT_CPU`        | Config | CPU limit for the container                     | `2.0`                      | No       |
| `SERVICE_LIMIT_MEMORY`     | Config | Memory limit for the container                  | `1G`                       | No       |
| `SERVICE_PORT`             | Port   | Host port for Duplicati web interface           | `8200`                     | No       |
| `SETTINGS_ENCRYPTION_KEY`  | Config | Encryption key for Duplicati settings database  | `your-secure-key-here`     | Yes      |
| `TZ`                       | Config | Container timezone                              | `Europe/Madrid`            | Yes      |

**Typical `.env`**

```dotenv
# User permissions
PUID=1000
PGID=1000

# Service
SERVICE_FOLDER_BACKUPS=/srv/duplicati/backups
SERVICE_FOLDER_CONFIG=/srv/duplicati/config
SERVICE_FOLDER_SOURCE=/srv/data
SERVICE_LIMIT_CPU=2.0
SERVICE_LIMIT_MEMORY=1G
SERVICE_PORT=8200

# Other
SETTINGS_ENCRYPTION_KEY=your-very-secure-encryption-key-here
TZ=Europe/Madrid
```

**Port mapping:**
- `SERVICE_PORT` → Container port 8200 (Web interface)

**Volume mapping:**
- `SERVICE_FOLDER_BACKUPS` → `/backups` (Backup storage location)
- `SERVICE_FOLDER_CONFIG` → `/config` (Duplicati configuration and database)
- `SERVICE_FOLDER_SOURCE` → `/source` (Source data to backup - read-only)

---

## Security Considerations

- **Encryption key**: Use a strong, unique `SETTINGS_ENCRYPTION_KEY` to protect
  settings database
- **File permissions**: Ensure `PUID`/`PGID` match your host user for proper
  file access
- **Network access**: Consider restricting web interface access with reverse
  proxy authentication
- **Source data**: Mounted read-only to prevent accidental modifications during
  backup
- **Backup encryption**: Configure backup job encryption within Duplicati for
  additional security
- **Cloud credentials**: Store cloud service credentials securely within
  Duplicati interface

---

## Troubleshooting

**Service won't start:**
- Check if port `SERVICE_PORT` is available: `netstat -tulpn | grep :8200`
- Verify all volume directories exist and are accessible
- Check container logs: `docker-compose logs duplicati`

**Can't access web interface:**
- Confirm service is running: `docker-compose ps duplicati`
- Test port connectivity: `telnet localhost ${SERVICE_PORT}`
- Check firewall settings

**Permission errors:**
- Verify `PUID` and `PGID` match your host user: `id username`
- Check directory ownership: `ls -la /path/to/volumes`
- Ensure source directory is readable by specified user

**Backup jobs failing:**
- Check available disk space in backup destination
- Verify cloud service credentials and connectivity
- Review job logs in Duplicati web interface
- Ensure source files are accessible and not locked

**Performance issues:**
- Increase `SERVICE_LIMIT_CPU` and `SERVICE_LIMIT_MEMORY` for large backups
- Check I/O performance on backup storage location
- Consider scheduling backups during off-peak hours
- Monitor resource usage: `docker stats duplicati`

---

## Links

- [Duplicati Official Site](https://www.duplicati.com/)
- [Duplicati Documentation](https://duplicati.readthedocs.io/)
- [LinuxServer Duplicati Image](https://docs.linuxserver.io/images/docker-duplicati)
