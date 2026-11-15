# Proxmox Homelab

**Project owner: Ian Bishop**

A hands-on homelab project focused on virtualization, Linux administration, networking, storage, and self-hosted services. The goal is to build and maintain a practical infrastructure environment that develops real-world systems administration skills.

The lab is built around a **Lenovo ThinkCentre M920q Tiny** and is growing from a single-node Proxmox VE host into a small, versatile infrastructure environment.

> **Current status:** Proxmox VE 9.2 is installed on the Lenovo's internal NVMe drive, and the `pve` node is online in the web interface. After package updates and reboot, the host is running kernel `7.0.14-29-pve` with `pve-manager` `9.2.27`. The TerraMaster D4-320 is connected over USB, and its installed 6 TB Seagate Exos drive is detected by Proxmox as `/dev/sda`. The disk's overall SMART status is `PASSED`. The initial attribute review shows zero reallocated, pending, offline-uncorrectable, and UDMA CRC error counts, with a reported temperature of 24°C. The extended self-test was intentionally stopped early; its log records Aborted by host with 80% remaining. Only the initial SMART snapshot is available for the first media-storage setup, so the drive will be monitored and important data backed up. The host reports an Intel Core i5-8500, six CPUs, and 15.46 GiB of usable RAM. The onboard Ethernet link (`nic0`) is currently negotiating at 1 Gbps. The Proxmox VE and Ceph Tentacle no-subscription repositories are enabled alongside Debian Trixie and Debian Security. Package upgrades have been applied successfully, and the Updates view now reports no available updates. The updated Proxmox kernel is active following a successful reboot. VM 100 (`media-server`) has been created with four vCPUs, 4 GiB RAM, a 64 GiB NVMe-backed system disk, VirtIO networking, and the Debian 13.7 installer attached. Debian 13.7 installation has completed in VM 100, including GRUB installation to the VM's virtual disk. Debian 13.7 has booted successfully, and the `ian` account login is verified. The guest hostname is `media-server`, and its VirtIO interface (`ens18`) is up with an IPv4 address assigned. DNS resolution is successful, the SSH service is active, and sudo authentication works. A Debian APT update successfully reached the Debian security, base, and updates repositories; APT reported all packages up to date. Remote SSH access and QEMU Guest Agent setup remain to be verified. See [Project Status](docs/project-status.md), [Installation Notes](virtualization/proxmox-installation.md), [Host Baseline](virtualization/proxmox-host-baseline.md), [Hardware Inventory](docs/hardware-inventory.md), and the [Media Storage Design](storage/storage-layout.md).

## Project goals

- Administer a Proxmox VE virtualization host.
- Deploy and maintain Debian virtual machines and Linux containers.
- Host self-managed applications, including a planned Jellyfin media server.
- Practice direct-attached storage administration, disk health monitoring, filesystem management, and backups.
- Learn managed switching, network documentation, and VLAN concepts.
- Build a Windows Server and Active Directory practice environment.
- Develop repeatable troubleshooting, maintenance, and recovery procedures.
- Maintain clear documentation that demonstrates practical IT skills.

## Hardware at a glance

| Component | Configuration | Current status |
|---|---|---|
| Compute | Lenovo ThinkCentre M920q Tiny, Intel Core i5-8500, 16 GB RAM, 256 GB NVMe | Proxmox VE 9.2.2 installed; web interface accessible |
| Storage enclosure | TerraMaster D4-320, four-bay USB DAS | Connected to Lenovo; installed drive detected |
| Data disks | 2 × Seagate Exos ST6000NM0115, 6 TB each | One detected as a 5.5 TiB disk; unpartitioned; initial SMART status passed; second awaiting delivery |
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

## Target architecture

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
                      Proxmox VE 9.2.2
                       /          \
                Virtual machines  USB
                 and containers    |
                            TerraMaster D4-320
                                      |
                             Seagate Exos SATA HDD
                                6 TB installed
