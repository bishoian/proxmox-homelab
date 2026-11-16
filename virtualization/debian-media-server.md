# Debian Media Server VM Build

**VM ID:** 100  
**VM name:** `media-server`  
**Hypervisor:** Proxmox VE on Lenovo ThinkCentre M920q Tiny  
**Guest OS:** Debian GNU/Linux 13.7 (Trixie), amd64  
**Build status:** Debian 13.7 installed; login, DNS, APT repository access, SSH, and QEMU Guest Agent communication verified. The Seagate physical disk is attached as SCSI1 and detected inside Debian as a separate 5.5 TiB SCSI disk; the ext4 filesystem is created and labeled `media`; the UUID-based `/etc/fstab` entry is configured and tested, and `/srv/media` is mounted read/write. Reboot persistence remains to be verified.

## VM configuration

| Setting | Value |
|---|---|
| vCPU | 1 socket, 4 cores |
| CPU type | `host` |
| Memory | 4096 MiB |
| System disk | 64 GiB on `local-lvm` |
| Disk bus | SCSI, VirtIO SCSI single |
| Disk options | Discard, IO thread, SSD emulation enabled |
| Firmware | SeaBIOS |
| Machine | Default i440fx |
| QEMU Guest Agent | Installed in Debian guest; service active; Proxmox communication verified |
| Network | VirtIO NIC on `vmbr0`, Proxmox firewall enabled |
| Installer media | Debian 13.7 amd64 netinst ISO |

## Debian installer choices

- Hostname: `media-server`
- Domain name: left blank
- Root password: left blank, allowing Debian to lock direct root login and use the regular account with sudo privileges
- Network: installer configured through the VM's VirtIO network interface
- Disk partitioning: guided, all files in one partition with a separate swap area
- Desktop environment: not selected
- SSH server: selected
- Standard system utilities: selected
- GRUB bootloader: installed to the VM's virtual system disk

## Guest disk layout

The Debian installer detected one approximately 68.7 GB QEMU virtual disk. The physical Seagate disk was not attached to this VM during installation.

| Partition | Approximate size | Filesystem / role | Mount |
|---|---:|---|---|
| Primary partition | 65.2 GB | ext4 | `/` |
| Logical partition | 3.5 GB | swap | swap |

The partition changes for the operating system were written only to the VM's virtual system disk. The Seagate Exos was attached to VM 100 as SCSI1 using a persistent disk ID; the 64 GiB Debian system disk remains SCSI0 and first in the boot order. Inside Debian, the Seagate appears as a separate 5.5 TiB SCSI disk, presented by QEMU as `QEMU HARDDISK`. It has a GPT partition (`/dev/sdb1`) formatted as ext4 and labeled `media`, mounted read/write at `/srv/media` using a UUID-based `/etc/fstab` entry. The exact physical disk path, serial, and filesystem UUID are not included in this public build record.

## Installation result

Debian displayed the installer completion screen after the base system and selected packages had been installed. GRUB installation was confirmed for the VM's virtual disk.

The VM has booted from its installed Debian system. Login succeeded with the `ian` account. The `hostnamectl` output confirmed hostname `media-server`, Debian GNU/Linux 13 (Trixie), kernel `6.12.17+deb13-amd64`, and x86-64 architecture. The `ip -br addr` output showed the VirtIO interface `ens18` UP with an IPv4 address assigned. DNS resolution has been verified with `getent hosts deb.debian.org`; the SSH service reports `active` via `systemctl is-active ssh`; `sudo -v` completed without an authentication error. The default route is present through the home gateway. `sudo apt update` successfully contacted the Debian Security, Trixie, and Trixie Updates repositories and reported all packages up to date. SSH login from Windows PowerShell succeeded; the client accepted the Debian host key and opened an interactive shell as `ian`. Confirm the installer ISO remains detached from the virtual CD/DVD drive to prevent a later reboot from returning to the installer.

## First-boot checklist

