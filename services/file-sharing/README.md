# Shared File Storage

I use the second 6 TB Seagate disk in my TerraMaster D4-320 as general-purpose shared storage for devices on my home LAN. I keep it separate from the media/download filesystem at `/srv/media`.

## Storage design

- **Enclosure:** TerraMaster D4-320 USB direct-attached storage.
- **Consumer:** Debian media-services VM 100.
- **Passthrough:** Separate virtual SCSI device (SCSI2).
- **Filesystem:** GPT partition with ext4, filesystem label `shared`.
- **Mount point:** `/srv/shared`, configured through a UUID-based `/etc/fstab` entry.
- **Share name:** `Shared`.
- **Protocol:** SMB 3-capable Samba service over TCP 445.
- **Scope:** Home LAN only. I do not forward SMB from the internet or publish it through Nginx Proxy Manager.

I omit the disk serial, filesystem UUID, and private LAN address from this repository.

## Directory layout

I created the initial directory structure:

```text
/srv/shared/
├── Games/
├── ISOs/
├── Backups/
├── Documents/
├── Installers/
└── General/
```

I can expand this structure as I add other shared files.

## Linux permissions and accounts

I created a Linux group named `shared` with GID 1500. My Linux account uses UID 1000, and the second Samba identity uses UID 1001. I added both identities to the shared group and configured the shared directories with group ownership and setgid permissions. The directories use mode `2770`, and Samba creates files with group-writable permissions.

I configured two individual Samba accounts. I do not publish their passwords, password database entries, or the local account environment file. The local `samba-accounts.env` file is root-owned with mode `600` and is excluded from Git.

## Docker deployment

I run Samba as a service in my media-stack Compose project using:

- Image: `ghcr.io/servercontainers/samba:smbd-only-latest`.
- Container name: `samba`.
- Host storage: `/srv/shared`.
- Container storage: `/shares/shared`.
- SMB share: `Shared`.
- Published port: TCP 445, bound to the Debian VM's LAN interface.
- Authentication: Individual accounts; guest access disabled.
- Permissions: Shared Linux group enforced for file creation.

I keep the LAN bind address and second account name as local Compose interpolation settings rather than publishing private network details or personal account names. The local `.env` file supplies those values. The account environment file remains separate and is not committed.

## Verification

I completed the following checks:

- I confirmed that the second disk appeared separately inside Debian and did not replace the existing media disk.
- I checked its initial SMART report. The overall health assessment passed, the captured reallocated/pending/uncorrectable/CRC error attributes were zero, and the reported temperature was 28°C. No self-test has been recorded yet.
- I verified the ext4 filesystem mount with `mount -a`, `findmnt`, and `df -hT`.
- I confirmed that the Samba container reached healthy status and that TCP 445 was listening on the VM's LAN interface.
- I confirmed both accounts were present in Samba's account database and belonged to the shared group.
- I authenticated locally as each account and tested directory listing, creation, and removal through SMB.
- I connected my Windows PC to the share using its Samba credentials and mapped it as a persistent network drive.

I have not yet verified the mount and Samba service after a full Debian VM reboot. I also have not tested access from my girlfriend's separate PC because she has not moved in yet.

## Windows connection

From a Windows PC on my home LAN, I connect to:

```text
\\<DEBIAN-LAN-ADDRESS>\Shared
```

I mapped the share as a persistent network drive. Windows initially displayed a generic Explorer access error, but I successfully authenticated with `net use` using the Samba account and then accessed the share. I do not publish the actual private LAN address.

## Remaining work

- [ ] I will reboot the Debian VM and verify that `/srv/shared` mounts automatically.
- [ ] I will verify that Samba starts healthy and that the Windows network drive reconnects after the VM reboot.
- [ ] I will test the second account from my girlfriend's PC after she moves in.
- [ ] I will establish independent backups for important shared files and test a restore.
- [ ] I will document the share's maintenance and account-management process.
