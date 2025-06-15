# Open WebUI

A user-friendly, open-source web UI for LLMs.

- **Official Website:** https://openwebui.com/

- **Starting Point:** [Coolify's One-Click Template for Open WebUI](https://github.com/coollabsio/coolify/blob/v4.x/templates/compose/open-webui.yaml) (_as of 16-Jun-2025_)

## Additional Services

Added two (2) additional services after `open-webui`:

1. **Postgres (`postgres`)** — To use Postgres instead of the default SQLite database.
2. **offen/docker-volume-backup (`backup`)** — To be able to backup Open WebUI's docker volume.

Hence, the following prefixes are used to organize the environment variables (with some exceptions, such as Coolify's `SERVICE_` variables):

| Service      | Prefix you will see in Coolify's UI |
| ------------ | ----------------------------------- |
| `open-webui` | `WEBUI_`                            |
| `postgres`   | `POSTGRES_`                         |
| `dvb-backup` | `DVB_` (_for Docker Volume Backup_) |

## OIDC Authentication

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

## Use Postgres instead of SQLite

Open WebUI uses SQLite by default... **but**, it can work with Postgres (see https://docs.openwebui.com/getting-started/env-configuration#database-pool)

Given that...

- Backups on Coolify are so much easier with Postgres
- Postgres is _in theory_, more scaleable...

This deployment has been configured to work with Postgres.

Hence, the `DATABASE_URL` environment variable has been configured like so...

```yaml
DATABASE_URL=postgres://${SERVICE_USER_POSTGRES}:${SERVICE_PASSWORD_POSTGRES}@postgres:5432/${POSTGRES_DB:-webui}
```

## Use of S3 Storage

Open WebUI can use S3 storage for uploads (see https://docs.openwebui.com/tutorials/s3-storage).

This deployment is designed to utilize this capability.

> [!CAUTION]
> As of 15-Jun-2025, Open WebUI's website classifies S3 storage feature as **experimental**.

The ff. environment variables are exposed to be able to configure your S3 provider.

| Open WebUI Env Var     | Env Var in Coolify's UI      |
| ---------------------- | ---------------------------- |
| `S3_ENDPOINT_URL`      | `WEBUI_S3_ENDPOINT_URL`      |
| `S3_BUCKET_NAME`       | `WEBUI_S3_BUCKET_NAME`       |
| `S3_ACCESS_KEY_ID`     | `WEBUI_S3_ACCESS_KEY_ID`     |
| `S3_SECRET_ACCESS_KEY` | `WEBUI_S3_SECRET_ACCESS_KEY` |

For more information, see https://docs.openwebui.com/tutorials/s3-storage.

## Added Docker Volume Backup

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
> **Use a dedicated S3 bucket for Docker Volume Backups**. Don't use an S3 bucket that is being used for other purposes. Docker Volume Backup prunes old backups ("old" is based on `DVB_RETENTION_DAYS`) approach is to scan **all** files within the bucket). If you have any other files in that bucket, it _can_ get deleted.

Additionally, the following values are configured by default:

```yaml
BACKUP_FILENAME=open-webui-dvb-%Y-%m-%dT%H-%M-%S-UTC.tar.gz
BACKUP_PRUNING_PREFIX=open-webui-dvb-
```

See https://offen.github.io/docker-volume-backup/ for more information.

> [!NOTE]
> Docker Volume Backup uses **UTC Timezone**. Keep that in mind when scheduling backups using the `DVB_CRON_EXP_UTC` env var.

## How many S3 buckets does this deployment rely on?

### ✅ TLDR: Three (3) S3 Buckets

This deployment comes with three functionalities that rely on S3 (listed below). **⚠️ USE SEPARATE S3 BUCKETS FOR EACH! ⚠️**.

1. Open WebUI File Uploads (configured under `open-webui` in the compose file)
2. Postgres Backup (configured via Coolify's UI)
3. Docker Volume Backups (configured under `backup` in the compose file)

> [!CAUTION]
>
> _For additional emphasis..._ **⚠️ USE SEPARATE S3 BUCKETS FOR EACH! ⚠️** The above listed functionalities use separate, independent S3 clients. The way they upload (and delete) files _can_ conflict with one another. To reduce the risk of your files getting overwritten, it's best to use separate buckets for each.
