# Project Status

**Project owner:** Ian Bishop  
**Last updated:** 2026-11-15  
**Overall status:** Proxmox VE 9.2.2 installed and accessible; D4-320 drive detected with SMART overall status passed.

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
- [x] Confirm installed Proxmox VE version 9.2.2.

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
- [ ] Reboot to activate the updated kernel after the Seagate extended SMART test finishes.
- [ ] Verify the active kernel and Proxmox manager version after reboot.
- [ ] Configure time and administrative settings.
- [ ] Document host recovery and console access.

## Milestone 2 — Storage commissioning

**Selected layout:** Use the first 6 TB Seagate as a standalone ext4 data disk dedicated to the Debian media-services VM. The disk will be passed through to the VM using a stable by-id path. The second 6 TB drive will be left for later expansion. See [Media Storage Design](../storage/storage-layout.md).

- [x] Connect the TerraMaster D4-320 to the Lenovo over USB.
- [x] Verify that Proxmox detects the installed drive.
- [x] Identify the installed drive as a 6 TB Seagate Exos ST6000NM0115.
- [x] Confirm SMART overall status reports `PASSED`.
- [x] Review and record the initial SMART attributes and reported power-on counters.
- [ ] Complete an extended SMART self-test. **Deferred:** the running test will be intentionally aborted before completion; the initial SMART snapshot will be used for the first media setup, with the incomplete test clearly recorded.
- [ ] Choose the data disk layout, filesystem, mount point, and access permissions.
- [ ] Design and test an independent backup plan before storing irreplaceable data.

**Current disk inventory:** One 6 TB Seagate Exos drive is installed in the enclosure. The second matching 6 TB drive is awaiting delivery. No disk has been initialized, formatted, or assigned to a Proxmox storage pool.

## Milestone 3 — Debian and application hosting

- [ ] Create a Debian virtual machine.
- [ ] Configure SSH access.
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
| 2026-11-15 | Started the Seagate extended SMART self-test | Estimated duration approximately 10 hours; an early abort has been selected, so no complete extended-test result will be claimed |
| 2026-11-15 | Reviewed the Proxmox node Summary | Six CPUs and 15.46 GiB usable RAM reported; CPU confirmed as Intel Core i5-8500 (standard model, not 8500T) |
| 2026-11-15 | Inspected block devices with `lsblk` | Internal Lenovo NVMe reports 238.5G with EFI and Proxmox LVM layout; Seagate Exos appears as a separate 5.5T disk with no partitions, filesystem, or mount point |
| 2026-11-15 | Checked host network interfaces from the Proxmox Shell | `nic0` UP at 1000 Mb/s; `vmbr0` UP on the same 1 Gbps uplink; `wlo1` DOWN; loopback interface present |
| 2026-11-15 | Configured Proxmox package sources | Proxmox VE no-subscription and Ceph Tentacle no-subscription enabled; Debian Trixie and security sources retained; Enterprise and Ceph Squid Enterprise entries disabled |
| 2026-11-15 | Refreshed the APT package database | Debian, Proxmox VE no-subscription, and Ceph Tentacle repositories fetched successfully; package lists read and task completed with `TASK OK` |
| 2026-11-15 | Applied all available package upgrades | Upgrade completed without reported errors; Updates view now reports no updates available; reboot pending to activate the updated kernel and verify running versions |

## Change notes

For significant changes, the record includes what changed, the affected component, configuration details, validation results, recovery steps, and lessons learned. Planned work remains separate from completed work.
