# Log Analysis

## Analyze SSH Log File

### SSH (Easy) Write-Up

<img width="879" height="792" alt="image" src="https://github.com/user-attachments/assets/99f5d0e6-69b4-4416-bdd4-cfcc0d8e4946" />


1. hostname = `myraptor`
2. first IP address to attack the server = `169.139.243.218`
3. second IP address to attack the server = `56.13.188.38`
4. third IP address to attack the server = `30.167.206.91`
5. user was targeted in the attack = `harvey` (poor harvey 🥲)
6. IP address in which the attacker was able to successfully log in = `30.167.206.91`


```
Oct 11 10:36:58 myraptor sshd[30001]: Connection from 30.167.206.91 port 55326

Oct 11 10:36:59 myraptor sshd[30003]: Accepted password for harvey from 30.167.206.91 port 55326 ssh2

Oct 11 10:36:59 myraptor sshd[30005]: pam_unix(sshd:session): session opened for user harvey by (uid=0)
```

<br>

## Log Analysis & Filter Commands

### 🛠️ Tools & Commands

1. `wc -l` = count lines of output
2. `head [options]` = displaying specific lines of output
3. `cut -f [field #]`  = cutting and extracting only specific fields/columns
4. `sort [options]` = sorting 
5. `uniq [options]` = displaying unique entries
### Login (Easy) Write-Up

- *Analyze a custom application login event log to help us understand user behavior: login.log*

<img width="634" height="874" alt="image" src="https://github.com/user-attachments/assets/52e2cd39-8dc4-4b27-bcaf-fa7e96d227fa" />


> Example Log Above :)

1. to see many total login attempts were made in this log, there are THREE ways:
	1. `$ wc -l login.log`
	2. `$ head -n -0 login.log | wc -l`
	3. `$ cat login.log | wc -l`

2. to see how many unique usernames appearing, run: `$ cat login.log | cut -f 3 | sort | uniq | wc -l`
	1. `cat login.log`: displays ALL contents
	2. `cut -f 3`: only displays column/field #3 which is the usernames
	3. `sort`: sorts alphabetical order
	4. `uniq`: displays unique entries
	5. `wc -l`: counts all lines

3. to see the username with the most login attempts, run: `$ cat login.log | cut -f 3 | sort | uniq -c | sort -n`
	1. `cat login.log`: displays ALL contents
	2. `cut -f 3`: only displays column/field #3 which is the usernames
	3. `sort`: sorts alphabetical order
	4. `uniq -c`: displays unique entries and the number of times that entry occurs
	5. `sort -n`: displays in numerical order (least - greatest)

4. to see the date with the most login attempts, run: `$ cat login.log | cut -d " " -f 1 | uniq -c | sort -n`
	1. `cat login.log`: displays ALL contents
	2. `cut -d " " -f 1`: split/divides the line by spaces and extract the first field, in other words, this ONLY displays the date only!
	3. `uniq -c`: displays unique entries and the number of times that entry occurs
	4. `sort -n`: displays in numerical order (least - greatest)

5. to see the username that had logins from the most unique IP addresses, run: `$ cat login.log | cut -f 2,3 | sort | uniq | cut -f 2 | sort | uniq -c | sort -n`
	1. `cat login.log`: displays ALL contents
	2. `cut -f 2,3`: displays the 2nd field (IP addresses) and 3rd field (usernames)
	3. `sort`: sorts the IP/Usernames
	4. `uniq`: displays unique entries
	5. `ctf -f 2`: extract just the usernames from each pair
	6. `sort`: sort the usernames
	7. `uniq -c`: get frequency count of unique pairs
	8. `sort -n`: displays frequencies and in numerical order (least - greatest)

<br>

## VSFTPD Log Analysis & More Filter Commands

### 🛠️ Tools & Commands

1. `awk -F '[options]'`: custom delimiter
2. `awk '{x+=$[#]} END {print x}'`: adding/totaling specific columns
3. `grep [options]`: finding specific word from each line

### VSFTPD (Easy) Write-Up

- *Analyze a vsftpd log file that we obtained: vsftpd.log*

<img width="919" height="436" alt="Screenshot 2026-10-10 145902" src="https://github.com/user-attachments/assets/a5391e86-d4bb-44ea-b366-973476c262fd" />

<img width="919" height="449" alt="Screenshot 2026-10-10 145944" src="https://github.com/user-attachments/assets/49cfe8f2-810b-4514-8178-587cabc30466" />

> Example Log Above :)

1. finding IP address "ftpuser" first logged in from: `$ cat vsftpd.log | grep 'ftpuser'`
	1. `cat vsftpd.log`: displays ALL contents
	2. `grep ftpuser`: displays ALL lines containing `ftpuser`

