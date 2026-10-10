# Architecture

## Network
All VMs on VirtualBox Internal Network `soclab` (192.168.56.0/24), isolated
from the host LAN. NAT Network used only for package installs / dashboard
access, not for inter-VM traffic.

| VM | Role | OS | IP (soclab) | Specs |
|----|------|----|----|----|
| VM1 | Wazuh Manager | Ubuntu Server 24.04 | 192.168.56.10 | 50GB, 2GB RAM, 2 CPU |
| VM2 | Victim | Ubuntu Server 24.04 | 192.168.56.20 | 25GB, 2GB RAM, 2 CPU |
| VM3 | Attacker | Kali Linux | 192.168.56.30 | 40GB, 2GB RAM, 2 CPU |

## Dashboard access
Wazuh dashboard reachable from the host via NAT port forwarding
(host `8443` → VM1 `443`), since VirtualBox Host-only networking was
non-functional on this setup — documented in [key-learnings.md].

## Diagram
```
[Host Browser] --https:8443--> [NAT] --> [VM1 Manager:443]
                                              |
                          soclab (192.168.56.0/24, isolated)
                                              |
                    [VM2 Victim:.20]  <---  [VM3 Kali:.30]
                      (agent, SSH target)      (attacker)
```
