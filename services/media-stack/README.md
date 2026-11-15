# Media Stack

Docker Compose project for the Debian 13 media-services VM.

## Planned services

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

The qBittorrent LinuxServer image prints a temporary Web UI password in its container log on first startup. Retrieve it from the local Docker log and change it in qBittorrent settings after logging in.

## Deployment status

This Compose definition is the planned stack configuration. It should only be marked deployed after it is copied to the VM, validated with `docker compose config`, started, and each application UI is checked.

## References

- [Jellyfin container documentation](https://jellyfin.org/docs/general/installation/container/)
- [LinuxServer Radarr documentation](https://docs.linuxserver.io/images/docker-radarr/)
- [LinuxServer qBittorrent documentation](https://docs.linuxserver.io/images/docker-qbittorrent/)
- [LinuxServer Prowlarr documentation](https://docs.linuxserver.io/images/docker-prowlarr/)
