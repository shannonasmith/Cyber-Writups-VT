<div align="center">

# 🕷️ Wizard Spider (TrickBot)  
## Ransomware Threat Actor & Enterprise Attack Chain Analysis

![Category](https://img.shields.io/badge/Category-Threat%20Intelligence-purple?style=for-the-badge)
![Focus](https://img.shields.io/badge/Focus-Ransomware%20Operations-blue?style=for-the-badge)
![Framework](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-red?style=for-the-badge)

</div>

---

<div align="center">

<img src="../images/Screenshot 2026-03-20 171903.png" width="500">

</div>

---

### 🎯 Objective

Investigate the **Wizard Spider** threat group to understand how organized cybercriminal operations conduct large-scale ransomware attacks.

The goal was to analyze:

- attacker structure and motivation  
- tools, malware, and infrastructure used  
- tactics, techniques, and procedures (TTPs)  
- how attacks progress across enterprise environments  

This investigation focuses on **threat actor profiling and attack chain analysis**.

---

### 🖥 Environment

| Component | Purpose |
|----------|--------|
| Threat intelligence data | Adversary profiling |
| MITRE ATT&CK framework | TTP classification |
| Malware analysis (TrickBot, Ryuk, Conti) | Capability understanding |
| Windows enterprise environments | Target systems |
| Active Directory | Privilege escalation & movement |

---

### 📦 Step 1 — Identify the Threat Actor

Wizard Spider is a highly organized cybercriminal group associated with:

- TrickBot malware  
- Ryuk ransomware  
- Conti ransomware  

The group operates like a **business**, with structured operations and specialized roles.

---

📸 **Adversary Profile**

<img src="../images/Screenshot 2026-03-20 171911.png" width="500">

---

#### 🔎 Analytical Observation

Unlike opportunistic attackers, Wizard Spider:

- develops its own tools  
- maintains persistent infrastructure  
- targets high-value organizations  

This places them in the **advanced / organized threat actor category**.

---

### 🔍 Step 2 — Initial Access

The group commonly gains access through:

- phishing campaigns  
- malicious attachments  
- compromised credentials  

Once access is obtained, malware is deployed to establish persistence.

---

📸 **Socio-Political Context & Motivation**

<img src="../images/Screenshot 2026-03-20 171937.png" width="500">

---

#### 🔎 Analytical Observation

Initial access is often the **least sophisticated phase**, but it enables:

- foothold establishment  
- internal reconnaissance  
- privilege escalation  

The real danger begins after access is achieved.

---

### 🧪 Step 3 — Malware Deployment (TrickBot)

Wizard Spider deploys **TrickBot**, a modular banking trojan that enables:

- credential harvesting  
- system enumeration  
- persistence mechanisms  
- data exfiltration  

---

📸 **Capabilities & Malware Ecosystem**

<img src="../images/Screenshot 2026-03-20 171945.png" width="500">

---

#### 🔎 Analytical Observation

TrickBot is highly flexible because:

- it uses modular components  
- it adapts to different environments  
- it enables follow-on attacks  

This makes it ideal for long-term enterprise compromise.

---

### 🔄 Step 4 — Persistence & Privilege Escalation

Once inside the network, the attacker:

- establishes persistence via scheduled tasks and registry changes  
- escalates privileges using stolen credentials  
- prepares for lateral movement  

---

### 🌐 Step 5 — Lateral Movement

The attacker moves through the network using:

- PsExec  
- Remote Desktop Protocol (RDP)  
- Active Directory enumeration tools (AdFind, nltest)  

---

📸 **Infrastructure & Tooling**

<img src="../images/Screenshot 2026-03-20 171951.png" width="500">

---

#### 🔎 Analytical Observation

Lateral movement is critical because:

- it expands attacker control  
- increases impact potential  
- enables targeting of high-value systems  

---

### 🧠 Step 6 — Discovery & Data Collection

The attacker performs:

- network discovery  
- account enumeration  
- system mapping  

Sensitive data is identified for:

- exfiltration  
- potential double extortion  

---

📸 **Victim Profile & Targeting**

<img src="../images/Screenshot 2026-03-20 171921.png" width="500">

---

### 💣 Step 7 — Ransomware Deployment

Final stage involves deploying:

- **Ryuk** or **Conti ransomware**

This results in:

- file encryption  
- system disruption  
- ransom demands  

---

📸 **Global Victim Distribution**

<img src="../images/Screenshot 2026-03-20 171929.png" width="500">

---

#### 🔎 Analytical Observation

Wizard Spider often uses **double extortion**:

- encrypt data  
- threaten to leak stolen data  

This increases pressure on victims to pay.

---

## 🧠 Methodology Framework Applied


Initial access (phishing / credentials)
↓
Malware deployment (TrickBot)
↓
Persistence & privilege escalation
↓
Lateral movement across network
↓
Discovery & data exfiltration
↓
Ransomware deployment (Ryuk / Conti)
↓
Extortion & impact


---

📸 **MITRE ATT&CK Mapping (TTPs)**

<img src="../images/Screenshot 2026-03-20 171955.png" width="500">

---

## 🛠 Techniques Used

Primary techniques observed:

- phishing-based initial access  
- credential harvesting  
- modular malware deployment  
- persistence mechanisms  
- Active Directory enumeration  
- lateral movement (PsExec, RDP)  
- ransomware deployment  
- double extortion  

Key concept investigated:


Enterprise ransomware as a multi-stage attack operation


---

## 🛡 Defensive Insight

Wizard Spider demonstrates that modern ransomware is not a single event, but a **multi-phase attack chain**.

To defend against this:

- monitor for early indicators (phishing, credential use)  
- restrict lateral movement paths  
- enforce least privilege access  
- monitor Active Directory activity  
- detect abnormal administrative tool usage  
- implement strong backup strategies  

Organizations must focus on **breaking the attack chain early** rather than reacting at the ransomware stage.

---

## 💡 Skills Reinforced

- threat intelligence analysis  
- adversary profiling  
- MITRE ATT&CK mapping  
- ransomware attack lifecycle understanding  
- enterprise attack chain analysis  

---

<div align="center">

🕷️ Wizard Spider operates like a business  
🔄 Ransomware is a multi-stage attack  
🔐 Early detection is critical to defense  

</div>
