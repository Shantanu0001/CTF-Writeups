

# Theory part:- 
---

1) What is NFTS?
	
	https://www.ntfs.com/index.html
	
2) What is the difference between NFTS and FAT32?
	
	https://www.geeksforgeeks.org/difference-between-fat32-and-ntfs/
	
3) What is journaling?
	
	- Journaling is a fault tolerance mechanism used by file systems to log intended changes before applying them to the main file system structures. It ensures file system integrity after crashes, power failures, or unexpected shutdowns.
	
	 In Detailed : - [[Journaling in file system]]
	
4) What is Command and Control (C2) and how to detect it. Also explain what is C2 Server?
	
	 -  https://hunt.io/glossary/detect-c2
	 - https://hunt.io/glossary/command-and-control-server-c2
	
5) How to find hex offset of a MFT record?
	
	 Hex offset = 1024 * Entry number


##### What is a Hex Offset?

A **hex offset** is a number written in hexadecimal (base-16) that tells you the exact **position of a byte** or data structure inside a file, memory, or disk.

It answers this question:

> “Where, precisely, does this data begin?”

###### What Does "Offset" Mean?

An **offset** means a distance from the **start of something** — in our case, from the start of a file or memory.

- **Offset 0x00** = very beginning
- **Offset 0x10** = 16 bytes from the start
- **Offset 0x2000** = 8192 bytes from the start

So, a hex offset tells us **how far** into a file we must go to find something.

##### Why Use Hexadecimal?

### Reasons hex is preferred:

1. **Binary is too long**: `0b000110111000110000000000` is unreadable.
2. **Decimal is not byte-aligned**: Computers work in powers of 2 (8-bit, 16-bit, 1024-byte blocks).
3. **Hex aligns with bytes**:
    
    - 1 byte = 8 bits = 2 hex digits.
        
    - 0x10 = 16 bytes (perfect for data alignment).

##### Example in File Forensics: Master File Table ($MFT)

##### MFT Overview:

- NTFS stores information about **every file** in a special file: `$MFT`
- Each **MFT record**:
    
    - Has a fixed size: **1024 bytes (0x400)**
        
    - Is identified by an **Entry Number**


######  Hex Offset in MFT:

Let’s say you want to view Entry #300 in a hex editor.

#### Steps:

1. **Calculate offset**:
    
    `Offset = EntryNumber × RecordSize        = 300 × 1024 bytes        = 307200 bytes = 0x4B000 (hex)`
    
2. **Open `$MFT` in a hex editor**
    
3. **Jump to offset `0x4B000`**
    
4. Now you're at **MFT record #300**

This is crucial for manually analyzing files, timestamps, paths, and malware traces directly in raw disk or MFT data.

##### Practical Use Cases in Forensics

|Task|Use of Hex Offset|
|---|---|
|🐞 Malware analysis|Find where a malicious file’s MFT record is stored|
|🧠 Memory forensics|Locate shellcode or injected code in memory dumps|
|🧾 File carving|Extract file content from raw disk using offsets|
|📑 Metadata recovery|Manually read timestamps, file names, flags from MFT|

#####  Tools That Use Hex Offsets

- **Hex editors**: HxD, WinHex, 010 Editor
    
- **Forensic suites**: Autopsy, FTK Imager, X-Ways
    
- **Command-line**: `xxd`, `hexdump`, `MFTECmd`



# Steps to solve:-
---

1) **Download the file :** wget "https://challenges-cdn.hackthebox.com/sherlocks/very-easy/BFT.zip/..."

2) **Unzip the file:** sudo 7z x BFT.zip
   
	There is one directory namely `C`.
	
	 - On that directory there is a data file namely $MFT (To identify the file data i did, file $MFT)
	


### 1) Simon Stark was targeted by attackers on February 13. He downloaded a ZIP file from a link received in an email. What was the name of the ZIP file he downloaded from the link?

- In the Flare VM i made the directory C:\Users\IEUser\HTB\BFT. 
- Then i downloaded the challenge. 
  
  >curl -o BFT.zip "https://challenges-cdn.hackthebox.com/sherlocks/very-easy/BFT.zip?u=2374664&p=mp&e=1748187267&t=1748180067&h=844187049583ba9bd9560e32597d925cbca1099085ceeaa707abdbb33b0dd18b"  

- Then i used MFTECmd to extract the $MFT to CSV.
  
> MFTECmd.exe -f $MFT --csv C:\Users\IEUser\HTB\BFT\mft-output 

- Then i open CSV in excel and analyze once, i noticed that there is `Extension column` so i simply did the find operation, by selecting the Extension column. 
#### Answer :- Stage-20240213T093324Z-001.zip


### 2) Examine the Zone Identifier contents for the initially downloaded ZIP file. This field reveals the HostUrl from where the file was downloaded, serving as a valuable Indicator of Compromise (IOC) in our investigation/analysis. What is the full Host URL from where this ZIP file was downloaded?

- By analyzing the same row you will find the HOST URL.

#### Answer :- https://storage.googleapis.com/drive-bulk-export-anonymous/20240213T093324.039Z/4133399871716478688/a40aecd0-1cf3-4f88-b55a-e188d5c1c04f/1/c277a8b4-afa9-4d34-b8ca-e1eb5e5f983c?authuser



### 3) What is the full path and name of the malicious file that executed malicious code and connected to a C2 server?

- In this i used tool called `TimelineExplorer`.
  
  Why TimelineExplorer?

|Feature|TimeLine Explorer|Excel|
|---|---|---|
|Handles large CSVs|✅ Yes|❌ May crash|
|DFIR-tailored filters|✅ Built-in|❌ Manual formulas|
|Visualize file activity|✅ Easy & quick|❌ Hard to track|
|Timestamp comparisons|✅ Built-in|❌ Not intuitive|
|Execution analysis support|✅ Excellent|❌ Manual effort|
  
- We know that Simon Stark downloaded a zip file, so the malicious application will be there, so to the find for `Stage-20240213T093324Z-001` , and when you do this and analyze it you will find the full path.

#### Answer :- C:\Users\simon.stark\Downloads\Stage-20240213T093324Z-001\Stage\invoice\invoices\invoice.bat


### 4) Analyze the $Created0x30 timestamp for the previously identified file. When was this file created on disk?

- By analyzing the same row in $Created0x30 column you will find when it was created on disk.

#### Answer :- 2024-02-13 16:38:39


### 5) Finding the hex offset of an MFT record is beneficial in many investigative scenarios. Find the hex offset of the stager file from Question 3.

- By analyzing the same row in the entry number column you will get the number and then by the formula you multiply that number with 1024, and then convert the result in hex.

#### Answer :- '0x16e3000' ---> 16E3000 (accepted this)


### 6) Each MFT record is 1024 bytes in size. If a file on disk has smaller size than 1024 bytes, they can be stored directly on MFT File itself. These are called MFT Resident files. During Windows File system Investigation, its crucial to look for any malicious/suspicious files that may be resident in MFT. This way we can find contents of malicious files/scripts. Find the contents of The malicious stager identified in Question3 and answer with the C2 IP and port.

- MFTECmd.exe -f  --de 23436

#### Answer :- 43.204.110.203:6666