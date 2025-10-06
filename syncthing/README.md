# Syncthing

## Overview

Syncthing is a continuous file synchronization program that synchronizes files
between multiple devices in real-time. It's decentralized, secure, and provides
a web-based interface for configuration and monitoring. Perfect for keeping
documents, photos, and other files in sync across your devices.

---

## Configuration

Environment is provided through `.env` (loaded with `env_file: .env`). Below are the variables from your `.env.example`:

| Variable                | Type   | Description                                 | Example                 | Required |
| ----------------------- | ------ | ------------------------------------------- | ----------------------- | -------- |
| `PGID`                  | Config | Group ID for file permissions               | `1000`                  | Yes      |
| `PUID`                  | Config | User ID for file permissions                | `1000`                  | Yes      |
| `SERVICE_FOLDER_CONFIG` | Volume | Host path for Syncthing configuration files | `/srv/syncthing/config` | No       |
| `SERVICE_FOLDER_DATA`   | Volume | Host path for synchronized data             | `/srv/syncthing/data`   | No       |
| `SERVICE_LIMIT_CPU`     | Config | CPU limit for the container                 | `1.0`                   | No       |
| `SERVICE_LIMIT_MEMORY`  | Config | Memory limit for the container              | `512M`                  | No       |
| `SERVICE_PORT`          | Port   | Host port for Syncthing web interface       | `8384`                  | No       |
| `TZ`                    | Config | Container timezone                          | `Europe/Madrid`         | Yes      |

**Typical `.env`**

```dotenv
# User permissions
PUID=1000
PGID=1000

# Service
SERVICE_FOLDER_CONFIG=/srv/syncthing/config
SERVICE_FOLDER_DATA=/srv/syncthing/data
SERVICE_LIMIT_CPU=1.0
SERVICE_LIMIT_MEMORY=512M
SERVICE_PORT=8384

# Other
TZ=Europe/Madrid
```

**Port mapping:**
- `SERVICE_PORT` → Container port 8384 (Web interface)
- `22000` → Container port 22000/tcp (File transfer)
- `22000` → Container port 22000/udp (File transfer)
- `21027` → Container port 21027/udp (Discovery)

**Volume mapping:**
- `SERVICE_FOLDER_CONFIG` → `/config` (Syncthing configuration, certificates,
  database)
- `SERVICE_FOLDER_DATA` → `/data` (Synchronized files and folders)

---

## Security Considerations

- **Web interface access**: Default setup allows local access only - configure
  authentication for remote access
- **Device authentication**: Only add trusted devices to your Syncthing network
- **File permissions**: Ensure `PUID`/`PGID` match your host user for proper
  file access
- **Network exposure**: Consider firewall rules for ports 22000 and 21027 if
  exposing to internet
- **Data encryption**: All data is encrypted in transit between devices
- **API key**: Protect the API key shown in web interface settings
- **Folder permissions**: Review shared folder permissions regularly

---

## Troubleshooting

**Service won't start:**
- Check if port `SERVICE_PORT` is available: `netstat -tulpn | grep :8384`
- Verify all volume directories exist and are writable
- Check container logs: `docker-compose logs syncthing`

**Can't access web interface:**
- Confirm service is running: `docker-compose ps syncthing`
- Test local connectivity: `curl http://localhost:${SERVICE_PORT}`
- Check for GUI address restrictions in Syncthing config

**Permission errors:**
- Verify `PUID` and `PGID` match your host user: `id username`
- Check directory ownership: `ls -la /path/to/volumes`
- Ensure data directories are writable by specified user

**Devices won't connect:**
- Check firewall settings for ports 22000 and 21027
- Verify network connectivity between devices
- Check device IDs are correctly added in web interface
- Review connection logs in web interface

**Synchronization issues:**
- Check available disk space on all devices
- Verify folder paths exist and are accessible
- Review ignore patterns that might exclude files
- Check for conflicting files in sync folders

**Performance issues:**
- Increase `SERVICE_LIMIT_CPU` and `SERVICE_LIMIT_MEMORY` for large syncs
- Monitor network bandwidth usage
- Consider adjusting sync settings for large folders
- Check resource usage: `docker stats syncthing`

---

## Links

- [Syncthing Official Site](https://syncthing.net/)
- [Syncthing Documentation](https://docs.syncthing.net/)
- [LinuxServer Syncthing Image](https://docs.linuxserver.io/images/docker-syncthing)
