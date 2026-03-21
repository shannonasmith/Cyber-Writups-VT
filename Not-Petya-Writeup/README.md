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
- failed logins followed by successful access
- abnormal use of admin tools (PsExec, WMIC)
- rapid SMB traffic between systems

### 📊 Detection Logic (Splunk)

```spl
index=windows EventCode=4624 OR EventCode=4625
| stats count by user, src_ip
```

➡️ Detects abnormal authentication patterns.

```spl
index=windows EventCode=7045
```

➡️ Detects suspicious service creation associated with PsExec-style lateral movement.

---

## 🖥️ Environment

| Tool | Purpose |
|---|---|
| Windows systems | Target environment |
| SMB | Lateral movement |
| Mimikatz | Credential dumping |
| PsExec / WMIC | Remote execution |
| EternalBlue / EternalRomance | Exploitation |
| SIEM | Detection analysis |

---

## 📦 Step 1 — Initial Access

A compromised update server for **M.E.Doc** delivered the malicious payload.

Victims installed what appeared to be a legitimate software update.

### 📸 Evidence 1 — Initial Infection

<div align="center">
  <img src="../images/Screenshot 2026-03-20 171726.png" width="500">
</div>

**Analysis:**  
Execution appeared trusted, making detection difficult. No phishing or direct user interaction was required.

**Key Insight**

- trusted source bypassed defenses
- execution blended into normal operations

➡️ This was a **supply chain attack**.

---

## 🔍 Step 2 — Foothold

Once executed, the malware established an early foothold by:

- performing internal enumeration
- preparing for credential harvesting
- maintaining minimal visible impact

➡️ Detection visibility was low at this stage.

---

## 🧪 Step 3 — Credential Dumping

NotPetya used **Mimikatz**-style credential theft from memory to obtain administrative access.

This enabled:

- administrative credential access
- legitimate authentication across systems
- rapid expansion without relying only on exploits

**Observation**

Credential abuse is highly effective because it:

- blends with normal behavior
- enables rapid escalation
- bypasses many controls

---

## 🔄 Step 4 — Lateral Movement

NotPetya spread using both legitimate administration tools and exploit-based techniques.

### 🔹 Tools
- PsExec
- WMIC

### 🔹 Exploits
- EternalBlue
- EternalRomance

### 📸 Evidence 2 — Activity Timeline

<div align="center">
  <img src="../images/Screenshot 2026-03-20 171621.png" width="500">
</div>

**Analysis:**  
This timeline shows rapid, coordinated spread across systems and reinforces the need for multi-source correlation inside a SOC.

**Key Insight**

- credential reuse + exploits
- multiple propagation paths
- rapid enterprise spread

---

## 🌐 Step 5 — Propagation

After gaining access, the malware propagated by:

- scanning for hosts
- opening SMB connections
- executing remotely on discovered systems

### 📊 Network Perspective (Zeek)

```bash
cat conn.log | zeek-cut id.orig_h id.resp_h service
```

➡️ Would reveal abnormal SMB traffic and internal scanning behavior.

➡️ This behavior closely resembles a **worm**.

---

## 💣 Step 6 — Destruction

Once propagation was complete, the malware moved into the destruction phase:

- MBR overwritten
- files encrypted without recovery capability
- systems rendered unusable

**Observation**

NotPetya was:

- not true ransomware
- missing a viable recovery path
- designed primarily for destruction

➡️ It functioned as **wiper malware**.

---

## 🔐 Step 7 — Impact

The final impact included:

- enterprise outages
- irreversible data loss
- global disruption

### 📸 Evidence 3 — Global Impact

<div align="center">
  <img src="../images/Screenshot 2026-03-20 171349.png" width="500">
</div>

**Analysis:**  
This highlights the scale of disruption and reinforces the importance of fast containment and lateral movement detection.

---

## 🧠 Attack Flow

```text
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
```

---

## 🛠️ Techniques

- supply chain compromise
- credential dumping
- lateral movement (PsExec, WMIC)
- SMB exploitation
- worm-like propagation
- destructive payload execution

### 📸 Evidence 4 — Attribution

<div align="center">
  <img src="../images/Screenshot 2026-03-20 171513.png" width="500">
</div>

**Analysis:**  
Sandworm attribution indicates nation-state capability and coordinated execution. While attribution is not always necessary for first-response SOC work, it strengthens threat intelligence context and detection planning.

---

## 🛡️ Response (SOC Playbook)

### Containment
- isolate affected systems
- disable compromised accounts
- block suspicious SMB traffic

### Eradication
- remove malicious artifacts
- reimage affected systems
- patch vulnerable hosts

### Recovery
- restore from clean backups
- validate system integrity before reconnecting

### Prevention
- enforce least privilege
- disable SMBv1
- implement network segmentation
- improve centralized logging and correlation

---

## 🧩 MITRE ATT&CK

| Technique | Description |
|---|---|
| T1075 | Pass-the-Hash |
| T1021 | Remote Services |
| T1059 | Command Execution |
| T1486 | Data Encryption for Impact |

---

## 📊 Key Takeaways

- lateral movement was the primary force multiplier
- trusted systems can be weaponized
- credential abuse accelerates compromise
- visibility across logs and traffic is critical

---

## 💡 Skills Demonstrated

- malware analysis
- lateral movement investigation
- SIEM detection logic
- network analysis
- SOC methodology

---

<div align="center">

🦠 **NotPetya was not ransomware — it was destruction**  
🔄 **Lateral movement enabled global impact**  
🔐 **Trust can be weaponized**

</div>
