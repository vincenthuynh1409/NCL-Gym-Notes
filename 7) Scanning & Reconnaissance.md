# Scanning & Reconnaissance

## DNS Resolver Query

### 🛠️ Tools

1. `$ dig [@server-hostname] [record type: A,MX,...] [domain] [options]`
	1. `A`: maps domain to IPv4 address
	2. `MX`: Identifies mail servers
	3. `NS`: Identifies authoritative DNS servers
	4. `RP`: responsible person

### Dig (Easy) Write-up

- *You've been tasked to help the newly launched company, Fortaigan, audit its DNS records. To complete the tasks, use the device with the `resolver` hostname in the provided terminal as the DNS resolver to query for `fortaigan.net`.*

> We will be using the provided terminal container for this challenge instead of Kali Linux VM

1. What is the IPv4 address for `fortaigan.net`?
	1. `$ dig @resolver A fortaigan.net`
		1. `A`: maps domain to IPv4 address

2. How many mail servers does `fortaigan.net` have?
	1. `$ dig @resolver MX fortaigan.net`
		1. `MX`: Identifies mail servers

3. What's the name of the mail server that takes the first priority?
	1. from #2, find the priority number that is LEAST
	2. in this case, it is 3 which corresponds to email: `maila2.fortaigan.net`!!!

4. What is the IPv4 address of the name server record for `fortaigan.net`?
	1.  `$ dig @resolver NS fortaigan.net` 
		1. `NS`: Identifies authoritative DNS servers
	2. we will find: `ns1.fortaigan.net`, which is the name server
	3. now, we will use the `A` record  type to get the IPv4 address for that name server by running:  `$ dig @resolver A ns1.fortaigan.net` > `104.231.73.10`!!!

5. What's the full name of the responsible person at `fortaigan.net`?
	1. `$ dig @resolver RP fortaigan.net`
		1. `RP`: responsible person
	2. we will find the contact email: `admin.fortaigan.net` > now use the email address to find the full name of the person responsible! 
	3. `$ dig @resolver TXT admin.fortaigan.net`
		1. `TXT`: stores text information

6. What is the text flag found in the DNS records?
	1. `$ dig @resolver TXT fortaigan.net`

7. What is the priority number given to the SIP service using the TCP protocol on `fortaigan.net`?
	1. `dig @resolver SRV _sip._tcp.fortaigan.net`
		1. `SRV`: Identifies servers providing specific services
		2. `_sip._tcp.`: standard SIP SRV hostname
	2. you would get something like:  `0 5 5060 sipserver.fortaigan.net.` > `0` would be the priority number!!!

8. What is the IPv4 address of the machine that's running the SIP service for `fortaigan.net`?
	1. `dig @resolver A sipserver.fortaigan.net`

<br>

