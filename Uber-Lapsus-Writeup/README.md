# 🚕 Uber Breach (Lapsus$)
## Identity-Based Attack & MFA Fatigue Exploitation Analysis

[![Category](https://img.shields.io/badge/Category-Incident%20Response-black?style=for-the-badge)](https://img.shields.io/badge/Category-Incident%20Response-black?style=for-the-badge) [![Focus](https://img.shields.io/badge/Focus-Identity%20Attack-blue?style=for-the-badge)](https://img.shields.io/badge/Focus-Identity%20Attack-blue?style=for-the-badge) [![Technique](https://img.shields.io/badge/Technique-MFA%20Fatigue-red?style=for-the-badge)](https://img.shields.io/badge/Technique-MFA%20Fatigue-red?style=for-the-badge)

---

[![](https://github.com/shannonasmith/Cyber-Writups-VT/raw/main/images/Screenshot%202026-03-20%20180116.png)](https://github.com/shannonasmith/Cyber-Writups-VT/blob/main/images/Screenshot%202026-03-20%20180116.png)

---

### 🎯 Objective

Investigate the 2022 Uber breach conducted by the Lapsus$ threat group to understand how **identity-based attacks bypass technical security controls through human manipulation** — without exploiting a single software vulnerability.

The goal was to analyze:

- how compromised credentials were obtained and operationalized
- how push-based MFA was defeated through social engineering rather than technical bypass
- how hardcoded credentials enabled rapid privilege escalation after initial access
- what the full access path looked like from contractor account to full enterprise visibility
- what detection and prevention controls would have interrupted the attack

This case is important because it represents a category of attack that becomes more common as organizations improve their technical defenses — when technical exploitation is hard, attackers go after people.

---

### 🖥 Environment

| Component | Purpose |
|-----------|---------|
| Contractor VPN account | Initial entry point — credential purchased from infostealer market |
| Duo MFA (push notifications) | Authentication control bypassed via fatigue attack |
| Active Directory | Privilege escalation path |
| Internal PowerShell scripts | Source of hardcoded credentials providing admin access |
| AWS, Google Workspace, vSphere | Cloud and virtualization platforms accessed post-compromise |
| Thycotic PAM | Privileged access management system accessed after escalation |
| SentinelOne, HackerOne, Slack | Internal security and collaboration tools accessed |

---

### 📦 Step 1 — Initial Access (Credential Purchase + MFA Fatigue)

The attacker obtained the contractor's VPN credentials from an **infostealer malware marketplace** — the credentials had been harvested by malware on the contractor's personal device and sold. This is a common initial access pattern for Lapsus$: credential purchase from underground markets rather than conducting their own phishing.

With valid credentials but no way past MFA, the attacker launched a **push bombing attack**:

- sent repeated Duo push authentication requests to the contractor's phone over an extended period
- the contractor eventually approved one — likely out of confusion or to stop the notifications
- the attacker successfully authenticated into the VPN

This technique requires no technical exploit and no malware. It defeats MFA entirely through behavioral manipulation of the user.

**Why push-based MFA is vulnerable here:** Standard push notifications show "Approve" or "Deny" but not the location, device, or context of the login attempt. A user who forgets they just got a request, or who assumes it's a system glitch, may approve without thinking. Number matching (requiring the user to enter a code shown on the login screen into the MFA app) closes this gap — the user must actively verify the request matches what's on their screen.

---

### 🎭 Step 2 — Social Engineering Layer

In parallel with the push bombing, the attacker also posed as Uber IT support — contacting the contractor via WhatsApp and explaining that the MFA pushes were part of a system update and asking them to approve. This dual-channel social engineering created a false sense of legitimacy for the authentication request.

This phase demonstrates:

- **Human trust is an attack vector with no technical patch** — awareness training and verification procedures are the only controls
- **Multi-channel social engineering** (automated push + human impersonation) is more effective than either alone
- **The absence of a callback verification procedure** — a legitimate IT team would require the user to call a known internal number to verify before approving any authentication they didn't initiate

---

### 🧪 Step 3 — Credential Discovery (Hardcoded Credentials)

Once inside the VPN, the attacker accessed an internal network share containing **PowerShell scripts used by the infrastructure team**. These scripts contained **hardcoded credentials** — specifically admin-level credentials embedded directly in the script bodies rather than retrieved from a secrets manager at runtime.

This single finding escalated the attacker from contractor-level VPN access to administrative access to privileged systems — without any additional exploitation.

**Why hardcoded credentials are a critical failure:**

- Secrets embedded in scripts get committed to version control and shared to network shares — significantly expanding exposure
- Rotating credentials requires finding and updating every script that contains them — so they rarely get rotated
- A single discovered script provides immediate high-privilege access with no further effort

The correct control is a secrets management system (HashiCorp Vault, AWS Secrets Manager, Azure Key Vault) where scripts retrieve credentials at runtime through an authenticated API call — the credential never appears in the script at all.

---

### 🔄 Step 4 — Lateral Movement & System Access

With admin credentials, the attacker moved rapidly across Uber's environment:

- **Thycotic** (Privileged Access Management) — accessed the PAM system itself, which contained credentials for other privileged systems
- **AWS** — cloud environment access
- **VMware vSphere** — virtualization infrastructure
- **SentinelOne** — endpoint detection platform (access to the security tooling creates the ability to suppress alerts)
- **HackerOne** — Uber's bug bounty program, exposing private vulnerability reports
- **Slack, Google Workspace** — internal communication and productivity platforms

[![](https://github.com/shannonasmith/Cyber-Writups-VT/raw/main/images/Screenshot%202026-03-20%20180128.png)](https://github.com/shannonasmith/Cyber-Writups-VT/blob/main/images/Screenshot%202026-03-20%20180128.png)

*Systems compromised and attack progression — from contractor VPN to full enterprise visibility in a single session.*

The PAM system access is particularly significant: by compromising Thycotic, the attacker potentially gained access to every credential the system managed — turning one credential discovery into many. This is why PAM systems must be treated as tier-0 infrastructure with the strictest access controls in the environment.

---

### 🧠 Step 5 — Internal Access & Operational Impact

With elevated privileges across cloud, virtualization, and security tooling, the attacker had effectively achieved full enterprise visibility:

- access to internal security tool configurations — knowing what's being monitored
- ability to view vulnerability reports in HackerOne — private, unremediated bugs
- posting in internal Slack channels announcing the breach
- screenshots of internal dashboards shared publicly

The decision to announce the breach publicly rather than maintain stealth is characteristic of Lapsus$, which is motivated by reputation and notoriety more than financial gain — a different threat model from ransomware groups, and one that requires different response prioritization.

---

### 🚨 Step 6 — Detection & Response Gap Analysis

[![](https://github.com/shannonasmith/Cyber-Writups-VT/raw/main/images/Screenshot%202026-03-20%20180254.png)](https://github.com/shannonasmith/Cyber-Writups-VT/blob/main/images/Screenshot%202026-03-20%20180254.png)

*Detection and monitoring gaps — the authentication anomalies that should have triggered alerts before the attacker reached internal systems.*

The attack exposed several specific detection failures:

- **MFA push volume was not monitored** — repeated push requests to a single user in a short window should auto-trigger an alert and temporarily lock the account
- **VPN authentication from an unusual location** was not flagged — geo-velocity checks (impossible travel) would have surfaced a contractor authenticating from an unexpected country
- **First-time access to sensitive internal shares** post-VPN login was not alerted on — a contractor account accessing PowerShell infrastructure scripts is anomalous behavior
- **Lateral movement to privileged systems** from a contractor-level account was not flagged — a contractor accessing Thycotic, vSphere, or SentinelOne should have triggered immediate review

---

### 🛡 Step 7 — Prevention & Mitigation

[![](https://github.com/shannonasmith/Cyber-Writups-VT/raw/main/images/Screenshot%202026-03-20%20180246.png)](https://github.com/shannonasmith/Cyber-Writups-VT/blob/main/images/Screenshot%202026-03-20%20180246.png)

*Preventative security controls — layered identity security controls that would have interrupted this attack at multiple stages.*

| Control | What It Prevents |
|---------|-----------------|
| Number matching MFA (FIDO2/hardware keys) | Push bombing — user must actively verify the request |
| MFA rate limiting | Blocks push bombing by limiting requests per time window |
| Impossible travel / geo-velocity alerts | Flags VPN authentication from unexpected locations |
| Secrets management (Vault, AWS Secrets Manager) | Eliminates hardcoded credentials as an escalation path |
| Least privilege for contractor accounts | Limits blast radius — contractors should not access infrastructure scripts |
| PAM access monitoring and alerting | Detects access to privileged credential stores |
| User behavior analytics (UEBA) | Flags anomalous access patterns post-authentication |

---

## 🧠 Attack Chain

```
Credentials purchased from infostealer marketplace
    ↓
Push bombing (repeated MFA requests) + WhatsApp social engineering
    ↓
Contractor VPN access obtained
    ↓
Hardcoded credentials discovered in internal PowerShell scripts
    ↓
Admin-level access to privileged systems
    ↓
PAM system compromise (Thycotic) → credential pivot
    ↓
Full enterprise access (AWS, vSphere, SentinelOne, HackerOne, Slack)
    ↓
Public announcement of breach
```

---

## 🛠 MITRE ATT&CK Techniques

| Technique | ID | Phase |
|-----------|-----|-------|
| Valid Accounts (contractor credentials) | T1078 | Initial Access |
| Multi-Factor Authentication Request Generation | T1621 | Credential Access |
| Social Engineering / Impersonation | T1656 | Initial Access |
| Credentials in Files (hardcoded) | T1552.001 | Credential Access |
| Abuse Elevation Control Mechanism | T1548 | Privilege Escalation |
| Remote Services (VPN, RDP) | T1021 | Lateral Movement |
| Account Discovery | T1087 | Discovery |

---

## 🛡 Defensive Insight

The Uber breach demonstrates that **identity is the attack surface that matters most in modern enterprise environments**. The attacker used no malware, no zero-days, and no novel techniques. Every step relied on:

- a credential that was already compromised (supply chain of the contractor's personal device)
- a human who approved a request they shouldn't have
- a configuration failure (hardcoded credentials) that most organizations have somewhere

The controls that matter most here are identity-focused: phishing-resistant MFA, behavioral analytics on authentication events, secrets management, and least privilege enforcement. Technical controls on the endpoint or network perimeter were irrelevant — the attacker entered through the front door with valid credentials.

---

## 💡 Skills Reinforced

- Identity-based attack chain analysis
- MFA bypass technique categorization and defensive countermeasures
- Credential management failure analysis and remediation
- Privilege escalation path mapping through enterprise systems
- Detection gap analysis against a social engineering intrusion

---

🚨 **Identity is the perimeter — not the network**
📱 **Push-based MFA is not phishing-resistant — number matching and hardware keys are**
🔐 **Hardcoded credentials are not a minor misconfiguration — they're a privilege escalation path waiting to be found**

← [Back to VT Writeups](../README.md)
