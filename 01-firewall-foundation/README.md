# P1 - Firewall Foundation

**What this is:** An OPNsense firewall/router (`fw01`) that sits between an isolated lab LAN and the internet, with a Kali workstation behind it. Every bit of lab traffic to the outside world passes through - and is controlled by - this one box.

## Goal
Stand up the firewall that every later zone and rule depends on: one router between an isolated LAN and the internet, reachable only through an admin login.

## What I built
- Built `fw01`, an OPNsense VM, with two NICs: **WAN** (`em0`, NAT) and **LAN** (`em1`, Internal Network `lab-lan`).
- Assigned interfaces by MAC (em0 → WAN, em1 → LAN) and set the LAN to `192.168.10.1/24`, DHCP range `.100–.200`.
- Set upstream DNS (`1.1.1.1` / `9.9.9.9`) on the firewall so every zone resolves names through fw01.
- Built `kali` on `lab-lan`; it pulled `192.168.10.100` by DHCP and reached the internet through the firewall.
- Verified connectivity from Kali:
    ping -c 4 8.8.8.8
    ping -c 4 opnsense.org
- Enabled **TOTP two-factor** on the root admin login.
- Downloaded a **config backup** (kept out of the repo - it holds secrets; `config.xml` is in `.gitignore`).
- Snapshotted both VMs as `P1-working`.

## Key decisions
- **Firewall as the only path out (NAT WAN + isolated LAN):** a compromised lab VM can't reach my host or home network - the firewall is the single, controlled chokepoint.
- **DNS set on the firewall:** every zone forwards DNS to fw01; without an upstream resolver here, nothing resolves and later updates/activation fail.
- **TOTP two-factor on admin:** a guessed or stolen root password alone no longer gets anyone in - they'd also need the time-based code from my phone. High-value control on the box that controls the whole network.
- **Config backup:** restore in minutes instead of rebuilding; kept off the repo because it contains keys and password hashes.

## Screenshots
<img width="1282" height="833" alt="opnsense-dashboard" src="https://github.com/user-attachments/assets/615b9074-9f66-4dc4-a1aa-ca4934a24b43" />

*OPNsense dashboard for `fw01` - the firewall up and routing after install.*

<img width="833" height="731" alt="successful-ping" src="https://github.com/user-attachments/assets/103d2fac-3b4e-448c-8746-3fa269a06a21" />

*Successful pings from Kali (`192.168.10.100`) to `8.8.8.8` and `opnsense.org` — proof the workstation reaches the internet through fw01.*

## What went wrong (and how I fixed it)
- **Interface assignment:** auto-detection (`a`) failed in a VM ("no link-up detected"), and `lab-lan` (the VirtualBox network name) isn't a valid interface name - OPNsense wanted the device names `em0`/`em1`. Matched them by MAC and assigned manually.
- **Break/fix drill:** disconnected Kali's virtual network adapter → it lost its IP and every ping failed; reconnected → DHCP lease returned in seconds. Lesson: *"no link, no address"* is the first thing to check on a connectivity ticket.

## What I learned
- `/24` = 256 addresses (254 usable); `192.168.x.x` is private.
- A firewall is a router with rules - WAN/LAN assignment and NAT are what give the lab internet while keeping it isolated.
- Two-factor on an admin interface raises the security bar.
