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

I deployed two additional household applications and configured their NPM proxy hosts. I updated and verified both HTTPS certificates and confirmed that both routes work. I use separate subdomains under my existing domain and keep the actual hostnames masked in this repository:

| Service | Redacted hostname | Docker upstream | NPM settings | Status |
|---|---|---|---|---|
| Mealie | `recipes.m*******.com` | `http://mealie:9000` | Request certificate, enable Force SSL and HTTP/2 | HTTPS certificate and route verified |
| Audiobookshelf | `books.m*******.com` | `http://audiobookshelf:80` | Force SSL, HTTP/2, and WebSocket Support | HTTPS certificate and route verified |

Both services remain on `media-stack_default`, which NPM already joins. I removed their direct host-port publications so the intended public path is NPM on TCP 443 rather than separate exposed application ports. Their Compose DNS settings retain the explicit upstream resolvers I use for containers that need outbound DNS.

For Mealie, I configured the application through its administrator and first-run setup. I will verify recipe import and household functionality, and keep public registration disabled.

For Audiobookshelf, I configured its NPM proxy host with WebSocket Support and verified the HTTPS route. I completed its administrator and first-run setup. I will verify library scanning and playback.

The public DNS records for both subdomains resolve to my home WAN address, and I verified their HTTPS routes through NPM. I use the existing NPM/Let's Encrypt entry point and router forwards for TCP 80/443; I do not forward the application ports, NPM's administration port 81, SMB 445, or any media-management interface. For Mealie uploads, I will review NPM's request-body limit if larger recipe imports or image uploads are rejected. Since I have not deployed Dynamic DNS yet, I will need to keep the public records current if my WAN address changes.

Both services are running and respond over `media-stack_default`; Mealie reports healthy. I completed administrator and first-run setup for both applications, configured their NPM proxy hosts, and verified their HTTPS certificates and routes. I will continue with functional library/import/playback testing and persistence checks. I will also confirm that direct access to application ports is not published.

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
- [x] I configured DNS records and NPM HTTPS proxy hosts for Mealie and Audiobookshelf.
- [x] I updated and verified both HTTPS certificates and routes.
- [x] I completed administrator and first-run setup for both applications.
- [ ] I will verify recipe import, library scanning, playback, and persistent data after container recreation.
- [ ] I will review my router's port-forwarding rules and document which services I expose publicly.
