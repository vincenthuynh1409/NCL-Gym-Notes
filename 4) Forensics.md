# Forensics

## Git Version Control

### 🛠️ Tools

1. `$ git [options]`
2. https://docs.github.com/en/get-started/git-basics/set-up-git (Git basics)

### Version Control (Easy) Write-Up

- *One of our employee's computer was compromised and we saw this backup file leave the network, but we couldn't find anything other than a simple README.md file in it. Help us found out what information the hackers got: "git_backup.zip"*

#### Git Logs, Git Show

1. Unzip "git_backup.zip" by typing: `$ unzip git_backup.zip` 
2. Change to "git_backup" directory by typing: `$ cd git_backup`
3. in the `~/git_backup` directory, list out ALL files/directories (+ hidden) by typing `$ ls -la`
4. you will see 20 total directories, subdirectories, and files → the `.git` directory is important

> The `.git` directory means that this is a git repository and we can use the `git` command to view and extract information.

5. `$ cd .git`
6. to check out the git log and see what commits have been created + view any users that are active on this repository, run: `$ git log` 

> Each commit is represented in a SHA1 hash with author/user of the commit, date, time, comment, etc.

7. to see a specific commit, run: `$ git show [hash]`

#### Git Branches

1. nothing useful in current branch, to switch branches: run `$ git branch` in `~/git_backup` directory to see the available branches in that repository
2. to switch branches, run: `$ git switch [branch name]`

<br>

## Binwalk

### 🛠️ Tools

1. `$ binwalk [options] [file]`

### File Carving (Medium) Write-Up

- *The security team has found a rather strange file exiting the network, we're not sure if it's containing any sensitive information. Help us identify what's in it: "green_file.bin"*

1. download `green_file.bin` file > check file type using `$ file green_file` > PNG!
2. to see how many files that can be extracted, use binwalk: `$ binwalk green_file` > 6
3. There are 2 ways to extract the files into your machine:
	1. `$ binwalk --extract green_file`
		- running this just creates another folder from the extracted PNG
	2. `$ binwalk --extract --dd “png:png” green_file`
		- running this creates another folder from the extracted PNG with only PNGs extracted.
		- `--dd "png:png"` → extract only files identified as PNGs
4. after new folder/directory created, navigate through them using `ls` and `cd`
5. after finding "CAB" file, it is actually a `tar archive` (a single file that bundles multiple files and directories together) > to unpack the tar archive, simply run: `$ tar xvf CAB` > you will be able to see directories
6. another way you can find the flag is by also doing `$ ls -la` to find hidden directories!

> i.e. there are so much different approaches to solving these problems!

<br>

## File Signatures & Bytes

### 🛠️ Tools

1. https://en.wikipedia.org/wiki/List_of_file_signatures (List of Hex File Signatures) > `CTRL+F`
2. https://gchq.github.io/CyberChef/ (CyberChef)
3. https://hexed.it/ (Hex Editor)


### Magic Bytes (Medium) Write-Up

- *This file appears to be changed in some way. Can you recover the original?: "flag.jpeg"*

> Basically figure out what the file originally was, repair its beginning, and then open it to get the flag.