2. finding first directory that ftpuser created: `$ cat vsftpd.log | grep 'ftpuser' | grep -i 'mkdir' | head -n 1`
	1. `cat vsftpd.log`: displays ALL contents
	2. `grep 'ftpuser'`: displays ALL lines containing `ftpuser`
	3. `grep -i 'mkdir'`: then, displays ALL lines containing "mkdir" (`-i` ignores the case)
	4. `head -n 1`: displays the first line of the pipe stream!

3. finding last directory that ftpuser created: `$ cat vsftpd.log | grep 'ftpuser' | grep -i 'mkdir' | tail -n 1`
	1. `cat vsftpd.log`: displays ALL contents
	2. `grep 'ftpuser'`: displays ALL lines containing `ftpuser`
	3. `grep -i 'mkdir'`: then, displays ALL lines containing "mkdir" (`-i` ignores the case)
	4. `tail -n 1`: displays the last line of the pipe stream!

4. finding file extension was the most used by ftpuser: `$ cat vsftpd.log | grep 'ftpuser' | grep 'OK UPLOAD' | awk -F ',' '{print $2} | awk -F '.' '{print $2}' | sort | uniq -c | sort -n
	1. `cat vsftpd.log`: displays ALL contents
	2. `grep 'ftpuser'`: displays ALL lines containing `ftpuser`
	3. `grep 'OK UPLOAD'`: then, displays ALL lines containing "OK UPLOAD" (ftpusers's file uploads)
	4. `awk -F ',' '{print $2}'`: sets custom delimiter to split at the symbol "," and outputs/displays the second (`$2`) section of that division
	5. `awk -F ',' '{print $2}'`: sets custom delimiter to split at the symbol "." and outputs/displays the second (`$2`) section of that division
	6. `sort`: sorts alphabetically
	7. `uniq -c`: displays only the unique entries with count
	8. `sort -n`: then finally sorts by numerical order

5. finding username of the other user in this log: `$ cat vsftpd.log | awk '{print $8}' | sort | uniq -c`

> `awk '{print $[#]}'`: only displays/outputs a specific column/section

6. finding IP address the other user logged in from: `$ cat vsftpd.log | grep 'jimmy' | head -n 1` OR just simply: `$ cat vsftpd.log | grep 'jimmy'`

7. finding how many TOTAL bytes jimmy UPLOADED: `$ cat vsftpd.log | grep 'jimmy' | grep 'OK UPLOAD' |  awk -F ',' '{print $3}' | awk '{x+=$1} END {print x}'`

> `awk '{x+=$1} END {print x}'`: adds/totals up ALL values of `$1` (section 1, which is the byte numbers) into variable `x`, ends operation, then print `x`.

8. finding how many TOTAL bytes ftpuser UPLOADED: `$ cat vsftpd.log | grep 'ftpuser' | grep 'OK UPLOAD' |  awk -F ',' '{print $3}' | awk '{x+=$1} END {print x}'` (Same as #7)

9. finding the IP address of the suspicious login (the login with no subsequent activity): `$ cat vsftpd.log | awk '{print $12}' | sort | uniq -c` OR `$ cat vsftpd.log | grep 'OK LOGIN' | awk -F '"' '{print $2}' | sort | uniq`

<br>

## Sysmon Logs from JSON file

### Sysmon (Easy) Write-Up

- *Here's a section of parsed Sysmon logs in a JSON format showing information about processes executed on a user’s computer.: processtree.json*

<img width="922" height="389" alt="Screenshot 2026-10-10 150421" src="https://github.com/user-attachments/assets/faafc936-b682-4480-be66-d57639a50be5" />


> Example log above :)

1. finding name of the bootstrap executable downloaded via the browser: `$ cat processtree.json | grep -i '.exe' | grep -i 'http'`

2. finding name of the main agent executable launched after the bootstrap runs: `$ cat processtree.json | grep -i 'agent'` > search for another agent executable (.exe) name that is not the previous one

3. finding PowerShell cmdlet used to create the firewall rules for the agent’s traffic: `$ cat processtree.json | grep -i 'firewall'` > Every PowerShell cmdlet follows a strict "Verb-Noun" pattern separated by a hyphen (EX: `Get-Process` or `New-Service`) > `New-NetFirewallRule`!!!

4. finding Windows program that is executed to likely verify that the agent is running > `taskmgr.exe` (Task Manager)

5. finding the process ID of the executable used to enable Windows Defender: 
	1. `$ cat processtree.json` > look for the first reference/instance of a "Defender" > look at the process ID (`process_id`) of that!

```JSON
{
    "timestamp": "2024-07-15 09:02:59",
    "process_name": "MpCmdRun.exe",
    "process_id": 1704,
    "parent_process_id": 7196,
    "image": "C:\\Program Files\\Windows Defender\\mpcmdrun.exe",
    "command_line": "\"C:\\Program Files\\Windows Defender\\mpcmdrun.exe\" -wdenable",
    "md5": "B3676839B2EE96983F9ED735CD044159"
}
```

6. finding many seconds after the last PowerShell firewall rule command was Defender enabled:
	1. look the `timespamp` of the last time a command for a PowerShell firewall rule was enabled > `09:02:13`
	2. look at the `timestamp` of the first time Windows Defender was enabled (from #5) > `09:02:59`
	3. find # of seconds between each > subtract the seconds `59 - 13 = 46 seconds`! 

```JSON
 {
    "timestamp": "2024-07-15 09:02:13",
    "process_name": "powershell.exe",
    "process_id": 7644,
    "parent_process_id": 7184,
    "image": "C:\\Windows\\Sysnative\\WindowsPowerShell\\v1.0\\powershell.exe",
    "command_line": "C:\\Windows\\Sysnative\\WindowsPowerShell\\v1.0\\powershell.exe -Command \"New-NetFirewallRule -DisplayName 'LayerAgent Service' -Name 'LayerAgent Service Command' -Direction Inbound -Program 'C:\\Program Files (x86)\\LayerAgent\\layer_agent_svc.exe' -Action Allow\"",
    "md5": "04029E121A0CFA5991749937DD22A1D9"
  }
  
  ...
  
  {
    "timestamp": "2024-07-15 09:02:59",
    "process_name": "MpCmdRun.exe",
    "process_id": 1704,
    "parent_process_id": 7196,
    "image": "C:\\Program Files\\Windows Defender\\mpcmdrun.exe",
    "command_line": "\"C:\\Program Files\\Windows Defender\\mpcmdrun.exe\" -wdenable",
    "md5": "B3676839B2EE96983F9ED735CD044159"
}
```

<br>

## Nginx Log Analysis

### Nginx (Medium) Write-Up

- *Analyze a nginx access log: access.log*

<img width="1848" height="847" alt="image" src="https://github.com/user-attachments/assets/93add60a-c72a-40d2-900b-55ded0931da5" />


> Example log above

1. finding how many different IP addresses reached the server (TWO ways): 
	1. `$ cat access.log | awk -F '- -' '{print $1}' | sort | uniq | wc -l`
	2. `$ cat access.log | cut -d ' ' -f 1 | sort | uniq | wc -l`

2. finding how many requests yielded a 200 status: `$ cat access.log | cut -d '"' -f 3 | cut -d ' ' -f 2 | sort | uniq -c | sort -n`

3. finding how many requests yielded a 400 status: `$ cat access.log | cut -d '"' -f 3 | cut -d ' ' -f 2 | sort | uniq -c | sort -n`

4. finding IP address rang at the doorbell: `$ cat access.log | grep "bell"`

5. finding version of the Googlebot visited the website: `$ cat access.log | grep "bot"`

6. finding IP address attempted to exploit the shellshock vulnerability: 
	1. look up "what does shellshock vulnerability look like in access log" > `() { :; };`
	2. then run: `$ cat access.log | grep "() { :; };"`

7. finding the most popular version of Firefox used for browsing the website: `$ cat access.log | grep -o "Firefox/.*" | sort | uniq -c`
	1. `grep -o`: stands for "only matching" (instead of printing whole line, it only prints exact match)
	2. `"Firefox/.*"`: the `.*` means match any character (`.`) any number of times (`*`).


8.  finding the most common HTTP method used (TWO ways):
	1. `$ cat access.log | cut -d ' ' -f 6 | sort | uniq -c | sort -n
	2. `$ cat access.log | awk -F " " '{print $6}' | sort | uniq -c | sort -n

9. finding many requests were for \x04\x01\x00P\xC6\xCE\x0Eu0\x00: `$ cat access.log | grep '\\x04\\x01\\x00P\\xC6\\xCE\\x0Eu0\\x00' | wc -l`

<br>

## SQLite Database Log File Analysis

### 🛠️ Tools & Commands

1. https://sqliteviewer.app/ (SQLite Viewer App)
2. https://beta.sqliteviewer.app/ (BETA - but better?)
3. https://kb.mozillazine.org/Places.sqlite (SQLite wiki - help narrow down which tables to look through)
4. `$ sqlite3 [file]`
	1. `sqlite> .tables` = displays all the database tables available to view
	2. `sqlite> SELECT * FROM [table name];` = views the information contained in the tables. (The `*` (wildcard) indicates to select all the columns, and the `;` (semicolon) at the end of the query signals the end of the query.
	3. `sqlite> PRAGMA table_info([table name]);`= list of the columns in tables
5. https://www.blockchain.com/explorer

### History (Medium) Write-Up

- *Analyze a Firefox SQLite history database: browser.sqlite*

1. finding what user searched for on craigslist:
	1. `$ sqlite3 browser.sqlite`
	2. first, display all the tables by running the `.tables` command: `sqlite> .tables`
	3. then list of the columns in the`sqlite> PRAGMA table_info(moz_places);`
	4. looks like we go to "url" column, so to find what the user searched on craigslist, we can use SQL operators and run: `sqlite> select * from moz_places where url like '%bitcoin%';`
	5. `http://baltimore.craigslist.org/search/sss?sort=rel&query=bitcoin`!!!

