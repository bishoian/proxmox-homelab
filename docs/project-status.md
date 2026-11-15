# Project Status

**Project owner:** Ian Bishop  
**Last updated:** 2026-11-15  
**Overall status:** Proxmox VE 9.2 updated, rebooted, and verified; D4-320 drive detected with an initial SMART overall status of passed.

This checklist tracks completed, in-progress, and planned work. Items are marked complete after the change has been made and the result has been verified.

## Milestone 0 — Preparation

- [x] Create the public GitHub repository.
- [x] Add the initial hardware inventory and network architecture.
- [x] Create the bootable Proxmox installation USB.
- [x] Enable Intel Virtualization Technology (VT-x) in the M920q BIOS.
- [x] Enable Intel VT-d in the M920q BIOS.
- [x] Boot from the USB and reach the Proxmox installer.
- [x] Install Proxmox VE to the Lenovo's internal NVMe.
- [x] Reboot successfully to the Proxmox local console.
- [x] Log in to the Proxmox web interface.
- [x] Confirm the `pve` node is online.
- [x] Install Proxmox VE 9.2 from the installation image.

## Milestone 1 — Proxmox host baseline

- [x] Verify the Proxmox summary reports six CPUs and 15.46 GiB of usable memory.
- [x] Confirm the installed CPU model as Intel Core i5-8500.
- [x] Identify the 256 GB Lenovo NVMe and inspect its EFI/LVM partition layout.
- [x] Verify onboard Ethernet link speed at 1 Gbps.
- [x] Confirm management interface is reachable from the home LAN.
- [x] Configure Proxmox VE and Ceph no-subscription package repositories.
- [x] Refresh package lists successfully from all configured sources.
- [x] Review and apply available package upgrades.
- [x] Confirm the Updates view reports no available updates after the upgrade.
- [x] Reboot to activate the updated kernel.
- [x] Verify active kernel `7.0.14-29-pve` and `pve-manager` version `9.2.27`.
- [ ] Configure time and administrative settings.
- [ ] Document host recovery and console access.

## Milestone 2 — Storage commissioning

**Selected layout:** Use the first 6 TB Seagate as a standalone ext4 data disk dedicated to the Debian media-services VM. The disk will be passed through to the VM using a stable by-id path. The second 6 TB drive will be left for later expansion. See [Media Storage Design](../storage/storage-layout.md).

- [x] Connect the TerraMaster D4-320 to the Lenovo over USB.
- [x] Verify that Proxmox detects the installed drive.
- [x] Identify the installed drive as a 6 TB Seagate Exos ST6000NM0115.
- [x] Confirm SMART overall status reports `PASSED`.
- [x] Review and record the initial SMART attributes and reported power-on counters.
- [x] Stop the extended SMART self-test early and record the incomplete result. The self-test log reports Aborted by host with 80% remaining; this is not a passing extended test.
- [ ] Consider a completed extended SMART self-test during a later maintenance window.
- [x] Select a standalone ext4 data disk dedicated to the Debian media-services VM; reserve the second drive for later.
- [x] Identify the Seagate's persistent `/dev/disk/by-id` entry on the Proxmox host (device-specific path retained locally, not in public notes).
- [x] Shut down VM 100 cleanly and attach the physical Seagate to SCSI1 using its persistent disk ID.
- [x] Boot Debian and confirm the passed-through Seagate appears as a separate 5.5 TiB SCSI disk (`sdb`) with no filesystem or mount point.
- [x] Inspect the guest disk with `lsblk -f`; no filesystem or mount point was reported.
- [x] Run `wipefs --no-act` against the guest disk; no known signatures were reported and no changes were made.
- [x] Create a GPT partition table on the guest data disk with one full-size partition (`/dev/sdb1`).
- [ ] Create an ext4 filesystem on `/dev/sdb1` inside Debian.
- [ ] Configure the UUID-based mount and access permissions.
- [ ] Design and test an independent backup plan before storing irreplaceable data.

**Current disk inventory:** One 6 TB Seagate Exos drive is installed in the enclosure. The second matching 6 TB drive is awaiting delivery. No disk has been initialized, formatted, or assigned to a Proxmox storage pool.

## Milestone 3 — Debian and application hosting

