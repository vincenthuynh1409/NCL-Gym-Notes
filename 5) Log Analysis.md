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

<img width="611" height="439" alt="Screenshot 2026-10-15 173712" src="https://github.com/user-attachments/assets/76f37ab6-d0fc-4632-8630-19a2ae2013a3" />


> Example log above

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

<br>

## SOAP Web Server Log Analysis

### 🛠️ Tools

1. https://www.convertcsv.com/xml-to-csv.htm (XML to CSV converter)
2. Google Spreadsheets

### Payments (Hard) Write-Up

- *A payment transaction log was compromised in a data breach. Help us determine what information was stolen: payments.log* 

> SOAP is used to send messages using the Extensible Markup Language (XML). Solving this challenge requires a mechanism to parse the XML and query the underlying data. This can be done by extracting the relevant XML from the log file, converting the XML to CSV format, and then using a spreadsheet editor to query the data.

<img width="1546" height="861" alt="image" src="https://github.com/user-attachments/assets/3c923b52-ecf5-402a-8a46-57bb051092f6" />


> Example log above

Before we answer any of the questions, we have to first convert the XML file we downloaded to a CSV file > spreadsheet!
1. download the file given in the challenge
2. extract the XML and parse the entries for requests and responses by running:
	1. `$ sed -nr 's/PPAPIService: Request: (.*)/\1/p' payments.log > requests.xml`
	2. `$ sed -nr 's/PPAPIService: Response: <\?.*\?>(.*)/\1/p' payments.log > responses.xml`
3. after, you should get a `requests.xml` and `responses.xml` file. Now, to convert it from XML to CSV, you will need to add `<xml>` to the beginning and `</xml>` to the end of your XML file (and make sure to click SAVE) so it can be recognized properly by your CSV converter
4. https://www.convertcsv.com/xml-to-csv.htm > convert XML files to CSV! :D 

Now, since we have the spreadsheet, we are able to answer the questions:

1. How many transactions are contained in the log?
	1. open/import both the `requests.xml` and `responses.xml` file into Google Spreadsheets and count each line EXCLUDING the header (first row)!

2. What is the transaction ID of the largest purchase made in the log?
	1. open/import both the `requests.xml` and `responses.xml` file into Google Spreadsheets > one in one window/tab and one in another
	2. in the `requests.xml` spreadsheet, the first row is the headers and below is the transactions! > since you want to find the largest purchase, look for the column/header that indicates a price > `Body/DoDirectPaymentReq/DoDirectPaymentRequest/DoDirectPaymentRequestDetails/PaymentDetails/OrderTotal/__text` > `OrderTotal`!
	3. sort that column from Z-A > right click column letter > "Create Filter" > Sort Z-A > find the largest price, then copy it > unsort by doing `CTRL+Z` > then use `CTRL+F` to find that price, and for this example it is `998.6` > note down the original row the price was in! (in this case, row 53)
	4. then, go to your `responses.csv` spreadsheet and find transaction ID header > `Body/DoDirectPaymentResponse/TransactionID` (used `CTRL+F`) > match the row from "requests" (53) with the corresponding row in "responses" (53) in the Transaction ID header/column > get the ID: `3a4da8c8-6934-4655-9ec5-335ab4540a2b`

3. Which state made the greatest number of purchases?
	1. on "requests" spreadsheet, find the states header/column (`Body/DoDirectPaymentReq/DoDirectPaymentRequest/DoDirectPaymentRequestDetails/PaymentDetails/ShipToAddress/StateOrProvince/__text`)
	2. right click on the column header letter > click on "Column Stats" > find the state with the greatest number of purchases!

<br>

## Custom File Format Log Analysis

### Custom File Format (Hard) Write-Up

- *We need to analyze a custom log format that we use to monitor data transfers on a secure network. Unfortunately, the parser was not committed to our code repository, but we do have a copy of the file format spec. See if you can use it and help us answer questions about the log file. Provide all date/timestamps as UTC: Custom File Format.sky*

To answer all the questions all in one without creating any additional files or converting in cyberchef, I created a python script for this challenge:

> I also renamed `Custom File Format.sky` to just `file.sky` so its easier.

