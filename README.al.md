# Triage Alarmesh SIEM dhe Investigim Multi-Source

Workflow-e të SOC analyst-it për triage të alarmeve, korrelacion të logs-eve, dhe rikonstruktim të incidenteve nëpër burime të shumta të dhënash. Ky portofol dokumenton query patterns, logjikën e pivot-it, dhe praktikat e raportimit të aplikuara në tetë skenarë hands-on SOC.

## Përmbledhje

Bërthama e ditës së një Tier 1 SOC analyst-i është triage i alarmeve: marrja e një alarmi nga një SIEM, validimi nëse përfaqëson një incident të vërtetë, pivot-imi nëpër burime logs-esh për ta scope-uar, dhe ose mbyllja si benign ose përshkallëzimi me dëshmi. Ky repository dokumenton metodologjinë dhe query patterns të përdorur në tetë skenarë që mbulojnë Splunk, Elastic Stack, analizë të trafikut të rrjetit me TShark, dhe authoring rregullash IDS me Snort.

Qëllimi është të demonstrohet **metodologjia e investigimit dhe rrjedhshmëria me query-të**, jo të riprodhohen zgjidhjet e lab-it. Nuk përfshihen flag-e, përgjigje, ose walkthroughs.

## Konteksti i Skenarëve

Tetë skenarët bazë mbuluan një kryqëzim përfaqësues të punës SOC:

- Tentativa brute-force authentication kundër shërbimeve të ekspozuara (SSH, RDP, web)
- Zinxhirë ekzekutimi procesesh të dyshimta të vëzhguara në telemetrinë endpoint
- Patterns të command-and-control beaconing në trafikun e rrjetit
- Exfiltrim i të dhënave nëpër ports dhe protocols non-standard
- Investigime capstone multi-stage që kërkojnë korrelacion mes logs-eve të autentifikimit, endpoint-it dhe rrjetit
- Rikonstruktim i bazuar në PCAP i aktivitetit të sulmuesit (file transfers, directory enumeration, lateral movement)

## Veglat dhe Teknikat

**Platformat SIEM**
- **Splunk** — Search Processing Language (SPL); field extraction; komandat statistikore (`stats`, `timechart`, `top`, `rare`); subsearches; lookup tables.
- **Elastic Stack / Kibana** — sintaksë KQL dhe Lucene; navigim i index pattern-eve; pivot-im i bazuar në Discover; interpretim i dashboard-eve.

**Analizë e Trafikut të Rrjetit**
- **TShark** — analizë PCAP nga CLI: display filters, statistika (`-z`), follow-stream extraction, ndarje hierarkike e protokolleve.
- **Wireshark** — deep dives nga GUI, follow TCP/HTTP/TLS streams, export i objekteve, IO graphs.

**Detection Engineering**
- **Snort** — authoring i rregullave IDS (rule header, options, content matching, PCRE), testim kundër trafikut të kapur.
- Njohuri me strukturën e rregullave Sigma dhe konceptet e SIEM-portability.

**Enrichment dhe Threat Context**
- VirusTotal, URLScan, AbuseIPDB, Talos Intelligence
- WHOIS dhe reverse-DNS pivots
- Mapim MITRE ATT&CK për atribuim të teknikave

## Metodologjia e Investigimit

Workflow-i standard i aplikuar në skenarë:

1. **Marrja e Alarmit dhe Triage Fillestar** — lexo titullin e alarmit, severity, dhe burimin; formo një hipotezë fillestare (`benign? shkelje policy? kompromis i vërtetë?`).
2. **Ndërtim Query-sh** — përkthe alarmin në query SPL ose KQL; zgjeroje scope-in duke hequr supozimet; ngushtoje scope-in duke shtuar correlation keys (user, host, source IP, time window).
3. **Pivot mes Burimeve** — pivot nga një burim të dhënash në një tjetër duke përdorur çelësat e përbashkët: endpoint logs → authentication logs → network logs → DNS logs → proxy logs.
4. **Rikonstruktim i Timeline-it** — rendit ngjarjet kronologjikisht; identifiko aktivitetin e parë të dëmshëm të vëzhguar dhe atë më të fundit.
5. **Mapim i Fazave të Sulmit** — mapo aktivitetin e vëzhguar me fazat e Cyber Kill Chain dhe teknikat MITRE ATT&CK.
6. **Analizë PCAP (kur është e aplikueshme)** — hap packet captures në TShark ose Wireshark, follow streams, ekstrakto objekte të transferuara, identifiko protokollet në përdorim kundrejt ports-ave të vëzhguar.
7. **Raportim** — prodho një incident summary të strukturuar: origjina e alarmit, scope i impaktit, veprimet e sulmuesit, IOCs, containment i rekomanduar, dhe path-i i përshkallëzimit.

