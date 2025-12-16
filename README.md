# Project-1: Sandbox, Firewall & Access Control

## Description & Background

This project builds a clean, repeatable virtual sandbox to design, enforce, and verify network security policies.
Using VirtualBox and a pfSense virtual router, we created two segmented subnets (internal and external) and deployed four virtual machines to emulate a small company environment.
The lab demonstrates how to combine router-level rules and host-level firewalls to achieve policy goals and how to validate enforcement using scanning and packet-capture tools.

**The sandbox consists of:**
- **Network A (Internal / Company):** Ubuntu Desktop (A.1 — server) and Windows XP (A.2 — workstation)
- **Network B (External):** Kali Linux (B.1 — attacker / scanner) and Windows 95 (B.2 — legacy)
- **Router:** pfSense virtual appliance connecting Network A and Network B

***Notes:***
- encountered and resolved practical issues such as legacy VM patches (Windows 95), IP/NIC misconfigurations, and identifying which policy items required host-level enforcement rather than router-only rules.
- There were a lot of tests and trials because this was the first time using and applying networking concepts

---

## Tools & Technologies Used

Key tools and skills demonstrated (employer-friendly):

- **Virtualization:** Oracle VirtualBox — VM creation, NIC binding, snapshotting  
- **Router & firewall:** pfSense — interface configuration, rule authoring, logging  
- **Operating systems:** Ubuntu Desktop, Windows XP, Kali Linux, Windows 95  
- **Network analysis & scanning:** Wireshark, Nmap  
- **Services:** Apache (HTTP), OpenSSH (SSH)  
- **Host hardening:** iptables (Ubuntu)  
- **Testing & validation:** curl, ping, ssh  

---

## Network Topology

Logical diagram showing networks, router, and hosts:

<img src="https://i.imgur.com/0PbmIyM.png" width="60%">

*A.1 = Ubuntu (server), A.2 = Windows XP (workstation), pfSense = router, B.1 = Kali, B.2 = Windows 95 (legacy).*

---

## Step-by-Step Setup

1. Installed VirtualBox and created VMs for Ubuntu, Kali, Windows XP, and Windows 95.  
   <img src="https://i.imgur.com/iG1MSqE.png" width="60%">

2. Configured pfSense with two interfaces: `LAN` for Network A and `OPT1` for Network B.  
   <img src="https://i.imgur.com/ccb1EGK.png" width="60%">

3. Assigned static IPs and verified NIC-to-subnet mappings.
4. Installed Apache and OpenSSH on A.1 and verified services.
5. Installed Wireshark and Nmap on A.1 and B.1.
6. Validated baseline connectivity using ping, curl, and ssh.
7. Documented environment quirks to ensure reproducibility.

---

## Task II & III: Network Checks & Diagnosis

Discovery scans and packet captures were performed to establish a baseline.

- Nmap scans from **B.1 (Kali)**  
- Wireshark captures during ping, curl, and ssh  
- Baseline evidence used for later comparison  

### Selected Wireshark Captures (Baseline)

**Ping and curl from B.1 → A.1**  
<img src="https://i.imgur.com/Cfs9b6y.png" width="45%">

**SSH from B.1 → A.1**  
<img src="https://i.imgur.com/Bu5DlBx.png" width="45%">

**pfSense packet capture from B.1 → A.1**  
<img src="https://i.imgur.com/P3JaWdy.png" width="45%">

**Ping from B.1 → A.2**  
<img src="https://i.imgur.com/amkWHpx.png" width="45%">

**Failed curl/ssh from B.1 → A.2**  
<img src="https://i.imgur.com/borAgQu.png" width="45%">

**Ping & curl from B.1 → B.2**  
<img src="https://i.imgur.com/9Ku8xDv.png" width="45%">

**Ping & curl from A.2 → A.1**  
<img src="https://i.imgur.com/aPPdxJM.png" width="45%">

---

### Nmap Baseline Examples

<img src="https://i.imgur.com/pMhYda2.png" width="60%">
<img src="https://i.imgur.com/NrFsGvb.png" width="60%">

---

## Task IV & V: Security Policy Implementation

Policy enforcement was implemented primarily in pfSense, with host-level controls applied where router enforcement was insufficient.

**Corporate policy summary:**
- Server provides HTTP and SSH internally, HTTP only externally
- Workstations may access services but must not host them
- Server must not initiate external connections
- External hosts cannot ping internal hosts

### Access Control Matrix

<img src="https://i.imgur.com/p4HTJUe.png" width="60%">

### pfSense Rules

**WAN Rules**  
<img src="https://i.imgur.com/DOAkzbD.png" width="60%">

**LAN Rules**  
<img src="https://i.imgur.com/TYuD7lH.png" width="60%">

**OPT1 Rules**  
<img src="https://i.imgur.com/7YP6lje.png" width="60%">

---

## Post-Implementation Verification

- Nmap confirmed only authorized services were exposed
- Wireshark verified blocked traffic
- Logs validated rule enforcement

<img src="https://i.imgur.com/bZeB44P.png" width="60%">
<img src="https://i.imgur.com/nYRv0ry.png" width="60%">
<img src="https://i.imgur.com/DthG8qW.png" width="60%">

---

## Task VI: Server Local Firewall Implementation (A.1)

Host-level iptables rules enforced policies not visible to the router.

```bash
sudo iptables -A OUTPUT -d 192.168.20.0/24 -j DROP
sudo iptables -A INPUT -p tcp --dport 22 -s 192.168.10.0/24 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p icmp -s 192.168.10.0/24 -j ACCEPT
```
<img src="https://i.imgur.com/f1mLB6a.png" width="60%"> <img src="https://i.imgur.com/W7tmMXL.png" width="60%">