- [x] Set the VM's CD/DVD drive to **Do not use any media** in Proxmox Hardware.
- [x] Reboot VM 100.
- [x] Confirm Debian reaches its login prompt.
- [x] Log in with the regular `ian` account.
- [x] Confirm the guest obtained a LAN IP address.
- [x] Verify DNS resolution for `deb.debian.org`.
- [x] Verify outbound package repository connectivity with `sudo apt update`.
- [x] Confirm the SSH service is active.
- [x] Verify SSH login from Windows PowerShell; authenticated as `ian` and reached the `media-server` shell.
- [x] Confirm `qemu-guest-agent` package is installed at the current repository version.
- [x] Confirm `systemctl is-active qemu-guest-agent` returns `active`.
- [x] Verify Proxmox-side communication with `qm agent 100 ping`; it returned silently without an error.
- [ ] Apply package upgrades and establish a maintenance routine.
- [x] Record the guest's hostname, Debian release, kernel, architecture, and interface status.
- [ ] Record the guest package baseline after updates.

## QEMU Guest Agent

The `qemu-guest-agent` package was already installed at the current repository version. The service check returned `active`. An attempt to use `systemctl enable --now` printed the expected no-installation-config message because the Debian unit is static; the agent is active, so it does not need to be enabled with `systemctl enable`. The output also included `systemd-ssh-generator` AF_VSOCK diagnostics, which are separate from the QEMU Guest Agent service. Proxmox-side agent communication was verified from the host with `qm agent 100 ping`, which completed without output or error, the expected successful response.

## Initial guest verification

| Check | Result |
|---|---|
| Account login | `ian` |
| Hostname | `media-server` |
| Operating system | Debian GNU/Linux 13 (Trixie) |
| Kernel | `6.12.17+deb13-amd64` |
| Architecture | x86-64 |
| Virtualization | KVM |
| Network interface | `ens18`, UP |
| IPv4 | DHCP address assigned; exact private address omitted from this public record |
| Default route | Present through home gateway |
| DNS | `deb.debian.org` resolved successfully |
| APT repository access | Debian Security, Trixie, and Trixie Updates reached successfully; APT reported all packages up to date |
| SSH daemon | Service reports `active`; remote Windows PowerShell login successful |
| Sudo | `sudo -v` succeeded |

## qBittorrent VPN gateway

Gluetun is deployed alongside the media stack as the ExpressVPN OpenVPN gateway. It is configured with `VPN_SERVICE_PROVIDER=expressvpn`, country selection `USA`, `NET_ADMIN`, and the `/dev/net/tun` device. Manual OpenVPN credentials are kept only in `/opt/media-stack/.env`, owned by root with mode `600`. The credential values are not included in the public build record.

qBittorrent uses `network_mode: service:gluetun` and waits for Gluetun's healthy status before starting. The qBittorrent Web UI is published through Gluetun on TCP port 8080. qBittorrent's network interface setting is bound to `tun0`. Radarr and Sonarr use `gluetun` as the qBittorrent download-client host at port 8080; both connection tests returned HTTP 200.

The kill-switch test was performed with downloads paused. Bringing `tun0` down caused a DNS-based public-IP request to fail. A direct-IP HTTPS request to `1.1.1.1` also returned HTTP 000 and timed out, verifying outbound traffic was blocked without relying on DNS. Gluetun and qBittorrent were recreated with Docker Compose; Gluetun returned healthy, qBittorrent returned running, and a new VPN egress IP was retrieved. This verifies network-level fail-closed behavior; it does not imply qBittorrent's UI automatically changes jobs to a paused state. A full Debian VM reboot test remains pending.

## Reverse proxy and custom domain

Nginx Proxy Manager (NPM) is deployed on this Debian VM in a separate Docker Compose project. The running container is named `nginx-proxy-manager-app-1` and publishes TCP ports 80, 81, and 443. Port 81 is the NPM administration interface and is intended for LAN access only.

The NPM proxy host for `moxxboxx.com` forwards HTTP internally to the Jellyfin service at `jellyfin:8096`. A Let's Encrypt certificate was issued successfully, and loading `https://moxxboxx.com` displays Jellyfin over HTTPS.

The first request returned 502 Bad Gateway because NPM and Jellyfin were attached to separate Docker bridge networks. Running `docker network connect media-stack_default nginx-proxy-manager-app-1` attached NPM to the media stack's network and restored upstream reachability. The network attachment was made persistent in `/home/ian/nginx-proxy-manager/docker-compose.yml` by declaring `media-stack_default` as an external while retaining NPM's default network. The Compose file passed `sudo docker compose config --quiet`. `sudo docker compose up -d` recreated the NPM container, and `docker inspect` confirmed it was attached to both `media-stack_default` and `nginx-proxy-manager_default`. A subsequent `curl -I https://moxxboxx.com` returned `HTTP/2 302` from OpenResty, confirming the HTTPS proxy path remained functional after recreation.