```python
import struct
import socket
import base64
from datetime import datetime, timezone
from collections import defaultdict

FILE = "file.sky"

with open(FILE, "rb") as f:
    data = f.read()

offset = 0

# ----- HEADER -----

magic = data[offset:offset+8]
offset += 8

version = data[offset]
offset += 1

created = struct.unpack(">I", dimport struct
import socket
import base64
from datetime import datetime, timezone
from collections import defaultdict

FILE = "file.sky"

with open(FILE, "rb") as f:
    data = f.read()

offset = 0

# ----- HEADER -----

magic = data[offset:offset+8]
offset += 8

version = data[offset]
offset += 1

created = struct.unpack(">I", data[offset:offset+4])[0]
offset += 4

hostname_len = struct.unpack(">I", data[offset:offset+4])[0]
offset += 4

hostname = data[offset:offset+hostname_len].decode()
offset += hostname_len

flag_len = struct.unpack(">I", data[offset:offset+4])[0]
offset += 4

flag_raw = data[offset:offset+flag_len]
offset += flag_len

num_entries = struct.unpack(">I", data[offset:offset+4])[0]
offset += 4

# Decode the Base64 flag
try:
    flag = base64.b64decode(flag_raw).decode()
except:
    flag = flag_raw.decode(errors="replace")
    
print("=== HEADER ===")
print("Magic:", magic.hex())
print("Version:", version)
print("Created:", datetime.fromtimestamp(created, timezone.utc))
print("Hostname:", hostname)
print("Flag:", flag)
print("Entries:", num_entries)

# ----- BODY -----

entries = []

for i in range(num_entries):

    source_raw, dest_raw, timestamp, transferred = struct.unpack(
        ">IIII", data[offset:offset+16]
    )

    offset += 16

    source = socket.inet_ntoa(struct.pack(">I", source_raw))
    destination = socket.inet_ntoa(struct.pack(">I", dest_raw))

    dt = datetime.fromtimestamp(timestamp, timezone.utc)

    entries.append({
        "source": source,
        "destination": destination,
        "timestamp": dt,
        "bytes": transferred
    })

print("\nParsed", len(entries), "entries.")

# Total bytes transferred
total_bytes = sum(e["bytes"] for e in entries)

# Unique IP addresses
unique_ips = set()

for e in entries:
    unique_ips.add(e["source"])
    unique_ips.add(e["destination"])

# Bytes sent by each SOURCE IP
bytes_by_source = defaultdict(int)

for e in entries:
    bytes_by_source[e["source"]] += e["bytes"]

top_ip, top_bytes = max(
    bytes_by_source.items(),
    key=lambda x: x[1]
)
 
# Bytes transferred per UTC day
bytes_by_day = defaultdict(int)

for e in entries:
    day = e["timestamp"].strftime("%Y-%m-%d")
    bytes_by_day[day] += e["bytes"]

busiest_day, busiest_bytes = max(
    bytes_by_day.items(),
    key=lambda x: x[1]
)
 
print("\n=== ANSWERS ===")
print("Total transferred bytes:", total_bytes)
print("Unique IP addresses:", len(unique_ips))
print("IP that sent the most data:", top_ip)
print("Bytes sent by that IP:", top_bytes)
print("Busiest day:", busiest_day)
print("Bytes transferred that day:", busiest_bytes)


```

Basically, the script reads the binary SKY log file then calculates the statistics

1. **Imports tools** for reading binary data, IP addresses, Base64, and UTC timestamps.

2. **Opens `file.sky`** in binary mode and reads all the data.

3. **Uses `offset`** to keep track of its current position in the file.

4. **Reads the header:**
    - Magic bytes
    - Version
    - Creation timestamp
    - Hostname
    - Flag
    - Number of entries

5. **Decodes the flag** from Base64.

6. **Reads each network entry** (16 bytes each):
    ```
    4 bytes → Source IP
    4 bytes → Destination IP
    4 bytes → Timestamp
    4 bytes → Bytes transferred
    ```
7. **Converts the data** into readable IP addresses and UTC dates.

8. **Calculates:**
    - Total bytes transferred
    - Number of unique IPs
    - IP that sent the most data
    - Busiest UTC day

To answer the final challenge questions, simply run: `$ python file.sky`

### Alternative way to solve "Custom File Format" (manually):

