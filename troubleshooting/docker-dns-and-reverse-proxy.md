# Troubleshooting: Docker DNS and Reverse Proxy

This record covers the Docker networking and DNS issues encountered while deploying the media stack and Nginx Proxy Manager (NPM).

## NPM returned 502 Bad Gateway

**Symptom:** Jellyfin worked locally, but the NPM proxy host returned 502.

**Cause:** NPM and Jellyfin were attached to separate Docker bridge networks. NPM could not resolve and reach the Jellyfin service name.

**Fix:** Connected NPM to `media-stack_default`, then made the attachment persistent in NPM's separate Compose project. NPM retained its default network and joined the external media network.

**Verification:** The Compose configuration passed `docker compose config --quiet`. After recreating NPM, inspection confirmed attachment to both `nginx-proxy-manager_default` and `media-stack_default`. The Jellyfin HTTPS endpoint returned an HTTP/2 302 response.

## NPM could not resolve the Let's Encrypt API

**Symptom:** Debian could resolve `acme-v02.api.letsencrypt.org`, but the NPM container could not resolve it through Docker's embedded resolver at `127.0.0.11`.

**Cause:** DNS forwarding failed inside the container; host connectivity was working.

**Fix:** Added explicit upstream resolvers (`1.1.1.1` and `8.8.8.8`) to the NPM service and recreated it.

**Verification:** NPM resolved the Let's Encrypt API hostname and the ACME request advanced to challenge validation. A later challenge failure was traced to a mistyped value and corrected. Jellyfin and Seerr HTTPS routes were subsequently verified.

## Radarr, Sonarr, and Prowlarr lookup failures

**Symptom:** Radarr and Sonarr could not resolve `api.radarr.video`; Prowlarr could not resolve `indexers.prowlarr.com`. The applications returned DNS errors through Docker's embedded resolver.

**Diagnosis:** Debian host DNS worked. Direct DNS queries from the containers to the router and public resolvers also succeeded, and direct-IP HTTPS from Radarr returned HTTP 200. This isolated the issue to Docker's embedded-DNS forwarding path.

**Fix:** Added explicit upstream resolvers to the Radarr, Sonarr, and Prowlarr Compose services:

```yaml
dns:
  - 1.1.1.1
  - 8.8.8.8
```

Gluetun and qBittorrent were not given these overrides so Gluetun could retain control of VPN-side DNS.

**Verification:** After recreating the affected containers, Radarr and Sonarr resolved `api.radarr.video`; Radarr search returned results. Prowlarr resolved `indexers.prowlarr.com`, and HTTPS to that endpoint returned HTTP 200.

## Lessons

- Compare host and container DNS before changing router or host networking.
- Use direct DNS queries and direct-IP connectivity to distinguish name-resolution failures from routing failures.
- Containers in separate Docker networks cannot automatically resolve one another by Compose service name.
- When sharing an external Docker network, retain each Compose project's existing network and declare the shared network explicitly.
- Keep VPN gateway DNS behavior separate from ordinary application DNS overrides.

See [Media Stack](../services/media-stack/README.md) and [Reverse Proxy](../services/reverse-proxy/README.md) for the current configuration.
