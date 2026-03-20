🧠 NotPetya Cyberattack Analysis

Writeup by Shannon “Shae” Smith

📌 Scenario

This is a real-world analysis of the NotPetya cyberattack (2017) — widely considered the most destructive cyberattack in history.

Unlike typical TryHackMe rooms, this isn’t a lab — but I approached it the same way:

Break down the attack

Identify techniques

Understand impact

Think like a SOC analyst

🎯 Objective

Understand how NotPetya worked

Identify attacker techniques

Map behavior to real-world detection

Translate this into SOC-relevant skills

🧭 Task 1 – What is NotPetya?
❓ Question

What type of malware is NotPetya?

🧠 Answer
Wiper Malware (Disguised as Ransomware)
💡 Explanation

At first glance, NotPetya looked like ransomware — it displayed a ransom note and demanded payment.

But here’s the key:

👉 There was no recovery mechanism

Once systems were encrypted, they were permanently destroyed. That makes this a wiper, not ransomware.

📸 Screenshot (Ransom Screen)
[INSERT: NotPetya ransom note screenshot]
👤 Task 2 – Threat Actor
❓ Question

Who was responsible for the NotPetya attack?

🧠 Answer
Sandworm Team (GRU Unit 74455)
💡 Explanation

This attack was attributed to a Russian state-sponsored group known as Sandworm.

They’ve also been linked to:

Industroyer

Olympic Destroyer

KillDisk

👉 This is not cybercrime — this is cyber warfare

🌍 Task 3 – Scope of Impact
❓ Question

How widespread was the attack?

🧠 Answer
64 countries
💡 Explanation

Even though Ukraine was the intended target, the worm spread globally.

🏢 Notable victims:

Maersk

FedEx (TNT Express)

Merck

Mondelez

📸 Screenshot (Global Impact Map)
[INSERT: Map of affected countries]
⚙️ Task 4 – Initial Access
❓ Question

How did attackers initially gain access?

🧠 Answer
Compromised M.E. Doc software update
💡 Explanation

Attackers:

Breached the M.E. Doc update server

Injected malicious code

Delivered it as a legitimate update

👉 This is a supply chain attack — one of the most dangerous attack vectors today.

📸 Screenshot (Infection Flow)
[INSERT: Supply chain attack diagram]
🧪 Task 5 – Lateral Movement
❓ Question

What tools and exploits were used to spread the malware?

🧠 Answer
Mimikatz, PSExec, WMIC, EternalBlue, EternalRomance
💡 Explanation

Once inside a system, NotPetya moved fast:

🧠 Mimikatz → dumps credentials

🔁 PSExec / WMIC → remote execution

💥 EternalBlue / EternalRomance → SMB exploits

👉 This allowed worm-like propagation across the network

📸 Screenshot (Lateral Movement Example)
[INSERT: Diagram or log showing SMB spread]
🧬 Task 6 – MITRE ATT&CK Mapping
❓ Question

What techniques were used?

🧠 Answer
Credential Dumping, Lateral Movement, SMB Exploitation, Supply Chain Compromise
💡 Breakdown
Tactic	Technique
Initial Access	Supply Chain Compromise
Credential Access	Mimikatz
Lateral Movement	PSExec / WMIC
Exploitation	EternalBlue
Impact	Data Destruction
💥 Task 7 – Impact
❓ Question

What was the total damage caused?

🧠 Answer
~$10 Billion
💡 Explanation

This attack caused massive disruption:

Maersk: ~$300M loss

FedEx: ~$300M loss

Merck: Hundreds of millions

📸 Screenshot (Company Impact)
[INSERT: News headline or company loss chart]
🎯 Task 8 – Motivation
❓ Question

Why was NotPetya launched?

🧠 Answer
To destabilize Ukraine
💡 Explanation

The goal was not money — it was geopolitical impact:

Disrupt economy

Undermine trust

Target critical infrastructure

👉 Classic nation-state cyber warfare strategy

🧠 Task 9 – Lessons Learned
❓ Question

What can defenders learn from this attack?

🧠 Answer
Backups, patching, segmentation, monitoring
💡 Key Takeaways

🔐 Always verify third-party updates

💾 Maintain offline backups

🧱 Segment networks

⚡ Patch known vulnerabilities

👀 Monitor lateral movement

🧰 Task 10 – SOC Perspective
❓ Question

How would you detect this in a real environment?

🧠 Answer
Monitor SMB traffic, credential dumping, and remote execution tools
💡 Detection Ideas
🔍 Suspicious Activity:

Sudden spike in SMB traffic (port 445)

PSExec execution across multiple hosts

Credential dumping behavior

🧪 Example Splunk Query
index=windows_logs EventCode=4688 
| search process_name="psexec.exe" OR process_name="wmic.exe"
index=network_logs dest_port=445 
| stats count by src_ip
🏁 Final Thoughts

This wasn’t just malware — it was a weapon.

NotPetya showed:

How dangerous supply chain attacks are

How fast malware can spread internally

How cyber warfare impacts real-world businesses

💼 Skills Demonstrated

Threat Analysis

Malware Behavior Understanding

MITRE ATT&CK Mapping

Incident Impact Analysis

SOC Detection Thinking