- [x] Create VM 100 (`media-server`) with four vCPUs, 4 GiB RAM, a 64 GiB `local-lvm` system disk, VirtIO SCSI, QEMU Agent enabled, and VirtIO networking on `vmbr0`.
- [x] Attach the Debian 13.7 netinst ISO and boot to the graphical installer.
- [x] Complete Debian 13.7 base installation using the graphical installer.
- [x] Install GRUB to the VM's virtual system disk.
- [x] Boot the installed Debian system and reach the `media-server` tty1 login prompt.
- [ ] Confirm the installer ISO is detached from the virtual CD/DVD drive.
- [x] Log in using the regular `ian` account.
- [x] Confirm hostname `media-server`, Debian 13 Trixie, and x86-64 architecture.
- [x] Confirm VirtIO interface `ens18` is up and has an IPv4 address assigned.
- [x] Verify DNS resolution for `deb.debian.org`.
- [x] Confirm the SSH service reports `active` inside Debian.
- [x] Confirm sudo authentication with `sudo -v`.
- [x] Verify outbound access to Debian package repositories with `sudo apt update`; APT reported all packages up to date.
- [x] Connect from Windows PowerShell over SSH and authenticate as `ian`.
- [x] Confirm QEMU Guest Agent package is installed at the current repository version and service reports `active`.
- [x] Verify Proxmox-to-guest QEMU Guest Agent communication using `qm agent 100 ping`; command completed successfully without output.
- [ ] Apply system updates and establish a maintenance routine.
- [ ] Install Docker Engine and Compose plugin in the Debian VM.
- [ ] Deploy a first test service.
- [ ] Record service health checks, logs, and update procedure.

## Milestone 4 — Media service

- [ ] Deploy Jellyfin.
- [ ] Configure media storage access.
- [ ] Verify playback from a trusted client.
- [ ] Document transcoding expectations and hardware limitations.
- [ ] Configure backups for application configuration and metadata.

## Milestone 5 — Monitoring and operations

- [ ] Deploy Uptime Kuma or equivalent monitoring.
- [ ] Monitor host and service availability.
- [ ] Document routine update and maintenance steps.
- [ ] Test a restore procedure.
- [ ] Record a troubleshooting incident with root cause and verification.

## Milestone 6 — Windows administration lab

- [ ] Create a Windows Server VM.
- [ ] Create a separate Windows client VM.
- [ ] Build an isolated practice domain.
- [ ] Configure Active Directory Domain Services.
- [ ] Practice DNS, DHCP, users, groups, and Group Policy.
- [ ] Document common support tasks and troubleshooting.

## Milestone 7 — Networking

- [ ] Identify the exact RealHD switch model and firmware.
- [ ] Document the current switch port map and link speeds.
- [ ] Verify router capabilities relevant to VLANs.
- [ ] Design management, server, trusted-client, and lab segments.
- [ ] Implement VLANs with a tested recovery path.
- [ ] Validate allowed and blocked traffic with explicit tests.

## Evidence log