1. load file into [CyberChef](https://gchq.github.io/CyberChef/ ) > use "To Hex" to check individual bytes of the file
2. copying the first couple of bytes into [Wiki List of Signatures](https://en.wikipedia.org/wiki/List_of_file_signatures) by using `CTRL+F` to find and try to match the bytes > for this example, we can see it kinda matches with JPEG, Exif, or JFIF file format
	- File = `ff d8 ff e0 00 10 4a 46 49 46 00 0d`
	- Wiki = `FF D8 FF E0 00 10 4A 46 49 46 00 01`

> the last byte is different and does not match!

3. use "Strings" in CyberChef > we see "`JFIF`" and "`IHDR`" (by doing online searches, `JFIF` is used in the JPEG filetype; while `IHDR` is used in PNG filetype!)

> based on this, we can attempt to edit the raw file to replace the JPEG file signature with the PNG file signature!
> 
> using the wiki, look up the PNG file signature > `89 50 4E 47 0D 0A 1A 0A` 

4. Go to [Hex Editor](https://hexed.it/) > open the JPEG file > select the first 8 bytes > "insert selected bytes here" > "Overwrite the bytes at the cursor position" > type: `89 50 4E 47 0D 0A 1A 0A` manually (replacing the 8 bytes)

> - However, even after correcting the magic bytes and changing the file extension to .PNG, the file still fails to open after saving it again.
> - Since the PNG file signature is 8-bytes long and the jpeg file signature is 12 bytes long, the extra 4 bytes that remain from the jpeg file signature is causing the error!
> - To fix this, we can attempt to copy the 4 bytes following the PNG file signature from a known valid PNG file and see if that will correct the problem.
> - a valid PNG file: `89 50 4E 47 0D 0A 1A 0A | 00 00 00 0D`

5. In the Hex editor, replace the last 4 bytes with `00 00 00 0D`
6. Save as .PNG > Open new recovered file > get flag!!!

<br>

## Doctor (Medium) Write-Up

- *We think this document is hiding something. Can you find what is hidden?*
	- "SuperAwesomeDoc.docx"

1. if you open the Microsoft document file, there isn't anything useful > after `$ file SuperAwesomeDoc.docx`, there's also nothing useful.
2. after doing `$ binwalk SuperAwesomeDoc.docx` we can see a bunch of ZIP file archives which is very strange, to confirm this > import file into CyberChef > To Hex > Check ZIP file signature on wiki > matches! 

> `SuperAwesomeDoc.docx` is ACTUALLY a .zip file!

3. unzip it by running: `$ unzip SuperAwesomeDoc.docx`
4. explore the files and directories until you find the image with the flag!

<br>

<br>

## Mainframe

### 🛠️ Tools

1. `$ base64 -d [file] > [output]` (base64)
2. google
3. `$ xxd [file]` (Hex Editor)

> or you can use other Hex Editors :)
 
4. `$ dd if=[input file] of=[output file] conv=[conversion]` (data conversion)
5. `$ john --format=[format] --wordlist=/usr/share/wordlists/rockyou.txt --rules=[rule] [file]` (John the ripper)

### Hack the Gibson (Medium) Write-Up

- *North Central Loan's mainframe was compromised by Liber8tion. All we've been able to gather so far is this file, analyze it and figure what they were able to collect: "ARCHIVE.NETDATA.XMI"*

1. after doing `$ file ARCHIVE.NETDATA.XMI`, it displays "ASCII text" and doing `$ cat ARCHIVE.NETDATA.XMI` displays a a bunch of base64 type gibberish > we can assume this file has been encoded in base64

2. to decrypt the file, run: `$ base64 -d ARCHIVE.NETDATA.XMI > ARCHIVE.NETDATA.decoded.XMI` 
3. then: `$ file ARCHIVE.NETDATA.decoded.XMI` > this file is a **IBM NETDATA** file!
4. Next, to find what type of file extension is the archive inside the XMI file, look up `extract netdata xmi`:
	1. `python3 -m venv venv`
	2. `source venv/bin/activate`
	3. `pip install xmi-reader`
5. Using the XMI reader documentation, extract the decoded XMI file with the command:  `$ extractxmi ARCHIVE.NETDATA.decoded.XMI`
6. after decoding, you would get a new file which would be the archive inside the XMI > it is a **ZIP** file! > unzip: `$ unzip ARCHIVE.NETDATA.DECODED.zip` > you would get a bunch of files
7. after investigating some of the files by doing `$ file EMAILS`, it just shows a bunch of "@" or diamonds > to see what the file ACTUALLY is, we can use a hex editor > you can run the hex editor in Linux by typing: `$ xxd [file]` > `$ xxd EMAILS`
8. once its opened, we can already see something different about this hex: there is a lot of repeated `4040` > the question asks what type of encoding the files are in, so after looking up "what type of encoding uses 4040s?" > it results to **EBCDIC**!!!
9. now since we understand that the file id encoded in EBCDIC, we can decode this by running `dd` a data conversion tool: `$ dd if=input.ebcdic of=output.ascii conv=ascii` (format) > `$ dd if=USERS of=decoded_users conv=ascii` 
10. after we get the "decoded_users" file, open it by using `cat` > we can see a bunch of hash passwords and by looking them up, the operating system that uses this password hash is called **z/OS**!!!
11. after looking up what type of hash the passwords are, it shows the hashed passwords in "RACF" format! > before we decrypt, we have to organize the line layout to prevent errors as it is kinda messy, and to do this: `$ sed 's/ \{2,\}/\n/g' decoded_users.txt > users_clean.txt` 
12. now, we just use `john` to decrypt the hashes: `$ john --format=racf --wordlist=/usr/share/wordlists/rockyou.txt users_clean.txt` > `$ john --show users_clean.txt`
13. in order to answer the last question, we must use the "best 64" john rules (curated set of 64 high-efficiency password mutation rules) > `$ john --format=RACF --wordlist=/usr/share/wordlists/rockyou.txt --rules=best64 users_clean.txt`


