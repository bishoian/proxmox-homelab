# Proxmox Homelab

**Project owner: Ian Bishop**

I am building this homelab as a practical learning environment for virtualization, Linux administration, networking, storage, and self-hosted services. My goal is to develop, test, document, and maintain infrastructure that demonstrates hands-on systems administration skills.

The lab is built around a **Lenovo ThinkCentre M920q Tiny** and will grow incrementally from a single-node Proxmox VE host into a small, versatile infrastructure environment.

> **Current status:** Proxmox VE has been installed to the Lenovo's internal NVMe drive, and the system has rebooted to the Proxmox local console. The console displays the web-management endpoint. First web-interface login and host verification are the next steps. See [Project Status](docs/project-status.md), [Installation Notes](virtualization/proxmox-installation.md), and [Hardware Inventory](docs/hardware-inventory.md).

## My project objectives

- Install and administer Proxmox VE on my Lenovo ThinkCentre M920q.
- Deploy and maintain Debian virtual machines and Linux containers.
- Host self-managed applications, including a planned Jellyfin media server.
- Learn direct-attached storage administration, disk health monitoring, filesystems, and backup planning.
- Practice managed switching, network documentation, and VLAN concepts.
- Build a Windows Server and Active Directory practice environment.
- Develop repeatable troubleshooting, maintenance, and recovery procedures.
- Maintain public, sanitized documentation that demonstrates my practical IT skills.

## Hardware at a glance

| Component | Configuration | Current status |
|---|---|---|
| Compute | Lenovo ThinkCentre M920q Tiny, Intel Core i5-8500T, 16 GB RAM, 256 GB NVMe | Proxmox VE installed |
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
                     (installed)
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

The TerraMaster D4-320 is **direct-attached storage (DAS)**. I plan to connect it to the Lenovo over USB, not to the Ethernet switch. The Lenovo will provide network access to data through services or file sharing that I configure.

This diagram represents my target design, not a claim that these services are already deployed. I will document exact cabling, port assignments, IP addressing, and VLANs only after I have verified them.

## My project milestones

1. **Preparation and installation**
   - [x] Create bootable Proxmox installer USB.
   - [x] Enable Intel Virtualization Technology (VT-x) and Intel VT-d in BIOS.
   - [x] Boot from the USB and reach the Proxmox installer.
   - [x] Install Proxmox VE to the Lenovo's internal NVMe and reboot to the local console.
   - [ ] Log in to the web interface and verify host status.
   - [ ] Record the installed Proxmox VE version and host hardware details.
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

- [Hardware Inventory](docs/hardware-inventory.md) — my equipment, specifications, and physical installation status.
- [Network Architecture](docs/network-architecture.md) — current and target network design.
- [Project Status](docs/project-status.md) — my milestone checklist and verification log.
- [Proxmox Installation Notes](virtualization/proxmox-installation.md) — installer choices and first-boot process.
- `virtualization/` — Proxmox host, VM, and container build records.
- `services/` — deployment notes and configuration examples for hosted applications.
- `troubleshooting/` — sanitized incidents, diagnosis, fixes, and verification.

## How I document this project

For each significant implementation, I record the objective, environment, prerequisites, implementation steps, validation evidence, issues encountered, resolution, and lessons learned.

I mark work complete only after I have performed and verified it. I label planned work clearly. I do not publish passwords, API tokens, private keys, personal data, router credentials, unredacted configuration exports, or sensitive network details. I use example values and placeholders in public documentation.

## License

I have not selected a license yet. Until I add one, this repository is publicly viewable on GitHub but does not grant an explicit open-source reuse license.
