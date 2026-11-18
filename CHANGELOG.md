# Changelog

## 2026-11-17

### Household applications configured and exposed over HTTPS
- I deployed Mealie and Audiobookshelf from the media-stack Compose project.
- I confirmed that Mealie reported healthy and that both services returned HTTP 200 over `media-stack_default`.
- I completed administrator and first-run setup for both applications.
- I configured their NPM proxy hosts, updated and verified their HTTPS certificates, and confirmed that both HTTPS routes work.
- Functional recipe import, audiobook/podcast library scanning, playback, and persistence checks remain on my validation list.

I use this changelog to record significant milestones in my homelab build. My current configuration and outstanding work are maintained in [Project Status](docs/project-status.md).

## 2026-11-17

### Shared storage and LAN file sharing
- I installed the second 6 TB Seagate disk in the D4-320, passed it through to Debian VM 100 as a separate SCSI device, and checked its initial SMART status.
- I created a GPT partition and ext4 filesystem labeled `shared`, then configured a UUID-based mount at `/srv/shared`.
- I created a shared Linux group and directory structure for games, disc images, backups, documents, installers, and general files.
- I deployed Samba in the media-stack Compose project with separate authenticated accounts and LAN-only SMB access.
- I verified local SMB read/write operations for both accounts and connected my Windows PC to the share. My girlfriend's separate-PC test remains pending until she moves in.

## 2026-11-17

### Household services prepared for deployment
- I retained the explicit Docker DNS resolvers for Mealie and Audiobookshelf.
- I removed their direct host-port publications so Nginx Proxy Manager is their intended access path.
- I configured Audiobookshelf's library mounts as read-only and documented its required WebSocket proxy setting.
- At this point, I had prepared their Compose definitions and documented separate HTTPS hostnames, account setup, and external-network verification. I deployed and configured both services later, as recorded in the newer entry above.

## 2026-11-16

### Media stack verification
- I verified that Radarr and Sonarr import and sort media correctly with the expected metadata.
- I verified Jellyfin's automatic library refresh and playback using a newly downloaded movie.
- I verified Seerr's external HTTPS request portal.
- I added Mealie and Audiobookshelf service definitions to Compose; I have not deployed them yet.

### Troubleshooting and reliability
- I corrected Docker DNS forwarding failures affecting Radarr, Sonarr, Prowlarr, and Nginx Proxy Manager.
- I corrected NPM's Docker network attachment and verified the reverse-proxy routes.
- I tested Gluetun's fail-closed behavior by taking down `tun0` and confirming that direct-IP HTTPS egress was blocked.
- I recreated the VPN services and rebooted the Debian VM; Gluetun and qBittorrent recovered.

## 2026-11-15

### Platform and storage
- I created the public repository and documented my hardware and network design.
- I installed Proxmox VE on the M920q, configured package repositories, applied updates, and verified the updated kernel after reboot.
- I created Debian VM 100 and verified SSH, DNS, APT, sudo, and QEMU Guest Agent connectivity.
- I diagnosed why the D4-320 disk was missing and found that its rear power switch was off.
- I passed the Seagate disk through to Debian, created a GPT partition and ext4 filesystem, and configured the `/srv/media` mount.
- I set shared media-directory ownership and permissions and verified container write access.

### Applications and remote access
- I deployed the initial Docker media stack and completed first-run setup.
- I configured Nginx Proxy Manager and corrected its Docker network connectivity to Jellyfin.

Earlier diagnostic detail is retained in the dedicated troubleshooting records and service documentation.
