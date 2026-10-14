# SPL-Commands

## What is SPL?
SPL stands for Search Processing Language. It is a proprietary query language developed by Splunk and is used to search, filter, analyze, and visualize data stored in a Splunk index.

## How SPL Works (The Pipeline Concept)
SPL is heavily inspired by the Unix pipeline (|) and SQL. Instead of writing one massive, complex query, you build an analysis by chaining commands together using pipes (|).

Each command takes the output of the previous command, processes it, and passes the result down the line.

**Example Pipeline:**
- index=security sourcetype=linux_secure failed password 
- | stats count by user 
- | sort - count 
- | head 5
  ##
- **Step 1** **(index=...) :** Searches the logs for failed password attempts in Linux security logs.

- **Step 2** **(|stats count by user) :** Aggregates the results, counting how many failed attempts happened for each username.

- **Step 3** **(|sort - count) :** Sorts the results in descending order (highest count first).

- **Step 4** **(|head 5) :** Keeps only the top 5 users with the most failed logins.

##
## 1] source *ssh_logs_new.json" host "Pratiksha" sourcetype" json" |stats count AS "Failed Login"
 **STATS COMMAND**
-  The stats command is a transforming command that performs statistical calculations on your search results. Instead of looking at thousands of individual, raw log events one by one, stats aggregates that data and turns it into clean, summarized numerical metrics and tables.

 **Why do we use the stats command?**
- We use stats whenever we want to answer questions like “How many?”, “What is the average?”, or “Who is doing this the most?”

 Detecting Brute-Force Attacks: Grouping failed login logs by username or source IP to see who has the highest number of failures.
-  index=security sourcetype=linux_secure failed password 
- | stats count by user, src_ip 
- | sort - count

<img width="1262" height="638" alt="stat 1" src="https://github.com/user-attachments/assets/54e16ac2-f529-47a5-b21c-c78ba503d878" />

**Key Elements & Content**

 **Search Query:**
- source="ssh_logs_new.json" host="Pratiksha" sourcetype="_json" | stats count AS "Failed Login"
**Event Count & Time:**
- 24,000 events recorded before 10/14/26 2:17:31.000 PM.  
**Results Tab:**
-  The Statistics (1) tab is selected.  
**Result Data:**
-  Under the column header Failed Login, the value displayed is 24000.  
**Environment:**
- The laptop is running Windows 11 (visible taskbar at the bottom) on a DELL laptop.

##

## 2]  source="ssh_logs_new.json" host="Pratiksha" sourcetype="_json" |stats count by event_type

**Why use it?**
- Instead of just showing a raw list of log lines, stats count by event_type summarizes the data into a clean, easy-to-read table showing how many times each specific category of event happened.

**stats:**
- This is a transforming command in Splunk that performs statistical calculations (like counting, averaging, or summing).  
**count:** 
- This counts the total number of occurrences for each group.  
**by event_type:** 
- This tells Splunk to group the results according to the unique values found in the event_type field (such as Failed SSH Login, Successful SSH Login, etc.).

<img width="1353" height="632" alt="2" src="https://github.com/user-attachments/assets/6c582f8d-9e6d-4f3f-8829-cca6901696a2" />

  ## 3] source-"ssh_logs_new.json"host-"Pratiksha" sourcetype" json" |table username event type id.org_h 
  
  **What is table command?**
  - In Splunk (SPL), table is a transforming command used to format search results into a clean, easy-to-read tabular format (with rows and columns).

    <img width="1355" height="608" alt="3" src="https://github.com/user-attachments/assets/e52ee0b4-8a7f-45f3-9931-ca7fd23336ac" />

    **Step-by-Step Execution Process**
**Step 1:** Filtering Log Events (Base Search)
source="ssh_logs_new.json" host="Pratiksha" sourcetype="_json"

Splunk searches through raw logs and filters only those matching:
- **Source file:** ssh_logs_new.json  
- **Host name:** Pratiksha  
- **Source type:** _json  
This step fetches **24,000 total events.**


