## 🦠 NotPetya
### Global Destructive Malware & SOC Investigation Analysis

[![Category](https://img.shields.io/badge/Category-Incident%20Response-red?style=for-the-badge)](https://img.shields.io/badge/Category-Incident%20Response-red?style=for-the-badge) [![Focus](https://img.shields.io/badge/Focus-Lateral%20Movement-blue?style=for-the-badge)](https://img.shields.io/badge/Focus-Lateral%20Movement-blue?style=for-the-badge) [![Impact](https://img.shields.io/badge/Impact-Destructive%20Wiper-black?style=for-the-badge)](https://img.shields.io/badge/Impact-Destructive%20Wiper-black?style=for-the-badge)

---

[![](https://github.com/shannonasmith/Cyber-Writups-VT/raw/main/images/Screenshot%202026-03-20%20171726.png)](https://github.com/shannonasmith/Cyber-Writups-VT/blob/main/images/Screenshot%202026-03-20%20171726.png)


*Figure 1. High-level overview of the NotPetya campaign used to frame this investigation.*

---

## 🧠 Scenario

NotPetya was a 2017 destructive malware campaign attributed to Sandworm, a Russian GRU-linked threat group. It was designed to cause maximum operational damage to Ukrainian infrastructure but spread globally due to its worm-like propagation, ultimately causing an estimated $10 billion in damage across shipping, pharmaceutical, and energy sectors.

This investigation approaches NotPetya from a **Security Operations Center (SOC)** perspective — not just what it did, but where detection was possible, what the attack looked like from inside a network, and what response should have looked like at each stage.

---

## 🎯 Objective

Investigate how a targeted supply chain compromise against a single Ukrainian accounting software vendor escalated into a **global destructive event** affecting organizations with no connection to Ukraine.

Focus areas:

- how trusted software delivery was weaponized for initial access
- how credential harvesting enabled rapid lateral movement without exploiting new vulnerabilities
- how EternalBlue exploitation accelerated propagation to unpatched systems
- how a wiper payload disguised as ransomware prevented recovery

---

## 🚨 Detection Perspective (SOC View)

From a SOC standpoint, NotPetya would present as a pattern of events that individually look like routine administrative activity — which is exactly what made it so effective. The attack chain:

- exploited a **trusted update mechanism**, so initial execution appeared legitimate
- used **credential reuse** rather than new exploits where possible, making lateral movement look like authorized admin access
- leveraged **built-in Windows tools** (PsExec, WMIC) that wouldn't trigger many AV signatures

Detection required visibility across authentication logs, SMB traffic, and service creation events simultaneously — not just endpoint alerting.

---

### 📊 Detection Logic (Splunk)

```splunk
index=windows EventCode=4624 OR EventCode=4625
| stats count by user, src_ip
| where count > 10
```
➡️ Surfaces abnormal authentication volume — brute force patterns or credential spray from a single source IP

```splunk
index=windows EventCode=7045
| where Service_Name IN ("PSEXESVC", "wmic")
```
➡️ Detects suspicious service creation associated with PsExec-based lateral movement — a high-fidelity indicator when seen alongside authentication failures on the same host

---

## 🖥️ Environment

| Tool | Purpose |
|------|---------|
| Windows enterprise systems | Target environment |
| SMB (ports 445/139) | Lateral movement and propagation vector |
| Mimikatz | Credential dumping from LSASS |
| PsExec / WMIC | Remote execution for lateral movement |
| EternalBlue / EternalRomance | SMB exploitation for systems without MS17-010 patch |
| M.E.Doc update server | Supply chain compromise delivery mechanism |
| SIEM | Detection analysis |

---

## 📦 Step 1 — Initial Access (Supply Chain Compromise)

A compromised update server for **M.E.Doc** — Ukrainian accounting software required by law for tax compliance — delivered the NotPetya payload as a legitimate software update. Victims installed the malware believing it was a routine patch.

This made initial access **invisible to traditional defenses**: the binary was signed, delivered through a trusted channel, and executed by the user with no phishing required. Organizations that validated software signatures or restricted update sources would have been protected; most didn't.

---

## 🔍 Step 2 — Foothold Establishment

Once executed, the malware operated quietly before triggering its destructive payload:

- performed internal network enumeration to identify reachable hosts
- extracted credentials from memory using Mimikatz-style LSASS dumping
- maintained a low profile to maximize propagation before detection

Detection visibility was minimal at this stage because the malware's enumeration behavior mimicked legitimate network discovery, and credential access via LSASS is a common technique that many organizations don't log or alert on by default.

---

## 🧪 Step 3 — Credential Dumping

NotPetya extracted credentials from memory using techniques equivalent to Mimikatz, targeting LSASS to obtain plaintext passwords and NTLM hashes for any accounts that had authenticated on the compromised system.

This enabled:

- credential reuse across systems without triggering account lockouts
- authentication using legitimate credentials, bypassing many detection controls
- targeting of domain admin accounts for maximum propagation reach

**Why this matters for defenders:** Organizations that enforce least privilege, credential tiering (keeping domain admin accounts off workstations), and Protected Users security groups significantly reduce the blast radius of this technique. If no domain admin credentials are cached on a workstation, a compromised workstation cannot become a domain controller compromise.

---

## 🔄 Step 4 — Lateral Movement

NotPetya spread using two complementary mechanisms depending on what it found:

**Credential-based movement (legitimate tools):**
- PsExec — remote execution using harvested credentials
- WMIC — Windows Management Instrumentation for remote command execution

**Exploit-based movement (unpatched systems):**
- EternalBlue (MS17-010) — SMB remote code execution
- EternalRomance — secondary SMB exploit for older Windows versions

[![](https://github.com/shannonasmith/Cyber-Writups-VT/raw/main/images/Screenshot%202026-03-20%20171621.png)](https://github.com/shannonasmith/Cyber-Writups-VT/blob/main/images/Screenshot%202026-03-20%20171621.png)

*Figure 2. Timeline showing coordinated Sandworm operations and the rapid execution of NotPetya across enterprise environments.*

The combination meant that even organizations that had patched EternalBlue were vulnerable if any system had cached domain credentials — and organizations that hadn't patched were doubly exposed. The dual propagation path is what turned a Ukrainian-targeted attack into a global incident.

---

## 🌐 Step 5 — Network Propagation

After lateral movement established footholds on additional hosts, the malware propagated worm-like across the network:

- scanned subnet ranges for live hosts via SMB
- attempted credential-based execution first, exploit-based second
- executed remotely across the network within minutes of initial compromise

### 📊 Network Detection (Zeek)

```bash
cat conn.log | zeek-cut id.orig_h id.resp_h service duration
```
➡️ Reveals abnormal volume of SMB connections from a single host in a short time window — worm propagation produces a distinct pattern of one-to-many connections that differs from normal server traffic

---

## 💣 Step 6 — Destructive Payload

Once propagation reached a threshold, the wiper payload triggered:

- **MBR (Master Boot Record) overwrite** — replaced the boot record with a custom bootloader that displays a fake ransom note on restart
- **MFT (Master File Table) encryption** — encrypted the file system table, making file recovery impossible even if the MBR were restored
- No functioning decryption key existed — the ransom demand was theater

This is what distinguishes NotPetya from ransomware: there was no recovery path by design. The ransom note was intended to delay incident response by making organizations treat it as a ransomware event rather than initiating immediate disaster recovery.

---

## 🔐 Step 7 — Impact & Lessons

[![](https://github.com/shannonasmith/Cyber-Writups-VT/raw/main/images/Screenshot%202026-03-20%20171349.png)](https://github.com/shannonasmith/Cyber-Writups-VT/blob/main/images/Screenshot%202026-03-20%20171349.png)

*Figure 3. Global impact illustrating the scale and economic damage — shipping giant Maersk lost approximately $300M; pharmaceutical company Merck lost approximately $870M.*

The collateral damage to multinational organizations operating in Ukraine demonstrated that **supply chain attacks can cascade globally with no connection to the intended target**. Organizations that shared network connectivity or software dependencies with Ukrainian entities were exposed regardless of their own security posture.

---

## 🧠 Attack Flow

```
Supply chain compromise (M.E.Doc update)
    ↓
Trusted execution (no user action required)
    ↓
Credential harvesting (LSASS dumping)
    ↓
Lateral movement (PsExec/WMIC + EternalBlue/EternalRomance)
    ↓
Network-wide propagation (worm behavior via SMB)
    ↓
Destructive payload (MBR + MFT wipe)
    ↓
Permanent operational disruption
```

---

## 🛠️ MITRE ATT&CK Techniques

| Technique | ID | Phase |
|-----------|-----|-------|
| Supply Chain Compromise | T1195.002 | Initial Access |
| OS Credential Dumping (LSASS) | T1003.001 | Credential Access |
| Lateral Tool Transfer | T1570 | Lateral Movement |
| Remote Services (PsExec) | T1021.002 | Lateral Movement |
| Exploit Public-Facing Application (EternalBlue) | T1190 | Lateral Movement |
| Disk Structure Wipe (MBR) | T1561.002 | Impact |

[![](https://github.com/shannonasmith/Cyber-Writups-VT/raw/main/images/Screenshot%202026-03-20%20171513.png)](https://github.com/shannonasmith/Cyber-Writups-VT/blob/main/images/Screenshot%202026-03-20%20171513.png)

*Figure 4. Strategic intent behind the attack — geopolitical disruption with economic collateral damage.*

---

## 🛡️ Response (SOC Playbook)

### Containment
- Immediately isolate affected systems from the network — do not wait for confirmation of wiper status
- Disable SMB (ports 445/139) at network boundaries to stop propagation
- Disable compromised accounts and revoke active sessions

### Eradication
- Reimage affected systems — MBR/MFT recovery is not reliable
- Rotate all credentials that may have been harvested (assume full domain compromise)
- Apply MS17-010 patch across all remaining systems

### Recovery
- Restore from offline backups validated before the compromise window
- Validate system integrity before reconnecting to the network

### Prevention
- Enforce software update validation and supply chain controls
- Deploy credential tiering — keep privileged accounts off workstations
- Disable SMBv1, enforce SMB signing
- Implement network segmentation to limit east-west movement
- Enable LSASS protection (Credential Guard, Protected Users group)

---

## 📊 Key Takeaways

- **NotPetya was not ransomware** — the ransom demand was a deception to delay incident response while destruction completed
- **Credential reuse is the force multiplier** — patching EternalBlue alone would not have stopped propagation against organizations with unhardened credential practices
- **Supply chain trust is an attack surface** — software update mechanisms must be treated as high-risk delivery paths
- **Lateral movement visibility is the critical detection window** — by the time the wiper triggers, it's too late

---

## 💡 Skills Demonstrated

- Attack chain reconstruction from a SOC investigation perspective
- MITRE ATT&CK technique mapping across a multi-phase campaign
- Detection logic development (Splunk SPL, Zeek) grounded in attacker behavior
- Supply chain and nation-state threat analysis
- Incident response playbook development

---

🦠 **NotPetya was destruction disguised as ransomware**
🔄 **Lateral movement drove global impact — not the initial exploit**
🔐 **Supply chain trust is an attack surface that must be actively defended**

← [Back to VT Writeups](../README.md)
