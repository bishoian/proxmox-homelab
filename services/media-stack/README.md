# Media Stack

I host this Docker Compose project on my Debian 13 media-services VM at `/opt/media-stack`.

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
| Samba | Authenticated LAN file sharing for `/srv/shared` | 445 (LAN only) |
| Mealie | Recipes and meal planning | 9000 (Docker network only) |
| Audiobookshelf | Audiobooks and podcasts | 80 (Docker network only) |

I have deployed Jellyfin, Radarr, Sonarr, Prowlarr, qBittorrent, Gluetun, Seerr, and Samba. I have prepared Mealie and Audiobookshelf in Compose, including their Docker DNS settings and reverse-proxy-only network access, but I have not deployed them yet.

## Storage and permissions

- Application configuration and databases are stored under `/opt/media-stack/config` on the VM's NVMe-backed system disk.
- The Seagate ext4 filesystem is mounted at `/srv/media`.
- Radarr, Sonarr, and qBittorrent share the same host data tree at container path `/data`.
- Jellyfin receives `/srv/media/data/media` read-only at `/media`.
- LinuxServer containers use PUID/PGID 1000 and UMASK 002. Jellyfin runs as UID/GID 1000:1000.
- The shared `/data` path keeps downloads and library folders on one filesystem, allowing hardlinks where configured. Hardlink/inode behavior has not yet been directly tested.

I set the directory tree ownership to UID/GID 1000 (`ian:ian`) and configured setgid directories. I verified non-root write access by creating and deleting a test file from an Alpine container.

## Deployment and access

I keep the Compose definition in version control at [compose.yaml](compose.yaml). My VM's local `.env` file contains ExpressVPN credentials and is excluded from Git; `.env.example` documents the required variable names without exposing credentials.

Use the VM's LAN address with the ports above. These administration interfaces should remain LAN-only. Do not forward them directly from the router. qBittorrent's Web UI is published through Gluetun on TCP 8080; Radarr and Sonarr connect to it at `http://gluetun:8080`.

Replace qBittorrent's temporary first-start administrator password during setup. Never commit credentials.

## Shared storage and Samba

I added the second D4-320 disk as a separate ext4 filesystem mounted at `/srv/shared`. I keep it independent from the media/download filesystem at `/srv/media`. I created top-level folders for Games, ISOs, Backups, Documents, Installers, and General.

I deployed Samba as a separate container in this Compose project. It publishes SMB over TCP 445 on the Debian VM's LAN interface only and shares `/srv/shared` as `Shared`. I require individual credentials, disable guest access, and grant both accounts access through a shared Linux group (GID 1500). The account environment file is stored only on the VM, protected as root-owned mode `600`, and excluded from Git.

I verified that the Samba container became healthy, both accounts were present in Samba's account database and shared group, and both accounts could authenticate and create/remove test directories through a local SMB client. I also connected my Windows PC and verified access to the share. I have not tested my girlfriend's separate PC because she has not moved in yet.

The Windows connection uses `\\<Debian-LAN-address>\Shared`; I mapped it as a persistent network drive on my PC. I do not publish the server's private LAN address, disk serials, or filesystem UUIDs here.

## Mealie

I have prepared Mealie as the recipe and meal-planning service for my household. I use the official SQLite-based container configuration, with application data persisted under `./config/mealie` on the VM's NVMe-backed disk. I set PUID/PGID to 1000, restrict open registration, and configure the initial administrator email and HTTPS base URL through local environment variables. I retain explicit DNS resolvers (`1.1.1.1` and `8.8.8.8`) because I previously diagnosed container DNS forwarding failures in this stack.

I removed Mealie's direct host-port publication. NPM will reach it over `media-stack_default` at `http://mealie:9000`, while remote users will use its dedicated HTTPS hostname. I will create a separate household account for my girlfriend rather than sharing my administrator login. I will verify first-run setup, recipe import, household membership, HTTPS access, and persistence after container recreation.

## Audiobookshelf

I have prepared Audiobookshelf for audiobooks and podcasts. Its configuration database and metadata are persisted under `./config/audiobookshelf` on the VM's NVMe-backed disk. I keep the audiobook and podcast libraries on the Seagate media filesystem at `/srv/media/data/audiobooks` and `/srv/media/data/podcasts`, mounted read-only inside the container.

I will create these host directories with UID/GID 1000 access before deployment. I will keep the service LAN-only until I have verified library scanning, playback, user setup, and persistence.

## Application verification

- I verified that Radarr and Sonarr import and sort media with the expected metadata.
- I tested Prowlarr's application connections to Radarr and Sonarr successfully.
- I connected Seerr to my Jellyfin Movies and Shows libraries and Radarr/Sonarr servers, then verified its external HTTPS request endpoint.
- I verified that Jellyfin automatically refreshes its library and plays a newly downloaded movie from a client.
- After rebooting my Debian VM, I confirmed that Gluetun returned healthy, qBittorrent remained bound to `tun0`, VPN egress was restored, and Radarr/Sonarr could reach the qBittorrent Web UI.

## VPN isolation

I configured Gluetun to connect to ExpressVPN using OpenVPN. qBittorrent uses `network_mode: service:gluetun`, waits for Gluetun's health check, and binds its network interface to `tun0`. I keep the local environment file root-owned with mode `600` and do not store credentials in this repository.

I tested the kill switch with downloads paused:

1. I confirmed that Gluetun established the tunnel and reported healthy.
2. I confirmed that qBittorrent shared Gluetun's network namespace and its public IP matched the VPN egress IP.
3. I confirmed that Radarr and Sonarr each reached the qBittorrent Web UI through `gluetun:8080` with HTTP 200.
4. I brought `tun0` down and confirmed that a DNS-based public-IP request failed.
5. I tested HTTPS directly against `1.1.1.1`; the request returned HTTP 000 and timed out, confirming that the failure was not limited to DNS.
6. I recreated the services and confirmed that Gluetun health, qBittorrent operation, and VPN egress recovered.

This verifies network-level fail-closed behavior. It does not mean qBittorrent's interface automatically marks jobs as paused when the tunnel drops.

ExpressVPN does not provide VPN-server port forwarding, which can limit incoming peer connectivity and seeding reachability. Outbound downloads and the tested kill-switch behavior do not require a provider change.

## Remaining work

- [ ] I will verify the `/srv/shared` mount and Samba service after a full Debian VM reboot.
- [ ] I will test the share from my girlfriend's PC after she moves in.
- [ ] I will deploy Mealie and Audiobookshelf, complete first-run setup, and verify persistent storage.
- [ ] I will configure dedicated NPM HTTPS proxy hosts for both apps and test them from an external network.
- [ ] I will create separate household and audiobook accounts for my girlfriend and verify her remote access.
- [ ] I will verify hardlink/inode behavior between download and library paths.
- [ ] I will repeat the VPN outage test after future Gluetun or qBittorrent image updates.
- [ ] I will document my routine service updates, health checks, and recovery process.
- [ ] I will add backups for application configuration and important media data.

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
