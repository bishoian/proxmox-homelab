# Changelog

A concise record of the homelab's major build and documentation milestones.

## 2026-11-15

### Initial project setup
- Created the public `proxmox-homelab` repository.
- Added the project overview, hardware inventory, planned network architecture, and milestone checklist.
- Documented the Lenovo M920q, TerraMaster D4-320, Seagate Exos drives, RealHD switch, RackMate cabinet, power distribution, patch panel, shelves, and cabling.

### Proxmox installation
- Created the Proxmox VE installation USB.
- Enabled Intel VT-x and Intel VT-d in the M920q BIOS.
- Booted the installer and installed Proxmox VE 9.2.2 on the internal NVMe using ext4.
- Rebooted successfully to the Proxmox local console.
- Signed in to the web interface and confirmed the `pve` node was online.

### Storage
- Connected the TerraMaster D4-320 to the Lenovo over USB.
- Traced the initial missing-drive symptom to the enclosure's rear power switch being off.
- Powered on the enclosure and refreshed Proxmox disk inventory; the 6 TB Seagate Exos appeared as `/dev/sda`.
- Confirmed the SMART overall status reports `PASSED`.
- Reviewed the initial SMART attribute table: zero reallocated, pending, offline-uncorrectable, reported-uncorrectable, and UDMA CRC error counts; 24°C reported temperature.
- Recorded the displayed power-on-hours and head-flying-hours counters.
- Started an extended SMART self-test on the Seagate; the drive estimated approximately 10 hours to completion.
- Stopped the test early. The self-test log reports Aborted by host with 80% remaining and no first-error LBA; the test is recorded as incomplete, not passed.

### Host baseline
- Reviewed the Proxmox node Summary: six CPUs, 15.46 GiB usable RAM, EFI boot, and Proxmox manager 9.2.2.
- Confirmed the installed processor is an Intel Core i5-8500 (standard model, not the i5-8500T).
- Identified that the Enterprise package repository is enabled but requires a subscription; repository configuration for the unsubscribed lab is pending.

### Host storage layout
- Identified the internal NVMe as a Lenovo NVMe 2280S 256GB SSD; Linux reports 238.5G.
- Recorded the Proxmox EFI and LVM layout, including the 8 GB swap volume, 69.4 GB root filesystem, and LVM-thin data pool.
- Confirmed the Seagate Exos is visible as a separate 5.5T disk with no partitions, filesystem, or mount point.

### Host hardware and network checks
- Confirmed the installed processor from the Proxmox Shell as an Intel Core i5-8500 at 3.00 GHz with six logical CPUs.
- Confirmed the physical Ethernet interface (`nic0`) is UP at 1000 Mb/s; the Proxmox bridge (`vmbr0`) uses the same uplink. The wireless interface is DOWN.
- Recorded the host's current wired link as 1 Gbps; the switch-side path and port negotiation remain to be checked.

### Host hardware and network checks
- Confirmed the installed processor from the Proxmox Shell as an Intel Core i5-8500 at 3.00 GHz with six logical CPUs.
- Confirmed the physical Ethernet interface (`nic0`) is UP at 1000 Mb/s; the Proxmox bridge (`vmbr0`) uses the same uplink. The wireless interface is DOWN.
- Recorded the host's current wired link as 1 Gbps, matching Lenovo's M920 Tiny specification for onboard Intel I219-LM Gigabit Ethernet. The switch-side path and port negotiation remain to be checked.

### Package repositories
- Disabled the Proxmox VE Enterprise and Ceph Squid Enterprise entries.
- Enabled the Proxmox VE no-subscription repository and Ceph Tentacle no-subscription repository.
- Retained the Debian Trixie and Debian Security repositories.
- Added documentation for the package source configuration.
- Refreshed the APT package database successfully; Debian, Proxmox VE no-subscription, and Ceph Tentacle repositories were reached, 530 kB was fetched, and the task ended with `TASK OK`.
- Reviewed and installed the available package upgrades without reported errors.
- Confirmed the Proxmox Updates view reports `No updates available`.
- Noted that a newer Proxmox kernel and manager package were included.

