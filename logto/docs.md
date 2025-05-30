# Logto

A comprehensive identity solution covering both the front and backend, complete with pre-built infrastructure and enterprise-grade solutions.

##### 🌐 OFFICIAL WEBSITE: https://logto.io

## Customizations

##### 📍 STARTING POINT: [Coolify's One-Click Template for Logto](https://github.com/coollabsio/coolify/blob/v4.x/templates/compose/logto.yaml) (_as of 27-May-2025_)

### Making it work with Coolify's Traefik Proxy:

- Using the one-click template results into this issue (after first user account creation): https://github.com/logto-io/logto/issues/6048

- Based on the above GitHub issue discussion, this can be fixed by using the `extra_hosts` directive. Hence, this is included as part of the [docker-compose.yml](./docker-compose.yml) file:

```yaml
# Under services > logto
extra_hosts:
  - "$LOGTO_ENDPOINT_NO_PROTOCOL:host-gateway"
  - "$LOGTO_ADMIN_ENDPOINT_NO_PROTOCOL:host-gateway"
```

- **Do note that:** there are two new environment variables that you need to configure in Coolify: `LOGTO_ENDPOINT_NO_PROTOCOL` and `LOGTO_ADMIN_ENDPOINT_NO_PROTOCOL`. These should hold the same value as `LOGTO_ENDPOINT` and `LOGTO_ADMIN_ENDPOINT` — just **without** the protocol (the `https://` prefix)
