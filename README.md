# Traffic Analysis Essentials

## Overview

This room explains how network security works from both a theoretical and practical perspective, focusing on how traffic analysis is used to detect and mitigate threats.

Network security is mainly based on two core principles:

- Authentication: Verifying identity  
- Authorization: Defining permissions  

These are implemented through layered security controls and supported by different technologies that monitor, analyze, and respond to network activity.

---

## Security Control Levels

### Physical Control
Protects hardware and infrastructure such as servers, switches, and cabling.  
Prevents unauthorized physical access or tampering.

### Technical Control
Focuses on logical protection mechanisms such as firewalls, VPNs, IDS/IPS, and encryption.  
This is where most real-time defense takes place.

### Administrative Control
Defines policies, procedures, and access rules.  
Example from the lab: Creating security policies belongs to this level.

---

## Security Approaches

### Access Control

Controls who can access the network and resources.

Includes:

- Firewall: Filters traffic based on rules  
- NAC (Network Access Control): Validates devices before access  
- IAM (Identity and Access Management): Manages identities and permissions  
- VPN: Provides secure remote access  
- Load Balancing: Distributes traffic based on metrics  

Load balancing uses metrics to manage data flow efficiently.

---

### Threat Control

Focuses on detecting and responding to malicious activity.

Includes:

- IDS/IPS: Detects and prevents threats  
- DLP: Prevents data leakage  
- SIEM: Correlates logs and events  
- SOAR: Automates response workflows  

SOAR correlates multiple data sources and automates incident response.

---

## Traffic Analysis

Traffic analysis is the process of monitoring and analyzing network communications to detect anomalies and threats.

### Flow Analysis
- Uses metadata such as IP addresses, ports, and traffic volume  
- Fast and scalable  
- Useful for detecting abnormal patterns like spikes or unusual connections  

### Packet Analysis (DPI)
- Deep inspection of packets  
- Analyzes payload content  
- Enables detection of exploits, malware signatures, and protocol misuse  

---

# Practical Analysis

## Level 1 – Malicious IP Identification and Filtering

![Level 1](images/level1.png)

### Context

Multiple hosts are communicating with a central server:

10.10.99.X → 10.10.99.199

An IDS/IPS system is monitoring traffic and generating alerts based on behavior.

### IDS/IPS Alerts

| IP Address   | Alert                          |
|-------------|--------------------------------|
| 10.10.99.16 | Corporate Policy Violation     |
| 10.10.99.29 | P2P Usage                      |
| 10.10.99.31 | Social Media Usage             |
| 10.10.99.99 | Multiple Login Attempts        |
| 10.10.99.99 | Metasploit Traffic             |
| 10.10.99.74 | Suspicious ARP Behaviour       |
| 10.10.99.62 | Bad Traffic                    |
| 10.10.99.56 | Protocol Other                 |

### Technical Analysis

The IDS/IPS provides different categories of alerts, which must be interpreted correctly.

High-confidence malicious indicators:

- Metasploit Traffic  
  Indicates the use of an exploitation framework, commonly associated with reverse shells and remote compromise.

- Bad Traffic  
  Suggests abnormal or known malicious patterns detected by signatures or heuristics.

- Suspicious ARP Behaviour  
  Could indicate ARP spoofing, often used in Man-in-the-Middle (MITM) attacks.

- Multiple Login Attempts  
  Suggests brute-force attempts or credential guessing.

Lower-priority alerts:

- Social Media Usage  
- P2P Usage  

These are policy violations but not necessarily active threats.

### Correlation and Decision Making

Instead of blocking all flagged IPs, prioritization is required:

- Focus on behavior linked to exploitation or lateral movement  
- Ignore non-critical policy violations  
- Correlate multiple alerts per IP (e.g., 10.10.99.99 appears multiple times)

### Action

Malicious IPs were blocked using firewall rules.

This represents:

- Implementation of Access Control Lists (ACLs)  
- Reduction of attack surface  
- Isolation of compromised hosts  

### Result

THM{PACKET_MASTER}

---

## Level 2 – Malicious Port Identification and Filtering

![Level 2](images/level2.png)

### Context

The analysis shifts from identifying malicious hosts to identifying malicious communication channels.

Traffic is directed toward the target server:

10.10.99.199

### Traffic Analyzer Data

| Destination Port |
|------------------|
| 4444             |
| 7777             |
| 2222             |
| 8080             |
| 21               |
| 445              |

### Technical Analysis

Instead of focusing on source IPs, the focus is on destination ports, which reveal how the attack is being executed.

#### Suspicious Ports

- 4444  
  Commonly used by Metasploit payloads for reverse shells.  
  Indicates command-and-control (C2) communication.

- 7777  
  Often used by custom backdoors or unauthorized services.  
  Not a standard port, making it suspicious in most environments.

- 2222  
  Alternative SSH port used to bypass security controls and evade detection.  
  Often associated with persistence mechanisms.

#### Normal / Expected Ports

- 8080 → Web services (HTTP alternative)  
- 21 → FTP  
- 445 → SMB (file sharing in internal networks)  

These ports are legitimate and widely used, so they are not immediately considered malicious without additional context.

### Correlation with IDS/IPS

The IDS/IPS alerts include:

- Metasploit Traffic  
- Bad Traffic  
- Suspicious Behaviour  

This supports the hypothesis that non-standard ports (4444, 7777, 2222) are being used for malicious purposes.

### Behavioral Indicators

- Use of uncommon ports  
- Repeated connections to the same target  
- Correlation with exploitation alerts  

These patterns strongly indicate active compromise attempts.

### Action

The following ports were blocked at the firewall level:

4444  
7777  
2222  

This results in:

- Disruption of reverse shells and C2 channels  
- Prevention of further exploitation  
- Containment of the attack vector  

---

## Conclusion

This lab demonstrates that traffic analysis is a critical skill in network security.

- IDS/IPS tools generate alerts, but require human interpretation  
- Port usage is a strong indicator of attacker behavior  
- Correlating multiple data sources improves detection accuracy  
- Blocking at the correct layer (IP vs Port) is key for effective mitigation  

Even when traffic is encrypted, behavioral analysis (patterns, ports, frequency) remains a powerful detection method.
