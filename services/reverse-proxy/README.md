# Reverse Proxy

Nginx Proxy Manager (NPM) provides the public HTTPS entry point for selected services hosted on the Debian media-services VM.

## Current deployment

NPM runs in its own Docker Compose project, separate from the `/opt/media-stack` Compose project. The current container name is `nginx-proxy-manager-app-1`. Docker publishes these TCP ports on the VM:

| Host port | NPM port | Purpose |
|---:|---:|---|
| 80 | 80 | HTTP and ACME HTTP-01 validation |
| 81 | 81 | NPM administration interface |
| 443 | 443 | HTTPS |

The NPM administration interface on port 81 is for trusted LAN access only. Do not create a router port-forward for port 81. Only the public web ports needed for the proxy should be exposed from the internet.

## Configured proxy host

| Setting | Value |
|---|---|
| Domain | `moxxboxx.com` |
| Upstream scheme | HTTP |
| Upstream host | `jellyfin` |
| Upstream port | `8096` |
| TLS certificate | Let's Encrypt |
| External URL | [https://moxxboxx.com](https://moxxboxx.com) |

The Let's Encrypt certificate request succeeded, and Jellyfin was verified working through the custom domain over HTTPS.

TLS terminates at NPM. NPM communicates with Jellyfin over the Docker bridge network. This is why the upstream scheme is HTTP even though clients use HTTPS externally.

## Docker network connectivity

The NPM Compose project initially used its own default bridge network (`nginx-proxy-manager_default`), while Jellyfin uses `media-stack_default`. The first proxy attempt returned 502 Bad Gateway because the containers did not share a Docker network.

The issue was corrected at runtime by running:

```bash
sudo docker network connect media-stack_default nginx-proxy-manager-app-1
```

NPM can now resolve the Jellyfin service name on the shared network. The runtime attachment was then made persistent in the NPM Compose project at `/home/ian/nginx-proxy-manager/docker-compose.yml`.

### Persistent Compose configuration

The NPM Compose service is named `app` (inferred from the current container name). Add the external media network to the existing `app` service while retaining its existing default network:

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

Merge this into the existing NPM Compose file; do not replace its existing ports, volumes, environment variables, database configuration, or other settings. Validate the Compose file before recreating the container. Docker Compose external networks are managed outside the NPM project lifecycle and can be shared across Compose projects. See the [Docker Compose networking documentation](https://docs.docker.com/compose/how-tos/networking/).

The existing Compose file was inspected before editing. Its image, published ports, restart policy, and persistent bind mounts (`./data` and `./letsencrypt`) were retained. The additional network configuration was merged into the existing file, validated with `sudo docker compose config --quiet`, and applied with `sudo docker compose up -d`.

The recreated container was verified on both `media-stack_default` and `nginx-proxy-manager_default`. A subsequent `curl -I https://moxxboxx.com` returned `HTTP/2 302` from OpenResty, confirming the public HTTPS proxy path remained functional after container recreation. No credentials, database contents, certificate private keys, or other secrets are included in this repository.

## Planned improvements

- [x] Make the `media-stack_default` network attachment persistent in the NPM Compose project.
- [x] Verify NPM remains attached to both Docker networks after container recreation.
- [ ] Verify the network attachment after a full Debian VM reboot.
- [ ] Implement Dynamic DNS for the domain using the selected DNS provider's API or supported updater.
- [ ] Review and document the router's public port-forwarding rules; keep NPM administration and application-management interfaces LAN-only.
- [ ] Consider additional subdomains only when there is a clear need and each service has an appropriate authentication and access-control plan.

Dynamic DNS is not installed yet. Its purpose is to update the public DNS record when the home's public IP address changes. The DNS provider and updater implementation have not been selected.

## Validation checklist

- [x] NPM container is running.
- [x] NPM publishes ports 80, 81, and 443.
- [x] Let's Encrypt certificate issuance completed for `moxxboxx.com`.
- [x] Jellyfin loads at `https://moxxboxx.com`.
- [x] NPM has runtime connectivity to Jellyfin through `media-stack_default`.
- [x] External network configuration committed to the NPM Compose file.
- [x] Persistent network connection tested after container recreation.
- [x] HTTPS endpoint returns a successful redirect after NPM container recreation.
- [ ] Dynamic DNS updater implemented and tested.
