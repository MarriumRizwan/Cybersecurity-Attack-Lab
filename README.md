# Cybersecurity Attack Lab

A simulated IoT-inspired cybersecurity testbed replicating 6 real-world
network attack categories in an isolated virtual machine environment.
Built as part of an academic assignment based on the IoT-CAD research paper.
All attacks were performed ethically in a controlled lab with no real
networks or systems harmed.

---

## Lab Environment

| VM | OS | IP Address | Role |
|---|---|---|---|
| Ubuntu Server | Ubuntu 24.04 LTS | 192.168.56.103 | Victim / IoT Server |
| Kali Linux | Kali Rolling | 192.168.56.102 | Attacker Machine |
| Host Machine | Windows | 192.168.56.1 | Gateway / Observer |

**Network Type:** VirtualBox Host-Only Network (fully isolated)  
**No internet bridge. All traffic contained within lab**

---

## Attack Scenarios Covered

| # | Attack Type | Tools Used | Layer | Threat Level |
|---|---|---|---|---|
| 1 | Probing | Nmap, Wireshark | Network (L3/L4) | Low |
| 2 | DoS / SYN Flood | hping3, Wireshark | Transport (L4) | High |
| 3 | SQL Injection | SQLMap, DVWA | Application (L7) | Critical |
| 4 | Brute Force | Hydra, rockyou.txt | Application (L7) | High |
| 5 | MITM / ARP Spoof | arpspoof, Wireshark | Data Link (L2) | Critical |
| 6 | Malware / Backdoor | msfvenom, Metasploit | Transport/App (L4/L7) | Critical |

---

## Repository Structure

```
Cybersecurity-Attack-Lab/
│
├── screenshots/
│   ├── 01-probing/          # Nmap scans, Wireshark ARP captures
│   ├── 02-dos/              # hping3 flood, Wireshark SYN packets
│   ├── 03-sql-injection/    # SQLMap output, DVWA pages, HTTP traffic
│   ├── 04-brute-force/      # Hydra output, Wireshark HTTP requests
│   ├── 05-mitm/             # arpspoof output, ARP Wireshark capture
│   └── 06-malware/          # msfvenom, Meterpreter session, TCP capture
│
├── analysis/
│   ├── 01-probing-analysis.md
│   ├── 02-dos-analysis.md
│   ├── 03-injection-analysis.md
│   ├── 04-brute-force-analysis.md
│   ├── 05-mitm-analysis.md
│   ├── 06-malware-analysis.md
│   └── traffic-labels.md
│
├── configs/
│   ├── network-topology.md
│   
│
└── README.md
```

---

## Attack Summaries

### 1. Probing
Performed network reconnaissance using Nmap from Kali against the Ubuntu
victim. Used three scan types: host discovery (-sn), service detection
(-sV), and OS fingerprinting (-O). Wireshark captured ARP broadcasts and
TCP SYN packets across the subnet. 5 hosts were discovered. This mirrors
the information gathering phase described in the IoT-CAD paper.

**Key finding:** All 1000 ports on victim were in reset state. OS could
not be specifically fingerprinted. ARP traffic clearly visible in Wireshark.

---

### 2. DoS / SYN Flood
Simulated a Denial of Service attack using hping3 in flood mode targeting
port 80 on the Ubuntu victim. The attack exploited the TCP three-way
handshake by sending thousands of SYN packets without completing the
connection, leaving the victim with thousands of half-open TCP connections.

**Key finding:** 592,348 packets transmitted with 100% packet loss on
victim. Wireshark captured 419,486 packets showing one-directional SYN
flood with no corresponding ACK responses.

---

### 3. SQL Injection
Deployed DVWA (Damn Vulnerable Web Application) on the Ubuntu victim with
security level set to LOW. Used SQLMap from Kali to automatically detect
and exploit a SQL injection vulnerability in the user ID parameter. The
backend MySQL database was fully enumerated including tables and credentials.

