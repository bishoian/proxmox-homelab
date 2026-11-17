# Media Stack

Docker Compose project hosted on the Debian 13 media-services VM at `/opt/media-stack`.

## Services

| Service | Purpose | LAN port |
|---|---|---:|
| Jellyfin | Media streaming | 8096 |
| Radarr | Movie management | 7878 |
| Sonarr | TV management | 8989 |
| Prowlarr | Indexer management | 9696 |
| qBittorrent | Download client | 8080 via Gluetun |
| Gluetun | ExpressVPN gateway and firewall | Shared with qBittorrent |
| Seerr | Media request portal | 5055 |
| Mealie | Recipes and meal planning | 9925 |
| Audiobookshelf | Audiobooks and podcasts | 13378 |

Jellyfin, Radarr, Sonarr, Prowlarr, qBittorrent, Gluetun, and Seerr are deployed. Mealie and Audiobookshelf are defined in Compose but not yet deployed.

## Storage and permissions

- Application configuration and databases are stored under `/opt/media-stack/config` on the VM's NVMe-backed system disk.
- The Seagate ext4 filesystem is mounted at `/srv/media`.
- Radarr, Sonarr, and qBittorrent share the same host data tree at container path `/data`.
- Jellyfin receives `/srv/media/data/media` read-only at `/media`.
- LinuxServer containers use PUID/PGID 1000 and UMASK 002. Jellyfin runs as UID/GID 1000:1000.
- The shared `/data` path keeps downloads and library folders on one filesystem, allowing hardlinks where configured. Hardlink/inode behavior has not yet been directly tested.

The directory tree is owned by UID/GID 1000 (`ian:ian`), with setgid directories. A non-root Alpine container write/delete test passed.

## Deployment and access

The Compose definition is version-controlled at [compose.yaml](compose.yaml). The VM's local `.env` contains ExpressVPN credentials and is excluded from Git; `.env.example` documents the required variable names without credentials.

Use the VM's LAN address with the ports above. These administration interfaces should remain LAN-only. Do not forward them directly from the router. qBittorrent's Web UI is published through Gluetun on TCP 8080; Radarr and Sonarr connect to it at `http://gluetun:8080`.

Replace qBittorrent's temporary first-start administrator password during setup. Never commit credentials.

## Application verification

- Radarr and Sonarr correctly import and sort media with the expected metadata.
- Prowlarr's application connections to Radarr and Sonarr passed their connection tests.
- Seerr is configured with the Jellyfin Movies and Shows libraries and the Radarr/Sonarr servers. Its external HTTPS request endpoint has been verified.
- Jellyfin automatically refreshes its library and successfully played a newly downloaded movie from a client.
- After a full Debian VM reboot, Gluetun returned healthy, qBittorrent remained bound to `tun0`, VPN egress was restored, and Radarr/Sonarr reached the qBittorrent Web UI.

## VPN isolation

Gluetun connects to ExpressVPN using OpenVPN. qBittorrent uses `network_mode: service:gluetun`, waits for Gluetun's health check, and binds its network interface to `tun0`. The local environment file is root-owned with mode `600`; credentials are not stored in this repository.

The kill switch was tested with downloads paused:

1. Gluetun established the tunnel and reported healthy.
2. qBittorrent shared Gluetun's network namespace, and its public IP matched the VPN egress IP.
3. Radarr and Sonarr each reached the qBittorrent Web UI through `gluetun:8080` with HTTP 200.
4. Bringing `tun0` down caused a DNS-based public-IP request to fail.
5. A direct-IP HTTPS request to `1.1.1.1` returned HTTP 000 and timed out, confirming the failure was not DNS-only.
6. Recreating the services restored Gluetun health, qBittorrent operation, and VPN egress.

This verifies network-level fail-closed behavior. It does not mean qBittorrent's interface automatically marks jobs as paused when the tunnel drops.

ExpressVPN does not provide VPN-server port forwarding, which can limit incoming peer connectivity and seeding reachability. Outbound downloads and the tested kill-switch behavior do not require a provider change.

## Remaining work

- [ ] Deploy Mealie and Audiobookshelf; complete first-run setup and verify persistent storage.
- [ ] Verify hardlink/inode behavior between download and library paths.
- [ ] Repeat the VPN outage test after future Gluetun or qBittorrent image updates.
- [ ] Document routine service updates, health checks, and recovery.
- [ ] Add backups for application configuration and important media data.

## Troubleshooting

Docker DNS failures affecting Radarr, Sonarr, Prowlarr, and NPM were resolved with explicit upstream resolvers. NPM's initial 502 was caused by separate Docker networks. See [Docker DNS and reverse-proxy troubleshooting](../../troubleshooting/docker-dns-and-reverse-proxy.md).

## References

- [Jellyfin container documentation](https://jellyfin.org/docs/general/installation/container/)
- [LinuxServer Radarr](https://docs.linuxserver.io/images/docker-radarr/)
- [LinuxServer qBittorrent](https://docs.linuxserver.io/images/docker-qbittorrent/)
- [LinuxServer Prowlarr](https://docs.linuxserver.io/images/docker-prowlarr/)
- [Gluetun ExpressVPN setup](https://github.com/qdm12/gluetun-wiki/blob/main/setup/providers/expressvpn.md)
- [Gluetun firewall](https://github.com/qdm12/gluetun-wiki/blob/main/faq/firewall.md)
- [Docker Compose networking](https://docs.docker.com/compose/how-tos/networking/)
- [Mealie SQLite installation](https://mealie.io/documentation/getting-started/installation/sqlite/)
- [Audiobookshelf Docker installation](https://audiobookshelf.org/docs/documentation/install/docker/)
