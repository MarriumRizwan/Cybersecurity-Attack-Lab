# Brute Force Password Attack Analysis

## Attack Overview
In a brute force attack the attacker tries guessing a victim’s password by trial and error using many different combinations until one works this is done using a premade list of common passwords. We used Hydra from Kali to carry out this attack using the “rockyou.txt” wordlist with common passwords. Hydra sent multiple HTTP re	quests trying each password one by one. We did crack the password. Wireshark showed exactly this the multiple HTTP requests to the same URL.

## Tools Used
- Hydra v9.4 (password cracker from Kali Linux)
- rockyou.txt wordlist
- DVWA Brute Force module on Ubuntu victim machine
- Wireshark (traffic capture during attack)

## VM Setup
- Attacker: Kali Linux (192.168.56.102)
- Victim: Ubuntu running DVWA (192.168.56.103)

## Command Run
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.56.103
http-get-form "/DVWA/vulnerabilities/brute/:username=^USER^
&password=^PASS^&Login=Login:incorrect"

## Observations
- Hydra successfully cracked the admin password from rockyou.txt
- Multiple rapid HTTP GET requests sent to the same login URL
- Each request tried a different password from the wordlist
- Wireshark showed 650 total packets, 96 after HTTP filter
- Repeated 200 OK responses containing "incorrect" in body
- Final successful response confirmed the password was found
- CPU load remained very low at 0.10, 0.14, 0.17 throughout
- Attack completed quickly due to password being in wordlist

## Traffic Pattern
- Rapid sequential HTTP GET requests to /DVWA/vulnerabilities/brute/
- All requests originated from same source IP 192.168.56.102
- Password parameter in URL changing with every single request
- Pattern immediately obvious when filtered by HTTP in Wireshark
- No encryption, credentials visible in plain text in packets

## Logs Collected
- cpu_password.log: load average 0.10, 0.14, 0.17 (minimal impact)
- connections_password.log: no persistent connections logged
- process_password.log: only standard system processes active

## Classification
Label: BRUTE FORCE
Layer: Application Layer 7
Protocol: HTTP
Key Indicator: Rapid repeated HTTP GET to login URL with cycling passwords
Threat Level: High (credential compromise achieved)