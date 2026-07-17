
# 🌐 DNS (Domain Name System) - The Internet's Phonebook

> **"Almost every connection on the Internet begins with a DNS lookup. If you can understand DNS, you can often detect malicious activity before the attacker even reaches their target."**

---

# 🎯 Purpose

Humans are good at remembering names.

Computers are good at remembering numbers.

DNS (Domain Name System) acts as a translator between the two by converting **human-readable domain names** into **IP addresses**.

For example:

```
github.com
```

becomes

```
140.82.121.4
```

Without DNS, every website, cloud service, email server, API, and application would need to be accessed using its IP address, making the Internet extremely difficult to use.

For SOC Analysts, DNS is one of the **most valuable telemetry sources** because almost every cyber attack—whether phishing, malware, ransomware, or Command & Control (C2)—starts with a DNS query.

---

# 💡 Real World Analogy

Imagine you want to call your friend.

You remember their name:

```
John
```

But your phone cannot call a name.

It needs a phone number.

Your contacts application translates:

```
John

↓

+91-98XXXXXXXX
```

DNS performs the exact same function for computers.

```
github.com

↓

140.82.121.4
```

Instead of remembering long numerical IP addresses, users only need to remember simple domain names.

---

# 🌍 Why DNS Exists

Imagine trying to browse the Internet like this:

```
https://142.250.183.78

https://140.82.121.4

https://13.107.42.14

https://151.101.1.69
```

Most people would never remember these addresses.

DNS was created to solve this problem by introducing readable names such as:

```
google.com

github.com

microsoft.com

amazon.com
```

This abstraction makes the Internet user-friendly while still allowing computers to communicate using IP addresses.

---

# 🔄 Complete DNS Resolution Process

Let's follow what happens when Alice opens GitHub.

She types:

```
https://github.com
```

Although it appears simple, the following events occur within milliseconds.

```
Alice

↓

Browser

↓

Browser DNS Cache

↓

Operating System DNS Cache

↓

Hosts File

↓

Configured DNS Resolver

↓

Root DNS Server

↓

Top Level Domain (TLD) Server

↓

Authoritative DNS Server

↓

IP Address Returned

↓

TCP Connection

↓

TLS Handshake

↓

HTTP Request

↓

GitHub Website Loads
```

This entire sequence normally completes in under a second.

---

# 🥇 Step 1 - Browser Cache

The browser first checks whether it has already resolved the domain recently.

Example:

```
github.com

↓

140.82.121.4
```

If the record is still valid (TTL has not expired), the browser immediately uses the cached IP address.

No DNS traffic is generated.

This improves performance and reduces unnecessary DNS queries.

---

### 🔵 SOC Perspective

During investigations, remember that not every website visit generates a DNS request.

If the domain is cached, there may be no DNS event at all.

Investigators should correlate:

- Browser History
- Browser Cache
- DNS Cache
- Proxy Logs
- Network Traffic

before concluding that a website was never accessed.

---

# 🥈 Step 2 - Operating System Cache

If the browser cache does not contain the record, the operating system checks its own DNS cache.

Examples:

Windows

Linux

macOS

Each operating system maintains a temporary cache of recently resolved domains.

This reduces network latency and decreases DNS server load.

---

### Windows Example

View cached DNS entries:

```powershell
ipconfig /displaydns
```

Clear the cache:

```powershell
ipconfig /flushdns
```

---

### 🔴 Red Team Perspective

Some malware clears the DNS cache to:

- Remove traces of malicious lookups.
- Force new DNS resolutions.
- Redirect victims after modifying DNS settings.

---

### 🔵 Blue Team Perspective

Unexpected DNS cache clearing may indicate:

- Malware execution.
- Administrative troubleshooting.
- Anti-forensic behavior.

Always correlate with:

- Process Creation
- PowerShell Logs
- Command Line Arguments
- User Activity

---

# 🥉 Step 3 - Hosts File

Before querying any DNS server, the operating system checks the local **hosts file**.

Windows:

```
C:\Windows\System32\drivers\etc\hosts
```

Linux/macOS:

```
/etc/hosts
```

The hosts file allows administrators to manually map domain names to IP addresses.

Example:

```
192.168.1.15 portal.company.local
```

When an entry exists here, DNS is completely bypassed.

---

### 🔴 Red Team Perspective

Attackers frequently abuse the hosts file to:

- Redirect banking websites.
- Redirect software update servers.
- Block antivirus updates.
- Block Microsoft Defender cloud services.
- Redirect victims to phishing pages.
- Disable access to security vendors.

Example:

```
127.0.0.1

www.microsoft.com
```

This would prevent the victim from reaching Microsoft's website.

---

### 🔵 Blue Team Perspective

Always inspect the hosts file during malware investigations.

Look for:

- Recently modified entries.
- Security vendor domains.
- Banking websites.
- Internal company domains.
- Unknown external IP addresses.

Many commodity malware families modify the hosts file as part of persistence or defense evasion.

---

# 🏅 Step 4 - Recursive DNS Resolver

If the hosts file contains no matching entry, the client sends a DNS request to its configured recursive resolver.

Common examples include:

- Corporate DNS Server
- ISP DNS
- Google Public DNS (8.8.8.8)
- Cloudflare DNS (1.1.1.1)
- Quad9 (9.9.9.9)

The recursive resolver performs the remaining DNS lookup on behalf of the client.

Most organizations monitor this stage because it provides excellent visibility into user activity.

---

# 🏆 Step 5 - Root DNS Server

If the recursive resolver does not already know the answer, it contacts one of the Internet's Root DNS Servers.

The Root Server does **not** know the IP address of GitHub.

Instead, it knows where to find the correct Top-Level Domain (TLD) server.

Example:

```
Where can I find .com?
```

The Root Server responds with the location of the appropriate `.com` TLD servers.

---

# 🎖️ Step 6 - Top-Level Domain (TLD) Server

The resolver now contacts the `.com` TLD server.

Question:

```
Where is github.com?
```

The TLD server replies:

```
Ask GitHub's Authoritative Name Server.
```

It provides the address of the authoritative server responsible for the domain.

---

# 🏁 Step 7 - Authoritative DNS Server

The resolver contacts GitHub's authoritative server.

This server owns the official DNS records.

Response:

```
github.com

↓

140.82.121.4
```

The resolver caches the result and returns it to Alice's computer.

Only now can the browser establish a TCP connection and continue loading the website.

---

# 💼 Real SOC Investigation

## Incident

A user reports that clicking a PDF invoice resulted in a suspicious download.

The SOC begins reconstructing the attack timeline.

### DNS Logs

```
09:14:22

finance-invoice-update.xyz

↓

Resolved

185.xxx.xxx.xxx
```

### Proxy Logs

```
GET /Invoice.pdf

↓

200 OK

↓

application/octet-stream
```

### Sysmon

```
Event ID 1

powershell.exe

↓

Invoke-WebRequest
```

### Defender

```
Trojan Downloader Detected
```

### Investigation

Although the malware itself was downloaded over HTTPS, the first observable indicator was the DNS query to a newly registered domain.

By correlating DNS, Proxy, Sysmon, and Defender telemetry, the analyst quickly reconstructed the complete attack chain and identified patient zero.

---

# 🎯 Key Takeaways

✅ DNS translates domain names into IP addresses.

✅ Most Internet communications begin with a DNS lookup.

✅ DNS records may be resolved from cache, the hosts file, or external DNS servers.

✅ DNS is one of the earliest indicators of compromise during many cyber attacks.

✅ SOC analysts should always correlate DNS telemetry with endpoint, proxy, firewall, EDR, and authentication logs to build a complete picture of an attack.

---

# 🔗 Coming Next

In the next section, we'll dive into **DNS Records (A, AAAA, CNAME, MX, TXT, NS, PTR, SOA, SRV)**, explaining how each record works, how attackers abuse them, and how SOC analysts use them during threat hunting, malware analysis, phishing investigations, and incident response.

---

# 📚 DNS Records - Understanding the Different Types of DNS Entries

> **"A DNS query is only half the story. The real intelligence comes from understanding which DNS record was requested and why."**

---

# 🎯 Purpose

When you visit a website, send an email, connect to a VPN, or use cloud applications, DNS doesn't simply return an IP address.

Instead, it looks up different **DNS Record Types**, each designed for a specific purpose.

Think of DNS as a huge database.

Every domain contains multiple records.

Each record answers a different question.

For example:

- What is the website's IP address?
- Which mail server should receive emails?
- Which server is authoritative?
- Is this domain verified by Microsoft?
- Which service should this application connect to?

As a SOC Analyst, identifying **which record type** was queried can immediately reveal an attacker's objective.

---

# 🗂️ Common DNS Record Types

| Record | Purpose | Example |
|---------|---------|----------|
| A | Maps Domain → IPv4 Address | github.com → 140.82.121.4 |
| AAAA | Maps Domain → IPv6 Address | github.com → 2606:50c0:: |
| CNAME | Creates an Alias | mail.company.com → outlook.office365.com |
| MX | Mail Server | gmail.com → alt1.gmail-smtp-in.l.google.com |
| TXT | Text Information | SPF, DKIM, Domain Verification |
| NS | Name Server | ns1.cloudflare.com |
| PTR | Reverse Lookup | IP → Domain |
| SOA | Zone Information | DNS Zone Configuration |
| SRV | Service Discovery | _ldap._tcp.company.local |

Each record plays an important role in how the Internet functions.

---

# 🌐 DNS Zone Example

Imagine a company owns the domain:

```
company.com
```

Its DNS zone might look like this:

```
company.com
│
├── A
├── AAAA
├── MX
├── TXT
├── NS
├── CNAME
├── SOA
└── SRV
```

Every service uses different records.

---

# 1️⃣ A Record (Address Record)

## Purpose

The **A Record** maps a domain name to an **IPv4 address**.

Example:

```
github.com

↓

140.82.121.4
```

Without an A Record, browsers would never know where the website is located.

---

### Example

```dns
github.com

A

140.82.121.4
```

---

### Real Life

When you type:

```
https://github.com
```

The browser usually requests an **A Record** first.

---

### 🔴 Red Team Perspective

Attackers abuse A Records by:

- Hosting malware.
- Hosting phishing websites.
- Rotating IP addresses.
- Fast Flux infrastructure.
- Command & Control Servers.

---

### 🔵 Blue Team Perspective

Investigate:

- Newly observed IP addresses.
- Reputation of resolved IP.
- ASN ownership.
- Geolocation.
- Unexpected IP changes.
- Connections to known malicious infrastructure.

---

### SOC Investigation

```
powershell.exe

↓

DNS Query

↓

update-security-check.xyz

↓

A Record

↓

185.xxx.xxx.xxx

↓

HTTPS Connection
```

The A Record helped identify the malicious server.

---

# 2️⃣ AAAA Record (IPv6 Address Record)

## Purpose

Functions exactly like an A Record,

but returns an IPv6 address.

Example:

```
google.com

↓

2607:f8b0:4005:805::200e
```

---

### Why It Matters

Many enterprise networks now support IPv6.

Attackers increasingly abuse IPv6 because:

- Less monitoring.
- Fewer detection rules.
- Limited firewall visibility.
- Analysts often overlook IPv6 traffic.

---

### 🔵 SOC Perspective

Don't ignore IPv6.

Many organizations have mature IPv4 detections but almost no IPv6 monitoring.

---

# 3️⃣ CNAME Record (Canonical Name)

## Purpose

Creates an alias.

Instead of pointing directly to an IP,

it points to another domain.

Example:

```dns
portal.company.com

CNAME

company.azurewebsites.net
```

DNS continues resolving until it reaches an A or AAAA record.

---

### Why Companies Use CNAME

Instead of changing hundreds of DNS entries,

they only update one destination.

Common with:

- Microsoft 365
- AWS
- Azure
- Cloudflare
- CDNs

---

### 🔴 Red Team Perspective

Attackers abuse CNAMEs during:

- Domain Takeover
- Dangling DNS Records
- Cloud Service Abuse

One forgotten CNAME can allow attackers to hijack an abandoned cloud resource.

---

### 🔵 Blue Team Perspective

Monitor for:

- Dangling CNAMEs.
- Unexpected cloud providers.
- Expired services.
- Third-party infrastructure.

---

# 4️⃣ MX Record (Mail Exchange)

## Purpose

Specifies which server receives email.

Example:

```dns
company.com

MX

mail.company.com
```

Without MX records,

email would not work.

---

### SOC Relevance

MX records are extremely important during:

- Phishing investigations.
- Email spoofing.
- Business Email Compromise (BEC).
- SPF failures.
- DKIM validation.
- DMARC investigations.

---

### 🔴 Red Team Perspective

Attackers often:

- Register lookalike domains.
- Configure malicious mail servers.
- Send phishing campaigns.
- Spoof executive email addresses.

---

### 🔵 Blue Team Perspective

Verify:

- Mail server reputation.
- SPF configuration.
- DKIM signatures.
- DMARC policies.
- Unauthorized MX changes.

---

# 5️⃣ TXT Record (Text Record)

## Purpose

Stores arbitrary text information.

Originally designed for notes,

today TXT records are heavily used for:

- SPF
- DKIM
- DMARC
- Microsoft Verification
- Google Verification
- AWS Verification

---

### Example

```dns
v=spf1 include:spf.protection.outlook.com -all
```

---

### 🔴 Red Team Perspective

TXT records are frequently abused for:

- Command & Control.
- Malware configuration.
- Encryption keys.
- DNS Tunneling.
- Payload delivery.

Because TXT records can contain long strings of text,

they are attractive for attackers.

---

### Malware Spotlight

Malware such as:

- Cobalt Strike
- DNSMessenger
- FrameworkPOS
- POS malware families

have historically abused TXT records for C2 communication.

---

### 🔵 Blue Team Perspective

Investigate:

- Large TXT responses.
- High-frequency TXT lookups.
- Random encoded strings.
- Base64-like TXT data.
- Repeated TXT queries to unknown domains.

---

# 💼 Real SOC Investigation

## Alert

```
Host:

SALES-LAPTOP-02

↓

DNS Query

↓

TXT Record

↓

update-check-service.xyz

↓

Returned

Q2hhbmdlQzJTZXR0aW5ncw==
```

The analyst notices the response appears to be Base64.

After decoding:

```
ChangeC2Settings
```

The TXT record wasn't storing verification data.

It was delivering instructions to malware.

---

# ⚡ Quick Revision

✅ A Records return IPv4 addresses.

✅ AAAA Records return IPv6 addresses.

✅ CNAME Records point one domain to another.

✅ MX Records determine where email is delivered.

✅ TXT Records store text but are also abused for malware communication.

---

# 🔗 Knowledge Connections

DNS Records
      │
      ├── HTTP / HTTPS
      ├── Email Security
      ├── SPF
      ├── DKIM
      ├── DMARC
      ├── DNS Tunneling
      ├── Fast Flux
      ├── Threat Intelligence
      ├── Malware C2
      ├── Phishing
      ├── Cloud Infrastructure
      └── Incident Response


> **Next Section:** We'll explore the remaining DNS records (**NS, PTR, SOA, and SRV**) and learn how they are used in Active Directory, reverse lookups, service discovery, and enterprise investigations.
---

# 📚 DNS Records - Understanding the Different Types of DNS Entries

> **"A DNS query is only half the story. The real intelligence comes from understanding which DNS record was requested and why."**

---

# 🎯 Purpose

When you visit a website, send an email, connect to a VPN, or use cloud applications, DNS doesn't simply return an IP address.

Instead, it looks up different **DNS Record Types**, each designed for a specific purpose.

Think of DNS as a huge database.

Every domain contains multiple records.

Each record answers a different question.

For example:

- What is the website's IP address?
- Which mail server should receive emails?
- Which server is authoritative?
- Is this domain verified by Microsoft?
- Which service should this application connect to?

As a SOC Analyst, identifying **which record type** was queried can immediately reveal an attacker's objective.

---

# 🗂️ Common DNS Record Types

| Record | Purpose | Example |
|---------|---------|----------|
| A | Maps Domain → IPv4 Address | github.com → 140.82.121.4 |
| AAAA | Maps Domain → IPv6 Address | github.com → 2606:50c0:: |
| CNAME | Creates an Alias | mail.company.com → outlook.office365.com |
| MX | Mail Server | gmail.com → alt1.gmail-smtp-in.l.google.com |
| TXT | Text Information | SPF, DKIM, Domain Verification |
| NS | Name Server | ns1.cloudflare.com |
| PTR | Reverse Lookup | IP → Domain |
| SOA | Zone Information | DNS Zone Configuration |
| SRV | Service Discovery | _ldap._tcp.company.local |

Each record plays an important role in how the Internet functions.

---

# 🌐 DNS Zone Example

Imagine a company owns the domain:

```
company.com
```

Its DNS zone might look like this:

```
company.com
│
├── A
├── AAAA
├── MX
├── TXT
├── NS
├── CNAME
├── SOA
└── SRV
```

Every service uses different records.

---

# 1️⃣ A Record (Address Record)

## Purpose

The **A Record** maps a domain name to an **IPv4 address**.

Example:

```
github.com

↓

140.82.121.4
```

Without an A Record, browsers would never know where the website is located.

---

### Example

```dns
github.com

A

140.82.121.4
```

---

### Real Life

When you type:

```
https://github.com
```

The browser usually requests an **A Record** first.

---

### 🔴 Red Team Perspective

Attackers abuse A Records by:

- Hosting malware.
- Hosting phishing websites.
- Rotating IP addresses.
- Fast Flux infrastructure.
- Command & Control Servers.

---

### 🔵 Blue Team Perspective

Investigate:

- Newly observed IP addresses.
- Reputation of resolved IP.
- ASN ownership.
- Geolocation.
- Unexpected IP changes.
- Connections to known malicious infrastructure.

---

### SOC Investigation

```
powershell.exe

↓

DNS Query

↓

update-security-check.xyz

↓

A Record

↓

185.xxx.xxx.xxx

↓

HTTPS Connection
```

The A Record helped identify the malicious server.

---

# 2️⃣ AAAA Record (IPv6 Address Record)

## Purpose

Functions exactly like an A Record,

but returns an IPv6 address.

Example:

```
google.com

↓

2607:f8b0:4005:805::200e
```

---

### Why It Matters

Many enterprise networks now support IPv6.

Attackers increasingly abuse IPv6 because:

- Less monitoring.
- Fewer detection rules.
- Limited firewall visibility.
- Analysts often overlook IPv6 traffic.

---

### 🔵 SOC Perspective

Don't ignore IPv6.

Many organizations have mature IPv4 detections but almost no IPv6 monitoring.

---

# 3️⃣ CNAME Record (Canonical Name)

## Purpose

Creates an alias.

Instead of pointing directly to an IP,

it points to another domain.

Example:

```dns
portal.company.com

CNAME

company.azurewebsites.net
```

DNS continues resolving until it reaches an A or AAAA record.

---

### Why Companies Use CNAME

Instead of changing hundreds of DNS entries,

they only update one destination.

Common with:

- Microsoft 365
- AWS
- Azure
- Cloudflare
- CDNs

---

### 🔴 Red Team Perspective

Attackers abuse CNAMEs during:

- Domain Takeover
- Dangling DNS Records
- Cloud Service Abuse

One forgotten CNAME can allow attackers to hijack an abandoned cloud resource.

---

### 🔵 Blue Team Perspective

Monitor for:

- Dangling CNAMEs.
- Unexpected cloud providers.
- Expired services.
- Third-party infrastructure.

---

# 4️⃣ MX Record (Mail Exchange)

## Purpose

Specifies which server receives email.

Example:

```dns
company.com

MX

mail.company.com
```

Without MX records,

email would not work.

---

### SOC Relevance

MX records are extremely important during:

- Phishing investigations.
- Email spoofing.
- Business Email Compromise (BEC).
- SPF failures.
- DKIM validation.
- DMARC investigations.

---

### 🔴 Red Team Perspective

Attackers often:

- Register lookalike domains.
- Configure malicious mail servers.
- Send phishing campaigns.
- Spoof executive email addresses.

---

### 🔵 Blue Team Perspective

Verify:

- Mail server reputation.
- SPF configuration.
- DKIM signatures.
- DMARC policies.
- Unauthorized MX changes.

---

# 5️⃣ TXT Record (Text Record)

## Purpose

Stores arbitrary text information.

Originally designed for notes,

today TXT records are heavily used for:

- SPF
- DKIM
- DMARC
- Microsoft Verification
- Google Verification
- AWS Verification

---

### Example

```dns
v=spf1 include:spf.protection.outlook.com -all
```

---

### 🔴 Red Team Perspective

TXT records are frequently abused for:

- Command & Control.
- Malware configuration.
- Encryption keys.
- DNS Tunneling.
- Payload delivery.

Because TXT records can contain long strings of text,

they are attractive for attackers.

---

### Malware Spotlight

Malware such as:

- Cobalt Strike
- DNSMessenger
- FrameworkPOS
- POS malware families

have historically abused TXT records for C2 communication.

---

### 🔵 Blue Team Perspective

Investigate:

- Large TXT responses.
- High-frequency TXT lookups.
- Random encoded strings.
- Base64-like TXT data.
- Repeated TXT queries to unknown domains.

---

# 💼 Real SOC Investigation

## Alert

```
Host:

SALES-LAPTOP-02

↓

DNS Query

↓

TXT Record

↓

update-check-service.xyz

↓

Returned

Q2hhbmdlQzJTZXR0aW5ncw==
```

The analyst notices the response appears to be Base64.

After decoding:

```
ChangeC2Settings
```

The TXT record wasn't storing verification data.