**Step 2:** Passing Data via the Pipe (|)
- The pipe operator (|) takes the 24,000 raw events returned by Step 1 and feeds them into the next command.

  **Step 3:** Formatting with table
  | table username event_type id.org_h
  **Purpose:**
  - By default, Splunk shows full raw log entries with dozens of extra fields (_time, host, source, sourcetype, etc.). The table command hides all unselected fields and formats the output into a clean tabular structure.

  ##
 ## 4] source "ssh_logs.new.json" host-"Pratiksha" sourcetype" json" | stats count by username |sort - count
  **What is the sort Command?**
- The sort command in Splunk is used to order the search results based on the values of specified fields (either in ascending or descending order).
**(| sort - count):**
- The minus sign (-) indicates descending order (from highest to lowest).
- count specifies the field to sort by.
- Therefore, | sort - count arranges the usernames so that the ones with the highest number of log events appear at the very top.

  **Why Do We Use the sort Command?**
  - We use sort to organize data and spot suspicious or high-volume activity immediately.
**What it is:**
-  A total number that tracks how many log events occurred for each unique username.  
**Why we use it:**
-  To measure activity levels and quickly see which user accounts have the highest number of attempts (such as logins or login failures).

  <img width="1346" height="631" alt="4" src="https://github.com/user-attachments/assets/db0a99f4-d22f-42d2-9966-b476da6368cb" />

  ## 5] source "ssh_logs_new.json" host "Pratiksha" sourcetype"" json" | stats count by id.org_h sort-count
  
  It finds which IP address is making the most SSH connection attempts and puts it at the top of the list.
 **How It Actually Works:**
- **Find Logs:** Gets all SSH logs from host "Pratiksha".  
- **Count by IP:** Groups and counts how many total connection attempts came from each IP address (id.org_h).  
- **Sort Highest First:** Arranges the list so the IP address with the most connection attempts appears at the very top (sort - count).


<img width="1349" height="634" alt="5" src="https://github.com/user-attachments/assets/488fad72-7b6f-4551-a2f7-430ac6417e6c" />


## 6] source="ssh_logs_new.json" host="Pratiksha" sourcetype="_json"|top username

- Instantly finds and lists the usernames with the highest number of login attempts (like admin with 2080 attempts), saving you from writing a longer stats count ... | sort - count query.

<img width="1338" height="632" alt="6" src="https://github.com/user-attachments/assets/2232deab-0608-43c2-a288-43055dfc7692" />

**How It Actually Works:**

- **Find Logs:** Gets all SSH log events from host "Pratiksha".  
- **top Command:** Automatically counts how many times each username appears, sorts them from highest to lowest, and shows only the top results   along with their counts and percentages.

  
## 7] source="ssh_logs_new.json" host="Pratiksha" sourcetype="_json" | dedup username |table id.resp_h username

**What is the dedup Command?**
- dedup (short for deduplicate) removes duplicate entries so that each value appears only once in your search results.
  
  **Where Do We Use It?**
- To eliminate repetition: When a user or IP appears thousands of times in logs.  
- To get unique lists: To quickly generate a list of unique usernames,IP addresses, or assets involved in an incident.

  <img width="1346" height="633" alt="7" src="https://github.com/user-attachments/assets/73ba5b92-7410-4db3-8b6b-456082523b31" />

**How the Query Works** 
- | dedup username | table id.resp_h username  
- Keeps only one entry per unique username (removes duplicates).  
- Displays the result in a clean table showing the IP and username.
  
 **What is id.resp_h and username?**
*id.resp_h (Responder Host / Destination IP):*
In network logs (like Zeek/Bro logs, which often use this naming convention), id.resp_h represents the Destination IP address or the server responding to the connection request (in this case, your SSH server's IP or the target system hosting the service).
*username:*
Represents the user account name that was used or targeted during the login attempt (such as admin, root, john.doe, etc.).
    


  
  

  
