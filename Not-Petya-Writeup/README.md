<div align="center">

# 🦠 NotPetya  
## Global Destructive Malware & Lateral Movement Investigation

![Category](https://img.shields.io/badge/Category-Malware%20Analysis-red?style=for-the-badge)
![Focus](https://img.shields.io/badge/Focus-Lateral%20Movement-blue?style=for-the-badge)
![Impact](https://img.shields.io/badge/Impact-Destructive%20Wiper-black?style=for-the-badge)

</div>

---

### 🎯 Objective

Investigate the NotPetya cyberattack to understand how a targeted supply chain compromise escalated into a **global destructive event**.

The goal was to analyze:

- how initial access was achieved  
- how the malware propagated across networks  
- what techniques enabled rapid lateral movement  
- why the attack caused widespread system destruction  

This investigation focuses on **real-world attack behavior, propagation mechanisms, and defensive gaps**.

---

### 🖥 Environment

| Tool | Purpose |
|-----|------|
| Windows enterprise systems | Target environment |
| SMB (Server Message Block) | Lateral movement vector |
| Mimikatz | Credential dumping |
| PsExec / WMIC | Remote execution |
| EternalBlue / EternalRomance | Exploitation |
| SIEM / Logs (simulated) | Detection analysis |

---

### 📦 Step 1 — Initial Access (Supply Chain Compromise)

The attack originated from a compromised update server for **M.E.Doc**, a widely used Ukrainian accounting software. :contentReference[oaicite:0]{index=0}  

Victims installed what appeared to be a legitimate software update.

Instead, the update delivered a malicious payload that executed within a trusted context.

---

#### 🔎 Analytical Observation

This bypassed traditional defenses because:

- the software source was trusted  
- no phishing or user interaction was required  
- execution occurred as part of normal operations  

This represents a **supply chain attack**, where trust is exploited instead of broken.

---

### 🔍 Step 2 — Establish Foothold

Once executed on the victim machine:

- the malware began internal system enumeration  
- prepared for credential harvesting  
- operated without immediate visible impact  

At this stage, detection opportunities were minimal.

---

### 🧪 Step 3 — Credential Dumping

The malware used **Mimikatz** to extract credentials from memory (LSASS).

This allowed the attacker to:

- obtain administrative credentials  
- authenticate legitimately across systems  
- avoid reliance on exploits alone  

---

#### 🔎 Analytical Observation

Credential-based movement is highly effective because:

- it blends in with normal admin behavior  
- it bypasses many security controls  
- it enables rapid privilege escalation  

---

### 🔄 Step 4 — Lateral Movement

NotPetya used a **dual propagation strategy**:

#### 🔹 Legitimate Tools
- PsExec  
- WMIC  

#### 🔹 Exploits
- EternalBlue  
- EternalRomance  

These techniques allowed the malware to:

- spread using valid credentials  
- exploit unpatched systems  
- move across entire networks rapidly  

---

### 🔎 Analytical Observation

Combining **credential reuse + exploitation** ensured:

- maximum spread  
- resilience against partial defenses  
- minimal dependency on a single attack path  

This is a key reason the attack escalated so quickly.

---

### 🌐 Step 5 — Network-Wide Propagation

After gaining access:

- the malware scanned for additional hosts  
- initiated SMB connections across the network  
- executed remotely on discovered systems  

This behavior resembled a **worm**, requiring no further user interaction.

---

### 💣 Step 6 — Destruction Phase

Once propagation was complete:

- the Master Boot Record (MBR) was overwritten  
- files were encrypted without recovery capability  
- systems became permanently unusable  

---

#### 🔎 Analytical Observation

Although presented as ransomware, NotPetya:

- had no functional recovery mechanism  
- was not designed for financial gain  
- prioritized **destruction over persistence**

This classifies it as a **wiper malware**.

---

### 🔐 Step 7 — Confirm Impact

The attack resulted in:

- rapid enterprise-wide outages  
- irreversible data loss  
- global operational disruption  

📸 **Example Impact (Conceptual)**  
Systems became inaccessible after reboot due to MBR overwrite.

---

## 🧠 Methodology Framework Applied


Supply chain compromise
↓
Trusted execution
↓
Credential harvesting
↓
Lateral movement (tools + exploits)
↓
Network-wide propagation
↓
Destructive payload execution


---

## 🛠 Techniques Used

Primary techniques observed:

- supply chain compromise  
- credential dumping (Mimikatz)  
- lateral movement (PsExec, WMIC)  
- SMB exploitation (EternalBlue)  
- worm-like propagation  
- destructive payload execution  

Key concept investigated:


Lateral movement as a force multiplier in cyber attacks


---

## 🛡 Defensive Insight

This attack highlights that:

- **initial access is not the main risk — lateral movement is**
- trusted systems can be weaponized against the organization  
- credential security is critical to limiting blast radius  

To mitigate similar attacks:

- enforce least privilege access  
- disable SMBv1 and patch vulnerabilities  
- restrict use of administrative tools  
- segment internal networks  
- monitor for abnormal lateral movement patterns  

Organizations must assume that once inside, attackers will attempt to **move laterally at scale**.

---

## 💡 Skills Reinforced

- malware behavior analysis  
- lateral movement investigation  
- credential abuse detection  
- enterprise attack chain analysis  
- understanding supply chain attack risks  

---

<div align="center">

🦠 NotPetya was not ransomware — it was destruction  
🔄 Lateral movement enabled global impact  
🔐 Trust in systems can be weaponized  

</div>