Dynamic DNS and Pi-hole are planned but not deployed. Dynamic DNS will keep the domain's public DNS record aligned with a changing home WAN address. Pi-hole is planned for LAN DNS filtering and local name resolution.

## Physical data disk passthrough

The physical Seagate Exos disk is attached to VM 100 as SCSI1 using its persistent model-based device ID. The Proxmox VM configuration confirms SCSI0 remains the 64 GiB NVMe-backed Debian system disk and first in the boot order. The virtual CD/DVD drive has no installation ISO attached. The disk was partitioned and formatted inside Debian after read-only signature checks; its ext4 filesystem is mounted at `/srv/media`.

## Guest-side data disk detection

After VM 100 was started, Debian detected the passed-through Seagate as a separate 5.5 TiB SCSI disk at `/dev/sdb`. The guest reports its presented model as `QEMU HARDDISK`, which is the virtual model exposed by the Proxmox/QEMU SCSI presentation. The existing Debian operating-system disk remains `/dev/sda` at 64 GiB. The Seagate now has one GPT partition (`/dev/sdb1`) formatted as ext4 and mounted at `/srv/media`.

## Read-only disk signature inspection

Inside Debian, `lsblk -f /dev/sdb` displayed the disk but no filesystem, UUID, label, or mount point. `sudo wipefs --no-act /dev/sdb` returned no known signatures and made no changes. These results indicate no recognizable filesystem or partition signatures were found; the disk has not yet been partitioned or formatted.

## GPT partitioning

Created a GPT partition table inside Debian on the passed-through Seagate disk, with one partition spanning the available capacity. The resulting partition is `/dev/sdb1`, approximately 5.5 TiB. It was subsequently formatted as ext4 with label `media` and mounted at `/srv/media`.

## Ext4 filesystem creation

Created an ext4 filesystem on `/dev/sdb1` with filesystem label `media`. The subsequent `lsblk -f /dev/sdb` output confirmed the ext4 filesystem and UUID. A UUID-based `/etc/fstab` entry was added for `/srv/media`. `sudo mount -a` succeeded, `findmnt` confirmed `/dev/sdb1` mounted as ext4 with read/write options, and `df -hT` showed approximately 5.4 TiB available. `sudo systemctl daemon-reload` completed successfully. `sudo findmnt --verify --verbose` reported zero parse errors and zero errors; its one warning is the expected empty virtual CD-ROM at `/media/cdrom0` with no medium inserted. A reboot persistence check remains pending.

## Persistent mount configuration

The ext4 filesystem UUID was used in `/etc/fstab` to configure `/srv/media` with filesystem type `ext4`, options `defaults,nofail`, and filesystem check fields `0 2`. `sudo mount -a` completed successfully. `findmnt /srv/media` confirmed `/dev/sdb1` mounted as ext4 with read/write options, and `df -hT /srv/media` reported approximately 5.5 TiB total and 5.4 TiB available. An initial `systemctl daemon-reload` attempt was run without `sudo` and was denied; the mount itself succeeded. `sudo systemctl daemon-reload` completed successfully. `findmnt --verify --verbose` reported zero parse errors and zero errors; its one warning identified the empty virtual CD-ROM at `/media/cdrom0`. A reboot persistence check remains pending. The filesystem UUID is kept out of the public build record.

## Media directory structure

Created and verified the following directories on the mounted ext4 filesystem:

```text
/srv/media/data/
├── torrents/
│   ├── movies/
│   └── tv/
├── usenet/
│   ├── incomplete/
│   └── complete/
│       ├── movies/
│       └── tv/
└── media/
    ├── movies/
    ├── tv/
    └── music/
```

The `lost+found` directory at the filesystem root is the standard ext4 recovery directory and is left untouched. The Debian account `ian` has UID 1000 and primary GID 1000. The data tree is owned by `ian:ian`; directories use mode `2775` with setgid inheritance, and the displayed directory listing confirmed `drwxrwsr-x`.

## Media data ownership and permissions

The Debian account `ian` was confirmed as UID 1000 with primary GID 1000. Ownership of `/srv/media/data` was set recursively to `1000:1000`. All directories under the data tree were assigned mode `2775`, enabling group inheritance for newly created items. Existing files were assigned mode `0664` (the tree was initially empty). A directory listing verified the displayed directories as `drwxrwsr-x` and owner/group `ian:ian`.

