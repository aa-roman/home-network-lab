# Network Design — Home Security Lab

Single source of truth for the lab: zones, addresses, hosts, and the policy
that ties them together. Every project adds its piece here.

## Zones / IP plan

| Zone | VirtualBox network | Subnet | Gateway (fw01) | Hosts |
| --- | --- | --- | --- | --- |
| LAN (trusted) | lab-lan | 192.168.10.0/24 | 192.168.10.1 | kali, WS01 |
| SERVERS | lab-servers | 192.168.20.0/24 | 192.168.20.1 | DC01, wazuh |
| GUEST (untrusted) | lab-guest | 192.168.30.0/24 | 192.168.30.1 | test device |
| MGMT | lab-mgmt | 192.168.99.0/24 | 192.168.99.1 | admin01 |
| VPN | WireGuard | 10.10.10.0/24 | 10.10.10.1 | remote laptop (.2) |

## Hosts

| Host | Zone | IP | Role | Added in |
| --- | --- | --- | --- | --- |
| fw01 | gateway | .1 in each zone | OPNsense firewall / router | P1 |
| kali | lab-lan | DHCP | Workstation / attacker | P1 |
| admin01 | lab-mgmt | 192.168.99.10 | Management box (drives the GUIs) | P2 |
| DC01 | lab-servers | 192.168.20.10 | Domain controller (corp.internal) | P4 |
| WS01 | lab-lan | DHCP | Employee workstation | P4 |
| wazuh | lab-servers | 192.168.20.20 | Wazuh SIEM | P5 |

## Firewall rules

- GUEST: DNS to fw01; all RFC1918 blocked+logged; internet allowed. (P2)
- SERVERS: DNS to fw01; RFC1918 blocked+logged; web egress 80/443 only. (P2)
- MGMT: DNS + consoles (443) to fw01 + wazuh; RFC1918 blocked+logged; web egress 80/443; only zone that reaches the GUIs. (P2)
- LAN -> SERVERS: AD ports + Wazuh agent 1514/1515 (allowed broadly in P2, scoped to these ports in Week 6/P4).
- LAN -> GUEST, LAN -> MGMT: blocked+logged. (P2)
- VPN: wazuh:443 only, logged; everything else blocked+logged. (P8)

## Firewall policy matrix

| From v / To > | LAN | SERVERS | GUEST | MGMT | Internet |
| --- | --- | --- | --- | --- | --- |
| LAN | - |  |  |  |  |
| SERVERS |  | - |  |  |  |
| GUEST |  |  | - |  |  |
| MGMT |  |  |  | - |  |
| VPN |  |  |  |  |  |

## IPv6

Decision (P2): __________  (e.g. disabled on all interfaces + Windows via
DisabledComponents = 0xFF; or RA Guard + mirrored v6 rules in production).

## Change log

- 2026-11-07 - P0: repo and this file created.
