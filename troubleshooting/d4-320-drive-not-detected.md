# Troubleshooting: D4-320 Drive Not Detected

**System:** TerraMaster D4-320 USB DAS  
**Host:** Lenovo ThinkCentre M920q running Proxmox VE 9.2.2  
**Status:** Resolved; detailed drive health checks remain in progress.

## Symptom

The D4-320 was connected to the Proxmox host, but the 6 TB Seagate Exos drive did not appear under **Node → Disks → Disks**. The disk inventory initially showed only the Lenovo's internal NVMe drive.

## Diagnosis

The enclosure's rear power switch had not been turned on. The USB connection was present, but the DAS itself was not powered.

## Resolution

1. Located the physical power switch on the rear of the D4-320.
2. Switched on the enclosure.
3. Returned to **Node → Disks → Disks** in Proxmox.
4. Refreshed the disk inventory.

## Verification

After powering on the enclosure, Proxmox detected the installed Seagate Exos drive:

- Linux device: `/dev/sda`
- Capacity: 6.00 TB
- Model family: Seagate Exos ST6000NM0115
- SMART overall status: `PASSED`
- GPT partition table: not present
- Proxmox disk usage: none

The Proxmox disk view also reported the internal NVMe's SMART overall status as `PASSED`.

The serial number is intentionally not included in this record.

## Follow-up

- [ ] Review detailed SMART attributes, including power-on hours and error counters.
- [ ] Run an extended SMART self-test.
- [ ] Confirm the disk is stable through the USB enclosure.
- [ ] Decide on filesystem, mount point, permissions, and backup approach.
- [ ] Only then initialize or format the disk if required.

No initialization, formatting, partitioning, or storage-pool creation was performed during this troubleshooting process.

## Takeaway

When a USB storage enclosure is connected but no drive appears in the operating system, verify the enclosure's own power state and indicators before investigating drivers or disk compatibility.
