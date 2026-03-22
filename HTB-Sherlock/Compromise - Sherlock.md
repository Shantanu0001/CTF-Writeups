
# Theory part:-

- HTTP is used to make the initial connection.


---

### Why does DNS use UDP and not TCP?

1. **Speed of UDP**:
    
    - UDP is much faster than TCP because it does not require the overhead of establishing a connection (like TCP’s 3-way handshake).
        
    - DNS servers benefit from this speed since they don't need to manage connections, which reduces the load on the server.
        
2. **Small DNS Queries**:
    
    - DNS queries are typically small (less than 512 bytes), which fits well within the standard size of a UDP packet.
        
    - This makes UDP a natural fit for DNS because there’s no need for the extra complexity that TCP provides for larger data transfers.
        
3. **Connectionless Nature of UDP**:
    
    - UDP is a connectionless protocol, meaning it doesn’t require a persistent connection, which is ideal for short DNS queries that need fast responses.
        
    - TCP, being connection-oriented, would introduce unnecessary delays due to its connection setup and teardown processes.
        
4. **Application Layer Reliability**:
    
    - While UDP itself is unreliable, DNS can implement its own reliability mechanisms at the application layer, such as timeouts and retries for failed queries.
        
    - This means DNS can ensure reliability without needing to rely on TCP’s built-in mechanisms.
        
5. **Efficient Traffic Handling**:
    
    - DNS servers handle a massive volume of requests daily, and using UDP helps with scalability and efficiency.
        
    - Since UDP doesn’t require managing connections, it can handle a higher rate of incoming requests compared to TCP.
        
6. **Fallback to TCP for Larger Responses**:
    
    - If a DNS response exceeds 512 bytes, or if there are other complex operations like zone transfers, DNS switches to TCP to handle the larger data.
        
    - This ensures that DNS can still operate efficiently for both small and large responses.
        
7. **EDNS (Extension Mechanism for DNS)**:
    
    - With EDNS, DNS can use larger UDP packets (beyond the 512-byte limit), enhancing its ability to handle larger responses without relying on TCP.
        
    - This makes UDP even more capable for DNS queries.
        
8. **Alternative Use of TCP**:
    
    - Although UDP is preferred for most DNS queries, DNS can fall back to TCP in certain cases, such as for zone transfers or when the UDP packet size limit is exceeded.
        
    - Some DNS resolver implementations may choose to use TCP for all queries to avoid potential UDP issues (e.g., fragmentation or loss).
        

In summary, while DNS requires reliability, UDP is chosen due to its speed, simplicity, and scalability. DNS leverages application layer mechanisms for reliability, and only falls back to TCP when necessary, ensuring both efficiency and functionality.

More details:
https://www.techtarget.com/searchnetworking/tip/Why-does-DNS-use-TCP-Port-53-and-UDP-Port-53

# Steps to solve:-
---

1) **Download the file :** wget "https://challenges-cdn.hackthebox.com/sherlocks/easy/compromised.zip/..."

2) **Unzip the file:** sudo 7z x Compromise.zip
	
	*capture.pcap file is there.*
	
  
3) **Open the file with wireshark.**


### 1) What is the IP address used for initial access?


-  As per the theory initial access is done by http/https.
- So use filter, and analyze the http traffic.

#### Answer :- 162.252.172.54

### 2) What is the SHA256 hash of the malware?

- Go to statistics and then select protocol hierarchy. This shows the breakdown of all protocols used.

![[Screenshot 2025-05-15 090045.png]]

- According to the above image i analyzed following:- 

| Protocol/Service               | Packet %             | Byte %         | Notes                                                                           |
| ------------------------------ | -------------------- | -------------- | ------------------------------------------------------------------------------- |
| **DNS** (`Domain Name System`) | **76.1% of packets** | 37.5% of bytes | ⚠️ Very high – potential DNS tunneling or exfiltration                          |
| **UDP**                        | 76.2%                | 2.1%           | Mostly DNS traffic                                                              |
| **TCP**                        | 23.8%                | 1.7%           | Low – not typical for normal web usage                                          |
| **HTTP**                       | Only 8 packets (0%)  | 11.6%          | Suspicious given the small number of HTTP packets but high byte count           |
| **TLS**                        | 2.7%                 | 2.7%           | Normal if encrypted services are used                                           |
| **PKIX CERT** / **Media Type** | Only 1 packet each   | 11.6% (both)   | 🟥 Extremely high byte count for a single packet – possible **encoded payload** |
| **SMB/NetBIOS**                | Minimal              | Negligible     | Might be internal LAN activity                                                  |

### Analyzing above table deeper:

##### **DNS (Domain Name System)**

- **Purpose**: Resolves domain names (like `google.com`) into IP addresses.
    
- **Protocol**: Primarily uses **UDP port 53**, though can use TCP for larger queries or zone transfers.
    
- **Normal Behavior**:
    
    - Bursts of DNS requests during web browsing or software updates.
        
    - Queries are usually small in size (under 512 bytes for UDP).
    
- **Captured Traffic**:
    
    - **76.1% of packets** but only **37.5% of bytes**.
    
