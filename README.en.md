# SIEM-Based Alert Triage & Multi-Source Investigation

SOC analyst workflows for alert triage, log correlation, and incident reconstruction across multiple data sources. This portfolio documents the query patterns, pivot logic, and reporting practices applied in eight hands-on SOC scenarios.

## Overview

The core of a Tier 1 SOC analyst's day is alert triage: receiving an alert from a SIEM, validating whether it represents a real incident, pivoting across log sources to scope it, and either closing it as benign or escalating with evidence. This repository documents the methodology and query patterns used across eight scenarios spanning Splunk, the Elastic Stack, network traffic analysis with TShark, and IDS rule authoring with Snort.

The aim is to demonstrate **investigation methodology and query fluency**, not to reproduce lab solutions. No flags, answers, or step-by-step walkthroughs are included.

## Scenario Context

The eight underlying scenarios covered a representative cross-section of SOC work:

- Brute-force authentication attempts against exposed services (SSH, RDP, web)
- Suspicious process execution chains observed in endpoint telemetry
- Command-and-control beaconing patterns in network traffic
- Data exfiltration over non-standard ports and protocols
- Multi-stage capstone investigations requiring correlation of authentication, endpoint, and network logs
- PCAP-based reconstruction of attacker activity (file transfers, directory enumeration, lateral movement)

## Tools & Techniques

**SIEM Platforms**
- **Splunk** — Search Processing Language (SPL); field extraction; statistical commands (`stats`, `timechart`, `top`, `rare`); subsearches; lookup tables.
- **Elastic Stack / Kibana** — KQL and Lucene query syntax; index pattern navigation; Discover-based pivoting; dashboard interpretation.

**Network Traffic Analysis**
- **TShark** — CLI-based PCAP analysis: display filters, statistics (`-z`), follow-stream extraction, hierarchical protocol breakdown.
- **Wireshark** — GUI-based deep dives, follow TCP/HTTP/TLS streams, export objects, IO graphs.

**Detection Engineering**
- **Snort** — IDS rule authoring (rule header, options, content matching, PCRE), testing against captured traffic.
- Familiarity with Sigma rule structure and SIEM-portability concepts.

**Enrichment & Threat Context**
- VirusTotal, URLScan, AbuseIPDB, Talos Intelligence
- WHOIS and reverse-DNS pivots
- MITRE ATT&CK mapping for technique attribution

## Investigation Methodology

The standard workflow applied across scenarios:

1. **Alert Receipt & Initial Triage** — read the alert title, severity, and source; form an initial hypothesis (`benign? policy violation? actual compromise?`).
2. **Query Building** — translate the alert into SPL or KQL queries; broaden scope by removing assumptions; narrow scope by adding correlation keys (user, host, source IP, time window).
3. **Source Pivoting** — pivot from one data source to another using shared keys: endpoint logs → authentication logs → network logs → DNS logs → proxy logs.
4. **Timeline Reconstruction** — order events chronologically; identify the first observable malicious activity and the most recent.
5. **Attack Stage Mapping** — map observed activity to Cyber Kill Chain phases and MITRE ATT&CK techniques.
6. **PCAP Analysis (where applicable)** — open packet captures in TShark or Wireshark, follow streams, extract transferred objects, identify protocols in use vs. ports observed.
7. **Reporting** — produce a structured incident summary: alert origin, scope of impact, attacker actions, IOCs, recommended containment, and escalation path.

## Skills Demonstrated

- Splunk SPL query authoring (search, transform, statistical commands)
- Kibana KQL and Lucene query authoring
- PCAP analysis at scale with TShark (CLI) and Wireshark (GUI)
- Snort rule authoring and IDS detection logic
- Multi-source log correlation across endpoint, network, authentication, and DNS telemetry
- Timeline reconstruction and structured incident reporting
- Cyber Kill Chain and MITRE ATT&CK mapping
- Triage prioritization (true positive vs. false positive vs. benign true positive)

## MITRE ATT&CK Coverage

Techniques observed and mapped across the scenarios include:

- **T1110** — Brute Force (Password Guessing and Spraying)
- **T1078** — Valid Accounts
- **T1059** — Command and Scripting Interpreter
- **T1071** — Application Layer Protocol (C2 communications)
- **T1041** — Exfiltration Over C2 Channel
- **T1003** — OS Credential Dumping
- **T1021** — Remote Services (RDP, SSH, SMB lateral movement)
- **T1046** — Network Service Discovery

## Key Takeaways

- The fastest path to closing an alert is to **disprove the hypothesis**, not to prove it. An analyst who looks for evidence the alert is benign closes tickets faster and with fewer false escalations.
- Time normalization matters. Correlating events across log sources without first normalizing time zones leads to incorrect timelines and missed activity.
- PCAP analysis is not a backup skill. Many alerts are unresolvable from logs alone; the analyst who can read traffic at the protocol level resolves the incidents others cannot.

## Disclaimer

This repository documents personal training methodology and skills development. The underlying scenarios are training labs hosted on TryHackMe. **No room answers, flags, or solution walkthroughs are included.** All observations are framed at the methodology level. The intent is to demonstrate analyst thinking and tool fluency, not to substitute for the original training material.

## Source Training Rooms (TryHackMe — SOC Level 1)

For transparency, the following rooms form the practical foundation of this portfolio:

- Splunk: The Basics
- Investigating with ELK 101 (Elastic Stack: The Basics)
- TShark: The Basics
- TShark Challenge I: Teamwork
- TShark Challenge II: Directory
- Summit (Capstone)
- Monday Monitor
- Friday Overtime
- Retracted
- Snort (and Snort Challenge rooms, prior path version)

---

*Maintained by Arejdi Haxhiu*
*🇦🇱 Albanian version available: [`README.al.md`](README.al.md)*
