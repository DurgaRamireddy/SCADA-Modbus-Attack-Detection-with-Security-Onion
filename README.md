# SCADA-Modbus-Attack-Detection-with-Security-Onion

## Overview
This project demonstrates how to monitor and detect Modbus-based attacks against a SCADA system using Security Onion.

The lab environment consisted of three virtual machines:
- Security Onion – network monitoring and intrusion detection platform
- Modsak1 – SCADA slave device
- Modsak2 – SCADA master controller
The objective was to:
- establish network communication between SCADA devices
- monitor Modbus traffic using Security Onion
- perform a Modbus packet injection attack
- detect the malicious activity using Suricata, Zeek, and Wireshark

## Lab Environment
| System         | Role                     |
| -------------- | ------------------------ |
| Security Onion | Network monitoring & IDS |
| Modsak1        | Modbus Slave             |
| Modsak2        | Modbus Master            |

**Network Configuration**
All systems were configured using:
- **Host-Only Adapter** -> internal SCADA network communication
- **Bridged Adapter (Security Onion only)** -> system updates and external access
After configuration, the virtual machines were restarted to apply network changes.

## Security Onion Configuration
Security Onion was configured to monitor SCADA traffic.
**Steps performed**
1. Accessed the Security Onion web interface
2. Created a network sensor
3. Enabled:
    - Suricata for intrusion detection
    - Zeek for deep packet inspection
4. Created a custom Suricata rule to detect suspicious Modbus commands
This configuration allowed the monitoring system to analyze industrial control system traffic.

## SCADA Communication Verification
To confirm normal operation of the SCADA environment, a Modbus command was sent from the master to the slave.

**Test Command**
**Report Slave ID**
Traffic was verified using Wireshark, which confirmed:
- successful communication between Modsak2 and Modsak1
- correct Modbus packet structure
- hex payload values needed for later attack simulation
This step ensured the environment was functioning normally before launching the attack.

## Modbus Packet Injection Attack
After confirming normal communication, a malicious Modbus packet was constructed to force the slave device into Listen-Only Mode.

**Attack Goal**
Disable responses from the slave device, preventing communication with the master controller.

**Attack Method**
The attack packet was transmitted using Netcat with a predefined hexadecimal payload.

Once the packet was sent:
- Modsak1 entered Listen-Only Mode
- the slave stopped responding to the master
- SCADA communication was disrupted
Security Onion successfully logged the malicious traffic.

## Automated SCADA Attack Script
To simulate a larger-scale attack, a Bash script was used to send the injection packet across an entire subnet.

for i in {1..254}; do
echo "Attacking 192.168.255.$i"
printf '\x00\x02\x00\x00\x00\x02\x01\x11\x08\x04' | nc 192.168.255.$i 502
done

This script attempted the attack against every possible device in the subnet.

Security Onion logs showed multiple Modbus injection attempts, demonstrating how such attacks could scale in real-world ICS environments.

## Detection in Security Onion
The attack activity was detected using multiple monitoring tools:

**Suricata**
- detected suspicious Modbus commands
- triggered intrusion alerts

**Zeek**
- analyzed industrial protocol traffic
- logged abnormal Modbus communication patterns

**Wireshark**
- verified packet structure
- confirmed attack payload transmission
These tools provided layered visibility into SCADA network activity.

## Challenges Encountered
Several issues occurred during the lab setup.

**Network Connectivity Issues**
Error: No Route to Host

Solution:
- verified all VMs were configured in Host-Only mode
- restarted network services

**SCADA Traffic Not Captured**
Security Onion initially failed to capture Modbus packets.

Solution:
- verified correct network interface monitoring
- restarted the Security Onion sensor

**IDS Alerts Not Triggering**
Some attack packets were not initially logged.

Solution:
- adjusted Suricata detection thresholds
- replayed attack traffic to verify detection

## Skills Demonstrated
- SCADA / ICS network monitoring
- Modbus protocol analysis
- Security Onion deployment
- Suricata rule creation
- Zeek network analysis
- Packet inspection with Wireshark
- Modbus packet injection
- Bash scripting for network attacks

## Key Takeaways
This lab demonstrated how industrial control systems can be disrupted using protocol-level attacks.

It also showed the importance of:
- monitoring ICS network traffic
- deploying intrusion detection systems
- analyzing protocol behavior for anomalies

Security Onion proved effective for detecting malicious Modbus activity and highlighting vulnerabilities in SCADA environments.

## Author
Durga Sai Sri Ramireddy </br>
Master's Student - Cybersecurity </br>
University of Houston

*This project was developed as part of academic coursework and expanded for cybersecurity portfolio demonstration.*
