# Hardware Inventory

The equipment below makes up the Proxmox homelab. Installation and commissioning status is tracked alongside each component.

**Last updated:** 2026-11-15

## Compute

| Quantity | Component | Specifications | Status |
|---:|---|---|---|
| 1 | Lenovo ThinkCentre M920q Tiny | Intel Core i5-8500, 6 cores; 16 GB RAM; 256 GB NVMe SSD | Proxmox VE 9.2.2 installed; web interface accessible |

The M920q is the Proxmox host, equipped with an Intel Core i5-8500 processor and 16 GB RAM. Intel VT-x and VT-d are enabled in BIOS. The web dashboard is accessible and the node is online. The Proxmox Summary reports six CPUs and 15.46 GiB usable RAM. The processor model was confirmed as the standard i5-8500, not the i5-8500T. The onboard Ethernet interface (`nic0`) is UP and negotiated at 1 Gbps; the Proxmox bridge (`vmbr0`) uses this same uplink. The internal NVMe is identified as a Lenovo NVMe 2280S 256GB SSD and reports 238.5G in Linux. The onboard Ethernet interface (`nic0`) is up and negotiated at 1 Gbps; the Proxmox bridge (`vmbr0`) uses this same uplink.

## Storage

| Quantity | Component | Specifications | Status |
|---:|---|---|---|
| 1 | TerraMaster D4-320 | Four-bay external USB DAS; diskless enclosure | Connected to Lenovo; drive detected by Proxmox |
| 2 | Seagate Exos ST6000NM0115 | 3.5-inch; 6 TB; 7200 RPM; SATA 6 Gb/s; 512e; 256 MB cache | One drive detected as `/dev/sda` (5.5 TiB reported by Linux); unpartitioned; SMART overall status passed; second awaiting delivery |

### Storage notes

- The D4-320 is direct-attached storage. It connects to the Lenovo over USB; it is not an Ethernet NAS.
- One 6 TB drive is installed in the enclosure and is detected by Proxmox as `/dev/sda` (5.5 TiB as displayed by Linux).
- The Proxmox disk view reports SMART overall status `PASSED` for the Seagate and the internal NVMe.
- The initial Seagate SMART table reports zero reallocated sectors, current pending sectors, offline-uncorrectable sectors, reported uncorrectable errors, and UDMA CRC errors. The reported temperature is 24°C.
- The reported power-on-hours raw value is 0, while the head-flying-hours counter shows approximately 4 minutes and 17 seconds. These counters are recorded as displayed and should not be treated as proof of the drive's complete service history.
- An extended SMART self-test is running; its result is pending.
- The `lsblk` output shows no partitions, filesystem, or mount point on `/dev/sda`. It has not been initialized or formatted.
- The second matching 6 TB drive is awaiting delivery.
- Detailed drive health, power-on hours, and firmware information will be reviewed after the extended test.
- Proposed layout: two independent ext4 filesystems, optionally combined with MergerFS for a single storage namespace. The layout is documented but has not been implemented.
- Mount points, Proxmox storage definitions, permissions, and backup jobs will be configured after both drives pass health checks.

## Networking

| Quantity | Component | Specifications | Status |
|---:|---|---|---|
| 1 | RealHD web-managed Ethernet switch | 8 × 2.5GbE ports; 1 × 10G SFP+ port; metal housing; fanless | Installed in rack |
| 1 | NETGEAR Nighthawk RAX41v2 | Existing home router | Existing equipment; assembled |
| 1 | GeeekPi 12-port patch panel | 10-inch; 0.5U; CAT6; RackMate-compatible | Installed in rack |
| 8 | CAT6A patch cables | 0.5 ft | Installed in rack |

The switch's exact hardware revision, firmware version, management method, port assignments, and feature set will be recorded after inspection. VLANs have not yet been configured.

The Lenovo's onboard Ethernet link is currently negotiated at 1 Gbps. The RealHD switch supports 2.5GbE, but the present connection is limited by the host's negotiated link speed. The physical path to the switch will be confirmed when the rack network is connected and traced.

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
- [x] Record NVMe model and partition layout.
- [ ] Review detailed NVMe health attributes.
- [x] Verify the Lenovo's onboard Ethernet link at 1 Gbps.
- [ ] Identify the RealHD switch's exact model/revision and firmware.
- [ ] Document switch port assignments.
- [x] Connect the D4-320 to the Lenovo.
- [x] Confirm Proxmox detects the D4-320's installed drive.
- [x] Confirm SMART overall status reports `PASSED`.
- [x] Review initial SMART attributes and record the reported power-on counters.
- [ ] Run an extended SMART self-test.
- [ ] Configure a safe storage mount and backup strategy.
- [ ] Verify PDU load and airflow under normal operating conditions.
