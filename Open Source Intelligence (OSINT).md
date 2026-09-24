# Open Source Intelligence (OSINT) 

#Cybersecurity #CTF #NCL #Resources 

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

![[Pasted image 20260921175046.png]]

<br>

## Lookup

A quick online search of each question should provide several sources with the answer. 

Be careful to make sure that the answer that you obtain can be verified using an authoritative source.

If you search for “DNS protocol specification”, you should find that the Internet Engineering Task Force (IETF) publishes the specification for DNS. You should use IETF resources as the authoritative source for answers.

Knowing how to read and understand a specification document is important because many technologies across all industries use these types of documents to keep implementation uniform.

DNSSEC is described in [RFC 4034](https://datatracker.ietf.org/doc/html/rfc4034). The information related to the record can be found in section 2.

The DNS Extension to Support IPv6 is described in [RFD 3596](https://www.rfc-editor.org/rfc/rfc3596). The information related to the record can be found in section 2.

The DNS record to delegate a DNS zone is described in [RFC 1035](https://datatracker.ietf.org/doc/html/rfc1035). Answer this challenge require reading the specification to understand what it means to delegate a DNS zone in order to identify that they DNS record type that is need to delegate a DNS zone is the one that indicates an authoritative name server.

<br>

## Threat Intelligence

This challenge will give you experience conducting research on common security vulnerabilities. All that is required to solve these questions is to query online search engines and find multiple sources to confirm the answers.

Wikipedia can be a good place for open source intelligence work because multiple sources for the information are often linked on the page. Always be sure to double check and verify your answer with another source!

When you want to make searching Wikipedia easier, or search any webpage or document, use `CTRL + F` on your keyboard and enter what you want to find into the dialog box that pops up.

<br>

## HTTP Headers

This challenge will give you experience researching HTTP headers.

The answers to these questions can be found by doing an online search. A full table of [HTTP headers can be found on Wikipedia](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

A useful skill for this challenge is to easily search or find keywords or phrases on a webpage. One method involves using the search function by pressing “CTRL + F” on the keyboard. This opens a dialog box where a keyword or short phrase can be entered to find specific content on the page.

It may be helpful to research and define unfamiliar terms from the question beforehand for better comprehension. Afterward, using “CTRL + F” can assist in identifying related terms or similar language within the page content.

<br>

## WHOIS

**WHOIS** = protocol for querying databases that store information about Internet resources and domain names.

**Domain Name** = human-readable address that identifies resources on the Internet. Instead of the numerical IP addresses (e.g. `8.8.8.8`) that computers use, domain names (e.g. `google.com`) provide an easier way for humans to access the Internet.

**DNS** = manages and translates domain names 

### 🛠️ Tools

1. `$ whois [domain]` (Kali Linux terminal)
2. https://lookup.icann.org/en (browser-based WHOIS tool)

### WHOIS (Easy) Write-Up

1. Type `$ whois cityinthe.cloud` in Kali Linux terminal
![[Pasted image 20260921173719.png]]

<br>

## PGP Lookup

**PGP (Pretty Good Privacy)** utilizes public-key cryptography wherein a public/private key pair is used to encrypt, decrypt, and sign messages.

**PGP Cryptography** = allows a message to be encrypted so that it can only be decrypted by its intended recipient. 
- To achieve this, the sender will use the recipient’s public key to encrypt the message so that only the recipient’s private key can decrypt the message.

> Alice requires Bob’s public key in order to encrypt a message so that only his private key may read it! 

There are public databases that store records of public keys and their owners so that a sender may obtain their recipient’s public key to encrypt a message for them. 

> There is no one single authoritative source keeping records of public keys, so it is important to compare the results across multiple different databases.

### 🛠️ Tools

1. https://keyserver.ubuntu.com/ (best one?)
2. https://keys.openpgp.org/
3. https://pgp.mit.edu/ (MIT PGP Public Key Server)

### PGP Lookup (Easy) Write-Up

1. https://keyserver.ubuntu.com/
2. search up `security@cpanel.net`
3. the key fingerprint is the stuff after `rsa4096/...`
	- there are two correct answers for this!

![[Screenshot 2026-09-21 180903.png]]

4. to find the email associated with a fingerprint, go back to https://keyserver.ubuntu.com/
	- (like `7A39A56B73D1E097D57435CFCDE2DE1DCB2077F2`)
5. add `0x` in front of `7A39A56B73D1E097D57435CFCDE2DE1DCB2077F2`
6. search up: `0x7A39A56B73D1E097D57435CFCDE2DE1DCB2077F2`
7. find email as well as expiration date

![[Pasted image 20260921180829.png]]

<br>

## SSL

**SSL certificates** = help to secure the communication between a client and a server. 
- Most modern browsers should have an interface to view the certificates in a SSL certificate chain. In this example, Google Chrome is used.

### 🛠️ Tools

1. https://www.sslshopper.com/ssl-checker.html (SSL checker)
2. Google Browser URL Icon (left of URL) > Connection is Secure > Certificate is Valid > Details

### SSL (Medium) Write-Up

1. Google Browser URL Icon (left of URL) > Connection is Secure > Certificate is Valid > Details

![[Pasted image 20260921183104.png]]

2. OR type `www.cyberskyline.com` into https://www.sslshopper.com/ssl-checker.html

<br>

## Barcode

### 🛠️ Tools

- https://online-barcode-reader.inliteresearch.com/ (Barcode Reader)
### Barcode (Medium) Write-Up

1. open Barcode Reader website
2. download barcode file
3. select barcode type > drag and drop > "read"

![[Pasted image 20260923152049.png]]

<br>

## Wayback Machine

**The Wayback Machine** = digital archive that allows you to see past versions of websites, old documents, and sometimes videos. Knowing how to use it is an important tool in OSINT.
### 🛠️ Tools

- https://web.archive.org/ (Wayback Machine)

### OWASP (Hard) Write-Up

1. https://web.archive.org/ (Wayback Machine)
2. Blue dots represent days with archived pages. Click on days with blue dots to see full pages. Green dots will have pages with redirects.

![[Screenshot 2026-09-23 182234.png]]

3. To find files from URLs:

![[Pasted image 20260923182340.png]]
