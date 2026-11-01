# Network Traffic Analysis

## Domain Name System (DNS) Traffic Analysis

- DNS is what enables us to access much of the internet without remembering IP addresses, analyze the network packet capture to understand more about DNS.

Before we do anything we must download the .pcap file and upload it onto Kali Linux machine. After the file is uploaded, open up "Wireshark" which is a pre-installed into Kali Linux, and click "File" > "Open" and open the pcap file!

Upon opening the pcap file on wireshark, we can only see that this file only has 9 packets! So this will make it way easier for us for analysis.

### DNS (Easy) Write-Up

1. What is the type of the DNS query requested?
	1. look for the DNS packet that has "Standard Query" in the Info column and click on that specific packet > expand the "Doman Name System (query)" packet dissectors > expand "Queries" > and find: "`type [TYPE]"

2. What domain was requested? 
	1. You can see the domain: `etas.com` in the Info column as well in the Queries drop down dissection info!


<img width="1213" height="717" alt="image" src="https://github.com/user-attachments/assets/9c0a2250-ce67-4b97-8701-c8b38e649209" />


> Above is screenshot for #1, #2, and #3

4. How many items were in the response?
	1. look for the DNS packet that has "Standard Query Response" in the Info column. 
	2. Expand the "Doman Name System (response)" packet dissectors > expand "Answers" > count how many number of entries in the answer section = 4!!!

5. What is the TTL for all of the DNS records?
	1. expand one of the entries in "Answers" dropdown and find "`Time to Live`" or "`TTL`"

6. What is the IP address for the "welcome" subdomain?
	1. Find the "`welcome.etas.com`" entry in the "Answers" dropdown and you can see the IP address: `1.1.1.1` (you can see it as well when expanding it)


<img width="919" height="915" alt="image" src="https://github.com/user-attachments/assets/800d82fa-9abd-49bc-98e1-1deead1280d4" />



> Above screenshot is for #4, #5, and #6

<br>

## File Transfer Protocol (FTP) Traffic Analysis

The File Transfer Protocol (FTP) is a standard network protocol used to transfer computer files between a client and a server over a TCP/IP network.

Before we do anything, make sure we must download the .pcap file and upload it onto Kali Linux machine. After the file is uploaded, open up "Wireshark" which is a pre-installed into Kali Linux, and click "File" > "Open" and open the pcap file!


### FTP Traffic (Easy) Write-Up

1. What was the first username and password combination attempt made to log in to the server? (e.g. `user:password`)
	1. Look at the first couple of packets in the Info column, find the first two requests that contain "USER" and "PASS" > you can see that the user/pass combination failed as the response is: "Login or password incorrect!" > `user1:cyberskyline`!!!
	2. you can also right click one of the first couple of packets > "Follow" > "TCP Stream"!!!

2. What software is the FTP server running? (Include name and version)
	1. find a 220 response code and the name and version after it > in this case, it is: `FileZilla Server 0.9.53 beta`

3. What is the first username and password combination that allows for successful authentication? (e.g. `user:password`)
	1. same as #1, but you find the user/pass combination that creates the response of: "Logged on"!!!

4. What is the first command the user executes on the ftp server?
	1. to filter to all user log in's, use `ftp.response.code == 230` filter command 
	2. right click on first packet > "Follow" > "TCP Stream" 
	3. find the `PORT` command (this is NOT the answer because the `PORT` command is used when you are about to trigger a data transfer via commands) > find the command used after the `PORT` command, which is `LIST`!!!

5. What file is deleted from the ftp server?
	1. same as #4 > find the command that says `DELE [file]`

6. What file is uploaded to the ftp server?
	1. same as #4 > find the command that says `STOR [file]`

7. What is the filesize (in bytes) of the uploaded file?
	1. use the filter command: `ftp-data` to show the actual content of transferred files and directory listings
	2. look for a packet with "(PORT) (LIST)" in the Info column > follow the TCP stream
	3. you would find something like: `28183 Oct 12 01:45 compcodes.zip` > `28183` is the file size!

8. What file does the anonymous user download?
	1.  to filter to all user log in's, use `ftp.response.code == 230` filter command 
	2. find when the anonymous user logs in successfully and find the `RETR` (Retrieve) command following with a file name > `RETR compcodes.zip`!!!

<br>

## HTTP Traffic Analysis

### HTTP (Easy) Write-Up

