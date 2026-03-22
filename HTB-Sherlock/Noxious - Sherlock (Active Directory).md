

# Theory part:- 
---

1) What is Link-Local Multicast Name Resolution (LLMNR) poisoning?
	
	 - https://tcm-sec.com/llmnr-poisoning-and-how-to-prevent-it/
	 
	 
2) What is New Technology LAN Manager (NTLM)?
	
	 - https://www.crowdstrike.com/en-us/cybersecurity-101/identity-protection/windows-ntlm/
	
3) What is Sever Message Block?
	
	 - https://amitschendel1.medium.com/smb-going-from-zero-to-hero-ff686e907e81
	




# Steps to solve:-
---

1) **Download the file :** wget -O Noxious.zip "https://labs.hackthebox.com/api/v4/challenges/747/cdn/...."


2) **Unzip the file:** sudo 7z x Noxious.zip
	
	 There is a pcap file namely, `capture.pcap`
	



### 1) Its suspected by the security team that there was a rogue device in Forela's internal network running responder tool to perform an LLMNR Poisoning attack. Please find the malicious IP Address of the machine.

- We know that the LLMNR is poisoned , so i filter `ip.addr==172.17.79.136 and llmnr` , (IP address of Forela-WKstn002 is given).
- After filtering , i analyze it and found the ip address because which is same subnet as source.

![[Pasted image 20250606140528.png]]

#### Answer :- 172.17.79.135


### 2) What is the hostname of the rogue machine?

- To find the hostname , i filter with `ip.addr==172.17.79.135 and dhcp` (the hostname in DHCP protocol traffic because, during the DHCP negotiation process, the client typically includes its hostname when requesting an IP address. This is part of standard DHCP behavior, specifically through **Option 12 (Host Name)** in the DHCP request packet. When a device connects to a network and sends a **DHCP Discover** or **DHCP Request**, it often includes its hostname to help the DHCP server identify the device. This hostname can then be used by the server for DNS registration, logging, or network inventory purposes. Additionally, some clients may also include **Option 81 (FQDN)**, which provides the Fully Qualified Domain Name (e.g., `laptop1.company.local`) instead of just the hostname. In Wireshark, you can inspect DHCP packets using the display filter `bootp`, and then expand the "Bootstrap Protocol" section in the packet details to see these options. The hostname is usually found in the `bootp.option.hostname` field.)

![[Pasted image 20250606213907.png]]

#### Answer :- kali


### 3) Now we need to confirm whether the attacker captured the user's hash and it is crackable!! What is the username whose hash was captured?

- I filter `ip.addr==172.17.79.136` and analyze the logs and found the username whose hash was captured.

![[Pasted image 20250606141409.png]]

#### Answer :- john.deacon


### 4) In NTLM traffic we can see that the victim credentials were relayed multiple times to the attacker's machine. When were the hashes captured the First time?

- I filter with `ntlmssp` protocol then in first log i found the time when the hashes were first time captured.

#### Answer :- 2024-06-24 11:18:30


### 5) What was the typo made by the victim when navigating to the file share that caused his credentials to be leaked?


#### Answer :- DCC01


### 6) To get the actual credentials of the victim user we need to stitch together multiple values from the ntlm negotiation packets. What is the NTLM server challenge value?

- Now, filter with `ntlmssp` (narrows down packets to only those involved in NTLM authentication, including Negotiate, Challenge, and Authenticate messages).
- Look for a packet with `NTLMSSP_CHALLENGE` in the Info column (this packet contains the challenge sent from the server to the client as part of the NTLM authentication handshake)
-  Expand the packet details -> NTLMSSP section (this section provides a structured view of the NTLM protocol, including the message type and challenge details.)
- Locate the field named `NTLM Server Challenge` (this is an 8-byte hexadecimal value used by the server to create the challenge-response mechanism). 

![[Pasted image 20250609125125.png]]


#### Answer :- 601019d191f054f1


### 7) Now doing something similar find the NTProofStr value.

- Now, filter with `ntlmssp` (Narrows down packets to only those involved in NTLM authentication, including Negotiate, Challenge, and Authenticate messages).
- Look for a packet with `NTLMSSP_AUTH` in the **Info** column (This is the final step in the NTLM handshake, where the client responds to the server challenge by providing the NTLMv2 response -- which contains the `NTProofStr`).
- Look SMB2 (Server Message Block Protocol) because NTLMSSP is typically encapsulated inside the SMB2 protocol -- specifically inside a Session Setup Request packet.
- Expand `Session Setup Request (0x01)` because This is where the client sends authentication data -- including the NTLMSSP AUTH payload.
- Expand `Security Blob` (This blob contains the authentication data wrapped inside GSS-API and SPNEGO structures)
- Expand `GSS-API Generic Security Service` -> `Simple Protected Negotiation (SPNEGO)` because Windows uses SPNEGO for negotiating NTLMSSP authentication, and Wireshark decodes this structure step-by-step.
- Expand `negTokenTarg` -> `NTLM Secure Service Provider`  (This shows the actual NTLMSSP message embedded inside SPNEGO.)
- Expand `NTLMv2 Response` .(This is the client’s proof to the server, built from a combination of the password hash, the server challenge, and a structured blob). 
- Look for NTProofStr and there you will find the value.

This is the first 16 bytes (32 hex characters) of the NTLMv2 response. It’s an HMAC-MD5 value that proves the client knows the password hash, computed as:  
`NTProofStr = HMAC-MD5(NTLM-Hash, ServerChallenge || Blob)`)

![[Pasted image 20250616084857.png]]

#### Answer :- c0cc803a6d9fb5a9082253a04dbd4cd4


### 8) To test the password complexity, try recovering the password from the information found from packet capture. This is a crucial step as this way we can find whether the attacker was able to crack this and how quickly.


#### Answer :- NotMyPassword0K?


### 9) Just to get more context surrounding the incident, what is the actual file share that the victim was trying to navigate to?


#### Answer :- \\DC01\DC-Confidential