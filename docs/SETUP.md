# LakeNet Setup: Host and VM Requirements

This page answers one question: **can my computer run the LakeNet lab, and how do I get it ready?** Read it before you follow [RESTORE.md](RESTORE.md).

> Lines marked **TBD** still need real numbers from our lab host. Update them as we confirm them.

## 1. Which computer runs the lab

| | |
|---|---|
| Lab host (the machine that runs the GNS3 VM) | **TBD:** owner, OS, CPU, RAM |
| How other teammates connect | GNS3 client on their own laptop, pointed at the lab host as a remote server, **or** each person runs their own copy |

### Apple Silicon Macs (M1/M2/M3/M4)

The GNS3 VM and the pfSense image are **x86 only**, so they won't run on an Apple Silicon Mac. If you have one:

- Install the GNS3 **client** only, and connect it to the lab host as a remote server (GNS3 > Preferences > Server), **or**
- Work on the lab host directly (remote desktop or in person).

Don't try to run the full lab under emulation. It's too slow to test with.

## 2. Host requirements

| | Minimum | Recommended |
|---|---|---|
| CPU | 4 cores, x86-64, **VT-x / AMD-V turned on in BIOS** | 6+ cores |
| RAM | 12 GB | 16 GB or more |
| Free disk | 30 GB | 50 GB (SSD) |
| OS | Windows 10/11, Linux, or Intel Mac | |
| Hypervisor for the GNS3 VM | VMware Workstation Player / Fusion (best support) or VirtualBox | |

## 3. What runs inside the lab

These are starting estimates. Measured values: **TBD**.

| Device | Runs as | vCPU | RAM | Count |
|---|---|---|---|---|
| pfSense CE 2.9.0 | QEMU VM | 1 | 1 to 2 GB | 1 |
| Open vSwitch 4.0.0 | GNS3 appliance (Docker) | 1 | 256 MB | 1 |
| Alpine 3.24.2 endpoints | GNS3 appliance (Docker) or QEMU | 1 | 128 to 256 MB | 1 per VLAN to start (4) |
| rsyslog server | Alpine or Debian VM | 1 | 512 MB | 1 |
| FreeRADIUS 3.2 | Alpine or Debian VM | 1 | 512 MB | 1 (Sprint 3) |
| **Total inside the GNS3 VM** | | | **about 4 GB** | |

Give the GNS3 VM at least **6 GB RAM and 4 vCPUs** so the host has room left over.

## 4. First-time setup

1. Install **GNS3 2.2.61** (the GUI) from gns3.com.
2. Download and import the **GNS3 VM 2.2.61** for your hypervisor. Set its RAM and vCPUs to the values in section 3.
3. In GNS3, open **Preferences > GNS3 VM**, turn it on, and check that it shows green.
4. Import the appliances from the GNS3 marketplace: **Open vSwitch**, **Alpine Linux**, **pfSense**.
5. For pfSense, download the **pfSense CE 2.9.0** installer image from Netgate when the appliance wizard asks for it.
6. Check the setup: drag one Alpine node onto a blank project and start it. If the console opens, you're ready for [RESTORE.md](RESTORE.md).

## 5. Versions we've confirmed

| Item | Version | Confirmed on | By |
|---|---|---|---|
| GNS3 GUI | 2.2.61 | **TBD** | |
| GNS3 VM | 2.2.61 | **TBD** | |
| Hypervisor | **TBD** | **TBD** | |
| pfSense CE | 2.9.0 | **TBD** | |
| Open vSwitch | 4.0.0 | **TBD** | |
| Alpine | 3.24.2 | **TBD** | |

## 6. Common problems

| Problem | Likely cause | Fix |
|---|---|---|
| GNS3 VM won't start | Virtualization is off | Turn on VT-x / AMD-V in BIOS. On Windows, turn off Hyper-V if you're using VMware/VirtualBox. |
| Nodes start, then the host freezes | Not enough RAM | Lower the GNS3 VM's RAM, or start fewer endpoints. |
| Can't open the pfSense web GUI | Coming from the wrong VLAN | Only the admin workstation in VLAN 10 is allowed in. |
| Mac says "not supported" | Apple Silicon | See section 1: connect to the lab host instead. |
