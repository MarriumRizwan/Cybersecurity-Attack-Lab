# Man-in-the-Middle (MITM) Attack Analysis

## Attack Overview
In a man in the middle attack two devices believe they’re communicating securely but there is an attacker between them who has full acess to the traffic being shared the attacker can easily modify or read the data. This is done through ARP spoofing where we sent (Kali) fake ARP replies claiming to the default gateway or the victim itself so that the traffic passes through kali before it reaches the correct device. Now kali has access to passwords cookies and all sensitive data. We used the command arpspoof from kali to carry this out. The wireshark output also shows kali as the default gateway. 
## Tools Used
- arpspoof (ARP poisoning tool from Kali Linux)
- Wireshark (traffic interception and capture)
- ip_forward (kernel setting to enable packet forwarding)

## VM Setup
- Attacker: Kali Linux (192.168.56.102)
- Victim: Ubuntu (192.168.56.103)
- Gateway: 192.168.56.1

## Commands Run
echo 1 > /proc/sys/net/ipv4/ip_forward
arpspoof -i eth0 -t 192.168.56.103 192.168.56.1
arpspoof -i eth0 -t 192.168.56.1 192.168.56.103

## Observations
- Kali successfully poisoned ARP caches of both victim and gateway
- Wireshark showed continuous stream of ARP reply packets from Kali
- All ARP replies falsely claiming Kali MAC is the legitimate gateway
- All victim traffic routed through Kali before reaching real gateway
- Kali had full visibility of all unencrypted victim traffic
- ARP cache of victim showed incorrect MAC address for gateway
- Attack was completely invisible to the victim machine
- Wireshark on Kali captured traffic not destined for Kali itself

## Traffic Pattern
- Continuous flood of ARP reply packets from attacker MAC address
- Same attacker MAC address claiming to be multiple IP addresses
- Duplicate ARP replies for the same IP visible in capture
- All victim outbound traffic appearing on Kali interface
- ARP reply rate far higher than normal network behavior

## Logs Collected
- cpu_mitm.log: load average 0.10, 0.14, 0.17 (minimal CPU impact)
- connections_mitm.log: no direct TCP connections to victim
- process_mitm.log: only standard system kernel processes

## Classification
Label: MITM
Layer: Data Link Layer 2
Protocol: ARP
Key Indicator: Duplicate ARP replies, attacker MAC mapped to gateway IP
Threat Level: Critical (full traffic interception and credential theft)