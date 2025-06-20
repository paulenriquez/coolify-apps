# Logto

Logto is the modern, open-source auth infrastructure for SaaS and AI apps.

- **Official Website:** https://logto.io

- **Starting Point:** [Coolify's One-Click Template for Logto](https://github.com/coollabsio/coolify/blob/v4.x/templates/compose/logto.yaml) (_as of 27-May-2025_)

### Prevent Admin Console _"Unauthorized. Please check credentials and its scope"_ Error

Logto does not work out-of-the-box when deployed to Coolify. Although you'll be able to create the first account, you'll come to find that performing any action within the Admin UI will result in **"Unauthorized. Please check credentials and its scope"** error.

The error is discussed here: https://github.com/logto-io/logto/issues/6048.

Based on that GitHub link, the said error can be fixed by using the `extra_hosts` directive in the Docker Compose file. The FQDNs of `ENDPOINT` and `ADMIN_ENDPOINT` must be mapped to your proxy's IP address.

For a Coolify deployment, this would pertain to the IP address of `coolify-proxy` within your Logto container's Docker Network.

**However,** the implication is... you would have to do the following:

1. Look for `coolify-proxy`'s IP address by performing a `docker network inspect` against the Logto container's network
2. Add the IP address to the Docker Compose file's `extra_hosts`
3. Repeat # 1 in case the IP address changes (maybe due to a container/ docker/ server restart)

Effectively, you would have to manually "hardcode" that IP address — which you'll then have to update everytime that IP changes. _Not so elegant!_

This custom [docker-compose.yaml](./docker-compose.yaml) solves this by automating that process.

Instead of using `extra_hosts`, it uses an expanded `entrypoint` script which does the following everytime the container is started:

1. Pings `coolify-proxy` from inside the container, parses the output, and extracts the IP address
2. Modifies the container's `/etc/hosts` file to map your FQDNs (using the extracted IP address) — thus effectively achieving the same effect that `extra_hosts` does.

**The benefit?** the container can easily watch for the Coolify Proxy's IP address upon instantiation.

> [!NOTE]
>
> If in case the IP of the Coolify Proxy changes _while_ the Logto container is already running (highly, highly unlikely!) — simply restart the Logto container and it should be able to automatically "discover" that new IP.

### Use `postgres:17-alpine` instead of `postgres:14-alpine`

Coolify's one-click compose file (**Starting Point**) uses Postgres 14, while Logto's uses 17. Might be better to just use the more recent version.

### Admin Console made inaccessible from the public internet

Logto's Admin Console (_runs on port 3002 by default, but this deployment changed it to **port 23002**_) has been bound to `localhost`, preventing it from being accessed through the public internet.

The intent is to make it accessible via SSH Local Port Forwarding only — thus ensuring that only authorized users are actually the only ones able to access the console.

To access the Logto Admin Console, run the following on your device:

```bash
ssh -L 23002:localhost:23002 <USERNAME>@<YOUR_SERVER_ADDRESS>
```
