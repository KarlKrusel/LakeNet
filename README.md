# LakeNet: Segmented Small-Office Network Redesign

**Team 1 · P06 · CIS 462/463 IT Capstone · Fall 2026**

LakeNet is a lab redesign of a hypothetical 25-person office that runs on a flat network. Today one compromised device can reach everything. We split the office into four VLANs. A pfSense firewall routes all traffic between them and blocks anything we haven't allowed. Logs go to a central syslog server, and every config is stored here so the network can be rebuilt.

Everything runs in **GNS3**. No real hardware, real user accounts, or production systems are used.

## Network at a glance

| VLAN | Name | Subnet | Gateway (pfSense) | Addressing | Who lives here |
|---|---|---|---|---|---|
| 10 | Management | 10.0.10.0/24 | 10.0.10.1 | Static | Admin workstation, syslog server, FreeRADIUS |
| 20 | Users | 10.0.20.0/24 | 10.0.20.1 | DHCP | Staff PCs |
| 30 | Servers | 10.0.30.0/24 | 10.0.30.1 | Static | File / web server (HTTPS 443) |
| 40 | Guest | 10.0.40.0/24 | 10.0.40.1 | DHCP | Visitors, internet only |

Traffic between VLANs goes through pfSense. Open vSwitch carries all four VLANs to pfSense over one 802.1Q trunk.

## Tools

| Tool | Version | Job |
|---|---|---|
| GNS3 | 2.2.61 | Runs the whole lab in one saved project |
| pfSense CE | 2.9.0 | Firewall, inter-VLAN routing, NTP server |
| Open vSwitch | 4.0.0 | VLAN switching and the trunk to pfSense |
| Alpine Linux | 3.24.2 | Lightweight test endpoints in each VLAN |
| rsyslog | 8.2608.0 | Central log server |
| FreeRADIUS | 3.2 | Admin login authentication |

## Repository layout

```
README.md              this file
docs/
  SETUP.md             host + VM requirements, first-time setup
  RESTORE.md           step-by-step rebuild checklist (Objective 1)
  threat-model.md      3 threat scenarios -> control, test, limitation (Sprint 4)
gns3/                  portable GNS3 project export (.gns3project)
configs/
  pfsense/             pfSense config backup (sanitized, see "Secrets" below)
  ovs/                 Open vSwitch bridge/port setup script + `ovs-vsctl show` output
  syslog/              rsyslog.conf
  freeradius/          FreeRADIUS client/user config (no real secrets)
  endpoints/           per-host network settings (Alpine /etc/network/interfaces)
testing/
  results-log.md       one row per test run: ID, date, tester, verifier, expected, actual, pass/fail
  evidence/            screenshots + log excerpts, named T-XX_source-dest_YYYY-MM-DD.png
diagrams/              network diagram source + exports
deliverables/          sprint presentations and reports
```

## Getting started

1. Read [docs/SETUP.md](docs/SETUP.md) to check that your computer can run the lab.
2. To rebuild the network from this repo, follow [docs/RESTORE.md](docs/RESTORE.md).

## How we work in this repo

- **One branch per task**, named `area/short-description`, for example `pfsense/deny-by-default` or `docs/restore-checklist`.
- **Open a pull request** into `main`. At least one teammate reviews it before it gets merged.
- **Link the issue** the PR closes (`Closes #12`), so every change says what it was for.
- **Keep `main` working.** Don't push straight to `main`.
- **Tag every sprint** on the last day: `sprint-1`, `sprint-2`, and so on. The tag is our restore point and our deliverable marker.

## Secrets: what never goes in this repo

- No passwords, RADIUS shared secrets, private keys, or API tokens, in files or in screenshots.
- **pfSense config backups contain password hashes and certificate keys.** Before committing `config.xml`, replace those values with `REDACTED`. Keep the real, unredacted backup (made with the "Encrypt this configuration file" option) in the team's private Drive.
- Lab-only test credentials go in a shared password manager or the private Drive, not here.

## Team

| Member | Area |
|---|---|
| Garrett Hoving | Network topology, switch config, primary contact |
| Ty Hafke | Logging and NTP, backup contact |
| Cameron Woodard | Firewall policy |
| Riley Buckingham | Testing and evidence |
| Karl Krusel | Restore documentation |

Stakeholder: Professor Sorio Boit