2. finding current price of bitcoin when the user was browsing:
	1. since we are trying to find a price, we can use the $ symbol, and since there isn't any in the "url" column, we can look at the "title" column.
	2. `sqlite> select * from moz_places where title like '%$bitcoin$%';`
	3. `https://www.bitstamp.net/|($239.5) Bitstamp - buy and sell bitcoins`

3. finding Bitcoin exchange the user logged in to:
	1. look through the urls: `sqlite> select url from moz_places;`
	2. find where the user logs in > Coinbase!!!

4. finding the email that was used to log into the exchange:
	1. throughout the urls, we can see that the user uses a google email, so the email must have "gmail" > after searching with operators in the url column, we cannot find an email
	2. we can try the "title" column next: `sqlite> select * from moz_places where title like '%gmail%';` 
	3. `b1gbird@gmail.com`

5. finding the ID of the Bitcoin transaction that the user looked at:
	1. `sqlite> select * from moz_places where url like '%transaction%';`
	2. after doing those operations, we can find one result which shows the transaction ID! > `https://www.coinbase.com/network/transactions/5274cfba585a4b5681527a37f95c76340428916bb7480cef6c545f0a28dcd2d7` > transaction ID = `5274cfba585a4b5681527a37f95c76340428916bb7480cef6c545f0a28dcd2d7`

