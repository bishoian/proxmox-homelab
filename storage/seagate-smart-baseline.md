# Seagate Exos SMART Baseline

**Drive:** Seagate Exos ST6000NM0115  
**Host:** Lenovo ThinkCentre M920q / Proxmox VE 9.2.2  
**Enclosure:** TerraMaster D4-320 USB DAS  
**Device path at inspection:** `/dev/sda`  
**Inspection date:** 2026-11-15  
**Status:** Initial SMART review completed; extended self-test running.

## Detection

After the D4-320 rear power switch was turned on, Proxmox detected the installed Seagate drive as a 6.00 TB hard disk at `/dev/sda`.

The Proxmox disk inventory reported the drive's overall SMART status as `PASSED`.

## Initial SMART observations

| Attribute | Value displayed | Initial observation |
|---|---:|---|
| Reallocated Sector Count | 0 | No reallocated sectors reported |
| Current Pending Sector | 0 | No pending sectors reported |
| Offline Uncorrectable | 0 | No offline-uncorrectable sectors reported |
| Reported Uncorrect | 0 | No reported uncorrectable errors |
| UDMA CRC Error Count | 0 | No interface CRC errors reported |
| Spin Retry Count | 0 | No spin retries reported |
| Command Timeout | 0 | No command timeouts reported |
| Start/Stop Count | 1 | Low counter value |
| Power Cycle Count | 1 | Low counter value |
| Power On Hours | 0 | Raw value displayed by Proxmox |
| Head Flying Hours | 0h 04m 16.862s | Approximately four minutes and seventeen seconds |
| Temperature | 24°C | Current reported temperature |
| Airflow Temperature | 24°C (minimum 18°C / maximum 24°C) | Reported temperature range |

The SMART table also displays nonzero raw values for Read Error Rate and Seek Error Rate, while their normalized values remain at 100 and above their listed thresholds. Seagate notes that individual SMART attribute raw values and thresholds are vendor-specific and that third-party SMART utilities may display them inconsistently. These raw numbers are not treated as standalone failure indicators.

Seagate's guidance on SMART interpretation: [How do I interpret SMART diagnostic utilities results?](https://www.seagate.com/support/kb/how-do-i-interpret-smart-diagnostic-utilities-results-203971en/)

Seagate's openSeaChest documentation also explains that raw attribute formats vary by drive vendor and that Power On Hours and Head Flight Hours represent different measurements: [Drive Health and SMART](https://github.com/Seagate/openSeaChest/wiki/Drive-Health-and-SMART).

## Assessment

The initial SMART snapshot is encouraging:

- Overall SMART status is `PASSED`.
- No reallocated, pending, or offline-uncorrectable sectors are reported.
- No reported uncorrectable errors or UDMA CRC errors are shown.
- The reported temperature is 24°C.

The Power On Hours raw value of zero and low cycle counters should not be interpreted as proof that a renewed drive has never been used. The values are recorded as displayed and will be considered alongside the extended self-test and future monitoring.

## Extended self-test

The extended SMART self-test was started with `smartctl -t long /dev/sda`. The drive reported an estimated duration of approximately 10 hours. The test runs in the drive's background; the Proxmox web interface does not need to remain open, but the Lenovo and D4-320 should remain powered on and connected. The result has not yet been collected.

To review the result after the estimated completion time, run:

```bash
smartctl -l selftest /dev/sda
smartctl -a /dev/sda
```

## Next test

- [x] Start the extended SMART self-test.
- [ ] Wait for the background test to complete (estimated duration approximately 10 hours).
- [ ] Check the self-test result and any error log entries.
- [ ] Recheck key SMART attributes after the test.
- [ ] Continue to monitor temperature and error counters during initial use.

No partitioning, formatting, filesystem creation, or Proxmox storage-pool configuration has been performed during this inspection.