### Storage architecture planning
- Selected a capacity-focused proposal for the two 6 TB drives: independent ext4 filesystems, with optional MergerFS pooling to provide one namespace without striping or mirroring.
- Kept active VM system disks on the internal NVMe; HDDs are intended for bulk data and local backup targets.
- Documented the proposed design separately from implementation. No formatting, mounting, or pool creation has occurred.

### Media storage design
- Selected the first 6 TB Seagate as a standalone media and application-data disk for a Debian VM running Jellyfin and supporting services such as Radarr and Sonarr.
- Decided to stop the extended SMART test early and proceed using the initial SMART snapshot; the test will be recorded as aborted/incomplete rather than passed.
- Planned physical disk passthrough to the Debian VM using a persistent `/dev/disk/by-id` path and ext4 inside the guest.
- Reserved the second 6 TB drive for later expansion. MergerFS and multi-disk pooling are no longer part of the initial setup.
- Kept the VM operating system and container configuration on the internal NVMe; media, downloads, and library data will share one filesystem in the guest.
- The storage design is selected but has not been implemented.

### Post-upgrade reboot
- Rebooted successfully after package upgrades.
- Verified the active kernel as `7.0.14-29-pve`.
- Verified `pve-manager` version `9.2.27` and the `proxmox-ve` meta-package version `9.2.0`.
- Confirmed the upgraded Ceph client package version `20.2.5-pve1` is installed; no Ceph cluster is deployed.

### SMART test disposition
- Recorded the extended SMART self-test as intentionally aborted by the host at 80% remaining; no successful extended-test result is claimed.

### First virtual machine
- Created VM 100, `media-server`, on the Proxmox host.
- Allocated four vCPUs, 4 GiB RAM, and a 64 GiB system disk on `local-lvm`.
- Configured VirtIO SCSI single, QEMU Agent, and VirtIO networking through `vmbr0`.
- Attached the Debian 13.7 netinst ISO and booted to the graphical installer in BIOS mode.
- Completed the Debian 13.7 base installation with no desktop environment, SSH server, and standard system utilities selected.
- Configured GRUB on the VM's virtual system disk.
- Booted from the installed system and reached the `media-server` tty1 login prompt.
- Logged in successfully as `ian`; verified hostname `media-server`, Debian GNU/Linux 13 (Trixie), kernel `6.12.17+deb13-amd64`, and x86-64 architecture.
- Confirmed the VirtIO interface `ens18` is UP and received an IPv4 address.
- Verified the default route through the home gateway, successful DNS resolution for `deb.debian.org`, working sudo authentication, and SSH service state `active`.
- Ran `sudo apt update` successfully; Debian Security, Trixie, and Trixie Updates repositories responded, and APT reported all packages up to date.
- Checked QEMU Guest Agent in Debian VM 100: package was already current and service reports `active`; Debian's static service unit does not require `systemctl enable`.
- Verified host-to-guest agent communication with `qm agent 100 ping`; it returned without output or error.
- Connected remotely from Windows PowerShell to the Debian VM over SSH; host key was accepted and authentication succeeded as `ian`.

### Seagate passthrough preparation
- Identified the installed Seagate as a separate USB-attached 5.5 TiB disk on the Proxmox host; it has no partitions, filesystem, or mount point.
- Located the persistent model-based `/dev/disk/by-id` link that resolves to the physical disk.
- Kept the device-specific identifier and serial out of the public repository. Physical passthrough to VM 100 has not yet been configured.

### Seagate disk passthrough
- Attached the whole Seagate Exos physical disk to VM 100 as SCSI1 using its persistent disk identifier.
- Verified VM configuration preserves the 64 GiB Debian system disk as SCSI0 and keeps SCSI0 first in the boot order.
- Confirmed the virtual CD/DVD drive has no installer ISO attached. The Seagate remains unpartitioned and unformatted; guest-side detection verification is next.

### Guest-side Seagate detection
- Started VM 100 after attaching the Seagate as SCSI1.
- Confirmed Debian sees a separate 5.5 TiB SCSI disk as `/dev/sdb`; QEMU presents the model string as `QEMU HARDDISK`.
- Confirmed the captured `lsblk` output shows no partitions, filesystem, or mount point on the data disk. A read-only signature check was completed with no known signatures reported; GPT initialization is the next step.