It was delivering instructions to malware.

---

# ⚡ Quick Revision

✅ A Records return IPv4 addresses.

✅ AAAA Records return IPv6 addresses.

✅ CNAME Records point one domain to another.

✅ MX Records determine where email is delivered.

✅ TXT Records store text but are also abused for malware communication.



# 🔗 Knowledge Connections

DNS Records
      │
      ├── HTTP / HTTPS
      ├── Email Security
      ├── SPF
      ├── DKIM
      ├── DMARC
      ├── DNS Tunneling
      ├── Fast Flux
      ├── Threat Intelligence
      ├── Malware C2
      ├── Phishing
      ├── Cloud Infrastructure
      └── Incident Response

> **Next Section:** We'll explore the remaining DNS records (**NS, PTR, SOA, and SRV**) and learn how they are used in Active Directory, reverse lookups, service discovery, and enterprise investigations.
---

# 🌍 Advanced DNS Records (NS, PTR, SOA & SRV)

> **"If A and AAAA records tell you where a server is, these records tell you who manages it, how services are discovered, and how DNS itself operates."**

---

# 🎯 Purpose

The remaining DNS records are not queried as frequently by end users, but they are **extremely important** for:

- Enterprise Networks
- Active Directory
- Domain Controllers
- Email Infrastructure
- Threat Hunting
- Incident Response
- Malware Analysis
- Digital Forensics

Many SOC analysts overlook these records, but attackers often interact with them during reconnaissance and lateral movement.

---

# 6️⃣ NS Record (Name Server Record)

## Purpose

The **NS (Name Server)** record tells the Internet **which DNS servers are authoritative for a domain**.

Think of it as asking:

> "Who is responsible for answering DNS questions about this domain?"

Example:

```dns
github.com

NS

dns1.p08.nsone.net

dns2.p08.nsone.net
```

Whenever someone wants information about **github.com**, these authoritative name servers provide the official DNS records.

---

## Real World Example

Imagine a company owns:

```
company.com
```

Its DNS configuration might look like:

```dns
company.com

↓

NS

ns1.cloudflare.com

↓

NS

ns2.cloudflare.com
```

Cloudflare is responsible for managing that domain's DNS records.

---

### 🔴 Red Team Perspective

Attackers perform NS lookups during reconnaissance to discover:

- DNS providers
- Cloud infrastructure
- Third-party hosting
- Attack surface
- Subdomain takeover opportunities

Common OSINT tools automatically retrieve NS records before attacking a target.

---

### 🔵 Blue Team Perspective

Investigate:

- Unexpected Name Server changes
- Unauthorized DNS provider changes
- New third-party DNS services
- DNS hijacking attempts
- Suspicious registrar modifications

A sudden NS record change can indicate:

- Domain Hijacking
- DNS Hijacking
- Registrar Compromise

---

# 7️⃣ PTR Record (Pointer Record)

## Purpose

Unlike A records:

```
Domain

↓

IP
```

PTR records perform the opposite operation.

```
IP

↓

Domain
```

This is called a **Reverse DNS Lookup**.

---

### Example

```dns
8.8.8.8

↓

dns.google
```

Instead of asking:

> "What IP belongs to github.com?"

you ask:

> "Which domain owns this IP?"

---

### Why Reverse DNS Matters

Many enterprise applications perform reverse lookups to:

- Validate servers
- Identify email senders
- Generate logs
- Verify trusted infrastructure

---

### 🔴 Red Team Perspective

Attackers perform PTR lookups during reconnaissance to:

- Identify exposed servers
- Map infrastructure
- Discover cloud resources
- Identify VPN gateways

---

### 🔵 Blue Team Perspective

PTR records help analysts:

- Identify unknown IP addresses
- Validate external servers
- Investigate phishing infrastructure
- Improve log readability

Instead of:

```
185.193.xxx.xxx
```

You immediately know:

```
mail.example-hosting.com
```

---

# 💼 SOC Investigation Example

Firewall Alert

```
Outbound Connection

↓

185.xxx.xxx.xxx
```

Without PTR

```
Unknown IP
```

With PTR

```
mail-backup.hostingprovider.net
```

This immediately provides valuable context.

---

# 8️⃣ SOA Record (Start of Authority)

## Purpose

The SOA record contains important administrative information about the DNS zone.

Think of it as the **identity card** of a DNS zone.

It tells DNS servers:

- Who owns the zone
- Which server is primary
- Serial Number
- Refresh Interval
- Retry Interval
- Expiration Time
- Default TTL

---

### Example

```dns
company.com

SOA

Primary DNS:

ns1.cloudflare.com

Admin:

admin@company.com

Serial:

2026071801
```

---

### Why It Matters

Whenever administrators modify DNS records,

the **Serial Number** increases.

Secondary DNS servers compare serial numbers to determine whether they need to synchronize updates.

---

### 🔴 Red Team Perspective

Attackers may inspect SOA records during reconnaissance to learn:

- DNS administrators
- Infrastructure details
- DNS providers
- Zone update frequency
- Internal naming conventions

---

### 🔵 Blue Team Perspective

SOA records help investigators:

- Verify DNS ownership
- Confirm DNS replication
- Identify misconfigurations
- Validate zone transfers

---

# 9️⃣ SRV Record (Service Record)

## Purpose

SRV records tell clients **where a specific service is running**.

Unlike A records,

they don't simply provide an IP address.

They identify:

- Service
- Protocol
- Hostname
- Port
- Priority
- Weight

---

### Example

```dns
_ldap._tcp.company.local

↓

dc01.company.local

↓

Port 389
```

This tells clients:

```
LDAP Service

↓

Domain Controller

↓

dc01.company.local

↓

Port 389
```

---

### Common Enterprise Services Using SRV

- Active Directory
- Kerberos
- LDAP
- SIP
- Microsoft Teams
- VoIP
- Microsoft Exchange

---

# 🏢 Active Directory Example

When a Windows computer joins a domain,

it doesn't magically know where the Domain Controller is.

Instead it performs:

```dns
_ldap._tcp.dc._msdcs.company.local
```

The DNS server replies:

```
dc01.company.local

↓

Port 389
```

The workstation now knows where to authenticate.

---

# 🔴 Red Team Perspective

Attackers frequently query SRV records during internal reconnaissance.

Why?

Because SRV records reveal:

- Domain Controllers
- Kerberos Servers
- LDAP Servers
- Global Catalog Servers
- Authentication Infrastructure

Tools like:

- BloodHound
- SharpHound
- PowerView
- Impacket

often rely on SRV lookups during Active Directory enumeration.

---

# 🔵 Blue Team Perspective

Monitor for:

- Unusual SRV lookups
- Excessive LDAP discovery
- Kerberos service enumeration
- Workstations querying many domain services
- Reconnaissance from compromised hosts

Large numbers of SRV queries may indicate Active Directory reconnaissance.

---

# 💼 Real SOC Investigation

## Alert

Windows workstation:

```
FINANCE-PC-17
```

Performs:

```
_ldap._tcp.dc._msdcs.company.local

↓

_kerberos._tcp.company.local

↓

_gc._tcp.company.local
```

Moments later:

```
LDAP Connections

↓

Kerberos Requests

↓

SMB Enumeration

↓

SharpHound.exe
```

### Investigation

The attacker was mapping the Active Directory environment before attempting privilege escalation.

The SRV queries provided the first indicator of internal reconnaissance.

---

# 🚨 Detection Opportunities

Investigate:

- Numerous SRV lookups.
- Large numbers of PTR lookups.
- Unexpected NS record changes.
- Unauthorized zone transfers (AXFR).
- Frequent SOA requests.
- Domain Controller discovery from non-administrative workstations.
- Reconnaissance immediately followed by LDAP or SMB traffic.

---

# 🎯 MITRE ATT&CK Mapping

| Activity | Technique |
|-----------|-----------|
| Active Directory Discovery | T1482 |
| Network Service Discovery | T1046 |
| System Network Configuration Discovery | T1016 |
| Domain Trust Discovery | T1482 |
| Internal Reconnaissance | Multiple Discovery Techniques |

---

# 🧪 SOC Investigation Challenge

## Alert

```
Endpoint

↓

ENG-LAPTOP-07

↓

DNS Activity

↓

_ldap._tcp.dc._msdcs.company.local

↓

_kerberos._tcp.company.local

↓

_gc._tcp.company.local

↓

445/TCP Connections

↓

BloodHound.exe Detected
```

### Investigation Questions

1. Why is this workstation querying LDAP services?
2. Is the user an administrator?
3. Was BloodHound executed?
4. Were Domain Controllers contacted?
5. Did Kerberos ticket requests increase?
6. Was lateral movement attempted?
7. Which Sysmon Event IDs should you review?
8. Which Active Directory logs should be correlated?
9. Is this attacker reconnaissance?
10. Which MITRE ATT&CK techniques apply?

---

# ⚡ Quick Revision

✅ **NS Records** identify the authoritative DNS servers for a domain.

✅ **PTR Records** perform reverse DNS lookups (IP → Domain).

✅ **SOA Records** contain administrative information about a DNS zone.

✅ **SRV Records** tell clients where network services such as LDAP and Kerberos are located.

✅ SRV records are especially important in **Active Directory** and frequently appear during attacker reconnaissance.

---

# 🏆 SOC Analyst Tip

During an investigation, don't stop at **A** records.

Always ask:

- Which **record type** was queried?
- Which **process** initiated the lookup?
- Which **user** was logged in?
- Was the lookup followed by network communication?
- Does the record reveal attacker reconnaissance, malware C2, or Active Directory discovery?

The **type of DNS record** often tells you **what the attacker is trying to accomplish**, even before they achieve it.


# 🔗 Knowledge Connections

Advanced DNS Records
        │
        ├── Active Directory
        ├── Kerberos
        ├── LDAP
        ├── Domain Controllers
        ├── BloodHound
        ├── SharpHound
        ├── PowerView
        ├── DNS Reconnaissance
        ├── Network Discovery
        ├── Threat Hunting
        ├── Windows Authentication
        ├── Incident Response
        └── Internal Network Enumeration

> **Next Section:** We'll move beyond DNS records and explore **DNS Caching, TTL (Time To Live), Recursive vs Authoritative DNS Servers, DNSSEC, DNS over HTTPS (DoH), DNS over TLS (DoT), and DNS Zone Transfers (AXFR)**—topics that are frequently encountered in enterprise SOC environments and advanced threat investigations.

---

# ⚡ DNS Caching & TTL (Time To Live) - Why DNS Doesn't Query Every Time

> **"DNS would be painfully slow if every request had to start from the Root Server. Caching makes the Internet fast, while TTL controls how long that cached information can be trusted."**

---

# 🎯 Purpose

Imagine opening:

```
https://github.com
```

100 times today.

If your computer contacted:

- Root DNS Servers
- TLD Servers
- Authoritative DNS Servers

every single time...

The Internet would become extremely slow.

To solve this problem, DNS uses **Caching**.

Caching stores previously resolved DNS records temporarily so they can be reused without performing another full DNS lookup.

The amount of time a cached record remains valid is controlled by **TTL (Time To Live).**

For SOC analysts, understanding caching is important because **the absence of a DNS query does not always mean a website wasn't accessed.**

---

# 💡 Real World Analogy

Imagine visiting your favorite coffee shop every morning.

On the first day, you use Google Maps.

```
Home

↓

Google Maps

↓

Coffee Shop
```

After visiting several times, you remember the route.

You no longer need Google Maps.

DNS caching works the same way.

The first lookup is expensive.

Future lookups reuse the cached information until it expires.

---

# 🌍 Where DNS Is Cached

DNS isn't cached in just one place.

It is cached at multiple layers.

```
Browser

↓

Operating System

↓

Corporate DNS Server

↓

ISP DNS Resolver

↓

Recursive Resolver
```

Each layer can answer a DNS request if it already has a valid cached record.

---

# 🥇 Browser Cache

Modern browsers maintain their own DNS cache.

Example:

```
Chrome

↓

github.com

↓

140.82.121.4
```

When you revisit GitHub a few minutes later, Chrome may use its own cache instead of querying the operating system.

---

### SOC Perspective

A browser may connect to a website **without generating new DNS traffic**.

Always correlate:

- Browser History
- Browser Cache
- Proxy Logs
- EDR Telemetry

Don't rely solely on DNS logs.

---

# 🥈 Operating System Cache

If the browser doesn't know the answer,

the operating system checks its DNS cache.

Windows Example:

```powershell
ipconfig /displaydns
```

Example Output

```
Record Name:

github.com

↓

Record Type:

A

↓

Time To Live:

245 Seconds

↓

IP Address:

140.82.121.4
```

---

### Flush DNS Cache

```powershell
ipconfig /flushdns
```

---

### 🔴 Red Team Perspective

Some malware intentionally flushes the DNS cache to:

- Remove traces of previous lookups.
- Force new DNS resolutions.
- Redirect victims after modifying DNS settings.
- Ensure malicious infrastructure is resolved immediately.

---

### 🔵 Blue Team Perspective

Unexpected execution of:

```powershell
ipconfig /flushdns
```

may deserve investigation when correlated with:

- PowerShell
- Command Prompt
- Malware Execution
- Persistence Mechanisms

By itself it is not malicious, but in context it can be significant.

---

# 🥉 Recursive Resolver Cache

Corporate DNS servers also cache responses.

Example:

```
Employee 1

↓

github.com

↓

Corporate DNS

↓

Authoritative Server
```

A few seconds later:

```
Employee 2

↓

github.com

↓

Corporate DNS

↓

Cached Response
```

Only one external DNS lookup was required.

This reduces bandwidth and improves performance across the organization.

---

# ⏳ What is TTL (Time To Live)?

TTL determines **how long a DNS record may remain cached** before a fresh lookup is required.

Example:

```
TTL

300 Seconds
```

This means:

```
DNS Query

↓

Cached

↓

Valid

↓

5 Minutes

↓

Expires

↓

New DNS Lookup Required
```

---

# 🌍 Example

```
github.com

↓

A Record

↓

140.82.121.4

↓

TTL

600 Seconds
```

For the next 10 minutes,

all cached responses remain valid.

After 10 minutes,

the resolver must ask the authoritative server again.

---

# Why TTL Matters

A very **short TTL** means:

- More DNS traffic.
- Faster updates.
- Better failover.

A very **long TTL** means:

- Less DNS traffic.
- Faster browsing.
- Slower propagation of DNS changes.

Organizations choose TTL values based on operational needs.

---

# 🏢 Real Enterprise Example

A company migrates:

```
portal.company.com
```

from one server to another.

Old IP:

```
192.168.1.15
```

New IP:

```
192.168.1.25
```

If the TTL is:

```
24 Hours
```

many users may continue connecting to the old server until their cached record expires.

---

# 🔴 Red Team Perspective

Attackers sometimes exploit TTL and caching by:

- Redirecting victims through DNS poisoning.
- Maintaining access to compromised infrastructure.
- Exploiting stale cached records.
- Leveraging Fast Flux infrastructure with rapidly changing IP addresses.

Although TTL itself is not malicious, it can influence the effectiveness of these techniques.

---

# 🔵 Blue Team Perspective

During investigations, remember:

A missing DNS query **does not prove** that no communication occurred.

Possible explanations include:

- Browser Cache
- Operating System Cache
- Recursive Resolver Cache
- Cached Proxy
- CDN Cache

Always correlate multiple telemetry sources before drawing conclusions.

---

# 💼 Real SOC Investigation

## Alert

```
Firewall

↓

HTTPS Connection

↓

github.com

↓

No DNS Query Observed
```

An inexperienced analyst concludes:

```
"No DNS lookup occurred."

Therefore,

"The user never visited GitHub."
```

Wrong.

Further investigation reveals:

Windows DNS Cache

```
github.com

↓

TTL Remaining

480 Seconds
```

The endpoint simply reused its cached DNS record.

The connection was completely legitimate.

---

# 🚨 Detection Opportunities

Investigate:

- Frequent DNS cache flushes.
- DNS cache poisoning indicators.
- Very short TTL values associated with suspicious infrastructure.
- Domains rapidly changing IP addresses.
- DNS requests immediately after cache clearing.
- Endpoint cache inconsistencies.
- Unexpected resolver behavior.

---

# ❌ Common Mistakes

### Mistake 1

"No DNS query means no website visit."

False.

Caching may have answered the request.

---

### Mistake 2

"TTL is a security feature."

False.

TTL is a performance mechanism.

It controls cache lifetime, not security.

---

### Mistake 3

"Every endpoint performs identical DNS lookups."

False.

Different endpoints may have:

- Different caches.
- Different TTL values.
- Different resolvers.
- Different browsing histories.

---

# 🎯 MITRE ATT&CK Mapping

TTL and caching are normal protocol behaviors and are **not MITRE ATT&CK techniques by themselves**.

However, they may appear during investigations involving:

| Observation | Related Technique |
|-------------|-------------------|
| DNS Poisoning | May support credential theft or redirection (context dependent) |
| Fast Flux Infrastructure | T1568 – Dynamic Resolution (context dependent) |
| DNS-Based C2 | T1071.004 – Application Layer Protocol: DNS |
| DGA Domains | T1568.002 – Domain Generation Algorithms |

Always map the **attacker's behavior**, not the protocol feature itself.

---

# 🧪 SOC Investigation Challenge

## Alert

```
Endpoint

↓

Chrome Browser

↓

HTTPS Connection

↓

portal.company.com

↓

No DNS Query

↓

Connection Successful
```

### Investigation Questions

1. Was the DNS record cached?
2. What is the remaining TTL?
3. Which process initiated the connection?
4. Was the cache recently flushed?
5. Which resolver originally answered the query?
6. Did another endpoint perform the initial lookup?
7. Are proxy logs available?
8. Does browser history confirm access?
9. Is EDR telemetry consistent?
10. Would you classify this as suspicious?

---

# ⚡ Quick Revision

✅ DNS caching improves Internet performance.

✅ DNS records may be cached by the browser, operating system, and recursive resolvers.

✅ TTL controls how long cached DNS records remain valid.

✅ The absence of a DNS query does **not** necessarily mean the absence of network activity.

✅ SOC analysts should correlate DNS caches, endpoint telemetry, proxy logs, and network traffic before making conclusions.


# 🔗 Knowledge Connections


DNS Caching
      │
      ├── Browser Cache
      ├── OS DNS Cache
      ├── Recursive Resolver
      ├── TTL
      ├── HTTP / HTTPS
      ├── Proxy Logs
      ├── CDN
      ├── Fast Flux
      ├── DNS Poisoning
      ├── Threat Hunting
      ├── Digital Forensics
      └── Incident Response
> **Next Section:** We'll explore **Recursive vs Authoritative DNS Servers**, followed by **DNSSEC, DNS over HTTPS (DoH), DNS over TLS (DoT), and DNS Zone Transfers (AXFR)**—topics that every SOC analyst should understand when investigating enterprise and cloud environments.

---

# 🌍 Recursive vs Authoritative DNS Servers - Who Finds the Answer and Who Owns It?

> **"A Recursive Resolver searches for the answer. An Authoritative DNS Server owns the answer."**

---

# 🎯 Purpose

One of the biggest points of confusion in networking is the difference between **Recursive DNS Servers** and **Authoritative DNS Servers**.

Although both answer DNS queries, they have **completely different responsibilities**.

Understanding the difference is essential for:

- SOC Analysts
- Threat Hunters
- DFIR Investigators
- Network Engineers
- Cloud Security Engineers

Many DNS investigations begin by determining **which DNS server responded** and **whether the response was trustworthy**.

---

# 💡 Real World Analogy

Imagine asking someone:

> **"Where is the nearest hospital?"**

### Option 1

You ask a local resident.

They don't know immediately.

They call several people,

find the answer,

and tell you.

This person behaves like a **Recursive Resolver**.

---

### Option 2

You ask the hospital receptionist directly.

They already know the answer because **they work there**.

This is the **Authoritative DNS Server**.

---

# 🏗️ High-Level DNS Flow

```
Your Browser

        │
        ▼
Recursive DNS Resolver
        │
        ▼
Root DNS Server
        │
        ▼
Top Level Domain (.com)
        │
        ▼
Authoritative DNS Server
        │
        ▼
IP Address Returned
        │
        ▼
Browser Connects
```

The Recursive Resolver performs the work.

The Authoritative Server owns the final answer.

---

# 🔎 What is a Recursive DNS Resolver?

A Recursive Resolver acts like a middleman.

Its job is to find the answer **on behalf of the client**.

It does **not** permanently own DNS records.

Instead, it asks other DNS servers until it discovers the correct answer.

Examples include:

- Corporate DNS Servers
- ISP DNS Servers
- Google DNS (8.8.8.8)
- Cloudflare DNS (1.1.1.1)
- Quad9 (9.9.9.9)

---

# 📖 Recursive Resolution Example

Alice opens:

```
github.com
```

The resolver checks:

```
Local Cache

↓

Not Found

↓

Root DNS

↓

.com DNS

↓

GitHub Authoritative DNS

↓

140.82.121.4

↓

Cache Result

↓

Return to Alice
```

The next employee requesting GitHub receives the cached answer instantly.

---

# 💾 Why Recursive Resolvers Cache

Without caching,

every employee visiting GitHub would generate:

```
Root Query

↓

TLD Query

↓

Authoritative Query
```

This would create enormous Internet traffic.

Instead:

```
Employee 1

↓

Internet Lookup

↓

Cache

↓

Employee 2

↓

Cached Response

↓

Employee 3

↓

Cached Response
```

Caching significantly improves performance.

---

# 🏛️ What is an Authoritative DNS Server?

