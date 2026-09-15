## TryHackMe-writeups
TryHackMe rooms documentation, write-ups, and CTF walkthroughs.

## TryHackMe: The Crown Jewel

- **Difficulty:** Easy
- **OS:** Linux / Windows Infrastructure
- **Entry Vector:** Network Traffic Analysis / Log Analysis
- **Category:** Blue Team / DFIR (Incident Response)
- **Tools Used:** Splunk, Wireshark

## Incident Overview
An alert for an Outbound Reverse Shell Connection was triggered for Imperium Labs. The objective of this investigation is to analyze raw PCAPs (challenge.pcap) and pre-ingested Splunk logs (index=network_logs) to reconstruct the attack lifecycle, detect ARP spoofing activity, identify malicious Command and Control (C2) channels, and uncover data exfiltration techniques.

## Investigation & Walkthrough

## Phase 1: Initial Triage & C2 Identification
# 1. Suspicious Internal Host:
Filtered network logs to identify the internal machine originating the suspicious outbound connections.
Finding: 10.10.10.X (Internal Host)

# 2. C2 Outbound Channel:
Inspected active outbound sessions on non-standard/suspicious ports to detect the C2 communication channel.
Finding: 1.1.1.X:XXXX (C2 Socket)

## Phase 2: Network Traffic Analysis (Wireshark)
# 1. ARP Spoofing & Gateway Impersonation:
Analyzed packet captures in Wireshark for duplicate MAC addresses and gratuitous ARP replies targeting the gateway 10.10.10.1.
Filter: arp.duplicate-address-frame or arp.opcode == 2
Attacker MAC Address: 00:0c:29:11:XX:XX
Total ARP Spoofing Attacks Observed: 9X packets

# 2. Reconnaissance & Credential Access (Jira):
Filtered HTTP traffic to inspect requests directed at the internal Jira server.
Filter: http.request.method == "POST"
Anomalous User-Agent: CVE-202X-XXXXXXX
Captured Plaintext Payload: username=dev_user&password=[REDACTED]

## Phase 3: Exfiltration Analysis (Splunk)
# 1. Exfiltration Domain & Protocol Identification:
Queried Splunk logs to detect unusual external domain queries and high-volume data transport mechanisms.
Query: index=network_logs | stats count by query
Attacker Domain: exfil-XXXXXX.xyz
Exfiltration Protocol: DNS (DNS Tunneling)
Inspected active outbound sessions on non-standard/suspicious ports to detect the C2 communication channel.
Finding: 1.1.1.X:XXXX (C2 Socket)

## Indicators of Compromise (IOCs) & Summary

Affected Internal Host: 10.10.10.X
C2 Channel Endpoint: 1.1.1.X:XXXX
Spoofed Gateway MAC:
00:0c:29:11:XX:XX
Suspicious User-Agent: CVE-202X-XXXXXXX
ARP Spoof Packets: 9X
Compromised Credentials: username=dev_user
Exfiltration Domain: exfil-XXXXXX.xyz
Exfiltration Protocol: DNS

## Remediation & Defense Recommendations

# Dynamic ARP Inspection (DAI): Enable DAI and DHCP Snooping on network switches to mitigate Man-in-the-Middle (MITM) ARP poisoning attacks.

# WAF & User-Agent Filtering: Configure Web Application Firewalls (WAF) to drop traffic presenting known exploit signatures or anomalous script-based User-Agents.

# DNS Exfiltration Defense: Implement DNS monitoring to detect high-frequency TXT/A query patterns and block unapproved dynamic DNS domains.
