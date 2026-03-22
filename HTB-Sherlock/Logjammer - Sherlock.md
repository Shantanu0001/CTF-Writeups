
# Theory part:- 
---


### `Folder and files` 
##### 1. PowerShell-Operational.evtx

###### Location:

`Applications and Services Logs > Microsoft > Windows > PowerShell > Operational`

#####  Key Event IDs:

|Event ID|Description|
|---|---|
|4103|Module logging – Logs functions and scripts used.|
|4104|Script Block Logging – Logs full content of executed script blocks.|
|4105|Pipeline execution start.|
|4106|Execution completion (or exception).|

##### Forensic Value:

- **Logs every executed PowerShell script**, including obfuscated and base64-encoded commands.
- Captures scripts downloaded from the internet (via IEX or `Invoke-WebRequest`).
- **Script origin**, command-line arguments, and user context (who ran it).

##### Use Case:

- **Detect fileless malware** using PowerShell.
- Detect **reconnaissance or credential theft** tools like `Invoke-Mimikatz`, `PowerView`, `PowerSploit`, etc.
- Detect **living-off-the-land** techniques (e.g., `Get-WmiObject`, `net user`, etc.).

#####  Correlation:

- Combine with **4688** (Process Creation) from `Security.evtx` to know **how PowerShell was launched**.
- Combine with `Windows Defender` or **Sysmon** logs for full visibility.

##### 2. Security.evtx

##### Location:

`Windows Logs > Security`

##### Key Event IDs:

|Event ID|Description|
|---|---|
|4624|Successful logon|
|4625|Failed logon|
|4634|Logoff|
|4648|Logon with explicit credentials|
|4672|Admin logon (special privileges assigned)|
|4688|Process creation (command line args)|
|4720|New user created|
|4722|User enabled|
|4723–4725|Password changed / reset|
|4732–4733|User added/removed from group|
|5140|Shared object accessed|

##### Forensic Value:

- Understand **who logged in, from where, and when**.
- Detect **brute force attacks**, **privilege escalation**, **pass-the-hash**, and **logon anomalies**.
- Capture process creation and parent/child processes.

##### Use Case:

- Identify a **suspicious user** (e.g., SYSTEM or unknown user) logging in at odd hours.
- **Account compromise** (e.g., a user added to Admins group).
- **Persistence** (e.g., new local users created or services installed).

##### Correlation:

- Pair 4688 (process creation) with PowerShell Event 4104 (script block).
- Use 4624 logon Type to understand access method (e.g., RDP = Type 10).

#### 3. System.evtx

##### Location:

`Windows Logs > System`

##### Key Event Types:

|Source|Description|
|---|---|
|Service Control Manager|Service start/stop events|
|Kernel-Power|Unexpected shutdown/reboot|
|EventLog|Log cleared (Event ID 1102)|
|Time-Service|Time changes (can be used to hide tracks)|
|DriverFramework|Driver install/load|

##### Forensic Value:

- Understand **system reboots, driver installations**, or **crash dumps**.
- Detect **log tampering** or suspicious reboots (e.g., log cleared before/after).
- See if a malicious driver was installed for persistence/rootkit.

##### Use Case:

- **Blue screen (BSOD)** after malware install? Check for faulty/malicious drivers.
- APTs often load **kernel-level malware** (detected here).
- Detect **new service creation** or manipulation of legit services (e.g., changing startup types).

##### Correlation:

- Match unexpected reboot times with **4624 logon** events.
- Use service names to correlate with known malware (e.g., using `sc.exe` or malicious `.dll` injections).

#### 4. Windows Defender-Operational.evtx

##### Location:

`Applications and Services Logs > Microsoft > Windows > Windows Defender > Operational`

##### Key Event IDs:

|Event ID|Description|
|---|---|
|1000|Malware detected|
|1116|Detected item|
|1117|Action taken (quarantine/remove)|
|2000|Real-time protection status|
|3002|Signature update success/failure|

##### Forensic Value:

- Show if **real-time protection** was disabled (manual or malware).
- Show exact **malware names, paths, and files** involved.
- Track whether malware was **quarantined**, **removed**, or **ignored**.

