# Open WebUI

A user-friendly, open-source web UI for LLMs.

- **Official Website:** https://openwebui.com/

- **Starting Point:** [Coolify's One-Click Template for Open WebUI](https://github.com/coollabsio/coolify/blob/v4.x/templates/compose/open-webui.yaml) (_as of 16-Jun-2025_)

## Services

This deployment has a total of four (4) services...

1. **Open WebUI (`open-webui`)**
2. **Postgres (`postgres`)** — To use Postgres instead of the default SQLite database.
3. **offen/docker-volume-backup (`docker-volume-backup`)** — To be able to backup Open WebUI's docker volume.
4. **Docling (`docling`)** — [Docling](https://docling-project.github.io/docling/) is used as the document extraction engine.

## S3 Storage

You'll need **three (3), separate** S3 buckets for this deployment.

It is important that you **⚠️ USE SEPARATE S3 BUCKETS FOR EACH! ⚠️**.

1. Open WebUI File Uploads (configured under `open-webui` in the compose file)
2. Postgres Backup (configured via Coolify's UI)
3. Docker Volume Backups (configured under `docker-volume-backup` in the compose file)

> [!CAUTION]
>
> _For additional emphasis..._ **⚠️ USE SEPARATE S3 BUCKETS FOR EACH! ⚠️** The above listed functionalities use separate, independent S3 clients. The way they upload (and delete) files _can_ conflict with one another. To reduce the risk of your files getting overwritten, it's best to use separate buckets for each.

## Customizations

### OIDC Authentication

This deployment is intended to be used with OIDC authentication.

The ff. environment variables are configured accordingly:

```yaml
ENABLE_SIGNUP=False
ENABLE_OAUTH_SIGNUP=False
```

Additionally, ff. environment variables are exposed to be able to input details of the OIDC provider.

| Open WebUI Env Var    | Env Var in Coolify's UI     |
| --------------------- | --------------------------- |
| `OAUTH_CLIENT_ID`     | `WEBUI_OAUTH_CLIENT_ID`     |
| `OAUTH_CLIENT_SECRET` | `WEBUI_OAUTH_CLIENT_SECRET` |
| `OPENID_PROVIDER_URL` | `WEBUI_OPENID_PROVIDER_URL` |
| `OAUTH_SCOPES`        | `WEBUI_OAUTH_SCOPES `       |

For more information on how to configure OIDC, please see https://docs.openwebui.com/features/sso#oidc

---

### Use Postgres instead of SQLite

Open WebUI uses SQLite by default. However, it is compatible with Postgres (see https://docs.openwebui.com/getting-started/env-configuration#database-pool).

Given that...

- Backups on Coolify are so much easier with Postgres
- Postgres is _in theory_, more scaleable...

This deployment has been configured to instead use Postgres.

Hence, the `DATABASE_URL` environment variable has been configured like so...

```yaml
DATABASE_URL=postgres://${SERVICE_USER_POSTGRES}:${SERVICE_PASSWORD_POSTGRES}@postgres:5432/${POSTGRES_DB:-webui}
```

---

### Use of S3 Storage

Open WebUI can use S3 storage for uploads (see https://docs.openwebui.com/tutorials/s3-storage).

This deployment is designed to utilize this capability.

> [!CAUTION]
> As of 15-Jun-2025, Open WebUI's website classifies the S3 storage feature as **experimental**.

The ff. environment variables are exposed to be able to configure your S3 provider.

| Open WebUI Env Var     | Env Var in Coolify's UI      |
| ---------------------- | ---------------------------- |
| `S3_ENDPOINT_URL`      | `WEBUI_S3_ENDPOINT_URL`      |
| `S3_BUCKET_NAME`       | `WEBUI_S3_BUCKET_NAME`       |
| `S3_ACCESS_KEY_ID`     | `WEBUI_S3_ACCESS_KEY_ID`     |
| `S3_SECRET_ACCESS_KEY` | `WEBUI_S3_SECRET_ACCESS_KEY` |

For more information, see https://docs.openwebui.com/tutorials/s3-storage.

---

### Added Docker Volume Backup

Although Open WebUI can utilize S3 for uploads, it still heavily relies on its Docker Volume to manage its persisted data.

This deployment utilizes [offen/docker-volume-backup](https://offen.github.io/docker-volume-backup/) to be able to backup Open WebUI's volume to an S3-compatible storage.

Use the following environment variables to configure Docker Volume Backup:

| Docker Vol. Backup Env Var | Env Var in Coolify's UI    |
| -------------------------- | -------------------------- |
| `AWS_ENDPOINT`             | `DVB_S3_ENDPOINT`          |
| `AWS_S3_BUCKET_NAME`       | `DVB_S3_BUCKET_NAME`       |
| `AWS_ACCESS_KEY_ID`        | `DVB_S3_ACCESS_KEY_ID`     |
| `AWS_SECRET_ACCESS_KEY`    | `DVB_S3_SECRET_ACCESS_KEY` |
| `BACKUP_CRON_EXPRESSION`   | `DVB_CRON_EXP_UTC`         |
| `BACKUP_RETENTION_DAYS`    | `DVB_RETENTION_DAYS`       |

> [!CAUTION]
>
> **Use a dedicated S3 bucket for Docker Volume Backups**. Don't use an S3 bucket that is being used for other purposes. Docker Volume Backup prunes old backups ("old" is based on `DVB_RETENTION_DAYS`) by scanning **all** files within the bucket. If you have any other files in that bucket, it _can_ get deleted.

Additionally, the following values are configured by default:

```yaml
BACKUP_FILENAME=open-webui-dvb-%Y-%m-%dT%H-%M-%SZ.tar.gz
BACKUP_PRUNING_PREFIX=open-webui-dvb-
```

See https://offen.github.io/docker-volume-backup/ for more information.

> [!NOTE]
> Docker Volume Backup uses **UTC Timezone**. Keep that in mind when scheduling backups using the `DVB_CRON_EXP_UTC` env var.

---

### Docling

This deployment includes [Docling](https://docling-project.github.io/docling/) (_more specifically_ [Docling Serve](https://github.com/docling-project/docling-serve)) which will be used as Open WebUI's Document Extraction Engine.

Docling is configured to run on Port 25001.

As such, the following environment variables under `open-webui` has been configured like so:

```yaml
CONTENT_EXTRACTION_ENGINE=docling
DOCLING_SERVER_URL=http://docling:25001
```

#### Docling Serve UI

Docling Serve has a bundled UI available at the `/ui` endpoint.

This deployment enables this feature through the `DOCLING_SERVE_ENABLE_UI=true` environment variable under the `docling` service.

To make this accessible via SSH Local Port Forwarding, the Docling Serve container is bound to `127.0.0.1` (see "ports" section of the `docling` service.)

Hence, by running `ssh -L 25001:localhost:25001 <USERNAME>@<YOUR_SERVER_ADDRESS>`, you'll be able to access this UI on your device at http://localhost:25001/ui.

---

### Web Search

Open WebUI works with DuckDuckGo out of the box. For ease of set-up, this has been configured as the default search engine of this Open WebUI deployment.

#### For more advanced use-cases...

I _highly_ recommend [Tavily](https://www.tavily.com/) — both for _Web Search_ and _Web Loader_.

They have a generous free tier, and have reasonable pay-as-you-go pricing.

---

### Timezone

Open WebUI can inject the system time in system prompts.

To ensure consistency, the `open-webui` service has explicitly been passed the `TZ=UTC` environment variable.

---

### Open WebUI Defaults

Some of Open WebUI's config are set-up with the following defaults:

```yaml
ENABLE_OLLAMA_API=False
DEFAULT_USER_ROLE=user
ENABLE_CODE_EXECUTION=False
ENABLE_CODE_INTERPRETER=False
ENABLE_TAGS_GENERATION=False
ENABLE_EVALUATION_ARENA_MODELS=False
ENABLE_COMMUNITY_SHARING=False
USER_PERMISSIONS_CHAT_CONTROLS=False
USER_PERMISSIONS_CHAT_STT=False
USER_PERMISSIONS_CHAT_TTS=False
USER_PERMISSIONS_CHAT_CALL=False
USER_PERMISSIONS_FEATURES_CODE_INTERPRETER=False
USER_PERMISSIONS_WORKSPACE_KNOWLEDGE_ACCESS=True
USER_PERMISSIONS_WORKSPACE_PROMPTS_ACCESS=True
FILE_IMAGE_COMPRESSION_WIDTH=720
FILE_IMAGE_COMPRESSION_HEIGHT=720
```