The Authoritative Server owns the official DNS records.

It doesn't search elsewhere.

It simply responds with the correct information.

Example:

```
github.com

↓

Authoritative DNS

↓

140.82.121.4
```

This is considered the official source of truth.

---

# 📦 Example DNS Zone

```
company.com
        │
        ├── A Record
        ├── AAAA Record
        ├── MX Record
        ├── TXT Record
        ├── CNAME
        └── SRV
```

These records are stored on the Authoritative DNS Server.

Whenever a Recursive Resolver needs information,

it eventually contacts this server.

---

# ⚖️ Recursive vs Authoritative

| Recursive Resolver | Authoritative Server |
|--------------------|----------------------|
| Finds the answer | Owns the answer |
| Performs DNS lookups | Stores official DNS records |
| Uses caching | Does not recursively search |
| Usually operated by ISPs or enterprises | Managed by the domain owner or DNS provider |
| May answer from cache | Always answers with authoritative data for its zones |

---

# 🏢 Enterprise Example

An employee types:

```
portal.company.com
```

The workstation sends a request to:

```
Corporate DNS Server
```

If the record is not cached:

```
Corporate DNS

↓

Root

↓

.com

↓

Authoritative Server

↓

Return IP

↓

Cache

↓

Employee
```

The next employee receives the cached result.

---

# 🔴 Red Team Perspective

Attackers interact with recursive and authoritative DNS servers during reconnaissance.

Common techniques include:

- DNS Enumeration
- Zone Walking
- Subdomain Discovery
- DNS Reconnaissance
- Passive DNS Collection
- Active DNS Queries
- DNS Zone Transfer Attempts (AXFR)
- DNS Hijacking
- Cache Poisoning

The objective is often to map the target's infrastructure before exploitation.

---

# 🦠 Malware Spotlight

Many malware families rely on recursive resolvers to locate their Command & Control (C2) servers.

Typical flow:

```
Malware Starts

↓

DNS Query

↓

Recursive Resolver

↓

Authoritative Server

↓

Malicious IP Returned

↓

HTTPS Connection

↓

C2 Communication
```

Examples include:

- Cobalt Strike
- Emotet
- QakBot
- IcedID
- Bumblebee
- AsyncRAT

---

# 🔵 Blue Team Perspective

Monitor for:

- Excessive DNS queries.
- Requests to suspicious recursive resolvers.
- Unauthorized public DNS usage (8.8.8.8, 1.1.1.1) where corporate policy requires internal DNS.
- DNS requests bypassing corporate infrastructure.
- Unexpected authoritative server changes.
- DNS traffic leaving the network directly instead of using approved resolvers.

---

# 🛡️ Detection Engineering

Investigate:

- Endpoints communicating directly with external DNS servers.
- DNS over non-standard ports.
- Public DNS usage from sensitive servers.
- Frequent cache misses for the same domain.
- Sudden changes in authoritative name servers.
- Recursive resolvers returning unexpected IP addresses.
- DNS requests immediately followed by suspicious outbound connections.

---

# 💼 Real SOC Investigation

## Alert

Firewall logs show:

```
FINANCE-PC-07

↓

DNS Query

↓

8.8.8.8

↓

Port 53
```

Corporate policy states:

```
All DNS

↓

Internal DNS Server Only
```

Further investigation reveals:

```
powershell.exe

↓

nslookup

↓

malicious-domain.xyz

↓

Google Public DNS
```

Moments later:

```
HTTPS Connection

↓

185.xxx.xxx.xxx

↓

Malware Download
```

### Investigation

The attacker intentionally bypassed the corporate DNS resolver to avoid internal DNS logging and filtering.

Correlating firewall logs, DNS telemetry, PowerShell logs, and Sysmon quickly revealed the attack.

---

# 🚨 Detection Opportunities

Investigate:

- Direct queries to public DNS resolvers.
- Internal servers using external DNS.
- Recursive resolvers responding with unexpected IP addresses.
- Authoritative server changes.
- DNS requests followed immediately by malware downloads.
- Unusual DNS volume from a single endpoint.
- DNS traffic outside business hours.

---

# ❌ Common Mistakes

### Mistake 1

"Recursive DNS Servers store every DNS record."

False.

They temporarily **cache** records.

The official records remain on the Authoritative Server.

---

### Mistake 2

"Authoritative DNS Servers search the Internet."

False.

They already own the records for their domains.

They do not perform recursive lookups.

---

### Mistake 3

"Using Google DNS is always malicious."

False.

Many home users legitimately use:

- Google DNS
- Cloudflare
- Quad9

However, in enterprise environments where internal DNS is required, unauthorized public DNS usage may be suspicious.

Always investigate in context.

---

# 🎯 MITRE ATT&CK Mapping

| Activity | Technique |
|-----------|-----------|
| DNS-Based C2 | T1071.004 – Application Layer Protocol: DNS |
| Dynamic Resolution | T1568 |
| Domain Generation Algorithms (DGA) | T1568.002 |
| DNS Reconnaissance | T1590 (Reconnaissance - Gather Victim Network Information, context dependent) |
| DNS Zone Transfer Attempts | T1590 (Context dependent) |

> **Note:** Normal recursive and authoritative DNS operations are legitimate protocol behavior. MITRE mapping applies only when these mechanisms are abused by attackers.

---

# 🧪 SOC Investigation Challenge

## Alert

```
Endpoint

↓

DEV-LAPTOP-04

↓

DNS Queries

↓

8.8.8.8

↓

1.1.1.1

↓

9.9.9.9

↓

Corporate DNS Not Used

↓

PowerShell

↓

HTTPS Connection

↓

Executable Download
```

### Investigation Questions

1. Why did the endpoint bypass the corporate DNS resolver?
2. Which process initiated the DNS requests?
3. Were the queried domains newly registered?
4. Did the recursive resolver return malicious infrastructure?
5. Was DNS filtering bypassed?
6. Which firewall logs should be reviewed?
7. Was PowerShell involved?
8. Were additional endpoints affected?
9. Should the endpoint be isolated?
10. Which MITRE ATT&CK techniques apply?

---

# ⚡ Quick Revision

✅ A **Recursive Resolver** searches for DNS answers and caches the results.

✅ An **Authoritative DNS Server** owns the official DNS records for a domain.

✅ Recursive resolvers improve performance through caching.

✅ Authoritative servers provide the source of truth for DNS information.

✅ SOC analysts should monitor for unauthorized DNS resolvers, suspicious DNS patterns, and resolver bypass attempts.



# 🔗 Knowledge Connections

Recursive DNS
      │
      ├── DNS Caching
      ├── TTL
      ├── Root Servers
      ├── TLD Servers
      ├── Authoritative DNS
      ├── DNSSEC
      ├── DoH
      ├── DoT
      ├── Firewall Logs
      ├── Proxy Logs
      ├── Threat Hunting
      ├── Malware C2
      ├── DNS Tunneling
      └── Incident Response
> **Next Section:** We'll cover **DNSSEC (DNS Security Extensions)**—how it protects DNS integrity, why it was created, how attackers attempt to bypass it, and what SOC analysts should look for during DNS security investigations.

---

# 🔐 DNSSEC (DNS Security Extensions) - Protecting DNS from Manipulation

> **"Traditional DNS tells you an answer. DNSSEC helps you verify that the answer hasn't been altered."**

---

# 🎯 Purpose

The original DNS protocol was designed in the early days of the Internet.

At that time, the Internet was built on **trust**, not security.

DNS could answer questions like:

```
What is the IP address of github.com?
```

But there was one major problem.

**DNS had no built-in way to verify whether the response was genuine.**

If an attacker intercepted or forged the DNS response, your computer would trust it without question.

DNSSEC (DNS Security Extensions) was created to solve this problem.

It adds **cryptographic verification** to DNS responses, allowing resolvers to confirm that the information came from the legitimate domain owner and was not modified during transit.

> **Important:** DNSSEC protects the **integrity and authenticity** of DNS data. It does **not** encrypt DNS traffic.

---

# 🤔 Why Was DNSSEC Created?

Imagine typing:

```
https://bank.com
```

Without DNSSEC, your computer asks:

```
What is the IP address of bank.com?
```

The legitimate DNS server replies:

```
192.168.10.20
```

But an attacker on the network quickly sends a fake reply:

```
203.0.113.50
```

Your computer cannot distinguish between the legitimate and forged responses.

It simply accepts whichever response it trusts first.

The result:

```
Victim

↓

Fake Banking Website

↓

Username Entered

↓

Password Entered

↓

Credentials Stolen
```

DNSSEC helps prevent this type of attack by allowing the resolver to verify the authenticity of the DNS response.

---

# 💡 Real World Analogy

Imagine receiving an important legal document.

Anyone can print a document.

But how do you know it is genuine?

You look for:

- Official Signature
- Government Seal
- Notary Stamp

These prove that the document has not been altered.

DNSSEC works in a similar way.

Instead of a stamp,

it uses **digital signatures**.

---

# 🌍 How DNSSEC Works

Normal DNS

```
Client

↓

DNS Query

↓

DNS Response

↓

Client Trusts It
```

DNSSEC

```
Client

↓

DNS Query

↓

DNS Response

↓

Digital Signature

↓

Signature Verification

↓

Trusted Response
```

If the signature fails verification,

the response is rejected.

---

# 🔑 Core Components

DNSSEC introduces several new DNS record types.

| Record | Purpose |
|----------|----------|
| DNSKEY | Stores the public key used to verify signatures |
| RRSIG | Digital signature protecting DNS records |
| DS | Links the child zone to the parent zone |
| NSEC / NSEC3 | Proves that a requested record does not exist |

These records work together to create a **Chain of Trust**.

---

# 🔗 The Chain of Trust

One of DNSSEC's most important concepts is the **Chain of Trust**.

Think of it like a hierarchy.

```
Root Zone

↓

.com

↓

github.com

↓

www.github.com
```

Each level cryptographically verifies the next.

If one link in the chain cannot be trusted,

verification fails.

---

# 🏢 Enterprise Example

An employee opens:

```
vpn.company.com
```

The DNS resolver receives:

```
IP Address

+

Digital Signature
```

The resolver verifies:

- Is the signature valid?
- Was the response modified?
- Does the chain of trust remain intact?

If all checks succeed,

the connection proceeds.

Otherwise,

the DNS response is rejected.

---

# 🔴 Red Team Perspective

DNSSEC makes several attacks more difficult, including:

- DNS Cache Poisoning
- DNS Spoofing
- Forged DNS Responses
- Man-in-the-Middle DNS Manipulation
- Rogue DNS Servers

However, DNSSEC **does not** stop attackers from:

- Registering malicious domains.
- Hosting phishing websites.
- Using newly registered domains.
- Operating Command & Control servers.
- Using compromised legitimate domains.

DNSSEC validates **ownership**, not **intent**.

A malicious domain can still be correctly signed.

---

# 🦠 Malware Spotlight

Modern malware generally avoids attacking DNSSEC directly.

Instead, attackers often:

```
Register

↓

evil-login-support.com

↓

Configure DNSSEC Correctly

↓

Host Phishing Website

↓

Victim Visits

↓

Credentials Stolen
```

The DNS response is legitimate.

The website itself is malicious.

This demonstrates an important lesson:

> DNSSEC verifies authenticity—not safety.

---

# 🔵 Blue Team Perspective

SOC analysts should understand that:

A successful DNSSEC validation **does not** mean a domain is trustworthy.

Always investigate:

- Domain reputation
- WHOIS information
- Domain age
- Threat Intelligence
- Certificate information
- User behavior
- Download activity

DNSSEC is only one piece of the investigation.

---

# 🛡️ Detection Engineering

Monitor for:

- DNSSEC validation failures.
- Misconfigured DNSSEC zones.
- Unexpected DNSKEY changes.
- Missing DS records.
- Resolver validation errors.
- Repeated DNSSEC verification failures.
- Clients switching to alternative DNS servers after validation failures.

While uncommon, repeated validation failures may indicate configuration issues or attempted manipulation.

---

# 💼 Real SOC Investigation

## Alert

Employees report:

```
Cannot Access

vpn.company.com
```

DNS Logs

```
DNSSEC Validation Failed
```

Network Team investigates.

The DNS records were recently updated.

However,

the DS record at the parent zone was never updated.

Result:

```
Signature Verification Failed

↓

Resolver Rejects Response

↓

Website Unreachable
```

No cyber attack occurred.

This was an operational misconfiguration.

---

# 🚨 Detection Opportunities

Investigate:

- DNSSEC validation failures.
- Expired DNSSEC keys.
- Incorrect DNSKEY records.
- Chain of Trust failures.
- Parent-child DS mismatches.
- Resolver configuration errors.
- Unexpected disabling of DNSSEC validation.

---

# ❌ Common Mistakes

### Mistake 1

"DNSSEC encrypts DNS."

False.

DNSSEC **does not encrypt** DNS traffic.

It only verifies authenticity and integrity.

Encryption is provided by technologies such as:

- DNS over HTTPS (DoH)
- DNS over TLS (DoT)

---

### Mistake 2

"DNSSEC prevents phishing."

False.

Attackers can register:

```
micr0soft-login.com
```

configure DNSSEC correctly,

and still host a phishing website.

DNSSEC confirms ownership of the DNS records—not whether the website is trustworthy.

---

### Mistake 3

"If DNSSEC succeeds, the website is safe."

False.

Always investigate:

- Reputation
- Certificates
- Threat Intelligence
- User activity
- Downloads
- Network behavior

A correctly signed malicious domain is still malicious.

---

# 🎯 MITRE ATT&CK Mapping

DNSSEC itself is a defensive technology and does **not** map directly to a MITRE ATT&CK technique.

However, it helps mitigate attacks involving:

| Threat | Related MITRE Technique |
|----------|-------------------------|
| DNS Spoofing | T1557 - Adversary-in-the-Middle (context dependent) |
| DNS-Based C2 | T1071.004 |
| DNS Manipulation | Context dependent |
| DNS Cache Poisoning | Context dependent |

MITRE maps attacker behavior, not defensive protocols.

---

# 🧪 SOC Investigation Challenge

## Alert

```
Internal DNS Resolver

↓

DNSSEC Validation Failed

↓

vpn.company.com

↓

Employees Unable To Connect
```

### Investigation Questions

1. Is this an attack or a configuration issue?
2. Were DNS records recently modified?
3. Is the DNSKEY valid?
4. Does the DS record match?
5. Has the certificate changed?
6. Are all users affected?
7. Which resolver reported the failure?
8. Is there evidence of DNS spoofing?
9. Should the domain owner be contacted?
10. What logs would help confirm the root cause?

---

# ⚡ Quick Revision

✅ DNSSEC protects DNS **integrity** and **authenticity**.

✅ DNSSEC uses digital signatures to verify DNS responses.

✅ DNSSEC does **not** encrypt DNS traffic.

✅ DNSSEC helps prevent forged DNS responses and cache poisoning.

✅ A DNSSEC-protected domain can still host malicious content.


# 🔗 Knowledge Connections

DNSSEC
    │
    ├── DNS
    ├── DNS Records
    ├── Recursive Resolver
    ├── Authoritative DNS
    ├── Digital Signatures
    ├── PKI
    ├── Certificates
    ├── TLS
    ├── MITM
    ├── DNS Spoofing
    ├── Cache Poisoning
    ├── Threat Intelligence
    └── Incident Response
> **Next Section:** We'll explore **DNS over HTTPS (DoH) and DNS over TLS (DoT)**—how they encrypt DNS traffic, why privacy-focused organizations use them, how attackers abuse them to evade monitoring, and how SOC analysts can detect and investigate encrypted DNS traffic.

---

# 🔒 DNS over HTTPS (DoH) & DNS over TLS (DoT) - Encrypted DNS

> **"Traditional DNS protects almost nothing. Anyone on the network can see your DNS queries. DoH and DoT were created to solve that problem by encrypting DNS traffic."**

---

# 🎯 Purpose

For decades, DNS queries were sent **in plaintext**.

That meant anyone between you and the DNS server could potentially see:

- Every website you visit
- Every domain your applications resolve
- Malware Command & Control lookups
- Internal DNS requests
- Cloud services you access

Even if the website itself used HTTPS, the **DNS lookup** often remained visible.

Example:

```
User

↓

DNS Query

github.com

↓

Plaintext

↓

Anyone Monitoring The Network Can See It
```

This exposed a significant privacy issue.

To solve this, two technologies were introduced:

- **DNS over HTTPS (DoH)**
- **DNS over TLS (DoT)**

Both encrypt DNS traffic—but they work differently.

---

# 🤔 Why Was Encryption Needed?

Imagine you're connected to public Wi-Fi at an airport.

You browse:

```
https://bank.com
```

The website uses HTTPS.

Your login credentials are encrypted.

However...

Before opening the website, your device performs:

```
DNS Query

↓

bank.com
```

If that DNS query is unencrypted,

someone monitoring the Wi-Fi network can still learn:

- Which websites you're visiting.
- Which services you're using.
- Which cloud providers you access.

While they cannot see the HTTPS content, they can often infer a great deal from the DNS queries alone.

---

# 💡 Real World Analogy

Imagine sending a postcard.

Everyone handling it can read the message.

That's traditional DNS.

```
DNS Query

↓

Visible To Everyone
```

Now imagine putting the same message inside a sealed envelope.

Only the intended recipient can read it.

That's encrypted DNS.

---

# 🌍 Traditional DNS

```
Client

↓

DNS Query

↓

Port 53

↓

Plaintext

↓

DNS Server
```

Anyone monitoring the network may observe the domain being queried.

---

# 🔐 DNS over HTTPS (DoH)

With DoH:

```
Client

↓

HTTPS (Port 443)

↓

Encrypted

↓

DNS Provider
```

The DNS query is hidden inside normal HTTPS traffic.

To a firewall,

it often appears similar to ordinary web browsing.

---

# 🔐 DNS over TLS (DoT)

With DoT:

```
Client

↓

TLS

↓

Port 853

↓

Encrypted

↓

DNS Provider
```

Unlike DoH,

DoT uses a dedicated port specifically for encrypted DNS.

---

# ⚖️ DoH vs DoT

| DNS over HTTPS (DoH) | DNS over TLS (DoT) |
|------------------------|--------------------|
| Uses HTTPS | Uses TLS |
| Port 443 | Port 853 |
| Blends with web traffic | Separate encrypted DNS traffic |
| Harder to distinguish | Easier to identify |
| Popular in browsers | Popular in operating systems and network devices |

Both protect DNS privacy.

---

# 🏢 Real Enterprise Example

A company requires:

```
All DNS

↓

Corporate DNS Server
```

An employee installs a browser that enables DoH.

Instead of:

```
Laptop

↓

Corporate DNS
```

The traffic becomes:

```
Laptop

↓

HTTPS

↓

Cloudflare DoH

↓

Encrypted
```

The organization's DNS monitoring may lose visibility into those queries.

---

# 🔴 Red Team Perspective

Attackers increasingly abuse DoH because it helps hide DNS activity from traditional monitoring.

Example attack flow:

```
Malware Starts

↓

DoH Request

↓

Encrypted DNS Query

↓

Malicious Domain Resolved

↓

HTTPS Connection

↓

C2 Communication
```

Since the DNS request is encrypted,

network monitoring tools may not easily identify the queried domain.

---

# 🦠 Malware Spotlight

Several malware families have incorporated DoH support or leveraged encrypted DNS techniques, including:

- Cobalt Strike (via configurable infrastructure)
- Sliver C2
- Brute Ratel (operator configurable)
- Some AsyncRAT variants
- Custom malware developed by advanced threat actors

The objective is to reduce visibility into DNS communications.

> **Note:** Support for DoH often depends on the malware configuration rather than being enabled by default.

---

# 🔵 Blue Team Perspective

Encrypted DNS improves user privacy,

but it also creates new monitoring challenges.

SOC analysts should investigate:

- Unauthorized DoH providers.
- Browsers bypassing corporate DNS.
- Endpoints resolving domains through public encrypted resolvers.
- Unexpected outbound HTTPS connections to known DoH services.
- DNS traffic leaving the organization outside approved channels.

Organizations often define approved DNS policies to balance privacy and security.

---

# 🛡️ Common Public DoH Providers

Examples include:

- Cloudflare
- Google Public DNS
- Quad9
- NextDNS
- Cisco OpenDNS
- AdGuard DNS

If your organization requires all DNS to pass through internal infrastructure, connections to these services may warrant review.

---

# 💼 Real SOC Investigation

## Alert

Firewall logs show:

```
Developer-Laptop

↓

HTTPS

↓

1.1.1.1

↓

Port 443
```

No HTTP web browsing activity is associated with the connection.

Further investigation reveals:

```
Firefox

↓

DNS over HTTPS Enabled
```

DNS queries were being sent directly to Cloudflare instead of the corporate DNS servers.

---

### Investigation

The analyst confirms:

- No malware.
- Legitimate browser configuration.
- Corporate DNS visibility bypassed.

The issue is operational rather than malicious.

Security policy is updated to manage approved encrypted DNS usage.

---

# 🚨 Detection Opportunities

Investigate:

- Connections to known DoH providers.
- DNS traffic over TCP/443 without expected web activity.
- Traffic to TCP/853.
- Browsers using external encrypted resolvers.
- Malware initiating DoH requests.
- Endpoints bypassing internal DNS infrastructure.
- Sudden changes in DNS resolver behavior.

---

# 🏢 Enterprise Detection Methods

Organizations commonly detect unauthorized DoH by:

- Blocking known DoH providers.
- Restricting outbound DNS traffic.
- Monitoring TLS destinations.
- Using DNS-aware firewalls.
- Enforcing browser policies.
- Monitoring EDR telemetry for resolver changes.
- Inspecting proxy logs.

Modern EDR solutions can often identify the process responsible for encrypted DNS traffic.

---

# 🔍 SOC Investigation Example

Alert Timeline:

```
09:18

powershell.exe Starts

↓

09:18

HTTPS Connection

↓

mozilla.cloudflare-dns.com

↓

09:18

Encrypted DNS Query

↓

09:19

Resolved

update-check-service.xyz

↓

09:19

HTTPS Download

↓

Malware Executed
```

Although the DNS query itself was encrypted,

the SOC correlated:

- EDR process telemetry.
- Firewall logs.
- Proxy logs.
- Network connections.
- File creation events.

This reconstructed the complete attack chain.

---

# ❌ Common Mistakes

### Mistake 1

"DoH is malicious."

False.

DoH is a legitimate privacy technology used by many browsers and operating systems.

---

### Mistake 2

"Encrypted DNS prevents SOC investigations."

False.

Although DNS contents may be hidden,

SOC analysts still have many data sources:

- EDR
- Firewall
- Proxy
- TLS Metadata
- Process Creation
- Network Connections
- Threat Intelligence

Detection shifts from pure DNS inspection to telemetry correlation.

---

### Mistake 3

"DoH and HTTPS are the same."

Not exactly.

HTTPS secures web traffic.

DoH uses HTTPS **specifically to transport DNS queries**.

---

# 🎯 MITRE ATT&CK Mapping

Encrypted DNS itself is legitimate.

However, attackers may abuse it during:

| Activity | Technique |
|-----------|-----------|
| DNS-Based Command & Control | T1071.004 - Application Layer Protocol: DNS |
| Encrypted C2 Channels | T1573 - Encrypted Channel |
| Dynamic Resolution | T1568 |
| Malware Using DoH | Context dependent |

Always map the attacker's behavior—not the technology.

---

# 🧪 SOC Investigation Challenge

## Alert

```
Endpoint

↓

Chrome Browser

↓

HTTPS Connection

↓

dns.google

↓

Port 443

↓

Corporate DNS Never Used

↓

Five Minutes Later

↓

Unknown Executable Downloaded
```

### Investigation Questions

1. Is DoH enabled?
2. Which process initiated the encrypted DNS traffic?
3. Was corporate DNS bypassed?
4. Which domains were likely resolved?
5. Was malware involved?
6. Are browser policies enforcing approved DNS settings?
7. Are other endpoints showing similar behavior?
8. What additional telemetry would you review?
9. Would you block the resolver or investigate first?
10. Which MITRE ATT&CK techniques may apply?

---

# ⚡ Quick Revision

✅ Traditional DNS uses **Port 53** and is typically unencrypted.

✅ DNS over HTTPS (DoH) encrypts DNS using **HTTPS on Port 443**.

✅ DNS over TLS (DoT) encrypts DNS using **TLS on Port 853**.

✅ Encrypted DNS improves privacy but can reduce visibility for traditional network monitoring.

✅ SOC analysts should correlate firewall, EDR, proxy, TLS metadata, and endpoint telemetry to investigate encrypted DNS activity.


# 🔗 Knowledge Connections


Encrypted DNS
       │
       ├── DNS
       ├── DNSSEC
       ├── HTTPS
       ├── TLS
       ├── Port 443
       ├── Port 853
       ├── Firewall Logs
       ├── Proxy Logs
       ├── EDR
       ├── Threat Hunting
       ├── Malware C2
       ├── Privacy
       └── Incident Response

> **Next Section:** We'll explore **DNS Zone Transfers (AXFR)**—one of the most valuable reconnaissance techniques for attackers and a high-impact topic for SOC analysts. You'll learn how misconfigured DNS servers can expose an organization's entire DNS infrastructure and how to detect and prevent it.

---

# 🌐 DNS Zone Transfers (AXFR) - When One Misconfiguration Exposes an Entire Organization

> **"One successful Zone Transfer can reveal more information than thousands of DNS queries."**

---

# 🎯 Purpose

Imagine trying to map an organization's infrastructure.

Normally, you would have to discover domains **one by one**.

For example:

```
www.company.com

mail.company.com

vpn.company.com

portal.company.com

api.company.com
```

Each discovery requires another DNS lookup.

Now imagine asking the DNS server:

> **"Instead of giving me one record... give me everything."**

If the DNS server allows it,

you receive the organization's **entire DNS database.**

This is called a **DNS Zone Transfer**.

---

# 🤔 What is a DNS Zone?

Earlier, we learned that a DNS Zone stores all DNS records for a domain.

Example:

```
company.com
│
├── A Records
├── AAAA Records
├── MX Records
├── TXT Records
├── CNAME Records
├── SRV Records
├── NS Records
└── PTR Records
```

Normally,

only the organization's DNS servers should access the full zone.

---

# 💡 Why Do Zone Transfers Exist?

Zone Transfers were never designed for attackers.

They were created for **DNS replication**.

Imagine a company has two DNS servers.

```
Primary DNS

↓

Secondary DNS
```

When an administrator updates:

```
portal.company.com
```

the secondary server must also receive the update.

Instead of manually copying every record,

the DNS servers synchronize automatically.

This synchronization is called a **Zone Transfer**.

---

# 🌍 Primary and Secondary DNS

```
Administrator

↓

Primary DNS Server

↓

AXFR

↓

Secondary DNS Server
```

Both servers now contain identical DNS records.

This improves:

- Availability
- Redundancy
- Fault Tolerance

If one DNS server fails,

the other continues answering queries.

---

# 🔄 Types of Zone Transfers

There are two common types.

---

## 1️⃣ AXFR (Full Zone Transfer)

AXFR copies the **entire DNS zone**.

Example:

```
company.com

↓

Every DNS Record

↓

Secondary DNS Server
```

This includes:

- A Records
- AAAA Records
- MX Records
- TXT Records
- CNAME Records
- SRV Records
- NS Records
- SOA Records

Everything.

---

## 2️⃣ IXFR (Incremental Zone Transfer)

Instead of copying everything,

IXFR transfers **only the changes**.

Example:

Administrator adds:

```
vpn.company.com
```

Instead of transferring 5,000 records,

only the new record is replicated.

Much faster.

Much more efficient.

---

# 🏢 Real Enterprise Example

Company:

```
company.com
```

Primary DNS:

```
10.10.10.5
```

Secondary DNS:

```
10.10.10.6
```

Administrator creates:

```
jira.company.com
```

The Primary DNS performs:

```
IXFR

↓

Secondary DNS
```

Both servers remain synchronized.

---

# 🔴 Red Team Perspective

Now imagine the DNS server is misconfigured.

Anyone on the Internet can request:

```
AXFR

↓

company.com
```

The server responds:

```
vpn.company.com

mail.company.com

jira.company.com

dc01.company.com

dc02.company.com

exchange.company.com

backup.company.com

citrix.company.com

dev.company.com

api.company.com

finance.company.com
```

The attacker now possesses an almost complete map of the organization's infrastructure.

This dramatically accelerates reconnaissance.

---

# 🎯 Why Attackers Love AXFR

A successful Zone Transfer may reveal:

- Internal server names.
- VPN gateways.
- Domain Controllers.
- Exchange Servers.
- Backup Servers.
- Development Environments.
- API Gateways.
- Database Servers.
- Cloud Services.
- Monitoring Systems.
- Legacy Infrastructure.

Instead of discovering systems individually,

the attacker receives everything at once.

---

# 🛠️ Common Enumeration Tools

Security professionals and attackers alike may test for Zone Transfers using tools such as:

- `dig`
- `host`
- `nslookup` (limited capabilities)
- DNSRecon
- Fierce
- Nmap NSE scripts

> **Important:** Always perform these tests only on systems you are authorized to assess.

---

# 💼 Real SOC Investigation

## Alert

Firewall Logs

```
External IP

↓

TCP Port 53

↓

Large Outbound Response

↓

DNS Server
```

At first glance,

it appears to be ordinary DNS traffic.

However,

the transferred data size is unusually large.

---

### DNS Logs

```
Query Type

AXFR
```

This immediately attracts attention.

Normal users never perform AXFR requests.

---

### Investigation

The analyst discovers:

```
203.0.113.55

↓

Requested

AXFR

↓

company.com
```

The DNS server responded successfully.

Over **4,800 DNS records** were transferred.

The attacker now has detailed knowledge of the organization's infrastructure.

---

# 🦠 Real World Attack Scenario

Imagine a ransomware group preparing an attack.

Step 1

```
Passive OSINT
```

↓

Step 2

```
Subdomain Enumeration
```

↓

Step 3

```
AXFR Request
```

↓

Step 4

```
Receives:

vpn.company.com

exchange.company.com

dc01.company.com

backup.company.com
```

↓

Step 5

```
Targets VPN

↓

Compromises Credentials

↓

Moves Laterally

↓

Deploys Ransomware
```

The Zone Transfer itself caused no damage.

But it provided the attacker with the roadmap.

---

# 🔵 Blue Team Perspective

Most organizations should **never** allow unrestricted Zone Transfers.

Best practices include:

- Allow AXFR only between trusted DNS servers.
- Restrict transfers by IP address.
- Disable AXFR if not required.
- Monitor DNS server logs.
- Alert on external AXFR attempts.
- Periodically audit DNS server configuration.

Even one successful external AXFR can expose years of infrastructure planning.

---

# 🛡️ Detection Engineering

Create alerts for:

- AXFR requests from external IP addresses.
- Large DNS responses over TCP Port 53.
- Unauthorized secondary DNS synchronization.
- Repeated Zone Transfer attempts.
- Unknown IPs querying SOA followed by AXFR.
- Failed AXFR requests (these still indicate reconnaissance).

A failed AXFR attempt is valuable intelligence—it tells you someone is probing your environment.

---

# 🚨 Detection Opportunities

Investigate when you observe:

- Query Type = AXFR
- Query Type = IXFR from unexpected systems
- External hosts contacting internal DNS servers
- High-volume DNS responses
- Multiple reconnaissance queries preceding an AXFR attempt
- DNS scans from cloud-hosted infrastructure

---

# ❌ Common Mistakes

### Mistake 1

"Zone Transfers are always malicious."

False.

Primary and secondary DNS servers legitimately perform Zone Transfers.

Always verify the source system.

---

### Mistake 2

"If the AXFR request failed, nothing happened."

False.

A failed AXFR still indicates reconnaissance.

The attacker has learned:

- A DNS server exists.
- Zone Transfers are restricted.
- The organization may be worth targeting.

---

### Mistake 3

"Only public DNS servers perform Zone Transfers."

False.

Internal Active Directory DNS servers also use Zone Transfers for replication and redundancy.

Context matters.

---

# 🎯 MITRE ATT&CK Mapping

| Activity | Technique |
|-----------|-----------|
| DNS Infrastructure Discovery | T1590 - Gather Victim Network Information |
| Active Reconnaissance | T1595 - Active Scanning |
| Internal Network Discovery | T1016 / T1046 (Context Dependent) |

> DNS Zone Transfers are typically associated with the **Reconnaissance** phase of the Cyber Kill Chain, where attackers gather information before attempting exploitation.

---

# 🧪 SOC Investigation Challenge

## Alert

```
External IP

↓

203.0.113.77

↓

TCP Port 53

↓

AXFR Request

↓

Response Size

18 MB

↓

DNS Server

↓

Successful
```

### Investigation Questions

1. Why was an AXFR request allowed?
2. Which DNS server responded?
3. Was the source IP trusted?
4. How many DNS records were transferred?
5. Was this a legitimate secondary DNS server?
6. Were additional reconnaissance activities observed?
7. Which firewall rules should be reviewed?
8. Should the DNS server configuration be audited?
9. What infrastructure information may now be exposed?
10. What containment actions should be taken?

---

# ⚡ Quick Revision

✅ A DNS Zone contains all DNS records for a domain.

✅ AXFR performs a **full** Zone Transfer.

✅ IXFR performs an **incremental** Zone Transfer.

✅ Zone Transfers are legitimate for DNS replication but dangerous if exposed to unauthorized systems.

✅ Successful AXFR requests can reveal an organization's entire DNS infrastructure.

---

# 🏆 SOC Analyst Tip

One of the easiest wins during a security assessment is checking for exposed Zone Transfers.

From a SOC perspective, **AXFR is a high-signal event**:

- It is rare.
- It is easy to detect.
- It often indicates reconnaissance.
- It can expose critical infrastructure information.

Even a **failed AXFR attempt** deserves investigation because it reveals attacker intent.



# 🔗 Knowledge Connections


DNS Zone Transfers
        │
        ├── DNS
        ├── SOA Records
        ├── NS Records
        ├── AXFR
        ├── IXFR
        ├── Reconnaissance
        ├── OSINT
        ├── Subdomain Enumeration
        ├── Active Directory DNS
        ├── Threat Hunting
        ├── Firewall Logs
        ├── Detection Engineering
        └── Incident Response
> **Next Section:** We'll dive into **DNS Tunneling**—one of the most important DNS abuse techniques used by attackers for **Command & Control (C2)** and **data exfiltration**. We'll cover how it works, real malware examples (Iodine, DNScat2, Cobalt Strike, etc.), Wireshark analysis, Splunk detection, Sigma rules, MITRE ATT&CK mapping, and a complete SOC investigation walkthrough.

---

# 🌐 DNS Zone Transfers (AXFR) - When One Misconfiguration Exposes an Entire Organization

> **"One successful Zone Transfer can reveal more information than thousands of DNS queries."**

---

# 🎯 Purpose

Imagine trying to map an organization's infrastructure.

Normally, you would have to discover domains **one by one**.

For example:

```
www.company.com

mail.company.com

vpn.company.com

portal.company.com

api.company.com
```

Each discovery requires another DNS lookup.

Now imagine asking the DNS server:

> **"Instead of giving me one record... give me everything."**

If the DNS server allows it,

you receive the organization's **entire DNS database.**

This is called a **DNS Zone Transfer**.

---

# 🤔 What is a DNS Zone?

Earlier, we learned that a DNS Zone stores all DNS records for a domain.

Example:

```
company.com
│
├── A Records
├── AAAA Records
├── MX Records
├── TXT Records
├── CNAME Records
├── SRV Records
├── NS Records
└── PTR Records
```

Normally,

only the organization's DNS servers should access the full zone.

---

# 💡 Why Do Zone Transfers Exist?

Zone Transfers were never designed for attackers.

They were created for **DNS replication**.

Imagine a company has two DNS servers.

```
Primary DNS

↓

Secondary DNS
```

When an administrator updates:

```
portal.company.com
```

the secondary server must also receive the update.

Instead of manually copying every record,

the DNS servers synchronize automatically.

This synchronization is called a **Zone Transfer**.

---

# 🌍 Primary and Secondary DNS

```
Administrator

↓

Primary DNS Server

↓

AXFR

↓

Secondary DNS Server
```

Both servers now contain identical DNS records.

This improves:

- Availability
- Redundancy
- Fault Tolerance

If one DNS server fails,

the other continues answering queries.

---

# 🔄 Types of Zone Transfers

There are two common types.

---

## 1️⃣ AXFR (Full Zone Transfer)

AXFR copies the **entire DNS zone**.

Example:

```
company.com

↓

Every DNS Record

↓

Secondary DNS Server
```

This includes:

- A Records
- AAAA Records
- MX Records
- TXT Records
- CNAME Records
- SRV Records
- NS Records
- SOA Records

Everything.

---

## 2️⃣ IXFR (Incremental Zone Transfer)

Instead of copying everything,

IXFR transfers **only the changes**.

Example:

Administrator adds:

```
vpn.company.com
```

Instead of transferring 5,000 records,

only the new record is replicated.

Much faster.

Much more efficient.

---

# 🏢 Real Enterprise Example

Company:

```
company.com
```

Primary DNS:

```
10.10.10.5
```

Secondary DNS:

```
10.10.10.6
```

Administrator creates:

```
jira.company.com
```

The Primary DNS performs:

```
IXFR

↓

Secondary DNS
```

Both servers remain synchronized.

---

# 🔴 Red Team Perspective

Now imagine the DNS server is misconfigured.

Anyone on the Internet can request:

```
AXFR

↓

company.com
```

The server responds:

```
vpn.company.com

mail.company.com

jira.company.com

dc01.company.com

dc02.company.com

exchange.company.com

backup.company.com

citrix.company.com

dev.company.com

api.company.com

finance.company.com
```

The attacker now possesses an almost complete map of the organization's infrastructure.

This dramatically accelerates reconnaissance.

---

# 🎯 Why Attackers Love AXFR

A successful Zone Transfer may reveal:

- Internal server names.
- VPN gateways.
- Domain Controllers.
- Exchange Servers.
- Backup Servers.
- Development Environments.
- API Gateways.
- Database Servers.
- Cloud Services.
- Monitoring Systems.
- Legacy Infrastructure.

Instead of discovering systems individually,

the attacker receives everything at once.

---

# 🛠️ Common Enumeration Tools

Security professionals and attackers alike may test for Zone Transfers using tools such as:

- `dig`
- `host`
- `nslookup` (limited capabilities)
- DNSRecon
- Fierce
- Nmap NSE scripts

> **Important:** Always perform these tests only on systems you are authorized to assess.

---

# 💼 Real SOC Investigation

## Alert

Firewall Logs

```
External IP

↓

TCP Port 53

↓

Large Outbound Response

↓

DNS Server
```

At first glance,

it appears to be ordinary DNS traffic.

However,

the transferred data size is unusually large.

---

### DNS Logs

```
Query Type

AXFR
```

This immediately attracts attention.

Normal users never perform AXFR requests.

---

### Investigation

The analyst discovers:

```
203.0.113.55

↓

Requested

AXFR

↓

company.com
```

The DNS server responded successfully.

Over **4,800 DNS records** were transferred.

The attacker now has detailed knowledge of the organization's infrastructure.

---

# 🦠 Real World Attack Scenario

Imagine a ransomware group preparing an attack.

Step 1

```
Passive OSINT
```

↓

Step 2

```
Subdomain Enumeration
```

↓

Step 3

```
AXFR Request
```

↓

Step 4

```
Receives:

vpn.company.com

exchange.company.com

dc01.company.com

backup.company.com
```

↓

Step 5

```
Targets VPN

↓

Compromises Credentials

↓

Moves Laterally

↓

Deploys Ransomware
```

The Zone Transfer itself caused no damage.

But it provided the attacker with the roadmap.

---

# 🔵 Blue Team Perspective

Most organizations should **never** allow unrestricted Zone Transfers.

Best practices include:

- Allow AXFR only between trusted DNS servers.
- Restrict transfers by IP address.
- Disable AXFR if not required.
- Monitor DNS server logs.
- Alert on external AXFR attempts.
- Periodically audit DNS server configuration.

Even one successful external AXFR can expose years of infrastructure planning.

---

# 🛡️ Detection Engineering

Create alerts for:

- AXFR requests from external IP addresses.
- Large DNS responses over TCP Port 53.
- Unauthorized secondary DNS synchronization.
- Repeated Zone Transfer attempts.
- Unknown IPs querying SOA followed by AXFR.
- Failed AXFR requests (these still indicate reconnaissance).

A failed AXFR attempt is valuable intelligence—it tells you someone is probing your environment.

---

# 🚨 Detection Opportunities

Investigate when you observe:

- Query Type = AXFR
- Query Type = IXFR from unexpected systems
- External hosts contacting internal DNS servers
- High-volume DNS responses
- Multiple reconnaissance queries preceding an AXFR attempt
- DNS scans from cloud-hosted infrastructure

---

# ❌ Common Mistakes

### Mistake 1

"Zone Transfers are always malicious."

False.

Primary and secondary DNS servers legitimately perform Zone Transfers.

Always verify the source system.

---

### Mistake 2

"If the AXFR request failed, nothing happened."

False.

A failed AXFR still indicates reconnaissance.

The attacker has learned:

- A DNS server exists.
- Zone Transfers are restricted.
- The organization may be worth targeting.

---

### Mistake 3

"Only public DNS servers perform Zone Transfers."

False.

Internal Active Directory DNS servers also use Zone Transfers for replication and redundancy.

Context matters.

---

# 🎯 MITRE ATT&CK Mapping

| Activity | Technique |
|-----------|-----------|
| DNS Infrastructure Discovery | T1590 - Gather Victim Network Information |
| Active Reconnaissance | T1595 - Active Scanning |
| Internal Network Discovery | T1016 / T1046 (Context Dependent) |

> DNS Zone Transfers are typically associated with the **Reconnaissance** phase of the Cyber Kill Chain, where attackers gather information before attempting exploitation.

---

# 🧪 SOC Investigation Challenge

## Alert

```
External IP

↓

203.0.113.77

↓

TCP Port 53

↓

AXFR Request

↓

Response Size

18 MB

↓

DNS Server

↓

Successful
```

### Investigation Questions

1. Why was an AXFR request allowed?
2. Which DNS server responded?
3. Was the source IP trusted?
4. How many DNS records were transferred?
5. Was this a legitimate secondary DNS server?
6. Were additional reconnaissance activities observed?
7. Which firewall rules should be reviewed?
8. Should the DNS server configuration be audited?
9. What infrastructure information may now be exposed?
10. What containment actions should be taken?

---

# ⚡ Quick Revision

✅ A DNS Zone contains all DNS records for a domain.

✅ AXFR performs a **full** Zone Transfer.

✅ IXFR performs an **incremental** Zone Transfer.

