# SOC-Analyst-Learning-journey
# SOC Lab Report: Networking Foundations & Diagnostic Investigation

*Analyst:* L1 SOC Analyst  
*Environment:* Kali Linux VM (Host & VPN Interfaces)  
*Status:* Completed & Verified  

---

## 1. Executive Summary
This document summarizes the practical verification and investigation conducted for the *Networking Foundations* module. The investigation focused on verifying active network interfaces, analyzing live traffic protocols, confirming VPN tunnel configuration, checking local proxy states, and enforcing host firewall rules.

---

## 2. Investigation Findings & Evidence

### Phase 1: Network Interface & Gateway Identification
- *Command:* ip a / ip route
- *Evidence:* 
  - Active Ethernet Interfaces: eth0 (192.168.56.101/24) and eth1 (10.0.3.15/24).
  - Default Gateway: 10.0.3.2 via eth1.
- *Finding:* System possesses multiple active network interfaces communicating with internal and external subnets.

### Phase 2: VPN Tunnel Verification
- *Command:* ip a & ps aux | grep -i vpn
- *Evidence:*
  - Network interface tun0 active with assigned IP (10.201.138.95).
  - Process openvpn running in background (PID verified via ps aux).
- *Finding:* A secure OpenVPN tunnel is active and routing network traffic.

### Phase 3: Proxy State Inspection
- *Command:* env | grep -i proxy & ss -tulnp | grep -E "8080|3128|8000"
- *Evidence:*
  - Environment variables returned no proxy configurations.
  - Port checks on common proxy ports (8080, 3128, 8000) returned blank.
- *Finding:* No local proxy service or interception mechanism is currently active on default ports.

### Phase 4: Host Firewall Enforcement (UFW)
- *Command:* sudo ufw allow 22/tcp & sudo ufw status numbered
- *Evidence:*
  - UFW Status: Active.
  - Active Rule: 22/tcp ALLOW IN Anywhere (IPv4 & IPv6).
- *Finding:* Host firewall is active with controlled inbound access for SSH.

---

## 3. Standard Incident Report Table

| Incident / Audit ID | Source Host / IP | Dest Host / IP | Protocol / Port | Findings | Verdict | Action Taken |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| *AUD-NET-001* | 10.0.3.15 | 10.0.3.2 | ICMP / IP Route | Default route active via eth1 | Normal | Baseline Documented |
| *AUD-VPN-002* | 10.201.138.95 | Remote VPN Gateway | UDP / OpenVPN | Active tun0 interface detected | Verified | Session Monitored |
| *AUD-FW-003* | Localhost | Any | TCP / 22 | Custom inbound rule added for SSH | Secured | Port 22 Allowed |

---

## 4. SOC Analyst Conclusion
All baseline diagnostics confirm a secure, well-configured network state with active firewall protection and verified VPN tunneling. The network setup is fully ready for deeper system diagnostics and threat monitoring.
