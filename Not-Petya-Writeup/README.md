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

<p align="center"><em>Figure 1. High-level overview of the NotPetya campaign used to frame this investigation.</em></p>

---

## 🧠 Scenario

NotPetya was a destructive malware campaign that spread rapidly across enterprise environments by abusing trusted software updates, legitimate administrative tools, and credential access.

This investigation approaches NotPetya from a **Security Operations Center (SOC)** perspective, focusing on:

- detection opportunities  
- lateral movement analysis  
- attack chain reconstruction  
- incident response considerations  

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

---

### 📊 Detection Logic (Splunk)

```spl
index=windows EventCode=4624 OR EventCode=4625
| stats count by user, src_ip
```

➡️ Detects abnormal authentication patterns  

```spl
index=windows EventCode=7045
```

➡️ Detects suspicious service creation associated with lateral movement  

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

---

## 🔍 Step 2 — Foothold

Once executed, the malware:

- performed internal enumeration  
- prepared for credential harvesting  
- maintained minimal visible impact  

➡️ Detection visibility was low at this stage  

---

## 🧪 Step 3 — Credential Dumping

NotPetya used **Mimikatz**-style credential theft to obtain administrative access.

This enabled:

- credential reuse across systems  
- legitimate authentication for lateral movement  
- rapid privilege escalation  

**Observation**

Credential abuse:

- blends with normal behavior  
- bypasses many controls  
- enables rapid spread  

---

## 🔄 Step 4 — Lateral Movement

NotPetya spread using both legitimate tools and exploit-based techniques.

### 🔹 Tools
- PsExec  
- WMIC  

### 🔹 Exploits
- EternalBlue  
- EternalRomance  

<p align="center"><em>Figure 2. Timeline showing coordinated Sandworm operations and rapid execution of NotPetya.</em></p>

<div align="center">
  <img src="images/Screenshot 2026-03-20 171621.png" width="500">
</div>

**Analysis:**  
The rapid progression shown here reinforces how credential reuse combined with exploitation enabled fast enterprise-wide spread.

---

## 🌐 Step 5 — Propagation

After gaining access, the malware:

- scanned for additional hosts  
- initiated SMB connections  
- executed remotely across the network  

### 📊 Network Perspective (Zeek)

```bash
cat conn.log | zeek-cut id.orig_h id.resp_h service
```

➡️ Reveals abnormal SMB traffic and scanning behavior  

➡️ Propagation resembled worm-like activity  

---

## 💣 Step 6 — Destruction

Once propagation was complete:

- MBR overwritten  
- files encrypted without recovery capability  
- systems became unusable  

➡️ Classified as **wiper malware**

---

## 🔐 Step 7 — Impact

- enterprise outages  
- irreversible data loss  
- global disruption  

<p align="center"><em>Figure 3. Global impact illustrating the scale and economic damage caused by NotPetya.</em></p>

<div align="center">
  <img src="../images/Screenshot 2026-03-20 171349.png" width="500">
</div>

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
- lateral movement  
- SMB exploitation  
- worm propagation  
- destructive payload  

<p align="center"><em>Figure 4. Strategic intent behind the attack.</em></p>

<div align="center">
  <img src="../images/Screenshot 2026-03-20 171513.png" width="500">
</div>

---

## 🛡️ Response (SOC Playbook)

### Containment
- isolate systems  
- disable accounts  
- block SMB  

### Eradication
- remove malware  
- reimage systems  
- patch vulnerabilities  

### Recovery
- restore backups  
- validate systems  

### Prevention
- least privilege  
- disable SMBv1  
- network segmentation  
- improve logging  

---

## 📊 Key Takeaways

- lateral movement drives impact  
- credential abuse accelerates compromise  
- visibility is critical  

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
