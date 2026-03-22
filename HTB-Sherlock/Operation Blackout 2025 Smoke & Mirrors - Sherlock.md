

# Theory part:- 
---

1) What is Local Security Authority (LSA) ?
	
	 - https://itm4n.github.io/lsass-runasppl/
	 - https://blog.cyberadvisors.com/technical-blog/blog/credential-dumping-protections-part-1-lsa-protection#:~:text=LSA%20Protection%20is%20a%20built,they%20want%2C%20no%20matter%20what.
	 - https://blog.cyberadvisors.com/technical-blog/blog/credential-dumping-protections-part-2-bypass-lsa-protection
	
2) 



# Steps to solve:-
---


1) **Download the file :** curl -L -o Operation_Blackout_2025_Smoke_n_Mirrors.zip "https://labs.hackthebox.com/api/v4/challenges/937/cdn/...."


2) **Unzip the file:** sudo 7z x Operation_Blackout_2025_Smoke_n_Mirrors.zip
	
	 There are three extv files namely, 
	 Microsoft-Windows-Powershell-Operational.evtx
	 Microsoft-Windows-Powershell.evtx
	 Microsoft-Windows-Sysmon-Operational.evtx
	




### 1) The attacker disabled LSA protection on the compromised host by modifying a registry key. What is the full path of that registry key?

-  

#### Answer :- 


### 2) Which PowerShell command did the attacker first execute to disable Windows Defender?

-  

#### Answer :-


### 3) The attacker loaded an AMSI patch written in PowerShell. Which function in the DLL is being patched by the script to effectively disable AMSI?

-  

#### Answer :-


### 4) Which command did the attacker use to restart the machine in Safe Mode?

-  

#### Answer :-