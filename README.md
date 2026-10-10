# Wazuh SOC Home Lab

A self-contained, isolated 3-VM environment simulating a SOC analyst workflow:
attack simulation, SIEM detection, and (attempted) automated response.

## Architecture
- **VM1 — Wazuh Manager** (Ubuntu Server 24.04): SIEM, dashboard, correlation rules
- **VM2 — Victim** (Ubuntu Server 24.04): Wazuh agent, SSH target, FIM demo
- **VM3 — Attacker** (Kali Linux): recon + brute-force tooling

All three VMs sit on an isolated VirtualBox Internal Network (no access to the
host LAN). See [docs/architecture.md](docs/architecture.md) for network diagram
and IP layout.

## What this demonstrates
1. **Recon** — `nmap` service/version scan against the victim
2. **Attack** — SSH brute-force via `hydra` against a deliberately weak account
3. **Detection (raw)** — Wazuh logs individual authentication failures
   (rule 5760, level 5)
4. **Detection (correlated)** — Wazuh's brute-force correlation rule (5763,
   level 10) fires after repeated failures from the same source IP; a second,
   higher-severity rule (40112, level 12) fires when a successful login
   follows the failure burst
5. **File Integrity Monitoring** — real-time detection of file add, modify,
   and delete events on a watched directory
6. **Active-response (investigated, not completed)** — see
   [docs/active-response-findings.md](docs/active-response-findings.md) for a
   root-cause writeup of a dispatch failure found during testing

Full walkthrough with screenshots: [docs/attack-detection-walkthrough.md](docs/attack-detection-walkthrough.md)

## Stack
Wazuh 4.9.2 (indexer + manager + dashboard, all-in-one) · Ubuntu Server 24.04 ·
Kali Linux · VirtualBox · hydra · nmap

## Notes
Built and tested entirely on an isolated VirtualBox Internal Network, with no
exposure to the host LAN. All target credentials and wordlists were created
specifically for this lab and hold no real-world value.
