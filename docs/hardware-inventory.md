# Hardware Inventory

The equipment below makes up the Proxmox homelab. Installation and commissioning status is tracked alongside each component.

**Last updated:** 2026-11-15

## Compute

| Quantity | Component | Specifications | Status |
|---:|---|---|---|
| 1 | Lenovo ThinkCentre M920q Tiny | Intel Core i5-8500T, 6 cores; 16 GB RAM; 256 GB NVMe SSD | Proxmox VE 9.2.2 installed; web interface accessible |

The M920q is the Proxmox host. Intel VT-x and VT-d are enabled in BIOS. The web dashboard is accessible and the node is online. CPU, memory, NVMe, and network details will be recorded during the host baseline review.

## Storage

| Quantity | Component | Specifications | Status |
|---:|---|---|---|
| 1 | TerraMaster D4-320 | Four-bay external USB DAS; diskless enclosure | Connected to Lenovo; disk detection pending |
| 2 | Seagate Exos ST6000NM0115 | 3.5-inch; 6 TB; 7200 RPM; SATA 6 Gb/s; 512e; 256 MB cache | One drive installed; second drive awaiting delivery |

### Storage notes

- The D4-320 is direct-attached storage. It connects to the Lenovo over USB; it is not an Ethernet NAS.
- One 6 TB drive is installed in the enclosure. Proxmox detection and drive health have not yet been checked.
- The second matching 6 TB drive is awaiting delivery.
- Drive health, power-on hours, firmware, and SMART attributes have not yet been recorded.
- The filesystem, mount point, permissions, and backup approach will be selected before the disk is used for important data.

## Networking

| Quantity | Component | Specifications | Status |
|---:|---|---|---|
| 1 | RealHD web-managed Ethernet switch | 8 × 2.5GbE ports; 1 × 10G SFP+ port; metal housing; fanless | Installed in rack |
| 1 | NETGEAR Nighthawk RAX41v2 | Existing home router | Existing equipment; assembled |
| 1 | GeeekPi 12-port patch panel | 10-inch; 0.5U; CAT6; RackMate-compatible | Installed in rack |
| 8 | CAT6A patch cables | 0.5 ft | Installed in rack |

The switch's exact hardware revision, firmware version, management method, port assignments, and feature set will be recorded after inspection. VLANs have not yet been configured.

The Lenovo's negotiated Ethernet link speed will be verified rather than inferred from the switch's maximum port speed.

## Rack and power

| Quantity | Component | Specifications | Status |
|---:|---|---|---|
| 1 | GeeekPi DeskPi RackMate T1 Plus | 8U; 10-inch rack; 260 mm / 10.23-inch depth | Assembled |
| 1 | GeeekPi rack PDU | 10-inch; 1U; six outlets (two front, four rear); surge protected; 14 AWG | Installed in rack |
| 1 | GeeekPi mini PC rack shelf | 10-inch; 1U; RJ45/CAT6 and HDMI pass-through ports | Installed in rack |
| 2 | GeeekPi ventilated cantilever shelves | 0.5U; 7.87-inch depth | Installed in rack |
| 2 | GeeekPi ventilated cantilever shelves | 1U; 7.87-inch depth | Installed in rack |

Equipment clearances, ventilation, weight limits, cable strain relief, and PDU load will be checked during final rack commissioning.

## Display and cabling accessories

| Quantity | Component | Specifications | Status |
|---:|---|---|---|
| 2 | HDMI cables | 8K HDMI 2.1 | Available |

The HDMI cables are for displays, not network connections. The RJ45/HDMI pass-through shelf provides convenient physical connections; its internal cabling path will be checked during rack cable documentation.

## Commissioning checklist

- [x] Install Proxmox VE and verify web interface access.
- [ ] Record BIOS/UEFI version and installed RAM layout.
- [ ] Record NVMe model and health.
- [ ] Verify the Lenovo's negotiated Ethernet speed.
- [ ] Identify the RealHD switch's exact model/revision and firmware.
- [ ] Document switch port assignments.
- [x] Connect the D4-320 to the Lenovo.
- [ ] Confirm Proxmox detects the D4-320's installed drive.
- [ ] Capture SMART health and extended self-test results.
- [ ] Configure a safe storage mount and backup strategy.
- [ ] Verify PDU load and airflow under normal operating conditions.