6. finding the total BTC of all the inputs of the Bitcoin transaction:
	1.   https://www.blockchain.com/explorer > search up the transaction ID from #5: `5274cfba585a4b5681527a37f95c76340428916bb7480cef6c545f0a28dcd2d7` > click "BTC" > look at "advanced details" > find "Input Value"
	2. `0.22616302 BTC`

7. finding bitcoin address receiving the majority of the Bitcoin in the transaction:
	1. on the "Overview" section (continuing from #6), we can see the "to" and "from" bitcoin transactions > looking at the "to" (which is the receiving end of the bitcoin transaction), we should look for the address that is receiving the MOST money > `18z6bTFjxkXCmhfp8YBetR2wgmoVjXGJZz`

<br>

## Squid Proxy Log Analysis 

### 🛠️ Tools & Commands

1. https://www.epochconverter.com/ (Epoch Date Converter)
2. https://wiki.squid-cache.org/Features/LogFormat (Squid Log Format)

### Squid (Hard) Write-Up

- *Analyze this Squid proxy log: squid_access.log* 

1. In what year was this log saved?
	1. display contents: `$ cat squid_access.log`
	2. usually the first field on the log is the date, but it looks like it is using epoch time format > to convert this, we can use online epoch converter (https://www.epochconverter.com/)

2. How many milliseconds did the fastest request take?
	1. looking up the format of a squid log ([https://wiki.squid-cache.org/Features/LogFormat](https://wiki.squid-cache.org/Features/LogFormat)) shows that the field (2nd one) after the timestamp represents the time spent in milliseconds!
	2. `$ cat squid_access.log | awk -F ' ' '{print $2}' |  sort -rn`

3. How many milliseconds did the longest request take?
	1. `$ cat squid_access.log | awk -F ' ' '{print $2}' | sort -n`

4. How many different IP addresses did the proxy service in this log?
	1. `$ cat squid_access.log | awk -F ' ' '{print $3}' | sort | uniq | wc -l`

5. How many GET requests were made?
	1. `$ cat squid_access.log | awk -F ' ' '{print $6}' | grep "GET" | wc -l`

6. How many POST requests were made?
	1. `$ cat squid_access.log | awk -F ' ' '{print $6}' | grep "POST" | wc -l`

7. What company created the antivirus used on the host at 192.168.0.224?
	1. `$ cat squid_access.log | grep "192.168.0.224"`
	2. The antivirus is using "Symantec Live Update", which downloads updates for Symantec/Norton antivirus products! > Symantec

8. What url is used to download an antivirus update?
	1. `$ cat squid_access.log | grep "192.168.0.224"` > `http://liveupdate.symantecliveupdate.com/streaming/norton$202009$20streaming$20virus$20definitions_1.0_symalllanguages_livetri.zip`




