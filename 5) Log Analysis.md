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

<img width="161" height="419" alt="Screenshot 2026-10-10 150115" src="https://github.com/user-attachments/assets/2f9f3d7e-70a4-47ed-8c97-9bcf570c7937" />

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

