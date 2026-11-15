# Proxmox Homelab

A documented, hands-on homelab project focused on virtualization, Linux administration, networking, storage, and self-hosted services.

Built around a **Lenovo ThinkCentre M920q Tiny**, this project will grow incrementally from a single-node hypervisor into a practical learning environment for systems administration and infrastructure operations.

> **Project status:** In progress. The bootable Proxmox installer USB has been created. Proxmox has not yet been installed or configured. See [Project Status](docs/project-status.md) for the current milestone and [Hardware Inventory](docs/hardware-inventory.md) for component details.

## Project objectives

- Install and administer Proxmox VE on a Lenovo ThinkCentre M920q.
- Deploy and maintain Debian virtual machines and Linux containers.
- Host self-managed applications, including a planned Jellyfin media server.
- Learn direct-attached storage administration, disk health monitoring, filesystems, and backup planning.
- Practice managed switching, network documentation, and VLAN concepts.
- Build a Windows Server and Active Directory practice environment.
- Develop repeatable troubleshooting, maintenance, and recovery procedures.
- Maintain public, sanitized documentation that demonstrates practical IT skills.

## Hardware at a glance

| Component | Configuration | Current status |
|---|---|---|
| Compute | Lenovo ThinkCentre M920q Tiny, Intel Core i5-8500T, 16 GB RAM, 256 GB NVMe | Installed in rack |
| Storage enclosure | TerraMaster D4-320, four-bay USB DAS | Owned; commissioning pending |
| Data disks | 2 × Seagate Exos ST6000NM0115, 6 TB each | One installed; second awaiting delivery |
| Network switch | RealHD 8-port 2.5GbE web-managed switch with 1 × 10G SFP+ port | Installed in rack |
| Router | NETGEAR Nighthawk RAX41v2 | Existing router |
| Rack | GeeekPi DeskPi RackMate T1 Plus, 8U, 10-inch, 260 mm depth | Assembled |
| Rack power | GeeekPi 1U PDU, six outlets, surge protection, 14 AWG | Installed in rack |
| Patch panel | GeeekPi 12-port, 0.5U CAT6, 10-inch | Installed in rack |
| Mini PC shelf | GeeekPi 10-inch 1U shelf with RJ45/CAT6 and HDMI pass-through ports | Installed in rack |
| Ventilated shelves | 2 × 0.5U and 2 × 1U, 7.87-inch depth | Installed in rack |
| Patch cables | 8 × 0.5 ft CAT6A | Installed in rack |
| Display cables | 2 × 8K HDMI 2.1 cables | Available |

See [Hardware Inventory](docs/hardware-inventory.md) for the complete component list and installation notes.

## Planned architecture

```text
                         Internet
                            |
                   NETGEAR Nighthawk
                        RAX41v2
                            |
                 RealHD managed switch
                            |
                 Lenovo ThinkCentre
                       M920q
                            |
                      Proxmox VE
                      (planned)
                 /          |          \
          Debian VM    Debian LXC    Windows VM
          Docker       Monitoring    Server lab
          (planned)    (planned)      (planned)
                 |
            USB connection
                 |
          TerraMaster D4-320
                 |
          Seagate Exos SATA HDDs
          6 TB each (2 planned)
```

The TerraMaster D4-320 is **direct-attached storage (DAS)**. It connects to the Lenovo over USB, not to the Ethernet switch. The Lenovo will provide network access to data through appropriately configured services or file sharing.

The diagram represents the target design, not a claim that these services are already deployed. Exact cabling, port assignments, IP addressing, and VLANs will be documented only after they are verified.

## Planned project milestones

1. **Preparation and installation**
   - [x] Create bootable Proxmox installer USB (user-reported).
   - [ ] Verify BIOS virtualization settings.
   - [ ] Install Proxmox VE to the 256 GB NVMe.
   - [ ] Record host network configuration and confirm web UI access.
2. **Foundational administration**
   - [ ] Configure updates, time, administrative access, and storage.
   - [ ] Create a Debian virtual machine.
   - [ ] Establish and test a backup approach before relying on hosted services.
3. **Storage and media services**
   - [ ] Commission the D4-320 and inspect disk SMART health.
   - [ ] Configure a safe mount and storage layout.
   - [ ] Deploy Jellyfin.
4. **Application hosting and monitoring**
   - [ ] Deploy Docker workloads in a Debian VM.
   - [ ] Add service monitoring and alerting.
5. **IT administration lab**
   - [ ] Build Windows Server and a Windows client VM.
   - [ ] Practice Active Directory, DNS, DHCP, and Group Policy in an isolated lab.
6. **Networking**
   - [ ] Document the current LAN and switch configuration.
   - [ ] Plan and test VLAN segmentation only after the baseline network is stable.

## Repository guide

- [Hardware Inventory](docs/hardware-inventory.md) — equipment, specifications, and physical installation status.
- [Network Architecture](docs/network-architecture.md) — current and target network design.
- [Project Status](docs/project-status.md) — milestone checklist and verification log.
- `virtualization/` — Proxmox host, VM, and container build records (to be added as systems are deployed).
- `services/` — deployment notes and configuration examples for hosted applications (to be added as services are deployed).
- `troubleshooting/` — sanitized incidents, diagnosis, fixes, and verification (to be added as troubleshooting events occur).

## Documentation and security practices

Each implementation record should include its objective, environment, prerequisites, implementation steps, validation evidence, issues encountered, resolution, and lessons learned.

Only verified work is marked complete. Planned work is labelled as planned. Do not commit passwords, API tokens, private keys, personal data, router credentials, unredacted configuration exports, or sensitive network details. Use example values and placeholders in public documentation.

## License

No license has been selected yet. Unless and until a license is added, the repository contents are shared publicly on GitHub but are not granted an explicit open-source reuse license.