✅ Zone Transfers are legitimate for DNS replication but dangerous if exposed to unauthorized systems.

✅ Successful AXFR requests can reveal an organization's entire DNS infrastructure.

---

# 🏆 SOC Analyst Tip

One of the easiest wins during a security assessment is checking for exposed Zone Transfers.

From a SOC perspective, **AXFR is a high-signal event**:

- It is rare.
- It is easy to detect.
- It often indicates reconnaissance.
- It can expose critical infrastructure information.

Even a **failed AXFR attempt** deserves investigation because it reveals attacker intent.

# 🔗 Knowledge Connections


DNS Zone Transfers
        │
        ├── DNS
        ├── SOA Records
        ├── NS Records
        ├── AXFR
        ├── IXFR
        ├── Reconnaissance
        ├── OSINT
        ├── Subdomain Enumeration
        ├── Active Directory DNS
        ├── Threat Hunting
        ├── Firewall Logs
        ├── Detection Engineering
        └── Incident Response
> **Next Section:** We'll dive into **DNS Tunneling**—one of the most important DNS abuse techniques used by attackers for **Command & Control (C2)** and **data exfiltration**. We'll cover how it works, real malware examples (Iodine, DNScat2, Cobalt Strike, etc.), Wireshark analysis, Splunk detection, Sigma rules, MITRE ATT&CK mapping, and a complete SOC investigation walkthrough.

---

# 🚨 DNS Tunneling - How Attackers Turn DNS into a Secret Communication Channel

> **"Firewalls block many things. DNS is usually allowed. Attackers know this—and that's why DNS Tunneling remains one of the most powerful covert communication techniques."**

---

# 🎯 Purpose

Most organizations allow DNS traffic without much restriction because it is essential for Internet access.

Without DNS,

users cannot browse websites,

applications cannot resolve servers,

and cloud services stop working.

Attackers abuse this trust.

Instead of using DNS only for resolving domain names,

they use DNS as a **communication channel**.

This technique is known as **DNS Tunneling**.

DNS Tunneling allows attackers to:

- Send commands to malware (Command & Control)
- Steal confidential information
- Bypass firewalls
- Evade network monitoring
- Maintain persistence inside a compromised network

For SOC analysts, DNS Tunneling is one of the most important techniques to understand because it often leaves subtle but detectable traces in DNS logs.

---

# 💡 Real World Analogy

Imagine a prison where every letter leaving the building is inspected.

The guards only expect letters to contain addresses.

Instead, a prisoner secretly hides messages **inside the address field**.

The guards still deliver the letter because it appears legitimate.

DNS Tunneling works in the same way.

Instead of sending normal DNS queries,

the attacker hides data **inside the DNS request itself**.

---

# 🌍 Normal DNS Communication

```
Laptop

↓

DNS Query

github.com

↓

DNS Server

↓

140.82.121.4

↓

Connection Established
```

Only a simple domain lookup occurs.

---

# 🌍 DNS Tunneling Communication

```
Laptop

↓

DNS Query

QWxleCBTYWxhcnk9NTAwMDA=.evil.com

↓

Attacker DNS Server

↓

Extract Hidden Data

↓

Send Response

↓

Malware Continues
```

The DNS request is no longer asking for an IP address.

It is secretly carrying information.

---

# 🤔 Why Attackers Use DNS

DNS traffic is:

- Trusted
- Always Allowed
- Rarely Blocked
- Often Ignored
- Usually Allowed Through Firewalls

Even organizations with strong web filtering often allow outbound DNS.

Attackers exploit this.

---

# 📦 What Can Be Stolen?

Almost anything.

Examples include:

```
Passwords

API Keys

Cookies

Documents

Credit Card Numbers

Database Records

Screenshots

Keystrokes

System Information

Domain Credentials

Kerberos Tickets
```

Instead of uploading files over HTTP,

the attacker sends small pieces inside DNS queries.

---

# 🔍 How DNS Tunneling Works

Imagine malware wants to steal:

```
Password:

Welcome@123
```

Step 1

Encode the data

```
V2VsY29tZUAxMjM=
```

Step 2

Place it inside the DNS query

```
V2VsY29tZUAxMjM=.company-data.evil.com
```

Step 3

The organization's DNS server forwards the request.

Step 4

The attacker's DNS server receives it.

Step 5

The attacker extracts:

```
Welcome@123
```

No HTTP upload was required.

---

# 🧠 Complete Attack Flow

```
Victim Opens Phishing Email

↓

Malware Executes

↓

Collect Credentials

↓

Encode Data

↓

DNS Query

↓

Corporate DNS

↓

Internet

↓

Attacker DNS Server

↓

Data Extracted

↓

Attacker Sends Next Command

↓

Malware Executes Again
```

Everything happens over DNS.

---

# 🔴 Red Team Perspective

DNS Tunneling is commonly used for:

- Command & Control (C2)
- Data Exfiltration
- Firewall Bypass
- Network Evasion
- Covert Communication
- Persistence

Popular offensive tools include:

- **dnscat2**
- **Iodine**
- **OzymanDNS**
- **Heyoka**
- **Cobalt Strike** (DNS Beacon)
- **Sliver C2**
- **Empire** (DNS listeners)

> ⚠️ These tools should only be used in authorized lab environments or penetration tests.

---

# 🦠 Malware Spotlight

Several malware families have abused DNS for C2 or data exfiltration.

Examples include:

- FrameworkPOS
- PlugX
- OilRig malware
- APT32 tooling
- DNSMessenger
- Cobalt Strike DNS Beacon
- Some variants of SUNBURST (used DNS for victim profiling before additional stages)

Rather than downloading commands over HTTP,

they ask the attacker's DNS server what to do next.

---

# 🔵 Blue Team Perspective

DNS Tunneling is difficult to detect,

but it is **not invisible**.

Analysts should monitor for:

- Extremely long domain names.
- Base64-like subdomains.
- High DNS query volume.
- Repeated TXT record lookups.
- Constant communication with one rare domain.
- DNS requests every few seconds.
- Domains with random-looking characters.
- Large numbers of NXDOMAIN responses.
- Unusual DNS traffic outside business hours.

---

# 🚨 Indicators of DNS Tunneling

Normal Query

```
github.com
```

Suspicious Query

```
QWxhZGRpbjpPcGVuU2VzYW1l.company-update-service.com
```

Normal Query

```
mail.google.com
```

Suspicious Query

```
Q0M6MTEwMDAwMTExMDA=.backup-service.evil.xyz
```

If you repeatedly observe long encoded-looking subdomains,

investigate immediately.

---

# 💼 Real SOC Investigation

## Alert

Splunk detects:

```
Host

↓

FINANCE-PC-17

↓

DNS Queries

↓

6,800

↓

Time Window

15 Minutes

↓

Average Query Length

78 Characters

↓

Record Type

TXT
```

---

### Investigation

Sysmon

```
powershell.exe

↓

python.exe

↓

dnscat2 Client
```

Firewall

```
53/UDP

↓

Repeated Queries

↓

One External Domain
```

DNS Logs

```
QWxhZGRpbjpPcGVuU2VzYW1l.attacker.xyz
```

Threat Intelligence

```
Domain Age

↓

2 Days
```

The analyst concludes:

> The endpoint is using DNS Tunneling for Command & Control and possible data exfiltration.

---

# 🔬 Wireshark Analysis

In Wireshark, look for:

```
Protocol

DNS
```

Observe:

- Very long Query Names
- Repeated TXT Requests
- Large DNS Responses
- High Query Frequency
- Same Destination Domain
- Encoded Subdomains
- Constant Request/Response Pattern

Useful filters:

```wireshark
dns
```

```wireshark
dns.qry.name
```

```wireshark
dns.flags.response == 0
```

```wireshark
dns.resp.type == TXT
```

---

# 📊 Splunk Hunting Ideas

Example searches:

```spl
index=dns
| stats count by query
| sort -count
```

---

Detect long domains:

```spl
index=dns
| eval length=len(query)
| where length > 60
```

---

Repeated DNS beaconing:

```spl
index=dns
| bucket span=1m _time
| stats count by src_ip, query
```

---

# 🛡️ Detection Engineering

Create alerts for:

- DNS queries longer than expected.
- High-frequency TXT requests.
- Excessive NXDOMAIN responses.
- DNS traffic every few seconds.
- Rare domains contacted repeatedly.
- Base64-like query strings.
- Domains with high entropy.
- DNS requests immediately followed by process execution.

---

# 🧪 Sigma Detection Idea

Detect suspicious PowerShell making DNS requests:

```yaml
title: Suspicious PowerShell DNS Activity

logsource:
  product: windows

detection:
  selection:
    Image: '*powershell.exe*'

  condition: selection

level: medium
```

> **Note:** This rule is intentionally simplified for learning. Production Sigma rules should include additional conditions such as suspicious command-line arguments, DNS-related cmdlets, parent processes, and exclusions to reduce false positives.

---

# ❌ Common Mistakes

### Mistake 1

"DNS is only for resolving websites."

False.

Attackers can use DNS as a covert communication protocol.

---

### Mistake 2

"If HTTP is blocked, malware cannot communicate."

False.

DNS itself can become the communication channel.

---

### Mistake 3

"Encrypted HTTPS always hides malware."

Not necessarily.

Many malware families reveal themselves through unusual DNS behavior before any HTTPS communication begins.

---

# 🎯 MITRE ATT&CK Mapping

| Activity | Technique |
|-----------|-----------|
| Command & Control via DNS | **T1071.004 – Application Layer Protocol: DNS** |
| Data Exfiltration Over DNS | **T1048 – Exfiltration Over Alternative Protocol** |
| Dynamic Resolution | **T1568** |
| DNS Beaconing | **T1071.004** |

---

# 🧪 SOC Investigation Challenge

## Alert

```
Endpoint

↓

HR-LAPTOP-08

↓

DNS Queries

↓

12,400

↓

Average Length

92 Characters

↓

TXT Records

↓

Every 8 Seconds

↓

One External Domain
```

### Investigation Questions

1. Which process generated the DNS traffic?
2. Is the queried domain newly registered?
3. Are the subdomains encoded?
4. Are TXT records being abused?
5. Is data being exfiltrated?
6. Are other hosts contacting the same domain?
7. What does the firewall show?
8. Is there evidence of malware execution?
9. Which MITRE ATT&CK techniques apply?
10. Would you isolate the endpoint immediately?

---

# ⚡ Quick Revision

✅ DNS Tunneling uses DNS as a covert communication channel.

✅ Attackers use it for **Command & Control** and **data exfiltration**.

✅ Look for long queries, high query frequency, TXT record abuse, high-entropy subdomains, and repeated communication with rare domains.

✅ Correlate DNS logs with EDR, Sysmon, firewall, proxy, and threat intelligence.

✅ DNS Tunneling is a high-priority hunting scenario in modern SOC environments.


# 🔗 Knowledge Connections


DNS Tunneling
      │
      ├── DNS
      ├── TXT Records
      ├── Command & Control
      ├── Malware
      ├── Data Exfiltration
      ├── Beaconing
      ├── Wireshark
      ├── Splunk
      ├── Sigma
      ├── Threat Hunting
      ├── MITRE ATT&CK
      ├── Incident Response
      └── Detection Engineering

> **Next Section:** We'll cover **DNS Beaconing**—how malware "checks in" with its Command & Control server at regular intervals, why beaconing is one of the strongest behavioral indicators of compromise, and how SOC analysts can detect it using DNS logs, EDR telemetry, Zeek, Wireshark, and SIEM correlation.

---

# 🚨 DNS Tunneling - How Attackers Turn DNS into a Secret Communication Channel

> **"Firewalls block many things. DNS is usually allowed. Attackers know this—and that's why DNS Tunneling remains one of the most powerful covert communication techniques."**

---

# 🎯 Purpose

Most organizations allow DNS traffic without much restriction because it is essential for Internet access.

Without DNS,

users cannot browse websites,

applications cannot resolve servers,

and cloud services stop working.

Attackers abuse this trust.

Instead of using DNS only for resolving domain names,

they use DNS as a **communication channel**.

This technique is known as **DNS Tunneling**.

DNS Tunneling allows attackers to:

- Send commands to malware (Command & Control)
- Steal confidential information
- Bypass firewalls
- Evade network monitoring
- Maintain persistence inside a compromised network

For SOC analysts, DNS Tunneling is one of the most important techniques to understand because it often leaves subtle but detectable traces in DNS logs.

---

# 💡 Real World Analogy

Imagine a prison where every letter leaving the building is inspected.

The guards only expect letters to contain addresses.

Instead, a prisoner secretly hides messages **inside the address field**.

The guards still deliver the letter because it appears legitimate.

DNS Tunneling works in the same way.

Instead of sending normal DNS queries,

the attacker hides data **inside the DNS request itself**.

---

# 🌍 Normal DNS Communication

```
Laptop

↓

DNS Query

github.com

↓

DNS Server

↓

140.82.121.4

↓

Connection Established
```

Only a simple domain lookup occurs.

---

# 🌍 DNS Tunneling Communication

```
Laptop

↓

DNS Query

QWxleCBTYWxhcnk9NTAwMDA=.evil.com

↓

Attacker DNS Server

↓

Extract Hidden Data

↓

Send Response

↓

Malware Continues
```

The DNS request is no longer asking for an IP address.

It is secretly carrying information.

---

# 🤔 Why Attackers Use DNS

DNS traffic is:

- Trusted
- Always Allowed
- Rarely Blocked
- Often Ignored
- Usually Allowed Through Firewalls

Even organizations with strong web filtering often allow outbound DNS.

Attackers exploit this.

---

# 📦 What Can Be Stolen?

Almost anything.

Examples include:

```
Passwords

API Keys

Cookies

Documents

Credit Card Numbers

Database Records

Screenshots

Keystrokes

System Information

Domain Credentials

Kerberos Tickets
```

Instead of uploading files over HTTP,

the attacker sends small pieces inside DNS queries.

---

# 🔍 How DNS Tunneling Works

Imagine malware wants to steal:

```
Password:

Welcome@123
```

Step 1

Encode the data

```
V2VsY29tZUAxMjM=
```

Step 2

Place it inside the DNS query

```
V2VsY29tZUAxMjM=.company-data.evil.com
```

Step 3

The organization's DNS server forwards the request.

Step 4

The attacker's DNS server receives it.

Step 5

The attacker extracts:

```
Welcome@123
```

No HTTP upload was required.

---

# 🧠 Complete Attack Flow

```
Victim Opens Phishing Email

↓

Malware Executes

↓

Collect Credentials

↓

Encode Data

↓

DNS Query

↓

Corporate DNS

↓

Internet

↓

Attacker DNS Server

↓

Data Extracted

↓

Attacker Sends Next Command

↓

Malware Executes Again
```

Everything happens over DNS.

---

# 🔴 Red Team Perspective

DNS Tunneling is commonly used for:

- Command & Control (C2)
- Data Exfiltration
- Firewall Bypass
- Network Evasion
- Covert Communication
- Persistence

Popular offensive tools include:

- **dnscat2**
- **Iodine**
- **OzymanDNS**
- **Heyoka**
- **Cobalt Strike** (DNS Beacon)
- **Sliver C2**
- **Empire** (DNS listeners)

> ⚠️ These tools should only be used in authorized lab environments or penetration tests.

---

# 🦠 Malware Spotlight

Several malware families have abused DNS for C2 or data exfiltration.

Examples include:

- FrameworkPOS
- PlugX
- OilRig malware
- APT32 tooling
- DNSMessenger
- Cobalt Strike DNS Beacon
- Some variants of SUNBURST (used DNS for victim profiling before additional stages)

Rather than downloading commands over HTTP,

they ask the attacker's DNS server what to do next.

---

# 🔵 Blue Team Perspective

DNS Tunneling is difficult to detect,

but it is **not invisible**.

Analysts should monitor for:

- Extremely long domain names.
- Base64-like subdomains.
- High DNS query volume.
- Repeated TXT record lookups.
- Constant communication with one rare domain.
- DNS requests every few seconds.
- Domains with random-looking characters.
- Large numbers of NXDOMAIN responses.
- Unusual DNS traffic outside business hours.

---

# 🚨 Indicators of DNS Tunneling

Normal Query

```
github.com
```

Suspicious Query

```
QWxhZGRpbjpPcGVuU2VzYW1l.company-update-service.com
```

Normal Query

```
mail.google.com
```

Suspicious Query

```
Q0M6MTEwMDAwMTExMDA=.backup-service.evil.xyz
```

If you repeatedly observe long encoded-looking subdomains,

investigate immediately.

---

# 💼 Real SOC Investigation

## Alert

Splunk detects:

```
Host

↓

FINANCE-PC-17

↓

DNS Queries

↓

6,800

↓

Time Window

15 Minutes

↓

Average Query Length

78 Characters

↓

Record Type

TXT
```

---

### Investigation

Sysmon

```
powershell.exe

↓

python.exe

↓

dnscat2 Client
```

Firewall

```
53/UDP

↓

Repeated Queries

↓

One External Domain
```

DNS Logs

```
QWxhZGRpbjpPcGVuU2VzYW1l.attacker.xyz
```

Threat Intelligence

```
Domain Age

↓

2 Days
```

The analyst concludes:

> The endpoint is using DNS Tunneling for Command & Control and possible data exfiltration.

---

# 🔬 Wireshark Analysis

In Wireshark, look for:

```
Protocol

DNS
```

Observe:

- Very long Query Names
- Repeated TXT Requests
- Large DNS Responses
- High Query Frequency
- Same Destination Domain
- Encoded Subdomains
- Constant Request/Response Pattern

Useful filters:

```wireshark
dns
```

```wireshark
dns.qry.name
```

```wireshark
dns.flags.response == 0
```

```wireshark
dns.resp.type == TXT
```

---

# 📊 Splunk Hunting Ideas

Example searches:

```spl
index=dns
| stats count by query
| sort -count
```

---

Detect long domains:

```spl
index=dns
| eval length=len(query)
| where length > 60
```

---

Repeated DNS beaconing:

```spl
index=dns
| bucket span=1m _time
| stats count by src_ip, query
```

---

# 🛡️ Detection Engineering

Create alerts for:

- DNS queries longer than expected.
- High-frequency TXT requests.
- Excessive NXDOMAIN responses.
- DNS traffic every few seconds.
- Rare domains contacted repeatedly.
- Base64-like query strings.
- Domains with high entropy.
- DNS requests immediately followed by process execution.

---

# 🧪 Sigma Detection Idea

Detect suspicious PowerShell making DNS requests:

```yaml
title: Suspicious PowerShell DNS Activity

logsource:
  product: windows

detection:
  selection:
    Image: '*powershell.exe*'

  condition: selection

level: medium
```

> **Note:** This rule is intentionally simplified for learning. Production Sigma rules should include additional conditions such as suspicious command-line arguments, DNS-related cmdlets, parent processes, and exclusions to reduce false positives.

---

# ❌ Common Mistakes

### Mistake 1

"DNS is only for resolving websites."

False.

Attackers can use DNS as a covert communication protocol.

---

### Mistake 2

"If HTTP is blocked, malware cannot communicate."

False.

DNS itself can become the communication channel.

---

### Mistake 3

"Encrypted HTTPS always hides malware."

Not necessarily.

Many malware families reveal themselves through unusual DNS behavior before any HTTPS communication begins.

---

# 🎯 MITRE ATT&CK Mapping

| Activity | Technique |
|-----------|-----------|
| Command & Control via DNS | **T1071.004 – Application Layer Protocol: DNS** |
| Data Exfiltration Over DNS | **T1048 – Exfiltration Over Alternative Protocol** |
| Dynamic Resolution | **T1568** |
| DNS Beaconing | **T1071.004** |

---

# 🧪 SOC Investigation Challenge

## Alert

```
Endpoint

↓

HR-LAPTOP-08

↓

DNS Queries

↓

12,400

↓

Average Length

92 Characters

↓

TXT Records

↓

Every 8 Seconds

↓

One External Domain
```

### Investigation Questions

1. Which process generated the DNS traffic?
2. Is the queried domain newly registered?
3. Are the subdomains encoded?
4. Are TXT records being abused?
5. Is data being exfiltrated?
6. Are other hosts contacting the same domain?
7. What does the firewall show?
8. Is there evidence of malware execution?
9. Which MITRE ATT&CK techniques apply?
10. Would you isolate the endpoint immediately?

---

# ⚡ Quick Revision

✅ DNS Tunneling uses DNS as a covert communication channel.

✅ Attackers use it for **Command & Control** and **data exfiltration**.

✅ Look for long queries, high query frequency, TXT record abuse, high-entropy subdomains, and repeated communication with rare domains.

✅ Correlate DNS logs with EDR, Sysmon, firewall, proxy, and threat intelligence.

✅ DNS Tunneling is a high-priority hunting scenario in modern SOC environments.

# 🔗 Knowledge Connections


DNS Tunneling
      │
      ├── DNS
      ├── TXT Records
      ├── Command & Control
      ├── Malware
      ├── Data Exfiltration
      ├── Beaconing
      ├── Wireshark
      ├── Splunk
      ├── Sigma
      ├── Threat Hunting
      ├── MITRE ATT&CK
      ├── Incident Response
      └── Detection Engineering

> **Next Section:** We'll cover **DNS Beaconing**—how malware "checks in" with its Command & Control server at regular intervals, why beaconing is one of the strongest behavioral indicators of compromise, and how SOC analysts can detect it using DNS logs, EDR telemetry, Zeek, Wireshark, and SIEM correlation.

---

# 📡 DNS Beaconing - The Malware "Heartbeat"

