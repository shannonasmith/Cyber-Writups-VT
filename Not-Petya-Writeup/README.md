# 🧠 NotPetya Cyberattack Analysis
**Shannon Smith | Threat Analysis | VT**

---

## 📌 Overview

This writeup analyzes the **NotPetya cyberattack (2017)** — one of the most destructive cyberattacks in history.

Unlike a typical TryHackMe room, this is a real-world case study. I approached it the same way I approach CTFs:
- Break down the attack
- Identify techniques
- Understand impact
- Think like a SOC analyst

---

## 🎯 Objectives

- Understand how NotPetya worked  
- Identify attacker techniques  
- Analyze real-world impact  
- Apply SOC analyst thinking  

---

# 🧭 Task 1: What is NotPetya?

### ❓ Question  
What type of malware is NotPetya?

### 🧠 Answer  

Wiper Malware (Disguised as Ransomware)


### 💡 Explanation  

NotPetya appeared to be ransomware, but it had **no recovery mechanism**.

Once encrypted:
- Data could not be recovered  
- Payment did nothing  

👉 This confirms it was a **wiper**, designed for destruction.

---

### 📸 Screenshot

[INSERT: NotPetya ransom screen]


---

# 👤 Task 2: Threat Actor

### ❓ Question  
Who was responsible for the attack?

### 🧠 Answer  

Sandworm Team (GRU Unit 74455)


### 💡 Explanation  

The attack is attributed to a **Russian state-sponsored group**.

Known for:
- Industroyer  
- Olympic Destroyer  
- KillDisk  

👉 This classifies the attack as **nation-state cyber warfare**

---

# 🌍 Task 3: Scope of Impact

### ❓ Question  
How widespread was the attack?

### 🧠 Answer  

64 countries


### 💡 Explanation  

Although Ukraine was the primary target, the malware spread globally.

### 🏢 Major victims:
- Maersk  
- FedEx (TNT Express)  
- Merck  
- Mondelez  

👉 This shows how dangerous **wormable malware** can be.

---

### 📸 Screenshot

[INSERT: Global impact map]


---

# ⚙️ Task 4: Initial Access

### ❓ Question  
How did the attackers gain access?

### 🧠 Answer  

Compromised M.E. Doc software update


### 💡 Explanation  

Attackers:
1. Breached update servers  
2. Injected malicious code  
3. Delivered it as a trusted update  

👉 This is a **supply chain attack**

---

### 📸 Screenshot

[INSERT: Supply chain attack diagram]


---

# 🧪 Task 5: Lateral Movement

### ❓ Question  
What tools and exploits were used?

### 🧠 Answer  

Mimikatz, PSExec, WMIC, EternalBlue, EternalRomance


### 💡 Explanation  

Once inside the network:

- 🧠 Mimikatz → dumps credentials  
- 🔁 PSExec / WMIC → remote execution  
- 💥 EternalBlue → SMB exploit  

👉 Enabled **rapid lateral movement across systems**

---

### 📸 Screenshot

[INSERT: SMB propagation or lateral movement diagram]


---

# 🧬 Task 6: MITRE ATT&CK Mapping

### ❓ Question  
What techniques were used?

### 🧠 Answer  

Credential Dumping, Lateral Movement, SMB Exploitation, Supply Chain Compromise


### 💡 Breakdown  

| Tactic | Technique |
|------|--------|
| Initial Access | Supply Chain Compromise |
| Credential Access | Mimikatz |
| Lateral Movement | PSExec / WMIC |
| Exploitation | EternalBlue |
| Impact | Data Destruction |

---

# 💥 Task 7: Impact

### ❓ Question  
What was the total damage?

### 🧠 Answer  

~$10 Billion


### 💡 Explanation  

Major losses included:

- Maersk: ~$300M  
- FedEx: ~$300M  
- Merck: Hundreds of millions  

👉 This made NotPetya the **most expensive cyberattack in history**

---

### 📸 Screenshot

[INSERT: News headline or financial loss graphic]


---

# 🎯 Task 8: Motivation

### ❓ Question  
Why was this attack conducted?

### 🧠 Answer  

To destabilize Ukraine


### 💡 Explanation  

The goal was geopolitical:

- Disrupt economy  
- Undermine trust  
- Target infrastructure  

👉 This aligns with **cyber warfare strategy**

---

# 🧠 Task 9: Lessons Learned

### ❓ Question  
What are the key security takeaways?

### 🧠 Answer  

Backups, patching, segmentation, monitoring


### 💡 Explanation  

- 🔐 Verify third-party updates  
- 💾 Maintain backups  
- 🧱 Segment networks  
- ⚡ Patch vulnerabilities  
- 👀 Monitor lateral movement  

---

# 🧰 Task 10: SOC Perspective

### ❓ Question  
How would this be detected?

### 🧠 Answer  

Monitor SMB traffic, credential dumping, and remote execution tools


### 💡 Detection Ideas  

- Spike in SMB traffic (port 445)  
- PSExec activity across hosts  
- Credential dumping behavior  

---

### 🧪 Example Splunk Queries

```spl
index=windows_logs EventCode=4688 
| search process_name="psexec.exe" OR process_name="wmic.exe"
index=network_logs dest_port=445 
| stats count by src_ip
🏁 Final Thoughts

This wasn’t just malware — it was a weaponized cyberattack.

NotPetya demonstrated:

The danger of supply chain attacks

The speed of internal network spread

The real-world impact of cyber warfare

💼 Skills Demonstrated

Threat Analysis

Malware Behavior Analysis

MITRE ATT&CK Mapping

Incident Impact Assessment

SOC Detection Thinking
