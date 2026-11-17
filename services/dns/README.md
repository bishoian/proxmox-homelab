# DNS Services Plan

This document tracks planned DNS improvements for the homelab. No Dynamic DNS updater or Pi-hole instance has been deployed yet.

## Dynamic DNS

The masked public hostname `m*******.com` currently reaches the Jellyfin reverse proxy through Nginx Proxy Manager. If the home's public IPv4 address changes, the public DNS record may become stale. A Dynamic DNS client will be added to update the domain's DNS record when the WAN address changes.

Implementation decisions:

- Identify the authoritative DNS provider for the masked public hostname and confirm it offers an API or supported Dynamic DNS update method.
- Choose a maintained updater compatible with that provider.
- Keep API tokens in a local environment file or Docker secret. Never commit tokens or other credentials to Git.
- Update only the necessary DNS record(s), and decide whether IPv6 (AAAA) updates are supported and wanted.
- Configure an appropriate update interval and verify that the updater detects and applies an actual address change.
- Document recovery steps for expired tokens, provider outages, or an incorrect DNS record.

The current provider and updater are not yet selected.

## Pi-hole

Pi-hole is planned as a LAN DNS service for network-level ad and tracker domain blocking, along with local DNS records for homelab services.

Potential initial responsibilities:

- Provide DNS resolution for LAN clients.
- Apply blocklists for ads, trackers, and other unwanted domains.
- Host local DNS records for the Proxmox host and selected internal services.
- Provide a stable local name for Nginx Proxy Manager and other approved homelab endpoints.
- Offer query visibility for troubleshooting DNS behavior.

### DHCP decision

The NETGEAR router currently provides the existing home network. Before enabling Pi-hole's DHCP service, decide whether DHCP should remain on the router or move to Pi-hole. Only one DHCP server should normally serve a given LAN segment. If DHCP remains on the router, configure the router's DHCP options to advertise Pi-hole as the DNS resolver if the router supports that setting.

### Network and security boundaries

- Pi-hole is intended for LAN use only.
- Do not expose DNS port 53 or the Pi-hole administration interface directly to the public internet.
- Keep a documented fallback DNS and recovery method so a Pi-hole outage does not strand the LAN without name resolution.
- If VLANs are implemented later, define which client networks may query Pi-hole and how local DNS records should resolve across those segments.
- Keep the public domain's authoritative DNS and Dynamic DNS updater separate from Pi-hole's LAN resolver role.

## Implementation checklist

- [ ] Confirm authoritative DNS provider and available API/update method.
- [ ] Select and deploy a Dynamic DNS updater.
- [ ] Test a DNS update and verify the public record follows the current WAN address.
- [ ] Deploy Pi-hole on a stable LAN address.
- [ ] Configure upstream DNS and initial blocklists.
- [ ] Decide whether DHCP remains on the router or moves to Pi-hole.
- [ ] Configure a small set of local DNS records for homelab services.
- [ ] Point a test LAN client at Pi-hole and validate name resolution and blocking.
- [ ] Document fallback DNS, backup, update, and recovery procedures.
