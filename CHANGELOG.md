# Changelog

A short record of significant project milestones. Current configuration and outstanding tasks are maintained in [Project Status](docs/project-status.md).

## 2026-11-16

### Media stack verification
- Verified Radarr and Sonarr import and sort media correctly with expected metadata.
- Verified Jellyfin automatic library refresh and playback using a newly downloaded movie.
- Verified Seerr's external HTTPS request portal.
- Added Mealie and Audiobookshelf service definitions to Compose; deployment remains pending.

### Troubleshooting and reliability
- Corrected Docker DNS forwarding failures affecting Radarr, Sonarr, Prowlarr, and Nginx Proxy Manager.
- Corrected NPM's Docker network attachment and verified the reverse-proxy routes.
- Tested Gluetun's fail-closed behavior by taking down `tun0`; direct-IP HTTPS egress was blocked.
- Recreated the VPN services and rebooted the Debian VM; Gluetun and qBittorrent recovered.

## 2026-11-15

### Platform and storage
- Created the public repository and documented the hardware and network design.
- Installed Proxmox VE on the M920q, configured package repositories, applied updates, and verified the updated kernel after reboot.
- Created Debian VM 100 and verified SSH, DNS, APT, sudo, and QEMU Guest Agent connectivity.
- Diagnosed the D4-320's missing disk: its rear power switch was off.
- Passed the Seagate disk through to Debian, created a GPT partition and ext4 filesystem, and configured the `/srv/media` mount.
- Set shared media-directory ownership and permissions; verified container write access.

### Applications and remote access
- Deployed the initial Docker media stack and completed first-run setup.
- Configured Nginx Proxy Manager and corrected its Docker network connectivity to Jellyfin.

Earlier diagnostic detail is retained in the dedicated troubleshooting records and service documentation.
