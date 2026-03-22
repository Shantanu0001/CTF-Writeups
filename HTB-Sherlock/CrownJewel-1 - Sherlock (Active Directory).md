

# Theory part:- 
---

1) What is domain controller? How it is differ from Active Directory?
	
	 - https://www.solarwinds.com/resources/it-glossary/domain-controller
	 - https://jumpcloud.com/blog/domain-controller-vs-active-directory#:~:text=Active%20Directory%20is%20a%20database,that%20authenticates%20users%20and%20devices.
	
2) What is NTDS.dit?
	
	 - http://medium.com/@harikrishnanp006/understanding-ntds-dit-the-core-of-active-directory-faac54cc628a 
	
3) What is Wdigest?
	
	 - In detailed manner: [[Windows Digest Authentication Protocol (WDigest)]]
	
4) What is vssadmin?
	
	 - In detailed manner : [[Volume Shadow Copy Service (VSS)]]
	   
5) What is Living Off the Land Binaries (LOLBIN)?
	
	 - https://lolbas-project.github.io/  --> Official website
	 - https://socprime.com/blog/what-are-lolbins/
	 - https://www.securityhq.com/blog/security-101-lolbins-malware-exploitation/
	 - https://techzone.bitdefender.com/en/tech-explainers/living-of-the-land-attacks.html
	
6) What is Local Security Authority Subsystem Service (LSASS)?
	
	 - https://en.wikipedia.org/wiki/Local_Security_Authority_Subsystem_Service
	

# Steps to solve:-
---

1) **Download the file :** curl -L -o CrownJewel-1.zip "https://labs.hackthebox.com/api/v4/challenges/751/cdn/...."


2) **Unzip the file:** sudo 7z x CrownJewel-1.zip
	
	 There are one directory namely Artifacts, in that there are three file namely,
	 
	- Microsoft-Windows-NTFS.evtx
	- SECURITY.evtx
	- SYSTEM.evtx
	  
	 and one directory namely C .
	 



### 1) Attackers can abuse the vssadmin utility to create volume shadow snapshots and then extract sensitive files like NTDS.dit to bypass security mechanisms. Identify the time when the Volume Shadow Copy service entered a running state.


- So create csv by `C:\Tools\EvtxECmd\EvtxeCmd>EvtxECmd.exe -f "C:\Users\IEUser\HTB\CrownJewel-1\Artifacts\SYSTEM.evtx" --csv "C:\Users\IEUser\HTB\CrownJewel-1\Output"` .
- Filter `Event ID with 7036` , look at the Payload Data1 column , in that you will notice Volume Shadow is logged. So in the same row check the Time created column in that you will find the time when the Volume Shadow Copy service entered a running state.

#### Answer :- 2024-05-14 03:42:16


### 2) When a volume shadow snapshot is created, the Volume shadow copy service validates the privileges using the Machine account and enumerates User groups. Find the two user groups the volume shadow copy process queries and the machine account that did it.

-  Open Security log and filter for event id 4799 *(Event ID **4799** is generated when a process queries the group memberships of a user or machine account. This typically happens during: Logon, Service execution (like `vssadmin`),* Scheduled task execution ,when SYSTEM or machine account validates permissions), since we want to look events related to enumeration of local groups and users groups. Keep in mind the timeframe from previous question and look for the events around that time.
- We see lots of consecutive events. If we look at details we can see the process responsible for all of these is vssvc.exe which is what we are looking for.
- We find 2 groups are enumerated by the account DC01$.

#### Answer :- Administrators, Backup Operators, DC01$


### 3) Identify the Process ID (in Decimal) of the volume shadow copy service process.

-  In the same sets of events, look for process id of the vss process and convert it into decimal.

#### Answer :- 4496


### 4) Find the assigned Volume ID/GUID value to the Shadow copy snapshot when it was mounted.


#### Answer :- {06c4a997-cca8-11ed-a90f-000c295644f9}


### 5) Identify the full path of the dumped NTDS database on disk.


#### Answer :- C:\Users\Administrator\Documents\backup_sync_Dc\Ntds.dit


### 6) When was newly dumped ntds.dit created on disk?

-  In the same look at the Created0x10 column , you will find the newly dumped ntds.dit created on disk.

#### Answer :- 2024-05-14 03:44:22


### 7) A registry hive was also dumped alongside the NTDS database. Which registry hive was dumped and what is its file size in bytes?

-  In the same look at the File name and File size column, you will find the registry hive that was dumped and its size.

#### Answer :- SYSTEM, 17563648


















- So now convert into csv by `C:\Tools\MFTECmd>MFTECmd.exe -f "C:\Users\IEUser\HTB\CrownJewel-1\Artifacts\C\$MFT" --csv "C:\Users\IEUser\HTB\CrownJewel-1\Output"` .



