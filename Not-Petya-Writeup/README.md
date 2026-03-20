# 🧠 NotPetya Cyberattack Analysis
**Shannon | Threat Analysis | VT**

---

## 📌 Overview

👉 This writeup breaks down the **NotPetya cyberattack (2017)**, one of the most destructive cyberattacks we’ve seen.

This isn’t a typical TryHackMe room — this is a **real-world attack scenario**.

I approached it the same way I approach a CTF:

- Break down how the attack worked  
- Identify the techniques used  
- Understand the real-world impact  
- Think through it like a SOC analyst  

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

NotPetya looked like ransomware at first, but there was **no way to recover files**.

Once systems were encrypted:
- Data was gone  
- No recovery option  

👉 This tells us it wasn’t ransomware at all — it was a **wiper built to destroy systems**.

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

The attack has been linked to a **Russian state-sponsored threat group**.

They’re also known for:
- Industroyer  
- Olympic Destroyer  
- KillDisk  

👉 This puts the attack firmly in the category of **nation-state cyber warfare**

---

# 🌍 Task 3: Scope of Impact

### ❓ Question  
How widespread was the attack?

### 🧠 Answer  

64 countries


### 💡 Explanation  

Ukraine was the main target, but the malware didn’t stay contained — it spread globally.

### 🏢 Major victims:
- Maersk  
- FedEx (TNT Express)  
- Merck  
- Mondelez  

👉 This is a good example of how dangerous **wormable malware** really is.

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
1. Compromised the update server  
2. Injected malicious code  
3. Pushed it out as a legitimate update  

👉 This is a classic **supply chain attack**

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

Once the malware was inside the network:

- 🧠 Mimikatz → dumps credentials  
- 🔁 PSExec / WMIC → remote execution  
- 💥 EternalBlue / EternalRomance → SMB exploits  

👉 This allowed it to move **very quickly across systems**

---

### 📸 Screenshot

[INSERT: SMB propagation or lateral movement diagram]


---

# 🧬 Task 6: MITRE ATT&CK Mapping

### ❓ Question  
What techniques are we seeing here?

### 🧠 Answer  

Credential Dumping, Lateral Movement, SMB Exploitation, Supply Chain Compromise


### 💡 Explanation  

Here’s how this maps out:

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

This attack caused **major disruption across multiple industries**.

- Maersk: ~$300M  
- FedEx: ~$300M  
- Merck: Hundreds of millions  

👉 This is why NotPetya is considered the **most expensive cyberattack to date**

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

This wasn’t about money — the goal was geopolitical:

- Disrupt the economy  
- Undermine trust in government  
- Target infrastructure  

👉 This lines up with a **cyber warfare strategy**

---

# 🧠 Task 9: Lessons Learned

### ❓ Question  
What should we take away from this?

### 🧠 Answer  

Backups, patching, segmentation, monitoring


### 💡 Explanation  

- Don’t blindly trust third-party updates  
- Always have backups  
- Segment your network  
- Patch known vulnerabilities  
- Monitor for lateral movement  

---

# 🧰 Task 10: SOC Perspective

### ❓ Question  
How would we actually catch this in a SOC?

### 🧠 Answer  

Monitor SMB traffic, credential dumping, and remote execution tools


### 💡 Explanation  

Things I’d be looking for:

- Spike in SMB traffic (port 445)  
- PSExec activity across multiple hosts  
- Credential dumping behavior  

---

### 🧪 Example Splunk Queries

```spl
index=windows_logs EventCode=4688 
| search process_name="psexec.exe" OR process_name="wmic.exe"
index=network_logs dest_port=445 
| stats count by src_ip
🧪 Simulated SOC Logs
🔍 Suspicious Process Creation
EventCode: 4688
New Process Name: C:\Windows\System32\psexec.exe
Parent Process: cmd.exe
User: DOMAIN\AdminUser

👉 Likely lateral movement

🔍 Credential Dumping
Process Name: mimikatz.exe
Command: sekurlsa::logonpasswords

👉 Credentials are being harvested

🔍 SMB Traffic Spike
Source IP: 10.0.0.15
Connections: 1500+ on port 445

👉 Possible worm activity

🕵️ SOC Investigation Walkthrough
Step 1: Initial Alert

Unusual SMB traffic detected across multiple hosts

Step 2: Process Review

PSExec and WMIC activity identified

Step 3: Credential Access

Mimikatz detected → admin creds likely compromised

Step 4: Spread

Multiple systems infected rapidly

Step 5: Impact

Systems failing / unrecoverable

Step 6: Conclusion

Confirmed destructive malware (wiper)

🛡️ Blue Team Response Timeline
Time	Action
T+0	SMB spike detected
T+5	Investigation started
T+15	Lateral movement confirmed
T+30	Systems isolated
T+60	Incident declared critical
T+2h	Systems taken offline
T+24h	Recovery begins
🏁 Final Thoughts

This wasn’t just malware — this was basically a weaponized attack.

What this really shows:

How dangerous supply chain attacks are

How fast malware can spread internally

How much damage cyberattacks can actually cause

💼 Skills Demonstrated

Threat Analysis

Malware Behavior Analysis

MITRE ATT&CK Mapping

Incident Impact Assessment

SOC Detection Thinking
