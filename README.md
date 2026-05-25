<div align="center">

# 🎓 Cybersecurity Writeups — Virginia Tech

## 🧠 Research & Analytical Security Studies

![Focus](https://img.shields.io/badge/Focus-Threat%20Analysis%20%7C%20Malware%20%7C%20Defense-blue?style=for-the-badge)
![Program](https://img.shields.io/badge/Program-Virginia%20Tech%20M.S.%20IT-maroon?style=for-the-badge)
![Approach](https://img.shields.io/badge/Approach-Analysis%20%7C%20Research%20%7C%20Application-success?style=for-the-badge)

</div>

---

## 🧠 What This Is

Technical threat research and attack chain analysis produced during my Master of Information Technology program at Virginia Tech. Each writeup examines a real-world incident or threat actor from a SOC analyst perspective — reconstructing how the attack unfolded, identifying where detection opportunities existed, and analyzing what the defensive response should have looked like.

The three cases deliberately cover different attack categories: a nation-state destructive malware campaign, a ransomware-as-a-service operation, and a social engineering intrusion. Together they represent a cross-section of the threat landscape a detection engineer or SOC analyst encounters in practice.

---

## 🧩 Writeups

---

### 🦠 [NotPetya — Global Destructive Malware Analysis](./Not-Petya-Writeup/README.md)

![Category](https://img.shields.io/badge/Category-Incident%20Response-red?style=for-the-badge)
![Focus](https://img.shields.io/badge/Focus-Lateral%20Movement-blue?style=for-the-badge)
![Impact](https://img.shields.io/badge/Impact-Destructive%20Wiper-black?style=for-the-badge)

**What it covers:** NotPetya's 2017 campaign — attributed to Sandworm — began as a supply chain compromise of M.E.Doc accounting software and escalated into one of the most destructive cyberattacks in history. This writeup reconstructs the attack chain from trusted software delivery → credential harvesting via Mimikatz-style dumping → lateral movement via EternalBlue, EternalRomance, PsExec, and WMIC → MBR overwrite and wiper payload execution.

**SOC perspective:** Analyzes what the attack would look like from inside a SOC — authentication spikes, abnormal SMB traffic patterns detectable via Zeek, suspicious service creation (Event ID 7045), and the detection logic that could surface lateral movement before propagation becomes enterprise-wide.

**Key insight:** NotPetya was not ransomware. It was destruction disguised as ransomware, designed to cause maximum operational damage rather than generate payment. The credential reuse and legitimate admin tool abuse made it nearly invisible until the wiper triggered — a pattern that reappears in modern nation-state intrusions.

---

### 🕷️ [Wizard Spider — Ransomware-as-a-Service Attack Chain Analysis](./Wizard-Spider-Writeup/README.md)

![Category](https://img.shields.io/badge/Category-Threat%20Research-orange?style=for-the-badge)
![Focus](https://img.shields.io/badge/Focus-Ransomware%20%7C%20TrickBot-blue?style=for-the-badge)

**What it covers:** Wizard Spider is a financially motivated threat actor responsible for TrickBot, Ryuk, and Conti ransomware operations. This writeup profiles the group's attack chain — phishing delivery → TrickBot infection for credential harvesting and network reconnaissance → Cobalt Strike deployment for C2 → Ryuk ransomware execution — and examines how the RaaS model enables sophisticated multi-stage attacks that move from initial access to ransomware in days.

**SOC perspective:** Focuses on the detection windows that exist between stages — TrickBot's network enumeration behavior, Cobalt Strike beacon traffic patterns, and the pre-ransomware reconnaissance activity that, if caught, can prevent the final payload from deploying.

**Key insight:** The gap between initial access and ransomware execution is where defenders have the most leverage. Wizard Spider's campaigns routinely spent days or weeks in victim environments before detonating — meaning visibility into lateral movement and C2 traffic is more operationally valuable than endpoint detection of the ransomware itself.

---

### 🚗 [Uber Breach — Lapsus$ Social Engineering & Access Escalation](./Uber-Lapsus-Writeup/README.md)

![Category](https://img.shields.io/badge/Category-Threat%20Research-orange?style=for-the-badge)
![Focus](https://img.shields.io/badge/Focus-Social%20Engineering%20%7C%20MFA%20Fatigue-blue?style=for-the-badge)

**What it covers:** The 2022 Uber breach by Lapsus$ demonstrates that sophisticated technical exploitation isn't required when social engineering is an option. This writeup analyzes the attack path: credential purchase from an infostealer market → MFA fatigue attack to bypass multi-factor authentication → WhatsApp social engineering to convince the target to approve the MFA prompt → access to internal systems including HackerOne, Slack, AWS, and Google Workspace.

**SOC perspective:** Examines what detection looked like (and didn't look like) — the MFA fatigue pattern, the post-authentication behavior that should have triggered alerts, and the access escalation path through internal tooling that gave the attacker broad visibility into Uber's environment.

**Key insight:** MFA is not a silver bullet. When push-based MFA is combined with social engineering, a determined attacker can bypass it without touching a vulnerability. Phishing-resistant MFA (FIDO2/hardware keys) and user behavior analytics on post-authentication activity are the meaningful controls here.

---

## 🧠 Why These Three Cases

| Case | Attack Category | Primary Technique | Defender Lesson |
|------|----------------|-------------------|-----------------|
| NotPetya | Nation-state, destructive | Supply chain + lateral movement | Wiper intent disguised as ransomware; lateral movement visibility is critical |
| Wizard Spider | Financially motivated RaaS | Multi-stage: TrickBot → C2 → Ryuk | Pre-ransomware dwell time is the detection window |
| Uber / Lapsus$ | Social engineering | MFA fatigue + access escalation | Technical controls fail without behavioral detection |

These cases were selected to cover the three major attacker motivations (geopolitical, financial, notoriety), three distinct initial access paths (supply chain, phishing, credential purchase), and three different detection challenges that map directly to real SOC work.

---

## 💡 Skills Demonstrated

- Attack chain reconstruction from real-world incidents
- SOC-perspective analysis: where detection was possible and where it wasn't
- MITRE ATT&CK technique identification across multi-stage intrusions
- Detection logic development (Splunk SPL, Zeek) grounded in actual attacker behavior
- Threat actor profiling and tradecraft analysis

---

## 🔗 Related Projects

These academic analyses inform the detection and investigation logic built in my hands-on SOC engineering work:

- [AI-Assisted SOC Alert Analyzer](https://github.com/shannonasmith/AI-Assisted-SOC-Alert-Analyzer) — triage pipeline with MITRE mapping
- [ATT&CK Mapping Engine](https://github.com/shannonasmith/AI-Assisted-SOC-MITRE-ATTACK-Mapping-Engine) — NLP-based technique classification
- [Agentic SOC Investigation Engine](https://github.com/shannonasmith/Agentic-SOC-Investigation-Engine) — stateful investigation and response automation

---

<div align="center">

## 👤 Shannon Smith

Cybersecurity | DFIR • Threat Analysis • Security Operations  
U.S. Navy Veteran | Virginia Tech — M.S. Information Technology

</div>