1. Open CyberChef, and click "Open file as input" > open the `Custom File Formay.sky` file
2. This [cyberchef recipe](https://cyberchef.io/#recipe=Take_bytes\(59,2592,false\)To_Hex\('None',16\)Fork\('%5C%5Cn','%5C%5Cn',false\)Subsection\('.%7B8%7D',true,true,false\)Find_/_Replace\(%7B'option':'Regex','string':'$'%7D,'%20',true,false,true,false\)Merge\(\)) can be used to create a column-formatted hex version of the log > save this result into your machine into a file called `hex.log`.
3. Once the log file is in a text format, it becomes easier to convert the data one column at a time and then rejoin the columns into an actual log!

4. Extract the first column from hex.log
	1. Convert the hex values into IPv4 addresses [using cyberchef](https://gchq.github.io/CyberChef/#recipe=Change_IP_format\('Hex','Dotted%20Decimal'\)&input=ZTVkNDE1ZDQKZTVkNDE1ZDQKYjhlZTAwMjAKYWY4NjM0NzEKNGRmZjI1M2MKNGRmZjI1M2MKYjE4NGUwZGQKZTVkNDE1ZDQKNThhYjY1ZWYKZTAzYTA3ODgKZjdjZDUwMTcKOGY2ZTIzZjAKZjdjZDUwMTcKODlkODZiNTAKNThhYjY1ZWYKNGRmZjI1M2MKNGRmZjI1M2MKNThhYjY1ZWYKMzJjN2NjYjcKYWJlOTZhNzUKODlkODZiNTAKZTVkNDE1ZDQKMTc2NTJmNjMKZTVkNDE1ZDQKYWY4NjM0NzEKYWJlOTZhNzUKMzJjN2NjYjcKZjdjZDUwMTcKMTc2NTJmNjMKODlkODZiNTAKYWY4NjM0NzEKZTVkNDE1ZDQKMTc2NTJmNjMKZTVkNDE1ZDQKNGRmZjI1M2MKZTVkNDE1ZDQKYWJlOTZhNzUKZTVkNDE1ZDQKYWJlOTZhNzUKZTVkNDE1ZDQKZTVkNDE1ZDQKNGRmZjI1M2MKNGRmZjI1M2MKODlkODZiNTAKYWY4NjM0NzEKYWJlOTZhNzUKZjdjZDUwMTcKNThhYjY1ZWYKOGY2ZTIzZjAKMTc2NTJmNjMKNThhYjY1ZWYKYWY4NjM0NzEKZTVkNDE1ZDQKODlkODZiNTAKYjhlZTAwMjAKOGY2ZTIzZjAKZTVkNDE1ZDQKNThhYjY1ZWYKOGY2ZTIzZjAKZjdjZDUwMTcKYjE4NGUwZGQKNGRmZjI1M2MKZTAzYTA3ODgKNGRmZjI1M2MKMzJjN2NjYjcKYWJlOTZhNzUKYWY4NjM0NzEKMTc2NTJmNjMKMzJjN2NjYjcKYWY4NjM0NzEKZjdjZDUwMTcKNGRmZjI1M2MKYWJlOTZhNzUKOGY2ZTIzZjAKNGRmZjI1M2MKYWY4NjM0NzEKZjdjZDUwMTcKYWJlOTZhNzUKODlkODZiNTAKODlkODZiNTAKNThhYjY1ZWYKYWJlOTZhNzUKNThhYjY1ZWYKNGRmZjI1M2MKODlkODZiNTAKYjE4NGUwZGQKZjdjZDUwMTcKZTAzYTA3ODgKMTc2NTJmNjMKMzJjN2NjYjcKYjE4NGUwZGQKMTc2NTJmNjMKNGRmZjI1M2MKYWJlOTZhNzUKZTVkNDE1ZDQKODlkODZiNTAKZTAzYTA3ODgKMzJjN2NjYjcKZTAzYTA3ODgKOGY2ZTIzZjAKOGY2ZTIzZjAKMzJjN2NjYjcKYWJlOTZhNzUKMzJjN2NjYjcKZTAzYTA3ODgKOGY2ZTIzZjAKZTAzYTA3ODgKNThhYjY1ZWYKZTVkNDE1ZDQKZTVkNDE1ZDQKYjE4NGUwZGQKODlkODZiNTAKYWY4NjM0NzEKZTVkNDE1ZDQKZjdjZDUwMTcKMTc2NTJmNjMKNThhYjY1ZWYKZTAzYTA3ODgKOGY2ZTIzZjAKZjdjZDUwMTcKYWJlOTZhNzUKYjhlZTAwMjAKMTc2NTJmNjMKYWJlOTZhNzUKZTVkNDE1ZDQKYjE4NGUwZGQKOGY2ZTIzZjAKZTVkNDE1ZDQKMTc2NTJmNjMKYjE4NGUwZGQKYWJlOTZhNzUKZTVkNDE1ZDQKZTVkNDE1ZDQKYWJlOTZhNzUKZTVkNDE1ZDQKYjE4NGUwZGQKZTAzYTA3ODgKZTAzYTA3ODgKNThhYjY1ZWYKOGY2ZTIzZjAKYWY4NjM0NzEKZTVkNDE1ZDQKYWY4NjM0NzEKZjdjZDUwMTcKZTVkNDE1ZDQKODlkODZiNTAKOGY2ZTIzZjAKZTVkNDE1ZDQKNThhYjY1ZWYKYjE4NGUwZGQKYWY4NjM0NzEKNThhYjY1ZWYKYjE4NGUwZGQKNGRmZjI1M2MKODlkODZiNTAKMTc2NTJmNjMKNThhYjY1ZWYKZTVkNDE1ZDQKYjhlZTAwMjAKYjE4NGUwZGQKZjdjZDUwMTcKYWJlOTZhNzUK)
	2. Save the results to a file named `col1.log`

```bash
cat hex.log | cut -d " " -f 1
```

4. Extract the second column from` hex.log`
	1. Convert the hex values into IPv4 addresses [using cyberchef](https://gchq.github.io/CyberChef/#recipe=Change_IP_format\('Hex','Dotted%20Decimal'\)&input=ZTVkNDE1ZDQKZTVkNDE1ZDQKYjhlZTAwMjAKYWY4NjM0NzEKNGRmZjI1M2MKNGRmZjI1M2MKYjE4NGUwZGQKZTVkNDE1ZDQKNThhYjY1ZWYKZTAzYTA3ODgKZjdjZDUwMTcKOGY2ZTIzZjAKZjdjZDUwMTcKODlkODZiNTAKNThhYjY1ZWYKNGRmZjI1M2MKNGRmZjI1M2MKNThhYjY1ZWYKMzJjN2NjYjcKYWJlOTZhNzUKODlkODZiNTAKZTVkNDE1ZDQKMTc2NTJmNjMKZTVkNDE1ZDQKYWY4NjM0NzEKYWJlOTZhNzUKMzJjN2NjYjcKZjdjZDUwMTcKMTc2NTJmNjMKODlkODZiNTAKYWY4NjM0NzEKZTVkNDE1ZDQKMTc2NTJmNjMKZTVkNDE1ZDQKNGRmZjI1M2MKZTVkNDE1ZDQKYWJlOTZhNzUKZTVkNDE1ZDQKYWJlOTZhNzUKZTVkNDE1ZDQKZTVkNDE1ZDQKNGRmZjI1M2MKNGRmZjI1M2MKODlkODZiNTAKYWY4NjM0NzEKYWJlOTZhNzUKZjdjZDUwMTcKNThhYjY1ZWYKOGY2ZTIzZjAKMTc2NTJmNjMKNThhYjY1ZWYKYWY4NjM0NzEKZTVkNDE1ZDQKODlkODZiNTAKYjhlZTAwMjAKOGY2ZTIzZjAKZTVkNDE1ZDQKNThhYjY1ZWYKOGY2ZTIzZjAKZjdjZDUwMTcKYjE4NGUwZGQKNGRmZjI1M2MKZTAzYTA3ODgKNGRmZjI1M2MKMzJjN2NjYjcKYWJlOTZhNzUKYWY4NjM0NzEKMTc2NTJmNjMKMzJjN2NjYjcKYWY4NjM0NzEKZjdjZDUwMTcKNGRmZjI1M2MKYWJlOTZhNzUKOGY2ZTIzZjAKNGRmZjI1M2MKYWY4NjM0NzEKZjdjZDUwMTcKYWJlOTZhNzUKODlkODZiNTAKODlkODZiNTAKNThhYjY1ZWYKYWJlOTZhNzUKNThhYjY1ZWYKNGRmZjI1M2MKODlkODZiNTAKYjE4NGUwZGQKZjdjZDUwMTcKZTAzYTA3ODgKMTc2NTJmNjMKMzJjN2NjYjcKYjE4NGUwZGQKMTc2NTJmNjMKNGRmZjI1M2MKYWJlOTZhNzUKZTVkNDE1ZDQKODlkODZiNTAKZTAzYTA3ODgKMzJjN2NjYjcKZTAzYTA3ODgKOGY2ZTIzZjAKOGY2ZTIzZjAKMzJjN2NjYjcKYWJlOTZhNzUKMzJjN2NjYjcKZTAzYTA3ODgKOGY2ZTIzZjAKZTAzYTA3ODgKNThhYjY1ZWYKZTVkNDE1ZDQKZTVkNDE1ZDQKYjE4NGUwZGQKODlkODZiNTAKYWY4NjM0NzEKZTVkNDE1ZDQKZjdjZDUwMTcKMTc2NTJmNjMKNThhYjY1ZWYKZTAzYTA3ODgKOGY2ZTIzZjAKZjdjZDUwMTcKYWJlOTZhNzUKYjhlZTAwMjAKMTc2NTJmNjMKYWJlOTZhNzUKZTVkNDE1ZDQKYjE4NGUwZGQKOGY2ZTIzZjAKZTVkNDE1ZDQKMTc2NTJmNjMKYjE4NGUwZGQKYWJlOTZhNzUKZTVkNDE1ZDQKZTVkNDE1ZDQKYWJlOTZhNzUKZTVkNDE1ZDQKYjE4NGUwZGQKZTAzYTA3ODgKZTAzYTA3ODgKNThhYjY1ZWYKOGY2ZTIzZjAKYWY4NjM0NzEKZTVkNDE1ZDQKYWY4NjM0NzEKZjdjZDUwMTcKZTVkNDE1ZDQKODlkODZiNTAKOGY2ZTIzZjAKZTVkNDE1ZDQKNThhYjY1ZWYKYjE4NGUwZGQKYWY4NjM0NzEKNThhYjY1ZWYKYjE4NGUwZGQKNGRmZjI1M2MKODlkODZiNTAKMTc2NTJmNjMKNThhYjY1ZWYKZTVkNDE1ZDQKYjhlZTAwMjAKYjE4NGUwZGQKZjdjZDUwMTcKYWJlOTZhNzUK)
	2. Save the results to a file named `col2.log`

```bash
cat hex.log | cut -d " " -f 2
```

5. Extract the third column from `hex.log`
	1. Convert the hex values into dates [using cyberchef](https://gchq.github.io/CyberChef/#recipe=Fork\('%5C%5Cn','%5C%5Cn',false\)From_Base\(16\)Translate_DateTime_Format\('UNIX%20timestamp%20\(seconds\)','X','UTC','DD-MM-YYYY%20','UTC'\))
	2. Save the results to a file named `col3.log`

```bash
cat hex.log | cut -d " " -f 3
```

6. Extract the fourth column from `hex.log`
	3. Convert the hex values into integers [using cyberchef](https://gchq.github.io/CyberChef/#recipe=Fork\('%5C%5Cn','%5C%5Cn',false\)From_Base\(16\))
	4. Save the results to a file named `col4.log`

```bash
cat hex.log | cut -d " " -f 4
```

7. Combine and merge the logs into a single file named `merged.log`

```bash
paste col1.log col2.log col3.log col4.log > merged.log
```

1. What is the hostname of the server?
	1. Calculate the length of the hostname, which is represented as a 4-byte integer at offset 13. Use this [cyberchef recipe](https://cyberchef.io/#recipe=Take_bytes\(13,4,false\)To_Binary\('None',8\)From_Base\(2\)) to calculate the length of the hostname, which is 14.
	2. Extract the hostname, which is an ASCII string of length 14 at offset 17. Use this [cyberchef recipe](https://cyberchef.io/#recipe=Take_bytes\(17,14,false\)) to extract the hostname.

2. What is the plaintext flag in the log file?
	1. Calculate the length of the flag, which is represented as a 4-byte integer at offset 31. 
	2. Use this [cyberchef recipe](https://cyberchef.io/#recipe=Take_bytes\(31,4,false\)To_Binary\('None',8\)From_Base\(2\)) to calculate the length of the flag, which is 20.
	3. Extract the flag, which is an ASCII string of length 20 at offset 35 > Then, perform a base64 decode. Use this [cyberchef recipe](https://cyberchef.io/#recipe=Take_bytes\(35,20,false\)From_Base64\('A-Za-z0-9%2B/%3D',true\)) to extract the flag.

3. On what date was the file created (in UTC)?
	1. The date is a 4-byte timestamp at offset 9. 
	2. Use this [cyberchef recipe](https://cyberchef.io/#recipe=Take_bytes\(9,4,false\)To_Binary\('None',8\)From_Base\(2\)From_UNIX_Timestamp\('Seconds%20\(s\)'\)) to extract the timestamp.

4. How many entries are in the log file?
	1. The number of entries is a 4-byte integer at offset 55. 
	2. Use this [cyberchef recipe](https://cyberchef.io/#recipe=Take_bytes\(55,4,false\)To_Binary\('None',8\)From_Base\(2\)) to extract the number of entries.

5. How many total transferred bytes were recorded in the log?
	1. You can use this [cyberchef recipe](https://cyberchef.io/#recipe=Take_bytes\(59,2592,false\)To_Hex\('Space',16\)Fork\('%5C%5Cn','%5C%5Cn',false\)From_Hex\('Space'\)Take_bytes\(12,4,false\)To_Hex\('None',0\)From_Base\(16\)Merge\(\)Sum\('Line%20feed'\)) to calculate the total transferred bytes. (This recipe extracts the bytes for the entries, splits the data into 16-byte lines (one line per entry), extracts just the number of bytes transferred as an integer value, and then sums the total amounts.)

6. How many unique IP addresses (both senders and receivers) are recorded?
	1. You can use this [cyberchef recipe](https://cyberchef.io/#recipe=Take_bytes\(59,2592,false\)To_Hex\('Space',16\)Fork\('%5C%5Cn','%5C%5Cn',false\)From_Hex\('Space'\)Take_bytes\(0,8,false\)To_Hex\('None',4\)Change_IP_format\('Hex','Dotted%20Decimal'\)Merge\(\)Unique\('Line%20feed',false\)Regular_expression\('IPv4%20address','\(?:\(?:%5C%5Cd%7C%5B01%5D?%5C%5Cd%5C%5Cd%7C2%5B0-4%5D%5C%5Cd%7C25%5B0-5%5D\)%5C%5C.\)%7B3%7D\(?:25%5B0-5%5D%7C2%5B0-4%5D%5C%5Cd%7C%5B01%5D?%5C%5Cd%5C%5Cd%7C%5C%5Cd\)\(?:%5C%5C/%5C%5Cd%7B1,2%7D\)?',true,true,false,false,false,true,'List%20matches'\)) to calculate the number of unique IP addresses. (This recipe extracts the bytes for the entries, splits the data into 16-byte lines (one line per entry), extracts the source + destination IP addresses, splits those into 4-byte lines (one line per IP address), converts each line into a human-readable IP addresses, and then gets a count of the number of unique IP addresses.)

7. Which IP address sent the most amount of data?
	1. Use `awk` to parse the log!

```bash
awk '{sums[$1]+=$4} END {for (ip in sums) print sums[ip], ip}' merged.log  | sort -n
```

> Create a hashtable where the keys are the source IP addresses (column 1), sum the number of bytes transferred (column 4) for each source IP address, print the result, and then sort by the most bytes transferred.

8. What was the busiest day (day with the most bytes transferred)?

```bash
awk '{sums[$3]+=$4} END {for (date in sums) print sums[date], date}' merged.log  | sort -n
```

> Create a hashtable where the keys are the dates (column 3), sum the number of bytes transferred (column 4) for each date, print the result, and then sort by the most bytes transferred.

