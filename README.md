<div align="center">

# 🎓 Cybersecurity Writeups — Virginia Tech

## 🧠 Research & Analytical Security Studies

![Focus](https://img.shields.io/badge/Focus-Threat%20Analysis%20%7C%20Malware%20%7C%20Defense-blue?style=for-the-badge)
![Program](https://img.shields.io/badge/Program-Virginia%20Tech%20M.S.%20IT-maroon?style=for-the-badge)
![Approach](https://img.shields.io/badge/Approach-Analysis%20%7C%20Research%20%7C%20Application-success?style=for-the-badge)

</div>

---

## 🧠 What This Is

Technical threat research and attack chain analysis produced during my Master of Information Technology program at Virginia Tech. Each writeup examines a real-world incident or threat actor from a SOC analyst perspective — reconstructing how the attack unfolded, identifying where detection opportunities existed at each stage, and analyzing what effective response should have looked like.

The three cases deliberately cover different attack categories, attacker motivations, and initial access paths. Together they represent a cross-section of the threat landscape a detection engineer or SOC analyst encounters in practice — and three different answers to the question "where could a defender have intervened?"

---

## 🧩 Writeups

---

### 🦠 [NotPetya — Global Destructive Malware Analysis](./Not-Petya-Writeup/README.md)

[![Category](https://img.shields.io/badge/Category-Incident%20Response-red?style=for-the-badge)]() [![Focus](https://img.shields.io/badge/Focus-Lateral%20Movement-blue?style=for-the-badge)]() [![Impact](https://img.shields.io/badge/Impact-Destructive%20Wiper-black?style=for-the-badge)]()

**What it covers:** NotPetya's 2017 Sandworm campaign — beginning with a supply chain compromise of M.E.Doc Ukrainian accounting software and escalating through Mimikatz-style credential dumping, dual-path lateral movement (PsExec/WMIC for credential-based spread, EternalBlue/EternalRomance for unpatched systems), and a final wiper payload that overwrote both the MBR and MFT, making recovery impossible even if the boot record was restored.

**SOC perspective:** Analyzes what NotPetya looks like from inside a SOC — authentication volume spikes detectable via Splunk (Event IDs 4624/4625), suspicious service creation (Event ID 7045) indicating PsExec-based lateral movement, and abnormal SMB connection patterns visible via Zeek. The writeup identifies why detection was so difficult: the attack used legitimate admin tools, valid credentials, and trusted software delivery — each stage individually appeared normal.

**Key insight:** NotPetya's dual propagation path is what made it globally catastrophic. Organizations that had patched EternalBlue were still fully vulnerable if any system had cached domain credentials. The credential reuse path meant no exploit was required. Defenders needed both patch management *and* credential hygiene — either alone was insufficient.

---

### 🕷️ [Wizard Spider — Ransomware-as-a-Service Attack Chain Analysis](./Wizard-Spider-Writeup/README.md)

[![Category](https://img.shields.io/badge/Category-Threat%20Intelligence-purple?style=for-the-badge)]() [![Focus](https://img.shields.io/badge/Focus-Ransomware%20Operations-blue?style=for-the-badge)]() [![Framework](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-red?style=for-the-badge)]()

**What it covers:** Wizard Spider's multi-stage attack chain — phishing delivery of malicious macro documents → TrickBot deployment (a modular trojan with swappable plugins for credential harvesting, Active Directory enumeration via `nltest`/`AdFind`, and network mapping) → Cobalt Strike C2 for persistent access and lateral movement → backup deletion via `vssadmin` → Ryuk/Conti ransomware deployment across domain-joined systems simultaneously via Group Policy or PsExec from a compromised domain controller. Includes double extortion via pre-encryption data exfiltration.

**SOC perspective:** Identifies detection opportunities at each stage: Office applications spawning network enumeration tools is a high-fidelity process lineage alert; Cobalt Strike beaconing produces consistent interval-based network patterns detectable with DNS and traffic analysis; `vssadmin delete shadows` is a ransomware pre-cursor that should trigger immediate response. By the time files encrypt, all of these signals have already fired and been missed.

**Key insight:** The average dwell time between TrickBot initial access and Ryuk/Conti deployment is days to weeks. This means the ransomware event itself is the *last* detection opportunity, not the first. Organizations that only focus on endpoint ransomware detection are solving the wrong problem — the attack is won or lost in the TrickBot and Cobalt Strike stages.

---

### 🚗 [Uber Breach — Lapsus$ Social Engineering & Access Escalation](./Uber-Lapsus-Writeup/README.md)

[![Category](https://img.shields.io/badge/Category-Incident%20Response-black?style=for-the-badge)]() [![Focus](https://img.shields.io/badge/Focus-Identity%20Attack-blue?style=for-the-badge)]() [![Technique](https://img.shields.io/badge/Technique-MFA%20Fatigue-red?style=for-the-badge)]()

**What it covers:** The 2022 Uber breach — contractor credentials purchased from an infostealer marketplace → push bombing (MFA fatigue) combined with WhatsApp impersonation of IT support → VPN access → discovery of hardcoded admin credentials in internal PowerShell scripts → compromise of Thycotic PAM (privileged access management), which contained credentials to downstream systems → access to AWS, VMware vSphere, SentinelOne, HackerOne (private vulnerability reports), and Slack. No malware, no zero-days, no software vulnerabilities exploited.

**SOC perspective:** Analyzes the specific detection failures: MFA push volume was not rate-limited or alerted on; VPN authentication from an unusual geographic location wasn't flagged; a contractor account accessing infrastructure PowerShell scripts was not anomalous to the monitoring system; lateral movement to Thycotic from a contractor account was not alerted. Each of these is a concrete, fixable gap with a specific control.

**Key insight:** Compromising the PAM system — Thycotic — was the force multiplier. One credential discovery turned into access to every credential the system managed. This is why PAM systems must be treated as tier-0 infrastructure: they're not just another tool, they're a master key to the entire environment. Their access logs, authentication patterns, and anomaly alerts need the same scrutiny as domain controllers.

---

## 🧠 Why These Three Cases

| Case | Motivation | Initial Access | Core Technique | Defender Intervention Point |
|------|-----------|----------------|----------------|---------------------------|
| NotPetya | Geopolitical disruption | Supply chain (trusted software update) | Credential reuse + EternalBlue lateral movement | Credential hygiene + SMB monitoring |
| Wizard Spider | Financial (RaaS) | Phishing + malicious macro | TrickBot → Cobalt Strike → ransomware | Process lineage alerting + C2 beacon detection |
| Uber / Lapsus$ | Notoriety | Purchased credentials + MFA fatigue | Social engineering → hardcoded creds → PAM compromise | MFA controls + behavioral analytics post-auth |

These cases were selected to cover the full range of what a SOC analyst faces: a nation-state campaign where the entry point was invisible, a financially motivated operation with a weeks-long dwell period, and an identity-based intrusion with no malware at all. Each one has a different answer to "what should have caught this" — and understanding all three means understanding why detection strategy can't be one-size-fits-all.

---

## 💡 Skills Demonstrated

- Attack chain reconstruction from real-world incidents with SOC analyst framing
- Detection opportunity identification at each attack phase — not just post-incident description
- MITRE ATT&CK technique mapping across multi-stage intrusions (T1195.002, T1003.001, T1561.002, T1566.001, T1490, T1621, T1552.001, and others)
- Detection logic development grounded in actual attacker behavior (Splunk SPL, Zeek)
- Threat actor profiling covering nation-state, RaaS, and hacktivist motivations
- Defensive architecture evaluation against identity, endpoint, and network-based attack paths

---

## 🔗 Related Projects

The analytical frameworks developed in these writeups directly inform the detection and investigation logic in my hands-on SOC engineering work:

- [AI-Assisted SOC Alert Analyzer](https://github.com/shannonasmith/AI-Assisted-SOC-Alert-Analyzer) — triage pipeline with MITRE mapping
- [ATT&CK Mapping Engine](https://github.com/shannonasmith/AI-Assisted-SOC-MITRE-ATTACK-Mapping-Engine) — NLP-based technique classification
- [Agentic SOC Investigation Engine](https://github.com/shannonasmith/Agentic-SOC-Investigation-Engine) — stateful investigation and response automation

---

<div align="center">

## 👤 Shannon Smith

Cybersecurity | DFIR • Threat Analysis • Security Operations
U.S. Navy Veteran | Virginia Tech — M.S. Information Technology

</div>