## Docker repository preparation

Installed the Debian prerequisites `ca-certificates` and `curl`. Checked for conflicting Docker/Podman packages; none were reported as installed. Added Docker’s official Debian package signing key and configured its stable APT repository for Debian Trixie on amd64. `sudo apt update` successfully retrieved the Docker repository Release metadata and package index; APT reported all package lists up to date. Docker Engine and the Compose plugin were subsequently installed and verified; details are recorded below.

## Docker installation and validation

Installed Docker Engine and CLI version 29.9.3, containerd 2.4.2, and Docker Compose plugin version 5.7.1 from Docker's official Debian Trixie stable APT repository. `systemctl is-active docker` returned `active`. `docker version` reported matching client and server Engine versions. `docker compose version` reported v5.7.1. The official `hello-world:latest` image was successfully pulled from Docker Hub, a test container was created and executed, and the expected success message was displayed. Docker commands are currently run with `sudo`; membership in the `docker` group has not been granted.

## Docker bind-mount storage validation

Pulled the Alpine test image and ran a one-shot container as UID/GID `1000:1000`, bind-mounting `/srv/media/data` into the container at `/data`. The container successfully created and removed a test file and printed `Container write test passed`. This verifies the current host directory ownership and permissions permit a non-root container process using the planned UID/GID to write to the media data mount.

## Docker Compose project definition

A version-controlled Compose project has been created at `services/media-stack/compose.yaml`. It defines Jellyfin, Radarr, Sonarr, Prowlarr, and qBittorrent. Application configuration and Jellyfin cache are mapped to project-local directories on the VM system disk. Radarr, Sonarr, and qBittorrent share `/srv/media/data` at the same in-container path, `/data`, to support consistent download/import paths and hardlinks on one filesystem. Jellyfin receives the media library at `/media` as a read-only bind mount. LinuxServer containers use UID/GID 1000 and UMASK 002; Jellyfin runs as UID/GID 1000:1000. The Compose file is committed to GitHub and has been downloaded to `/opt/media-stack/compose.yaml` on the Debian VM. Service configuration and Jellyfin cache directories were created under the project path and assigned to UID/GID 1000. `sudo docker compose config --quiet` completed with exit status 0. All five service images were pulled and their containers started with `sudo docker compose up -d`. `sudo docker compose ps` showed Jellyfin healthy; Radarr, Sonarr, Prowlarr, and qBittorrent reported running. Initial logs showed normal Jellyfin startup/migrations, Radarr and Sonarr database migrations, Prowlarr listening on its configured port, and qBittorrent Web UI initialization. Initial first-run setup has been completed in all five applications, and the temporary qBittorrent administrator password has been replaced. Radarr and Sonarr have their library root folders configured and use qBittorrent as the download client with separate categories. Prowlarr application connections to Radarr and Sonarr have been configured. End-to-end application sync, download/import behavior, hardlink creation, Jellyfin library scans, and playback remain to be validated.

## Initial media-stack deployment

Deployed the five services from `/opt/media-stack/compose.yaml` with Docker Compose. All images were pulled successfully, the Compose network was created, and the Jellyfin, Radarr, Sonarr, Prowlarr, and qBittorrent containers started. The captured `docker compose ps` output showed Jellyfin in a healthy state and the other four containers running. Startup logs showed Jellyfin completing its initial startup and migrations, Radarr and Sonarr running database migrations, Prowlarr starting its HTTP listener, and qBittorrent initializing the Web UI. Initial first-run setup has since been completed in all five applications. Radarr and Sonarr root folders are configured, both use qBittorrent with separate categories, and Prowlarr has application connections configured for Radarr and Sonarr. End-to-end download/import behavior, hardlink creation, Jellyfin library scans, and playback still need validation.

The qBittorrent first-start log issued a temporary Web UI administrator password. That credential is intentionally not recorded here or in any other public project file. The temporary password was replaced during initial setup. The service interfaces are intended for trusted LAN access only; no router port forwarding is planned.

## Media storage disposition

The 6 TB Seagate Exos is passed through to this Debian VM and formatted as ext4 inside Debian. It is mounted at `/srv/media` for media, downloads, and application data. An Alpine container write/delete test passed against `/srv/media/data` using UID/GID 1000:1000. The second 6 TB drive remains reserved for future expansion.