##### Use Case:

- Detect **ransomware or malware outbreaks**.
- Investigate if an alert was **ignored or user allowed it**.
- Confirm **persistence tactics** (e.g., same malware repeatedly detected in temp directories).

##### Correlation:

- Correlate timestamps with PowerShell and Security logs.
- If malware is detected right after a PowerShell script runs, connect the dots.


#### **5. Windows Firewall-Firewall.evtx**

##### Location:

`Applications and Services Logs > Microsoft > Windows > Windows Firewall With Advanced Security > Firewall`

##### Key Event Types:

|Event ID|Description|
|---|---|
|2004|Firewall rule modification|
|2033|Inbound connection allowed/blocked|
|2031|Outbound connection allowed/blocked|
|2041|Packet dropped|
|2002|Firewall service status changed|

##### Forensic Value:

- See **when rules were changed** (backdoor added via firewall).
- See **blocked connections** from IPs (scan attempts, malware C2).
- Understand **network behavior** of malware (sending data externally).

##### Use Case:

- Detect **exfiltration** or **command and control (C2)** traffic.
- Detect **unauthorized rule changes** (e.g., allow traffic on non-standard ports).
- Detect **stealthy lateral movement** or suspicious open ports.

##### Correlation:

- Cross-check allowed outbound connection with a suspicious PowerShell script contacting a domain.
- If firewall was disabled, correlate it with Event ID 2002 and Security logs for privilege abuse.

#### Example Attack Scenario – PowerShell Backdoor

Let’s say an attacker uses PowerShell to drop a reverse shell.

### Step-by-Step Detection:

| Stage            | Event                                                          | Source                              |
| ---------------- | -------------------------------------------------------------- | ----------------------------------- |
| Initial Access   | 4624 logon (Type 10) from external IP                          | `Security.evtx`                     |
| Execution        | 4688: `powershell.exe -enc <base64>`                           | `Security.evtx`                     |
| Script Logging   | 4104: Reveals `IEX(New-Object Net.WebClient).DownloadString()` | `PowerShell-Operational.evtx`       |
| AV Detection     | 1116: "Backdoor:PS/Empire.A" detected                          | `Windows Defender-Operational.evtx` |
| Firewall Evasion | 2004: Added firewall rule to allow 4444                        | `Firewall.evtx`                     |
| Persistence      | 4720: New user created `backdoorAdmin`                         | `Security.evtx`                     |

---

1) What is audit logging?
	
	 https://www.datadoghq.com/knowledge-center/audit-logging/
	
2) What is Invoke-WebRequest?
	
	 https://www.truesec.com/hub/blog/invoke-webrequest-or-invoke-restmethod
	
3) What is IEX?
	
	 https://www.securonix.com/blog/hiding-the-powershell-execution-flow/
	
4) What is powerview?
	
	 https://www.hackingarticles.in/active-directory-enumeration-powerview/
	 

### What is a Channel?

A **Channel** is essentially a **category or log container** that organizes Windows events based on their source, type, or purpose.

Think of it as a **"folder" or "stream"** where specific kinds of events are recorded.


###### Why Channels Matter

- Channels help **filter** and **focus** analysis.
- Tools like **Event Viewer**, **EvtxECmd**, and **Timeline Explorer** use Channels to display or filter logs.
- **Forensics investigators** often focus on certain channels depending on the investigation (e.g., Security for intrusion, PowerShell for scripts, etc.).

###### Channel vs Provider

- **Channel** = Where the log is written (like "Security" or "System").
    
- **Provider** = Who wrote the log (like `Microsoft-Windows-EventLog`).


---

# Steps to solve:-
---

1) **Download the file :** curl -L -o Logjammer.zip "https://labs.hackthebox.com/api/v4/challenges/557/cdn/..."

2) **Unzip the file:** sudo 7z x Logjammer.zip
	 There is folder called Event-Logs.
	 In that folder, there are five files with evtx extension (Windows XML Event Log file), namely
	- Powershell-Operational.evtx   
	- Security.evtx   
	- System.evtx  
	- 'Windows Defender-Operational.evtx'  
	- 'Windows Firewall-Firewall.evtx'



