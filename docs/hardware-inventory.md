# Hardware Inventory

This document records the physical components selected for the Proxmox homelab and their current deployment status.

**Last updated:** 2026-11-15

## Compute

| Quantity | Component | Specifications | Status |
|---:|---|---|---|
| 1 | Lenovo ThinkCentre M920q Tiny | Intel Core i5-8500T, 6 cores; 16 GB RAM; 256 GB NVMe SSD | Installed in rack |

The Lenovo is the planned Proxmox host. Proxmox installation, BIOS configuration, firmware details, and network configuration have not yet been verified and will be recorded after commissioning.

## Storage

| Quantity | Component | Specifications | Status |
|---:|---|---|---|
| 1 | TerraMaster D4-320 | Four-bay external USB DAS; diskless enclosure | Owned; storage commissioning pending |
| 2 | Seagate Exos ST6000NM0115 | 3.5-inch; 6 TB; 7200 RPM; SATA 6 Gb/s; 512e; 256 MB cache | One drive installed; second drive has not arrived |

### Storage notes

- The D4-320 is direct-attached storage. It is intended to connect to the Lenovo over USB; it is not an Ethernet NAS.
- One 6 TB drive is currently installed. The second matching drive is pending delivery.
- The drives' health, power-on hours, firmware, and SMART attributes have not yet been recorded.
- Do not treat two disks as redundancy or a backup until a deliberate storage layout and independent backup plan have been designed and tested.
- Before formatting or partitioning any disk, positively identify it by model, serial number, and device path. Confirm that the correct disk is selected.

## Networking

| Quantity | Component | Specifications | Status |
|---:|---|---|---|
| 1 | RealHD web-managed Ethernet switch | 8 × 2.5GbE ports; 1 × 10G SFP+ port; metal housing; fanless | Installed in rack |
| 1 | NETGEAR Nighthawk RAX41v2 | Existing home router | Existing equipment; assembled |
| 1 | GeeekPi 12-port patch panel | 10-inch; 0.5U; CAT6; RackMate-compatible | Installed in rack |
| 8 | CAT6A patch cables | 0.5 ft | Installed in rack |

The switch's exact hardware revision, firmware version, management address, port assignments, and feature set will be documented after they are inspected. No VLAN or switch configuration is claimed to be complete.

The Lenovo's negotiated Ethernet link speed will be verified during installation rather than assumed from the switch's maximum port speed.

## Rack and power

| Quantity | Component | Specifications | Status |
|---:|---|---|---|
| 1 | GeeekPi DeskPi RackMate T1 Plus | 8U; 10-inch rack; 260 mm / 10.23-inch depth | Assembled |
| 1 | GeeekPi rack PDU | 10-inch; 1U; six outlets (two front, four rear); surge protected; 14 AWG | Installed in rack |
| 1 | GeeekPi mini PC rack shelf | 10-inch; 1U; RJ45/CAT6 and HDMI pass-through ports | Installed in rack |
| 2 | GeeekPi ventilated cantilever shelves | 0.5U; 7.87-inch depth | Installed in rack |
| 2 | GeeekPi ventilated cantilever shelves | 1U; 7.87-inch depth | Installed in rack |

Check equipment clearances, ventilation, weight limits, cable strain relief, and PDU load before finalizing the arrangement. Do not assume that every shelf or accessory supports every possible device orientation.

## Display and cabling accessories

| Quantity | Component | Specifications | Status |
|---:|---|---|---|
| 2 | HDMI cables | 8K HDMI 2.1 | Available |

The HDMI cables are display cables, not network links. The RJ45/HDMI pass-through shelf provides convenient physical connections; its exact internal cabling path should be verified during rack cable documentation.

## Commissioning checklist

- [ ] Record Lenovo BIOS/UEFI version and virtualization settings.
- [ ] Record installed RAM layout and NVMe model.
- [ ] Install Proxmox VE and record its version.
- [ ] Record the Lenovo's negotiated Ethernet speed.
- [ ] Identify the RealHD switch's exact model/revision and firmware.
- [ ] Document switch port assignments without publishing sensitive credentials.
- [ ] Connect the D4-320 and identify both installed/pending disks by model and serial number.
- [ ] Capture SMART health and extended self-test results for each disk.
- [ ] Document safe storage mounts, permissions, and backup strategy.
- [ ] Verify PDU load and airflow under normal operating conditions.

## Public documentation safety

Do not publish serial numbers, passwords, API keys, private SSH keys, router credentials, public IP addresses, or unredacted exports from network equipment. Keep sensitive operational notes in a private location.
