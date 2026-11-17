# Changelog

I use this changelog to record significant milestones in my homelab build. My current configuration and outstanding work are maintained in [Project Status](docs/project-status.md).

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