### 1) When did the cyberjunkie user first successfully log into his computer? (UTC)

- Open the event viewer, then open the Security.evtx because we know that all logging details are saved in Security.evtx file.
- To find the successful logon filter `Event ID with 4624` and by analyzing from the filtered list we get `TargetUserName` `CyberJunkie`.

![[Pasted image 20250531100951.png]]

So, in System section you will find the TimeCreated , in that SystemTime is mentioned and that is our answer.

![[Pasted image 20250531101221.png]]
#### Answer :- 27/03/2023 14:37:09



### 2) The user tampered with firewall settings on the system. Analyze the firewall event logs to find out the Name of the firewall rule added?

- Now open and use `Timeline Explorer.exe`, and open the `Windows Firewall-Firewall.evtx` because any modification done in the firewall is logged here.
- Once opened filter it with `Event ID 2004` , there in Remote Host column you see `4444` (Port 4444 is used by tools like Metasploit for reverse shell connections. It has some other applications for remote administration and communication, like SAP App Servers). 
- So look into that row and in the `Payload` column you will find the rule. 

#### Answer :- Metasploit C2 Bypass


### 3) What's the direction of the firewall rule?

- In the Direction column of the above row you will find the direction of the firewall rule.

#### Answer :- Outbound


### 4) The user changed audit policy of the computer. What's the Subcategory of this changed policy?

- For, this use Event Viewer, and open the `Security.evtx` (because, It records all security-relevant events like logons, policy changes, privilege use, and audit modifications — making it the primary source for detecting attacks, policy tampering, and suspicious user activity in Windows systems. ).
- Now, filter `Event ID with 4719` , in audit log you will find the sub-category.

#### Answer :- Other Object Access Events


### 5) The user "cyberjunkie" created a scheduled task. What's the name of this task?

- Now, in `Security.evtx` filter `Event ID with 4698`, you will get one log which contain the name of scheduled task created by cyberjunkie.

![[Pasted image 20250601122042.png]]

#### Answer :- HTB-AUTOMATION


### 6) What's the full path of the file which was scheduled for the task?

- In the same, you will also find the full path of the file which was scheduled for the task.

#### Answer :- C:\Users\CyberJunkie\Desktop\Automation-HTB.ps1


### 7) What are the arguments of the command?

- In the same, you will also find the argument of the command.

#### Answer :- -A cyberjunkie@hackthebox.eu


### 8) The antivirus running on the system identified a threat and performed actions on it. Which tool was identified as malware by antivirus?

- In `Windows Defender-Operational.evtx` filter `Event ID with 1117`, in the first log you will find the threat that was detected by the antivirus.
![[Pasted image 20250601124442.png]]

#### Answer :- Sharphound


### 9) What's the full path of the malware which raised the alert?

- In the same you will find the full path of the malware which raised the alert.

#### Answer :- C:\Users\CyberJunkie\Downloads\SharpHound-v1.1.0.zip


### 10) What action was taken by the antivirus?

- In the same you will find the action that was taken by the antivirus.

#### Answer :- Quarantine


### 11) The user used Powershell to execute commands. What command was executed by the user?

- In `Powershell-Operational.evtx`  filter `Event ID with 4104` , in the second log you will find the command that was executed by the user through PowerShell.
![[Pasted image 20250601125604.png]]

#### Answer :- Get-FileHash -Algorithm md5 .\Desktop\Automation-HTB.ps1


### 12) We suspect the user deleted some event logs. Which Event log file was cleared?

- Firstly i checked in the `Security.evtx` filter `Event ID with 1102` , in that one log was found but in there, i didn't find any relevant information or correct answer.
- So, In the `System.evtx` filter `Event ID with 104` , in the first log you will find the event log file that was cleared, because the firewall log was cleared not the security log.

![[Pasted image 20250601140208.png]]

#### Answer :- Microsoft-Windows-Windows Firewall With Advanced Security/Firewall

