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

On the Debian VM, the Compose project will be stored at `/opt/media-stack`:

- `compose.yaml` — service definitions
- `config/<service>/` — persistent application settings and databases
- `cache/jellyfin/` — Jellyfin cache

## Web interfaces

Use the Debian VM's current LAN address with the ports listed above. These are intended for trusted LAN access. Do not configure router port forwarding for the administration interfaces. Set strong application credentials during first-run configuration.

The qBittorrent LinuxServer image prints a temporary Web UI password in its container log on first startup. Use the locally displayed first-start credential to sign in as `admin`, then immediately replace it with a strong unique password in qBittorrent settings. Never commit this temporary credential or the replacement password to this repository.

## Deployment status

The Compose definition is stored at `/opt/media-stack/compose.yaml` on the Debian VM. Project configuration and cache directories were created, ownership was set to UID/GID 1000, and `docker compose config --quiet` completed with exit status 0. All five images were pulled and all five application containers were started with `docker compose up -d`. The subsequent `docker compose ps` output showed Jellyfin healthy and Radarr, Sonarr, Prowlarr, and qBittorrent running. Initial logs showed normal Jellyfin startup, database migrations for Radarr and Sonarr, Prowlarr listening on its configured port, and qBittorrent Web UI initialization. Initial first-run setup has been completed in all five applications. The qBittorrent temporary administrator password was replaced. Radarr and Sonarr have their root folders configured and use qBittorrent as their download client with separate categories. Prowlarr is configured with Radarr and Sonarr application connections. End-to-end application sync, download/import behavior, hardlink creation, Jellyfin library scans, and playback still need validation.

## References

- [Jellyfin container documentation](https://jellyfin.org/docs/general/installation/container/)
- [LinuxServer Radarr documentation](https://docs.linuxserver.io/images/docker-radarr/)
- [LinuxServer qBittorrent documentation](https://docs.linuxserver.io/images/docker-qbittorrent/)
- [LinuxServer Prowlarr documentation](https://docs.linuxserver.io/images/docker-prowlarr/)