| Date | Activity | Result |
|---|---|---|
| 2026-11-15 | Created the public GitHub project and documented the initial hardware inventory and network architecture | Repository initialized |
| 2026-11-15 | Enabled Intel VT-x and Intel VT-d in the Lenovo M920q BIOS | BIOS virtualization settings enabled and saved |
| 2026-11-15 | Booted the Lenovo from the Proxmox USB installer | Proxmox installer started successfully |
| 2026-11-15 | Installed Proxmox VE using ext4 on the internal NVMe and rebooted | System reached the Proxmox local console |
| 2026-11-15 | Opened the Proxmox web interface and signed in | Dashboard loaded; node `pve` online; Proxmox VE 9.2.2 confirmed |
| 2026-11-15 | Connected the TerraMaster D4-320 to the Proxmox host over USB | Enclosure connected; drive initially absent because the rear power switch was off |
| 2026-11-15 | Powered on the D4-320 and refreshed Proxmox disk inventory | 6 TB Seagate Exos detected as `/dev/sda`; SMART overall status `PASSED` |
| 2026-11-15 | Reviewed the Seagate's initial SMART attribute table | Zero reallocated, pending, offline-uncorrectable, reported-uncorrectable, and UDMA CRC counts; 24°C reported temperature |
| 2026-11-15 | Started the Seagate extended SMART self-test | Estimated duration approximately 10 hours |
| 2026-11-15 | Stopped the Seagate extended SMART self-test early | Self-test log reports Aborted by host with 80% remaining; test is incomplete and is not recorded as a pass |
| 2026-11-15 | Selected the initial media storage layout | First Seagate reserved as a standalone ext4 data disk for the Debian media-services VM; second drive reserved for future expansion |
| 2026-11-15 | Reviewed the Proxmox node Summary | Six CPUs and 15.46 GiB usable RAM reported; CPU confirmed as Intel Core i5-8500 (standard model, not 8500T) |
| 2026-11-15 | Inspected block devices with `lsblk` | Internal Lenovo NVMe reports 238.5G with EFI and Proxmox LVM layout; Seagate Exos appears as a separate 5.5T disk with no partitions, filesystem, or mount point |
| 2026-11-15 | Checked host network interfaces from the Proxmox Shell | `nic0` UP at 1000 Mb/s; `vmbr0` UP on the same 1 Gbps uplink; `wlo1` DOWN; loopback interface present |
| 2026-11-15 | Identified the Seagate's persistent disk path for VM passthrough | Confirmed the 5.5 TiB Seagate Exos is a separate USB disk with no partitions, filesystem, or mount point; a model-based `/dev/disk/by-id` link resolves to the disk. Exact serial and device path are omitted from public documentation |
| 2026-11-15 | Attached the Seagate physical disk to Debian VM 100 | Added the whole physical disk as SCSI1 using its persistent disk ID. VM configuration confirms SCSI0 remains the 64 GiB Debian system disk and boot order prioritizes SCSI0; installer media is detached |
| 2026-11-15 | Verified Seagate passthrough inside Debian | `lsblk` shows a separate 5.5 TiB SCSI disk as `sdb`, model presented as `QEMU HARDDISK`, with no partition, filesystem, or mount point |
| 2026-11-15 | Performed read-only signature inspection on the Seagate guest disk | `lsblk -f` showed no filesystem or mount point; `sudo wipefs --no-act /dev/sdb` returned no known signatures. The inspection did not modify the disk |
| 2026-11-15 | Created the Seagate GPT partition table | Created one partition using the full available disk space; `lsblk` confirms `/dev/sdb1` at approximately 5.5 TiB with no filesystem or mount point. Ext4 creation is next |
| 2026-11-15 | Configured Proxmox package sources | Proxmox VE no-subscription and Ceph Tentacle no-subscription enabled; Debian Trixie and security sources retained; Enterprise and Ceph Squid Enterprise entries disabled |
| 2026-11-15 | Refreshed the APT package database | Debian, Proxmox VE no-subscription, and Ceph Tentacle repositories fetched successfully; package lists read and task completed with `TASK OK` |
| 2026-11-15 | Applied all available package upgrades | Upgrade completed without reported errors; Updates view reported no updates available |
| 2026-11-15 | Rebooted Proxmox and checked package versions | Running kernel `7.0.14-29-pve`; `pve-manager` `9.2.27`; `proxmox-ve` meta-package `9.2.0`; updated host booted successfully |
| 2026-11-15 | Created VM 100 (`media-server`) | Configured 4 vCPUs, 4 GiB RAM, 64 GiB NVMe-backed `local-lvm` disk, VirtIO SCSI single, QEMU Agent, and VirtIO network on `vmbr0` |
| 2026-11-15 | Booted the Debian 13.7 netinst ISO in VM 100 | Debian graphical installer boot menu displayed in BIOS mode |
| 2026-11-15 | Completed Debian 13.7 installation in VM 100 | Base system and selected packages installed; GRUB configured on the VM's virtual system disk |
| 2026-11-15 | Booted the installed Debian VM for the first time | Debian reached the `media-server` tty1 login prompt |
| 2026-11-15 | Verified initial Debian guest login and network interface | Logged in as `ian`; `hostnamectl` confirmed Debian 13 Trixie on x86-64 with hostname `media-server`; VirtIO interface `ens18` was UP and had an IPv4 address assigned |
| 2026-11-15 | Checked Debian administration and network services | `sudo -v` succeeded; default route via home gateway is present; `getent hosts deb.debian.org` resolved successfully; `systemctl is-active ssh` returned `active`. Remote SSH login remains pending |
| 2026-11-15 | Refreshed Debian package indexes in VM 100 | `sudo apt update` successfully reached Debian Security, Trixie, and Trixie Updates repositories; APT reported all packages are up to date |
| 2026-11-15 | Connected to Debian VM 100 remotely from Windows | SSH host identity accepted and password authentication succeeded; interactive shell opened as `ian` on `media-server` |
| 2026-11-15 | Installed and checked QEMU Guest Agent in VM 100 | Package was already at the current repository version; `systemctl is-active qemu-guest-agent` returned `active`. The unit is static and does not require `systemctl enable`; Proxmox `qm agent 100 ping` completed successfully without output, confirming host-to-guest communication |

## Change notes

For significant changes, the record includes what changed, the affected component, configuration details, validation results, recovery steps, and lessons learned. Planned work remains separate from completed work.
