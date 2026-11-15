# Project Status

**Project owner:** Ian Bishop  
**Last updated:** 2026-11-15  
**Overall status:** Proxmox VE 9.2.2 installed; web interface verified; D4-320 connected and awaiting disk detection checks.

I use this checklist as the source of truth for project progress. I mark an item complete only after I have performed the work and verified the result. Planned and unverified work remains clearly labelled.

## Milestone 0 — Preparation

- [x] Create the public GitHub repository.
- [x] Document the initial hardware inventory and planned network architecture.
- [x] Create the bootable Proxmox installation USB.
- [x] Enable Intel Virtualization Technology (VT-x) in the M920q BIOS.
- [x] Enable Intel VT-d in the M920q BIOS.
- [x] Boot from the USB and reach the Proxmox installer.
- [x] Install Proxmox VE to the Lenovo's internal NVMe.
- [x] Reboot successfully to the Proxmox local console.
- [x] Log in to the Proxmox web interface from another device.
- [x] Confirm the `pve` node is online in the web interface.
- [x] Record installed Proxmox VE version 9.2.2.

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
- [ ] Run extended SMART self-tests and review results.
- [ ] Decide on filesystem, mount point, and access permissions.
- [ ] Design and test an independent backup plan before storing irreplaceable data.

**Current disk inventory:** One 6 TB Seagate Exos drive is installed in the enclosure. The second matching 6 TB drive is awaiting delivery. No disk has been initialized, formatted, or assigned to a Proxmox storage pool as part of this project yet.

## Milestone 3 — Debian and application hosting

- [ ] Create a Debian virtual machine.
- [ ] Configure SSH access securely.
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
- [ ] Document current switch port map and link speeds.
- [ ] Verify router capabilities relevant to VLANs.
- [ ] Design management, server, trusted-client, and lab segments.
- [ ] Implement VLANs with a tested recovery path.
- [ ] Validate allowed and blocked traffic with explicit tests.

## Evidence log

| Date | Activity | Result / evidence |
|---|---|---|
| 2026-11-15 | Created the public GitHub project and documented my initial hardware inventory and planned network architecture | Repository initialized |
| 2026-11-15 | Enabled Intel VT-x and Intel VT-d in the Lenovo M920q BIOS and saved the settings | BIOS configuration completed |
| 2026-11-15 | Booted the Lenovo from the Proxmox USB installer | Reached the Proxmox installer |
| 2026-11-15 | Installed Proxmox VE using the ext4 installer option on the internal NVMe and rebooted the system | System reached the Proxmox local console |
| 2026-11-15 | Logged in to the Proxmox web interface | Dashboard loaded; node `pve` displayed as online; interface identified Proxmox VE 9.2.2 |
| 2026-11-15 | Connected the TerraMaster D4-320 to the Proxmox host over USB | Physical connection completed; disk detection and health checks pending |

## Change-control notes

For significant changes, I record:

- What changed and why.
- The affected hardware, VM, container, or network component.
- Commands or configuration changes.
- Before-and-after validation.
- Rollback or recovery steps.
- Lessons learned.

I do not mark a task complete based only on an intention or an unverified configuration change.
