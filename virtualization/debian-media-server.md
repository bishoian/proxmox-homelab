# Debian Media Server VM Build

**VM ID:** 100  
**VM name:** `media-server`  
**Hypervisor:** Proxmox VE on Lenovo ThinkCentre M920q Tiny  
**Guest OS:** Debian GNU/Linux 13.7 (Trixie), amd64  
**Build status:** Debian 13.7 installed; first login verified as `ian`; hostname and active network interface verified. DNS, internet access, SSH, and QEMU Guest Agent checks remain.

## VM configuration

| Setting | Value |
|---|---|
| vCPU | 1 socket, 4 cores |
| CPU type | `host` |
| Memory | 4096 MiB |
| System disk | 64 GiB on `local-lvm` |
| Disk bus | SCSI, VirtIO SCSI single |
| Disk options | Discard, IO thread, SSD emulation enabled |
| Firmware | SeaBIOS |
| Machine | Default i440fx |
| QEMU Guest Agent | Enabled in VM configuration; guest package still needs installation |
| Network | VirtIO NIC on `vmbr0`, Proxmox firewall enabled |
| Installer media | Debian 13.7 amd64 netinst ISO |

## Debian installer choices

- Hostname: `media-server`
- Domain name: left blank
- Root password: left blank, allowing Debian to lock direct root login and use the regular account with sudo privileges
- Network: installer configured through the VM's VirtIO network interface
- Disk partitioning: guided, all files in one partition with a separate swap area
- Desktop environment: not selected
- SSH server: selected
- Standard system utilities: selected
- GRUB bootloader: installed to the VM's virtual system disk

## Guest disk layout

The Debian installer detected one approximately 68.7 GB QEMU virtual disk. The physical Seagate disk was not attached to this VM during installation.

| Partition | Approximate size | Filesystem / role | Mount |
|---|---:|---|---|
| Primary partition | 65.2 GB | ext4 | `/` |
| Logical partition | 3.5 GB | swap | swap |

The partition changes were written only to the VM's virtual system disk. The Seagate Exos data disk remains on the Proxmox host and has not been passed through, partitioned, or formatted.

## Installation result

Debian displayed the installer completion screen after the base system and selected packages had been installed. GRUB installation was confirmed for the VM's virtual disk.

The VM has booted from its installed Debian system. Login succeeded with the `ian` account. The `hostnamectl` output confirmed hostname `media-server`, Debian GNU/Linux 13 (Trixie), kernel `6.12.17+deb13-amd64`, and x86-64 architecture. The `ip -br addr` output showed the VirtIO interface `ens18` UP with an IPv4 address assigned. DNS resolution has been verified with `getent hosts deb.debian.org`; the SSH service reports `active` via `systemctl is-active ssh`; `sudo -v` completed without an authentication error. The default route is present through the home gateway. `sudo apt update` successfully contacted the Debian Security, Trixie, and Trixie Updates repositories and reported all packages up to date. SSH login from another LAN device has not yet been tested. Confirm the installer ISO remains detached from the virtual CD/DVD drive to prevent a later reboot from returning to the installer.

## First-boot checklist

- [ ] Set the VM's CD/DVD drive to **Do not use any media** in Proxmox Hardware.
- [ ] Reboot VM 100.
- [ ] Confirm Debian reaches its login prompt.
- [ ] Log in with the regular account created during installation.
- [ ] Confirm the guest obtained a LAN IP address.
- [x] Verify DNS resolution for `deb.debian.org`.
- [x] Verify outbound package repository connectivity with `sudo apt update`.
- [x] Confirm the SSH service is active.
- [ ] Verify SSH login from another LAN device.
- [x] Confirm `qemu-guest-agent` package is installed at the current repository version.
- [x] Confirm `systemctl is-active qemu-guest-agent` returns `active`.
- [x] Verify Proxmox-side communication with `qm agent 100 ping`; it returned silently without an error.
- [ ] Apply Debian updates.
- [x] Record the guest's hostname, Debian release, kernel, architecture, and interface status.
- [ ] Record the guest package baseline after updates.

## QEMU Guest Agent

The `qemu-guest-agent` package was already installed at the current repository version. The service check returned `active`. An attempt to use `systemctl enable --now` printed the expected no-installation-config message because the Debian unit is static; the agent is active, so it does not need to be enabled with `systemctl enable`. The output also included `systemd-ssh-generator` AF_VSOCK diagnostics, which are separate from the QEMU Guest Agent service. Proxmox-side agent communication was verified from the host with `qm agent 100 ping`, which completed without output or error, the expected successful response.

## Initial guest verification

| Check | Result |
|---|---|
| Account login | `ian` |
| Hostname | `media-server` |
| Operating system | Debian GNU/Linux 13 (Trixie) |
| Kernel | `6.12.17+deb13-amd64` |
| Architecture | x86-64 |
| Virtualization | KVM |
| Network interface | `ens18`, UP |
| IPv4 | DHCP address assigned; exact private address omitted from this public record |
| Default route | Present through home gateway |
| DNS | `deb.debian.org` resolved successfully |
| APT repository access | Debian Security, Trixie, and Trixie Updates reached successfully; APT reported all packages up to date |
| SSH daemon | Service reports `active` |
| Sudo | `sudo -v` succeeded |

## Planned media storage

The 6 TB Seagate Exos will be passed through to this Debian VM after first-boot configuration. It will be formatted as ext4 inside Debian and mounted for media, downloads, and application data. The second 6 TB drive remains reserved for future expansion.
