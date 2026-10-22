# Password Cracking

## Hashcat

Use when given just a hash password :)
- Example: `68a96446a5afb4ab69a2d15091771e39`

### 🛠️ Tools

1. https://www.tunnelsup.com/hash-analyzer/ (Hash Analyzer/Identifier)
2. https://hashes.com/en/tools/hash_identifier (Better Hash Identifier?)
3. https://crackstation.net/ (CrackStation)
4. `$ hashcat --identify ([hash] or [hashes.txt])` (Hash Analyzer/Identifier)
5. `$ hashcat [hashes.txt] -m [hash mode] -a [attack type] [etc]` (Hashcat)
	- Examples:
		1. Dictionary Attack = `$ hashcat [hashes.txt] -m [hash mode] -a 0 [path to dictionary]`
		2. Brute Force Attack = `$ hashcat [hashes.txt] -m [hash mode] -a 3 [brute force]`
		3. Hybrid Attack = `$ hashcat [hashes.txt] -m [hash mode] -a 6 [path to dictionary] [brute force]`

### RockYou (Easy) Write-Up

1. create a txt file for hashes > `$ nano hashes.txt` > paste hashes line by line > save
2. use https://www.tunnelsup.com/hash-analyzer/ to identify hash type > MD5
3. `$ hashcat hashes.txt -m 0 -a 0 /usr/share/wordlists/rockyou.txt`
	- `hash.txt` : the file location + file name that has the crackable hashes
	- `-m 0` : uses hash-mode `0` (indicates the hashes are MD5 hashes)
	- `-a 0` : use a dictionary attack (this needs a wordlist to be specified)
	- `/usr/share/wordlists/rockyou.txt` : the file location + name of the wordlist

### Mask (Medium) Write-Up

- *It appears that they are all in the format: `SKY-HQNT-` followed by **4 digits.***

> We can see all possible combinations of `SKY-HQNT-0000` thru `SKY-HQNT-9999` if `?d` is used to represent each of the unknown numbers in the password as follows: `‘SKY-HQNT-?d?d?d?d’`. This is basically brute-forcing the password!

1.  create a txt file for hashes > `$ nano hashes.txt` > paste hashes line by line > save
2. use https://www.tunnelsup.com/hash-analyzer/ to identify hash type > MD5
3. `$ hashcat hashes.txt -m 0 -a 3 SKY-HQNT-?d?d?d?d` 
	- `hash.txt` : the file location + file name that has the crackable hashes
	- `-m 0` : uses hash-mode `0` (indicates the hashes are MD5 hashes)
	- `-a 3` : uses brute-force/mask attack
	- `‘SKY-HQNT-?d?d?d?d’` : attempt different digits in the place of each `?d`

### Pokemon (Medium) Write-Up

- *It appears that all the passwords are based on **Pokemons**.*

1.  create a txt file for hashes > `$ nano hashes.txt` > paste hashes line by line > save
2. `$ hashcat --identify hashes.txt` > MD5 
3. create a txt file containing ALL pokemon (find online) > this would be your dictionary
4. `hashcat hashes.txt -m 0 -a 0 pokemon_all_gen1-9_lowercase.txt`
	- `hash.txt` : the file location + file name that has the crackable hashes
	- `-m 0` : uses hash-mode `0` (indicates the hashes are MD5 hashes)
	- `-a 0` : use a dictionary attack (this needs a wordlist to be specified)
	- `pokemon_all_gen1-9_lowercase.txt` : the file location +name of the wordlist

### Law & Order (Hard) Write-Up

- *It appears that they are based off of **"Law and Order: SVU" episodes** and **end in 2 digits.***

1.  create a txt file for hashes > `$ nano hashes.txt` > paste hashes line by line > save
2. `$ hashcat --identify hashes.txt` > MD5 
3. create a txt file containing ALL law & order svu episode names (find online) > this would be your dictionary
4. use HYBRID ATTACK (since there is wordlist dictionary + number brute force) --> `hashcat hashes.txt -m 0 -a 0 -a 6  law_and_order_svu_episode_titles_all.txt ?d?d`

<br>

## NTLM Password Cracking

**Windows NTLM** is an authentication system that uses a hash of a user's password instead of storing the plaintext password.

**NT hash** = newer Windows password hash.  
**LM hash** = older, weaker Windows password hash.

Ophcrack uses rainbow tables to try to match these hashes back to possible passwords.

### 🛠️ Tools

1. Ophcrack (pre-installed in Kali Linux)
	1. https://ophcrack.sourceforge.io/tables.php (table downloads)

### Rainbow Tables (Easy) Write-Up

Before doing anything, we should download the rainbow table wordlists and set up the tool in order to use "Ophcrack"! 
1. Open up Kali Linux and on the top left, click the kali logo, and search up "Ophcrack" (open the tool)

<img width="1443" height="757" alt="image" src="https://github.com/user-attachments/assets/c0e1a81b-7d39-4b54-899d-771fbf47aaab" />


