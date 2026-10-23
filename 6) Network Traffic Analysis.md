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
![[Pasted image 20261022173754.png]]
> Above is screenshot for #1, #2, and #3

4. How many items were in the response?
	1. look for the DNS packet that has "Standard Query Response" in the Info column. 
	2. Expand the "Doman Name System (response)" packet dissectors > expand "Answers" > count how many number of entries in the answer section = 4!!!

5. What is the TTL for all of the DNS records?
	1. expand one of the entries in "Answers" dropdown and find "`Time to Live`" or "`TTL`"

6. What is the IP address for the "welcome" subdomain?
	1. Find the "`welcome.etas.com`" entry in the "Answers" dropdown and you can see the IP address: `1.1.1.1` (you can see it as well when expanding it)
![[Pasted image 20261022195848.png]]
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
