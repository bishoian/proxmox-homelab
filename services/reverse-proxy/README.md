# Reverse Proxy

I use Nginx Proxy Manager (NPM) to provide HTTPS access to selected services on my Debian media-services VM.

## Deployment

I run NPM in a separate Compose project from `/opt/media-stack`.

| Host port | Purpose |
|---:|---|
| 80 | HTTP and ACME HTTP-01 validation |
| 81 | Administration interface, LAN only |
| 443 | HTTPS |

I reserve port 81 and application-management interfaces for LAN access; I do not forward them from my router.

## Configured services

| Service | Public hostname | Docker upstream | Verification |
|---|---|---|---|
| Jellyfin | `m*******.com` | `http://jellyfin:8096` | HTTPS and playback verified |
| Seerr | `requests.m*******.com` | `http://seerr:5055` | External HTTPS request portal verified |

Public hostnames are intentionally masked. TLS terminates at NPM; upstream traffic uses the shared Docker network.

## Docker network

I initially received 502 Bad Gateway responses because NPM and Jellyfin were attached to separate Docker networks. I restored access by connecting NPM to `media-stack_default`.

I updated NPM's separate Compose project to retain its default network and also join the external media network:

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

I retained the existing ports, volumes, environment, and database configuration. I validated the Compose file with `docker compose config --quiet`. After recreating NPM, I confirmed that it was attached to both `media-stack_default` and `nginx-proxy-manager_default`.

I maintain the separate NPM Compose file locally on the VM and do not include it in this repository.

## NPM DNS

NPM initially could not resolve the Let's Encrypt API from inside its container, although my Debian host could. I added explicit upstream resolvers (`1.1.1.1` and `8.8.8.8`) to the NPM service, which restored DNS resolution. I traced a later ACME challenge failure to a mistyped value and corrected it.

See [Docker DNS and reverse-proxy troubleshooting](../../troubleshooting/docker-dns-and-reverse-proxy.md) for the diagnostic sequence.

## Validation

- [x] NPM runs and publishes ports 80, 81, and 443.
- [x] Jellyfin HTTPS route and certificate verified.
- [x] Seerr HTTPS request endpoint verified externally.
- [x] NPM shares the media Docker network.
- [x] Network attachment persists after NPM container recreation.
- [x] NPM resolves the Let's Encrypt API.
- [ ] I will reboot my Debian VM and verify that NPM remains attached to both Docker networks.
- [ ] I will select and test a Dynamic DNS updater.
- [ ] I will review my router's port-forwarding rules and document which services I expose publicly.
