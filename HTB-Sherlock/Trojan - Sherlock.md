
# Theory part:- 
---

### What is an .ad1 File?

###### Full Name:

	AccessData Custom Content Logical Image File (.ad1)

###### Type:

- **Logical image file** (not a bit-for-bit physical disk image).
- Part of a **forensic acquisition process**, storing only selected content (files, folders, or logical artifacts) rather than imaging an entire drive.
###### Origin and Tool

- Created by **FTK Imager**, a widely-used forensic imaging and preview tool by **AccessData** (now part of **Exterro**).
- Introduced as a format to support **Custom Content Images**, allowing forensic investigators to capture a **subset of a file system** (e.g., Documents folder, browser data, etc.) without imaging the entire disk.

#### Internal Structure

An .ad1 file is a **container format**, somewhat like a forensic-optimized archive (e.g., ZIP), but with added metadata and hashing. Internally, it contains:

###### 1. **File Headers**

- Signature or magic number to identify the format.
- Version information, flags, and compression indicators.
###### 2. **File Metadata**

- File names, timestamps (MAC times — Modified, Accessed, Created), attributes (hidden, read-only, etc.).
- Original file paths and logical location in source file system.

###### 3. **Data Blocks**

- Chunked and optionally compressed data of the original files.
- Each block has its own **hash (MD5/SHA1)** for integrity verification.

###### 4. **Index and Directory Structure**

- Hierarchical directory view reflecting the logical file structure.
- Facilitates mounting and browsing within forensic tools.
###### 5. **Checksums & Hashes**

- Global and per-block hashing (usually MD5 and/or SHA-1).
- Ensures forensic soundness and verifiability in court/legal cases.

###  Use Cases in Digital Forensics

|Scenario|Description|
|---|---|
|**Targeted Collection**|Collect only email folders, browser caches, user documents, registry hives, etc.|
|**Live Forensics**|Acquire logical files from a live system (not shutdown), avoiding full disk imaging.|
|**Cloud/Remote Imaging**|Use `.ad1` to gather logical files over a network with minimal bandwidth.|
|**eDiscovery**|In litigation, only relevant documents or communications are collected to reduce volume.|
|**Triage and Preliminary Analysis**|Create a quick snapshot of suspect artifacts for fast initial review.|
## Limitations

- **Not suitable for full-disk recovery**: You can’t recover deleted sectors, unallocated space, or low-level artifacts.
    
- **Limited in some tools**: Not every forensic tool supports `.ad1`.
    
- **Risk of missing evidence**: Since it’s not a full image, important hidden or deleted evidence may not be captured.
---

## What Is the Amcache?

- **Amcache.hve** is a Windows registry hive that logs metadata about **executables** that were run or installed on the system.
- It was introduced with **Windows 8+**.
- It captures data like:
    - Full file path
    - Execution time
    - SHA1/SHA256 hashes
    - Program name
    - Publisher
    - PE compile timestamp
    - And most importantly, **Last Modified timestamp of the Amcache entry**, which _often correlates with first execution_.

##### What This Entry Tells You

|**Field**|**Value / Meaning**|
|---|---|
|**File Path**|`c:\users\john\downloads\data_recovery\recovery_setup.exe` — suspicious EXE file|
|**Amcache Event Type**|`registry key modified` — The Amcache registry recorded/modded this EXE's metadata|
|**Timestamp**|`2023-05-30 02:06:29 UTC` — When the Amcache registry key for this file was last modified|

##### So, What Does It _Really_ Mean?

🔐 **This means the file was _first executed or interacted with_ around `2023-05-30 02:06:29 UTC`.**

- Windows **only logs an entry in Amcache when an executable is run**, not when it’s just copied or downloaded.
- So, **the timestamp is a strong indicator of when the file was executed** for the first time.
- The Amcache log is very **forensically reliable** — malware can try to wipe it, but it's harder to tamper with during memory acquisition.
##### Why Is This Important in Malware Analysis?

- The timestamp helps build a **timeline of attack or infection**.
- You can correlate this with:
    - `windows.pslist`, `cmdline`, `shimcache`, `userassist` for other signs of execution.
        
    - Registry changes, persistence mechanisms (`Run` keys, `ScheduledTasks`, etc.).
    
- If this was a trojan or malware, this could mark **initial execution (Initial Access)**.
- You can also **pivot off this timestamp** in SIEM logs or network forensics.





----
#### `Folder and files` 

