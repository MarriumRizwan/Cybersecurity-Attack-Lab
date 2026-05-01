# Probing Attack Analysis

## Attack Overview
In this task we carried out probing. Probing is when an attacker before their attack gathers relevant information about the victims such as the network, the operating system running on their device and the services they offer. To carry out this we used NMAP on kali which was our attack VM to scan the network we used three scans “–sn” for network discovery which reveals what devices are on, “-sV” for service scanning which tells what services are running such as if they WIFI or not the third scan is “-O” which specifies the OS running. We also used wireshark to view ARP broadcasts to get the mac addresses so kali can discover active devices.

## Tools Used
- nmap -sn 192.168.56.0/24 (host discovery)
- nmap -sV 192.168.56.103 (service version scan)
- nmap -sV -T4 -O 192.168.56.103 (OS fingerprinting)
- Wireshark (traffic capture)

## VM Setup
- Attacker: Kali Linux (192.168.56.102)
- Victim: Ubuntu (192.168.56.103)

## Commands Run
nmap -sn 192.168.56.0/24
nmap -sV 192.168.56.103
nmap -sV -T4 -O 192.168.56.103

## Observations
- 5 hosts discovered on the subnet
- All 1000 scanned ports on victim were in ignored/reset state
- OS fingerprinting could not give specific OS details
- ARP broadcasts visible in Wireshark from attacker to victim
- 513 packets captured during probing session

## Traffic Pattern
- High volume of TCP SYN packets sent to multiple ports
- ARP who-has requests flooding from attacker IP
- No established connections, purely scanning behavior

## Logs Collected
- cpu_probing.log: system load was low (0.07, 0.29, 0.27)
- connections_probing.log: no active peer connections
- process_probing.log: only system/kernel processes running

## Classification
Label: PROBING
Layer: Network Layer 3 and 4
Protocol: TCP, ARP
Key Indicator: SYN packets to multiple ports, ARP broadcasts
Threat Level: Low (reconnaissance only)