- **Significance**:
    
    - ⚠️ **Very high packet count** may suggest **DNS tunneling**, where attackers encode data into DNS queries/responses to bypass firewalls or exfiltrate data.
        
    - Tools like `iodine`, `dnscat2`, or `dns2tcp` abuse DNS this way.
    
##### 2. **UDP (User Datagram Protocol)**

- **Purpose**: A lightweight, connectionless transport layer protocol used for fast transmissions.
    
- **Typical Uses**: DNS, DHCP, SNMP, VoIP, and streaming.
    
- **Normal Behavior**:
    
    - Often used for small, fast, stateless communications.
        
    - DNS over UDP is normal.
    
- **Captured Traffic**:
    
    - **76.2% of packets**, but only **2.1% of bytes**.
    
- **Significance**:
    
    - Matches DNS profile, confirming DNS is the primary UDP consumer.
        
    - ⚠️ A large number of small packets — again hinting at potential DNS abuse (tunneling).
    

##### 3. **TCP (Transmission Control Protocol)**

- **Purpose**: Connection-oriented, reliable transmission protocol used for most internet communications.
    
- **Typical Uses**: HTTP, HTTPS, FTP, SSH, SMTP.
    
- **Normal Behavior**:
    
    - Manages packet ordering, retransmissions, and flow control.
        
    - Common for web, email, and file transfer protocols.
    
- **Captured Traffic**:
    
    - **23.8% of packets**, **1.7% of bytes**.
    
- **Significance**:
    
    - Very **low byte usage** for TCP, which is **not typical** in environments with normal web or application usage.
        
    - May suggest encryption or stealthy, non-browser usage (e.g., bot C2).
    

##### 4. **HTTP (Hypertext Transfer Protocol)**

- **Purpose**: The foundational protocol for unencrypted web traffic (port 80).
    
- **Normal Behavior**:
    
    - Frequent with web browsing, REST API calls, or ads/tracking services.
        
    - Each HTTP request/response pair typically involves multiple packets.
    
- **Captured Traffic**:
    
    - **Only 8 packets (0%)**, yet **11.6% of bytes**.
    
- **Significance**:
    
    - ⚠️ **Unusual** – Few packets but very high data volume suggests **bulk transfer**.
        
    - Could be a **malicious file download**, large encoded payload, or an anomaly.
        
    - May merit inspecting packet payload for suspicious activity (e.g., malware delivery, beacon).
    
##### 5. **TLS (Transport Layer Security)**

- **Purpose**: Encrypts TCP traffic to secure communication (HTTPS, SMTPS, etc.).
    
- **Ports**: Commonly seen on 443 (HTTPS), 993 (IMAPS), etc.
    
- **Normal Behavior**:
    
    - Presence is expected if encrypted communication is in use (web, email, VPN).
        
    - Packet sizes vary based on encryption negotiations and data volume.
    
- **Captured Traffic**:
    
    - **2.7% of packets**, **2.7% of bytes**.
    
- **Significance**:
    
    - **Normal range** – No immediate concern.
        
    - If this traffic occurs to unknown IPs/domains, it could be **encrypted command-and-control (C2)** communication.
    
##### 6. **PKIX CERT / Media Type**

- **Purpose**:
    
    - **PKIX CERT**: Likely refers to a `certificate` object shared during TLS negotiation or standalone certificate delivery.
        
    - **Media Type**: Possibly refers to `Content-Type` in HTTP headers indicating file type (e.g., `application/pdf`, `image/png`, etc.).
    
- **Captured Traffic**:
    
    - **Only 1 packet each**, but **11.6% of bytes** each (very large single packets).
    
- **Significance**:
    
    - 🟥 Extremely **suspicious**. Each packet carries a huge payload.
    
    - Could be:
        
        - An **encoded payload** (e.g., malware, exfiltrated archive).
            
        - A **data blob** wrapped in `certificate/media-type` to evade detection.
            
        - An anomaly or manipulation in traffic (stealth file delivery).
            
    - Needs **deeper inspection** (e.g., payload analysis with Wireshark).
    
##### 7. **SMB/NetBIOS**

- **Purpose**: Used for **Windows file and printer sharing**, and NetBIOS is used for LAN name resolution.
    
- **Ports**: 137–139 (NetBIOS), 445 (SMB).
    
- **Normal Behavior**:
    
    - Present in Windows LAN environments for sharing, authentication, discovery.
    
- **Captured Traffic**:
    
    - **Minimal/Negligible**.
    
- **Significance**:
    
    - Likely **internal LAN chatter**.
        
    - Low priority unless there's evidence of lateral movement or SMB exploit attempts (e.g., EternalBlue).
---



- So we first filter and analyze the http traffic.
- After the http filtering you see the IP address that is 162.252.172.54 which was used for initial access.
- There you will see the malicious link, copy the link and analyze it in virustotal/hybrid-analysis.
- After analyzing there you will get SHA256 of the malware.
#### Answer :- 9b8ffdc8ba2b2caa485cca56a82b2dcbd251f65fb30bc88f0ac3da6704e4d3c6


### 3) What is the Family label of the malware?

- In virustotal/hybrid-analysis you will find the family in the detection section under Crowdsourced context mentioned in HIGH or you can find in the community section.
#### Answer :- Sophos - Pikabot


### 4) When was the malware first seen in the wild (UTC)?

#### Answer :- 
