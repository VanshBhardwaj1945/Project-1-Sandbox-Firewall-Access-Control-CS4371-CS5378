<h1>Project-1: Sandbox, Firewall & Access Control</h1>

<h2>CS4371/CS5378 – Fall 2025</h2>

<h2>Description & Background</h2>
This project demonstrates how to build a secure virtual network sandbox to safely experiment with network security policies.
The goal was to understand and implement real-world security practices, including firewall rules, network segmentation, and traffic monitoring, in a controlled environment.
<br />
The sandbox consists of:
<ul>
<li><b>Network A (Internal/Company)</b>: Ubuntu Desktop (server) and Windows XP (workstation)</li>
<li><b>Network B (External)</b>: Kali Linux (attacker/penetration testing) and Windows 95 (legacy system)</li>
<li><b>Router:</b> pfSense virtual appliance connecting networks A and B</li>
</ul>

<p class="note">
We encountered several challenges during this project, including configuring Windows 95 in a virtual environment, understanding IP assignments, and determining which security policies can only be enforced at the host level.
</p>

<h2>Tools & Utilities Used</h2>
<ul>
<li><b>Virtualization:</b> Oracle VirtualBoxn</li>
<li><b>Router:</b> pfSense</li>
<li><b>Operating Systems:</b> Ubuntu Desktop, Kali Linux, Windows XP, Windows 95</li>
<li><b>Network Analysis:</b> Wireshark, NMap</li>
<li><b>Services:</b> Apache HTTP server, OpenSSH</li>
<li><b>Host firewall:</b> iptables (Linux)</li>
</ul>

<h2>Network Topology</h2>
<p class="muted">Logical diagram showing networks, router, and hosts:</p>
<div class="placeholder">
<!-- INSERT IMAGE: Network topology diagram (show R, Network A & B, IP ranges, services) -->
Network topology diagram placeholder
</div>

<h2>Step-by-Step Setup</h2>
<ol>
<li>Installed VirtualBox/VMware and created VMs for Ubuntu, Kali, Windows XP, and Windows 95.</li>
<li>Configured pfSense router with two interfaces: LAN for Network A, OPT1 for Network B.</li>
<li>Assigned static IPs and ensured all NICs matched the network topology.</li>
<li>Installed services on Ubuntu (Apache, SSH) and ensured they were running.</li>
<li>Installed Wireshark and NMap on A.1 and B.1 for traffic capture and network scanning.</li>
<li>Verified network connectivity: ping, curl, ssh between hosts; verified internet access.</li>
<li>Documented any issues, such as Windows 95 VM requiring FIX95CPU_V3_FINAL patch.</li>
</ol>

<h2>Task II & III: Network Checks & Diagnosis</h2>
<p>Before implementing security policies, we performed network diagnosis:</p>
<ul>
<li>Ran NMap scans from B.1 to discover services on A.1, A.2, B.2.</li>
<li>Captured traffic with Wireshark during ping, curl, and ssh tests.</li>
<li>Recorded allowed services in a table:</li>
</ul>
<div class="placeholder">
<!-- INSERT IMAGE: Wireshark screenshots -->
Wireshark screenshot placeholder
</div>
<div class="placeholder">
<!-- INSERT IMAGE: NMap scans before security policy -->
NMap scan screenshot placeholder
</div>

<h2>Task IV & V: Security Policy Implementation</h2>
<p>The corporate security policy for the internal network (Network A) is as follows:</p>
<ul>
<li>Server (A.1) provides HTTP and SSH to internal workstations; HTTP to external hosts.</li>
<li>Workstations (A.2) can access server and external HTTP; cannot provide services.</li>
<li>Server cannot initiate connections to external hosts.</li>
<li>Ping allowed between all internal hosts; external hosts cannot ping internal hosts.</li>
</ul>

<h3>Access Control Matrix</h3>
<div class="placeholder">
<!-- INSERT IMAGE: Table showing subjects (hosts) and objects (services) -->
Access Control Matrix screenshot placeholder
</div>

<h3>Router Rules in pfSense</h3>
<div class="placeholder">
<!-- INSERT IMAGE: Router firewall rules screenshot -->
Router rules screenshot placeholder
</div>

<h3>NMap & Wireshark Post-Implementation</h3>
<ul>
<li>Verified exposed services using NMap scans from B.1.</li>
<li>Captured traffic with Wireshark to confirm policy enforcement.</li>
<li>Documented differences from pre-policy scans.</li>
</ul>
<div class="placeholder">
<!-- INSERT IMAGE: NMap post-policy -->
NMap post-policy screenshot placeholder
</div>
<div class="placeholder">
<!-- INSERT IMAGE: Wireshark post-policy -->
Wireshark post-policy screenshot placeholder
</div>

<h2>Task VI: Server Local Firewall Implementation</h2>
<p>Certain policy items required local firewall rules on A.1 to fully enforce restrictions:</p>
<ul>
<li>Configured iptables to block outgoing connections that violate policy.</li>
<li>Verified allowed services with NMap and Wireshark.</li>
<li>Confirmed that classified data could not be transferred externally via unauthorized services.</li>
</ul>
<div class="placeholder">
<!-- INSERT IMAGE: Local firewall rules on A.1 -->
Local firewall rules placeholder
</div>
<div class="placeholder">
<!-- INSERT IMAGE: Wireshark traffic validation -->
Wireshark traffic post-local firewall placeholder
</div>

<h2>Key Observations & Honest Mistakes</h2>
<ul>
<li>Windows 95 VM required patch to function correctly in VirtualBox.</li>
<li>Initially misconfigured IPs caused ping failures across networks.</li>
<li>Some policy items could not be fully enforced by router rules alone and required host-level firewalls.</li>
<li>Learned the importance of verifying traffic on multiple layers (router + host) to ensure security policies are fully applied.</li>
</ul>

<h2>Conclusion</h2>
<p>
This project successfully demonstrated the implementation and verification of network security policies in a controlled sandbox environment. 
It strengthened our understanding of firewall rules, router policies, host-level security, and traffic analysis with Wireshark and NMap.
Challenges included configuring legacy systems, understanding which policies require host-level enforcement, and ensuring consistent IP and service configuration.
</p>

<h2>Resume Blurb</h2>
<div class="resume-blurb">
Built a secure virtual network sandbox to experiment with cybersecurity policies using pfSense, VirtualBox/VMware, Ubuntu, Kali, Windows XP/95, NMap, Wireshark, Apache, and SSH. Implemented and tested firewall and router rules, monitored traffic, and verified policy compliance in a realistic network environment.
</div>

<!--
Replace all <div class="placeholder"> blocks with screenshots and diagrams.
-->