### Read-only Seagate signature inspection
- Checked the guest disk with `lsblk -f`; no filesystem or mount point was reported.
- Ran `sudo wipefs --no-act` against the guest data disk; no known signatures were reported and the command made no changes.
- Confirmed the disk remains unpartitioned and unformatted. GPT initialization is the next step.

### Seagate GPT partitioning
- Created a GPT partition table on the passed-through Seagate disk from inside Debian.
- Created one full-capacity partition, `/dev/sdb1`, approximately 5.5 TiB.
- Verified the partition is present and still has no filesystem or mount point. Ext4 creation remains pending.

### Seagate ext4 filesystem
- Formatted the full-size GPT partition `/dev/sdb1` as ext4 with filesystem label `media`.
- Verified the filesystem type, label, and generated UUID with `lsblk -f`.
- The filesystem is not mounted yet; the next step is a persistent UUID-based mount at `/srv/media`.

### Seagate mount configuration
- Added a UUID-based `/etc/fstab` entry to mount the ext4 filesystem at `/srv/media` with `defaults,nofail`.
- Ran `sudo mount -a` successfully and verified the mount with `findmnt` and `df -hT`; the filesystem is mounted read/write with approximately 5.4 TiB available.
- A `systemctl daemon-reload` attempt without elevated privileges was denied; the mount is active. A privileged `systemctl daemon-reload` completed successfully. `findmnt --verify --verbose` reported zero parse errors and zero errors; its only warning is the empty virtual CD-ROM at `/media/cdrom0`. A reboot persistence check remains pending.

### Media directory layout
- Created the shared `/srv/media/data` directory structure for torrent downloads, usenet incomplete and complete downloads, movies, television, and music.
- Verified the directory tree from Debian.
- Confirmed the Debian account `ian` uses UID 1000 and primary GID 1000 for consistent container permission mapping; ownership and permission configuration is next.

### Media storage permissions
- Set ownership of `/srv/media/data` to `ian:ian` (UID/GID 1000).
- Applied directory mode `2775` with setgid inheritance and file mode `0664` to any existing files.
- Verified the directory tree reports `drwxrwsr-x` with owner/group `ian:ian`.

### Docker repository preparation
- Checked for conflicting Docker/Podman packages; none were reported as installed.
- Installed `ca-certificates` and `curl` prerequisites.
- Added Docker’s official Debian signing key and stable APT repository for Debian Trixie amd64.
- Ran `sudo apt update` successfully; Docker Release metadata and the stable package index were retrieved.
- Docker Engine and Compose are installed and validated; media stack deployment remains pending.

### Docker Engine installation and validation
- Installed Docker Engine and CLI 29.9.3, containerd 2.4.2, and Docker Compose plugin 5.7.1.
- Verified the Docker service reports `active`.
- Verified Docker client/server versions and Compose plugin availability.
- Pulled and ran `hello-world:latest` from Docker Hub; the container printed the expected success message.
- Retained `sudo docker` usage rather than adding the administrative account to the Docker group.

### Docker media-storage bind-mount validation
- Ran an Alpine test container as UID/GID `1000:1000` with `/srv/media/data` bind-mounted at `/data`.
- The container created and deleted a test file successfully and printed `Container write test passed`.
- Confirmed the directory ownership and setgid permission model works for a non-root container process.

### Current state
- Proxmox VE is installed and accessible through the web interface, with `pve-manager` 9.2.27 and kernel 7.0.14-29-pve active.
- One 6 TB Seagate Exos drive is installed in the D4-320; the second matching drive is awaiting delivery.
- The D4-320 drive is detected as `/dev/sda` and reports SMART overall status `PASSED`; the extended test was aborted early and is documented as incomplete.
- VM 100 has Debian 13.7 installed; login, sudo authentication, default route, DNS resolution, APT repository connectivity, SSH service state, and Proxmox-to-guest QEMU Guest Agent communication are verified. Remote SSH login from Windows PowerShell is verified. The passed-through Seagate is visible inside Debian as a separate 5.5 TiB SCSI disk with one GPT partition (`/dev/sdb1`) formatted as ext4 and labeled `media`, mounted read/write at `/srv/media` by UUID. The media directory tree and ownership are configured; container read/write validation passed.
