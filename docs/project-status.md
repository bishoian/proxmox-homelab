# Project Status

**Project owner:** Ian Bishop  
**Last updated:** 2026-11-15  
**Overall status:** BIOS virtualization settings configured; Proxmox installation not yet completed.

I use this checklist as the source of truth for project progress. I mark an item complete only after I have performed the work and verified the result. Planned and unverified work remains clearly labelled.

## Milestone 0 — Preparation

- [x] Create the public GitHub repository.
- [x] Document the initial hardware inventory and planned network architecture.
- [x] Create the bootable Proxmox installation USB.
- [x] Enable Intel Virtualization Technology (VT-x) in the M920q BIOS.
- [x] Enable Intel VT-d in the M920q BIOS.
- [ ] Boot the installer USB and verify that the Proxmox installer starts.
- [ ] Confirm the intended UEFI boot mode and USB boot selection.
- [ ] Confirm the 256 GB NVMe is the intended installation target.
- [ ] Keep the TerraMaster and data disks disconnected during Proxmox installation to reduce the risk of selecting the wrong disk.

## Milestone 1 — Proxmox installation and baseline

- [ ] Install Proxmox VE to the Lenovo's 256 GB NVMe.
- [ ] Record Proxmox VE version and installation date.
- [ ] Record CPU, memory, NVMe model, and NIC information.
- [ ] Configure a stable management address after inspecting my existing LAN.
- [ ] Verify the management web UI from another trusted device.
- [ ] Apply updates using the appropriate Proxmox repository configuration.
- [ ] Configure time synchronization and administrative access.
- [ ] Document recovery and console access.

## Milestone 2 — Storage commissioning

- [ ] Connect the TerraMaster D4-320 to the Lenovo.
- [ ] Confirm enclosure detection and USB connection mode.
- [ ] Record each disk's model and capacity; keep serial numbers in private records only.
- [ ] Capture SMART health and power-on hours.
- [ ] Run extended SMART self-tests and review results.
- [ ] Decide on filesystem, mount point, and access permissions.
- [ ] Design and test an independent backup plan before storing irreplaceable data.

**Caution:** My second 6 TB disk is awaiting delivery. I will not assume mirroring, RAID, pooling, or backup behavior merely because multiple disks are installed in the enclosure.

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
| 2026-11-15 | Enabled Intel VT-x and Intel VT-d in the Lenovo M920q BIOS and saved the settings | User-reported BIOS configuration complete; USB boot and Proxmox installation remain pending |

## Change-control notes

For significant changes, I record:

- What changed and why.
- The affected hardware, VM, container, or network component.
- Commands or configuration changes, with secrets removed.
- Before-and-after validation.
- Rollback or recovery steps.
- Lessons learned.

I do not mark a task complete based only on an intention or an unverified configuration change.