2. Go to https://ophcrack.sourceforge.io/tables.php and you can see all the downloadable tables for Ophcrack! > we are going to be using the "xp_free_fast" table! (The download will take a while)
3. once downloaded (it will be a zip file), drag it into your kali linux machine and move that zipped file into `/usr/share/wordlists` > to keep things organized we have to unzip all the files into a separate folder, so run:  `$ sudo unzip tables_xp_free_fast.zip -d xp_free_fast`
4. once you finished creating the table in the wordlist, go back home and create a text file with the hashes (separated by line):
```
3DD1FBC27831C5F37C3113B4A1A5E3A0:AFEFFCDAAF0662AD320CEB423C9A160D
FA4F6C499D7A1598BB738DDACC45834C:BAE8A18E6A5CA516465279CCA50E8D36
B253395EF2B08A3C5D066BE4D31DBA16:64757A073C44C714F6FE0F07E11F9082
FA8A4C51567993309C5014AE4718A7EE:1F4FD09BE84B20FE891FB93A60F61499
DBC12B38AA32A681F58CFA8E98E4E5B6:86178E8EADE411AF3DB1446D8082289F
93AF3851019BC8C793E28745B8BF4BA6:506DD82C015DEEE0CDDDC5DAC449E1AB
```
5. open up Ophcrack, click "Tables" > click the first option which is the table we downloaded called "xp_free_fast" > click "Install" > locate the `/usr/share/wordlists/xp_free_fast` folder (it will be in "+ Other Locations" > "Computer") and press "Open"
6. once the table is installed, go to "Load" > navigate to your text file containing all the password hashes that are needed to be cracked > press "Crack"!!!

<img width="814" height="636" alt="image" src="https://github.com/user-attachments/assets/29b0e5eb-a420-498f-99ab-cbb478ed38a0" />


> Example above!

<br>

## /etc/shadow

Example Full Shadow Entry:

```
hollie:$y$j9T$/WzixhAsn8sdXhCquYzh01$KZlio78LilItobsx/17ecFf1e2SbsduhP1sZEWuHrL4:18934:0:99999:7:::
```

- each "section" of the shadow entry is separated with a `:`
- `hollie` - the first field is the username.
- `$y$j9T$sZPOHCdOIBvfkKhVJRSp7.$oCNH1mJKQWFWM9HNjkjz3nFWuGPHF2RRG7j7eChfGw9` - the second field is the Hash.
- `18934` - the third field represents the date of the last password change (measured in DAYS since Jan 1, 1970)

Example Hash from Shadow entry:

```
$y$j9T$/WzixhAsn8sdXhCquYzh01$KZlio78LilItobsx/17ecFf1e2SbsduhP1sZEWuHrL4
```

- `$y$` - Identifies this hash as `yescrypt` 
- `j9T` - Encoded cost parameters 
- `WzixhAsn8sdXhCquYzh01` - Salt
- `KZlio78LilItobsx/17ecFf1e2SbsduhP1sZEWuHrL4` - Hash Digest

### 🛠️ Tools

- https://www.epochconverter.com/seconds-days-since-y0 (Epoch Converter)
- `$ john [options] [password files]` (JohnTheRipper)
### Kali Linux

- *We have obtained `/etc/shadow` from a Kali Linux machine. Help us obtain the password, we think this might be a using a password from the Rockyou wordlist.*

1. *Finding username of the only user account with a password?*
	1. scroll down `/etc/shadow` file until you see the username with a long hash: `hollie:$y$j9T$/WzixhAsn8sdXhCquYzh01$KZlio78LilItobsx/17ecFf1e2SbsduhP1sZEWuHrL4:18934:0:99999:7:::`
	2. first field is username!

2. *What date was the user's password last changed?*
	1. scroll down `/etc/shadow` file until you see the username with a long hash: `hollie:$y$j9T$/WzixhAsn8sdXhCquYzh01$KZlio78LilItobsx/17ecFf1e2SbsduhP1sZEWuHrL4:18934:0:99999:7:::`
	2. look for the third field
	3. use https://www.epochconverter.com/seconds-days-since-y0 to find the date > scroll down to "Days Since 1970-01-01" > paste `18934` > get date!

3. *What is the salt used to secure the user's password?*
	1. scroll down `/etc/shadow` file until you see the username with a long hash: `hollie:$y$j9T$/WzixhAsn8sdXhCquYzh01$KZlio78LilItobsx/17ecFf1e2SbsduhP1sZEWuHrL4:18934:0:99999:7:::`
	2. look for the salt by finding the sequence that starts before the `$` in the hash > in this case, the salt is: `WzixhAsn8sdXhCquYzh01`
		- `$y$j9T$/ {WzixhAsn8sdXhCquYzh01} $KZlio78LilItobsx/17ecFf1e2SbsduhP1sZEWuHrL4`

4. *What is the hash digest of the user's password?*
	1. scroll down `/etc/shadow` file until you see the username with a long hash: `hollie:$y$j9T$/WzixhAsn8sdXhCquYzh01$KZlio78LilItobsx/17ecFf1e2SbsduhP1sZEWuHrL4:18934:0:99999:7:::`
	2. look for hash digest which is after the `$` or after the salt

5. *What is the plaintext password of the user's password?*
	1. copy the ENTIRE hash (`<username>:$y$j9T$<salt>$<hash>`)
	2. create a txt file for password > `$ nano password.txt` > paste password > save
	3. `$ john --format=crypt password.txt`

