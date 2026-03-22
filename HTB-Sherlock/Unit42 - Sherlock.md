

# Theory part:- 
---

1) What is Sysmon log?
	
	 -  https://www.manageengine.com/products/eventlog/logging-guide/what-is-sysmon.html
	 
	 - https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon --> in detailed (with Event ID explained)
	
2) What is Windows Management Instrument (WMI)?
	
	 -  https://redcanary.com/threat-detection-report/techniques/windows-management-instrumentation/
	 
	-  https://www.techtarget.com/searchwindowsserver/definition/Windows-Management-Instrumentation
	

# Steps to solve:-
---

1) **Download the file :** curl -o  Unit42.zip "https://challenges-cdn.hackthebox.com/sherlocks/very-easy/unit42.zip/..."

2) **Unzip the file:** sudo 7z x Unit42.zip
	
	 There is one file with evtx extension (Windows XML Event Log file)
	



### 1) How many Event logs are there with Event ID 11?

- Open the event viewer, then open the evtx file and filter with the Event ID 11.
- At above you will see the number of events.

#### Answer :- 56


### 2) Whenever a process is created in memory, an event with Event ID 1 is recorded with details such as command line, hashes, process path, parent process path, etc. This information is very useful for an analyst because it allows us to see all programs executed on a system, which means we can spot any malicious processes being executed. What is the malicious process that infected the victim's system?

- Now, filter with Event ID 1. So, after filtering we found that there are total 6 events.
- So, by analyzing each event we found the malicious process that infected the victim's system.

#### Answer :- C:\Users\CyberJunkie\Downloads\Preventivo24.02.14.exe.exe 


### 3) Which Cloud drive was used to distribute the malware?

- Now, filter with ID one by one 3 (Network connection), 11 (FileCreate), 15 (FileCreateStreamHash), 23 (File Delete archived). 
- In ID 15, we got the the name of cloud drive.

#### Answer :- dropbox


### 4) For many of the files it wrote to disk, the initial malicious file used a defense evasion technique called Time Stomping, where the file creation date is changed to make it appear older and blend in with other files. What was the timestamp changed to for the PDF file?

- Use Timeline Explorer, and filter by `pdf` , you will get two lines.
- By analyzing the creating time in the first line you will get the timestamp changed to for the PDF file.

#### Answer :- 2024-01-14 08:10:06


### 5) The malicious file dropped a few files on disk. Where was "once.cmd" created on disk? Please answer with the full path along with the filename.

- Now, filter by `once.cmd`, you will get four lines.
-  By analyzing in Payload Data4 column you will get the answer.

#### Answer :-  C:\Users\CyberJunkie\AppData\Roaming\Photo and Fax Vn\Photo and vn 1.1.2\install\F97891C\WindowsVolume\Games\once.cmd


### 6) The malicious file attempted to reach a dummy domain, most likely to check the internet connection status. What domain name did it try to connect to?

- Now, do the filter in Event ID which has ID number `22 (DNS query)`
- In  `Payload Data4 column` you will get the query name.

#### Answer :- www.example.com


### 7) Which IP address did the malicious process try to reach out to?

- Now, do the filter in Event ID which has ID number `3 (Network connection)`.
- In `Destination IP cell content` in `Payload Data6 column` you will get the IP address.

#### Answer :- 93.184.216.34


### 8) The malicious process terminated itself after infecting the PC with a backdoored variant of UltraVNC. When did the process terminate itself?

- Now, do the filter with the name of UltraVNC.
- In `Payload column` you will get the process termination time.

#### Answer :- 2024-02-14 03:41:58