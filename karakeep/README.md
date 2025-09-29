# Karakeep

## Overview

Karakeep is a self-hosted bookmark and content management application that saves
web pages with full content archival, search capabilities, and organization
features. It uses Chrome for web scraping, Meilisearch for powerful search
functionality, and provides a modern web interface for managing your saved
content.

---

## Configuration

Environment is provided through `.env` (loaded with `env_file: .env`). Below are
the variables from your `.env.example`:

| Variable                      | Type   | Description                                | Example                        | Required |
| ----------------------------- | ------ | ------------------------------------------ | ------------------------------ | -------- |
| `BROWSER_WEB_URL`             | Config | Chrome debugging URL for web scraping      | `http://chrome:9222`           | Yes      |
| `DATA_DIR`                    | Config | Internal data directory path               | `/data`                        | Yes      |
| `MEILI_ADDR`                  | Config | Meilisearch server address                 | `http://meilisearch:7700`      | Yes      |
| `MEILI_MASTER_KEY`            | Config | Meilisearch master key for API access      | `your-secure-master-key`       | Yes      |
| `NEXTAUTH_SECRET`             | Config | NextAuth secret key for session encryption | `your-secure-secret-here`      | Yes      |
| `NEXTAUTH_URL`                | Config | NextAuth base URL for authentication       | `https://karakeep.example.com` | Yes      |
| `SERVICE_FOLDER_DATA`         | Volume | Host path for Karakeep data storage        | `/srv/karakeep/data`           | No       |
| `SERVICE_LIMIT_CPU`           | Config | CPU limit for Karakeep container           | `1.0`                          | No       |
| `SERVICE_LIMIT_CPU_CHROME`    | Config | CPU limit for Chrome container             | `1.0`                          | No       |
| `SERVICE_LIMIT_CPU_SEARCH`    | Config | CPU limit for Meilisearch container        | `1.0`                          | No       |
| `SERVICE_LIMIT_MEMORY`        | Config | Memory limit for Karakeep container        | `1G`                           | No       |
| `SERVICE_LIMIT_MEMORY_CHROME` | Config | Memory limit for Chrome container          | `512M`                         | No       |
| `SERVICE_LIMIT_MEMORY_SEARCH` | Config | Memory limit for Meilisearch container     | `1G`                           | No       |
| `SERVICE_PORT`                | Port   | Host port for Karakeep web interface       | `3000`                         | No       |
| `TZ`                          | Config | Container timezone                         | `Europe/Madrid`                | No       |

**Typical `.env`**

```dotenv
# Core configuration
BROWSER_WEB_URL=http://chrome:9222
DATA_DIR=/data
NEXTAUTH_SECRET=your-very-secure-nextauth-secret-key
NEXTAUTH_URL=https://karakeep.example.com

# Search configuration
MEILI_ADDR=http://meilisearch:7700
MEILI_MASTER_KEY=your-secure-meilisearch-master-key

# Service
SERVICE_FOLDER_DATA=/srv/karakeep/data
SERVICE_LIMIT_CPU=1.0
SERVICE_LIMIT_CPU_CHROME=1.0
SERVICE_LIMIT_CPU_SEARCH=1.0
SERVICE_LIMIT_MEMORY=1G
SERVICE_LIMIT_MEMORY_CHROME=512M
SERVICE_LIMIT_MEMORY_SEARCH=512M
SERVICE_PORT=3000

# Other
TZ=Europe/Madrid
```

**Port mapping:**
- `SERVICE_PORT` → Container port 3000 (Web interface)
- Chrome and Meilisearch use internal networking only

**Volume mapping:**
- `SERVICE_FOLDER_DATA` → `/data` (Bookmarks, archived content, database)
- `meilisearch` → `/meili_data` (Search index - Docker managed volume)

---

## Security Considerations

- **Authentication secrets**: Use strong, unique values for `NEXTAUTH_SECRET`
  and `MEILI_MASTER_KEY`
- **Signup control**: Disable `DISABLE_SIGNUPS` after creating initial accounts
- **Chrome security**: Runs with `--no-sandbox` for Docker compatibility -
  isolated from host
- **User permissions**: Karakeep runs as user 1000:1000 for security
- **Network isolation**: Chrome and Meilisearch not exposed to host network
- **Data privacy**: Archived content may contain sensitive information
- **Search index**: Meilisearch indexes all saved content
- **Reverse proxy**: Use HTTPS reverse proxy for production deployment
- **API keys**: Protect Meilisearch master key securely

---

## Troubleshooting

**Services won't start:**
- Check if port `SERVICE_PORT` is available: `netstat -tulpn | grep :3000`
- Verify all volume directories exist and are accessible
- Ensure startup order allows time for dependencies

**Chrome connection issues:**
- Verify Chrome container is running: `docker-compose ps chrome`
- Check `BROWSER_WEB_URL` points to correct Chrome container
- Test Chrome debugging: `curl http://localhost:9222/json/version`
- Review Chrome logs: `docker-compose logs chrome`

**Meilisearch not working:**
- Confirm Meilisearch container is healthy
- Verify `MEILI_MASTER_KEY` is correctly set
- Check `MEILI_ADDR` points to Meilisearch container
- Test Meilisearch: `curl http://localhost:7700/health`

**Web scraping failures:**
- Ensure Chrome has sufficient memory allocation
- Check for timeout errors in Karakeep logs
- Verify target websites are accessible
- Monitor Chrome resource usage

**Search not returning results:**
- Verify Meilisearch is running and healthy
- Check if content is being indexed correctly
- Review Meilisearch logs for indexing errors
- Test search API directly

**Permission errors:**
- Ensure data directory is owned by user 1000:1000
- Check directory permissions: `ls -la ${SERVICE_FOLDER_DATA}`
- Verify container can write to data directory

**Performance issues:**
- Increase memory limits for Chrome and Meilisearch
- Enable `DB_WAL_MODE` for better SQLite performance
- Monitor resource usage: `docker stats karakeep chrome meilisearch`
- Consider reducing concurrent scraping operations

**Authentication problems:**
- Verify `NEXTAUTH_SECRET` is properly set
- Check `NEXTAUTH_URL` matches actual access URL
- Clear browser cookies and cache
- Review authentication logs in Karakeep

---

## Links

- [Karakeep GitHub](https://github.com/karakeep-app/karakeep)
- [Meilisearch Documentation](https://www.meilisearch.com/docs)
- [Alpine Chrome](https://github.com/Zenika/alpine-chrome)
