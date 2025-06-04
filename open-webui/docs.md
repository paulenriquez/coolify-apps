# Open WebUI

A user-friendly, open-source web UI for LLMs.

##### 🌐 OFFICIAL WEBSITE: https://openwebui.com/

## Customizations

##### 📍 STARTING POINT: [Docker Compose File from Open WebUI's GitHub Repo](https://github.com/open-webui/open-webui/blob/main/docker-compose.yaml) (_as of 27-May-2025_)

### No Ollama:

- This setup does not include Ollama, as Open WebUI will be used as a front-end to connect to external model APIs.

### OIDC Authentication:

- Added OpenID Connect (OIDC) environment variables for authentication. These variables (`OIDC_CLIENT_ID`, `OIDC_CLIENT_SECRET`, `OIDC_ISSUER_URL`, `OIDC_AUTH_URL`, `OIDC_TOKEN_URL`, `OIDC_USERINFO_URL`, `OIDC_SCOPES`) need to be configured in Coolify with your OIDC provider's details.

### Database Backup:

- Included `offen/docker-volume-backup` service to regularly backup the SQLite database used by Open WebUI.
- Configured to upload backups to an S3-compatible storage bucket. The following environment variables need to be set in Coolify: `S3_ENDPOINT`, `S3_ACCESS_KEY_ID`, `S3_SECRET_ACCESS_KEY`, and `S3_BUCKET`.
- The number of days to keep backups can be configured using the `BACKUP_KEEP_DAYS` environment variable (defaults to 7 days).
- The backup runs daily at midnight (0 0 \* \* \*), but can be customized using the `BACKUP_CRON_SCHEDULE` environment variable.
- For more details, refer to the [offen/docker-volume-backup documentation](https://offen.github.io/docker-volume-backup/).

### Removed `ports` from the compose file:

- Prevents Open WebUI from being accessible at `<YOUR_SERVER_IP>:<OPEN_WEBUI_PORT>`.
