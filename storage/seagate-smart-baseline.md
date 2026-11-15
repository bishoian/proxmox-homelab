# Seagate Exos SMART Baseline

**Drive:** Seagate Exos ST6000NM0115  
**Host:** Lenovo ThinkCentre M920q / Proxmox VE 9.2.2  
**Enclosure:** TerraMaster D4-320 USB DAS  
**Device path at inspection:** `/dev/sda`  
**Inspection date:** 2026-11-15  
**Status:** Initial SMART attributes reviewed; extended self-test intentionally aborted before completion.

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

## Initial assessment

The initial SMART snapshot is encouraging:

- Overall SMART status is `PASSED`.
- No reallocated, pending, or offline-uncorrectable sectors are reported.
- No reported uncorrectable errors or UDMA CRC errors are shown.
- The reported temperature is 24°C.

The Power On Hours raw value of zero and low cycle counters should not be interpreted as proof that a renewed drive has never been used. The initial snapshot is not a substitute for a complete extended read test.

## Extended self-test result

The extended SMART test was started with `smartctl -t long /dev/sda`. The drive estimated approximately 10 hours for the test. It was intentionally stopped with `smartctl -X /dev/sda`.

The resulting self-test log showed:

| Field | Result |
|---|---|
| Test description | Extended offline |
| Status | Aborted by host |
| Remaining | 80% |
| Power-on lifetime at test log | 1 hour |
| LBA of first error | No value shown |

The test had progressed approximately 20% before it was stopped. The log contains no first-error LBA for this aborted entry. This is an incomplete test, not a successful extended-test result and not, by itself, evidence of a disk failure.

## Current decision

The first Seagate will be used for the initial media-services build based on the encouraging initial SMART snapshot. The incomplete extended test will remain documented. The disk will be monitored during initial use, and important media or application data will require a separate backup.

A completed extended SMART self-test can be run during a later maintenance window.

## Follow-up

- [x] Capture the initial SMART attribute table.
- [x] Start the extended SMART self-test.
- [x] Stop the test early and record the Aborted by host result.
- [ ] Consider a completed extended SMART self-test during a later maintenance window.
- [ ] Recheck key SMART attributes during routine maintenance.
- [ ] Monitor temperature and error counters during initial use.

No partitioning, formatting, filesystem creation, or Proxmox storage-pool configuration has been performed during this inspection.