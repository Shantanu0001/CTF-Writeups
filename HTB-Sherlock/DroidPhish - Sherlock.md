
# Steps to solve:-
---

1) **Download the file :** wget "https://challenges-cdn.hackthebox.com/sherlocks/easy/DroidPhish.zip/..."

2) **Unzip the file:** sudo 7z x DroidPhish.zip

	 *DroidPhish.dd file is there.* 
	

3) **Determine file type :** file DroidPhish.dd

	 DroidPhish.dd: Linux rev 1.0 ext4 filesystem data, UUID=e98cc545-b7fe-4ba9-8b33-2fb9bba476d6, volume name "Android-x86" (needs journal recovery) (errors) (extents) (large files)
	 
	 *what is this means?*
	 
	-  **Linux rev 1.0 ext4 filesystem data**: The file is an ext4 filesystem, which is a common filesystem format used in Linux systems.
    
	- **UUID=e98cc545-b7fe-4ba9-8b33-2fb9bba476d6**: This is the unique identifier for the filesystem (UUID), which is used to identify the filesystem on the system.
    
	- **volume name "Android-x86"**: The filesystem is labeled as "Android-x86", which indicates this is likely an image from an Android system running on an x86 architecture (instead of ARM, which is common for Android devices).
    
	- **(needs journal recovery)**: This suggests that the filesystem was not properly unmounted, and it might need some recovery actions before it can be used. ext4 uses a journaling feature to track changes, and if the system crashed or was abruptly shut down, the journal might need recovery.
    
	- **(extents)**: This indicates that the filesystem uses extents to track file data, which is more efficient than the traditional block mapping.
    
	- **(large files)**: The filesystem supports large files, which is typical for modern ext4 filesystems.


4) 



#####  I use tool called autopsy to solve this.

### 1) Provide the last boot time of the device in UTC format.

- 

### File location:

> /data/android-9.0-r2/data/misc/bootstat/last_boot_time_utc

This file likely contains information regarding the last boot time in UTC, as indicated by its name. The associated time in the data is:

- **Written Time**: 2024-11-15 23:48:22 (IST)
    
- **Access Time**: 2024-11-15 23:48:22 (IST)
    
- **Change Time**: 2024-11-24 17:35:19 (IST)

> Convert it into UTC:

- **Written Time (UTC)**: 2024-11-15 18:18:22 UTC
- **Access Time (UTC)**: 2024-11-15 18:18:22 UTC
- **Change Time (UTC)**: 2024-11-24 12:05:19 UTC

#### Answer :- 2024-11-24 12:05:19
---

### 2) The user was exposed to a phishing attack. Provide the name of the email app used as the attack vector.

-  

#### Answer :- Proton mail

---

### 3) Provide the title of the phishing email.

- 

Subject is in the MIME encoded-word format (UTF-8/QP), so we need to convert in Plain-text. So to convert i am using python one liner.

>python3 -c 'from email.header import decode_header; print(decode_header("=?utf-8?Q?Celebrating_3_Years_of_Success_=E2=80=93_Thank_You!?=")[0][0].decode())'
>

>  Celebrating 3 Years of Success – Thank You!

#### Answer :- Celebrating 3 Years of Success – Thank You!

---

### 4) Provide the time in UTC when the phishing email was received.

- Analyze the email it is mentioned in that.

#### Answer :- 2024-11-24 17:04:42


### 5) Provide the download URL for the malicious application.

#### Answer :- 

### 6) 

---

# *Things that i had done to reach answers* 

## For the first question, 
---

After determining the file type.

> *Mount the Filesystem from the Disk Image*

We want to access the file system and look for files that contain boot logs.

	-> mkdir mnt_droid
	-> sudo mount -o ro,loop DroidPhish.dd mnt_droid
	
	 > This mounts the .dd image in read-only mode at mnt_droid/.
	

> **Error:-**
> 
> mount: /home/helix/htb_15_droid/mnt_droid: cannot mount /dev/loop0 read-only. dmesg(1) may have more information after failed mount system call.

The error you're seeing typically means that the file system inside the image (`DroidPhish.dd`) is either:

- **corrupted**,
- requires **journal recovery**, or
- the image doesn't begin directly with the filesystem (i.e., it has a partition table, and you need to mount an _offset_ inside the image).

> **Troubleshoot :-**

1) **Check If the Image Has Partitions**

		 sudo fdisk -l DroidPhish.dd

**OUTPUT:** ''Disk DroidPhish.dd: 2.5 GiB, 2681409024 bytes, 5237127 sectors Units: sectors of 1 * 512 = 512 bytes Sector size (logical/physical): 512 bytes / 512 bytes I/O size (minimum/optimal): 512 bytes / 512 bytes

*what is means?*
 
- No partitions were detected inside `DroidPhish.dd`.

- That means this image is a **flat disk image** — the ext4 filesystem starts right at the beginning of the file, with **no partition table**.





