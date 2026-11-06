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

<br>

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


## Custom TCP Traffic Analysis

### Pandora (Hard) Write-Up

- The hackers have created their own custom protocol for private communication. Luckily, our analysts have managed to obtain the documentation describing the protocol. Use the provided capture to answer the following questions about a custom protocol hackers have created
- We are given a .pcap file named: `pandora.pcap`

1. What is the IP address of the server?
	1. First, after analyzing the custom communication, we must filter for TCP because the custom protocol uses TCP to communicate between the client and server.
	2. Open the .pcap file onto Wireshark and use the filter:  `tcp && !(tcp.port == 22) && !(tcp.port == 80)`
		- `tcp`: Includes only packets that use the Transmission Control Protocol.
		- `&&`: The logical `AND` operator, requiring all conditions in the filter to be true simultaneously.
		- `!(tcp.port == 22)`: Excludes any TCP traffic where either the source or destination port is 22, which is typically used for Secure Shell (SSH) remote management.
		- `!(tcp.port == 80)`: Excludes any TCP traffic where either the source or destination port is 80, which is used for unencrypted web traffic via HTTP.
	3. look for the first packet that has `[SYN]` since the destination IP receiving the SYN is the server (and client vice versa) > look at "Destination" > `10.1.0.20` !!!

2. What is the IP address of the client?
	1. same as #1, but look at the "Source" IP address for the same packet: `10.1.0.217`!!!

3. What port is the server listening on?
	1. On the same packet, expand the "Transmission Control Protocol" packet dissector, and find `Destination Port:` > `60123`!!!

4. What is the magic 2-byte ID in decimal representation?
	1. On the same packet > Follow TCP stream > Show as: Hex Dump > Look at the first client-to-server data > The first 4 bytes represent the number of requests (`05`) and The next 2 bytes are the magic ID (which is `04 17`) 
	2. Convert `04 17` into decimal via CyberChef > use ingredient: "From Base (Radix: 16)" > `1047`!!!

5. How many encrypt requests were made by the client?
	1. On the same packet > Follow TCP stream > Show as: Hex Dump > Look at the first client-to-server data > The first 4 bytes represent the number of requests (`05`)!!!

6. What is the length of the first encrypt request (in bytes)?
	1. On the same packet > Follow TCP stream > Show as: Hex Dump > Find the first magic ID: `04 17`, and  the next 4 bytes represent the request length > `04 17 00 00 00 58` (The length is `00 00 00 58` in hexadecimal.)
	2. To convert hex to bytes, we can use CyberChef > input: `58` > Ingredients: "From Hex" and "To Decimal" > `88`!!!

7. What is the length of the second encrypt request (in bytes)?
	1. On the same packet > Follow TCP stream > Show as: Hex Dump > Find the second occurrence of the magic ID: `04 17` (use ``CTRL+F`` `"04 17"` `Find Next`) and the next 4 bytes represent the 2nd request length > `04 17 00 00 00 48` (The length is `00 00 00 48` in hexadecimal.)
	2. To convert hex to bytes, we can use CyberChef > input: `48` > Ingredients: "From Hex" and "To Decimal" > `72`!!!

8. How large is an individual encrypt hash (in bytes)?
	1. On the same packet > Follow TCP stream > Show as: Hex Dump > Find the server's response length: `00 00 00 A0` >  convert `A0` into bytes using CyberChef (same ingredients as previous) > `160` bytes > Divide the total response length by the number of requests (`5`) > `160 / 5 = 32` > `32`!!!

9. What was the encrypt response, in hexadecimal representation, for the first request? (e.g. `123456789ABCDEF`)
	1. On the same packet > Follow TCP stream > Show as: Hex Dump 
	2. we have to find the server-to-client portion of the Hex Dump, so on the left of "Show as: Hex Dump", change "`Entire Conversation`" to "`10.1.0.20:PORT -> 10.1.0.217:PORT`" 
	3. Locate the response data after the 4-byte response length (`00 00 00 A0`) > Select and copy the first 32 bytes (first hash): `b8 c9 7b 08 e1 98 fa 9f   f7 9a 3a 9c 1f 01 09 b1   86 87 b7 a1 a3 ff 17 72   c2 9b 4d c8 67 53 d7 11`!!!

