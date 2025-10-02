<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
</head>
<body>
  <h1>Project-1: Sandbox, Firewall &amp; Access Control</h1>
  <h2>CS4371 / CS5378 – Fall 2025</h2>

  <h2>Description &amp; Background</h2>
  <p>
    This project builds a clean, repeatable virtual sandbox to design, enforce, and verify network security policies.
    Using VirtualBox and a pfSense virtual router, we created two segmented subnets (internal and external) and deployed four virtual machines to emulate a small company environment.
    The lab demonstrates how to combine router-level rules and host-level firewalls to achieve policy goals and how to validate enforcement using scanning and packet-capture tools.
  </p>

  <p><strong>The sandbox consists of:</strong></p>
  <ul>
    <li><strong>Network A (Internal / Company)</strong>: Ubuntu Desktop (A.1 — server) and Windows XP (A.2 — workstation)</li>
    <li><strong>Network B (External)</strong>: Kali Linux (B.1 — attacker / scanner) and Windows 95 (B.2 — legacy)</li>
    <li><strong>Router:</strong> pfSense virtual appliance connecting Network A and Network B</li>
  </ul>

  <p>
    <em>Notes:</em> The group encountered and resolved practical issues such as legacy VM patches (Windows 95), IP/NIC misconfigurations, and recognizing which policy items require host-level enforcement rather than router-only rules.
  </p>

  <h2>Tools &amp; Technologies Used</h2>
  <p>Key tools and skills demonstrated (employer-friendly):</p>
  <ul>
    <li><strong>Virtualization:</strong> Oracle VirtualBox — VM creation, NIC binding, snapshotting</li>
    <li><strong>Router &amp; firewall:</strong> pfSense — interface configuration, rule authoring, logging</li>
    <li><strong>Operating systems:</strong> Ubuntu Desktop (server), Windows XP (workstation), Kali Linux (attacker), Windows 95 (legacy)</li>
    <li><strong>Network analysis &amp; scanning:</strong> Wireshark (packet capture), Nmap (service discovery)</li>
    <li><strong>Services:</strong> Apache (HTTP), OpenSSH (SSH)</li>
    <li><strong>Host hardening:</strong> iptables on Ubuntu for host-level controls</li>
    <li><strong>Testing &amp; validation:</strong> curl, ping, ssh, and routine verification steps</li>
  </ul>

  <h2>Network Topology</h2>
  <p>Logical diagram showing networks, router, and hosts:</p>
  <figure>
    <img src="https://i.imgur.com/cb5KwK2.png" alt="Network topology diagram" width="60%" />
    <figcaption>A.1 = Ubuntu (server), A.2 = Windows XP (workstation), pfSense = router, B.1 = Kali, B.2 = Windows 95 (legacy).</figcaption>
  </figure>

  <h2>Step-by-Step Setup</h2>
  <ol>
    <li>
      Installed VirtualBox (or VMware) and created VMs for Ubuntu, Kali, Windows XP, and Windows 95.
      <figure><img src="https://i.imgur.com/iG1MSqE.png" alt="VirtualBox Manager screenshot" width="60%" /></figure>
    </li>
    <li>
      Configured pfSense with two interfaces: <code>LAN</code> for Network A and <code>OPT1</code> for Network B.
      <figure><img src="https://i.imgur.com/ccb1EGK.png" alt="pfSense interfaces screenshot" width="60%" /></figure>
    </li>
    <li>Assigned static IPs and verified NIC-to-subnet mappings to ensure accurate routing and repeatable tests.</li>
    <li>Installed Apache and OpenSSH on A.1 and confirmed services were active before testing.</li>
    <li>Installed Wireshark and Nmap on A.1 and B.1 for capture and discovery tasks.</li>
    <li>Validated baseline connectivity (ping, curl, ssh) and recorded initial captures for later comparison.</li>
    <li>Documented environment quirks (e.g., FIX95CPU_V3_FINAL for Windows 95) so the setup can be reproduced reliably.</li>
  </ol>

  <h2>Task II &amp; III: Network Checks &amp; Diagnosis</h2>
  <p>We ran discovery scans from <strong>B.1 (Kali)</strong> to enumerate hosts and services, and captured traffic during functional tests to create a baseline for comparison after policy enforcement.</p>
  <ul>
    <li>Nmap scans from B.1 to identify services on A.1, A.2, and B.2.</li>
    <li>Wireshark captures during ping, curl, and ssh operations to verify actual packet flows.</li>
    <li>Baseline records were used to validate the effect of firewall rules applied later.</li>
  </ul>

  <h3>Selected Wireshark captures (baseline)</h3>