## Aftësi të Demonstruara

- Authoring query-sh Splunk SPL (search, transform, komanda statistikore)
- Authoring query-sh Kibana KQL dhe Lucene
- Analizë PCAP në shkallë me TShark (CLI) dhe Wireshark (GUI)
- Authoring rregullash Snort dhe logjikë detection IDS
- Korrelacion logs-esh multi-source nëpër telemetri endpoint, network, authentication dhe DNS
- Rikonstruktim timeline dhe raportim i strukturuar i incidentit
- Mapim Cyber Kill Chain dhe MITRE ATT&CK
- Prioritizim triage (true positive kundrejt false positive kundrejt benign true positive)

## Mbulimi MITRE ATT&CK

Teknikat e vëzhguara dhe të mapuara nëpër skenarë përfshijnë:

- **T1110** — Brute Force (Password Guessing dhe Spraying)
- **T1078** — Valid Accounts
- **T1059** — Command and Scripting Interpreter
- **T1071** — Application Layer Protocol (komunikim C2)
- **T1041** — Exfiltration Over C2 Channel
- **T1003** — OS Credential Dumping
- **T1021** — Remote Services (RDP, SSH, SMB lateral movement)
- **T1046** — Network Service Discovery

## Mësimet Kryesore

- Rruga më e shpejtë për të mbyllur një alarm është **të hedhësh poshtë hipotezën**, jo ta provosh. Një analyst që kërkon dëshmi se alarmi është benign mbyll tickets më shpejt dhe me më pak përshkallëzime false.
- Normalizimi i kohës ka rëndësi. Korrelacioni i ngjarjeve nëpër burime logs-esh pa normalizuar fillimisht time zones çon në timelines të pasakta dhe aktivitet të humbur.
- Analiza PCAP nuk është aftësi backup. Shumë alarme janë të pazgjidhshme vetëm nga logs-et; analyst-i që mund të lexojë trafikun në nivel protokolli zgjidh incidentet që të tjerët nuk mund t'i zgjidhin.

## Disclaimer

Ky repository dokumenton metodologjinë personale të trajnimit dhe zhvillimin e aftësive. Skenarët bazë janë training labs të mbajtur në TryHackMe. **Nuk përfshihen përgjigje, flag-e, ose walkthroughs.** Të gjitha vëzhgimet janë formuluar në nivel metodologjie. Qëllimi është të demonstrohet të menduarit analitik dhe rrjedhshmëria me veglat, jo të zëvendësohet materiali origjinal i trajnimit.

## Source Training Rooms (TryHackMe — SOC Level 1)

Për transparencë, rooms-at e mëposhtëm përbëjnë themelin praktik të këtij portofoli:

- Splunk: The Basics
- Investigating with ELK 101 (Elastic Stack: The Basics)
- TShark: The Basics
- TShark Challenge I: Teamwork
- TShark Challenge II: Directory
- Summit (Capstone)
- Monday Monitor
- Friday Overtime
- Retracted
- Snort (dhe Snort Challenge rooms, nga versioni i mëparshëm i path-it)

---

*Mirëmbajtur nga Arejdi Haxhiu — Student në vitin e fundit të Inxhinierisë Elektronike, në kalim drejt rolit të SOC analyst.*
*🇬🇧 Versioni në anglisht: [`README.en.md`](README.en.md)*
