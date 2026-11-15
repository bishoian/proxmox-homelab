# Package Repository Configuration

**Host:** Proxmox VE 9.2.2  
**Base distribution:** Debian 13 (Trixie)  
**Status:** Repository configuration completed; package-list refresh successful; upgrade review pending.

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
- [ ] Review the available package list and any dependency or repository errors.
- [ ] Apply updates after reviewing the proposed changes.
- [ ] Reboot if a kernel or other reboot-requiring component is updated.
- [ ] Verify Proxmox VE version and node health after updates.

The refresh task completed with `TASK OK` and reported 530 kB fetched. No package upgrade has been performed yet.