10. What was the encrypt response, in hexadecimal representation, for the second request?
	1. same as #9, but copy the next 32 bytes.
	2. `88 17 15 3a e8 1d 94 b5 d6 c7 45 e6 3d 1d f3 1d 5d 02 bd 3b 03 0b 82 0c 3c 03 86 54 fd ca 61 9c` !!!

11. What is the hidden flag being sent over the protocol?
	1. On the same packet > Follow TCP stream > Show as: ASCII 
	2. we have to find the client-to-server portion of the Hex Dump, so on the left of "Show as: ASCII", change "`Entire Conversation`" to "`10.1.0.217:PORT -> 10.1.0.20:PORT`" 
	3. you will see something like: `.........XTkNMLUZKQ0ctMTYzMiBOQ0wtRkpDRy0xNjMyIE5DTC1GSkNHLTE2MzIgTkNMLUZKQ0ctMTYzMiBO...` and it looks awfully like base64 encoding! 
	4. finally to convert the ASCII to Base64, copy the entire ASCII dump and paste it into CyberChef > use ingredient "From Base64" > we can find the flag repeated multiple times: `NCL-FJCG-1632`!!!

<br>

## CAN Bus Traffic Analysis

### CAN Bus (Hard) Write-Up

- *Ohm Motors has requested assistance with conducting a security audit on their CANBUS protocol. They have provided a small code snippet for updating vehicle speed indicators. Use this to help you complete this report: candump.pcap* 

```C++
int speed_id = 589;
int speed_pos = 3;
struct canfd_frame frame;

while (PollEvent(&event) != 0) {
    read_data(&event, &frame);
    if (frame.can_id == speed_id) {
        double speed = frame->data[speed_pos] << 8;
        speed += frame->data[speed_pos + 1];
        speed = speed / 100;
        speed = speed * 0.6213751;
        update_speed(speed);
    }
}
```

1. How many unique CAN Bus IDs are present in this capture?
	1. open the .pcap file into Wireshark (Kali Linux) > click the first packet > expand the CAN Bus section of the packet dissector > right click on "ID" > then click "apply as column" > Go to "File" (top left) > "Export Packet Dissections" > "As CSV."
	2. Open the CSV file into Google Sheets (via import), then find the column letter that contains the ID's (in my case, it is column H) > on empty cell, run: `=COUNTUNIQUE(H2:H)` and the unique number of bus ID's are shown > `36`!!!

2. How many speed update messages are present in this capture?
	1. Looking at the code, we can see that the speed ID is 589. Since speed messages have CAN ID 589, we count how many times that ID appears! 
	2. run on an empty cell: `=COUNTIF(H2:H,"*589*")` to find all iterations of ID 589 > `352`!!!

3. What is the maximum speed, in mph, that this vehicle reached in the capture? (round to the nearest hundredth)
	1. on wireshark, click one of the first packets which contains ID 589 (the first one lol) > expand the CAN Bus section of the packet dissector > expand "Data" > right click `Data: 000000013b` > click "Apply as Column" (you will see a new column form with values that look like `000000013b`) > Go to "File" (top left) > "Export Packet Dissections" > "As CSV."
	2. Open your new CSV file into Google Sheets (via import), then find the column letter that contains the data; in this case, it is now column H and the ID column is column I > on cell J2, paste this formula: `=IF(REGEXMATCH(I2,"ID: 589"),((HEX2DEC(MID(H2,7,2))*256+HEX2DEC(MID(H2,9,2)))/100)*0.6213751,"")` > `1.957331565` and drag the formula down through all rows (by double clicking the small blue dot on the bottom right of the cell) > In an empty cell, paste `=MAX(J2:J)` in which you would get the max speed >`20.13255324` > round to hundredth = `20.13`!!!


<br>

## WiFi Network Traffic Analysis

### 🛠️ Tools

1. `$ aircrack-ng [file]`

### WiFi PCAP 1 (Easy) Write-Up

- *We have created a packet capture from our test lab to see if you can handle the challenges of wireless password cracking: PCAP1.pcap*

