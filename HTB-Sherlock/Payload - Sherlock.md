

# Theory part:- 
---

1) What is Address Space Layout Randomization (ASLR)?
	
	 - https://en.wikipedia.org/wiki/Address_space_layout_randomization
	 - https://medium.com/@boutnaru/security-aslr-address-space-layout-randomization-part-1-overview-3aec7fec01e0
	
2) What is base address of executable? (Related/Linked to above question)
	
	 - More detailed: [[what is base address of executable]]
	   




# Steps to solve:-
---

1) **Download the file :** curl -L -o Payload.zip  "https://labs.hackthebox.com/api/v4/challenges/751/cdn/...."


2) **Unzip the file:** sudo 7z x Payload.zip
	
	 There is one zip file namely danger.zip and one text file namely danger.txt
	 
	After unzipping the danger.zip file (password in danger.txt) there is one executable file called func_pointer.exe.




### 1) What is the SHA256 hash of func_pointer.exe?

-  In the terminal execute `sudo sha250sum func_pointer.exe` , you will get the SHA256 of the func_pointer.exe. 

#### Answer :- EDD41B4A819F917F81203424730AAF0C24CC95E40ACFC0F1BD90B11DADF58015


### 2) What compiler is being used?

- Copy the hash value and paste in the virustotal , and go to the details section in that you will see the DetectItEasy , in that name of the compiler is mentioned which is  ` Linker: GNU linker ld (GNU Binutils) (2.40) [Console64,console]` . In short Mingw.

Or

- In the terminal execute `sudo strings -n 6 func_pointer.exe | grep -i -E "gcc| msvc|borland|delphi|ompiler|linker|cl\.exe|Visual|mingw|studio"`, you will get the name of the compiler.

#### Answer :- MingW


### 3) What is the compilation date?

-  In the details section, look at the Portable Executable Info section, there you will find the compilation timestamp.

#### Answer :- 2023-04-06 15:21:17


### 4) Is ASLR enabled? (True or False)

-  There is no presence of `DllCharacteristics` in PE header though .`reloc` is present which is required for ASLR, so this tell us that ASLR isn't enabled.

#### Answer :- False


### 5) What is the image base address?

-  From above we know that malicious executable file is not supporting ASLR that means it is not set `/DYNAMICBASE` , so it is using base address and default base address for EXE in `x64 machince` is `0x140000000`.

#### Answer :- 0x140000000


### 6) What is the entry point?

-   In the PE header section you will find the entry point which will be in decimal, so you have to covert into in hexadecimal. 
- To convert is use `printf "0x%x\n" 4389` .

#### Answer :- 0x1125 (4389 in decimal)


### 7) What are the first 8 bytes of the encrypted payload that is being moved to allocated memory? (format: daffd563616c632e)

-   

#### Answer :- 


### 8) What is the key for decryption in hex?

-   

#### Answer :- 


### 9) What is the address of the decrypted payload?

-   

#### Answer :- 
