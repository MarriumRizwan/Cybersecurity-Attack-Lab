# SQL Injection Attack Analysis

## Attack Overview
SQL injection is when we insert a malicious code into a web application to do something other than its designated use. Through SQL injection an attacker can extract and modify data. We used an intentionally weak application called DVWA (Damn vulnerable web application) to test this, instead of manually hacking the application we used SQLMAP from Kali this automatically exploits vulnerabilities.  SQLMAP helped us find the operating system and the exposed database.

## Tools Used
- DVWA (Damn Vulnerable Web Application) on Ubuntu
- SQLMap (automated SQL injection tool from Kali)
- Wireshark (traffic capture)
- Firefox (manual testing and session cookie retrieval)

## VM Setup
- Attacker: Kali Linux (192.168.56.102)
- Victim: Ubuntu running DVWA (192.168.56.103)

## Command Run
sqlmap -u "http://192.168.56.103/DVWA/vulnerabilities/sql/?id=1&Submit=Submit#"
--cookie="PHPSESSID=your_session_id; security=low"

## Observations
- SQLMap identified the backend DBMS as MySQL 5.0.12 (MariaDB fork)
- Time-based blind SQL injection technique confirmed
- UNION query injection also successfully confirmed
- Web server identified as Apache 2.4.58 on Linux Ubuntu
- Database name, tables, and columns successfully enumerated
- Admin credentials exposed through the injection attack
- Security level was set to LOW in DVWA settings
- SQLMap sent 6 total HTTP requests to identify injection point

## Traffic Pattern
- Repeated HTTP GET requests to /DVWA/vulnerabilities/sql/
- Requests contained SQL payloads in the id parameter
- Response sizes varied indicating successful data extraction
- Full TCP handshake visible unlike DoS attack traffic
- HTTP 200 OK responses throughout the attack session

## Logs Collected
- cpu_injection.log: load average 0.29, 0.34, 0.29 (very low)
- connections_injection.log: empty, local loopback style attack
- process_injection.log: only standard system processes visible

## Classification
Label: INJECTION
Layer: Application Layer 7
Protocol: HTTP, MySQL
Key Indicator: Repeated HTTP GET with SQL payloads in parameters
Threat Level: Critical (full database exfiltration possible)