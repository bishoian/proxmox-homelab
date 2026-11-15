# Project Status

**Project owner:** Ian Bishop  
**Last updated:** 2026-11-15  
**Overall status:** Proxmox VE 9.2.2 installed and accessible; D4-320 connected, with disk detection still to be checked.

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

- [ ] Verify CPU, memory, NVMe, and NIC recognition.
- [ ] Verify management network reachability and negotiated Ethernet link speed.
- [ ] Review update repositories and apply available updates.
- [ ] Configure time and administrative settings.
- [ ] Document host recovery and console access.

## Milestone 2 — Storage commissioning

- [x] Connect the TerraMaster D4-320 to the Lenovo over USB.
- [ ] Verify that Proxmox detects the enclosure and its installed drive.
- [ ] Identify the installed drive by model and capacity.
- [ ] Capture SMART health and power-on hours.
- [ ] Run an extended SMART self-test and review the result.
- [ ] Decide on filesystem, mount point, and access permissions.
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
| 2026-11-15 | Connected the TerraMaster D4-320 to the Proxmox host over USB | Enclosure physically connected; disk detection and health checks pending |

## Change notes

For significant changes, the record includes what changed, the affected component, configuration details, validation results, recovery steps, and lessons learned. Planned work remains separate from completed work.
