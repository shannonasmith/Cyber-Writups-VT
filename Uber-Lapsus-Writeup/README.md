<div align="center">

# 🚕 Uber Breach (Lapsus$)  
## Identity-Based Attack & MFA Fatigue Exploitation Analysis

![Category](https://img.shields.io/badge/Category-Incident%20Response-black?style=for-the-badge)
![Focus](https://img.shields.io/badge/Focus-Identity%20Attack-blue?style=for-the-badge)
![Technique](https://img.shields.io/badge/Technique-MFA%20Fatigue-red?style=for-the-badge)

</div>

---

<div align="center">

<img src="../images/Screenshot 2026-03-20 180116.png" width="500">

</div>

---

### 🎯 Objective

Investigate the Uber breach conducted by the Lapsus$ threat group to understand how **identity-based attacks and social engineering techniques** were used to bypass security controls.

The goal was to analyze:

- how initial access was obtained  
- how MFA protections were bypassed  
- how attackers escalated privileges  
- how access spread across enterprise systems  

This investigation focuses on **identity security weaknesses and real-world attack chains**.

---

### 🖥 Environment

| Component | Purpose |
|----------|--------|
| VPN Access | Initial entry point |
| MFA (Duo) | Authentication control |
| Active Directory | Privilege escalation |
| PowerShell scripts | Credential exposure |
| Cloud platforms (AWS, GSuite) | Target systems |
| Internal tools (Slack, vSphere, SentinelOne) | Post-compromise access |

---

### 📦 Step 1 — Initial Access (MFA Fatigue Attack)

The attacker gained access through a **compromised contractor account**.

- Repeated MFA push notifications were sent to the target  
- The user eventually approved a request  
- The attacker successfully authenticated into the VPN  

---

#### 🔎 Analytical Observation

This is known as **MFA fatigue (push bombing)**:

- attackers overwhelm users with repeated prompts  
- users approve access out of frustration or confusion  
- no exploit required — purely behavioral  

👉 This demonstrates that **MFA alone is not sufficient without proper controls**

---

### 🎭 Step 2 — Social Engineering

After initial access:

- the attacker posed as internal IT support  
- convinced the user to approve authentication  
- maintained access through deception  

---

#### 🔎 Analytical Observation

This phase highlights:

- human trust as an attack vector  
- lack of user awareness training  
- absence of verification procedures  

👉 Social engineering + MFA fatigue = high success rate

---

### 🧪 Step 3 — Credential Discovery

Once inside the network:

- attacker accessed internal PowerShell scripts  
- discovered **hardcoded credentials**  
- obtained admin-level access to privileged systems  

---

#### 🔎 Analytical Observation

Hardcoded credentials create:

- immediate privilege escalation paths  
- persistent security exposure  
- easy lateral movement opportunities  

👉 This is a **critical configuration failure**

---

### 🔄 Step 4 — Lateral Movement & System Access

The attacker moved across multiple systems:

- Privileged Access Management (Thycotic)  
- AWS cloud environment  
- VMware vSphere  
- Endpoint detection systems (SentinelOne)  
- Internal collaboration tools (Slack, GSuite)  

---

📸 **Systems Compromised & Attack Progression**

<img src="../images/Screenshot 2026-03-20 180128.png" width="500">

---

#### 🔎 Analytical Observation

This stage demonstrates:

- rapid privilege escalation  
- broad access across infrastructure  
- ability to pivot between cloud and on-prem systems  

👉 Identity = full environment access

---

### 🧠 Step 5 — Internal Access & Impact

With elevated privileges, the attacker:

- accessed sensitive internal systems  
- viewed security tools and configurations  
- gained visibility into detection mechanisms  
- posted messages within internal platforms (Slack)  

---

#### 🔎 Analytical Observation

At this stage, the attacker had:

- operational visibility  
- administrative control  
- ability to disrupt or persist  

👉 This is effectively **full enterprise compromise**

---

### 🚨 Step 6 — Detection & Response Gaps

The attack revealed weaknesses in:

- MFA implementation (no rate limiting or number matching)  
- monitoring of authentication activity  
- detection of abnormal login behavior  
- credential management practices  

---

📸 **Detection & Monitoring Gaps**

<img src="../images/Screenshot 2026-03-20 180254.png" width="500">

---

#### 🔎 Analytical Observation

Detection failed because:

- authentication anomalies were not flagged early  
- excessive MFA requests were not treated as suspicious  
- credential exposure was not monitored  

👉 Early detection could have stopped the attack

---

### 🛡 Step 7 — Prevention & Mitigation

To prevent similar attacks:

- implement **number matching MFA**  
- enforce MFA request rate limiting  
- remove hardcoded credentials  
- enforce least privilege access  
- monitor authentication anomalies  
- restrict lateral movement paths  

---

📸 **Preventative Security Controls**

<img src="../images/Screenshot 2026-03-20 180246.png" width="500">

---

## 🧠 Methodology Framework Applied


Compromised credentials
↓
MFA fatigue attack
↓
Social engineering
↓
VPN access gained
↓
Credential discovery (PowerShell)
↓
Privilege escalation
↓
Lateral movement across systems
↓
Enterprise-wide access


---

## 🛠 Techniques Used

Primary techniques observed:

- MFA fatigue (push bombing)  
- social engineering  
- credential harvesting  
- hardcoded credential exploitation  
- privilege escalation  
- lateral movement  
- cloud and on-prem pivoting  

Key concept investigated:


Identity as the primary attack surface in modern environments


---

## 🛡 Defensive Insight

This attack demonstrates that:

- identity systems are the new perimeter  
- MFA can be bypassed through user manipulation  
- credential management is critical to security posture  

Organizations must:

- treat authentication systems as high-risk attack surfaces  
- monitor identity-based anomalies in real time  
- implement layered security controls beyond MFA  

---

## 💡 Skills Reinforced

- incident response analysis  
- identity-based attack investigation  
- MFA bypass techniques  
- privilege escalation analysis  
- enterprise attack chain mapping  

---

<div align="center">

🚨 Identity is the new perimeter  
📱 MFA can be manipulated  
🔐 Security must account for human behavior  

</div>
