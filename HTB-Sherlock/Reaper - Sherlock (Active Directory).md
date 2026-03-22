


# Steps to solve:-
---

1) **Download the file :** wget -O Reaper.zip "https://labs.hackthebox.com/api/v4/challenges/749/cdn/..."


2) **Unzip the file:** sudo 7z x Reaper.zip
	
	 There are two files namely,
	 - ntlmrelay.pcapng  
	 - Security.evtx
	


### 1) What is the IP Address for Forela-Wkstn001?

- Open the `ntlmrelay.pcapng` file in wireshark and filter with dns.  

#### Answer :- 172.17.79.129


### 2) What is the IP Address for Forela-Wkstn002?


#### Answer :- 172.17.79.136


### 3) What is the username of the account whose hash was stolen by attacker?

- Open the `Security.evtx` and filter with `Event ID 4624 (which is for logon)` . There you will get lots of logs so by analyzing each you will find a log at `7/30/2024 9:55:16` . In that you will find the username of the account in the Event Data section.

![[Pasted image 20250617094009.png]]

#### Answer :- arthur.kyle


### 4) What is the IP Address of Unknown Device used by the attacker to intercept credentials?

- In there under `Event Data section` you will find the IP Address of Unknown device used by the attacker to intercept credentials.
- Or else, in the pcap file , first filter with `ip.addr==172.17.79.124` and by understanding the routes to  `ip.addr==172.17.79.4` and from `ip.addr==172.17.79.124` to `ip.addr==172.17.79.135` .

#### Answer :- 172.17.79.135


### 5) What was the fileshare navigated by the victim user account?

- ~~Again filter `Event ID with 4624` and by analyzing each log you will find the fileshare navigated by the victim user account.~~ 

#### Answer :- \\DC01\Trip


### 6) What is the source port used to logon to target workstation using the compromised account?

- Look at the same log where you found the username of account stolen by the attacker you will find the source port used to logon to target workstation using the compromised account.

#### Answer :- 40252


### 7) What is the Logon ID for the malicious session?

- In there you will find the Logon Id for the malicious session.

#### Answer :- 0x64A799


### 8) The detection was based on the mismatch of hostname and the assigned IP Address.What is the workstation name and the source IP Address from which the malicious logon occur?

- In there you will find both hostname and IP address.
- Or you can also analyze the in wireshark by filtering the `ntlmspp` , we know the ip address of the second user that is `172.17.79.136` and by the security.evtx we know the unknown device IP address that is `172.17.79.135` , so mapping them combine and analyzing them you can find the both workstation and IP address.

#### Answer :- FORELA-WKSTN002, 172.17.79.135


### 9) At what UTC time did the the malicious logon happen?

- Look at the same log where you found the username of account stolen by the attacker. In there, in the `System section` you will find the time when the malicious logon happened.

#### Answer :- 2024-07-31 04:55:16


### 10) What is the share Name accessed as part of the authentication process by the malicious tool used by the attacker?

- Now, filter `Event ID with 5140`.  Then in the EventData you will find the name of the path.

![[Pasted image 20250616135426.png]]

#### Answer :-  \\*\IPC$ 