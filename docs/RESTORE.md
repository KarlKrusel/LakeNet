# LakeNet Restore Checklist

**Version 0.1 (Sprint 1 draft)** · Owner: Karl Krusel

**Goal (Objective 1):** a teammate who did **not** build the lab can rebuild the full segmented network from this repo in **under one hour**, using only this checklist and the files at a sprint tag.

> This is a draft. Steps marked **TBD** get filled in as each part is built: topology and switch from Garrett, firewall from Cameron, logging and NTP from Ty. The real test run happens in Sprint 5.

---

## Before you start

- [ ] Your computer passes [SETUP.md](SETUP.md), or you're connected to the lab host
- [ ] You checked out the right tag: `git checkout sprint-N`
- [ ] You have the lab-only admin credentials from the team's private Drive. **They are never in this repo.**
- [ ] Start a timer and write down the start time: `____`

## Step 1: Open the topology

- [ ] In GNS3, go to **File > Import portable project** and pick `gns3/lakenet.gns3project`
- [ ] Check that these nodes are there: pfSense, Open vSwitch, one Alpine endpoint per VLAN, syslog server
- [ ] Start nodes in this order, waiting for each to finish booting:
  1. Open vSwitch
  2. pfSense
  3. syslog server
  4. endpoints

## Step 2: Restore the switch (Open vSwitch)

- [ ] Open the Open vSwitch console
- [ ] Run the setup script from `configs/ovs/`: **TBD** (file name from Garrett)
- [ ] Check it: `ovs-vsctl show` should show
  - the trunk port to pfSense carrying VLANs 10, 20, 30, 40
  - each access port tagged with the right VLAN
- [ ] Compare it to `configs/ovs/ovs-vsctl-show.txt`. They should match.

## Step 3: Restore the firewall (pfSense)

- [ ] From the admin workstation (VLAN 10), open `https://10.0.10.1`
- [ ] Go to **Diagnostics > Backup & Restore > Restore Backup**
- [ ] Upload the full backup from the team's private Drive (**not** the redacted copy in the repo)
- [ ] Let pfSense reboot, then check:
  - [ ] **Interfaces:** VLAN 10, 20, 30, 40 interfaces exist, each with gateway `.1`
  - [ ] **Firewall > Rules:** each VLAN's rules match the policy matrix
  - [ ] **Services > DHCP:** running on VLAN 20 and 40 only
  - [ ] **Services > NTP:** running (Sprint 2)

## Step 4: Restore logging (Sprint 2–3)

- [ ] Copy `configs/syslog/rsyslog.conf` onto the syslog server and restart rsyslog: **TBD** (exact commands from Ty)
- [ ] In pfSense: **Status > System Logs > Settings > Remote logging** points to the syslog server on UDP 514
- [ ] Check that the syslog server's log file shows new pfSense entries

## Step 5: Restore admin login (Sprint 3)

- [ ] FreeRADIUS config from `configs/freeradius/`: **TBD**

## Step 6: Prove it works

Tick a box only after you've actually run that check.

| Check | Command (run from) | Expected | Done |
|---|---|---|---|
| Four VLANs up | `ovs-vsctl show` (switch) | 10, 20, 30, 40 on the trunk | [ ] |
| User reaches gateway | `ping -c 4 10.0.20.1` (VLAN 20 host) | 4/4 replies | [ ] |
| T-01 User → Server HTTPS | `curl -kI https://<server>` (VLAN 20) | HTTP response = allowed | [ ] |
| T-02 User → Mgmt blocked | `nc -zv -w 3 <admin-pc> 22` (VLAN 20) | Timeout + pfSense block log | [ ] |
| T-05 Guest → User blocked | `ping -c 4 <user-host>` (VLAN 40) | No reply + block log | [ ] |
| T-07 Admin manages devices | SSH to pfSense from admin PC (VLAN 10) | Login prompt | [ ] |
| Logs arrive | check the syslog server log | Entries from the tests above | [ ] |
| Clocks match (Sprint 2) | `date` on each device | Within a few seconds | [ ] |

- [ ] Stop the timer. End time: `____` · Total: `____` minutes (goal: under 60)

## Step 7: Write down how it went

Add a row to `testing/results-log.md`:

| Date | Tag | Restored by | Verified by | Minutes | Unclear steps / questions asked | Pass? |
|---|---|---|---|---|---|---|
| | | | | | | |

If anything was unclear or took too long, open an issue labeled `restore-docs`, assign it to Karl, and fix the step before the next run.

---

## Change log

| Version | Date | Change |
|---|---|---|
| 0.1 | Oct 2026 | First draft: step order, checks, and placeholders for each component |