<ul>
  <li style="overflow:auto; margin-bottom:12px;">
    Ping and curl from B.1 → A.1
    <img src="https://i.imgur.com/Cfs9b6y.png" alt="Ping and curl from B.1 to A.1"
         style="float:right; margin-left:12px; width:35%; max-width:300px; height:auto;" />
    <div style="clear:both;"></div>
  </li>

  <li style="overflow:auto; margin-bottom:12px;">
    SSH from B.1 → A.1
    <img src="https://i.imgur.com/Bu5DlBx.png" alt="SSH from B.1 to A.1"
         style="float:right; margin-left:12px; width:35%; max-width:300px; height:auto;" />
    <div style="clear:both;"></div>
  </li>

  <li style="overflow:auto; margin-bottom:12px;">
    pfSense packet capture from B.1 → A.1
    <img src="https://i.imgur.com/P3JaWdy.png" alt="pfSense packet capture"
         style="float:right; margin-left:12px; width:35%; max-width:300px; height:auto;" />
    <div style="clear:both;"></div>
  </li>

  <li style="overflow:auto; margin-bottom:12px;">
    Ping from B.1 → A.2
    <img src="https://i.imgur.com/amkWHpx.png" alt="Ping from B.1 to A.2"
         style="float:right; margin-left:12px; width:35%; max-width:300px; height:auto;" />
    <div style="clear:both;"></div>
  </li>

  <li style="overflow:auto; margin-bottom:12px;">
    Failed curl/ssh from B.1 → A.2
    <img src="https://i.imgur.com/borAgQu.png" alt="Failed curl and ssh"
         style="float:right; margin-left:12px; width:35%; max-width:300px; height:auto;" />
    <div style="clear:both;"></div>
  </li>

  <li style="overflow:auto; margin-bottom:12px;">
    Ping &amp; curl from B.1 → B.2
    <img src="https://i.imgur.com/9Ku8xDv.png" alt="Ping and curl from B.1 to B.2"
         style="float:right; margin-left:12px; width:35%; max-width:300px; height:auto;" />
    <div style="clear:both;"></div>
  </li>

  <li style="overflow:auto; margin-bottom:12px;">
    Ping &amp; curl from A.2 → A.1
    <img src="https://i.imgur.com/aPPdxJM.png" alt="Ping and curl from A.2 to A.1"
         style="float:right; margin-left:12px; width:35%; max-width:300px; height:auto;" />
    <div style="clear:both;"></div>
  </li>
