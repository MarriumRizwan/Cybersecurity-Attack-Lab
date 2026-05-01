# DoS Attack Analysis

## Attack Overview
This was a simulation of a DOS (Denial of service attack). In this attack we make a machine unavailable by flooding it with traffic that’s not legitimate so that real users cannot access it. This works by exploiting the TCP three way handshake, the attacker sends thousands of SYN packets but never sends the final ACK needed to close the connection so the victim keeps indefinitely waiting for the ACK meanwhile not being able to serve real users. We carried out this attack by using hping3 from Kali with syn flood flags this flooded our victim with thousands of SYN packets this led to numerous half opened TCP connections that overwhelmed the victim. The wireshark logs show the numerous SYN packets from kali to the victim with no ACK sent. The flooding occurs on port 80 which is the HTTP traffic port.

## Tools Used
- hping3 (SYN flood tool from Kali)
- Wireshark (traffic capture on Ubuntu)

## VM Setup
- Attacker: Kali Linux (192.168.56.102)
- Victim: Ubuntu (192.168.56.103)

## Command Run
sudo hping3 --syn --flood -p 80 192.168.56.103

## Observations
- 592,348 packets transmitted, 0 received (100% packet loss)
- Victim port 80 (HTTP) was the target
- Wireshark showed thousands of SYN packets from attacker
- No ACK packets returned, classic SYN flood pattern
- CPU load on victim: 0.41, 0.35, 0.15 (moderate increase)
- nm-dispatcher process showed 25% CPU spike
- 419,486 total packets captured, 209,738 displayed at 50%

## Traffic Pattern
- Massive one-directional TCP SYN flood to port 80
- RST/ACK responses from victim showing connection rejection
- No completed TCP three-way handshakes visible
- Packet rate overwhelmed all normal traffic completely

## Logs Collected
- cpu_dos.log: load average 0.41, 0.35, 0.15
- connections_dos.log: numerous half-open connections visible
- process_dos.log: nm-dispatcher process at 25% CPU usage

## Classification
Label: DoS
Layer: Transport Layer 4
Protocol: TCP
Key Indicator: Thousands of SYN packets with no ACK, port 80 flood
Threat Level: High (service disruption)