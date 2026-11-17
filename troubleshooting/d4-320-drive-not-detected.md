# Troubleshooting: D4-320 Drive Not Detected

**System:** TerraMaster D4-320 USB DAS  
**Host:** Lenovo ThinkCentre M920q running Proxmox VE  
**Status:** Resolved

## Symptom

The D4-320 was connected over USB, but the installed Seagate drive did not appear under **Node → Disks** in Proxmox.

## Diagnosis

The enclosure's rear power switch was off. The USB cable was connected, but the DAS itself was not powered on.

## Resolution

1. Switched on the D4-320 using its rear power switch.
2. Refreshed the Proxmox disk inventory.

## Verification

Proxmox detected the installed Seagate Exos ST6000NM0115 as a 6 TB disk (approximately 5.5 TiB). Its initial SMART overall status reported `PASSED`.

The disk was later passed through to Debian VM 100, partitioned, formatted as ext4, and mounted at `/srv/media`. Detailed health observations and the incomplete extended test are recorded in the [SMART baseline](../storage/seagate-smart-baseline.md).

## Takeaway

When a USB enclosure is connected but its disk is missing, check the enclosure's own power state before investigating drivers, cables, or disk compatibility.

## Related follow-up

- [x] Review initial SMART attributes.
- [x] Record the extended SMART test being aborted at 80% remaining.
- [x] Select a filesystem and mount design.
- [x] Pass the disk through to Debian and create the ext4 filesystem.
- [ ] Complete an extended SMART self-test during a maintenance window.
- [ ] Confirm the mount source after a VM reboot.
- [ ] Establish independent backups.