```

The TerraMaster D4-320 is **direct-attached storage (DAS)**. It connects to the Lenovo over USB rather than to the Ethernet switch. The enclosure is connected, and the installed drive is visible to Proxmox. Its overall SMART status reports `PASSED`; detailed health attributes and an extended self-test remain to be reviewed.

The diagram shows the target network layout. Switch cabling, port assignments, and VLAN configuration will be documented as they are confirmed. Planned virtual machines and services are not considered deployed until they have been configured and tested.

## Project milestones

1. **Preparation and installation**
   - [x] Create bootable Proxmox installer USB.
   - [x] Enable Intel Virtualization Technology (VT-x) and Intel VT-d in BIOS.
   - [x] Boot from the USB and reach the Proxmox installer.
   - [x] Install Proxmox VE to the Lenovo's internal NVMe and reboot to the local console.
   - [x] Log in to the web interface and confirm the `pve` node is online.
   - [x] Confirm Proxmox VE version 9.2.2.
   - [x] Verify CPU model, CPU count, and usable memory.
   - [x] Confirm the onboard Ethernet link negotiates at 1 Gbps.
   - [x] Record the NVMe model and partition layout.
2. **Foundational administration**
   - [x] Configure Proxmox VE and Ceph no-subscription repositories.
   - [x] Refresh package lists successfully.
   - [x] Review and apply available package upgrades.
   - [x] Reboot to activate the updated kernel and verify the running kernel and Proxmox package versions.
   - [x] Configure time and administrative settings.
   - [x] Create VM 100 (`media-server`) with the planned CPU, memory, NVMe disk, and VirtIO network configuration.
   - [x] Complete Debian 13.7 base installation and install GRUB to the VM's virtual disk.
   - [x] Detach the installer ISO, reboot, and verify the first Debian boot.
   - [ ] Establish and test a backup approach before relying on hosted services.
3. **Storage and media services**
   - [x] Connect the D4-320 to the Proxmox host.
   - [x] Verify disk detection and confirm SMART overall status is `PASSED`.
   - [x] Review the initial SMART attributes and record the reported power-on counters.
   - [x] Stop the extended SMART self-test early and record its incomplete result.
   - [ ] Consider a completed extended test during a later maintenance window.
   - [ ] Configure a filesystem, mount point, and storage layout.
   - [ ] Deploy Jellyfin.
4. **Application hosting and monitoring**
   - [ ] Deploy Docker workloads in a Debian VM.
   - [ ] Add service monitoring and alerting.
5. **IT administration lab**
   - [ ] Build Windows Server and a Windows client VM.
   - [ ] Practice Active Directory, DNS, DHCP, and Group Policy in an isolated lab.
6. **Networking**
   - [ ] Document the current LAN and switch configuration, including the physical uplink path.
   - [ ] Plan and test VLAN segmentation after the baseline network is stable.

## Repository guide

- [Hardware Inventory](docs/hardware-inventory.md) — equipment, specifications, and installation status.
- [Network Architecture](docs/network-architecture.md) — current and target network design.
- [Project Status](docs/project-status.md) — milestone checklist and verification log.
- [Proxmox Installation Notes](virtualization/proxmox-installation.md) — installer choices and first-boot process.
- [Proxmox Host Baseline](virtualization/proxmox-host-baseline.md) — first web-interface login and initial host observations.
- [Package Repository Configuration](docs/package-repositories.md) — Proxmox VE, Ceph, and Debian package sources.
- `virtualization/` — Proxmox host, VM, and container build records.
- `services/` — deployment notes and configuration examples for hosted applications.
- [D4-320 Drive Not Detected](troubleshooting/d4-320-drive-not-detected.md) — power-state diagnosis and storage detection verification.
- [Seagate SMART Baseline](storage/seagate-smart-baseline.md) — initial health attributes and planned self-test.
- [Media Storage Design](storage/storage-layout.md) — single-disk layout for the Debian media-services VM.
- `troubleshooting/` — incidents, diagnosis, fixes, and verification.

## Documentation approach

Each major implementation includes its purpose, environment, configuration, validation results, any issues encountered, and lessons learned. Planned work remains clearly identified until it has been completed and tested.

## License

No license has been selected. The repository is publicly viewable on GitHub, but no explicit open-source reuse license has been granted.