1. How many IVs are in the packet capture?
	1. we will being using "aircrack" which is a pre-installed Kali Linux tool for assessing, monitoring, attacking, and testing the security of Wi-Fi networks! > to solve this question, we simply just run: `$ aircrack-ng PCAP1.pcap`

> we can see that the wireless network data encryption method is WEP (Wired Equivalent Privacy) security!

2. What is the key size of the wireless network data encryption method in bits?
	1. near the end of the aircrack results, you can see the key found: `KEY FOUND! [ A4:81:53:B4:CF ]` > the key contains 5 bytes (10 hex characters), and since each byte is 8 bits, multiply: `5 * 8 = 40`
	2. 40 isn't the key size though since we have to also account for the additional 24-bit Initialization Vector (IV), so we add: `40 + 24 = 64`!!!

3. What is the IV for the first packet in the capture in hexadecimal representation?
	1. open the .pcap file onto Wireshark > use the display filter: `wlan.wep.iv` to identify and isolate packets encrypted with WEP! > find and click on the first packet (no. 1) > expand the "IEEE 802.11 QoS Data ..." packet dissector > expand "WEP Parameters" > and find: `Initialization Vector: 0x003a33` > `00 3A 33`!!!

4. What is the WEP key?
	1. same as #1 & #2, near the end of the aircrack results, you can see the key found: `KEY FOUND! [ A4:81:53:B4:CF ]` > `A4:81:53:B4:CF`!!!

5. What is the TCP checksum, in hexadecimal representation, of the first packet in the capture?
	1. on wireshark, we will decrypt the traffic using the WEP key (`A4:81:53:B4:CF`) from earlier! > this can be done by selecting "Edit" (top left) > "Preferences" > "Protocols" > "IEEE 802.11" > then, check "Enable decryption" > click "Edit..." for Decryption keys > press the `+` sign to add a "wep" key type, and paste in `A4:81:53:B4:CF` for the key! > enter, then click "Apply" to apply the decryption!
	2. Once the traffic is decrypted we can see a bunch of SSH and TCP packets! > click on the first packet, and look for the `Transmission Control Protocol` packet dissector and expand it > finally, find the checksum: `Checksum: 0x897b ...` > `89 7B`!!!

### WiFi PCAP 2 (Medium) Write-Up

- *We have created a packet capture from our test lab to see if you can handle the challenges of wireless password cracking: PCAP2.cap*

1. How many IVs are in the packet capture?
	1.  `$ aircrack-ng PCAP1.cap` (this also uses WEP encryption!)

2. What is the key size of the wireless network data encryption method in bits?
	1. `DE:AD:10:CC:D1:5E:A5:EC:DC:C2:D6:7A:CA` (13 bytes)
	2. `13 * 8 = 104` > `104 + 24 (IV) = 128` > `128` bits!!!

3. What is the IV for the first packet in the capture in hexadecimal representation?
	1. on first packet (using `wlan.wep.iv` filter), expand the "IEEE 802.11 QoS Data ..." packet dissector > expand "WEP Parameters" > and find: `Initialization Vector: 0x0994ff` > `09 94 FF`!!!

4. What is the WEP key?
	1. aircrack > `DE:AD:10:CC:D1:5E:A5:EC:DC:C2:D6:7A:CA`!!!

5. What is the TCP checksum, in hexadecimal representation, of the first packet in the capture?
	1. decrypt network traffic data using WEP key (`DE:AD:10:CC:D1:5E:A5:EC:DC:C2:D6:7A:CA`) via "Edit" > click first packet and find `Transmission Control Protocol` packet dissector and expand it > finally, find the checksum: `Checksum: 08f0 ...` > `08 F0`!!!


### WiFi PCAP 3 (Hard) Write-Up

- *We have created a packet capture on a temporary wireless network that a couple of hackers have set up. Break into their network and see what you can learn: PCAP3.cap*

1. What is the MAC address of the router?
	1. opening up wireshark, we will find that the first packet (named `TpLinkTechno_80...`) is the router since it is the SSID (Service Set Identifier) name! 
	2. click on that first packet > expand the "IEEE 802.11 Beacon frame" packet dissector > find `BSS Id: TpLinkTechno_80:76:e4 (c0:4a:00:80:76:e4)` > and `c0:4a:00:80:76:e4` is the MAC address!!!

