# Troubleshooting: Docker DNS and Reverse Proxy

I encountered the following Docker networking and DNS issues while deploying my media stack and Nginx Proxy Manager (NPM). This record explains what I observed, how I isolated each issue, and how I verified the fixes.

## NPM returned 502 Bad Gateway

**What I observed:** Jellyfin worked locally, but my NPM proxy host returned 502.

**What I found:** I had NPM and Jellyfin attached to separate Docker bridge networks, so NPM could not resolve and reach the Jellyfin service name.

**What I changed:** I connected NPM to `media-stack_default` and made that attachment persistent in NPM's separate Compose project. NPM retained its default network and joined the external media network.

**How I verified it:** I ran `docker compose config --quiet`, recreated NPM, and inspected its network attachments. It was connected to both `nginx-proxy-manager_default` and `media-stack_default`. The Jellyfin HTTPS endpoint returned an HTTP/2 302 response.

## NPM could not resolve the Let's Encrypt API

**What I observed:** Debian could resolve `acme-v02.api.letsencrypt.org`, but my NPM container could not resolve it through Docker's embedded resolver at `127.0.0.11`.

**What I found:** Host connectivity was working, but DNS forwarding failed inside the container.

**What I changed:** I added explicit upstream resolvers (`1.1.1.1` and `8.8.8.8`) to the NPM service and recreated it.

**How I verified it:** NPM resolved the Let's Encrypt API hostname and the ACME request advanced to challenge validation. I traced a later challenge failure to a mistyped value and corrected it. I subsequently verified the Jellyfin and Seerr HTTPS routes.

## Radarr, Sonarr, and Prowlarr lookup failures

**What I observed:** Radarr and Sonarr could not resolve `api.radarr.video`, and Prowlarr could not resolve `indexers.prowlarr.com`. The applications returned DNS errors through Docker's embedded resolver.

**How I isolated it:** Debian host DNS worked. Direct DNS queries from the containers to the router and public resolvers also succeeded, and direct-IP HTTPS from Radarr returned HTTP 200. These tests isolated the issue to Docker's embedded-DNS forwarding path.

**What I changed:** I added explicit upstream resolvers to the Radarr, Sonarr, and Prowlarr Compose services:

```yaml
dns:
  - 1.1.1.1
  - 8.8.8.8
```

I did not add these overrides to Gluetun or qBittorrent because I wanted Gluetun to retain control of VPN-side DNS.

**How I verified it:** After recreating the affected containers, I confirmed that Radarr and Sonarr resolved `api.radarr.video` and that Radarr search returned results. Prowlarr resolved `indexers.prowlarr.com`, and HTTPS to that endpoint returned HTTP 200.

## Lessons

- I compare host and container DNS before changing router or host networking.
- I use direct DNS queries and direct-IP connectivity to distinguish name-resolution failures from routing failures.
- I account for the fact that containers on separate Docker networks cannot automatically resolve one another by Compose service name.
- When I share an external Docker network, I retain each Compose project's existing network and declare the shared network explicitly.
- I keep VPN gateway DNS behavior separate from ordinary application DNS overrides.

See [Media Stack](../services/media-stack/README.md) and [Reverse Proxy](../services/reverse-proxy/README.md) for the current configuration.
