# Password Cracking

#Cybersecurity #CTF #NCL #Resources 

## Hashcat

### 🛠️ Tools

1. https://www.tunnelsup.com/hash-analyzer/ (Hash Analyzer/Identifier)
2. `$ hashcat --identify ([hash] or [hashes.txt])` (Hash Analyzer/Identifier)
3. `$ hashcat [hashes.txt] -m [hash mode] -a [attack type] [etc]` (Hashcat)
	- Examples:
		1. Dictionary Attack = `$ hashcat [hashes.txt] -m [hash mode] -a 0 [path to dictionary]
		2. Brute Force Attack = `$ hashcat [hashes.txt] -m [hash mode] -a 3 [brute force]
		3. Hybrid Attack = `$ hashcat [hashes.txt] -m [hash mode] -a 6 [path to dictionary] [brute force]

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