2. What is the ESSID of the wifi network?
	1. ESSID (Extended Service Set Identifier) and SSID (Service Set Identifier) are used interchangeably, and in wireshark, it uses SSID, so we look for SSID!
	2. expand the "IEEE 802.11 Wireless Management" packet dissector > "Tagged parameters..." > "Tag: SSID parameter set: "TP-LINK_8076E4"" > `SSID: "TP-LINK_8076E4"` > `TP-LINK_8076E4`!!!

> There is another way to solve #1 and #2 more faster by using aircrack on your Kali Linux terminal, run: `$ aircrack-ng PCAP3.cap`  and it will provide you with both the MAC address and the ESSID of the wifi network.

3. What is the password for the wireless network?
	1. to crack the password, we can use aircrack! we will be using the rockyou wordlist: `aircrack-ng -w /usr/share/wordlists/rockyou.txt -b C0:4A:00:80:76:E4 PCAP3`
		1. `-w`: specifies wordlist
		2. `-b`: specifies the target access point's BSSID
		3. `PCAP3`: the file containing the packet capture of the network traffic
	2.  `KEY FOUND! [ blueberrymuffin ]` > the password is `blueberrymuffin`!!

4. What is the IP address of the router?
	1. on wireshark, we will decrypt the traffic using the WPA decryption key (`blueberrymuffin`) from earlier! > this can be done by selecting "Edit" (top left) > "Preferences" > "Protocols" > "IEEE 802.11" > then, check "Enable decryption" > click "Edit..." for Decryption keys > press the `+` sign to add a "wpa-pwd (WPA password)" key type, and paste in `blueberrymuffin` for the key! > enter, then click "Apply" to apply the decryption!
	2. after applying and pressing OK, we will see that there is a new packet (~38) which contains the source IP address and destination IP address as DHCP packet > we look at the source IP: `192.168.0.254`!!!

5. What company manufactured the router?
	1. after decrypting the network traffic, you will see a bunch of new TCP and HTTP packets! we can filter these by using the display filter: `tcp && http`
	2. click on the first packet > follow HTTP stream > and find: `WWW-Authenticate: Basic realm="TP-LINK Wireless N Nano Router WR702N"` (the exact router model!) > the company is `TP-LINK`!!!

6. What is the model of the router?
	1.  `TP-LINK Wireless N Nano Router WR702N`

7. What firmware version is installed on the router?
	1. find the HTTP packet with info `GET /userRpm/StatusRpm.htm HTTP/1.1` (this path is the internal status page used by TP-Link routers for their web management interface) > follow HTTP stream > find `"4.19.1 Build 130528 Rel.52704n ",` > the version is: `4.19.1`

8. What release number is the router using?
	1. `"4.19.1 Build 130528 Rel.52704n ",` > `Rel.52704n` > `52704n`!!!

9. What is the IP address of the user who logged into the router admin panel?
	1. TWO ways:
		1. look at the destination IP address of the packet > `192.168.0.101`!!!
		2. on the HTTP stream of that packet, click "Entire Conversation" and find the IP address which isn't the router IP address (`192.168.0.254`) > `192.168.0.101`!!!

10. What is the MAC address of the first victim of the deauth attack?
	1. A Wi-Fi deauthentication (deauth) attack is a type of denial-of-service (DoS) attack that disconnects a target device from a wireless access point (WAP) > we have to filter the packets that selects specific packets which contain deauth attacks, and to do this, we use the display filter: `wlan.fc.type_subtype == 12` 
	2. clicking on the first packet, we expand the "IEEE 802.11 Deauthentication" packet dissector > and find: `Destination address: Apple_47:44:38 (b8:e8:56:47:44:38)` > `b8:e8:56:47:44:38` !!!

11. What is the MAC address of the second victim of the deauth attack?
	1. scroll down until you find another destination address which is different than the router MAC address and the first victim MAC address > `Destination address: Apple_0b:26:ba (80:e6:50:0b:26:ba)` > `80:e6:50:0b:26:ba`!!!