**Key finding:** SQLMap confirmed time-based blind and UNION-based injection.
Backend identified as MySQL 5.0.12 on Apache 2.4.58. Full database contents
extracted successfully.

---

### 4. Brute Force
Attacked the DVWA Brute Force login module using Hydra from Kali with the
rockyou.txt wordlist. Hydra automated HTTP GET requests to the login page,
cycling through thousands of passwords until the correct one was found.
Wireshark clearly showed the rapid repeated requests to the same URL.

**Key finding:** Admin password successfully cracked. Wireshark captured
650 packets showing identical GET requests to /DVWA/vulnerabilities/brute/
with only the password parameter changing each time.

---

### 5. MITM / ARP Spoofing
Performed a Man-in-the-Middle attack using arpspoof from Kali. Enabled IP
forwarding on Kali then sent fake ARP replies to both the victim and the
gateway, poisoning their ARP caches. This caused all victim traffic to
route through Kali before reaching its destination, giving full visibility
of all unencrypted traffic.

**Key finding:** ARP cache of victim successfully poisoned. Wireshark on
Kali showed traffic not destined for Kali being captured. Continuous stream
of ARP replies visible claiming Kali MAC as the gateway address.

---

### 6. Malware / Reverse Shell Backdoor
Created a malicious ELF reverse shell payload using msfvenom, hosted it
on a Python HTTP server on Kali, and had the victim download and execute
it using wget. Once executed, the victim called back to the Metasploit
listener on Kali, opening a full Meterpreter remote session with complete
system access.

**Key finding:** Full Meterpreter session opened. Confirmed victim as
Ubuntu 24.04 x64. Retrieved username via getuid. Wireshark showed
outbound TCP connection from victim to Kali on port 4444 with 1,062,760
bytes of stage transferred.

---

## Data Collection Methodology

For each attack the following logs were collected from the Ubuntu victim:

| Log File | Contents | Purpose |
|---|---|---|
| cpu_[attack].log | top command output | CPU and memory usage during attack |
| process_[attack].log | ps aux output | Running processes during attack |
| connections_[attack].log | ss command output | Active network connections |
| [attack]_capture.pcapng | Wireshark capture | Full packet capture for analysis |

Traffic was labelled into categories matching IoT-CAD paper methodology:

| Label | Attack |
|---|---|
| NORMAL | Baseline traffic |
| PROBING | Nmap scans |
| DoS | SYN flood |
| INJECTION | SQLMap HTTP requests |
| BRUTE FORCE | Hydra login attempts |
| MITM | ARP spoofed traffic |
| MALWARE | Reverse shell C2 traffic |

---

## Tools Reference

| Tool | Purpose | Machine |
|---|---|---|
| Nmap | Network scanning and OS fingerprinting | Kali |
| hping3 | SYN flood packet generation | Kali |
| SQLMap | Automated SQL injection | Kali |
| DVWA | Intentionally vulnerable web application | Ubuntu |
| Hydra | HTTP brute force password cracker | Kali |
| arpspoof | ARP cache poisoning for MITM | Kali |
| msfvenom | Malicious payload generator | Kali |
| Metasploit | Exploit framework and listener | Kali |
| Wireshark | Packet capture and analysis | Ubuntu / Kali |

---

## Safety and Ethics

- All attacks performed exclusively on isolated VirtualBox host-only network
- No real networks, public IPs, or external systems were targeted
- DVWA used as the intentional target for web attacks
- Dummy credentials used throughout all experiments
- Lab environment reset after completion of all tasks
- This project is purely academic and was conducted under course guidelines

---

## Academic Context

This lab replicates the testbed methodology described in:

**IoT-CAD: A Comprehensive Digital Framework for IoT Cyberattack Detection**

The three-layer IoT architecture (perception, network, application) is
mirrored in this lab through the Ubuntu IoT server, the host-only LAN,
and the DVWA web application layer respectively.

---


