# Open Source Intelligence (OSINT) 

## Metadata Extraction

**Metadata** = hidden "data about data" that describes a digital file's history, characteristics, and context without showing its visible content

### 🛠️ Tools

1. `$ exiftool [filename]` (Kali Linux terminal)
2. http://metadata2go.com/ (Metadata2go website - aesthetic/techy, clean UI layout)
	- view metadata > drag and drop file > extract/view metadata
3. https://exif.tools/ (exiftool website - clean, simple UI layout)
	- drag and drop file > extract/view metadata
### Meta (Easy) Write-Up

1. Drag and drop image file from main Windows machine → Kali Linux VM machine 
2. Saved as `Meta.jpg`
3. Extract/View metadata: `$ exiftool Meta.jpg`

<img width="709" height="838" alt="image" src="https://github.com/user-attachments/assets/de575eb9-5e1c-4c80-a708-6da013fc354c" />


<br>

## Lookup

Do online search of each question to get your answers.

> Make sure answers are verified using an authoritative source!

If you search for “DNS protocol specification”, you should find that the Internet Engineering Task Force (IETF) publishes the specification for DNS. 
You should use IETF resources as the authoritative source for answers!


- DNSSEC - [RFC 4034](https://datatracker.ietf.org/doc/html/rfc4034) (Section 2)

- DNS Extension to Support IPv6 - [RFD 3596](https://www.rfc-editor.org/rfc/rfc3596) (Section 2)

- DNS record to delegate a DNS zone - [RFC 1035](https://datatracker.ietf.org/doc/html/rfc1035)

<br>

## Threat Intelligence

To solve these questions, simply just query online search engines like Wiki and find multiple sources to confirm the answer!

**Wikipedia** can be a good place for OSINT because multiple sources for the information are often linked on the page. 

> Always be sure to double check and verify your answer with another source!!!

When you want to make searching Wikipedia easier, or search any webpage or document, use `CTRL + F` on your keyboard and enter what you want.

<br>

## HTTP Headers

The answers to these questions can be found by doing an online search!

- Full Table of HTTP headers (Wiki): https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields

When you want to make searching Wikipedia easier, or search any webpage or document, use `CTRL + F` on your keyboard and enter what you want.


<br>

## WHOIS

**WHOIS** = protocol for querying databases that store information about Internet resources and domain names.

**Domain Name** = human-readable address (from IP address) that identifies resources on the Internet.

**DNS** = manages and translates domain names! 

### 🛠️ Tools

1. `$ whois [domain]` (Kali Linux terminal)
2. https://lookup.icann.org/en (browser-based WHOIS tool)

### WHOIS (Easy) Write-Up

1. Type `$ whois cityinthe.cloud` in Kali Linux terminal

<img width="891" height="637" alt="image" src="https://github.com/user-attachments/assets/9dd50610-df87-42b0-8a65-3b76b0b86666" />


<br>

## PGP Lookup

**PGP (Pretty Good Privacy)** utilizes public-key cryptography wherein a public/private key pair is used to encrypt, decrypt, and sign messages.

**PGP Cryptography** = allows a message to be encrypted so that it can only be decrypted by its intended recipient. 
- To achieve this, the sender will use the recipient’s public key to encrypt the message so that only the recipient’s private key can decrypt the message.

There are public databases that store records of public keys and their owners so that a sender may obtain their recipient’s public key to encrypt a message for them. 

> Make sure you compare the results across multiple different databases.

### 🛠️ Tools

1. https://keyserver.ubuntu.com/ (best one?)
2. https://keys.openpgp.org/
3. https://pgp.mit.edu/ (MIT PGP Public Key Server)

### PGP Lookup (Easy) Write-Up

1. https://keyserver.ubuntu.com/
2. search up `security@cpanel.net`
3. the key fingerprint is the stuff after `rsa4096/...`
	- there are two correct answers for this!

<img width="993" height="877" alt="image" src="https://github.com/user-attachments/assets/066f63cf-074f-4ed0-af90-56511b71ddd6" />


4. to find the email associated with a fingerprint, go back to https://keyserver.ubuntu.com/
	- (like `7A39A56B73D1E097D57435CFCDE2DE1DCB2077F2`)
5. add `0x` in front of `7A39A56B73D1E097D57435CFCDE2DE1DCB2077F2`
6. search up: `0x7A39A56B73D1E097D57435CFCDE2DE1DCB2077F2`
7. find email as well as expiration date

<img width="1912" height="429" alt="image" src="https://github.com/user-attachments/assets/38bb112a-4e2e-41dd-bd03-5e49dcf1e5db" />


<br>

## SSL

**SSL certificates** = help to secure the communication between a client and a server. 
- Most modern browsers should have an interface to view the certificates in a SSL certificate chain. In this example, Google Chrome is used.

### 🛠️ Tools

1. https://www.sslshopper.com/ssl-checker.html (SSL checker)
2. Google Browser URL Icon (left of URL) > Connection is Secure > Certificate is Valid > Details

### SSL (Medium) Write-Up

1. Google Browser URL Icon (left of URL) > Connection is Secure > Certificate is Valid > Details

<img width="303" height="75" alt="image" src="https://github.com/user-attachments/assets/4a3b3ec9-28bf-4335-8a69-0d8250ae1aa8" />


2. OR type `www.cyberskyline.com` into https://www.sslshopper.com/ssl-checker.html

<br>

## Barcode

### 🛠️ Tools

- https://online-barcode-reader.inliteresearch.com/ (Barcode Reader)
### Barcode (Medium) Write-Up

1. open Barcode Reader website
2. download barcode file
3. select barcode type > drag and drop > "read"

<img width="819" height="426" alt="image" src="https://github.com/user-attachments/assets/e1f63383-8803-40e7-af66-b4c809f49ddc" />

> type, contents, etc are shown in image above!


<br>



## Wayback Machine

**The Wayback Machine** = digital archive that allows you to see past versions of websites, old documents, and sometimes videos. Knowing how to use it is an important tool in OSINT.
### 🛠️ Tools

- https://web.archive.org/ (Wayback Machine)

### OWASP (Hard) Write-Up

1. https://web.archive.org/ (Wayback Machine)
2. Blue dots represent days with archived pages. Click on days with blue dots to see full pages. Green dots will have pages with redirects.

<img width="930" height="814" alt="image" src="https://github.com/user-attachments/assets/9fb44906-d6e8-453d-b45b-efac7bdf917b" />


3. To find files from URLs:

<img width="1482" height="546" alt="image" src="https://github.com/user-attachments/assets/b80b3324-fb35-4f85-bafd-17a4b7026ae5" />

