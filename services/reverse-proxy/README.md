# Reverse Proxy

Nginx Proxy Manager (NPM) provides HTTPS access to selected services on the Debian media-services VM.

## Deployment

NPM runs in a separate Compose project from `/opt/media-stack`.

| Host port | Purpose |
|---:|---|
| 80 | HTTP and ACME HTTP-01 validation |
| 81 | Administration interface, LAN only |
| 443 | HTTPS |

Do not forward port 81 or application-management interfaces from the router.

## Configured services

| Service | Public hostname | Docker upstream | Verification |
|---|---|---|---|
| Jellyfin | `m*******.com` | `http://jellyfin:8096` | HTTPS and playback verified |
| Seerr | `requests.m*******.com` | `http://seerr:5055` | External HTTPS request portal verified |

Public hostnames are intentionally masked. TLS terminates at NPM; upstream traffic uses the shared Docker network.

## Docker network

NPM initially returned 502 Bad Gateway because its container and Jellyfin were on separate Docker networks. Connecting NPM to `media-stack_default` restored access.

The separate NPM Compose project was updated to retain its default network and also join the external media network:

```yaml
services:
  app:
    networks:
      - default
      - media_stack

networks:
  media_stack:
    external: true
    name: media-stack_default
```

The existing ports, volumes, environment, and database configuration were retained. The Compose file passed `docker compose config --quiet`. After recreation, NPM was confirmed on both `media-stack_default` and `nginx-proxy-manager_default`.

The separate NPM Compose file is maintained locally on the VM and is not included in this repository.

## NPM DNS

NPM initially could not resolve the Let's Encrypt API from inside its container, although the Debian host could. Explicit upstream resolvers (`1.1.1.1` and `8.8.8.8`) were added to the NPM service. DNS resolution then succeeded. A later ACME challenge failure was traced to a mistyped value and corrected.

See [Docker DNS and reverse-proxy troubleshooting](../../troubleshooting/docker-dns-and-reverse-proxy.md) for the diagnostic sequence.

## Validation

- [x] NPM runs and publishes ports 80, 81, and 443.
- [x] Jellyfin HTTPS route and certificate verified.
- [x] Seerr HTTPS request endpoint verified externally.
- [x] NPM shares the media Docker network.
- [x] Network attachment persists after NPM container recreation.
- [x] NPM resolves the Let's Encrypt API.
- [ ] Verify network attachment after a full Debian VM reboot.
- [ ] Select and test a Dynamic DNS updater.
- [ ] Review router port-forwarding rules and document the public exposure boundary.
