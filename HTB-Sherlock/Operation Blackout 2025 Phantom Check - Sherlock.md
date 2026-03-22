
# Theory part:- 
---

1) What is Windows Management Instrumentation (WMI) ?
	
	 - https://learn.microsoft.com/en-us/windows/win32/wmisdk/wmi-start-page
	
2) Difference between WMI and WBEM?
	
	 - In depth comparison: [[In-Depth Comparison WMI vs WBEM]]
	
3) What are WMI classes ?
	
	 - https://www.windows-active-directory.com/wmi-classes-and-categories.html
	
4) What is anti-virtualization ?
	
	 - https://www.picussecurity.com/resource/virtualization/sandbox-evasion-how-attackers-avoid-malware-analysis
	 - https://www.apriorit.com/dev-blog/545-sandbox-evading-malware
	


# Steps to solve:-
---


1) **Download the file :** curl -L -o  Operation_Blackout_2025_Phantom_Check.zip "https://labs.hackthebox.com/api/v4/challenges/935/cdn/...."


2) **Unzip the file:** sudo 7z x Operation_Blackout_2025_Phantom_Check.zip
	
	 There are two extv files namely, 
	 Microsoft-Windows-Powershell.evtx 
	 Windows-Powershell-Operational.evtx
	 



### 1) Which WMI class did the attacker use to retrieve model and manufacturer information for virtualization detection?

-  In `Windows-Powershell-Operational.evtx` , filter the Event ID with 4104 and logs will be displayed. In that logs look into each log specifically in `ScriptBlockText section` .
- In that you will find the WMI class which is used by the attacker to retrieve model (which is done at 2025-04-09 09:19:10) and manufacture (which is done at 2025-04-09 09:19:09) information for virtualization detection.

![[Pasted image 20250710154708.png]]
![[Pasted image 20250710154832.png]]

#### Answer :- Win32_ComputerSystem


### 2) Which WMI query did the attacker execute to retrieve the current temperature value of the machine?

-  In `Windows-Powershell-Operational.evtx`, filter the Event ID with 4104 and logs will be displayed. In that logs look into each log specifically in `ScriptBlockText section` .
- At   `2025-04-09T09:20:11` a query was executed to retrieve the current temperature value of the machine.

![[Pasted image 20250710144345.png]]

#### Answer :- SELECT * FROM MSAcpi_ThermalZoneTemperature



### 3) The attacker loaded a PowerShell script to detect virtualization. What is the function name of the script?

-  In `Windows-Powershell-Operational.evtx` , filter the `Event ID with 4104` and in the sixth log you will find the name of the function of the PowerShell script to detect virtualization.

![[Pasted image 20250710143417.png]]

#### Answer :- Check-VM


### 4) Which registry key did the above script query to retrieve service details for virtualization detection?

-  In the `ScriptBlockText section` of the same log, you will find the registry key which is used to retrieve service details for virtualization detection.

![[Pasted image 20250710144003.png]]

#### Answer :- HKLM:\SYSTEM\ControlSet001\Services



### 5) The VM detection script can also identify VirtualBox. Which processes is it comparing to determine if the system is running VirtualBox?

-  In the same you will also find the processes which are compared to determine if the system is running in the VirtualBox which are `vboxservice.exe` *(This is the **VirtualBox Guest Additions Service**, installed when **Guest Additions** are set up in a virtual machine. It provides core integration features between the **guest OS** and the **VirtualBox hypervisor**.)*  and `vboxtray.exe` *(System tray application part of **Guest Additions** in Windows. Typically runs under user context (e.g., after login) and places the **VirtualBox icon in the taskbar** of the guest OS.)*

![[Pasted image 20250710150611.png]]

#### Answer :- vboxservice.exe, vboxtray.exe


### 6) The VM detection script prints any detection with the prefix 'This is a'. Which two virtualization platforms did the script detect?

-  In the same section , you will see that VM names are mentioned with indication This is a ... , and by through this you will identify the two virtualization platforms detected by the script.

#### Answer :- Hyper-V, Vmware