> **"Malware doesn't continuously talk to its attacker. Instead, it periodically checks in, waits for instructions, executes them, and checks in again. That periodic communication is called Beaconing."**

---

# 🎯 Purpose

Once malware infects a victim machine,

its first goal is usually **not** stealing files.

Instead, it wants to answer one question:

> **"Can I still communicate with my operator?"**

To achieve this,

the malware periodically contacts its **Command & Control (C2) Server**.

This periodic communication is called **Beaconing**.

One of the most common ways to implement beaconing is through **DNS** because DNS traffic is almost always allowed.

---

# 💡 Real World Analogy

Imagine a spy.

Instead of calling headquarters every second,

he sends a message every hour.

```
"Everything is normal."

↓

Wait

↓

"Still here."

↓

Wait

↓

"Any new mission?"
```

Nothing appears suspicious because the communication is:

- Small
- Predictable
- Quiet
- Regular

Malware behaves exactly the same way.

---

# 🌍 What is a Beacon?

A Beacon is simply a small communication between malware and its operator.

Example

```
Victim

↓

DNS Query

↓

C2 Server

↓

"Do you have any commands?"

↓

"No."

↓

Wait 60 Seconds

↓

Repeat
```

The malware may perform this for:

- Days
- Weeks
- Months

without performing any other activity.

---

# 🤔 Why DNS Beaconing?

Attackers prefer DNS because:

✅ DNS is almost always allowed.

✅ DNS rarely requires authentication.

✅ Firewalls often trust DNS.

✅ Small DNS packets don't attract attention.

✅ DNS logs are often ignored.

---

# 📦 Typical DNS Beacon

```
finance-pc.company.local

↓

update.microsoft-check.com

↓

TXT Record

↓

"No Commands"

↓

Sleep

↓

Repeat
```

Looks harmless.

Actually,

the malware is waiting for instructions.

---

# 🔄 Typical Beacon Cycle

```
Malware Starts

↓

Sleep

↓

DNS Query

↓

Receive Response

↓

Execute Commands

↓

Sleep Again

↓

Repeat Forever
```

This cycle is called the **Beacon Interval**.

---

# ⏱️ Beacon Interval

Every malware family has its own timing.

Example

```
Every 5 Seconds

↓

Very Noisy
```

---

```
Every 30 Seconds

↓

Common
```

---

```
Every 5 Minutes

↓

Stealthier
```

---

```
Random

↓

Most Difficult To Detect
```

Modern malware often randomizes ("jitters") the interval to avoid predictable patterns.

---

# 🎯 Beacon with Jitter

Instead of:

```
Exactly Every 60 Seconds
```

Malware may communicate:

```
57 Seconds

↓

73 Seconds

↓

64 Seconds

↓

51 Seconds

↓

69 Seconds
```

This randomness is called **Jitter**.

It makes simple interval-based detection much harder.

---

# 🏢 Real Enterprise Example

An employee opens a phishing attachment.

```
Excel Macro

↓

PowerShell

↓

Malware Installed

↓

DNS Beacon Every 60 Seconds
```

No ransomware.

No file encryption.

No obvious alerts.

Only one DNS request every minute.

This attack could remain unnoticed unless analysts recognize the beaconing pattern.

---

# 🔴 Red Team Perspective

Beaconing provides attackers with:

- Persistent C2 communication.
- Remote command execution.
- Download capability.
- Data upload capability.
- Lateral movement coordination.
- Network health monitoring.

Frameworks supporting configurable beacon intervals include:

- Cobalt Strike
- Sliver
- Mythic
- Havoc
- Brute Ratel
- Empire

Operators carefully tune beacon timing to balance responsiveness and stealth.

---

# 🦠 Malware Spotlight

Examples of malware using beacon-like communication include:

- Cobalt Strike Beacon
- TrickBot
- QakBot
- Emotet
- IcedID
- Bumblebee
- PlugX
- AsyncRAT
- Sliver implants

Although transport methods differ (DNS, HTTP, HTTPS, SMB, etc.), periodic "check-ins" are a common behavior.

---

# 🔵 Blue Team Perspective

Beaconing is one of the strongest behavioral indicators of compromise.

Monitor for:

- DNS requests at fixed intervals.
- Communication with one rare domain.
- Repeated TXT lookups.
- Low-volume but persistent traffic.
- DNS traffic during nights or weekends.
- Identical request sizes.
- Regular HTTPS connections after DNS resolution.
- Long-lived outbound sessions.

Behavior often reveals malware even when signatures do not.

---

# 💼 Real SOC Investigation

## Alert

Splunk identifies:

```
ENG-LAPTOP-22

↓

DNS Query

↓

every 60 seconds

↓

update-service.cloud

↓

24 Hours
```

---

### Investigation Timeline

```
08:10

powershell.exe

↓

08:11

DNS Query

↓

08:12

DNS Query

↓

08:13

DNS Query

↓

08:14

DNS Query

↓

08:15

DNS Query
```

The endpoint generated exactly one DNS request every minute for an entire day.

No user activity matched this pattern.

---

### EDR Findings

```
powershell.exe

↓

Spawned

↓

unknown.exe

↓

Scheduled Task Created

↓

Beacon Every 60 Seconds
```

Threat Intelligence classified the domain as malicious.

The endpoint was isolated before additional payloads were delivered.

---

# 🔬 Wireshark Analysis

Useful display filters:

```wireshark
dns
```

Observe:

- Same destination domain.
- Constant intervals.
- TXT record requests.
- Small packets.
- Low bandwidth.
- Long-running communication.

Timeline View:

```
10:00

DNS

↓

10:01

DNS

↓

10:02

DNS

↓

10:03

DNS

↓

10:04

DNS
```

Perfect periodicity is a major indicator.

---

# 📊 Splunk Hunting Ideas

Top queried domains:

```spl
index=dns
| stats count by query
| sort -count
```

---

Repeated DNS activity by host:

```spl
index=dns
| bucket span=1m _time
| stats count by src_ip query _time
```

---

Frequent TXT lookups:

```spl
index=dns record_type=TXT
| stats count by src_ip query
```

---

Hosts communicating with one domain continuously:

```spl
index=dns
| stats earliest(_time) latest(_time) count by src_ip query
```

---

# 🛡️ Detection Engineering

Alert on:

- Fixed interval DNS requests.
- High-frequency TXT records.
- One endpoint contacting one rare domain repeatedly.
- DNS requests followed by HTTPS connections.
- Long-running DNS communication.
- Repeated NXDOMAIN lookups.
- DNS activity from service accounts.
- Identical DNS packet sizes over long periods.

Behavior-based detection is generally more resilient than signature-based detection.

---

# 🚨 Beaconing vs DNS Tunneling

| DNS Beaconing | DNS Tunneling |
|---------------|---------------|
| Small periodic communication | Hidden data transfer |
| Checks for commands | Transfers commands or stolen data |
| Usually low bandwidth | Can consume higher bandwidth |
| Long-term persistence | Often active during exfiltration |
| "Are there new instructions?" | "Here's the stolen data." |

Think of Beaconing as the **heartbeat** and Tunneling as the **conversation**.

---

# ❌ Common Mistakes

### Mistake 1

"Low DNS volume means low risk."

False.

A single DNS request every minute for weeks may indicate persistent malware.

---

### Mistake 2

"Beaconing always uses DNS."

False.

Beaconing can also occur over:

- HTTP
- HTTPS
- SMB
- ICMP
- WebSockets
- Named Pipes
- Cloud APIs

DNS is just one transport mechanism.

---

### Mistake 3

"If the interval changes, it's not beaconing."

False.

Modern malware often introduces **jitter** specifically to defeat interval-based detections.

---

# 🎯 MITRE ATT&CK Mapping

| Activity | Technique |
|-----------|-----------|
| DNS-Based C2 | **T1071.004 – Application Layer Protocol: DNS** |
| Web-Based C2 | **T1071.001 – Web Protocols** |
| Encrypted C2 | **T1573 – Encrypted Channel** |
| Dynamic Resolution | **T1568** |

---

# 🧪 SOC Investigation Challenge

## Alert

```
Endpoint

↓

HR-LAPTOP-15

↓

DNS Query

↓

secure-update-sync.com

↓

Every 65 ± 8 Seconds

↓

TXT Record

↓

14 Days

↓

No User Activity
```

### Investigation Questions

1. Which process is generating the DNS queries?
2. Does the beacon interval include jitter?
3. Is the domain newly registered?
4. Are TXT records carrying commands?
5. Has the endpoint downloaded additional payloads?
6. Are other hosts beaconing to the same domain?
7. What does Sysmon Event ID 22 reveal?
8. Is there evidence of scheduled tasks or persistence?
9. Which MITRE ATT&CK techniques apply?
10. Should the endpoint be isolated immediately?

---

# ⚡ Quick Revision

✅ Beaconing is periodic communication between malware and its C2 server.

✅ DNS is a popular beaconing protocol because it is widely trusted.

✅ Look for fixed or semi-random intervals, repeated TXT requests, and persistent communication with rare domains.

✅ Correlate DNS logs with EDR, Sysmon, firewall, proxy, and threat intelligence.

✅ Beaconing is one of the most reliable behavioral indicators of malware persistence.

---

# 🏆 SOC Analyst Tip

Experienced analysts rarely detect beaconing from a **single log source**.

Instead, they build a timeline:

```
Process Creation
        ↓
DNS Query
        ↓
Firewall Connection
        ↓
Threat Intelligence
        ↓
Sysmon
        ↓
User Activity
        ↓
Proxy Logs
        ↓
EDR Telemetry
```

When all these pieces align, a seemingly harmless DNS query can become the first clue that uncovers an advanced compromise.



# 🔗 Knowledge Connections


DNS Beaconing
      │
      ├── DNS
      ├── Command & Control
      ├── DNS Tunneling
      ├── Malware
      ├── Cobalt Strike
      ├── Sliver
      ├── Threat Hunting
      ├── Splunk
      ├── Sigma
      ├── Wireshark
      ├── Sysmon
      ├── MITRE ATT&CK
      ├── Incident Response
      └── Detection Engineering


> **Next Section:** **DNS in Active Directory (AD)** — one of the most important topics for SOC analysts. We'll connect everything you've learned about DNS with **Kerberos, LDAP, SRV records, Domain Controllers, authentication, BloodHound, SharpHound, lateral movement, and real-world Active Directory attack investigations.** This will be one of the strongest chapters in the Networking repository.

---

# 🏢 DNS in Active Directory (AD) - The Foundation of Windows Authentication

> **"Without DNS, Active Directory doesn't work. Every login, every Kerberos ticket, every Group Policy update, and every Domain Controller discovery begins with DNS."**

---

# 🎯 Purpose

When people think about DNS,

they usually think about websites.

```
google.com

↓

IP Address
```

But inside an enterprise,

DNS has a much bigger responsibility.

It allows Windows computers to locate:

- Domain Controllers
- LDAP Servers
- Kerberos Servers
- Global Catalog Servers
- File Servers
- Certificate Authorities
- Exchange Servers

Without DNS,

Active Directory becomes almost unusable.

For SOC analysts,

understanding AD DNS is essential because **almost every attack against Active Directory starts with DNS discovery.**

---

# 🌍 Real World Example

Imagine joining a new company.

On your first day,

you don't know:

- Where HR sits.
- Where IT sits.
- Where Finance sits.

So you ask the receptionist.

The receptionist tells you exactly where to go.

DNS plays the same role inside Active Directory.

Instead of asking:

```
Where is HR?
```

A computer asks:

```
Where is the Domain Controller?
```

DNS provides the answer.

---

# 💻 What Happens When You Log In?

Suppose a user logs into:

```
CORP\John
```

Windows does **not** magically know where the Domain Controller is.

Instead,

it performs several DNS queries.

---

## Step 1

The workstation starts.

```
HR-PC-21
```

↓

Needs Authentication

↓

Looks for a Domain Controller

---

## Step 2

Windows queries DNS.

```
_ldap._tcp.dc._msdcs.company.local
```

↓

DNS replies:

```
dc01.company.local

Port 389
```

The workstation now knows where LDAP is running.

---

## Step 3

Windows needs Kerberos.

DNS Query:

```
_kerberos._tcp.company.local
```

↓

DNS replies:

```
dc01.company.local

Port 88
```

---

## Step 4

The workstation contacts the Domain Controller.

```
Kerberos AS-REQ

↓

AS-REP

↓

TGT Issued
```

Authentication begins.

---

# 🔍 Complete Authentication Flow

```
User Login

↓

DNS Query

↓

LDAP SRV Record

↓

Domain Controller Found

↓

Kerberos SRV Record

↓

Authentication Server Found

↓

Kerberos Ticket

↓

User Logged In
```

Notice something important.

**Everything starts with DNS.**

---

# 🗂️ Important SRV Records

Active Directory relies heavily on **SRV Records**.

These records tell Windows where services are located.

| SRV Record | Purpose |
|------------|----------|
| `_ldap._tcp.dc._msdcs.company.local` | Locate Domain Controllers |
| `_kerberos._tcp.company.local` | Locate Kerberos Server |
| `_gc._tcp.company.local` | Locate Global Catalog |
| `_kpasswd._tcp.company.local` | Password Change Service |
| `_ldap._tcp.SiteName._sites.company.local` | Site-specific DC discovery |

SOC analysts should become familiar with these records because they frequently appear during authentication and attacker reconnaissance.

---

# 🏢 Enterprise Example

Employee opens laptop.

```
Laptop

↓

Connects to Wi-Fi

↓

Needs Domain Login

↓

DNS

↓

Find Domain Controller

↓

Kerberos

↓

Authenticate

↓

Apply Group Policy

↓

Map Network Drives

↓

Ready To Use
```

DNS is involved before the user even opens Outlook.

---

# 🔴 Red Team Perspective

One of the first things attackers do after compromising a Windows endpoint is discover the Active Directory environment.

Typical sequence:

```
Compromised Host

↓

DNS SRV Queries

↓

Locate Domain Controller

↓

Locate LDAP

↓

Locate Kerberos

↓

Locate Global Catalog

↓

Begin Enumeration
```

The attacker isn't attacking yet.

They're gathering information.

---

# 🛠️ Tools That Query AD DNS

Many legitimate administration tools—and many offensive tools—perform these lookups.

Examples include:

- BloodHound
- SharpHound
- PowerView
- Impacket
- Rubeus
- Certipy
- Windows `nltest`
- Windows `dsquery`

These tools often begin by asking DNS where Active Directory services are located.

---

# 🦠 Real Malware Example

Imagine ransomware gains access to a workstation.

Before encrypting anything,

it wants to understand the environment.

```
Malware

↓

SRV Lookup

↓

Find Domain Controller

↓

LDAP Enumeration

↓

Kerberos Enumeration

↓

Privilege Escalation

↓

Lateral Movement

↓

Ransomware Deployment
```

DNS is often the first observable step.

---

# 🔵 Blue Team Perspective

SRV lookups occur constantly in Active Directory,

so they are **not inherently malicious**.

However,

investigate when you observe:

- Large numbers of SRV queries.
- Workstations querying every AD service.
- PowerShell immediately followed by LDAP lookups.
- BloodHound or SharpHound execution.
- DNS discovery followed by SMB enumeration.
- Authentication attempts from unexpected hosts.

The sequence of events matters more than the individual query.

---

# 💼 Real SOC Investigation

## Alert

Sysmon

```
powershell.exe
```

↓

DNS Queries

```
_ldap._tcp.dc._msdcs.company.local

↓

_kerberos._tcp.company.local

↓

_gc._tcp.company.local
```

↓

LDAP Connections

↓

SMB Connections

↓

LSASS Access Attempt

---

### Timeline

```
09:10

PowerShell Started

↓

09:11

LDAP Discovery

↓

09:12

Kerberos Discovery

↓

09:13

SMB Enumeration

↓

09:14

BloodHound Executed

↓

09:15

Credential Dump Attempt
```

This sequence strongly suggests Active Directory reconnaissance followed by privilege escalation attempts.

---

# 🛡️ Detection Engineering

Consider alerts for:

- Numerous SRV record lookups in a short time.
- DNS discovery immediately followed by LDAP enumeration.
- SharpHound execution.
- BloodHound collection activity.
- PowerShell querying Domain Controllers.
- Unusual Kerberos discovery from non-admin systems.
- DNS queries followed by SMB sessions to multiple hosts.

One event rarely tells the story.

The **sequence** often does.

---

# 🚨 Detection Opportunities

Watch for:

- SRV queries outside normal authentication patterns.
- Repeated requests for multiple AD services.
- LDAP enumeration after DNS discovery.
- Kerberos ticket spikes.
- DNS discovery from service accounts.
- Workstations scanning multiple Domain Controllers.
- Discovery followed by PsExec, WMI, or WinRM activity.

---

# 🔗 Connecting DNS to the Attack Chain

```
DNS Discovery

↓

Find Domain Controller

↓

LDAP Enumeration

↓

Kerberos Enumeration

↓

User Enumeration

↓

Group Enumeration

↓

Privilege Escalation

↓

Lateral Movement

↓

Domain Admin

↓

Persistence

↓

Ransomware
```

Understanding this chain helps analysts identify attacks **before** the final impact.

---

# ❌ Common Mistakes

### Mistake 1

"SRV lookups are always malicious."

False.

Every Windows workstation performs legitimate SRV lookups during normal operation.

Context is everything.

---

### Mistake 2

"If I see LDAP traffic, an attack is happening."

False.

LDAP is used constantly for authentication, Group Policy, and directory services.

Look for abnormal behavior, not normal protocol usage.

---

### Mistake 3

"DNS has nothing to do with Active Directory security."

False.

DNS is one of the earliest indicators of Active Directory reconnaissance.

---

# 🎯 MITRE ATT&CK Mapping

| Activity | Technique |
|-----------|-----------|
| Active Directory Discovery | **T1482** |
| Network Service Discovery | **T1046** |
| System Network Configuration Discovery | **T1016** |
| Permission Groups Discovery | **T1069** |
| Account Discovery | **T1087** |
| Remote Services | **T1021** |

---

# 🧪 SOC Investigation Challenge

## Alert

```
FINANCE-PC-03

↓

powershell.exe

↓

SRV Queries

↓

_ldap._tcp.dc._msdcs.company.local

↓

_kerberos._tcp.company.local

↓

_gc._tcp.company.local

↓

LDAP Sessions

↓

BloodHound.exe

↓

SMB Connections

↓

Administrator Logon
```

### Investigation Questions

1. Which process initiated the SRV queries?
2. Were these lookups expected for this endpoint?
3. Was BloodHound executed?
4. Which Domain Controllers were contacted?
5. Were Kerberos tickets requested?
6. Was LDAP enumeration successful?
7. Did lateral movement follow?
8. Were privileged accounts involved?
9. Which additional logs should be reviewed?
10. Which MITRE ATT&CK techniques best describe this activity?

---

# ⚡ Quick Revision

✅ Active Directory depends heavily on DNS.

✅ Windows uses SRV records to locate Domain Controllers and authentication services.

✅ Every authentication begins with DNS discovery.

✅ Attackers also use DNS to locate AD services before enumeration and lateral movement.

✅ Correlating DNS, LDAP, Kerberos, SMB, Sysmon, and EDR telemetry provides excellent visibility into Active Directory attacks.

---

# 🏆 SOC Analyst Tip

One isolated DNS query rarely indicates an attack.

But when you observe this sequence:

```
PowerShell
      ↓
DNS SRV Queries
      ↓
LDAP Enumeration
      ↓
Kerberos Requests
      ↓
SMB Connections
      ↓
BloodHound
```

you are no longer looking at "network traffic."

You're watching an attacker map your Active Directory environment in real time.


# 🔗 Knowledge Connections

Active Directory DNS
         │
         ├── DNS
         ├── SRV Records
         ├── LDAP
         ├── Kerberos
         ├── Domain Controllers
         ├── Global Catalog
         ├── BloodHound
         ├── SharpHound
         ├── PowerView
         ├── Rubeus
         ├── Sysmon
         ├── Splunk
         ├── Threat Hunting
         ├── Detection Engineering
         └── MITRE ATT&CK

---

# 🌐 Split DNS (Split Horizon DNS) - One Domain, Two Different Answers

> **"The same domain can return different IP addresses depending on who is asking."**

---

# 🎯 Purpose

Organizations often need to expose some services to the Internet while keeping others private.

For example,

```
company.com
```

should work for:

- Employees inside the office.
- Employees connected through VPN.
- Customers on the Internet.

However,

these users should **not** always receive the same DNS response.

This is where **Split DNS** comes into play.

Split DNS allows an organization to return **different DNS records for the same domain** based on where the DNS request originates.

---

# 💡 Real World Analogy

Imagine a large company office.

Employees enter through:

```
Employee Entrance
```

Visitors enter through:

```
Visitor Entrance
```

Both are entering the same building,

but they receive different access.

Split DNS works similarly.

The same domain is queried,

but different users receive different answers.

---

# 🌍 Example

An employee inside the corporate network queries:

```
portal.company.com
```

Internal DNS replies:

```
10.10.20.15
```

An external customer queries:

```
portal.company.com
```

Public DNS replies:

```
203.0.113.20
```

Same domain.

Different IP addresses.

---

# 🏢 Enterprise Example

```
Employee

↓

Internal DNS

↓

portal.company.com

↓

10.10.20.15

(Private Server)
```

---

```
Customer

↓

Public DNS

↓

portal.company.com

↓

203.0.113.20

(Public Web Server)
```

The employee accesses internal resources.

The customer reaches only the public-facing application.

---

# 🤔 Why Organizations Use Split DNS

Split DNS helps organizations:

