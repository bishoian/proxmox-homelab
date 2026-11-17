# Hardware Inventory

Current equipment and commissioning state for the Proxmox homelab.

## Compute

| Qty. | Component | Configuration | Status |
|---:|---|---|---|
| 1 | Lenovo ThinkCentre M920q Tiny | Intel Core i5-8500, 6 cores; 16 GB RAM; 256 GB NVMe | Proxmox VE 9.2 installed and updated |

The CPU is the standard i5-8500, not the i5-8500T. Linux reports the NVMe as 238.5 GB. The host has 15.46 GiB usable memory. Intel VT-x and VT-d are enabled.

## Storage

| Qty. | Component | Configuration | Status |
|---:|---|---|---|
| 1 | TerraMaster D4-320 | Four-bay USB direct-attached enclosure | Connected to the Lenovo |
| 2 | Seagate Exos ST6000NM0115 | 3.5-inch SATA, 6 TB, 7200 RPM | One installed and passed through to Debian; second reserved for later |

The installed disk is detected by Proxmox as a 5.5 TiB device and passed through to VM 100 as SCSI1 using a persistent device identifier. Debian manages its GPT partition and ext4 filesystem, mounted at `/srv/media` through a UUID-based `/etc/fstab` entry.

Initial SMART overall status was `PASSED`. The initial attribute table showed no reallocated, pending, offline-uncorrectable, reported-uncorrectable, or UDMA CRC errors, and a reported temperature of 24°C. The extended SMART test was stopped by the host with 80% remaining. It is incomplete, not a passing test. See the [SMART baseline](../storage/seagate-smart-baseline.md).

The disk's exact serial, persistent device path, and filesystem UUID are intentionally omitted. The second 6 TB disk has not yet been installed.

## Networking

| Qty. | Component | Configuration | Status |
|---:|---|---|---|
| 1 | RealHD managed switch | 8 × 2.5GbE, 1 × 10G SFP+ | Installed; exact revision and port map not yet documented |
| 1 | NETGEAR Nighthawk RAX41v2 | Home router | In service |
| 1 | GeeekPi 12-port patch panel | 10-inch, 0.5U, CAT6 | Installed |
| 8 | CAT6A patch cables | 0.5 ft | Installed |

The M920q's onboard Ethernet link negotiates at 1 Gbps. The switch supports 2.5GbE, but the host's onboard NIC is Gigabit Ethernet. VLANs are not configured.

## Rack and power

| Qty. | Component | Configuration | Status |
|---:|---|---|---|
| 1 | GeeekPi DeskPi RackMate T1 Plus | 8U, 10-inch, 260 mm depth | Assembled |
| 1 | GeeekPi PDU | 1U, six outlets, surge protection, 14 AWG | Installed |
| 1 | GeeekPi mini PC shelf | 10-inch, 1U, RJ45 and HDMI pass-through | Installed |
| 2 | GeeekPi ventilated shelves | 0.5U, 7.87-inch depth | Installed |
| 2 | GeeekPi ventilated shelves | 1U, 7.87-inch depth | Installed |

## Accessories

| Qty. | Component | Configuration | Status |
|---:|---|---|---|
| 2 | HDMI cables | HDMI 2.1, 8K rated | Available |

## Remaining commissioning

- [ ] Record BIOS/UEFI version and installed RAM layout.
- [ ] Review detailed NVMe health attributes.
- [ ] Identify the switch's exact model, hardware revision, and firmware.
- [ ] Document switch port assignments and trace rack cabling.
- [ ] Verify PDU load and airflow under normal operation.
- [ ] Complete an extended SMART test during a maintenance window.
