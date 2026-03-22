

# Theory part:- 
---

## What is S3?

Amazon S3 is a **fully managed object storage service** that allows you to store and retrieve unlimited data, at any time, from anywhere on the web. Unlike file systems or block storage, S3 is designed to manage data as **objects**, each of which contains the data itself, metadata, and a unique identifier.

## **Core Concepts**

### ✅ Buckets

- A **bucket** is a container for storing objects (like folders in a filesystem
- Each bucket is **globally unique** and created in a specific AWS region.
### ✅ Objects

- An **object** is the actual data stored in S3.
- Components of an object:
    - **Key** (unique identifier or file name within a bucket)
    - **Value** (actual data, from 0 bytes to 5 TB)
    - **Metadata** (info like content-type, creation date, etc.)
    - **Version ID** (if versioning is enabled)


## **Storage Classes**

Amazon S3 offers multiple **storage classes** to optimize cost and performance:

| Storage Class                      | Use Case                                    | Durability    | Availability  | Cost     |
| ---------------------------------- | ------------------------------------------- | ------------- | ------------- | -------- |
| S3 Standard                        | Frequent access                             | 99.999999999% | 99.99%        | High     |
| S3 Intelligent-Tiering             | Auto-moving between tiers based on usage    | Same as above | Same as above | Medium   |
| S3 Standard-IA (Infrequent Access) | Less frequent access                        | Same          | 99.9%         | Lower    |
| S3 One Zone-IA                     | Infrequent access, one availability zone    | Lower         | 99.5%         | Lower    |
| S3 Glacier                         | Archive storage, retrieval in minutes/hours | High          | Low           | Very low |
| S3 Glacier Deep Archive            | Long-term archival, retrieval in hours      | High          | Very low      | Cheapest |

### Common Use Cases:

- Website hosting (static websites)
- Backup and restore
- Data archiving
- Big data analytics storage
- Software delivery and updates
- Hosting media content (videos, images, etc.)

## **Security in Amazon S3**

Amazon S3 includes robust security features:

### Access Control

- **IAM Policies** – Grant permissions at the user/group level.
- **Bucket Policies** – JSON-based rules applied directly to buckets.
- **ACLs (Access Control Lists)** – Legacy method for access control.
- **S3 Block Public Access** – Prevents accidental public exposure.

### Encryption

- **Server-Side Encryption (SSE)**:
    - SSE-S3: Managed keys by AWS
        
    - SSE-KMS: Customer Master Keys using AWS KMS
        
    - SSE-C: Customer-provided keys
        
- **Client-Side Encryption**: Data is encrypted before upload.

---
## What is PCAP (Packet Capture)


It is a file format used to **store network traffic data** captured over a computer network. It contains raw packet data that can be analyzed to understand what happened on the network during the capture period.

It is a **binary file format** .

### PCAP File Structure

Each PCAP file is made up of two types of blocks:

1. **Global Header (24 bytes)** – appears only once at the beginning
2. **Packet Records (variable size)** – repeat for each captured packet

---
# Steps to solve:-
---
1) **Download the file :** wget "https://challenges-cdn.hackthebox.com/sherlocks/very-easy/Origins.zip/..."

2) **Unzip the file:** sudo 7z x  Origin.zip

	 *there is pcap file, so open wireshark to analyze the content.*
	

3) What is the attacker's IP address?
	In the question it is mentioned that root cause is FTP. So look at the FTP protocol and look at the destination address. 
	
	So the answer is : 15.206.185.207
	
4) 
5) 