- Hide internal infrastructure.
- Prevent exposure of private IP addresses.
- Separate internal and external services.
- Improve security.
- Simplify internal access.
- Reduce unnecessary Internet traffic.

---

# 🔴 Red Team Perspective

Attackers often compare:

```
Internal DNS

vs

External DNS
```

to identify differences.

If internal DNS accidentally exposes:

- Domain Controllers
- File Servers
- Backup Servers
- Development Systems

the attacker gains valuable reconnaissance data.

Misconfigured Split DNS can leak sensitive infrastructure information.

---

# 🔵 Blue Team Perspective

SOC analysts should investigate:

- Unexpected public exposure of internal hostnames.
- Differences between internal and external DNS records.
- Internal DNS queries originating from unauthorized devices.
- VPN users receiving incorrect DNS responses.
- DNS misconfigurations after infrastructure changes.

---

# 💼 Real SOC Investigation

## Alert

A penetration test reports:

```
vpn.company.com

↓

Public DNS

↓

10.10.10.8
```

Instead of returning a public IP,

the external DNS server leaked a private address.

Although the private IP is unreachable from the Internet,

the attacker now learns:

- Internal addressing scheme.
- VPN naming convention.
- Internal network ranges.

The issue is corrected before it can aid further reconnaissance.

---

# 🛡️ Detection Opportunities

Monitor for:

- Internal IP addresses exposed in public DNS.
- Unauthorized external DNS zone changes.
- Unexpected differences between internal and external records.
- DNS misconfigurations after deployments.
- Public records pointing to internal services.

---

# ❌ Common Mistakes

### Mistake 1

"Split DNS means two different domains."

False.

The domain remains the same.

Only the DNS response changes.

---

### Mistake 2

"Private IP addresses are harmless if exposed."

False.

Even if unreachable,

they reveal valuable information about internal network architecture.

---

# 🎯 MITRE ATT&CK Mapping

Split DNS is a defensive architecture and does **not** directly map to a MITRE ATT&CK technique.

However, misconfigured Split DNS may assist attackers performing:

| Activity | Technique |
|-----------|-----------|
| Network Reconnaissance | T1590 |
| Active Scanning | T1595 |
| Infrastructure Discovery | T1592 |

---

# 🧪 SOC Investigation Challenge

## Alert

```
portal.company.com

↓

Internal DNS

↓

10.20.15.25

↓

Public DNS

↓

10.20.15.25
```

### Investigation Questions

1. Why is a private IP exposed publicly?
2. Was this caused by a recent DNS change?
3. Which DNS server hosts the incorrect record?
4. Are other internal services exposed?
5. What information could an attacker gain?

---

# ⚡ Quick Revision

✅ Split DNS returns different answers for the same domain.

✅ Internal users typically receive private IP addresses.

✅ External users receive public IP addresses.

✅ Misconfigurations may leak internal infrastructure information.


# 🔗 Knowledge Connections

Split DNS
      │
      ├── DNS
      ├── Internal DNS
      ├── Public DNS
      ├── VPN
      ├── DNS Zones
      ├── Network Segmentation
      ├── Active Directory
      ├── Threat Hunting
      └── Incident Response
---

# 📜 DNS Logging - The First Clue in Many Cyber Attacks

> **"Attackers can hide their malware, encrypt their traffic, and obfuscate their payloads—but almost every attack still needs DNS."**

---

# 🎯 Purpose

Every time a device resolves a domain name,

a DNS server may generate a log entry.

These logs tell us:

- Who made the request
- When it happened
- What domain was queried
- Which DNS server answered
- Whether the lookup succeeded or failed

For SOC analysts,

DNS logs are often the **first indicator of compromise (IOC)**.

Many attacks begin with DNS long before malware is downloaded or credentials are stolen.

---

# 🌍 Why DNS Logs Matter

Imagine a phishing attack.

```
User Opens Email

↓

Clicks Link

↓

Laptop Queries

login-microsoft-support[.]com

↓

DNS Resolves Domain

↓

Malicious Website Opens
```

Even if the attacker later deletes the phishing website,

the DNS log remains.

It becomes valuable forensic evidence.

---

# 💡 Real World Analogy

Imagine a receptionist at a company.

Every visitor signs a register before entering.

Months later,

even if the visitor has left,

the register still contains:

- Name
- Time
- Date
- Purpose of visit

DNS logs work in the same way.

They record which domains systems attempted to visit.

---

# 📝 What Does a DNS Log Contain?

Although formats vary between products,

most DNS logs include:

| Field | Description |
|--------|-------------|
| Timestamp | When the query occurred |
| Client IP | Device making the request |
| Queried Domain | Domain being resolved |
| Record Type | A, AAAA, MX, TXT, SRV, etc. |
| Response Code | Success or Failure |
| DNS Server | Resolver handling the request |
| Response IP | Returned IP Address |

These fields allow analysts to reconstruct user and malware activity.

---

# 🏢 Enterprise Example

Employee opens a browser.

```
Laptop

↓

DNS Query

github.com

↓

DNS Server

↓

Log Generated
```

Sample log:

```
Time:
09:42:31

Client:
10.20.15.44

Query:
github.com

Record:
A

Response:
140.82.121.4

Status:
NOERROR
```

Simple.

But incredibly valuable.

---

# 🔴 Red Team Perspective

Attackers know defenders rely on DNS logs.

To reduce visibility,

they may:

- Use Domain Generation Algorithms (DGA)
- Use DNS over HTTPS (DoH)
- Register short-lived domains
- Rotate infrastructure frequently
- Use cloud providers
- Use compromised legitimate domains

Even then,

DNS activity often leaves useful traces.

---

# 🦠 Malware Example

Malware starts.

```
↓

DNS Query

↓

cdn-update-storage[.]xyz

↓

IP Returned

↓

HTTPS Connection

↓

Payload Downloaded
```

The DNS request happened **before** the malware download.

An analyst reviewing logs can reconstruct the entire timeline.

---

# 🔵 Blue Team Perspective

SOC analysts use DNS logs to identify:

- Malware communication
- Phishing domains
- Command & Control servers
- Beaconing activity
- DNS tunneling
- Data exfiltration
- Newly registered domains
- DGA-generated domains
- Suspicious TXT requests

DNS logs often answer the question:

> **"What was the system trying to reach?"**

---

# 💼 Real SOC Investigation

## Alert

EDR reports:

```
powershell.exe

↓

Unknown Network Activity
```

Firewall logs show:

```
Outbound HTTPS

↓

185.xxx.xxx.xxx
```

The IP has no hostname.

DNS logs reveal:

```
powershell.exe

↓

cdn-security-check[.]xyz

↓

Resolved

↓

185.xxx.xxx.xxx
```

Threat Intelligence identifies the domain as malicious.

Without DNS logs,

the analyst would only have an IP address.

---

# 📊 Common DNS Log Sources

SOC teams commonly collect DNS logs from:

- Windows DNS Server
- BIND DNS
- Microsoft Defender
- Sysmon (Event ID 22)
- Zeek
- Infoblox
- Cisco Umbrella
- Cloudflare Gateway
- AWS Route 53 Resolver Logs
- Azure DNS Analytics

Each source provides different levels of visibility.

---

# 🖥️ Sysmon Event ID 22

One of the most useful Windows events.

It records:

- Process Name
- Process ID
- User
- Queried Domain
- Query Result

Example:

```
Image

C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe

↓

Query

evil-update.xyz

↓

Result

203.0.113.55
```

Now you know **which process** performed the DNS lookup.

That is incredibly valuable during investigations.

---

# 📡 Zeek DNS Logs

Zeek captures rich DNS telemetry including:

- Query
- Response
- Record Type
- TTL
- Client
- Server
- Transaction ID

Unlike Windows DNS logs,

Zeek observes traffic directly from the network.

---

# 🛡️ Detection Engineering

Create alerts for:

- Newly registered domains
- High-entropy domains
- Long DNS queries
- TXT record abuse
- DNS queries from PowerShell
- DNS queries from Office applications
- Excessive NXDOMAIN responses
- Rare domains contacted by multiple endpoints

The best detections combine DNS logs with endpoint telemetry.

---

# ❌ Common Mistakes

### Mistake 1

"DNS logs only help network teams."

False.

SOC analysts, DFIR investigators, malware analysts, and threat hunters all rely heavily on DNS logs.

---

### Mistake 2

"If HTTPS is encrypted, DNS logs are useless."

False.

Even when web traffic is encrypted,

DNS often reveals **where** the endpoint intended to connect.

---

### Mistake 3

"Only malicious domains appear in DNS logs."

False.

The vast majority of DNS traffic is legitimate.

The analyst's job is to identify the small percentage that is suspicious.

---

# 🎯 MITRE ATT&CK Mapping

DNS logging is a defensive capability rather than an attack technique.

However, it is invaluable for detecting techniques such as:

| Activity | Technique |
|-----------|-----------|
| DNS Command & Control | T1071.004 |
| DNS Tunneling | T1071.004 / T1048 |
| Dynamic Resolution | T1568 |
| Phishing Infrastructure | Context Dependent |
| Malware Beaconing | T1071 |

---

# 🧪 SOC Investigation Challenge

## Alert

```
Host

↓

ACCOUNTS-PC-08

↓

powershell.exe

↓

DNS Query

↓

update-microsoft365-login[.]xyz

↓

Resolved

↓

HTTPS Connection

↓

Unknown Executable Downloaded
```

### Investigation Questions

1. Which process performed the DNS lookup?
2. Is the domain newly registered?
3. Has any other endpoint queried the same domain?
4. What was downloaded?
5. Did execution occur?
6. Was persistence established?
7. Which logs would you correlate?
8. Does Threat Intelligence flag the domain?
9. Which MITRE ATT&CK techniques apply?
10. Should the endpoint be isolated?

---

# ⚡ Quick Revision

✅ DNS logs record domain resolution activity.

✅ They often provide the earliest evidence of an attack.

✅ DNS logs become significantly more powerful when combined with Sysmon, Zeek, firewall logs, EDR, and Threat Intelligence.

✅ Understanding DNS telemetry is a core skill for every SOC analyst.

# 🔗 Knowledge Connections

DNS Logging
      │
      ├── DNS
      ├── Sysmon Event ID 22
      ├── Zeek
      ├── Splunk
      ├── Sigma
      ├── Threat Hunting
      ├── Malware Analysis
      ├── Incident Response
      ├── EDR
      ├── Firewall Logs
      └── MITRE ATT&CK
---

# 🎯 Threat Hunting with DNS - Hunting Attackers Before They Strike

> **"Alerts tell you what is already suspicious. Threat hunting is about finding what no one has detected yet."**

---

# 🎯 Purpose

Until now, we've learned:

- How DNS works.
- How attackers abuse DNS.
- What DNS logs contain.

Now it's time to answer the most important question:

> **How does a SOC analyst use DNS to proactively hunt for threats?**

Threat hunting is different from incident response.

In incident response:

```
Alert

↓

Investigation

↓

Response
```

In threat hunting:

```
No Alert

↓

Analyst Searches For Suspicious Activity

↓

Potential Threat Discovered
```

The goal is to identify malicious behavior before automated security tools generate an alert.

---

# 💡 Real World Analogy

Imagine you're a detective.

Waiting for someone to report a crime is **reactive**.

Walking through a neighborhood, identifying suspicious behavior before a crime occurs is **proactive**.

Threat hunting follows the same principle.

Instead of waiting for alerts,

SOC analysts actively search through logs looking for hidden attacker activity.

---

# 🌍 Why DNS is Excellent for Threat Hunting

Almost every cyber attack needs DNS.

Examples include:

```
Phishing Website

↓

DNS Lookup
```

---

```
Malware Download

↓

DNS Lookup
```

---

```
Command & Control

↓

DNS Lookup
```

---

```
Data Exfiltration

↓

DNS Lookup
```

---

```
Cloud Service Abuse

↓

DNS Lookup
```

If an attacker needs to communicate,

DNS is often involved.

---

# 🔍 What Can You Hunt For?

DNS logs can reveal:

- Phishing domains
- Malware downloads
- Command & Control servers
- DNS Tunneling
- Beaconing
- Newly Registered Domains (NRDs)
- Domain Generation Algorithms (DGA)
- Fast Flux infrastructure
- Rare domains
- Unusual TXT record usage
- Excessive NXDOMAIN responses

Each of these can become a hunting hypothesis.

---

# 🏢 Enterprise Example

Imagine an organization with:

```
2,000 Employees

↓

350,000 DNS Queries Per Day
```

Most queries are expected:

```
microsoft.com

google.com

github.com

office.com
```

One endpoint, however, repeatedly queries:

```
kd82js91-update-service.xyz
```

No alert exists.

But a threat hunter notices:

- The domain has never been seen before.
- Only one endpoint communicates with it.
- The domain is newly registered.
- Queries occur every 45 seconds.

This becomes the starting point of an investigation.

---

# 🔴 Red Team Perspective

Attackers attempt to blend into normal DNS traffic by:

- Registering domains that resemble trusted brands.
- Using cloud-hosted infrastructure.
- Rotating domains frequently.
- Randomizing beacon intervals.
- Encrypting subsequent traffic.
- Using DNS over HTTPS (DoH).

Their objective is to avoid standing out in DNS telemetry.

---

# 🔵 Blue Team Perspective

A DNS hunter asks questions such as:

- Which domains are completely new?
- Which endpoints contact rare domains?
- Which domains generate repeated TXT requests?
- Which systems produce excessive NXDOMAIN responses?
- Which endpoints beacon at regular intervals?
- Which users contact known malicious infrastructure?

Rather than focusing on a single event, the hunter looks for behavioral patterns.

---

# 💼 Hunting Scenario

Imagine you review DNS logs for the last 24 hours.

You discover:

```
Host

↓

HR-LAPTOP-07

↓

Queried Domain

↓

microsoft-login-security-update.xyz

↓

Query Count

184

↓

First Seen

Today

↓

Domain Age

1 Day
```

No security alert has fired.

However, several characteristics are suspicious:

- Newly registered domain.
- Brand impersonation.
- High query frequency.
- Single affected endpoint.

This is enough to begin a deeper investigation.

---

# 🛡️ Hunting Hypothesis

A threat hunt usually begins with a hypothesis.

Example:

> "Attackers often use newly registered domains for phishing and malware delivery."

The analyst then searches DNS logs to validate or reject that hypothesis.

Good threat hunting is driven by questions—not assumptions.

---

# ❌ Common Mistakes

### Mistake 1

"Threat hunting only starts after an alert."

False.

Threat hunting is proactive.

Its purpose is to discover attacks before automated detections do.

---

### Mistake 2

"If a domain isn't on a blocklist, it's safe."

False.

Many malicious domains are used within hours of registration—before they appear in threat intelligence feeds.

---

### Mistake 3

"One unusual DNS query confirms malware."

False.

A single event rarely proves compromise.

Always correlate with:

- Endpoint telemetry.
- Process execution.
- Firewall logs.
- Proxy logs.
- Threat intelligence.
- User activity.

---

# 🎯 MITRE ATT&CK Mapping

DNS hunting can help identify techniques including:

| Activity | Technique |
|-----------|-----------|
| DNS Command & Control | T1071.004 |
| Dynamic Resolution | T1568 |
| Phishing Infrastructure | T1583 |
| Data Exfiltration | T1048 |
| Active Scanning | T1595 |

---

# 🧪 SOC Investigation Challenge

## Scenario

Your organization generated:

```
410,000 DNS Queries

↓

Last 24 Hours
```

You notice one endpoint contacting:

```
update-office365-secure.xyz
```

Every 50 seconds.

No alert exists.

### Investigation Questions

1. Is the domain newly registered?
2. Which process initiated the DNS requests?
3. Has any other endpoint contacted the same domain?
4. Does the endpoint show signs of beaconing?
5. Is the domain associated with known threat intelligence?
6. Are HTTPS connections occurring after the DNS lookups?
7. Is this normal application behavior?
8. Which additional telemetry would you collect?

---

# ⚡ Quick Revision

✅ Threat hunting is proactive, not reactive.

✅ DNS is one of the richest data sources for threat hunting.

✅ Look for patterns rather than isolated events.

✅ Always validate findings using multiple telemetry sources.

# 🔗 Knowledge Connections

Threat Hunting
      │
      ├── DNS Logs
      ├── Sysmon
      ├── Zeek
      ├── EDR
      ├── Threat Intelligence
      ├── Beaconing
      ├── DNS Tunneling
      ├── Malware
      ├── Splunk
      └── MITRE ATT&CK
---

# 🔎 Hunting Newly Registered Domains (NRDs) - Catching Attacks in Their Early Stages

> **"Many cyber attacks begin with a brand-new domain that has never been seen before."**

---

# 🎯 Purpose

One of the simplest and most effective threat hunting techniques is identifying **Newly Registered Domains (NRDs).**

Attackers frequently register new domains because:

- Their old infrastructure has been blocked.
- They need fresh phishing websites.
- They need a new Command & Control (C2) server.
- They want to evade reputation-based security products.

Since these domains are new,

they often have:

- No reputation.
- Very little historical activity.
- Few detections from antivirus vendors.

For this reason, NRDs deserve extra attention during investigations.

---

# 💡 Real World Analogy

Imagine someone opens a brand-new bank account today.

Within one hour,

millions of rupees are transferred through it.

Wouldn't that seem suspicious?

The account itself isn't malicious,

but because it has **no history**, it deserves investigation.

Newly registered domains are similar.

They aren't automatically malicious,

but attackers often abuse them before defenders have time to classify them.

---

# 🌍 What is a Newly Registered Domain?

Example:

```
microsoft-support-login.xyz
```

Registered:

```
Today
```

First DNS Query:

```
15 Minutes Later
```

Employees begin visiting it.

This should immediately attract a SOC analyst's attention.

---

# 🤔 Why Attackers Prefer NRDs

Attackers frequently lose infrastructure because defenders:

- Block malicious domains.
- Share Indicators of Compromise (IOCs).
- Update threat intelligence feeds.
- Suspend phishing websites.

To continue operating,

they simply register another domain.

Example:

```
login-company-support.xyz

↓

Blocked
```

↓

```
secure-company-login.xyz

↓

Blocked
```

↓

```
company-authentication-help.xyz

↓

Campaign Continues
```

This constant rotation makes NRDs a common indicator during phishing campaigns.

---

# 🦠 Real Malware Example

Imagine a threat actor launches a ransomware campaign.

Step 1

```
Register Domain

↓

files-update-secure.xyz
```

Step 2

```
Configure DNS

↓

Create HTTPS Website
```

Step 3

```
Send Phishing Emails
```

Step 4

```
Victim Clicks Link
```

Step 5

```
DNS Query

↓

Website Opens

↓

Malware Download
```

The domain may only be **two hours old**.

---

# 🔴 Red Team Perspective

Attackers choose NRDs because they:

- Avoid existing blocklists.
- Blend into Internet traffic.
- Replace infrastructure quickly.
- Launch short-lived phishing campaigns.
- Host malware for only a few hours.

Many domains are abandoned before defenders even finish their investigation.

---

# 🔵 Blue Team Perspective

Not every NRD is malicious.

Many legitimate businesses register new domains every day.

Instead of blocking every NRD,

analysts should investigate factors such as:

- Domain age.
- WHOIS information.
- Registrar reputation.
- Hosting provider.
- SSL certificate details.
- Number of affected endpoints.
- Associated IP reputation.
- Threat intelligence results.

Always use multiple indicators before making a conclusion.

---

# 💼 Real SOC Investigation

## Alert

DNS Logs

```
Client

↓

HR-LAPTOP-12

↓

Query

↓

office365-security-update.xyz

↓

First Seen

Today

↓

Domain Age

3 Hours
```

Threat Intelligence

```
No Reputation Available
```

WHOIS

```
Registered

Today
```

EDR

```
chrome.exe

↓

Downloaded

invoice.pdf.exe
```

The analyst determines that the DNS request led to a phishing website distributing malware.

---

# 🛡️ Detection Engineering

Create detections for:

- Domains registered within the last 30 days.
- Brand impersonation domains.
- Newly observed domains contacted by multiple endpoints.
- NRDs followed by executable downloads.
- NRDs contacted outside business hours.
- NRDs resolving to infrastructure with poor reputation.

These detections should be enriched with threat intelligence whenever possible.

---

# 🚨 Detection Opportunities

Investigate when you observe:

- A domain first seen in your environment.
- Multiple users contacting the same new domain.
- New domains combined with PowerShell activity.
- NRDs followed by Office macro execution.
- Downloads immediately after DNS resolution.

The combination of events is often more important than the domain age alone.

---

# ❌ Common Mistakes

### Mistake 1

"Every newly registered domain is malicious."

False.

Many are completely legitimate.

Treat NRDs as a signal for investigation—not automatic evidence of compromise.

---

### Mistake 2

"If VirusTotal shows no detections, the domain is safe."

False.

Brand-new domains often have no detections because they haven't been analyzed yet.

---

### Mistake 3

"I should block every NRD."

False.

Doing so would generate many false positives.

Use additional context before taking action.

---

# 🎯 MITRE ATT&CK Mapping

| Activity | Technique |
|-----------|-----------|
| Phishing Infrastructure | T1583 |
| Command & Control | T1071 |
| Malware Delivery | Context Dependent |
| Resource Development | T1583 |

---

# 🧪 SOC Investigation Challenge

## Scenario

DNS Logs show:

```
Host

↓

FINANCE-PC-04

↓

Query

↓

secure-payroll-portal.xyz

↓

First Seen

Today

↓

Domain Age

6 Hours

↓

HTTPS Connection

↓

Executable Download
```

