# Hardware Inventory

This inventory records the equipment I use in my Proxmox homelab and its current commissioning state.

## Compute

| Qty. | Component | Configuration | Status |
|---:|---|---|---|
| 1 | Lenovo ThinkCentre M920q Tiny | Intel Core i5-8500, 6 cores; 16 GB RAM; 256 GB NVMe | Proxmox VE 9.2 installed and updated |

I confirmed that the CPU is the standard i5-8500, not the i5-8500T. Linux reports the NVMe as 238.5 GB, and the host has 15.46 GiB of usable memory. I enabled Intel VT-x and VT-d in BIOS.

## Storage

| Qty. | Component | Configuration | Status |
|---:|---|---|---|
| 1 | TerraMaster D4-320 | Four-bay USB direct-attached enclosure | Connected to the Lenovo |
| 2 | Seagate Exos ST6000NM0115 | 3.5-inch SATA, 6 TB, 7200 RPM | Both installed in the D4-320 and passed through separately to Debian; one for media and one for shared storage |

I passed the media disk through to VM 100 as SCSI1 and the shared-storage disk as SCSI2, using persistent host-side device identifiers. Proxmox detects each as a 5.5 TiB device, while Debian manages their separate GPT partitions and ext4 filesystems. I mounted them at `/srv/media` and `/srv/shared` through UUID-based `/etc/fstab` entries.

Initial SMART overall status was `PASSED`. The initial attribute table showed no reallocated, pending, offline-uncorrectable, reported-uncorrectable, or UDMA CRC errors, and a reported temperature of 24°C. The extended SMART test was stopped by the host with 80% remaining. It is incomplete, not a passing test. See the [SMART baseline](../storage/seagate-smart-baseline.md).

I omit both disks' exact serials, persistent device paths, and filesystem UUIDs from this public repository. The second disk's initial SMART assessment passed; its captured attributes showed no reallocated, pending, offline-uncorrectable, reported-uncorrectable, or UDMA CRC errors. It reported 28°C and has no recorded self-test yet.

## Networking

| Qty. | Component | Configuration | Status |
|---:|---|---|---|
| 1 | RealHD managed switch | 8 × 2.5GbE, 1 × 10G SFP+ | Installed; exact revision and port map not yet documented |
| 1 | NETGEAR Nighthawk RAX41v2 | Home router | In service |
| 1 | GeeekPi 12-port patch panel | 10-inch, 0.5U, CAT6 | Installed |
| 8 | CAT6A patch cables | 0.5 ft | Installed |

My M920q's onboard Ethernet link negotiates at 1 Gbps. Although my switch supports 2.5GbE, the host's onboard NIC is Gigabit Ethernet. I have not configured VLANs.

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

- [ ] I will record the BIOS/UEFI version and installed RAM layout.
- [ ] I will review the NVMe's detailed health attributes.
- [ ] I will identify the switch's exact model, hardware revision, and firmware.
- [ ] I will document switch port assignments and trace my rack cabling.
- [ ] I will check PDU load and rack airflow under normal operation.
- [ ] I will complete an extended SMART test during a maintenance window.
