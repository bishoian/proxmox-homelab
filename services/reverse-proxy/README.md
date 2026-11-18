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

## Planned household services

I am preparing two additional NPM proxy hosts for remote household access. I will use separate subdomains under my existing domain and keep the actual hostnames masked in this repository:

| Service | Redacted hostname | Docker upstream | NPM settings | Status |
|---|---|---|---|---|
| Mealie | `recipes.m*******.com` | `http://mealie:9000` | Request certificate, enable Force SSL and HTTP/2 | Prepared in Compose; proxy host and deployment pending |
| Audiobookshelf | `books.m*******.com` | `http://audiobookshelf:80` | Request certificate, enable Force SSL, HTTP/2, and WebSocket Support | Prepared in Compose; proxy host and deployment pending |

Both services remain on `media-stack_default`, which NPM already joins. I removed their direct host-port publications so the intended public path is NPM on TCP 443 rather than separate exposed application ports. Their Compose DNS settings retain the explicit upstream resolvers I use for containers that need outbound DNS.

For Mealie, I will set the local `MEALIE_BASE_URL` to its final HTTPS URL before deployment. I will keep public registration disabled and create a separate household account for my girlfriend. I will use Mealie's built-in authentication rather than sharing the administrator account.

For Audiobookshelf, I will enable WebSocket Support in the NPM proxy-host settings. Audiobookshelf requires WebSocket connections, and its documentation recommends a reverse proxy or VPN for remote access. I will create a separate user with only the library permissions she needs.

I will create public DNS records for both subdomains pointing to my home WAN address. I will use the existing NPM/Let's Encrypt entry point and the existing router forwards for TCP 80/443; I will not forward the application ports, NPM's administration port 81, SMB 445, or any media-management interface. For Mealie uploads, I will review NPM's request-body limit if larger recipe imports or image uploads are rejected. Since I have not deployed Dynamic DNS yet, I will need to keep the public records current if my WAN address changes.

I will verify each service over HTTPS from a device outside my home LAN before marking remote access complete. I will also confirm that direct access to the old application ports is no longer published.

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
- [ ] I will create DNS records and NPM HTTPS proxy hosts for Mealie and Audiobookshelf.
- [ ] I will test both household services over HTTPS from an external network.
- [ ] I will review my router's port-forwarding rules and document which services I expose publicly.