### Investigation Questions

1. Who registered the domain?
2. Which user visited it?
3. Was the download executed?
4. Are other endpoints contacting the domain?
5. Does the SSL certificate appear suspicious?
6. Is the hosting provider associated with previous abuse?
7. What additional telemetry should be reviewed?
8. Should the domain be blocked immediately?

---

# ⚡ Quick Revision

✅ Newly Registered Domains (NRDs) are frequently used in phishing, malware delivery, and C2 infrastructure.

✅ A new domain is **not automatically malicious**, but it deserves additional scrutiny.

✅ Combine DNS logs with WHOIS, Threat Intelligence, EDR, proxy logs, and user activity.

✅ Context is more important than domain age alone.


# 🔗 Knowledge Connections
Newly Registered Domains
            │
            ├── DNS Logs
            ├── WHOIS
            ├── Threat Intelligence
            ├── Phishing
            ├── Malware
            ├── Command & Control
            ├── EDR
            ├── Proxy Logs
            ├── Threat Hunting
            └── MITRE ATT&CK

---

# 🎲 Domain Generation Algorithms (DGA) - Malware That Creates Its Own Domains

> **"If defenders block one malicious domain, attackers simply generate thousands of new ones."**

---

# 🎯 Purpose

Many malware families rely on a **Command & Control (C2)** server to receive instructions.

In the early days, malware contacted a single hardcoded domain.

Example:

```
malware-control.com
```

If defenders blocked or seized that domain,

the malware lost communication with its operator.

To solve this problem, attackers developed **Domain Generation Algorithms (DGAs).**

Instead of relying on one domain,

the malware automatically generates hundreds or even thousands of domain names every day.

The attacker only needs to register **one** of those generated domains.

The malware eventually finds it and reconnects to the C2 server.

---

# 💡 Real World Analogy

Imagine you and a friend agree to meet every day,

but you don't know the exact location.

Instead,

both of you have the same secret notebook that lists:

```
Day 1 → Coffee Shop A

Day 2 → Park B

Day 3 → Library C

Day 4 → Restaurant D
```

Neither of you needs to communicate beforehand.

As long as you both have the same notebook,

you'll meet successfully.

A DGA works the same way.

The attacker and the malware use the same algorithm to independently generate identical domain names.

---

# 🌍 How DGA Works

Step 1

The malware uses inputs such as:

- Current date
- Current time
- Seed value
- Secret key
- Mathematical algorithm

↓

Step 2

It generates a list of possible domains.

Example:

```
abjskdqw.xyz

↓

kjdhwuep.biz

↓

pqowmxla.net

↓

jdiwmske.org

↓

...
```

↓

Step 3

The malware queries each domain through DNS.

↓

Step 4

Only one of those domains has been registered by the attacker.

↓

Step 5

The malware successfully establishes communication with the C2 server.

---

# 🏢 Enterprise Example

A workstation becomes infected.

Every morning at 9:00 AM,

it generates 500 domain names.

DNS logs show:

```
hskd82kd.com

↓

mxpq912.org

↓

ajqkw921.net

↓

pqozk212.xyz

↓

...
```

Eventually,

one domain resolves successfully.

The malware downloads new instructions.

---

# 🤔 Why Attackers Use DGA

DGAs provide several advantages:

- Eliminate reliance on a single domain.
- Make infrastructure takedowns more difficult.
- Reduce dependence on fixed IP addresses.
- Increase malware resilience.
- Complicate threat intelligence efforts.

Even if defenders block 499 domains,

the attacker only needs one working domain.

---

# 🦠 Real Malware Examples

Several well-known malware families have used DGAs, including:

| Malware | DGA Usage |
|----------|-----------|
| Conficker | Generated hundreds of domains daily |
| Locky | Used DGA for ransomware infrastructure |
| Kraken | Rotated C2 domains dynamically |
| Matsnu | Large-scale DGA implementation |
| Ramnit | Frequently changed C2 domains |

Each family implemented its own algorithm,

but the objective remained the same:

**Maintain reliable communication with the attacker.**

---

# 🔴 Red Team Perspective

Although many penetration testing frameworks do not implement DGAs by default,

real threat actors value them because they:

- Survive domain takedowns.
- Bypass static blocklists.
- Make attribution harder.
- Allow rapid infrastructure rotation.

Modern malware often combines DGAs with HTTPS and encrypted C2 channels.

---

# 🔵 Blue Team Perspective

SOC analysts should watch for:

- Large numbers of failed DNS queries.
- Random-looking domain names.
- High domain entropy.
- Domains with no browsing history.
- Repeated NXDOMAIN responses.
- Hundreds of unique domains queried by one endpoint.

These behaviors often indicate automated domain generation rather than normal user activity.

---

# 💼 Real SOC Investigation

## Alert

DNS monitoring identifies:

```
Endpoint

↓

ENG-LAPTOP-09

↓

1,240 DNS Queries

↓

Within 15 Minutes

↓

1,215 Returned NXDOMAIN
```

Most queried domains appear random:

```
jqpwke91.net

↓

mxqaz812.org

↓

pkdwls92.xyz
```

The remaining 25 domains resolve successfully.

Threat intelligence identifies one of them as malware infrastructure.

The endpoint is isolated before additional payloads are delivered.

---

# 🛡️ Detection Engineering

Create detections for:

- Excessive NXDOMAIN responses.
- High-entropy domain names.
- Hundreds of unique domains queried by one host.
- Short-lived domains with random naming patterns.
- Automated DNS requests at high frequency.
- Rare domains queried immediately after PowerShell execution.

Combining DNS telemetry with endpoint activity significantly improves detection accuracy.

---

# 🚨 Detection Opportunities

Investigate systems that show:

- Unusually high DNS query volume.
- Sequential random-looking domains.
- Queries with no corresponding user browsing activity.
- PowerShell or scripts immediately followed by DNS bursts.
- DGA patterns occurring at regular intervals.

Behavioral analysis is often more effective than relying solely on threat intelligence.

---

# ❌ Common Mistakes

### Mistake 1

"Random-looking domains are always malicious."

False.

Some legitimate services generate unique subdomains or tracking identifiers.

Context is essential.

---

### Mistake 2

"If one DGA domain is blocked, the malware is stopped."

False.

The malware simply generates additional domains.

The underlying infection must still be remediated.

---

### Mistake 3

"Threat intelligence alone is enough."

False.

Many DGA domains are generated faster than threat intelligence feeds can classify them.

Behavioral detection remains critical.

---

# 🎯 MITRE ATT&CK Mapping

| Activity | Technique |
|-----------|-----------|
| Dynamic Resolution | **T1568.002 – Domain Generation Algorithms** |
| Command & Control | **T1071.004 – DNS** |
| Encrypted C2 | **T1573** |

---

# 🧪 SOC Investigation Challenge

## Scenario

DNS logs reveal:

```
Host

↓

DEV-PC-22

↓

2,500 DNS Queries

↓

2,430 NXDOMAIN Responses

↓

Several Random Domains

↓

One Successful Resolution

↓

HTTPS Connection Established
```

### Investigation Questions

1. Does the domain naming pattern resemble a DGA?
2. Which process generated the DNS queries?
3. Is there evidence of malware execution?
4. Was a payload downloaded after the successful resolution?
5. Do other endpoints show the same pattern?
6. Is the successful domain present in threat intelligence?
7. Which logs should be correlated next?
8. Should the endpoint be isolated?

---

# ⚡ Quick Revision

✅ DGAs allow malware to generate thousands of potential domains automatically.

✅ The attacker only needs to register one generated domain.

✅ Large numbers of NXDOMAIN responses and random-looking domains are common indicators.

✅ Behavioral detection is more effective than relying only on domain reputation.

# 🔗 Knowledge Connections

Domain Generation Algorithms
             │
             ├── DNS
             ├── Command & Control
             ├── Malware
             ├── Threat Hunting
             ├── NXDOMAIN
             ├── Threat Intelligence
             ├── Beaconing
             ├── Sysmon
             ├── Splunk
             └── MITRE ATT&CK
---

# 🌪️ Fast Flux DNS - One Domain, Hundreds of IP Addresses

> **"Instead of changing the domain, attackers constantly change the IP addresses behind it."**

---

# 🎯 Purpose

One of the biggest challenges for defenders is blocking malicious infrastructure.

Imagine you discover:

```
evil-update.com

↓

185.24.10.55
```

You block the IP.

Problem solved?

Not necessarily.

Within minutes the attacker changes the IP address.

Now the same domain points to:

```
evil-update.com

↓

91.214.18.99
```

A few minutes later...

```
evil-update.com

↓

45.66.102.14
```

The domain never changes.

Only the IP addresses do.

This technique is known as **Fast Flux**.

---

# 💡 Real World Analogy

Imagine police are trying to arrest a criminal.

Every five minutes,

the criminal moves to a different house.

The criminal's phone number stays the same,

but the address keeps changing.

Eventually,

finding and blocking the criminal becomes much more difficult.

Fast Flux works exactly like this.

The domain remains constant,

while its IP address changes repeatedly.

---

# 🌍 How Fast Flux Works

```
Victim

↓

DNS Query

↓

evil-update.com

↓

DNS Response

↓

185.24.10.55
```

Five minutes later...

```
Victim

↓

DNS Query

↓

evil-update.com

↓

DNS Response

↓

91.214.18.99
```

Five minutes later...

```
Victim

↓

DNS Query

↓

evil-update.com

↓

DNS Response

↓

45.66.102.14
```

The attacker can rotate dozens or even hundreds of IP addresses in a single day.

---

# 🏢 Why Attackers Use Fast Flux

Fast Flux makes malicious infrastructure more resilient.

Benefits include:

- Harder to block.
- Harder to take down.
- Increased availability.
- Better resistance against blacklists.
- Difficult attribution.

Even if defenders block several IPs,

new ones quickly replace them.

---

# 🦠 Real Malware Example

Many botnets use infected computers as temporary servers.

```
Attacker

↓

Botnet

↓

Thousands of Infected PCs

↓

DNS Rotates Between Them
```

When defenders block one infected machine,

another infected computer immediately replaces it.

The malicious service remains online.

---

# 🔴 Red Team Perspective

Threat actors use Fast Flux to protect:

- Phishing websites.
- Malware download servers.
- Command & Control infrastructure.
- Botnets.
- Credential harvesting sites.

Instead of relying on one server,

they distribute services across many compromised hosts.

---

# 🔵 Blue Team Perspective

SOC analysts should investigate domains that:

- Resolve to many IP addresses.
- Frequently change A records.
- Have unusually short TTL values.
- Use geographically scattered infrastructure.
- Appear across multiple autonomous systems (ASNs).

Rapid infrastructure changes often indicate malicious intent.

---

# 💼 Real SOC Investigation

## Alert

Threat Intelligence identifies:

```
secure-login-update.com
```

During investigation,

DNS responses show:

```
09:00

↓

185.12.10.44
```

```
09:10

↓

91.66.201.7
```

```
09:20

↓

45.90.112.18
```

```
09:30

↓

103.25.44.77
```

Within only thirty minutes,

the same domain has resolved to four different IP addresses.

Further investigation reveals dozens more IPs across multiple countries.

The domain is confirmed to be part of a phishing campaign.

---

# ⏳ TTL - Time To Live

One characteristic of Fast Flux is a **very low TTL (Time To Live).**

TTL tells devices how long they should cache a DNS response.

Example:

```
TTL

↓

30 Seconds
```

After 30 seconds,

the client asks DNS again.

The attacker now returns a completely different IP address.

Short TTL values make IP rotation much faster.

---

# 📡 Typical DNS Pattern

```
DNS Query

↓

Domain

↓

IP #1

↓

30 Seconds Later

↓

DNS Query

↓

IP #2

↓

30 Seconds Later

↓

DNS Query

↓

IP #3
```

Repeated IP changes are a strong indicator that something unusual is happening.

---

# 🛡️ Detection Engineering

Look for:

- One domain resolving to many IP addresses.
- Frequent A record changes.
- Low TTL values.
- IP addresses spread across multiple countries.
- Infrastructure hosted on residential networks.
- Domains associated with botnet activity.

Fast Flux is easier to detect when DNS logs are combined with passive DNS and threat intelligence.

---

# 🚨 Detection Opportunities

Investigate when:

- A domain resolves to dozens of different IPs.
- IP addresses change every few minutes.
- The domain has a TTL of only a few seconds or minutes.
- DNS activity is followed by suspicious HTTPS sessions.
- The domain has recently appeared in phishing reports.

---

# ❌ Common Mistakes

### Mistake 1

"Multiple IP addresses always indicate Fast Flux."

False.

Large services such as Microsoft, Google, and Cloudflare also use multiple IP addresses for redundancy and load balancing.

The difference is **how frequently** those IPs change and the overall behavior.

---

### Mistake 2

"Blocking one IP stops the attack."

False.

Fast Flux is specifically designed to defeat IP-based blocking.

Blocking should focus on the domain, infrastructure, or malicious activity—not just a single IP.

---

### Mistake 3

"Low TTL always means malicious."

False.

Content Delivery Networks (CDNs) and cloud providers may legitimately use short TTL values.

Always consider context before drawing conclusions.

---

# 🎯 MITRE ATT&CK Mapping

| Activity | Technique |
|-----------|-----------|
| Dynamic Resolution | **T1568** |
| DNS Command & Control | **T1071.004** |
| Resource Development | **T1583** |

---

# 🧪 SOC Investigation Challenge

## Scenario

A suspicious domain is observed.

```
cdn-security-check.com

↓

09:00

↓

185.24.10.44
```

```
09:05

↓

103.17.91.18
```

```
09:10

↓

91.44.18.73
```

```
TTL

↓

20 Seconds
```

### Investigation Questions

1. Is this Fast Flux or legitimate load balancing?
2. How many unique IP addresses has the domain used?
3. Are the IPs associated with known malicious activity?
4. Which endpoints contacted the domain?
5. Are HTTPS sessions occurring immediately after DNS resolution?
6. Does threat intelligence identify the domain?
7. Is the infrastructure spread across multiple countries?
8. What containment actions should be taken?

---

# ⚡ Quick Revision

✅ Fast Flux keeps the domain name constant while rapidly changing IP addresses.

✅ It is commonly used by phishing campaigns, botnets, and Command & Control infrastructure.

✅ Low TTL values and frequent A record changes are key indicators.

✅ Correlate DNS telemetry with passive DNS, firewall logs, EDR, and threat intelligence before making conclusions.

# 🔗 Knowledge Connections

Fast Flux
      │
      ├── DNS
      ├── TTL
      ├── A Records
      ├── Command & Control
      ├── Botnets
      ├── Phishing
      ├── Threat Hunting
      ├── Threat Intelligence
      ├── Passive DNS
      └── MITRE ATT&CK

---

# 🕵️ Passive DNS (pDNS) - Looking Into a Domain's Past

> **"A domain may change today, but Passive DNS remembers where it has been."**

---

# 🎯 Purpose

When analysts investigate a suspicious domain,

one of the first questions they ask is:

> **"Has this domain been seen before?"**

Normal DNS only tells us the **current** answer.

Example:

```
malicious-domain.com

↓

203.0.113.25
```

But what if yesterday it pointed to:

```
91.12.55.80
```

Or last month it pointed to:

```
185.44.19.200
```

Normal DNS cannot answer those questions.

Passive DNS can.

---

# 💡 Real World Analogy

Imagine you're investigating a suspect.

Knowing **where they live today** is useful.

But knowing:

- Where they lived last year
- Who lived with them
- Which vehicles they used
- Who visited them

provides a much clearer picture.

Passive DNS works the same way.

It records the historical relationships between domains and IP addresses.

---

# 🌍 What is Passive DNS?

Passive DNS (pDNS) is a database of historical DNS observations.

Instead of asking:

```
"What is the IP now?"
```

you ask:

```
"What IPs has this domain ever used?"
```

or

```
"What domains have used this IP?"
```

This historical data helps analysts uncover attacker infrastructure.

---

# 📚 What Information Can Passive DNS Provide?

Depending on the data source, Passive DNS may reveal:

- Historical IP addresses
- Previous domain names
- First seen date
- Last seen date
- Record types
- Name servers
- TTL history
- Related domains
- Shared infrastructure

This information is extremely valuable during threat hunting and incident response.

---

# 🏢 Enterprise Example

Your firewall blocks traffic to:

```
secure-login-update.xyz
```

Threat intelligence identifies it as suspicious.

Passive DNS reveals:

```
Last Month

↓

185.24.18.55
```

↓

```
Two Weeks Ago

↓

91.204.18.22
```

↓

```
Yesterday

↓

45.77.199.88
```

You now know the attacker has changed infrastructure multiple times.

---

# 🔍 Reverse Passive DNS

Passive DNS can also work in reverse.

Instead of asking:

```
Domain

↓

IP
```

you ask:

```
IP

↓

All Domains Hosted
```

Example:

```
185.24.18.55

↓

login-update.xyz

↓

office-auth.xyz

↓

microsoft-secure.xyz

↓

vpn-company-login.xyz
```

Suddenly,

you discover several phishing domains hosted on the same infrastructure.

---

# 🔴 Red Team Perspective

Threat actors frequently:

- Change IP addresses.
- Register new domains.
- Move to different hosting providers.
- Rotate DNS records.

However,

historical Passive DNS data often preserves these relationships,

making infrastructure analysis much easier for defenders.

---

# 🔵 Blue Team Perspective

SOC analysts use Passive DNS to:

- Investigate phishing domains.
- Track attacker infrastructure.
- Discover related malicious domains.
- Build indicators of compromise (IOCs).
- Enrich alerts.
- Correlate incidents across campaigns.

Passive DNS often transforms one suspicious domain into an entire malicious infrastructure map.

---

# 💼 Real SOC Investigation

## Alert

Firewall blocks:

```
invoice-storage-download.xyz
```

Threat Intelligence

↓

Unknown

No previous detections.

Passive DNS reveals:

```
Current IP

↓

91.22.44.18
```

Reverse lookup shows:

```
office365-security.xyz

↓

vpn-login-update.xyz

↓

microsoft-authentication.xyz

↓

sharepoint-secure.xyz
```

All four domains share the same infrastructure.

The investigation expands from one suspicious domain to an entire phishing campaign.

---

# 🛡️ Detection Engineering

Passive DNS is commonly used to enrich alerts.

Example workflow:

```
DNS Alert

↓

Threat Intelligence

↓

Passive DNS

↓

WHOIS

↓

SSL Certificate Analysis

↓

Risk Score

↓

SOC Investigation
```

The additional context helps analysts prioritize investigations.

---

# 🧰 Common Passive DNS Platforms

Analysts often use:

- VirusTotal
- SecurityTrails
- RiskIQ / Microsoft Defender TI
- Recorded Future
- Cisco Umbrella Investigate
- Farsight DNSDB
- AlienVault OTX

Each platform provides different historical datasets and enrichment capabilities.

---

# 🚨 Detection Opportunities

Use Passive DNS to identify:

- Domains sharing malicious infrastructure.
- Fast Flux activity.
- Infrastructure reused across campaigns.
- Domains rapidly changing IP addresses.
- Multiple phishing domains on the same server.
- Historical connections to known malware.

---

# ❌ Common Mistakes

### Mistake 1

"Passive DNS replaces normal DNS."

False.

Normal DNS provides the **current** answer.

Passive DNS provides the **historical** answers.

Both are useful.

---

### Mistake 2

"If two domains share an IP, both are malicious."

False.

Shared hosting environments may host thousands of unrelated websites.

Always investigate further before drawing conclusions.

---

### Mistake 3

"No Passive DNS history means the domain is safe."

False.

A newly registered domain may simply have no historical records yet.

---

# 🎯 MITRE ATT&CK Mapping

Passive DNS itself is an investigation technique.

It helps analysts investigate activity associated with:

| Activity | Technique |
|-----------|-----------|
| Infrastructure Discovery | T1590 |
| Resource Development | T1583 |
| DNS Command & Control | T1071.004 |
| Dynamic Resolution | T1568 |

---

# 🧪 SOC Investigation Challenge

## Scenario

EDR generates an alert.

```
Host

↓

FINANCE-PC-09

↓

Queried

↓

secure-payroll-check.xyz
```

Threat Intelligence

↓

No detections.

Passive DNS reveals:

```
Current IP

↓

91.22.44.18
```

Historical Records:

```
185.33.18.7

↓

103.18.44.2

↓

91.22.44.18
```

Reverse Passive DNS shows:

```
vpn-office-login.xyz

↓

microsoft-auth-check.xyz

↓

onedrive-secure.xyz
```

### Investigation Questions

1. How many domains share this infrastructure?
2. Has the IP been associated with previous attacks?
3. Are any domains impersonating trusted brands?
4. Which endpoints contacted these domains?
5. Are additional IOCs available?
6. Should these domains be blocked proactively?
7. Does this indicate a larger phishing campaign?
8. Which logs should be correlated next?

---

# ⚡ Quick Revision

✅ Passive DNS stores historical DNS observations.

✅ It helps analysts investigate the history of domains and IP addresses.

✅ Reverse Passive DNS identifies other domains hosted on the same infrastructure.

✅ Passive DNS is invaluable during phishing, malware, and C2 investigations.

# 🔗 Knowledge Connections

Passive DNS
      │
      ├── DNS
      ├── Threat Intelligence
      ├── WHOIS
      ├── Fast Flux
      ├── DGA
      ├── Phishing
      ├── Command & Control
      ├── IOC Enrichment
      ├── Incident Response
      └── MITRE ATT&CK
