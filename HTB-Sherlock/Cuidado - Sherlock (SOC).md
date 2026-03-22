# Steps to solve:-
---


1) **Download the file :** wget -O Cuidado.zip "https://labs.hackthebox.com/api/v4/challenges/967/cdn/..."

2) **Unzip the file:** sudo 7z x Cuidado.zip	
	
	 There is one pcap file namely network_capture.pcap
	 




### 1) What is the victim's IP address?

-  Open `network_capture.pcap` file in wireshark and `filter with http` then  by analyzing you will see victim's IP address.

#### Answer :- 192.168.1.152


### 2) What is the IP address of the attacker from whom the files were downloaded?

-  By analyzing you will see IP address of attacker.

#### Answer :- 94.156.177.109


### 3) Which malicious file appears to be the first one downloaded?

-  By analyzing the traffic you will see the victim ask to attacker by GET method to get the /sh file.

![[Pasted image 20250821104525.png]]

#### Answer :- sh


### 4) What is the name of the function that the attacker used to download the payload?

-  In that traffic you will see the HTTP response was success with 200 status code after GET /sh , so click on that and copy the text to notepad for further analyzing.
- In the whole script you will notice the name of the function that the attacker used to download the payload.

#### Answer :- dlr


### 5) Which port does the attacker's server use?

-  In the same you will notice the port number attached with the attacker ip in the exe command line.

#### Answer :- 80


### 6) The script checks which directories it can write to by attempting to create test files. What is the size of the second test file? (Size in MB)

-  In the same you will find the size of the second test file.

#### Answer :- 2


### 7) What is the full command that the script uses to identify the CPU architecture?

-  In the same you will find the command.

#### Answer :- uname -mp


### 8) What is the name of the file that is downloaded after the CPU architecture is compared with reference values?

-  By analyzing the traffic further you will see that GET is made for the x86_64 by the victim and in the script you will also notice that.

#### Answer :- x86_64


### 9) What is the full command that the attacker used to disable any existing mining service?

-  By analyzing the traffic , in the 3th HTTP success response , you will see the text so copy that in notepad for further analyzing.
- By analyzing the script you will see the command which is used to disable the mining service.

#### Answer :- systemctl disable c3pool_miner


### 10) Apparently, the attacker used a packer to compress the malware. Which version of this packer was used? (Format X.XX)

-  We know that the x86_64 file is used earlier and we have only pcap file.
- So to find the version of this packer we need to download the exe file and analyze it.
- So to download the file follow this steps:
	
	 - Go to file in the wireshark.
	 - Then click on export objects then select HTTP.
	 - Then save the x86_64 file.
	 - Then execute the command in terminal i.e. `string x86_64 | grep -E "UPX|{0-9}\.[0-9]{2}"`
	

Note : We write UPX (Ultimate Packer for Executables) in the command, that insight we got from the HTTP success response after the Certificate revocation list.

![[Pasted image 20250821103136.png]]

#### Answer :- 4.23


### 11) What is the entropy value of unpacked malware?

-  First unpack the malware i.e. X86_64 file, with this command `upx -d x86_64 `.
- Then execute this command `binwalk --entropy x86_64`, then you will get a entropy graph.


![[Pasted image 20250821115758.png]]

- From the entropy graph , the **unpacked malware’s entropy** is in the range of **~0.7–0.8 (normalized scale)**. If we convert that back to the standard Shannon scale.
	  
	0.7 × 8 ≈ 5.6  
	0.8 × 8 ≈ 6.4
	

- The entropy value of the unpacked malware is approximately: **6.0 (bits/byte)**  _(normal for an unpacked Windows PE executable — much lower than packed/encrypted samples which usually show ~7.5–8.0)._
- To get the exact number (X.XXXXXX) , i wrote and executed the python script to calculate the entropy.
#### Answer :- 6.488449


### 12) What is the file name with which the unpacked malware was submitted on VirusTotal?

-  First check it sha256 hash by executing sha256sum x86_64. Then paste that hash to virus total and then go to Details section and then check in name section, there you will find the file name.

#### Answer :- redtail.cuidado


### 13) What MITRE ATT&CK technique ID is associated with the main purpose of the malware?

-  Go to Behavior section, in that go to Impact section under MITRE ATT&CK Tactics and Techniques, there is Resource Hijacking section in Impact section with ID T1496.

#### Answer :- T1496


