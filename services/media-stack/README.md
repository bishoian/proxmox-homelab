# Media Stack

Docker Compose project for the Debian 13 media-services VM.

## Services

| Service | Purpose | LAN web UI |
|---|---|---:|
| Jellyfin | Media streaming | 8096 |
| Radarr | Movie library management | 7878 |
| Sonarr | Television library management | 8989 |
| Prowlarr | Indexer management | 9696 |
| qBittorrent | Download client | 8080 |

## Storage design

- Application configuration and Jellyfin cache live under this project directory on the VM's NVMe-backed virtual system disk.
- The Seagate ext4 filesystem is mounted at `/srv/media`.
- Radarr, Sonarr, and qBittorrent all see the same host data tree at the in-container path `/data`.
- Jellyfin receives only `/srv/media/data/media`, mounted read-only at `/media`.
- Consistent `/data` paths keep download and library directories on the same filesystem, allowing hardlinks where the applications and download client are configured to use them.
- LinuxServer containers use PUID/PGID 1000 and UMASK 002 to align with the Debian `ian` account and shared setgid directories.
- Jellyfin runs as UID/GID 1000:1000.

## Deployment location

On the Debian VM, the Compose project is stored at `/opt/media-stack`:

- `compose.yaml` — service definitions
- `config/<service>/` — persistent application settings and databases
- `cache/jellyfin/` — Jellyfin cache

## Web interfaces

Use the Debian VM's current LAN address with the ports listed above. These are intended for trusted LAN access. Do not configure router port forwarding for the administration interfaces. Set strong application credentials during first-run configuration.

The qBittorrent LinuxServer image prints a temporary Web UI password in its container log on first startup. Use the locally displayed first-start credential to sign in as `admin`, then immediately replace it with a strong unique password in qBittorrent settings. Never commit this temporary credential or the replacement password to this repository.

## Deployment status

The Compose definition is stored at `/opt/media-stack/compose.yaml` on the Debian VM. Project configuration and cache directories were created, ownership was set to UID/GID 1000, and `docker compose config --quiet` completed with exit status 0. All five images were pulled and all five application containers were started with `docker compose up -d`. The subsequent `docker compose ps` output showed Jellyfin healthy and Radarr, Sonarr, Prowlarr, and qBittorrent running. Initial logs showed normal Jellyfin startup, database migrations for Radarr and Sonarr, Prowlarr listening on its configured port, and qBittorrent Web UI initialization. Initial first-run setup has been completed in all five applications. The qBittorrent temporary administrator password was replaced. Radarr and Sonarr have their root folders configured and use qBittorrent as their download client with separate categories. Prowlarr is configured with Radarr and Sonarr application connections. End-to-end application sync, download/import behavior, hardlink creation, Jellyfin library scans, and playback still need validation.

## Planned qBittorrent VPN isolation

The next privacy and network-isolation improvement is to route qBittorrent through a dedicated VPN gateway container rather than allowing it to use the media VM's ordinary Docker network path.

**Proposed design:** add [Gluetun](https://github.com/qdm12/gluetun) as the VPN gateway and run qBittorrent with `network_mode: service:gluetun`. qBittorrent would share Gluetun's network namespace, and Gluetun's firewall would block outbound traffic that is not allowed through the VPN tunnel. This fail-closed network behavior is the important kill-switch property: when the tunnel is down, torrent traffic has no ordinary-network fallback. The firewall blocks traffic; it does not necessarily change qBittorrent's UI state to a visibly paused state.

**Existing ExpressVPN subscription:** Gluetun documents native ExpressVPN integration using manual OpenVPN credentials and supports UDP or TCP OpenVPN. This is sufficient to build a VPN-enforced qBittorrent container without changing providers. ExpressVPN's consumer apps now list WireGuard among supported protocols, but Gluetun's ExpressVPN provider integration currently documents OpenVPN credentials rather than native ExpressVPN WireGuard setup. Do not assume the app's protocol list means its WireGuard profile can be exported for Gluetun.

**Potential provider change:** ExpressVPN does not provide port forwarding on its VPN servers. This does not prevent ordinary outbound torrent downloads, but it can limit incoming peer connections and seeding reachability. If port forwarding becomes important, evaluate a paid provider with P2P support, WireGuard, and port forwarding; Proton VPN documents these features for paid plans and P2P servers. A provider change is not required just to get a kill switch.

Implementation requirements before deployment:

- Store VPN credentials and private keys in a local environment file or Docker secret, never in Git.
- Move qBittorrent's Web UI and torrent port publication to the Gluetun service; do not publish the Web UI to the public internet.
- Preserve LAN access needed by Radarr and Sonarr to communicate with qBittorrent through the VPN gateway.
- Configure and test the Gluetun firewall's inbound allowances for the Web UI and trusted Docker networks.
- Confirm public IP and DNS-leak tests show VPN egress while connected.
- Simulate a tunnel outage and verify qBittorrent cannot reach the internet through the VM's normal gateway.
- Re-test behavior after container restarts and VM reboots.

This design is planned and is not yet deployed. The current qBittorrent container still uses the normal media-stack Docker network.

References: [Gluetun ExpressVPN setup](https://github.com/qdm12/gluetun-wiki/blob/main/setup/providers/expressvpn.md), [Gluetun firewall / kill switch](https://github.com/qdm12/gluetun-wiki/blob/main/faq/firewall.md), [Gluetun Compose routing guide](https://gluetun.org/guides/docker-compose/), [ExpressVPN VPN-server port forwarding](https://www.expressvpn.com/blog/vpn-ports/), and [Proton VPN port forwarding](https://protonvpn.com/support/port-forwarding).

## References

- [Jellyfin container documentation](https://jellyfin.org/docs/general/installation/container/)
- [LinuxServer Radarr documentation](https://docs.linuxserver.io/images/docker-radarr/)
- [LinuxServer qBittorrent documentation](https://docs.linuxserver.io/images/docker-qbittorrent/)
- [LinuxServer Prowlarr documentation](https://docs.linuxserver.io/images/docker-prowlarr/)
