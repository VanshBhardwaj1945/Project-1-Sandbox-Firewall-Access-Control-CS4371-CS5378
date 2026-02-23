# Project-1: Sandbox, Firewall & Access Control  
**Repo:** https://github.com/VanshBhardwaj1945/Project-1-Sandbox-Firewall-Access-Control-CS4371-CS5378

## Table of Contents

1. [Overview](#overview)  
2. [Sandbox Components](#sandbox-components)  
3. [Tools & Technologies](#tools--technologies-used)  
4. [Network Topology](#network-topology)  
5. [Setup](#setup-high-level-steps)  
6. [Discovery & Baseline Validation](#discovery--baseline-validation)  
   - [Selected Baseline Captures](#selected-baseline-captures)  
   - [Nmap Baseline Examples](#nmap-baseline-examples)  
7. [Security Policy Implementation](#security-policy-implementation)  
   - [Access Control Matrix](#access-control-matrix)  
   - [pfSense Rules](#pfsense-rules-visual-snapshots)  
8. [Post-Implementation Verification](#post-implementation-verification)  
9. [Host-Level Firewall Configuration](#host-level-firewall-server-a1)  
10. [Summary Outcomes](#summary-outcomes)


## Overview
A repeatable VirtualBox-based sandbox designed to implement, enforce, and verify network security policies. The environment models an internal company network and an external attacker network separated by a pfSense virtual router. The project demonstrates how to combine router-level rules and host-level firewall controls and how to validate enforcement using active scanning and packet captures.

## Sandbox components
- **Network A (Internal / Company)** — Ubuntu Desktop (server) and Windows XP (workstation)  
- **Network B (External / Attacker)** — Kali Linux (attacker / scanner) and Windows 95 (legacy)  
- **Router / Firewall** — pfSense virtual appliance connecting Network A and Network B

**Notes / lessons learned**
- Resolved legacy VM patching and NIC misconfiguration issues to keep the environment reproducible.  
- Identified policy items that require host-level enforcement when gateway rules were insufficient.  
- Captured configuration and verification artifacts so the sandbox can be re-created for future testing.

---

## Tools & technologies used
- **Virtualization:** VirtualBox (VM creation, NIC binding, snapshots)  
- **Router & firewall:** pfSense (interface configuration, rule authoring, logging)  
- **Operating systems:** Ubuntu, Kali Linux, Windows XP, Windows 95 (legacy)  
- **Network analysis & scanning:** Wireshark, tcpdump, Nmap / Zenmap  
- **Services:** Apache (HTTP), OpenSSH (SSH)  
- **Host hardening:** iptables (Ubuntu)  
- **Testing & validation:** curl, ping, ssh

---

## Network topology

<img src="https://i.imgur.com/0PbmIyM.png" width="60%">

*A.1 = Ubuntu (server), A.2 = Windows XP (workstation), pfSense = router, B.1 = Kali, B.2 = Windows 95 (legacy).*

---

## Setup (high-level steps)

1. Installed Oracle VirtualBox and provisioned VMs for Ubuntu, Kali, Windows XP, and Windows 95.  
   <img src="https://i.imgur.com/iG1MSqE.png" width="60%">

2. Configured pfSense with two interfaces: `LAN` for Network A and `OPT1` for Network B.  
   <img src="https://i.imgur.com/ccb1EGK.png" width="60%">

3. Assigned static IPs and verified NIC-to-subnet mappings.  
4. Installed Apache and OpenSSH on the internal server (A.1) and verified services.  
5. Installed Wireshark and Nmap on attacker and server VMs for traffic capture and scanning.  
6. Validated baseline connectivity using `ping`, `curl`, and `ssh`.  
7. Documented environment quirks (snapshots + notes) to ensure reproducibility.

---

## Discovery & baseline validation
Performed discovery scans and packet captures to establish a clear baseline before any policy changes.

- Nmap scans from the attacker VM (Kali) to enumerate services and ports.  
- Wireshark/tcpdump captures during ping, curl, and ssh to verify normal traffic patterns.  
- Baseline evidence saved for before/after comparison.

### Selected baseline captures
**Ping and curl from attacker → server**  
<img src="https://i.imgur.com/Cfs9b6y.png" width="45%">

**SSH from attacker → server**  
<img src="https://i.imgur.com/Bu5DlBx.png" width="45%">

**pfSense packet capture (attacker → server)**  
<img src="https://i.imgur.com/P3JaWdy.png" width="45%">

**Ping from attacker → workstation**  
<img src="https://i.imgur.com/amkWHpx.png" width="45%">

**Failed curl/ssh from attacker → workstation (expected after rules)**  
<img src="https://i.imgur.com/borAgQu.png" width="45%">

**Ping & curl within external network**  
<img src="https://i.imgur.com/9Ku8xDv.png" width="45%">

**Internal workstation → internal server traffic**  
<img src="https://i.imgur.com/aPPdxJM.png" width="45%">

### Nmap baseline examples
<img src="https://i.imgur.com/pMhYda2.png" width="60%">
<img src="https://i.imgur.com/NrFsGvb.png" width="60%">

---

## Security policy implementation
Policy enforcement was implemented primarily in pfSense. Host-level controls (iptables) were used for policy items the router could not express.

**Corporate policy summary**
- Server: HTTP and SSH allowed internally; HTTP allowed externally (read-only).  
- Workstations: allowed to initiate internal access but not host external services.  
- Server must not initiate external connections.  
- External hosts should not be able to ping internal hosts.

### Access control matrix
<img src="https://i.imgur.com/p4HTJUe.png" width="60%">

### pfSense rules (visual snapshots)
**WAN rules**  
<img src="https://i.imgur.com/DOAkzbD.png" width="60%">

**LAN rules**  
<img src="https://i.imgur.com/TYuD7lH.png" width="60%">

**OPT1 rules**  
<img src="https://i.imgur.com/7YP6lje.png" width="60%">

---

## Post-implementation verification
- Nmap confirmed only authorized services were exposed after rules applied.  
- Wireshark captures validated that blocked traffic did not traverse internal interfaces.  
- System and firewall logs corroborated rule enforcement.

<img src="https://i.imgur.com/bZeB44P.png" width="60%">
<img src="https://i.imgur.com/nYRv0ry.png" width="60%">
<img src="https://i.imgur.com/DthG8qW.png" width="60%">

---

## Host-level firewall (server A.1)
Applied host-level iptables rules to enforce policies that could not be implemented at the gateway.

```bash
sudo iptables -A OUTPUT -d 192.168.20.0/24 -j DROP
sudo iptables -A INPUT -p tcp --dport 22 -s 192.168.10.0/24 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p icmp -s 192.168.10.0/24 -j ACCEPT
```
<img src="https://i.imgur.com/f1mLB6a.png" width="60%"> <img src="https://i.imgur.com/W7tmMXL.png" width="60%">

## Summary Outcomes
- Created a reproducible lab environment for network policy testing and security validation.
- Implemented defense-in-depth with combined router and host controls.
- Validated enforcement and produced reproducible evidence (scans, captures, and logs) to support detection and remediation workflows
