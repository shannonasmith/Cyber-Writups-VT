<div align="center">

## 🦠 NotPetya  
### Global Destructive Malware & SOC Investigation Analysis

![Category](https://img.shields.io/badge/Category-Incident%20Response-red?style=for-the-badge)
![Focus](https://img.shields.io/badge/Focus-Lateral%20Movement-blue?style=for-the-badge)
![Impact](https://img.shields.io/badge/Impact-Destructive%20Wiper-black?style=for-the-badge)

</div>

---

<div align="center">
<img src="../images/Screenshot 2026-03-20 171726.png" width="500">
</div>

---

## 🧠 Scenario

NotPetya was a destructive malware campaign that spread rapidly across enterprise environments by abusing trusted software updates, legitimate administrative tools, and credential access.

This investigation approaches NotPetya from a **Security Operations Center (SOC)** perspective, focusing on:

- Detection opportunities  
- Lateral movement analysis  
- Attack chain reconstruction  
- Incident response considerations  

---

## 🎯 Objective

Investigate how a targeted supply chain compromise escalated into a **global destructive event**.

Focus areas:

- initial access  
- propagation across networks  
- lateral movement techniques  
- destructive impact  

---

## 🚨 Detection Perspective (SOC View)

From a SOC standpoint, NotPetya would present as:

- spikes in authentication attempts  
- failed logins followed by success  
- abnormal use of admin tools (PsExec, WMIC)  
- rapid SMB traffic between systems  

---

### 📊 Detection Logic (Splunk)

```spl
index=windows EventCode=4624 OR EventCode=4625
| stats count by user, src_ip

➡️ Detects abnormal authentication patterns

index=windows EventCode=7045

➡️ Detects suspicious service creation (PsExec activity)

🖥 Environment
Tool	Purpose
Windows systems	Target environment
SMB	Lateral movement
Mimikatz	Credential dumping
PsExec / WMIC	Remote execution
EternalBlue / EternalRomance	Exploitation
SIEM	Detection analysis
📦 Step 1 — Initial Access

Compromised update server for M.E.Doc delivered malicious payload.

Victims installed what appeared to be a legitimate update.

📸 Evidence 1 — Initial Infection
<div align="center"> <img src="../images/Screenshot 2026-03-20 171726.png" width="500"> </div>

Analysis:
Execution appeared trusted, making detection difficult. No phishing or user interaction required.

Key Insight

trusted source bypassed defenses
execution blended into normal operations

➡️ Supply chain attack

🔍 Step 2 — Foothold
internal enumeration
preparation for credential harvesting
minimal visible impact

➡️ Low detection visibility at this stage

🧪 Step 3 — Credential Dumping

Used Mimikatz to extract credentials from memory.

Result:

administrative credential access
legitimate authentication across systems

Observation

Credential abuse:

blends with normal behavior
enables rapid escalation
bypasses many controls
🔄 Step 4 — Lateral Movement
🔹 Tools
PsExec
WMIC
🔹 Exploits
EternalBlue
EternalRomance
📸 Evidence 2 — Activity Timeline
<div align="center"> <img src="../images/Screenshot 2026-03-20 171621.png" width="500"> </div>

Analysis:
Shows rapid coordinated spread across systems → requires multi-source correlation in a SOC.

Key Insight

credential reuse + exploits
multiple propagation paths
rapid enterprise spread
🌐 Step 5 — Propagation
scanning for hosts
SMB connections
remote execution
📊 Network Perspective (Zeek)
cat conn.log | zeek-cut id.orig_h id.resp_h service

➡️ Reveals abnormal SMB traffic and scanning behavior

➡️ Behavior resembles a worm

💣 Step 6 — Destruction
MBR overwritten
files encrypted (no recovery)
systems rendered unusable

Observation

NotPetya:

not true ransomware
no recovery mechanism
designed for destruction

➡️ Wiper malware

🔐 Step 7 — Impact
enterprise outages
irreversible data loss
global disruption
📸 Evidence 3 — Global Impact
<div align="center"> <img src="../images/Screenshot 2026-03-20 171349.png" width="500"> </div>

Analysis:
Highlights scale → reinforces need for rapid containment and lateral movement detection.

🧠 Attack Flow
Supply chain compromise
↓
Trusted execution
↓
Credential harvesting
↓
Lateral movement
↓
Network-wide propagation
↓
Destruction
🛠 Techniques
supply chain compromise
credential dumping
lateral movement (PsExec, WMIC)
SMB exploitation
worm propagation
destructive payload
📸 Evidence 4 — Attribution
<div align="center"> <img src="../images/Screenshot 2026-03-20 171513.png" width="500"> </div>

Analysis:
Sandworm attribution indicates nation-state capability and coordinated execution.

🛡️ Response (SOC Playbook)

Containment

isolate systems
disable accounts
block SMB traffic

Eradication

remove malware
reimage systems
patch vulnerabilities

Recovery

restore from backups
validate integrity

Prevention

least privilege
disable SMBv1
network segmentation
enhanced logging
🧩 MITRE ATT&CK
Technique	Description
T1075	Pass-the-Hash
T1021	Remote Services
T1059	Command Execution
T1486	Data Encryption
📊 Key Takeaways
lateral movement is the primary risk
trusted systems can be weaponized
credential abuse drives spread
visibility is critical
💡 Skills Demonstrated
malware analysis
lateral movement investigation
SIEM detection logic
network analysis
SOC methodology
<div align="center">

🦠 NotPetya was not ransomware — it was destruction
🔄 Lateral movement enabled global impact
🔐 Trust can be weaponized

</div> ```
