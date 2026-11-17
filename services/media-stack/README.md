# Media Stack

Docker Compose project for the Debian 13 media-services VM.

## Services

| Service | Purpose | LAN web UI |
|---|---|---:|
| Jellyfin | Media streaming | 8096 |
| Radarr | Movie library management | 7878 |
| Sonarr | Television library management | 8989 |
| Prowlarr | Indexer management | 9696 |
| Gluetun | ExpressVPN gateway and firewall | Shared with qBittorrent |
| qBittorrent | Download client, routed through Gluetun | 8080 via Gluetun |
| Seerr | Jellyfin-connected media request portal using Radarr and Sonarr | 5055 |
| Mealie | Self-hosted recipes, meal planning, and shopping lists | 9925 |
| Audiobookshelf | Audiobook and podcast library/server | 13378 |

## Storage design

- Application configuration and Jellyfin cache live under this project directory on the VM's NVMe-backed virtual system disk.
- The Seagate ext4 filesystem is mounted at `/srv/media`.
- Radarr, Sonarr, and qBittorrent all see the same host data tree at the in-container path `/data`.
- Jellyfin receives only `/srv/media/data/media`, mounted read-only at `/media`.
- Consistent `/data` paths keep download and library directories on the same filesystem, allowing hardlinks where the applications and download client are configured to use them.
- LinuxServer containers use PUID/PGID 1000 and UMASK 002 to align with the Debian `ian` account and shared setgid directories.
- Jellyfin runs as UID/GID 1000:1000.
- qBittorrent shares Gluetun's network namespace rather than using an independent Docker network route. Its configured network interface is `tun0`.

## Deployment location

On the Debian VM, the Compose project is stored at `/opt/media-stack`:

- `compose.yaml` — service definitions
- `config/<service>/` — persistent application settings and databases
- `cache/jellyfin/` — Jellyfin cache
- `config/seerr/`, `config/mealie/`, and `config/audiobookshelf/` — persistent application state on the VM's NVMe-backed disk
- `.env` — local ExpressVPN manual OpenVPN credentials, excluded from Git; use `.env.example` as the template

## Web interfaces

Use the Debian VM's current LAN address with the ports listed above. The qBittorrent Web UI is published by Gluetun on TCP 8080; Radarr and Sonarr reach it at `http://gluetun:8080`. These interfaces are intended for trusted LAN access. Do not configure router port forwarding for the administration interfaces. Set strong application credentials during first-run configuration.

Mealie is published on LAN TCP 9925 and Audiobookshelf on LAN TCP 13378. Seerr is published on LAN TCP 5055. Do not forward these ports directly from the router; use NPM if external access is later desired.

The qBittorrent LinuxServer image prints a temporary Web UI password in its container log on first startup. Use the locally displayed first-start credential to sign in as `admin`, then immediately replace it with a strong unique password in qBittorrent settings. Never commit this temporary credential or the replacement password to this repository.

## Deployment status

The Compose definition is stored at `/opt/media-stack/compose.yaml` on the Debian VM and is maintained in this repository. Application configuration is kept under the project directory on the VM's NVMe-backed system disk; the Seagate filesystem stores media and downloads.

The original Jellyfin, Radarr, Sonarr, Prowlarr, Gluetun, and qBittorrent services are deployed. Gluetun is healthy after a full Debian VM reboot, qBittorrent remains bound to `tun0`, and Radarr/Sonarr can reach the qBittorrent Web UI through `gluetun:8080`. Radarr and Sonarr have explicit DNS resolvers, as does Prowlarr, to work around the Docker embedded-DNS forwarding issue encountered during setup.

Seerr has been added to the same Compose project. Its container started successfully and reported healthy. The local setup wizard connected Seerr to the Jellyfin Movies and Shows libraries and configured the Radarr and Sonarr servers. Local Seerr access is working. Public access through `requests.moxxboxx.com` remains subject to final DNS, certificate, and external-client verification.