1)  `AppCompat Folder`
	
	 `Location:`
	
		C:\Windows\System32\AppCompat
	
	 `Purpose:`
	
	 This folder contains **application compatibility data**, used by Windows to ensure older programs can still run properly. It may also include:
	
	- Programs compatibility DBs
	- AppCompatCache (stored in registry, but referenced here)
	- DLLs used for shimming (application redirection for compatibility)
	
	 `Forensics Use:`
	
	 Although the folder itself may not contain logs, **application compatibility data** is also stored in the registry under:
	
	 `HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\AppCompatCache`
	
	 This data is extracted during forensic investigations to:
	
	- See **what programs have run** on the system
	- Determine the **last time** an executable was launched
	- Track **malware persistence** using renamed executables
	
	 You can parse this using Regripper plugin:
	 
	`rip.pl -r SYSTEM -p shimcache
	
	
2) `Prefetch Folder`

	`Location:`
	
		`C:\Windows\Prefetch`
	
	` Purpose:`
	
	 This folder contains `.pf` files that Windows creates for **frequently executed programs** to speed up their launch.
	
	Each `.pf` file:
	
	- Is named like: `APPNAME.EXE-3D4B2C40.pf`
	- Contains a timestamp of **last run**
	- Shows how many **times** the app was executed
	- Lists associated DLLs and file paths used during execution
	
	 `Forensics Use:`
	
	This is **very useful** in forensics for:

| Analysis             | Info Retrieved                                               |
| -------------------- | ------------------------------------------------------------ |
| Program Execution    | What was run and when                                        |
| Frequency            | How often was it run                                         |
| Malware Detection    | Malicious tools like `mimikatz.exe`, `meterpreter.exe`, etc. |
| Timeline Correlation | Link to other log sources for a timeline                     |

3) 
	- `memory.vmem` = raw RAM dump of the virtual machine.
	- `memory.vmsn` = snapshot metadata (contains config info, CPU state, etc.).
	
	These files come from **VMware Workstation**, **Fusion**, or **ESXi**.

4) 


# Steps to solve:-
---

1) **Download the file :** wget "https://challenges-cdn.hackthebox.com/sherlocks/easy/Trojan.zip/..."

2) **Unzip the file:** sudo 7z x Trojan.zip

	 There are three folders namely:
	  
	  - disk artifacts --> disk_artifacts.ad1
	  - memory capture --> memory.vmem , memory.vmsn
	  - packet capture --> network.pcapng
	 


### 1) What is the build version of the operating system?
 

-  We know that, if we want to find build version we have to analyze the disk_artifacts.ad1.
-  We analyze disk_artifacts.ad1 in windows using the tool called Exterro FTK Imager. 
- So, first download and install Exterro FTK Imager for windows. After installing do the following: 

	- Open **FTK Imager**.
	- Go to `File → Add Evidence Item → Image File`.
	- Load `disk_artifacts.ad1`    
	- In the evidence tree navigate to evidence tree i navigated to ![[Pasted image 20250520090019.png]] because...![[Pasted image 20250520083226.png]]
	
	- Export the amcache.hve to analyze it. 
	- So to analyze it do the following : -
	  
	1.  **Use [Eric Zimmerman's AmcacheParser](https://github.com/EricZimmerman/AmcacheParser)**
    2. Download the precompiled release from the [Releases tab](https://github.com/EricZimmerman/AmcacheParser/releases) and Extract it.
	3. Open **Command Prompt** where the `.exe` is located and run:
	
		 `AmcacheParser.exe -f "C:\Users\IEUser\Desktop\Trojan\appcompat\Programs\Amcache.hve" --csv "C:\Users\IEUser\Desktop\Trojan\output" --csvf "amcache-output.csv"`
    
	4. Open the CSV to review:
    
	    - Application names
	    - Install paths
	    - SHA1 hashes
	    - File creation timestamps
	    - OS version info (may be included in metadata)
	  
#### Answer :- 19041

### 2) What is the computer hostname?

- In the `20250518221404_Amcache_DeviceContainers.csv` file you will find the hostname.
#### Answer :- DESKTOP-38NVPD0

### 3) What is the name of the downloaded ZIP file?

- To find the download file you have to look at the pcap file. So open the pcap file in wireshark.
- In the wireshark filter the `http.request.method == GET`. After filtering you will find the following. 
  ![[Pasted image 20250520093204.png]]
#### Answer :- Data_Recovery.zip

### 4) What is the domain of the website (including the third-level domain) from which the file was downloaded?

- In the HTTP content you will find the domain of the website.
#### Answer :- praetorial-gears.000webhostapp.com

### 5) The user then executed the suspicious application found in the ZIP archive. What is the process PID?

- To find the process PID you need to analyze the `memory.vmem` and `memory.vmsn` which is present in memory capture folder.
- To analyze the `memory.vmem` and `memory.vmsn` you need a tool. So we will use `Volatility`.
  
	`1. git clone https://github.com/volatilityfoundation/volatility3.git`
	`2. python3 -m venv vol3-env`
	`3. source vol3-env/bin/activate`
	`4. cd volatility3` 
	`5. pip install .`
	`6. After the install completes run python3 vol.py -h ,you should see the Volatility 3 help menu `
	`7. Check the permission of the file because sometimes it give errors `
	
		sudo chown helix:helix "../memory capture/memory.vmsn"
	
	`8. python3 vol.py -f "../memory capture/memory.vmem" windows.info.Info
	
	If the above command execute successfully then you will see following :-
	![[Pasted image 20250520100109.png]]
`
	`9. python3 vol.py -f "../memory capture/memory.vmem" windows.pslist.PsList`

