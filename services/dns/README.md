# DNS Services Plan

Dynamic DNS and Pi-hole are planned additions. Neither is deployed yet.

## Dynamic DNS

Dynamic DNS will update the public DNS record when the home's WAN address changes. Jellyfin and Seerr currently use Nginx Proxy Manager with verified HTTPS access, but the DNS provider and update method have not been selected.

- [ ] Identify the authoritative DNS provider and confirm API/update support.
- [ ] Select and deploy an updater.
- [ ] Test an update and confirm the public record follows the current WAN address.
- [ ] Document credential handling, monitoring, and recovery without publishing secrets.

## Pi-hole

Pi-hole is planned for LAN DNS filtering and local DNS records. It should use a stable LAN address and remain private to the home network.

The router currently provides DHCP. Before enabling Pi-hole DHCP, decide whether DHCP remains on the router or moves to Pi-hole. Do not run both DHCP servers on the same LAN without a deliberate, coordinated design.

- [ ] Deploy Pi-hole on a stable LAN address.
- [ ] Configure upstream DNS and initial blocklists.
- [ ] Decide which device provides DHCP.
- [ ] Add local DNS records for homelab services.
- [ ] Test resolution and filtering from a LAN client.
- [ ] Document backup, update, fallback DNS, and recovery procedures.

Do not expose DNS or Pi-hole's administration interface directly to the internet.
