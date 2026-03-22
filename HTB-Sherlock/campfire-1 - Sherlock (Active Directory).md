

# Theory part:- 
---

1) What is Kerberos?
	
	 - https://www.fortinet.com/resources/cyberglossary/kerberos-authentication
	 
2)  What is Kerberos attack - `Kerberoasting`?
	
	 - https://www.crowdstrike.com/en-us/cybersecurity-101/cyberattacks/kerberoasting/
	
3) What is ticketing?
	 
	- In detailed : [[Ticketing]]
	- https://www.crowdstrike.com/en-us/cybersecurity-101/cyberattacks/golden-ticket-attack/
	- https://www.crowdstrike.com/en-us/cybersecurity-101/cyberattacks/silver-ticket-attack/
	- https://www.sentinelone.com/cybersecurity-101/threat-intelligence/what-are-pass-the-hash-pth-pass-the-ticket-ptt/

4) What is Service Principle Name (SPN)?

	
5) What is Domain Controller Security log?
	
	
# Steps to solve:-
---

1) **Download the file :** wget -O campfire-1.zip "https://labs.hackthebox.com/api/v4/challenges/737/cdn/...."


2) **Unzip the file:** sudo 7z x campire-1.zip
	
	 There is one directory namely `Triage`, inside that there are two more directory namely `Domain Controller` &  `Workstation`.
	 
	 In `Domain Controller` there is a file namely, `SECURITY-DC.evtx` ,
	 
	 and in `Workstation` there is another directory namely , `2024-05-21T033012_triage_asset` and inside that `C` directory is there.
	 

### 1) Analyzing Domain Controller Security Logs, can you confirm the date & time when the kerberoasting activity occurred?

-  Open the `Security.evtx` file from the Domain Controller using tools like **Event Viewer** .

- Now, Filter for Event ID `4769` — this event logs every **Kerberos service ticket request** (TGS-REQ), which is crucial for spotting Kerberoasting.

- Look for a user account (e.g., `alonzo.spire@FORELA.LOCAL`) making **multiple service ticket requests**, especially those **not ending with `$`** (non-computer/service accounts like `MSSQLService`).

- Check the Ticket Encryption Type — `0x17` (RC4-HMAC) is a red flag, as it's a weak encryption method commonly targeted in Kerberoasting.

![[Pasted image 20250605104529.png]]

- Note the Ticket Options — values like `0x40800000` are typical for Kerberoasting tools (they request renewable and forwardable tickets).

#### Answer :- 2024-05-21 03:18:09


### 2) What is the Service Name that was targeted?

- In the same place you will find the service name that was targeted.

#### Answer :- MSSQLService


### 3) It is really important to identify the Workstation from which this activity occurred. What is the IP Address of the workstation?

- In the same place you will find the workstation IP address from which this activity has occurred.

#### Answer :- 172.17.79.129


### 4) Now that we have identified the workstation, a triage including PowerShell logs and Prefetch files are provided to you for some deeper insights so we can understand how this activity occurred on the endpoint. What is the name of the file used to Enumerate Active directory objects and possibly find Kerberoastable accounts in the network?

- Open the `Powershell-Operational.evtx` file from the Workstation using tools like **Event Viewer** .

- Now, Filter Event ID with `4104` (here are the logs are of ID 4104), so by analyzing each `Event Data` you will find the name of the file which was used to Enumerate Active directory objects.

![[Pasted image 20250605132032.png]]


#### Answer :- powerview.ps1


### 5) When was this script executed?

- In the same , in `System Details` you will find when the script was executed.

![[Pasted image 20250605132413.png]]

#### Answer :- 2024-05-21 03:16:32


### 6) What is the full path of the tool used to perform the actual kerberoasting attack?

#### Answer :- C:\Users\Alonzo.spire\Downloads\Rubeus.exe


### 7) When was the tool executed to dump credentials?

#### Answer :- 2024-05-21 03:18:08