By running above command you will find the process PID of the malicious application.
#### Answer :- 484

### 6) What is the full path of the suspicious process?

- Once the PID is found you can easily find the full path of the suspicious process by executing the following command:-
  
	`python3 vol.py -f "../memory capture/memory.vmem" windows.dlllist.DllList --pid 484`

#### Answer :- C:\Users\John\Downloads\Data_Recovery\Recovery_Setup.exe


### 7) What is the SHA-256 hash of the suspicious executable?

- Export the Recovery_Setup.exe from the ![[Pasted image 20250520101555.png]] by using FTK Imager.
  - After exporting the file, go to virustotal and upload the file, in the details section you will find the SHA-256.
#### Answer :- C34601c5da3501f6ee0efce18de7e6145153ecfac2ce2019ec52e1535a4b3193

### 8) When was the malicious program first executed?

- To find the time execute the following command:-
  
	 `python3 vol.py -f "../memory capture/memory.vmem" timeliner.Timeliner | grep -i 'Recovery_Setup.exe'`

![[Pasted image 20250520104657.png]]

>The **Amcache** is a Windows artifact that logs **execution and installation metadata** for executables (especially `.exe` files).
>
  Amcache entries are **persistent** and survive reboots.
  This data helps you **build a timeline of malicious activity**, especially during incident response.


#### Answer :- 2023-05-30 02:06:29


### 9) How many times in total has the malicious application been executed?

- To find this you need to go and look into prefetch folder, because we known that the Prefetch folder stores data that helps speed up application launch — and each time an executable is run Windows (if prefetching is enabled) creates or updates a `.pf` file.

###### What information does the `.pf` file store?

The `.pf` file stores:

| Information         | Description                                                             |
| ------------------- | ----------------------------------------------------------------------- |
| **Executable name** | Which EXE was executed                                                  |
| **Path**            | Full path to the EXE file (not in the filename, but inside the pf file) |
| **Last Run Time**   | When the EXE was last executed                                          |
| **Run Count**       | **How many times the EXE was executed** (very useful!)                  |
| **Files loaded**    | DLLs, INIs, etc. used during execution                                  |

- Now to analyze the .pf file you need a tool called PECmd which you can download from https://download.ericzimmermanstools.com/PECmd.zip . After downloading , extract it and run the following command.

	`PECmd.exe -f "C:\Users\IEUser\Desktop\Trojan\Prefetch\RECOVERY_SETUP.EXE-A808CDAB.pf"`

#### Answer :- 2


### 10) The malicious application references two .TMP files, one is IS-NJBAT.TMP, which is the other?

- By executing above command you will find another references.
#### Answer :- IS-R7RFP.TMP

### 11) How many of the URLs contacted by the malicious application were detected as malicious by VirusTotal?

- In the relation section of virustotal you will see the contacted url by the malicious application were detected as malicious.
#### Answer :- 4


### 12) The malicious application downloaded a binary file from one of the C2 URLs, what is the name of the file?

- In the virustotal go to behavior section, in that section you will see the MITRE ATT&CK Tactics and Techniques section and inside that there is Command and control section.
- There you find 4 urls and by analyzing them you will find the file name.
- You can also see the same url in the relation section of virustotal.
#### Answer :- puk.php


### 13) Can you find any indication of the actual name and version of the program that the malware is pretending to be?

#### Answer :- FinalRecovery v3.0.7.0325