# Package Repository Configuration

**Host:** Proxmox VE 9.2.2  
**Base distribution:** Debian 13 (Trixie)  
**Status:** Repository refresh, package upgrade, and post-upgrade reboot completed; running versions verified.

## Enabled repositories

| Repository | Distribution / suite | Component | Status |
|---|---|---|---|
| Debian base | Trixie and Trixie Updates | Main and configured Debian components | Enabled |
| Debian Security | Trixie Security | Main and configured Debian components | Enabled |
| Proxmox VE | Trixie | `pve-no-subscription` | Enabled |
| Ceph | Trixie | Ceph Tentacle `no-subscription` | Enabled |

Configured Proxmox source endpoints:

- Proxmox VE: `http://download.proxmox.com/debian/pve`
- Ceph Tentacle: `http://download.proxmox.com/debian/ceph-tentacle`

## Disabled repositories

| Repository | Reason |
|---|---|
| Proxmox VE Enterprise | Requires an active subscription |
| Ceph Squid Enterprise | Requires an active subscription; older Ceph release channel |

The Proxmox VE and Ceph no-subscription channels display a warning that they are not recommended for production use. This configuration is intended for a personal homelab rather than a production environment.

The Ceph repository is configured for future package availability and learning. Ceph itself is not installed as a cluster and is not part of the current single-node storage design.

## Next steps

- [x] Refresh package lists from **Node → Updates → Refresh**.
- [x] Confirm Debian, Proxmox VE no-subscription, and Ceph Tentacle no-subscription sources were reached and package lists were read successfully.
- [x] Review the available package list.
- [x] Apply the available package upgrades.
- [x] Confirm the Updates view reports no remaining upgrades.
- [x] Rebooted after package upgrades to activate the updated kernel.
- [x] Verified running kernel `7.0.14-29-pve` and `pve-manager` `9.2.27`.

The refresh task completed with `TASK OK` and reported 530 kB fetched. The available package upgrades were subsequently applied without reported errors. The Updates view now reports no available updates. The package list included a newer Proxmox kernel and manager package; the host was rebooted and the running kernel was verified as `7.0.14-29-pve`; `pve-manager` is `9.2.27`.
