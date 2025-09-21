# Mealie

## Overview

Mealie is a self-hosted recipe manager and meal planner with a RestAPI backend
and a reactive frontend application. It allows you to organize recipes, plan
meals, generate shopping lists, and import recipes from various sources. Perfect
for managing your culinary adventures and meal planning.

---

## Configuration

Environment is provided through `.env` (loaded with `env_file: .env`). Below are the variables from your `.env.example`:

| Variable               | Type   | Description                        | Example                      | Required |
| ---------------------- | ------ | ---------------------------------- | ---------------------------- | -------- |
| `BASE_URL`             | Config | Base URL for Mealie application    | `https://mealie.example.com` | Yes      |
| `PGID`                 | Config | Group ID for file permissions      | `1000`                       | Yes      |
| `PUID`                 | Config | User ID for file permissions       | `1000`                       | Yes      |
| `SERVICE_FOLDER_DATA`  | Volume | Host path for Mealie data storage  | `/srv/mealie/data`           | Yes      |
| `SERVICE_LIMIT_CPU`    | Config | CPU limit for the container        | `1.0`                        | Yes      |
| `SERVICE_LIMIT_MEMORY` | Config | Memory limit for the container     | `512M`                       | Yes      |
| `SERVICE_PORT`         | Port   | Host port for Mealie web interface | `9001`                       | Yes      |
| `TZ`                   | Config | Container timezone                 | `Europe/Madrid`              | Yes      |

**Typical `.env`**

```dotenv
# Core configuration
BASE_URL=https://mealie.example.com

# User permissions
PUID=1000
PGID=1000

# Service
SERVICE_FOLDER_DATA=/srv/mealie/data
SERVICE_LIMIT_CPU=1.0
SERVICE_LIMIT_MEMORY=512M
SERVICE_PORT=9001

# Other
TZ=Europe/Madrid
```

**Port mapping:**
- `SERVICE_PORT` → Container port 9000 (Web interface and API)

**Volume mapping:**
- `SERVICE_FOLDER_DATA` → `/app/data/` (Recipes, meal plans, user data, images)

---

## Security Considerations

- **Signup control**: Disable `ALLOW_SIGNUP` after creating initial accounts
- **Base URL**: Set correct `BASE_URL` for proper CORS and redirect handling
- **File permissions**: Ensure `PUID`/`PGID` match your host user for proper
  access
- **User management**: Create strong passwords for Mealie user accounts
- **Reverse proxy**: Use HTTPS reverse proxy for secure external access
- **API access**: Secure API tokens if using external integrations
- **Data privacy**: Recipe and meal data may contain personal dietary
  information
- **Image uploads**: Monitor storage usage for recipe images

---

## Troubleshooting

**Service won't start:**
- Check if port `SERVICE_PORT` is available: `netstat -tulpn | grep :9001`
- Verify `SERVICE_FOLDER_DATA` directory exists and is writable
- Check container logs: `docker-compose logs mealie`

**Permission errors:**
- Verify `PUID` and `PGID` match your host user: `id username`
- Check directory ownership: `ls -la /path/to/data/directory`
- Ensure data directory is writable by specified user

**Can't access web interface:**
- Confirm service is running: `docker-compose ps mealie`
- Test connectivity: `curl http://localhost:${SERVICE_PORT}`
- Check `BASE_URL` configuration matches your access method

**Recipe import issues:**
- Verify internet connectivity from container
- Check if target websites block automated requests
- Review import logs for specific error messages
- Test with different recipe sources

**Image upload problems:**
- Check available disk space in data directory
- Verify file permissions on data directory
- Ensure proper `PUID`/`PGID` configuration
- Monitor upload size limits

**Meal planning not working:**
- Confirm recipes are properly saved and accessible
- Check database integrity in data directory
- Verify no conflicts with existing meal plans
- Review application logs for specific errors

**Performance issues:**
- Increase `SERVICE_LIMIT_CPU` and `SERVICE_LIMIT_MEMORY` for better performance
- Monitor storage usage for recipe images
- Check resource usage: `docker stats mealie`
- Consider cleanup of old or unused recipes

**API integration problems:**
- Verify API endpoints are accessible
- Check authentication tokens and permissions
- Confirm `BASE_URL` is correctly configured
- Test API calls manually with curl

---

## Links

- [Mealie Official Site](https://mealie.io/)
- [Mealie Documentation](https://docs.mealie.io/)
- [Mealie GitHub](https://github.com/mealie-recipes/mealie)
- [Recipe Import Guide](https://docs.mealie.io/documentation/getting-started/importing-recipes/)