Mealie and Audiobookshelf service definitions have now been added to the version-controlled Compose file, but their containers have not yet been deployed. Mealie uses a pinned stable image and local SQLite-backed application storage. Audiobookshelf stores its database/configuration and metadata on the VM's NVMe-backed disk and reads audiobook/podcast content from separate Seagate-backed directories. Their deployment and first-run setup remain pending.

Prowlarr's Radarr/Sonarr application connection tests succeeded. A filesystem inspection found a movie file under the configured movie library root, while the downloaded TV season folders were still in the torrent download area and the TV library root was empty. Sonarr's TV import workflow, hardlink verification, Jellyfin playback, and automatic library-refresh behavior therefore remain open validation items.

## Container DNS troubleshooting

During Radarr movie lookup, Radarr and Sonarr returned DNS failures for `api.radarr.video` through Docker's embedded resolver at `127.0.0.11`. Prowlarr separately returned DNS failures for `indexers.prowlarr.com` and could not download indexer definitions. Debian itself resolved the hostname and made HTTPS requests successfully. Direct DNS queries from the Radarr container to the router resolver and public resolvers (Cloudflare and Google) also succeeded, and a direct-IP HTTPS request from Radarr returned HTTP 200. This isolated the issue to Docker's normal embedded-DNS forwarding path for those application containers.

The Compose services for Radarr, Sonarr, and Prowlarr now specify explicit upstream resolvers:

```yaml
dns:
  - 1.1.1.1
  - 8.8.8.8
```

After recreating Radarr and Sonarr, Docker's embedded resolver (`127.0.0.11`) successfully resolved `api.radarr.video` from both, and Radarr search returned the Big Buck Bunny movie result. Prowlarr initially showed the same `SERVFAIL` behavior for its indexer-definition host. It now has the same explicit DNS resolvers; after recreation, its embedded resolver successfully resolved `indexers.prowlarr.com` and HTTPS to that endpoint returned HTTP 200. These explicit DNS settings apply only to Radarr, Sonarr, and Prowlarr. Gluetun and qBittorrent are intentionally not given these overrides so Gluetun retains control of VPN-side DNS. Jellyfin DNS has not required troubleshooting.

## qBittorrent VPN isolation

**Deployment status:** Gluetun and qBittorrent are deployed together in the media-stack Compose project. Gluetun connects to ExpressVPN using OpenVPN. The provider is selected with `VPN_SERVICE_PROVIDER=expressvpn`, and the country selector uses `SERVER_COUNTRIES=USA`. The manual OpenVPN username and password are stored only in the VM's local `/opt/media-stack/.env` file. That file has `600` permissions and ownership `root:root`; no VPN credentials are committed to this repository.

### Network design

- Gluetun runs with `NET_ADMIN` and `/dev/net/tun` and receives environment variables through the local `.env` file.
- Gluetun publishes TCP port 8080 for the qBittorrent Web UI and permits that input port through its default interface.
- qBittorrent uses `network_mode: service:gluetun` and waits for Gluetun's health check before starting.
- qBittorrent does not publish independent ports. Its existing configuration and download/media bind mounts are retained.
- qBittorrent's own **Network interface** setting is bound to `tun0`.
- Radarr and Sonarr use `gluetun` as the qBittorrent download-client host on port 8080. Requests from each application to the Web UI returned HTTP 200.
- The qBittorrent Web UI remains a LAN-facing administration interface. Do not forward port 8080 from the router or publish it through the public reverse proxy.

### Kill-switch validation

The VPN path and recovery were tested while qBittorrent downloads were paused:

1. Gluetun established the ExpressVPN OpenVPN tunnel and reported healthy. Its logs showed `tun0` initialized and a public egress IP returned through the tunnel.
2. qBittorrent was confirmed to share Gluetun's network namespace. The `tun0` interface was visible from inside qBittorrent, and its public IP check matched Gluetun's reported VPN egress IP.
3. Radarr and Sonarr each reached the qBittorrent Web UI through `http://gluetun:8080` and received HTTP 200.
4. The `tun0` interface was deliberately brought down inside Gluetun. A DNS-based public-IP request failed at name resolution.
5. A second test used a direct IP URL (`https://1.1.1.1/cdn-cgi/trace`) to avoid relying on DNS. The request returned HTTP 000 and timed out after approximately three seconds. This confirms outbound traffic was blocked while the VPN interface was down, rather than the first failure being DNS-only.
6. Gluetun and qBittorrent were recreated with Docker Compose. Gluetun returned to healthy, qBittorrent returned to running, and a new public VPN egress IP was retrieved.

This is a network-level fail-closed test. The firewall blocks traffic when the VPN route is unavailable; it does not necessarily change qBittorrent's UI state to visibly mark jobs as paused.

### Remaining validation

- [x] Verify Gluetun and qBittorrent recover correctly after a full Debian VM reboot.
- [x] Re-check qBittorrent interface binding and Radarr/Sonarr download-client connectivity after reboot; `tun0` remained present and both application requests returned HTTP 200.
- [ ] Verify the complete Radarr/Sonarr download-to-library workflow with authorized test media; one movie was present in the movie library root, while TV imports remained pending.
- [ ] Repeat the tunnel outage test after future Gluetun or qBittorrent image updates.

ExpressVPN does not provide VPN-server port forwarding. This does not prevent outbound downloads, but can limit incoming peer connectivity and seeding reachability. A provider with supported P2P port forwarding may be evaluated later if that becomes important; a provider change is not required for the tested kill-switch behavior.

References: [Gluetun ExpressVPN setup](https://github.com/qdm12/gluetun-wiki/blob/main/setup/providers/expressvpn.md), [Gluetun firewall / kill switch](https://github.com/qdm12/gluetun-wiki/blob/main/faq/firewall.md), [Gluetun firewall options](https://github.com/qdm12/gluetun-wiki/blob/main/setup/options/firewall.md), [Docker Compose environment files](https://docs.docker.com/compose/how-tos/environment-variables/set-environment-variables/), [Docker Compose network mode](https://docs.docker.com/reference/compose-file/services/#network_mode), and [ExpressVPN VPN-server port forwarding](https://www.expressvpn.com/blog/vpn-ports/).

## References

- [Jellyfin container documentation](https://jellyfin.org/docs/general/installation/container/)
- [LinuxServer Radarr documentation](https://docs.linuxserver.io/images/docker-radarr/)
- [LinuxServer qBittorrent documentation](https://docs.linuxserver.io/images/docker-qbittorrent/)
- [LinuxServer Prowlarr documentation](https://docs.linuxserver.io/images/docker-prowlarr/)


## Mealie and Audiobookshelf deployment plan

The Compose file defines both applications, but deployment is not complete until the directories are created and the containers are started and checked.

- Mealie uses the official `ghcr.io/mealie-recipes/mealie:v3.31.0` image, publishes host TCP 9925 to container TCP 9000, stores data under `./config/mealie`, and uses SQLite for this small household deployment. Sign-up is disabled; change the documented first-run default password immediately after the first login.
- Audiobookshelf uses `ghcr.io/advplyr/audiobookshelf:latest`, publishes host TCP 13378 to container TCP 80, and runs as UID/GID 1000:1000. Its `/config` database and `/metadata` directories are stored on the VM's local system disk. The separate `/srv/media/data/audiobooks` and `/srv/media/data/podcasts` directories are mounted as the media libraries.
- Both services use explicit DNS resolvers because of the Docker DNS forwarding issue previously observed with other containers.
- Keep ports 9925 and 13378 LAN-only. Use the reverse proxy for any future remote access rather than forwarding application ports directly from the router.

Official setup references: [Mealie SQLite installation](https://mealie.io/documentation/getting-started/installation/sqlite/), [Mealie backend configuration](https://mealie.io/documentation/getting-started/installation/backend-config/), and [Audiobookshelf Docker installation](https://audiobookshelf.org/docs/documentation/install/docker/).
