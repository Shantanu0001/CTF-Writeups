
# Theory part:- 
---

1) What is AS-REP Roasting?
	
	 - https://blog.netwrix.com/2022/11/03/cracking_ad_password_with_as_rep_roasting/
	 
	 






# Steps to solve:-
---

1) **Download the file :** wget -O campfire-2.zip "https://labs.hackthebox.com/api/v4/challenges/736/cdn/..."


2) **Unzip the file:** sudo 7z x campire-2.zip
	
	 There is one file namely, `Security.evtx`
	




### 1) When did the ASREP Roasting attack occur, and when did the attacker request the Kerberos ticket for the vulnerable user?

- Open the `Security.evtx` file in event viewer and filter with `Event ID 4768`.
- There are several logs so by analyzing each i found the target user, so in that you will find the time.

![[Pasted image 20250606095339.png]]

#### Answer :- 2024-05-29 06:36:40


### 2) Please confirm the User Account that was targeted by the attacker.

- In the same you will find the user account that was targeted by the attacker.

#### Answer :- arthur.kyle


### 3) What was the SID of the account?

- In the same you will find the SID of the account.

#### Answer :- S-1-5-21-3239415629-1862073780-2394361899-1601


### 4) It is crucial to identify the compromised user account and the workstation responsible for this attack. Please list the internal IP address of the compromised asset to assist our threat-hunting team.

- In the same you will find the IP address of the compromised asset.

#### Answer :- 172.17.79.129


### 5) We do not have any artifacts from the source machine yet. Using the same DC Security logs, can you confirm the user account used to perform the ASREP Roasting attack so we can contain the compromised account/s?

- Now, filter `Event ID with 4769` , there are lots of logs, so we know that attacker at occurred at `2024-05-29 06:36:40` so by analyzing i found the user account used to perform the ASREP Roasting attack.

![[Pasted image 20250606100711.png]]

#### Answer :- happy.grunwald