# 🕷️ Wizard Spider (TrickBot)
## Ransomware Threat Actor & Enterprise Attack Chain Analysis

[![Category](https://img.shields.io/badge/Category-Threat%20Intelligence-purple?style=for-the-badge)](https://img.shields.io/badge/Category-Threat%20Intelligence-purple?style=for-the-badge) [![Focus](https://img.shields.io/badge/Focus-Ransomware%20Operations-blue?style=for-the-badge)](https://img.shields.io/badge/Focus-Ransomware%20Operations-blue?style=for-the-badge) [![Framework](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-red?style=for-the-badge)](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-red?style=for-the-badge)

---

[![](https://github.com/shannonasmith/Cyber-Writups-VT/raw/main/images/Screenshot%202026-03-20%20171903.png)](https://github.com/shannonasmith/Cyber-Writups-VT/blob/main/images/Screenshot%202026-03-20%20171903.png)

---

### 🎯 Objective

Investigate the **Wizard Spider** threat group to understand how a sophisticated, financially motivated criminal organization conducts large-scale enterprise ransomware operations — from initial phishing through to final payload deployment and extortion.

The goal was to move beyond surface-level "ransomware bad" analysis and understand:

- how the group structures its operations and tools
- where each stage of the attack creates detection opportunities
- what the dwell time between initial access and ransomware deployment means for defenders
- why early-stage detection matters more than endpoint ransomware detection

---

### 🖥 Environment

| Component | Purpose |
|-----------|---------|
| Threat intelligence data | Adversary profiling and TTP mapping |
| MITRE ATT&CK framework | Structured TTP classification |
| TrickBot, Ryuk, Conti malware analysis | Capability and behavior understanding |
| Windows enterprise / Active Directory | Target environment |
| Cobalt Strike | Post-exploitation C2 framework used by the group |

---

### 📦 Step 1 — Threat Actor Profile

Wizard Spider is a highly organized, Russia-based cybercriminal group first identified around 2016. Unlike opportunistic attackers, the group operates with business-level structure — dedicated development teams, specialized operators, and affiliate relationships. They are responsible for:

- **TrickBot** — a modular banking trojan evolved into a full-featured post-exploitation platform
- **Ryuk** — ransomware deployed selectively against high-value targets after extended dwell periods
- **Conti** — successor ransomware operation with a documented RaaS (ransomware-as-a-service) affiliate model

[![](https://github.com/shannonasmith/Cyber-Writups-VT/raw/main/images/Screenshot%202026-03-20%20171911.png)](https://github.com/shannonasmith/Cyber-Writups-VT/blob/main/images/Screenshot%202026-03-20%20171911.png)

*Adversary profile: Wizard Spider's organizational structure, tooling, and target selection criteria.*

The group selects victims based on **ability to pay and operational dependency on uptime** — hospitals, logistics companies, and financial institutions are disproportionately targeted because downtime is existential, increasing ransom payment probability.

---

### 🔍 Step 2 — Initial Access

Wizard Spider's most common initial access vector is **malicious email campaigns** — specifically:

- phishing emails with malicious document attachments (Word/Excel with macros)
- malspam campaigns at scale targeting organizations in specific sectors
- occasionally compromised credentials purchased from initial access brokers

[![](https://github.com/shannonasmith/Cyber-Writups-VT/raw/main/images/Screenshot%202026-03-20%20171937.png)](https://github.com/shannonasmith/Cyber-Writups-VT/blob/main/images/Screenshot%202026-03-20%20171937.png)

*Socio-political context and sector targeting: healthcare, education, and critical infrastructure are preferred targets.*

Initial access is actually the **least sophisticated phase** of a Wizard Spider attack. The goal is simply foothold establishment — the real damage happens weeks later. This is important for defenders: blocking the phishing email prevents everything that follows, but the attack chain is designed to persist through detection of individual stages.

---

### 🧪 Step 3 — TrickBot Deployment

Once a victim opens the malicious document and enables macros, TrickBot is downloaded and executed. TrickBot is a **modular trojan** — its capabilities are configured per-campaign and extended through downloadable plugins:

| TrickBot Module | Capability |
|-----------------|------------|
| `systeminfo` | Host and network enumeration |
| `injectDll` | Browser credential theft |
| `pwgrab` | Credential harvesting from applications |
| `networkDll` | Active Directory enumeration |
| `importDll` | Domain controller discovery |
| `shareDll` | SMB share enumeration |

[![](https://github.com/shannonasmith/Cyber-Writups-VT/raw/main/images/Screenshot%202026-03-20%20171945.png)](https://github.com/shannonasmith/Cyber-Writups-VT/blob/main/images/Screenshot%202026-03-20%20171945.png)

*TrickBot capabilities and malware ecosystem — demonstrating the modular architecture that enables targeted post-exploitation.*

TrickBot communicates with C2 infrastructure over HTTPS, making traffic detection difficult without SSL inspection. Its persistence mechanism uses scheduled tasks or Windows services, which blend into normal system activity.

**Detection opportunity:** TrickBot's network enumeration behavior — particularly Active Directory queries using `nltest` and `AdFind` — creates a detectable signature when combined with the process that launched it. A Word document spawning network enumeration tools is a high-fidelity alert that most organizations miss because they don't correlate process lineage with network activity.

---

### 🔄 Step 4 — Cobalt Strike C2 & Persistence

After TrickBot establishes the initial foothold and performs reconnaissance, Wizard Spider typically deploys **Cobalt Strike** — a commercial penetration testing framework repurposed for attack operations.

Cobalt Strike provides:

- interactive command shell and process injection
- Beacon C2 communication with configurable sleep intervals (evading time-based detection)
- lateral movement modules (PsExec, WMI, SMB)
- credential manipulation (pass-the-hash, pass-the-ticket)

Persistence is established via:

- scheduled tasks with legitimate-looking names
- Windows services
- registry run keys

**Detection opportunity:** Cobalt Strike beaconing — even with long sleep intervals — produces a consistent network pattern. Beacons check in at regular intervals (often every 60 seconds with jitter) to a small number of external IPs. DNS beaconing produces predictable query patterns. These signatures are well-documented and detectable with proper network monitoring.

---

### 🌐 Step 5 — Lateral Movement

With Cobalt Strike established, the group conducts systematic lateral movement across the environment using a combination of techniques:

- **PsExec** — remote service creation using harvested credentials
- **RDP** — interactive sessions to high-value systems
- **AdFind / nltest** — Active Directory enumeration to map the domain and identify domain controllers, privileged accounts, and backup systems

[![](https://github.com/shannonasmith/Cyber-Writups-VT/raw/main/images/Screenshot%202026-03-20%20171951.png)](https://github.com/shannonasmith/Cyber-Writups-VT/blob/main/images/Screenshot%202026-03-20%20171951.png)

*Infrastructure and tooling: Cobalt Strike C2 infrastructure combined with credential reuse enables systematic traversal of enterprise environments.*

The goal of lateral movement is twofold: reaching domain controllers (for maximum ransomware deployment coverage) and identifying backup systems (for targeting or disabling, to eliminate recovery options).

---

### 🧠 Step 6 — Discovery & Pre-Ransomware Staging

Before deploying ransomware, the group spends time understanding the environment:

- enumerating file shares and identifying high-value data for double extortion
- identifying and disabling or deleting backup systems (VSS shadow copies, backup agents)
- mapping domain-joined systems to maximize encryption coverage

[![](https://github.com/shannonasmith/Cyber-Writups-VT/raw/main/images/Screenshot%202026-03-20%20171921.png)](https://github.com/shannonasmith/Cyber-Writups-VT/blob/main/images/Screenshot%202026-03-20%20171921.png)

*Victim profile and targeting criteria: sector, revenue, and operational dependency on uptime drive target selection.*

**This is the critical detection window.** Backup deletion (particularly `vssadmin delete shadows`) and mass file enumeration are high-fidelity indicators that ransomware deployment is imminent. Organizations that alert on these behaviors have hours to respond before encryption begins.

---

### 💣 Step 7 — Ransomware Deployment (Ryuk / Conti)

The final stage involves deploying Ryuk or Conti across all domain-joined systems simultaneously, typically via Group Policy or PsExec from the domain controller — maximizing coverage and minimizing the time between first and last encrypted system.

[![](https://github.com/shannonasmith/Cyber-Writups-VT/raw/main/images/Screenshot%202026-03-20%20171929.png)](https://github.com/shannonasmith/Cyber-Writups-VT/blob/main/images/Screenshot%202026-03-20%20171929.png)

*Global victim distribution: Wizard Spider operations span healthcare, education, financial services, and government sectors across North America and Europe.*

**Double extortion:** Beyond encrypting files, the group exfiltrates sensitive data before encryption and threatens to publish it on a leak site if the ransom isn't paid. This removes the option of simply restoring from backups — even a full recovery doesn't prevent the data leak threat.

---

## 🧠 Attack Chain

```
Phishing email (malicious macro document)
    ↓
TrickBot deployment (credential harvesting, AD enumeration)
    ↓
Cobalt Strike C2 (persistent access, lateral movement platform)
    ↓
Lateral movement (PsExec, RDP, credential reuse)
    ↓
Discovery & staging (backup deletion, share enumeration, data exfil)
    ↓
Ransomware deployment (Ryuk/Conti via GPO or PsExec from DC)
    ↓
Double extortion (ransom demand + leak threat)
```

---

## 🛠 MITRE ATT&CK Techniques

[![](https://github.com/shannonasmith/Cyber-Writups-VT/raw/main/images/Screenshot%202026-03-20%20171955.png)](https://github.com/shannonasmith/Cyber-Writups-VT/blob/main/images/Screenshot%202026-03-20%20171955.png)

*MITRE ATT&CK mapping across the full Wizard Spider attack chain.*

| Technique | ID | Phase |
|-----------|-----|-------|
| Spearphishing Attachment | T1566.001 | Initial Access |
| Command and Scripting Interpreter (Macros) | T1059.005 | Execution |
| Scheduled Task / Job | T1053.005 | Persistence |
| OS Credential Dumping | T1003 | Credential Access |
| Domain Account Enumeration (AdFind) | T1087.002 | Discovery |
| Lateral Tool Transfer | T1570 | Lateral Movement |
| Remote Services (PsExec, RDP) | T1021 | Lateral Movement |
| Inhibit System Recovery (VSS deletion) | T1490 | Defense Evasion |
| Data Encrypted for Impact | T1486 | Impact |
| Exfiltration for Double Extortion | T1041 | Exfiltration |

---

## 🛡 Defensive Insight

The key lesson from Wizard Spider is that **ransomware itself is the last event in a weeks-long intrusion, not a standalone attack**. By the time files are encrypting, defenders have already missed multiple intervention points.

The most effective defense layers, in order of where they interrupt the chain:

- **Email filtering and macro execution controls** — stop TrickBot before it runs
- **Process lineage alerting** — detect Office applications spawning network enumeration tools
- **Network monitoring for C2 beaconing** — identify Cobalt Strike before lateral movement begins
- **Privileged access controls** — credential tiering limits the blast radius of credential theft
- **Backup isolation** — offline or immutable backups survive even when the attacker reaches backup systems
- **VSS and shadow copy monitoring** — backup deletion is a ransomware pre-cursor; alert on it

---

## 💡 Skills Reinforced

- Threat actor profiling and RaaS operational model analysis
- Multi-stage attack chain reconstruction with MITRE ATT&CK mapping
- Detection opportunity identification across each attack phase
- Ransomware pre-cursor behavioral analysis
- Enterprise defensive architecture evaluation

---

🕷️ **Wizard Spider operates like a business — with targets, tools, and a deployment timeline**
🔄 **Ransomware is the final event in a weeks-long intrusion — early detection is the only effective defense**
🔐 **Backup deletion before encryption is the clearest signal that ransomware deployment is imminent**

← [Back to VT Writeups](../README.md)
