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

Evidence: Port Listening Verification (ss -tuln & netstat -tuln)
![Port Listening Check](image_13.png)

Evidence: Detailed Process & Port Mapping (sudo ss -tulnp)
![Process & Listening Port Mapping](image_14.png)

---

### Phase 2: VPN Tunnel Verification
- *Command:* ip a & ps aux | grep -i vpn
- *Evidence:*
  - Network interface tun0 active with assigned IP (10.201.138.95).
  - Process openvpn running in background (PID verified via ps aux).
- *Finding:* A secure OpenVPN tunnel is active and routing network traffic.

Evidence: Live Traffic Analysis on OpenVPN Interface
![Wireshark Capture on VPN Interface](image_16.png)

---

### Phase 3: Proxy State Inspection
- *Command:* env | grep -i proxy & ss -tulnp | grep -E "8080|3128|8000"
- *Evidence:*
  - Environment variables returned no proxy configurations.
  - Port checks on common proxy ports (8080, 3128, 8000) returned blank.
- *Finding:* No local proxy service or interception mechanism is currently active on default ports.

---

### Phase 4: Host Firewall Enforcement (UFW)
- *Command:* sudo ufw allow 22/tcp & sudo ufw status numbered
- *Evidence:*
  - UFW Status: Active.
  - Active Rule: 22/tcp ALLOW IN Anywhere (IPv4 & IPv6).
- *Finding:* Host firewall is active with controlled inbound access for SSH.

Evidence: UFW Rules and Status Breakdown
![UFW Status and Rules](image_8.png)

---

## 3. Traffic & Protocol Analysis (Wireshark Deep Packet Inspection)

Understanding network traffic and packet streams is vital for SOC Analysts to spot abnormal traffic or malicious payloads.

### 3.1 HTTP Traffic Capture & Stream Analysis
Analysis of unencrypted HTTP communication confirmed successful HTTP GET requests and 200 OK server responses.

Evidence: Wireshark Packet Capture for HTTP Stream
![Wireshark HTTP Stream Analysis](image_10.png)

Evidence: Detailed HTTP Header Breakdown
![HTTP Request Header Analysis](image_11.png)

Evidence: TCP Packet Sequence for Web Traffic
![HTTP Packet Details](image_15.png)

### 3.2 DNS Query & Response Verification
Verification of DNS request/response pairs used for domain resolution.

Evidence: DNS Query/Response Transaction Details
![Wireshark DNS Query Analysis](image_12.png)

### 3.3 Follow TCP Stream (Payload Reconstruction)
Reconstructing cleartext TCP payload to inspect raw data exchanged between source and destination.

Evidence: HTTP Payload Reconstruction via Follow TCP Stream
![Follow TCP Stream Payload View](image_9.png)

---

## 4. Standard Incident Report Table

| Incident / Audit ID | Source Host / IP | Dest Host / IP | Protocol / Port | Findings | Verdict | Action Taken |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| *AUD-NET-001* | 10.0.3.15 | 10.0.3.2 | ICMP / IP Route | Default route active via eth1 | Normal | Baseline Documented |
| *AUD-VPN-002* | 10.201.138.95 | Remote VPN Gateway | UDP / OpenVPN | Active tun0 interface detected | Verified | Session Monitored |
| *AUD-FW-003* | Localhost | Any | TCP / 22 | Custom inbound rule added for SSH | Secured | Port 22 Allowed |
| *AUD-PKT-004* | 10.0.3.15 | 199.232.169.91 | HTTP / 80 | Valid cleartext GET request (/success.txt) | Clean | Payload Verified |

---

## 5. SOC Analyst Conclusion
All baseline diagnostics confirm a secure, well-configured network state with active firewall protection, verified VPN tunneling, and fully analyzed packet streams. The repository serves as an evidence-backed baseline report for future SOC investigations.
