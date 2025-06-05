# Logto

Logto is the modern, open-source auth infrastructure for SaaS and AI apps.

- **Official Website:** https://logto.io

- **Starting Point:** [Coolify's One-Click Template for Logto](https://github.com/coollabsio/coolify/blob/v4.x/templates/compose/logto.yaml) (_as of 27-May-2025_)

### Making Logto work with Coolify's Proxy:

Using the one-click template results into this issue (after first user account creation): https://github.com/logto-io/logto/issues/6048

Based on that GitHub issue discussion, this can be fixed by using the `extra_hosts` directive. The FQDNs of the `ENDPOINT` and `ADMIN_ENDPOINT` must be mapped to the proxy's IP address (`coolify-proxy` in this case).

By using the `extra_hosts` directive, you would effectively have to hardcode Coolify Proxy's IP address (either as an env variable or through the compose file itself). The problem is — IP addresses aren't permanent. This can get reassigned once your Coolify's proxy (or the server) is restarted — which means you'll have to change something in your config.

This custom [docker-compose.yml](./docker-compose.yml) solves this by automating the IP discovery process. Instead of the `extra_hosts` directive, it uses an expanded `entrypoint` script which configures `/etc/hosts` before starting Logto:

```sh
# Ensure coolify-proxy is reachable.
echo "------------------------------------------------------------"
echo "Pinging coolify-proxy..."
until ping -c1 coolify-proxy &>/dev/null; do
  echo "coolify-proxy is not reachable. Trying again in 5s..."
  echo ""
  sleep 5
  echo "Pinging coolify-proxy..."
done
echo ""

# Extract coolify-proxy IP from ping command
COOLIFY_PROXY_IP=$(ping -c1 coolify-proxy | head -n1 | awk '{print $3}' | tr -d '():')
echo "coolify-proxy IP: $${COOLIFY_PROXY_IP}"

# Add details to /etc/hosts (same effect as the extra_hosts
# directive in the docker-compose file)
echo "Appending to /etc/hosts:"

echo "  $${COOLIFY_PROXY_IP} ${LOGTO_ENDPOINT_FQDN}"
echo "$${COOLIFY_PROXY_IP} ${LOGTO_ENDPOINT_FQDN}" >> /etc/hosts

echo "  $${COOLIFY_PROXY_IP} ${LOGTO_ADMIN_ENDPOINT_FQDN}"
echo "$${COOLIFY_PROXY_IP} ${LOGTO_ADMIN_ENDPOINT_FQDN}" >> /etc/hosts

echo "------------------------------------------------------------"

# Run the logto entrypoint command
npm run cli db seed -- --swe && npm start
```

> [!NOTE]
>
> You must assign the FQDNs as environment variables through `LOGTO_ENDPOINT_FQDN` and `LOGTO_ADMIN_ENDPOINT_FQDN`. These should hold the same value as `LOGTO_ENDPOINT` and `LOGTO_ADMIN_ENDPOINT` respectively... just **without** the protocol (the `https://` prefix)

###