1. What Linux tool was used to execute a file download?
	1. apply the `http.request` filter to check all HTTP requests
	2. click the first packet > expand the "Hypertext Transfer Protocol" dropdown section of the packet dissector > expand `GET /images/layout/logo.png ...` dropdown > and look for the command after  `User-Agent:` (The “User-Agent” field in the HTTP portion of the packet is used to identify the software making the HTTP request)> wget!!!

2. What is the name of the web server software that handled the request?
	1. apply the `http.request` filter to check all HTTP requests
	2. click the first packet > right click > "Follow" > "HTTP Stream"
	3. find `Server: nginx/0.8.53` > nginx!!!

3. What IP address initiated request?
	1. apply the `http.request` filter to check all HTTP requests
	2. look at the first packet's "Source" column IP address > `192.168.1.140`!!!

4. What is the IP address of the server?
	1. apply the `http.request` filter to check all HTTP requests
	2. look at the packet's "Destination" columb IP address > `174.143.213.184`!!!

5. What is the md5sum of the file downloaded?
	1. use the `http` filter and find the packet that has "(PNG)" in the info column
	2. go down to "Portable Network Graphics" and expand the dropdown packet dissector > click on "PNG Signature" (so the staring hex values on the right are highlighted) > right click on the highlighted hex values > "Copy... as Hex Stream"
	3. after copying, head on to CyberChef (https://gchq.github.io/CyberChef/) and paste the copied values into "Input section"
	4. Use the "From Hex" operation > then click the magic wand! > an image should show up!
	5. Now, to find the MD5 sum of the file, type “MD5” into the operations section and move it to the recipe > `966007c476e0c200fba8b28b250a6379`

<br>

## Telnet Traffic Analysis

### Telnet (Easy) Write-up

1. What is the username that was used to log in?
	1. right click one of the first TELNET protocol packets > follow the TCP stream > find the username (its doubled blue/red > so `tteesstt` = `test`)!!!

2. What is the password that was used to log in?
	1. same as #1 > find "Password:" and the readable string is found below it: `capture`!!!

3. What command was executed once the user was authenticated?
	1. same as #1, find the command by finding the `$` symbol: `$uunnaammee --aa` = `$ uname -a`!!!

4. In what year was this capture created?
	1. same as #1, find the year: `Linux cm4116 2.6.30.2-uc0 #3 Tue Feb 22 00:57:18 EST 2011 armv4tl unknown` > `2011`!!!

5. What is the hostname of the machine that was logged in to?
	1. same as #4, `Linux cm4116 2.6.30.2-uc0 #3 Tue Feb 22 00:57:18 EST 2011 armv4tl unknown` > `cm4116`

6. What CPU architecture does the remote machine use?
	1. same as #4, `Linux cm4116 2.6.30.2-uc0 #3 Tue Feb 22 00:57:18 EST 2011 armv4tl unknown` > `armv4tl`!!!

<br>

## Packet Dissection 

### Packet Dissection (Medium) Write-up

- *Dissect an IP packet header to understand how the protocol works:*

> NCL provides us with two diagrams for this challenge, and to make it easier to explain, I will just label them as "Diagram A" and "Diagram B" for reference!

Diagram A (IPv4 Headers):

<img width="1522" height="361" alt="image" src="https://github.com/user-attachments/assets/a0ac71ba-20dc-4cd4-b645-89c1aca6ca7a" />


Diagram B (Binary Packet):

<img width="470" height="310" alt="image" src="https://github.com/user-attachments/assets/d506c469-bd71-4260-b29b-ecb065e03f5c" />


1. What is the header checksum in hexadecimal representation?
	1. First, we look for "Header Checksum" in Diagram A which we can see is located at bit position 16-31 and on the 64 bit row.
	2. since the row starts at 64 and the checksum starts on position 16 and ends at position 31, we add `64 + 16 = 80` and `64 + 31` (meaning that "Header Checksum"  begins at bit 80 (actual) and ends at bit 95).
	3. thus, the checksum occupies bits 80-95 (16 bits total and 2 bytes total, because counting starts at the first number AND 1 byte = 8 bits).
	4. Now, to convert the bit positions into byte positions, we divide the starting bit position by 8 (`80 / 8 = 10`) > So the checksum starts at byte 10! Now since the checksum starts a byte position 10, and checksum occupies 2 bytes (80-95), the next byte position is simply 11.
	5. Next, looking at Diagram B, knowing checksum is at byte positions 10 and 11, we go to offset row 8 and count right to 10-11 > in this case, checksum is at `01001111` and `10010011`
	6. Finally, to get the hexadecimal representation, we go to https://gchq.github.io/CyberChef/ (CyberChef), and convert those two binaries into hexadecimal (use recipes: "From Binary" and "To Hex") > `4f 93`!!!

2. What is the TTL (Time To Live) of the packet?
	1. looking at diagram A, TTL is located at bit position 0-7 (8 bits = 1 byte occupied)
	2. Since TTL is on offset row 64 and starts at bit position 0, you add `64 + 0 = 64`, so the actual bits for TTL is 64 (start)
	3. To convert to byte position, we divide the starting bit position by 8: `64 / 8 = 8`, so the TTL starts at byte 8!
	4. next, looking at Diagram B, locate byte 8 (and since the length of TTL is only 8 bits, its only a 1 byte binary) > `01000000` 
	5. Finally, convert `01000000` to decimal (for TTL) using CyberChef > `64`!!!

3. What is the source IP address?
	1. on Diagram A, the source IP address located at bit position 0-31 (32 bits counted = 4 bytes occupied)
	2. since source IP address is on offset row 96 and starts at bit position 0: `96 + 0 = 96`
	3. divide: `96 / 8 = 12` to find byte location
	4. on Diagram B, find the four bytes starting at offset 12, in this case: `11000000`, `10101000`, `10000000`, and `10000000` 
	5. Finally, convert these binaries into IP address using CyberChef:
		1. input: `11000000 10101000 10000000 10000000`
		2. ingredients: "From Binary" > "To Hex" > "Change IP Format (Input Format: Hex)"
	6. `192.168.128.128`!!!

4. What is the destination IP address?
	1. same as #3: destination IP address occupies 4 bytes, is on offset row 128, and the byte location is at 16 > `10011111`, `11001011`, `01100000`, and `10011010`.
	2. convert binaries into IP addresses using CyberChef (same as #3) = `159.203.96.154`!!!

## HTTPS & SSL/TLS Protocol Traffic Analysis

### Decrypt (Medium) Write-Up

- *The 'S' in HTTPS stands for secure and it uses the TLS/SSL protocol to achieve its security. Let's decrypt this traffic to find the hidden flag.*

We are given a .PCAP file and a SSL Key Log file which contains:

```
# SSL/TLS secrets log file, generated by NSS
CLIENT_RANDOM b9f308c7a66a767c5f09c14959806a27b25be9b5ed2183a53591f480cbf17ef2 fc3925af3af1496a0f877d35e4d22d49c0b6e27cae041710569594e59c6db51e4348491acc427d6a9b78acf722cdfb75
CLIENT_RANDOM aed936c3b98cc1ea78108d04c0dbdbecda4842a96dbf2857561b17c1762019c3 fc3925af3af1496a0f877d35e4d22d49c0b6e27cae041710569594e59c6db51e4348491acc427d6a9b78acf722cdfb75
```

1. What Cipher Suite was chosen by the secure socket server?
	1. You must find and look at the "Server Hello" packet to find the chosen cipher suite. The "Client Hello" only shows a menu of 15 options that the client is willing to use. The server will pick one final winner from this list and announce its choice in the "Server Hello" packet.
	2. Click the packet > Transport Layer Security > TLSv1.2 Record Layer: Handshake Protocol: Server Hello > Handshake Protocol: Server Hello > `Cipher Suite: TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (0xc02f)` > `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`!!! 

2. What is the domain covered by the SSL key?
	1. On the same packet, go to Click the packet > Transport Layer Security > TLSv1.2 Record Layer: Handshake Protocol: Certificate > Handshake Protocol: Certificate > Certificates > Certificate > signedCertificate > subject > `commonName=tomsvacations.com` > `tomsvacations.com`!!!

3. What is the flag transferred over HTTPS?
	1. download the `sslkeylog.log` file, and load it into wireshark:
		1. Edit > Preferences > expand Protocols > scroll down to select TLS > Look for the `(Pre)-Master-Secret log filename` field > Click Browse... > select the downloaded key log file (`sslkeylog.log`), and click Open > Click OK to save the changes.
	2. Wireshark will automatically refresh, and use the `http` filter to find one of the packets that has labeled: `429 GET /flag.txt HTTP/1.1` > Follow the HTTP stream, and you will find the plaintext flag! > `SKY-LADN-1435`!!!

<br>
