
# Theory part:- 
---

1) What is SMTP (Simple Mail Transfer protoco)?
	
	https://mailtrap.io/blog/smtp/#What-is-SMTP
	
2) What is DKIM (Domain Key Identified Mail)?
	
	 https://mailtrap.io/blog/dkim/
	 
3) What is Return Path?
	- https://mailtrap.io/blog/returnpath-email/#:~:text=The%20return%20path%20is%20an,and%20the%20reasons%20behind%20them.
	
	 - https://sendgrid.com/en-us/blog/what-is-return-path   
	   
4) What is MTA (Mail Transfer Agent)
	
	https://mailtrap.io/blog/mail-transfer-agent/
	
5) What is MX records?
	
	https://mailtrap.io/blog/dns-mx-records/
	
6) What is DNS Zone file?
	
	
# Steps to solve:-
---

1) **Download the file :** wget "https://challenges-cdn.hackthebox.com/sherlocks/easy/TeamWork.zip/..."

2) **Unzip the file:** sudo 7z x Teamwork.zip
   
	jasonlongfield@edny.net  directory is present
	
	 In that directory there are 13 emails.


### 1) Identify the sender of the suspicious email?

#### Answer :- theodore.todtenhaupt@developingdreams.site

### 2) The suspicious email came from a custom domain, identify its creation date.

- Once you found the sender of the suspicious email , and now you want to check the creation date . So to do that go to `whois` and paste the domain to check the creation date or registration date.

#### Answer :- 2025-01-31

### 3) The domain was registered shortly before the suspicious email was received, which likely corresponds to the time when the threat actor was planning this campaign. Which MITRE ATT&CK sub-technique of the Resource Development tactic corresponds to this activity? 

#### Answer :- T1583.001


### 4) The previously identified domain appears to belong to a company, what is the full URL of the company's page on X (formerly Twitter)?

- 

#### Answer :- 