</ul>


  <p>Nmap baseline examples:</p>
  <figure>
    <img src="https://i.imgur.com/pMhYda2.png" alt="Nmap screenshot 1" width="60%" />
    <img src="https://i.imgur.com/NrFsGvb.png" alt="Nmap screenshot 2" width="60%" />
  </figure>

  <h2>Task IV &amp; V: Security Policy Implementation</h2>
  <p>We implemented the corporate policy primarily in pfSense and validated enforcement from an external host. Items that could not be fully enforced by the router (same-subnet traffic) were enforced on the server with iptables.</p>

  <p>The corporate policy for Network A (summary):</p>
  <ul>
    <li>Server (A.1) provides HTTP and SSH to internal workstations and only HTTP to external hosts.</li>
    <li>Workstations (A.2) may access server services and external HTTP only; they must not host services.</li>
    <li>The server must not initiate connections to external hosts.</li>
    <li>Internal hosts may ping each other; external hosts cannot ping internal hosts.</li>
  </ul>

  <h3>Access Control Matrix</h3>
  <figure>
    <img src="https://i.imgur.com/p4HTJUe.png" alt="Access Control Matrix" width="60%" />
  </figure>

  <h3>pfSense router rules (screenshots)</h3>
      <li>WAN Rules</li>
  <figure>
    <img src="https://i.imgur.com/DOAkzbD.png" alt="pfSense rules screenshot 1" width="60%" />
  <figure>
  <li>Lan Rules</li>
  <figure>
    <img src="https://i.imgur.com/TYuD7lH.png" alt="pfSense rules screenshot 2" width="60%" />
  </figure>
 <li>opt1 Rules</li>
  <figure>
    <img src="https://i.imgur.com/7YP6lje.png" alt="pfSense rules screenshot 3" width="60%" />
  </figure>

  <h3>Post-implementation verification</h3>
  <ul>
    <li>Ran Nmap from B.1 to confirm externally exposed services were limited to HTTP on the server.</li>
    <li>Captured traffic with Wireshark to confirm that SSH, ICMP, and other services were blocked from external hosts.</li>
    <li>Compared post-policy captures to baseline to verify rule behavior.</li>
  </ul>

  <figure>
    <img src="https://i.imgur.com/bZeB44P.png" alt="Nmap post-policy" width="60%" />
    <img src="https://i.imgur.com/nYRv0ry.png" alt="Wireshark post-policy" width="60%" />
    <img src="https://i.imgur.com/DthG8qW.png" alt="pfSense logs showing blocked attempts" width="60%" />
  </figure>

  <h2>Task VI: Server Local Firewall Implementation (A.1)</h2>
  <p>To fully enforce policies that do not traverse the router, we applied host-level iptables rules on A.1. These rules blocked server-initiated external connections and limited SSH to internal hosts only.</p>

  <pre>
# example rules applied on A.1
sudo iptables -A OUTPUT -d 192.168.20.0/24 -j DROP
sudo iptables -A INPUT -p tcp --dport 22 -s 192.168.10.0/24 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p icmp -s 192.168.10.0/24 -j ACCEPT
  </pre>

  <figure>
    <img src="https://i.imgur.com/f1mLB6a.png" alt="iptables screenshot" width="60%" />
    <img src="https://i.imgur.com/W7tmMXL.png" alt="Wireshark post-local-firewall" width="60%" />
  </figure>

  <h2>What I learned</h2>
  <p>Concrete skills and takeaways that are relevant to employers:</p>
  <ul>
    <li><strong>Network segmentation:</strong> Designing subnets and ensuring traffic flows only where intended.</li>
    <li><strong>Firewall engineering:</strong> Writing pfSense rules and host iptables to enforce policy and maintain an audit trail.</li>
    <li><strong>Verification:</strong> Using Nmap and Wireshark together to validate policy and detect unintended exposure.</li>
    <li><strong>Operational discipline:</strong> Documenting IPs, interfaces, and VM patches to make the lab reproducible.</li>
    <li><strong>Defense-in-depth:</strong> Using both router-level and host-level controls to meet security goals.</li>
  </ul>

  <h2>Conclusion</h2>
  <p>
    The project resulted in a documented, repeatable sandbox environment that enforces a realistic company security policy through a mix of pfSense router rules and host-level iptables. 
    Post-implementation scans and captures confirmed that external access was limited to explicitly authorized services and that host-level rules closed gaps left by router-only enforcement. 
    The lab reinforced practical skills that map directly to entry-level network security and SOC roles: firewall configuration, traffic analysis, and disciplined lab operations.
  </p>

  

</body>
</html>
