# 🌐 HTTP & HTTPS - The Complete SOC Analyst Guide

> *"Every web request leaves a trail. Understanding HTTP and HTTPS allows SOC analysts to reconstruct that trail, detect malicious activity, and investigate cyber attacks."*

---

# 📖 Introduction

The **HyperText Transfer Protocol (HTTP)** is the foundation of communication on the World Wide Web. Every time you open a website, click a link, submit a login form, download a file, or interact with a web application, HTTP is working behind the scenes.

Its secure counterpart, **HyperText Transfer Protocol Secure (HTTPS)**, adds encryption and integrity protection through **Transport Layer Security (TLS)**. Today, almost all modern websites use HTTPS to protect user data during transmission.

For cybersecurity professionals, HTTP and HTTPS are among the most important protocols to understand because a large percentage of modern cyber attacks either **use**, **abuse**, or **hide within** web traffic.

Whether you're investigating malware, phishing campaigns, web application attacks, data exfiltration, or command-and-control (C2) communication, you'll encounter HTTP or HTTPS almost every day.

---

# 🎯 Why Should a SOC Analyst Learn HTTP?

Imagine the following security alerts:

🚨 Suspicious PowerShell Download

🚨 Possible Data Exfiltration

🚨 Command & Control Communication

🚨 Web Shell Detected

🚨 SQL Injection Attempt

🚨 Malicious File Download

🚨 Phishing Website Access

Although these alerts appear different, they often have one thing in common:

**HTTP or HTTPS traffic.**

Understanding how web communication works allows a SOC analyst to determine:

- What happened?
- Who communicated?
- What resource was accessed?
- Was sensitive data transferred?
- Was malware downloaded?
- Was the communication legitimate?

Without understanding HTTP, investigating web-based incidents becomes significantly more difficult.

---

# 🌍 Why HTTP Exists

Before HTTP existed, sharing information across different computer systems was inconsistent and difficult.

Researchers needed a universal method for requesting and delivering documents over a network.

HTTP solved this problem by introducing a standardized way for:

- Clients to request information.
- Servers to process requests.
- Servers to return responses.

Today, HTTP is used for much more than websites.

Modern applications use HTTP or HTTPS for:

- Web Applications
- REST APIs
- Mobile Applications
- Cloud Platforms
- Software Updates
- IoT Devices
- Webhooks
- Authentication Services
- File Downloads
- SaaS Platforms

If an application communicates over the Internet, there's a good chance HTTP or HTTPS is involved.

---

# 🕰️ A Brief History of HTTP

### HTTP/0.9 (1991)

The first version of HTTP was extremely simple.

It only supported the **GET** method and could retrieve basic HTML documents.

There were:

- No headers
- No status codes
- No images
- No cookies
- No authentication

---

### HTTP/1.0 (1996)

HTTP/1.0 introduced major improvements:

- HTTP Headers
- Status Codes
- Multiple Content Types
- Better Error Handling

Each request required a new TCP connection, which made communication less efficient.

---

### HTTP/1.1 (1997)

HTTP/1.1 became the standard for many years.

New features included:

- Persistent Connections (Keep-Alive)
- Host Header
- Chunked Transfer Encoding
- Improved Caching
- Better Performance

Many environments still use HTTP/1.1 today.

---

### HTTP/2 (2015)

HTTP/2 focused on performance rather than changing how websites behaved.

Key improvements included:

- Multiplexing
- Header Compression
- Stream Prioritization
- Reduced Latency

Multiple requests could now share a single TCP connection.

---

### HTTP/3 (2022)

HTTP/3 introduced a significant architectural change.

Instead of using TCP, it runs over the **QUIC** protocol, which is built on UDP.

Benefits include:

- Faster Connection Setup
- Reduced Packet Loss Impact
- Better Mobile Performance
- Improved Reliability
- Lower Latency

Modern browsers increasingly prefer HTTP/3 when supported.

---

# 🌐 Where HTTP Fits in the OSI Model

HTTP operates at the **Application Layer (Layer 7)** of the OSI Model.

However, successful web communication depends on multiple layers working together.

| OSI Layer | Role During Web Browsing |
|-----------|--------------------------|
| Layer 7 | HTTP / HTTPS |
| Layer 6 | TLS Encryption |
| Layer 5 | Session Management |
| Layer 4 | TCP or QUIC |
| Layer 3 | IP Routing |
| Layer 2 | Ethernet / Wi-Fi |
| Layer 1 | Physical Transmission |

Understanding this relationship helps explain why investigating a web incident often requires evidence from multiple layers.

---

# 🔗 Connection to Previous Chapter

In **Chapter 01 – OSI Model**, we learned **where** protocols operate within the networking stack.

Now, we'll focus on **how HTTP and HTTPS actually communicate**, how attackers abuse them, and how defenders detect malicious web activity.

Think of it this way:

**OSI Model answers:**

> *"Where does HTTP operate?"*

This chapter answers:

> *"How does HTTP work, and how can attackers abuse it?"*

---

# 💡 Simple Analogy

Imagine you're sitting in a restaurant.

You don't walk into the kitchen to cook your own meal.

Instead:

1. You read the menu.
2. You place an order with the waiter.
3. The waiter delivers your request to the kitchen.
4. The chef prepares the food.
5. The waiter brings the food back to your table.

This is exactly how HTTP works.

- 🍽️ You = Client (Browser)
- 🧾 Waiter = HTTP Request
- 👨‍🍳 Kitchen = Web Server
- 🍔 Food = HTTP Response

The client asks for a resource, and the server responds with the requested information.

---

# 📌 Key Takeaways

- HTTP is the foundation of web communication.
- HTTPS secures HTTP using TLS encryption.
- Almost every modern web application relies on HTTP or HTTPS.
- Understanding HTTP is essential for SOC analysts investigating web-based attacks.
- The OSI Model explains where HTTP operates, while this chapter explains how it works.
- The next section explores the **Client–Server Architecture**, the communication model that powers every HTTP request.

---

# 🏗️ Client–Server Architecture

## 🎯 Purpose

HTTP follows a **Client–Server Architecture**, one of the most fundamental concepts in networking.

Instead of devices communicating randomly, every HTTP communication follows a structured model:

- A **Client** requests a resource.
- A **Server** processes the request.
- The **Server** sends a response.

This simple interaction powers nearly every website, web application, cloud platform, mobile application, and API on the Internet.

---

## 💡 Simple Analogy

Imagine visiting a library.

📚 You ask the librarian for a specific book.

The librarian searches the shelves.

If the book exists, they hand it to you.

If it doesn't exist, they tell you it isn't available.

HTTP works exactly the same way.

- 👤 You = Client
- 📚 Librarian = Web Server
- 📖 Book = Requested Resource
- 📝 Request = HTTP Request
- 📦 Book Returned = HTTP Response

---

# 🌐 What is a Client?

A **Client** is any system that requests information from another system.

Common clients include:

- Web Browsers (Chrome, Firefox, Edge)
- Mobile Applications
- Desktop Applications
- REST API Clients
- PowerShell
- curl
- Postman
- Python Scripts
- Malware
- Command & Control (C2) Beacons

Anything that initiates an HTTP request is acting as a client.

---

# 🖥️ What is a Server?

A **Server** is a system that receives requests, processes them, and returns responses.

Servers may host:

- Websites
- APIs
- Login Portals
- File Downloads
- Images
- Videos
- Cloud Applications
- Databases (through applications)

A server waits for incoming requests and responds based on the requested resource.

---

# 🔄 Basic Communication Flow

A typical HTTP communication follows this sequence:

```text
Client
   │
   │ HTTP Request
   ▼
Web Server
   │
   │ Process Request
   ▼
Application
   │
   │ Query Database (if needed)
   ▼
Database
   │
   │ Return Data
   ▼
Application
   │
   │ Generate HTTP Response
   ▼
Web Server
   │
   │ HTTP Response
   ▼
Client
```

The client never communicates directly with the database.

Instead, the request passes through multiple components before a response is generated.

---

# 🌍 Real-World Example

You type:

```
https://github.com
```

into your browser.

Behind the scenes, several steps occur in milliseconds:

1. The browser checks whether the domain is already cached.
2. If not, it performs a DNS lookup.
3. A TCP connection (or QUIC for HTTP/3) is established.
4. If HTTPS is used, a TLS handshake begins.
5. The browser sends an HTTP request.
6. The server processes the request.
7. The server returns an HTTP response.
8. The browser downloads HTML, CSS, JavaScript, images, and other resources.
9. The page is rendered on your screen.

What looks like "opening a website" is actually dozens (or even hundreds) of HTTP requests working together.

---

# 🧩 Components Behind a Modern Website

A browser rarely communicates with just one server.

Modern web applications often include:

```text
Browser
    │
    ▼
Content Delivery Network (CDN)
    │
    ▼
Web Application Firewall (WAF)
    │
    ▼
Load Balancer
    │
    ▼
Reverse Proxy
    │
    ▼
Web Server
    │
    ▼
Application Server
    │
    ▼
Database
```

Each component has a different role:

| Component | Purpose |
|-----------|---------|
| CDN | Delivers static content faster from locations closer to users |
| WAF | Filters and blocks malicious web requests |
| Load Balancer | Distributes traffic across multiple servers |
| Reverse Proxy | Protects backend servers and forwards requests |
| Web Server | Receives HTTP requests |
| Application Server | Executes business logic |
| Database | Stores and retrieves application data |

---

# 🔴 Red Team Perspective

Attackers don't always attack the web server directly.

Instead, they look for weaknesses throughout the communication path.

Possible targets include:

- Login Pages
- APIs
- Reverse Proxies
- Web Servers
- Load Balancers
- Authentication Services
- Session Management
- File Upload Features

If any component is misconfigured, it may become the entry point into the environment.

---

## 🚨 Common Attack Examples

- SQL Injection
- Cross-Site Scripting (XSS)
- Server-Side Request Forgery (SSRF)
- File Upload Abuse
- Directory Traversal
- Remote Code Execution (RCE)
- API Abuse
- Authentication Bypass
- Session Hijacking

These attacks all begin with a seemingly normal HTTP request.

---

# 🔵 Blue Team Perspective

A SOC analyst should understand the complete request path because evidence is spread across multiple systems.

For example:

| Component | Useful Logs |
|-----------|-------------|
| Browser | Browser History |
| Proxy | Proxy Logs |
| WAF | WAF Alerts |
| Firewall | Connection Logs |
| Web Server | Access Logs |
| Application | Application Logs |
| Database | Database Audit Logs |
| EDR | Process & Network Telemetry |

A single investigation often requires correlating logs from several of these sources.

---

# 💼 Real-World SOC Scenario

A Web Application Firewall generates an alert for repeated SQL Injection attempts against a customer portal.

The SOC analyst begins by reviewing:

- WAF alerts to identify the malicious payload.
- Web server access logs to determine which URLs were targeted.
- Reverse proxy logs to trace the original client IP.
- Firewall logs to review connection history.
- EDR telemetry from the web server to verify whether any suspicious processes started after the requests.

The investigation confirms that the WAF blocked the attack before it reached the application server, preventing a compromise.

This demonstrates why understanding the entire client–server path is essential for effective investigations.

---

# 🎯 Detection Opportunities

Monitor for:

- Unusual request rates
- Large numbers of failed requests
- Suspicious User-Agent values
- Requests from anonymous VPNs or Tor exit nodes
- Unexpected HTTP methods
- Access to sensitive endpoints
- Large outbound responses
- Requests containing known attack patterns

---

# 🎤 SOC Analyst Interview Questions

### What is the difference between a client and a server?

A client initiates requests for resources, while a server receives those requests, processes them, and returns responses.

---

### Why is understanding client–server architecture important for a SOC analyst?

Because web attacks leave evidence across multiple systems. Knowing how requests travel helps analysts identify where to collect logs, correlate events, and determine whether malicious activity reached the target application.

---

# 📌 Key Takeaways

- HTTP follows a Client–Server communication model.
- Modern web applications include multiple security and infrastructure components.
- Every HTTP request passes through several layers before reaching the application.
- Attackers target different points in the request path, not just the web server.
- SOC analysts investigate web incidents by correlating telemetry from proxies, WAFs, firewalls, servers, applications, databases, and EDR solutions.

---

## 🔗 Coming Next

Now that you understand **who communicates with whom**, the next section explores **How HTTP Works**, following a single web request from the moment you press **Enter** in your browser until the webpage appears on your screen.

---

# ⚙️ How HTTP Works – From Browser to Server

## 🎯 Purpose

At first glance, opening a website seems simple. You type a URL, press **Enter**, and within seconds the webpage appears.

Behind the scenes, however, dozens of networking components and protocols work together to make that happen.

Understanding this process is critical for SOC analysts because every step generates logs, network traffic, and telemetry that can be used during an investigation.

In this section, we'll follow a single HTTP request from the moment a user enters a URL into a browser until the webpage is displayed on the screen.

---

# 🌍 Real World Scenario

Let's assume an employee named **Alice** wants to access her company's employee portal.

She opens Google Chrome and types:

```

https://portal.company.com

```

Then presses **Enter**.

Everything that happens next occurs within milliseconds.

---

# 🔄 Complete Communication Flow

```

Alice
│
▼
Browser
│
▼
Browser Cache
│
▼
DNS Lookup
│
▼
TCP Three-Way Handshake
│
▼
TLS Handshake (HTTPS Only)
│
▼
HTTP Request
│
▼
Firewall
│
▼
Reverse Proxy / Load Balancer
│
▼
Web Server
│
▼
Application Server
│
▼
Database
│
▼
Application Server
│
▼
Web Server
│
▼
HTTP Response
│
▼
Browser
│
▼
Webpage Displayed

```

Although the user only sees a webpage loading, multiple systems have already processed the request.

---

# 🥇 Step 1 – Browser Checks Local Cache

Before sending any network traffic, the browser first checks whether it already has the required resources stored locally.

The browser may already have:

- HTML pages
- Images
- CSS files
- JavaScript files
- Fonts
- Cookies
- DNS records

If the required resource is still valid, it can be loaded directly from the cache, making the webpage load much faster.

---

## 💡 Why Cache Exists

Without caching, every image, logo, stylesheet, and script would need to be downloaded every time you visited a website.

Caching reduces:

- Network traffic
- Server load
- Internet bandwidth usage
- Page loading time

---

## 🔴 Red Team Perspective

Attackers sometimes abuse browser caching to:

- Hide malicious scripts
- Deliver outdated malicious content
- Store payloads locally
- Exploit cache poisoning vulnerabilities

One common attack is **Web Cache Poisoning**, where attackers manipulate cached responses so that multiple users receive malicious content.

---

## 🔵 Blue Team Perspective

While browser cache is generally a performance feature, investigators may examine it during forensic analysis to determine:

- Which websites were visited
- Whether malicious JavaScript was downloaded
- Whether phishing pages were cached
- Whether payloads were stored locally

---

## 💼 SOC Investigation Mindset

Imagine an employee claims:

> "I never visited that phishing website."

Before accepting that statement, consider:

- Is the phishing page present in the browser cache?
- Are there cached images from the malicious domain?
- Are cookies associated with that website?
- Is browser history consistent with cached content?

Browser cache can provide valuable evidence even if browsing history has been deleted.

---

# 🥈 Step 2 – DNS Resolution

The browser cannot communicate using domain names.

Computers communicate using **IP addresses**.

Before connecting to the server, the browser must determine the IP address associated with:

```

portal.company.com

```

This process is called **DNS Resolution**.

Example:

```

portal.company.com

↓

203.0.113.25

```

Only after obtaining the IP address can the browser continue.

---

## 🌍 Why DNS Is Required

Humans remember:

```

google.com

github.com

microsoft.com

```

Computers understand:

```

142.250.183.78

140.82.121.4

20.70.246.20

```

DNS acts like the Internet's phonebook by translating domain names into IP addresses.

---

## 🔴 Red Team Perspective

DNS is one of the most abused protocols in cybersecurity.

Attackers may use DNS for:

- Command and Control (C2)
- Data Exfiltration
- DNS Tunneling
- Malware Communication
- Domain Generation Algorithms (DGA)
- Fast Flux Infrastructure

Many malware families communicate through DNS before switching to HTTP or HTTPS.

---

## 🔵 Blue Team Perspective

SOC analysts should monitor DNS for:

- Newly registered domains
- Suspicious subdomains
- Long DNS queries
- High volumes of failed lookups
- Unusual geographic destinations
- Algorithmically generated domains

DNS often provides the first indication of malware activity.

---

## 💼 SOC Investigation Mindset

A workstation suddenly begins querying:

```

xj4k2l9-api-service-update.net

```

Questions to ask:

- Has this domain ever been contacted before?
- Is it newly registered?
- Which process initiated the query?
- Did an HTTP connection follow?
- Does threat intelligence flag the domain?

Investigating DNS early can prevent larger compromises.

---

# 🥉 Step 3 – TCP Connection

Once the destination IP address is known, the browser must establish a reliable connection with the server.

This is achieved using the **TCP Three-Way Handshake**.

The client and server exchange packets to confirm they are both ready to communicate reliably.

Without this handshake, HTTP communication cannot begin (except when using HTTP/3 over QUIC).

---

## 🔴 Red Team Perspective

Attackers commonly abuse TCP by:

- Port Scanning
- SYN Flood Attacks
- Connection Exhaustion
- Reconnaissance
- Network Enumeration

The TCP handshake often provides early evidence of attacker reconnaissance.

---

## 🔵 Blue Team Perspective

Monitor for:

- Large numbers of SYN packets
- Connections to unusual ports
- Repeated failed handshakes
- High connection rates
- Internal hosts scanning multiple systems

Firewall logs, IDS alerts, and NetFlow data are useful sources for these detections.

---

# 🏅 Step 4 – TLS Handshake (HTTPS)

If the website uses HTTPS, encryption must be established before any sensitive information is exchanged.

During the TLS handshake:

- The server presents its certificate.
- The client verifies the certificate.
- Encryption algorithms are negotiated.
- Session keys are created.
- Secure communication begins.

Only after this process can encrypted HTTP traffic be transmitted.

---

## 🔴 Red Team Perspective

Attackers may attempt:

- SSL Stripping
- Rogue Certificates
- Weak Cipher Exploitation
- TLS Downgrade Attacks
- Man-in-the-Middle (MITM)

Although modern TLS is highly secure, misconfigurations can still introduce risk.

---

## 🔵 Blue Team Perspective

Investigate:

- Expired certificates
- Self-signed certificates
- Weak TLS versions
- Deprecated cipher suites
- Certificate mismatches

These issues may indicate misconfiguration or malicious interception.

---

# 🏆 Step 5 – HTTP Request

Only after networking and encryption are complete does the browser create the HTTP request.

This request contains:

- HTTP Method
- URL
- Headers
- Cookies
- User-Agent
- Authentication Tokens
- Request Body (if applicable)

The request is then transmitted to the web server.

This section introduces the request lifecycle. The structure and components of HTTP requests will be explored in detail later in this chapter.

---

# 📌 Quick Revision

✅ Browser checks local cache first.

✅ DNS converts domain names into IP addresses.

✅ TCP establishes a reliable connection.

✅ TLS secures communication for HTTPS.

✅ Only after these steps is the HTTP request sent.

Every webpage you open follows this sequence, even though it all happens in a fraction of a second.

---

# 🔗 Coming Next

In the next section, we'll break apart a real **HTTP Request** packet and explain every field, header, method, and value that a SOC analyst may encounter during packet analysis, proxy investigations, malware analysis, or incident response.

---

# 📨 Anatomy of an HTTP Request

## 🎯 Purpose

Now that the browser has established a connection with the web server, it is ready to send an **HTTP Request**.

An HTTP request is a message sent by the client to ask the server for a specific resource.

Every time you:

- Open a website
- Click a link
- Submit a login form
- Download a file
- Upload an image
- Refresh a webpage
- Call an API

your browser sends one or more HTTP requests.

For a SOC analyst, understanding HTTP requests is essential because many cyber attacks are hidden inside what initially appears to be normal web traffic.

---

# 💡 Simple Analogy

Imagine ordering food from a restaurant.

You don't simply say:

> "Food."

Instead, you provide details.

- Which dish?
- How many?
- Any special instructions?
- Who is ordering?

An HTTP request works the same way.

It tells the server exactly what the client wants.

---

# 📦 Basic Structure of an HTTP Request

Every HTTP request contains several components.

```
HTTP Method
↓

Requested Resource (URI)

↓

HTTP Version

↓

Headers

↓

Blank Line

↓

Request Body (Optional)
```

A simplified request looks like this:

```http
GET /index.html HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0
Accept: text/html

```

Although only a few lines are visible here, every line provides useful information during an investigation.

---

# 🌍 Real Example

Alice enters:

```
https://portal.company.com/login
```

Her browser sends a request similar to:

```http
GET /login HTTP/1.1
Host: portal.company.com
User-Agent: Mozilla/5.0
Accept: text/html
Accept-Language: en-US
Connection: keep-alive
```

The server now knows:

- Which page Alice wants.
- Which browser she is using.
- Which language she prefers.
- Which website the request is intended for.
- Whether the connection should remain open.

---

# 🧩 Components of an HTTP Request

## 1️⃣ HTTP Method

The method tells the server **what action the client wants to perform**.

Example:

```http
GET /dashboard HTTP/1.1
```

Here,

```
GET
```

is the HTTP Method.

We'll study all HTTP methods in detail later.

---

## 2️⃣ URI (Uniform Resource Identifier)

The URI identifies the requested resource.

Example:

```http
GET /images/logo.png HTTP/1.1
```

Requested resource:

```
/images/logo.png
```

The server looks for this resource and returns it if available.

---

## 3️⃣ HTTP Version

Example:

```http
HTTP/1.1
```

or

```
HTTP/2
```

or

```
HTTP/3
```

The version determines which communication features are supported.

---

## 4️⃣ Headers

Headers contain additional information about the request.

Think of them as metadata.

Example:

```http
Host: portal.company.com

User-Agent: Mozilla/5.0

Accept: text/html

Accept-Encoding: gzip

Cookie: SESSIONID=ABC123
```

Headers tell the server:

- Which website is being accessed.
- Which browser is sending the request.
- Which content types are accepted.
- Whether cookies exist.
- What compression methods are supported.

Headers play a major role in both application functionality and security investigations.

---

## 5️⃣ Request Body

Not every request contains a body.

GET requests usually do **not**.

POST and PUT requests often do.

Example:

```http
POST /login HTTP/1.1

username=alice

password=Password123
```

The body contains the data being sent to the server.

Examples include:

- Login credentials
- Uploaded files
- JSON data
- Form submissions
- API requests

---

# 🌍 Real Internet Example

Suppose Alice logs into her company portal.

Browser Request:

```http
POST /login HTTP/1.1
Host: portal.company.com

username=alice
password=MyPassword
```

The server validates the credentials.

If authentication succeeds,

the server responds with a session cookie.

The browser stores that cookie for future requests.

---

# 🔴 Red Team Perspective

Attackers manipulate HTTP requests constantly.

Examples include:

- SQL Injection payloads
- XSS payloads
- Directory Traversal
- Command Injection
- File Upload Abuse
- API Manipulation
- Authentication Bypass
- Parameter Tampering
- Session Hijacking

A malicious HTTP request often looks similar to a legitimate request, making detection more challenging.

---

# 🦠 Malware Spotlight

Many malware families generate HTTP requests that resemble normal browser traffic.

Examples:

- Cobalt Strike Beacon
- Emotet
- QakBot
- TrickBot
- IcedID
- AsyncRAT

Instead of using Chrome,

they may generate custom HTTP requests with fake User-Agent strings to blend into normal network activity.

---

# 🔵 Blue Team Perspective

When reviewing HTTP requests, SOC analysts should pay attention to:

- HTTP Method
- URI
- Source IP
- Destination IP
- User-Agent
- Referrer
- Cookies
- Authentication Headers
- Response Size
- Request Size
- Time of Request

Small anomalies often become the first indicator of compromise.

---

# 🧠 SOC Investigation Mindset

Imagine your proxy generates this alert:

```
PowerShell initiated an HTTP request.
```

Instead of immediately assuming malware,

ask:

- Which process created the request?
- Which user executed the process?
- Which destination domain was contacted?
- Was the domain recently registered?
- Was DNS observed immediately beforehand?
- Did the request download a file?
- Did the downloaded file execute?
- Was Defender or EDR triggered?
- Were similar requests seen from other hosts?

Good analysts investigate the entire sequence, not just the alert.

---

# 🚨 Detection Opportunities

Monitor for:

- PowerShell making HTTP requests
- cmd.exe using curl
- rundll32 initiating outbound traffic
- certutil downloading files
- mshta contacting the Internet
- Encoded PowerShell commands
- Unusual User-Agent strings
- HTTP requests to newly registered domains
- Large outbound POST requests
- Requests containing known attack patterns

---

# ❌ Common Mistakes

### Mistake 1

"Every HTTP request is suspicious."

False.

Thousands of HTTP requests occur every minute in a normal enterprise.

The goal is to identify the requests that deviate from the baseline.

---

### Mistake 2

"If HTTPS is used, I can't investigate it."

False.

Even if the content is encrypted,

metadata remains valuable.

Investigators can still analyze:

- Destination
- Process
- Timing
- Certificate
- SNI
- JA3 Fingerprint
- DNS
- Traffic Pattern

---

# 💼 Real SOC Scenario

A user reports that their browser briefly opened a webpage before closing automatically.

The proxy logs reveal:

```
GET /invoice.pdf
```

The response returned:

```
200 OK
```

Seconds later,

EDR detects:

```
powershell.exe
```

followed by

```
curl.exe
```

making outbound HTTPS connections to an unfamiliar domain.

The analyst correlates:

- Proxy logs
- DNS logs
- Sysmon Process Creation
- PowerShell Event ID 4104
- EDR telemetry

The investigation confirms that a malicious PDF triggered a PowerShell-based downloader.

---

# ⚡ Quick Revision

✅ Every browser action generates one or more HTTP requests.

✅ A request consists of a Method, URI, Version, Headers, and an optional Body.

✅ HTTP headers contain valuable metadata for investigations.

✅ Many attacks begin with a malicious HTTP request.

✅ SOC analysts should correlate HTTP activity with DNS, endpoint telemetry, and authentication logs.

---

# 🔗 Coming Next

In the next section, we'll examine the **HTTP Response**—how servers reply to clients, what each response contains, how status codes work, and how attackers and defenders can use response data during investigations.

---

# 📨 Anatomy of an HTTP Response

## 🎯 Purpose

After a server receives and processes an HTTP request, it sends information back to the client. This reply is called an **HTTP Response**.

The response tells the client:

- Was the request successful?
- Was the requested resource found?
- Is authentication required?
- Should the browser redirect to another page?
- Was an error encountered?
- What data should be displayed?

Every webpage you view, every image that loads, every API response you receive, and every file you download is delivered through an HTTP response.

For a SOC analyst, HTTP responses are just as valuable as requests because they reveal **what the server actually returned**.

---

# 💡 Simple Analogy

Imagine ordering a pizza.

Your order is the **HTTP Request**.

The restaurant's reply is the **HTTP Response**.

The response could be:

🍕 "Your pizza is ready."

❌ "We're out of stock."

🚚 "Your order has been redirected to another branch."

🔒 "Please verify your identity before ordering."

Every response tells you the outcome of your request.

---

# 📦 Structure of an HTTP Response

Every HTTP response contains the following components:

```
HTTP Version
↓

Status Code

↓

Status Message

↓

Headers

↓

Blank Line

↓

Response Body
```

Example:

```http
HTTP/1.1 200 OK
Date: Mon, 20 Jan 2025 10:00:00 GMT
Server: nginx
Content-Type: text/html
Content-Length: 4521

<html>
...
</html>
```

---

# 🌍 Breaking Down the Response

## 1️⃣ HTTP Version

Example:

```http
HTTP/1.1
```

or

```
HTTP/2
```

or

```
HTTP/3
```

This tells the client which version of HTTP the server is using.

---

## 2️⃣ Status Code

Example:

```
200
```

The status code tells the client whether the request succeeded or failed.

We'll explore all status codes in detail in the next section.

---

## 3️⃣ Status Message

Example:

```
OK
```

Other examples include:

- Not Found
- Forbidden
- Unauthorized
- Internal Server Error

This provides a human-readable explanation of the result.

---

## 4️⃣ Response Headers

Response headers provide metadata about the server's reply.

Example:

```http
Content-Type: text/html

Server: nginx

Content-Length: 4521

Set-Cookie: SESSIONID=98ABCD123

Cache-Control: no-cache
```

These headers tell the browser:

- What type of content is being returned.
- Which web server generated the response.
- Whether a session cookie should be stored.
- Whether the content should be cached.
- How large the response is.

---

## 5️⃣ Response Body

The response body contains the actual content requested by the client.

Examples include:

- HTML pages
- Images
- PDFs
- Videos
- JSON
- XML
- CSS
- JavaScript
- ZIP files

Example:

```html
<html>

<head>

<title>Employee Portal</title>

</head>

<body>

Welcome Alice

</body>

</html>
```

---

# 🌍 Real World Example

Alice opens:

```
https://portal.company.com
```

The browser sends:

```http
GET / HTTP/1.1
```

The server replies:

```http
HTTP/1.1 200 OK

Content-Type: text/html

Content-Length: 7854

Server: nginx

Set-Cookie: SESSIONID=A123BC45
```

The browser now:

- Stores the cookie.
- Reads the HTML.
- Finds CSS files.
- Finds JavaScript files.
- Downloads images.
- Renders the webpage.

One response often generates dozens of additional requests.

---

# 📊 Common Response Headers

| Header | Purpose |
|---------|----------|
| Content-Type | Type of returned content |
| Content-Length | Size of response |
| Server | Web server software |
| Date | Time response was generated |
| Cache-Control | Browser caching policy |
| Set-Cookie | Creates a session cookie |
| Location | Redirect destination |
| Content-Encoding | Compression used |
| Last-Modified | Last update time |
| ETag | Resource version identifier |

These headers often provide valuable forensic information.

---

# 🔴 Red Team Perspective

Attackers carefully analyze HTTP responses to gather intelligence.

Information commonly collected includes:

- Server software
- Framework versions
- Error messages
- Directory structures
- API responses
- Authentication behavior
- Session cookies
- Hidden files

Verbose responses can unintentionally reveal information useful for exploitation.

---

# 🦠 Malware Spotlight

Many malware families rely on HTTP responses to receive instructions.

Example:

```
Beacon

↓

HTTP GET

↓

C2 Server

↓

HTTP Response

↓

Encrypted Commands

↓

Execute Payload
```

The HTTP response may contain:

- New commands
- Updated malware configuration
- URLs for additional payloads
- Encryption keys
- Shell commands

To defenders, it may look like a normal web response unless correlated with endpoint activity.

---

# 🔵 Blue Team Perspective

SOC analysts should review:

- Status codes
- Response size
- Response headers
- Downloaded file type
- Content-Type mismatches
- Unexpected redirects
- Server banners
- Session cookie behavior

Response analysis often reveals successful exploitation or malware delivery.

---

# 🧠 SOC Investigation Mindset

Imagine your SIEM generates this alert:

```
Executable downloaded from external website
```

Before declaring it malicious, ask:

- Which HTTP response delivered the file?
- What was the Content-Type?
- Was the file digitally signed?
- Was the response compressed?
- What process initiated the download?
- Did the file execute?
- Did Defender quarantine it?
- Were similar downloads observed elsewhere?

The response often contains the evidence needed to answer these questions.

---

# 🚨 Detection Opportunities

Look for:

- Large executable downloads
- ZIP or ISO files from unknown domains
- Suspicious redirects
- Unexpected content types
- Mismatched file extensions
- Unusual server banners
- Multiple failed responses before success
- Responses containing known malware signatures

---

# ❌ Common Mistakes

### Mistake 1

"A successful response means everything is safe."

False.

A response with **200 OK** may deliver:

- Malware
- Phishing pages
- Web shells
- Malicious JavaScript
- Exploit kits

Success simply means the server successfully processed the request.

---

### Mistake 2

"I only need to inspect requests."

False.

Responses reveal:

- What was delivered.
- Whether exploitation succeeded.
- Whether malware was downloaded.
- Whether authentication was granted.

Ignoring responses leaves half the investigation incomplete.

---

# 💼 Real SOC Scenario

A proxy alert shows:

```
GET /update.exe
```

Response:

```
HTTP/1.1 200 OK

Content-Type: application/octet-stream

Content-Length: 8.6 MB
```

Seconds later:

- `explorer.exe` launches the downloaded file.
- `powershell.exe` starts.
- DNS queries begin to unfamiliar domains.
- Outbound HTTPS traffic increases.

By correlating:

- Proxy logs
- Response headers
- EDR telemetry
- Sysmon Event ID 1
- DNS logs

the analyst confirms that the downloaded executable initiated a malware infection.

---

# 🧪 Mini Investigation

You observe:

```
Status Code:

200 OK

Content-Type:

application/x-msdownload

Downloaded By:

powershell.exe

Domain:

files-update-service.net
```

### Questions

- Is the file expected?
- Is the domain trusted?
- Which user initiated the request?
- Was the file executed?
- Did additional network connections occur?
- Would you isolate the endpoint?

Think through the investigation before checking threat intelligence.

---

# ⚡ Quick Revision

✅ HTTP responses tell clients the result of their requests.

✅ Every response contains a status code, headers, and usually a body.

✅ Response headers provide valuable metadata for investigations.

✅ Malware frequently receives payloads or commands through HTTP responses.

✅ Always analyze both requests and responses during an investigation.

---

# 🔗 Coming Next

Now that you understand how requests and responses work together, we'll explore one of the most important parts of HTTP:

**HTTP Methods (GET, POST, PUT, DELETE, PATCH, HEAD, OPTIONS, CONNECT, TRACE)**.

Understanding these methods is critical because attackers often abuse them during web exploitation, API attacks, malware communication, and data exfiltration.

---

# 🚀 HTTP Methods - Understanding Client Actions

## 🎯 Purpose

Every HTTP request contains a **Method**, sometimes called an **HTTP Verb**.

The method tells the web server **what action the client wants to perform**.

Think of it as the first instruction sent by the browser.

For example:

- "Give me this webpage."
- "Submit these login credentials."
- "Upload this file."
- "Delete this record."
- "Update this profile."

Without HTTP methods, the server would not know what operation the client intends to perform.

For SOC analysts, HTTP methods provide important context during investigations because many attacks abuse specific methods.

---

# 💡 Simple Analogy

Imagine walking into a bank.

You don't simply say:

> "Bank."

Instead, you perform an action.

- Withdraw Money
- Deposit Money
- Check Balance
- Transfer Funds
- Close Account

The bank performs different operations depending on your request.

HTTP works exactly the same way.

The **Method** tells the server what action should be performed.

---

# 🌍 Common HTTP Methods

| Method | Purpose | Common Usage |
|----------|----------|--------------|
| GET | Retrieve data | Open a webpage |
| POST | Submit data | Login forms, file uploads |
| PUT | Replace existing data | Update an entire record |
| PATCH | Modify part of data | Update a user's email |
| DELETE | Remove data | Delete a file or record |
| HEAD | Retrieve headers only | Health checks, diagnostics |
| OPTIONS | Discover supported methods | CORS, API discovery |
| TRACE | Diagnostic loopback request | Rarely enabled |
| CONNECT | Establish a tunnel | HTTPS through proxies |

Although HTTP defines several methods, most web traffic consists of **GET** and **POST** requests.

---

# 🟢 GET Method

## Purpose

The GET method requests data from the server.

It should **not modify** any information on the server.

Example:

```http
GET /index.html HTTP/1.1
Host: example.com
```

The browser asks:

> "Please send me the homepage."

The server responds:

```
HTTP/1.1 200 OK
```

along with the webpage.

---

## Everyday Examples

Opening:

```
https://github.com
```

Opening:

```
https://google.com
```

Viewing:

```
https://company.com/about
```

All of these generate GET requests.

---

## 🔴 Red Team Perspective

Attackers use GET requests for:

- Downloading malware
- Fetching malicious scripts
- Directory enumeration
- Web shell communication
- Command & Control beaconing
- Retrieving stolen information

Example:

```
GET /payload.exe
```

Although it looks harmless,

the downloaded file may contain malware.

---

## 🔵 Blue Team Perspective

Investigate GET requests that:

- Download executable files
- Access suspicious domains
- Contain long encoded URLs
- Request hidden directories
- Generate repetitive beacon traffic
- Access admin panels unexpectedly

---

## 💼 SOC Investigation Mindset

Alert:

```
GET /invoice.pdf
```

Questions:

- Was the file actually a PDF?
- Did the response contain malware?
- Which process initiated the request?
- Was SmartScreen triggered?
- Did Defender quarantine the download?

Never assume a file extension reflects the actual file type.

---

# 🟠 POST Method

## Purpose

POST sends data to the server.

Unlike GET,

POST usually contains a request body.

Example:

```http
POST /login HTTP/1.1

username=alice

password=Password123
```

The browser sends information instead of requesting it.

---

## Common Examples

- Login Forms
- Registration Forms
- File Uploads
- Payment Requests
- API Calls
- Contact Forms

---

## 🔴 Red Team Perspective

POST is one of the most abused HTTP methods.

Attackers use POST for:

- Uploading Web Shells
- Sending Stolen Credentials
- Data Exfiltration
- C2 Communication
- Malware Beaconing
- Uploading Payloads

Large POST requests often deserve investigation.

---

## 🦠 Malware Spotlight

Many malware families use POST requests because they allow data to be sent to the attacker's server.

Examples:

- Cobalt Strike
- Emotet
- TrickBot
- QakBot
- AsyncRAT

Example:

```
POST /gate.php
```

Body:

```
Encrypted Beacon Data
```

To network devices,

this may simply appear as another HTTP POST request.

---

## 🔵 Blue Team Perspective

Investigate POST requests with:

- Large request bodies
- Base64 encoded data
- Unknown destinations
- Repeating beacon intervals
- PowerShell as the originating process
- Encoded JSON payloads
- Rare User-Agent strings

---

## 💼 SOC Investigation Mindset

Alert:

```
Large HTTP POST
```

Before assuming data exfiltration,

ask:

- Which process generated it?
- Which user owns the process?
- What destination received the data?
- Is the destination approved?
- Was compression used?
- Is encryption hiding sensitive information?
- Have similar uploads occurred before?

Context is critical.

---

# 🔵 PUT Method

## Purpose

PUT replaces an existing resource.

Example:

```http
PUT /users/15
```

The server updates the specified resource with the supplied data.

PUT is commonly used in REST APIs.

---

## Red Team Perspective

If authentication is weak,

attackers may abuse PUT requests to:

- Replace application files
- Modify configurations
- Upload malicious content
- Deface websites

---

## Blue Team Perspective

Monitor for:

- Unexpected PUT requests
- Administrative API endpoints
- File replacement attempts
- Unauthorized configuration changes

---

# 🟡 PATCH Method

## Purpose

PATCH modifies part of an existing resource.

Example:

```
PATCH /profile
```

Instead of replacing the entire object,

only selected fields are updated.

Common in modern REST APIs.

---

## SOC Tip

Unexpected PATCH requests outside normal application behavior may indicate API abuse or unauthorized account changes.

---

# 🔴 DELETE Method

## Purpose

DELETE removes a resource from the server.

Example:

```
DELETE /users/52
```

If authorized,

the server deletes the requested object.

---

## Red Team Perspective

Compromised administrator accounts may use DELETE requests to:

- Destroy evidence
- Delete backups
- Remove logs
- Delete cloud resources

---

## Blue Team Perspective

DELETE requests should always be logged.

Investigate:

- Which user initiated them?
- Was MFA used?
- Was the action expected?
- Were multiple DELETE requests observed?

Deletion activity may indicate destructive attacks.

---

# ⚪ HEAD Method

HEAD works like GET,

except the server returns only the headers.

No response body is sent.

Used for:

- Availability checks
- Content verification
- Monitoring systems

Attackers may also use HEAD requests during reconnaissance.

---

# 🟣 OPTIONS Method

OPTIONS asks:

> "What HTTP methods does this server support?"

Example:

```
OPTIONS /api/
```

The response may reveal:

- GET
- POST
- PUT
- DELETE

Attackers often use OPTIONS while enumerating APIs.

---

# ⚫ TRACE Method

TRACE returns the original request back to the client.

Originally intended for diagnostics.

Modern servers usually disable TRACE because it can introduce security risks such as Cross-Site Tracing (XST).

---

# 🟤 CONNECT Method

CONNECT establishes a tunnel between the client and the destination server.

Most commonly used by:

- HTTPS Proxies
- VPNs
- Secure Gateways

Although legitimate,

unexpected CONNECT requests may warrant investigation.

---

# 🎯 Method Comparison

| Method | Sends Data | Retrieves Data | Commonly Abused |
|---------|------------|----------------|-----------------|
| GET | ❌ | ✅ | Malware Downloads |
| POST | ✅ | ✅ | Data Exfiltration |
| PUT | ✅ | ❌ | File Replacement |
| PATCH | ✅ | ❌ | API Abuse |
| DELETE | ✅ | ❌ | Evidence Destruction |
| HEAD | ❌ | Headers Only | Reconnaissance |
| OPTIONS | ❌ | Server Capabilities | Enumeration |
| TRACE | ❌ | Diagnostics | XST |
| CONNECT | Tunnel | Tunnel | Proxy Abuse |

---

# 🚨 Detection Opportunities

Monitor for:

- Unexpected PUT requests
- DELETE requests from normal users
- Large POST requests
- GET requests downloading executables
- OPTIONS enumeration
- TRACE requests
- Repeating POST beacon traffic
- Unusual CONNECT tunnels

---

# 🧠 SOC Investigation Mindset

Imagine your SIEM generates:

```
HTTP Method:

DELETE

User:

service_backup

Destination:

Production API
```

Ask yourself:

- Is this expected?
- Was there a maintenance window?
- Which IP initiated it?
- Was MFA used?
- Was the account compromised?
- Did similar DELETE requests occur previously?
- Were backups removed afterward?

Never investigate a method in isolation—always correlate it with the user, endpoint, process, destination, and timing.

---

# ⚡ Quick Revision

✅ HTTP methods define the action requested by the client.

✅ GET retrieves data.

✅ POST submits data.

✅ PUT replaces resources.

✅ PATCH updates part of a resource.

✅ DELETE removes resources.

✅ OPTIONS and HEAD are often used during reconnaissance.

✅ POST and GET are the most commonly observed methods in enterprise traffic.

---

# 🔗 Coming Next

In the next section, we'll explore **HTTP Status Codes**—how servers communicate success, failure, authentication issues, redirects, and errors, and how SOC analysts use these codes to identify reconnaissance, brute-force attacks, exploitation attempts, and application failures.

---

# 🚦 HTTP Status Codes - Understanding Server Responses

## 🎯 Purpose

After a server receives an HTTP request, it returns a **Status Code** to indicate the result of that request.

Think of the status code as the server's answer to the client.

It tells us whether:

- The request was successful.
- Authentication is required.
- Access is denied.
- The requested resource does not exist.
- The server encountered an error.
- The client should redirect to another location.

For SOC analysts, status codes provide valuable insight into attacker behavior, application health, reconnaissance, brute-force attempts, exploitation attempts, and malware communication.

---

# 💡 Simple Analogy

Imagine visiting a hotel.

You ask the receptionist for Room 305.

The receptionist may respond:

✅ "Here is your room key."

❌ "That room doesn't exist."

🔒 "Please show your ID first."

🚫 "You are not allowed to enter."

🏨 "The hotel system is currently unavailable."

HTTP Status Codes work exactly the same way.

Every request receives a response describing the outcome.

---

# 📚 Status Code Categories

HTTP Status Codes are divided into five categories.

| Code Range | Meaning |
|------------|---------|
| 1xx | Informational |
| 2xx | Success |
| 3xx | Redirection |
| 4xx | Client Errors |
| 5xx | Server Errors |

Think of these categories as high-level response families.

---

# 🟢 1xx - Informational Responses

These responses indicate that the request has been received and processing will continue.

Common Examples:

```
100 Continue

101 Switching Protocols

102 Processing
```

These are rarely encountered during normal web browsing.

---

## SOC Perspective

1xx responses are uncommon in enterprise investigations.

However, unexpected protocol switching may be observed during:

- WebSocket communication
- API upgrades
- Reverse proxy behavior

---

# 🟢 2xx - Success Responses

The request completed successfully.

This does **NOT** mean the activity was safe.

It simply means the server processed the request successfully.

---

## 200 OK

The most common HTTP response.

Example:

```
GET /index.html

↓

200 OK
```

The requested resource was successfully returned.

---

### Legitimate Examples

- Opening a website
- Downloading a PDF
- Viewing an image
- Loading JavaScript
- Retrieving API data

---

### 🔴 Red Team Perspective

A malicious request may also receive:

```
200 OK
```

Examples:

- Malware downloaded successfully
- SQL Injection returned data
- Web Shell executed
- Stolen credentials accepted
- C2 server responded

Never assume "200 OK" means safe.

---

### 🔵 Blue Team Perspective

Investigate:

- 200 responses for executable downloads
- Large file transfers
- Responses from suspicious domains
- Unexpected admin pages
- Web shell access

---

## 201 Created

The requested object was successfully created.

Example:

```
POST /users
```

↓

```
201 Created
```

Often seen in REST APIs.

---

## 204 No Content

The request succeeded,

but the server has nothing to return.

Frequently used by APIs.

---

# 🟡 3xx - Redirection Responses

The client must access another location.

The browser automatically follows these redirects.

---

## 301 Moved Permanently

The requested resource has permanently moved.

Example:

```
http://example.com

↓

301

↓

https://example.com
```

Very common when websites force HTTPS.

---

## 302 Found

Temporary redirect.

Often used after successful login.

---

## 304 Not Modified

The browser already has the latest cached version.

The server tells the browser:

> "Use your local copy."

This improves performance.

---

### 🔴 Red Team Perspective

Attackers abuse redirects during:

- Phishing campaigns
- Open Redirect vulnerabilities
- Malware delivery
- Credential harvesting

---

### 🔵 Blue Team Perspective

Investigate:

- Redirect chains
- Redirects to unknown domains
- Multiple redirects before download
- Unexpected country changes

---

# 🔴 4xx - Client Errors

The request could not be completed because of an issue with the client request.

These status codes are extremely valuable during investigations.

---

## 400 Bad Request

The request was malformed.

Possible reasons:

- Invalid syntax
- Corrupted request
- Missing parameters

Repeated 400 responses may indicate fuzzing or poorly written attack scripts.

---

## 401 Unauthorized

Authentication is required.

The client has not yet authenticated.

---

### SOC Investigation

Repeated

```
401

401

401

401

401
```

may indicate:

- Password Spraying
- Brute Force
- Credential Stuffing

---

## 403 Forbidden

Authentication may exist,

but access is denied.

Example:

An ordinary employee attempts to access:

```
/admin
```

↓

```
403 Forbidden
```

---

### Red Team Perspective

Attackers use 403 responses to:

- Discover protected resources
- Enumerate directories
- Identify administrative panels

---

## 404 Not Found

The requested resource does not exist.

One of the most valuable status codes during threat hunting.

---

### Example

```
GET /backup.zip

↓

404
```

---

### Red Team Perspective

Attackers generate thousands of 404 responses during:

- Directory Bruteforce
- Web Enumeration
- Vulnerability Scanning
- Content Discovery

Tools:

- Gobuster
- Dirsearch
- FFUF
- Feroxbuster

often produce large numbers of 404 responses.

---

### 🔵 Blue Team Perspective

Look for:

- Hundreds of 404 responses
- Sequential requests
- Common wordlists
- Automated scanning behavior
- High request rates

---

## 405 Method Not Allowed

The requested HTTP Method is not permitted.

Example:

```
DELETE /login
```

↓

```
405 Method Not Allowed
```

Attackers often trigger 405 responses while testing APIs.

---

## 429 Too Many Requests

The server is rate limiting the client.

Common causes:

- Brute Force
- API Abuse
- Web Scraping
- Credential Stuffing

429 is often a positive sign that security controls are functioning.

---

# 🔥 5xx - Server Errors

The request reached the server,

but the server failed to process it.

These responses often indicate application problems.

---

## 500 Internal Server Error

Generic server failure.

Possible causes:

- Coding bugs
- Application crashes
- Unexpected exceptions

---

### Red Team Perspective

Attackers intentionally trigger 500 responses to:

- Reveal debugging information
- Leak stack traces
- Identify vulnerable applications

---

## 502 Bad Gateway

Usually indicates communication problems between:

- Reverse Proxy
- Load Balancer
- Backend Server

---

## 503 Service Unavailable

The server is temporarily unavailable.

Possible reasons:

- Maintenance
- Resource exhaustion
- DDoS attack
- Application crash

---

## 504 Gateway Timeout

One server waited too long for another server to respond.

Common in distributed applications.

---

# 🦠 Malware Spotlight

Many malware families expect:

```
200 OK
```

from their Command & Control server.

If they receive:

```
404

403

500
```

they may:

- Retry later
- Switch domains
- Activate backup infrastructure
- Use Domain Generation Algorithms (DGA)

Monitoring unusual repeated responses can reveal beaconing behavior.

---

# 🧠 SOC Investigation Mindset

## Scenario

Your SIEM reports:

```
Source IP:

10.15.22.81

Destination:

portal.company.com

Requests:

6,243

Status Codes:

404

Time:

4 Minutes
```

Before declaring it malicious, ask:

- Which User-Agent was used?
- Is this a browser or an automated tool?
- Are the requested paths sequential?
- Is a known wordlist being used?
- Has this IP performed similar activity before?
- Is this vulnerability scanning?
- Should the IP be blocked?

The status code alone is never enough.

Context determines the severity.

---

# 🚨 Detection Opportunities

Investigate:

- Large numbers of 401 responses
- Repeated 403 responses
- Hundreds of 404 responses
- Multiple 500 responses after suspicious requests
- Unexpected 302 redirects
- Large numbers of 429 responses
- Success (200) immediately after repeated authentication failures

---

# 🎯 MITRE ATT&CK Mapping

| Observation | Possible Technique |
|-------------|--------------------|
| Many 401 responses | Password Guessing (T1110) |
| Numerous 404 responses | Active Scanning (T1595) |
| 200 after brute-force attempts | Valid Accounts (T1078) |
| Redirect to phishing site | Phishing (T1566) |
| 500 errors during exploit attempts | Exploitation for Client Execution (context dependent) |

> **Note:** MITRE mappings depend on the full activity, not the status code alone. A status code is evidence—not proof—of a specific technique.

---

# 💼 Real SOC Scenario

A web server suddenly logs:

- 3,800 HTTP requests
- 3,100 responses with **404 Not Found**
- Requests targeting:
  - `/admin`
  - `/backup.zip`
  - `/phpmyadmin`
  - `/.env`
  - `/wp-login.php`

Firewall logs show the same source IP.

The User-Agent identifies **ffuf**.

The analyst concludes the activity is an automated content discovery scan and blocks the IP while reviewing whether any requests succeeded.

---

# ⚡ Quick Revision

✅ Status codes describe the outcome of an HTTP request.

✅ 2xx means the request succeeded—not necessarily that it was safe.

✅ Large numbers of 401 responses may indicate password attacks.

✅ Numerous 404 responses often suggest reconnaissance or directory brute-forcing.

✅ 429 responses show rate limiting is working.

✅ Always correlate status codes with logs, users, processes, User-Agent strings, and request patterns.

---

# 🔗 Coming Next

Next we'll explore **HTTP Headers**—one of the richest sources of information in web investigations. We'll break down headers like `Host`, `User-Agent`, `Cookie`, `Authorization`, `Referer`, `Origin`, `X-Forwarded-For`, and many more, explaining how attackers manipulate them and how SOC analysts use them to detect malicious activity.

---

# 🏷️ HTTP Headers - The Metadata Behind Every Request

> **"HTTP Headers don't usually contain the data you want—they contain the information that tells you everything about the communication."**

---

# 🎯 Purpose

HTTP Headers are additional pieces of information exchanged between the client and the server.

Think of them as the **metadata** of an HTTP request or response.

They answer questions such as:

- Who sent the request?
- Which website is being accessed?
- Which browser is being used?
- Is the user authenticated?
- Which language does the client prefer?
- Should the response be cached?
- Should a session cookie be created?
- Is the request coming through a proxy?

For a SOC Analyst, HTTP headers are one of the richest sources of evidence during web investigations.

---

# 💡 Simple Analogy

Imagine sending a courier package.

The package contains the actual item.

But outside the package are labels such as:

- Sender
- Receiver
- Address
- Delivery Instructions
- Priority
- Tracking Number

These labels are similar to HTTP Headers.

The package is the **Body**.

The labels are the **Headers**.

---

# 📦 Real HTTP Request

```http
GET /login HTTP/1.1
Host: portal.company.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
Accept: text/html
Accept-Language: en-US
Accept-Encoding: gzip, deflate
Connection: keep-alive
Referer: https://portal.company.com
Cookie: SESSIONID=98AF6D2BC41
```

Every line after the request line is an HTTP Header.

---

# 📖 Anatomy of Important Headers

---

# 🌐 Host

```http
Host: portal.company.com
```

## Purpose

Identifies which website the client wants to access.

Modern web servers often host multiple websites on the same IP address.

Without the Host header,

the server wouldn't know which website to serve.

---

### 🔴 Red Team Perspective

Attackers manipulate the Host header during:

- Host Header Injection
- Password Reset Poisoning
- Cache Poisoning
- Reverse Proxy Attacks

---

### 🔵 Blue Team Perspective

Investigate:

- Unexpected Host values
- Internal domains exposed externally
- Mismatched Host headers
- Requests to unknown virtual hosts

---

# 🖥️ User-Agent

```http
User-Agent:

Mozilla/5.0

Windows NT 10.0

Chrome/137
```

## Purpose

Identifies the client software making the request.

Common examples:

Chrome

Firefox

Edge

Safari

curl

PowerShell

Python Requests

Go-http-client

Java

---

### Legitimate Examples

```
Mozilla/5.0

Chrome

Edge
```

---

### Suspicious Examples

```
curl/8.0

python-requests

Go-http-client

PowerShell

Wget

libwww-perl
```

These are not automatically malicious,

but they deserve additional investigation.

---

### 🦠 Malware Spotlight

Many malware families spoof User-Agent strings.

Examples:

```
Mozilla/5.0
```

while actually being

- Cobalt Strike
- AsyncRAT
- QakBot
- Emotet

Never trust the User-Agent alone.

Always correlate with:

- Process
- Parent Process
- Command Line
- JA3 Fingerprint
- DNS
- TLS

---

### SOC Investigation

Alert:

```
User-Agent

PowerShell/7.4
```

Questions:

- Why is PowerShell communicating externally?

- Which script executed?

- Which user launched it?

- Which process spawned PowerShell?

- Was Event ID 4104 generated?

- Was Defender triggered?

---

# 🍪 Cookie

```http
Cookie:

SESSIONID=A12BC345DEF
```

## Purpose

Cookies allow websites to remember users.

Without cookies,

every webpage would treat you as a new visitor.

Cookies commonly store:

- Session IDs
- Authentication Tokens
- User Preferences
- Shopping Carts
- Language Settings

---

### 🔴 Red Team Perspective

Cookies are valuable targets.

Attackers attempt:

- Cookie Theft
- Session Hijacking
- Pass-the-Cookie
- XSS Cookie Theft

Stealing a valid session cookie may allow an attacker to bypass authentication.

---

### 🔵 Blue Team Perspective

Investigate:

- Session reuse

- Same cookie from multiple countries

- Impossible travel

- Cookie replay

- Stolen browser sessions

---

# 🔐 Authorization

```http
Authorization:

Bearer eyJhbGc...
```

## Purpose

Proves the client's identity.

Common authentication methods include:

- Basic Authentication

- Bearer Tokens

- JWT

- API Keys

- OAuth Tokens

---

### Red Team Perspective

Attackers attempt to steal:

- JWT Tokens

- OAuth Tokens

- API Keys

- Bearer Tokens

A stolen token may provide immediate access without requiring a password.

---

### Blue Team Perspective

Monitor:

- Expired Tokens

- Token Reuse

- Multiple IPs using same token

- Impossible Travel

- Anonymous VPN usage

---

# 🔗 Referer

```http
Referer:

https://google.com
```

## Purpose

Indicates which webpage referred the user.

Example:

Google Search

↓

GitHub

↓

Referer

↓

google.com

---

### SOC Use Cases

Helpful during phishing investigations.

Questions:

Where did the victim come from?

Was the request initiated by:

- Email

- Advertisement

- Search Engine

- Social Media

- Internal Application

---

# 🌍 Origin

```http
Origin:

https://portal.company.com
```

Used mainly for:

- APIs

- Cross-Origin Requests

- CORS Validation

Attackers sometimes manipulate the Origin header during API attacks.

---

# 🧾 Accept

```http
Accept:

text/html
```

Tells the server which content types the client accepts.

Examples:

- HTML

- JSON

- XML

- Images

- PDFs

---

# 🌎 Accept-Language

```http
Accept-Language:

en-US
```

Specifies the preferred language.

Can also provide useful context during investigations.

---

# 📦 Content-Type

```http
Content-Type:

application/json
```

Describes the data contained in the request or response.

Common values:

```
text/html

application/json

application/xml

multipart/form-data

application/octet-stream

image/png
```

---

### Detection Tip

A file named

```
invoice.pdf
```

with

```
Content-Type:

application/octet-stream
```

deserves investigation.

The extension and actual content may not match.

---

# 📏 Content-Length

```http
Content-Length:

10485760
```

Indicates the size of the transmitted data.

Useful for detecting:

- Large Uploads

- Data Exfiltration

- Malware Downloads

- Unexpected Transfers

---

# 🌍 X-Forwarded-For

```http
X-Forwarded-For:

203.0.113.51
```

When traffic passes through proxies or load balancers,

this header often stores the original client IP.

Critical during investigations.

---

# 🔴 Red Team Perspective

Attackers may attempt:

- Header Manipulation

- Header Injection

- Host Header Injection

- Cache Poisoning

- X-Forwarded-For Spoofing

- Referer Spoofing

- User-Agent Spoofing

Headers should never be trusted blindly.

---

# 🔵 Blue Team Perspective

Headers help answer:

- Which browser?

- Which operating system?

- Which application?

- Which session?

- Which authentication token?

- Which original IP?

- Which referring website?

Headers often provide the missing context needed to complete an investigation.

---

# 💼 Real SOC Investigation

## Alert

```
Suspicious Download
```

Proxy Log

```http
GET /invoice.pdf

User-Agent:

PowerShell/7.4

Host:

download-share.net

Content-Length:

18 MB

Response:

200 OK
```

EDR

```
powershell.exe

↓

invoice.pdf

↓

invoice.exe

↓

Execution
```

Sysmon

```
Event ID 1

↓

powershell.exe

↓

Command Line

↓

Invoke-WebRequest
```

### Investigation

The analyst correlates:

✔ Proxy Logs

✔ Sysmon

✔ DNS Logs

✔ Defender

✔ EDR

✔ HTTP Headers

Result:

The "PDF" was actually an executable downloaded through PowerShell.

---

# 🛡️ Detection Engineering

### Suspicious Indicators

- PowerShell User-Agent
- curl User-Agent
- python-requests
- Go-http-client
- Missing Referer
- Extremely Large Content-Length
- Authorization Token Reuse
- Multiple Countries Using Same Cookie
- Suspicious Host Header
- Fake Browser Versions
- Rare Content-Type
- X-Forwarded-For Mismatch

---

# 🎯 MITRE ATT&CK Mapping

| Observation | Possible Technique |
|------------|--------------------|
| Stolen Session Cookie | T1539 – Steal Web Session Cookie |
| PowerShell Download | T1105 – Ingress Tool Transfer |
| User-Agent Spoofing | T1036 – Masquerading |
| Large POST Request | T1041 – Exfiltration Over C2 Channel |
| Token Theft | T1528 – Steal Application Access Token |

---

# ⚡ Quick Revision

✅ Headers contain metadata, not the primary content.

✅ User-Agent identifies the client application.

✅ Host specifies the target website.

✅ Cookies maintain user sessions.

✅ Authorization headers prove identity.

✅ X-Forwarded-For helps identify the original client IP.

✅ Content-Type reveals the actual format of transferred data.

✅ Content-Length helps identify large uploads or downloads.

✅ Attackers frequently manipulate headers to evade detection.

---

# 🧠 SOC Analyst Challenge

A proxy log shows:

```http
GET /update.exe HTTP/1.1

Host: cdn-microsoft-update.net

User-Agent: Mozilla/5.0

Referer: -

Content-Type: application/octet-stream

Content-Length: 24 MB

Response: 200 OK
```

At first glance, it appears to be a normal browser download.

Before concluding it's legitimate, answer these questions:

1. Is the domain actually owned by Microsoft?
2. Does the User-Agent match the originating process?
3. Why is the Referer missing?
4. What process initiated the download?
5. Did the file execute after download?
6. Was Defender or EDR triggered?
7. Is this domain newly registered?
8. Did other endpoints contact the same domain?
9. Does the downloaded file have a valid digital signature?
10. Would you escalate this as a potential malware infection?

Remember:

**Headers provide clues, but only correlation across DNS, proxy, EDR, Sysmon, and endpoint telemetry reveals the full story.**

---

# 🔗 Knowledge Connections

HTTP Headers
        │
        ├── Cookies → Session Hijacking
        ├── Authorization → OAuth / JWT
        ├── User-Agent → Malware Detection
        ├── Host → Virtual Hosting
        ├── Content-Type → Malware Delivery
        ├── Content-Length → Data Exfiltration
        ├── Referer → Phishing Investigation
        └── X-Forwarded-For → Client Attribution

---

# 🍪 Cookies & Sessions - Maintaining State on the Web

> **"HTTP is stateless by design. Cookies and Sessions allow websites to remember who you are."**

---

# 🎯 Purpose

HTTP is a **stateless protocol**.

This means every HTTP request is treated as an independent request.

The server does **not** automatically remember:

- Who you are.
- Whether you've already logged in.
- What's in your shopping cart.
- Which page you visited previously.
- Whether you're an administrator.

Without cookies and sessions, you would need to log in **every time** you clicked a new page.

Cookies and Sessions solve this problem.

For SOC analysts, understanding cookies and sessions is essential because attackers frequently target them to **bypass authentication without stealing passwords.**

---

# 💡 Simple Analogy

Imagine checking into a hotel.

When you arrive:

You provide your ID.

The receptionist verifies your identity.

Instead of asking for your ID every time you enter the hotel,

they hand you a **room key card**.

The key card proves you've already been verified.

Cookies work the same way.

The website verifies your identity once,

then gives your browser a unique identifier that is sent with every future request.

---

# 🌐 Why HTTP Needs Cookies

Without Cookies...

```
Browser

↓

GET /dashboard

↓

Server

↓

Who are you?

↓

Login Again

↓

GET /profile

↓

Who are you?

↓

Login Again

↓

GET /settings

↓

Who are you?

↓

Login Again
```

This would make modern websites unusable.

---

With Cookies...

```
Login Once

↓

Server Creates Session

↓

Browser Stores Cookie

↓

Every Future Request

↓

Cookie Sent Automatically

↓

Server Recognizes User

↓

Access Granted
```

---

# 📦 What is a Cookie?

A Cookie is a **small piece of data** stored by the browser.

Example

```http
Set-Cookie:

SESSIONID=ABC123XYZ789
```

The browser stores it.

Every future request automatically includes:

```http
Cookie:

SESSIONID=ABC123XYZ789
```

The server now recognizes the user.

---

# 🌍 Login Example

Alice visits

```
https://portal.company.com
```

She enters:

```
Username

Alice

Password

********
```

Browser

↓

POST /login

↓

Server Validates Credentials

↓

Server Creates Session

↓

Server Generates Cookie

↓

Browser Stores Cookie

↓

Future Requests

↓

Cookie Included Automatically

↓

User Remains Logged In

---

# 🔍 Actual HTTP Example

Server Response

```http
HTTP/1.1 200 OK

Set-Cookie:

SESSIONID=F7A81B92AC11
```

Future Browser Request

```http
GET /dashboard

Cookie:

SESSIONID=F7A81B92AC11
```

The server checks the session ID.

If valid,

the request is accepted.

---

# 🧠 What is a Session?

A Session is **server-side memory**.

The Cookie itself usually contains only an identifier.

Example

Browser

```
SESSIONID=A12345
```

Server Database

| Session ID | User | Role | Login Time |
|------------|------|------|------------|
| A12345 | Alice | Administrator | 08:31 |
| B92811 | Bob | HR | 09:14 |
| K923AA | Charlie | Finance | 10:02 |

The browser stores only:

```
SESSIONID=A12345
```

Everything else remains securely on the server.

---

# 🔄 Complete Session Lifecycle

```
User Opens Login Page

↓

POST /login

↓

Credentials Verified

↓

Session Created

↓

Session ID Generated

↓

Cookie Sent

↓

Browser Stores Cookie

↓

Future Requests Include Cookie

↓

Server Validates Session

↓

Access Granted

↓

Logout

↓

Session Destroyed

↓

Cookie Invalid
```

---

# 🍪 Common Cookie Attributes

## Secure

```http
Secure
```

Cookie only travels over HTTPS.

Never transmitted using HTTP.

---

## HttpOnly

```http
HttpOnly
```

JavaScript cannot access the cookie.

Protects against many XSS attacks.

---

## SameSite

```http
SameSite=Lax
```

Helps protect against Cross-Site Request Forgery (CSRF).

Values include:

```
Strict

Lax

None
```

---

## Expires

Determines when the cookie becomes invalid.

Example

```
Expires:

Tue, 21 Jan 2026
```

---

## Domain

Defines which websites can use the cookie.

---

## Path

Limits where the cookie is sent.

Example

```
Path=/admin
```

Only administrative pages receive this cookie.

---

# 🔴 Red Team Perspective

Cookies are extremely valuable.

Stealing a valid session cookie may allow an attacker to bypass authentication completely.

Common attacks include:

- Session Hijacking
- Cookie Theft
- Session Fixation
- Cross-Site Scripting (XSS)
- Cross-Site Request Forgery (CSRF)
- Browser Cookie Extraction
- Pass-the-Cookie
- Evilginx / Adversary-in-the-Middle (AiTM)
- Token Replay

Many modern phishing kits don't steal passwords.

They steal **authenticated session cookies**.

---

# 🦠 Malware Spotlight

Modern information stealers target browser cookies.

Examples include:

- Lumma Stealer
- RedLine Stealer
- Raccoon Stealer
- Vidar
- RisePro
- StealC

Common targets:

- Chrome
- Edge
- Firefox
- Brave

Attackers use these cookies to hijack:

- Microsoft 365
- Google Workspace
- Facebook
- GitHub
- AWS
- Azure
- Banking Sessions

Sometimes **without ever knowing the user's password**.

---

# 🔵 Blue Team Perspective

Monitor for:

- Same session used from multiple countries.
- Impossible travel.
- Session reuse after logout.
- New device using an existing cookie.
- Session active for unusually long periods.
- Multiple IP addresses sharing one session.
- Cookie replay from different browsers.
- Stolen authentication tokens.

---

# 🛡️ Detection Engineering

Investigate:

- Multiple geolocations using one Session ID.
- Cookie reuse after password reset.
- Same Session ID across different User-Agents.
- Session activity outside business hours.
- Authentication bypass without login events.
- New IP using an existing authenticated session.
- Browser fingerprint mismatch.

---

# 🎯 MITRE ATT&CK Mapping

| Observation | Technique |
|------------|-----------|
| Session Cookie Theft | T1539 - Steal Web Session Cookie |
| Browser Credential Theft | T1555 - Credentials from Password Stores |
| Token Replay | T1528 - Steal Application Access Token |
| XSS Cookie Theft | T1056 - Input Capture (Context Dependent) |
| Browser Data Collection | T1217 - Browser Session Discovery |

---

# 💼 Real SOC Investigation

## Alert

```
Microsoft 365 Login

User

Alice

Location

Mumbai

08:11
```

Five minutes later

```
Same Session

Location

Moscow

08:16

No New Login

No MFA
```

Identity Logs show:

No authentication event.

No password reset.

No MFA challenge.

The Session ID remained identical.

EDR Investigation finds:

```
chrome.exe

↓

Cookie Database Accessed

↓

Lumma Stealer Executed
```

Conclusion:

The attacker did **not** steal Alice's password.

They stole her authenticated browser session.

---

# 🚨 Detection Opportunities

Look for:

- Session reuse from different countries.
- Long-lived sessions.
- Same cookie across multiple IP addresses.
- Authentication bypass.
- Browser cookie database access.
- Suspicious browser extensions.
- Info-stealer activity.
- Unexpected OAuth token usage.

---

# ❌ Common Mistakes

### Mistake 1

"Cookies store passwords."

False.

Cookies usually store only a **session identifier** or authentication token.

Passwords should never be stored inside cookies.

---

### Mistake 2

"Changing the password immediately logs attackers out."

Not always.

If the attacker already possesses a valid session cookie,

they may remain authenticated until:

- The session expires.
- The server revokes the session.
- The user signs out everywhere.
- Administrators invalidate active sessions.

---

### Mistake 3

"HTTPS prevents cookie theft."

HTTPS protects cookies **during transmission**.

It does **not** prevent malware already running on the endpoint from stealing browser cookies stored locally.

---

# 🧪 SOC Investigation Challenge

## Alert

```
User:

Alice

Application:

Microsoft 365

Session ID:

AB91F7D22

Location 1:

Nagpur

09:02

Location 2:

Romania

09:06

MFA:

No

New Login:

No
```

### Investigation Questions

1. Was there a new authentication event?
2. Was the same Session ID reused?
3. Did EDR detect an information stealer?
4. Were browser cookie databases accessed?
5. Were other cloud services accessed using the same session?
6. Should all active sessions be revoked?
7. Should OAuth tokens also be invalidated?
8. Is this an AiTM phishing attack or local malware?

---

# ⚡ Quick Revision

✅ HTTP is stateless.

✅ Cookies allow browsers to remember users.

✅ Sessions store user state on the server.

✅ Cookies usually contain only a Session ID.

✅ Attackers often steal cookies instead of passwords.

✅ Session hijacking can bypass MFA if the attacker steals an already authenticated session.

✅ SOC analysts should correlate cookies, authentication logs, EDR telemetry, browser artifacts, and cloud identity logs during investigations.

---

# 🔗 Knowledge Connections

Cookies
     │
     ├── Sessions
     ├── Authentication
     ├── JWT
     ├── OAuth
     ├── MFA
     ├── Session Hijacking
     ├── XSS
     ├── CSRF
     ├── Browser Forensics
     ├── Cloud Identity
     ├── Info-Stealers
     └── Incident Response

---

# 🔐 Authentication & Authorization - Proving Identity vs Granting Access

> **"Authentication answers 'Who are you?' Authorization answers 'What are you allowed to do?'"**

---

# 🎯 Purpose

Authentication and Authorization are two of the most misunderstood concepts in cybersecurity.

Many people use these terms interchangeably, but they solve completely different problems.

Almost every web application, cloud platform, VPN, email service, API, and enterprise application relies on both.

For a SOC Analyst, understanding the difference is critical because attackers often:

- Steal credentials.
- Hijack sessions.
- Abuse authentication tokens.
- Escalate privileges.
- Bypass authorization controls.

Understanding **where authentication ends and authorization begins** helps investigators determine exactly **how an attacker gained access and what they were able to do afterward.**

---

# 💡 Simple Analogy

Imagine arriving at an airport.

### Step 1 — Authentication

The security officer checks your:

- Passport
- Face
- Boarding Pass

They verify **who you are**.

This is **Authentication**.

---

### Step 2 — Authorization

Now the airline checks:

Can you enter:

- Business Lounge?
- Cockpit?
- Baggage Area?
- Runway?
- VIP Terminal?

These permissions depend on your role.

This is **Authorization**.

---

# 🌍 Authentication vs Authorization

| Authentication | Authorization |
|---------------|---------------|
| Verifies Identity | Grants Permissions |
| Happens First | Happens After Authentication |
| "Who are you?" | "What can you do?" |
| Login | Access Control |
| Username + Password | User Roles |
| MFA | RBAC |
| Biometrics | Permissions |
| Certificates | Policies |

---

# 🔑 Authentication

Authentication is the process of verifying a user's identity.

Common authentication methods include:

- Username & Password
- Multi-Factor Authentication (MFA)
- Smart Cards
- Biometrics
- Security Keys (FIDO2/WebAuthn)
- Certificates
- OAuth
- SAML
- OpenID Connect (OIDC)

---

# 🌍 Login Flow

```
User

↓

Username

↓

Password

↓

MFA

↓

Identity Verified

↓

Authentication Successful
```

At this point,

the server knows **who the user is.**

---

# 🔓 Authorization

After identity is verified,

the application decides what the user is allowed to access.

Example

Alice logs in successfully.

Authentication succeeds.

But Alice belongs to the HR department.

She attempts to access:

```
https://portal.company.com/admin
```

Authentication:

✅ Successful

Authorization:

❌ Denied

The application returns:

```
403 Forbidden
```

Alice proved who she is,

but she lacks permission to access the admin portal.

---

# 🏢 Real Enterprise Example

| User | Role | Can View HR Data | Can Manage Users | Can Delete Logs |
|------|------|------------------|------------------|-----------------|
| Alice | HR | ✅ | ❌ | ❌ |
| Bob | SOC Analyst | ❌ | ❌ | ❌ |
| Charlie | Domain Admin | ✅ | ✅ | ✅ |

All three users authenticate successfully.

However,

their permissions differ based on authorization.

---

# 🧠 Authentication Factors

Authentication usually relies on one or more factors.

### Something You Know

- Password
- PIN
- Security Questions

---

### Something You Have

- Smartphone
- OTP Token
- Smart Card
- FIDO2 Security Key

---

### Something You Are

- Fingerprint
- Face Recognition
- Iris Scan
- Voice Recognition

---

### Something You Do

- Typing Pattern
- Mouse Movement
- Behavioral Biometrics

---

### Somewhere You Are

- Trusted Office Network
- Corporate VPN
- GPS Location

Modern Zero Trust environments often evaluate multiple factors simultaneously.

---

# 🔴 Red Team Perspective

Attackers rarely "hack" authentication directly.

Instead, they target weaknesses surrounding it.

Common techniques include:

- Password Spraying
- Credential Stuffing
- Brute Force
- MFA Fatigue
- SIM Swapping
- Session Hijacking
- Token Theft
- Pass-the-Hash
- Pass-the-Ticket
- Golden Ticket
- Silver Ticket
- Evilginx (AiTM)
- OAuth Consent Phishing

The goal is to appear as a legitimate authenticated user.

---

# 🦠 Malware Spotlight

Many malware families focus on stealing authentication material rather than passwords.

Examples:

- Lumma Stealer
- RedLine
- Vidar
- Raccoon
- StealC
- AsyncRAT

Common targets:

- Browser Passwords
- Session Cookies
- JWT Tokens
- OAuth Tokens
- Saved Credentials
- VPN Credentials

---

# 🔵 Blue Team Perspective

Monitor for:

- Impossible Travel
- Multiple Failed Logins
- Password Spraying
- New Device Logins
- New Browser Fingerprints
- Disabled MFA
- Privilege Escalation
- Token Replay
- Authentication from Anonymous VPNs
- Dormant Accounts Becoming Active

Authentication events rarely tell the whole story.

Always correlate with endpoint and network telemetry.

---

# 🛡️ Authorization Models

## RBAC (Role-Based Access Control)

Permissions are assigned based on roles.

Example:

```
SOC Analyst

↓

Read SIEM Logs

↓

Cannot Delete Logs
```

---

## ABAC (Attribute-Based Access Control)

Access depends on attributes.

Example:

```
Department

AND

Country

AND

Device Compliance

AND

Risk Score
```

Common in cloud environments.

---

## Least Privilege

Users receive only the permissions necessary to perform their jobs.

One of the most important cybersecurity principles.

---

## Zero Trust

Never trust.

Always verify.

Every request is continuously evaluated based on:

- Identity
- Device
- Risk
- Location
- Behavior
- Compliance

---

# 💼 Real SOC Investigation

## Alert

```
Azure AD Sign-In

User:

Alice

Authentication:

Successful

MFA:

Passed

Country:

India
```

Five minutes later

```
Azure Activity Logs

↓

User Added

↓

Global Administrator Role
```

Questions:

- Did Alice normally perform administrative tasks?
- Who granted the new role?
- Was Privileged Identity Management (PIM) used?
- Was the authentication legitimate?
- Was the session hijacked?
- Was an OAuth application abused?

Authentication succeeded.

The real incident occurred during **authorization**.

---

# 🔍 Common Authentication Attacks

| Attack | Description |
|---------|-------------|
| Brute Force | Tries many passwords for one account |
| Password Spraying | Tries one password against many accounts |
| Credential Stuffing | Uses leaked credentials |
| MFA Fatigue | Bombards users with MFA prompts |
| Token Theft | Steals authentication tokens |
| Session Hijacking | Reuses active sessions |
| Pass-the-Hash | Uses stolen NTLM hashes |
| Pass-the-Ticket | Uses stolen Kerberos tickets |
| Golden Ticket | Creates forged Kerberos TGT |
| Evilginx (AiTM) | Steals authenticated sessions |

---

# 🚨 Detection Opportunities

Investigate:

- Multiple failed logins followed by a success.
- Successful logins from unusual countries.
- New device with existing session.
- Administrator role assignments.
- Disabled MFA.
- Excessive privilege changes.
- Dormant account activity.
- Authentication without corresponding user activity.
- Impossible travel.
- Token reuse across different IP addresses.

---

# 🎯 MITRE ATT&CK Mapping

| Observation | Technique |
|-------------|-----------|
| Password Spraying | T1110.003 |
| Credential Stuffing | T1110.004 |
| Valid Accounts | T1078 |
| Steal Web Session Cookie | T1539 |
| Steal Application Access Token | T1528 |
| Pass-the-Hash | T1550.002 |
| Pass-the-Ticket | T1550.003 |
| Forge Kerberos Tickets (Golden Ticket) | T1558.001 |
| Forge Kerberos Tickets (Silver Ticket) | T1558.002 |
| Account Manipulation | T1098 |

---

# 🧪 SOC Investigation Challenge

## Incident

```
08:00

Alice logs into Microsoft 365

↓

MFA Successful

↓

08:07

Azure Role Assignment

↓

Global Administrator

↓

08:09

New OAuth Application Registered

↓

08:12

Mass SharePoint Downloads

↓

08:16

Large HTTPS Upload
```

### Investigation Questions

1. Was Alice's authentication legitimate?
2. Was the device compliant?
3. Did privilege escalation occur?
4. Was the OAuth application malicious?
5. Were authentication tokens stolen?
6. Was data exfiltration attempted?
7. Which logs should you review?
8. Which MITRE techniques apply?
9. Should active sessions be revoked?
10. Would you isolate the endpoint?

---

# ⚡ Quick Revision

✅ Authentication verifies identity.

✅ Authorization grants permissions.

✅ Authentication always occurs before authorization.

✅ Successful authentication does not guarantee authorized access.

✅ Most modern attacks target sessions, tokens, and identities—not just passwords.

✅ Always correlate authentication logs with privilege changes, endpoint telemetry, cloud activity, and network traffic.

---

# 🔗 Knowledge Connections

Authentication
       │
       ├── Passwords
       ├── MFA
       ├── Cookies
       ├── Sessions
       ├── JWT
       ├── OAuth
       ├── SAML
       ├── OIDC
       ├── Kerberos
       ├── NTLM
       ├── Active Directory
       ├── Azure AD / Entra ID
       ├── Identity Protection
       ├── Zero Trust
       ├── Privileged Access
       ├── Session Hijacking
       ├── Token Theft
       ├── Credential Attacks
       └── Incident Response

---

# 🔑 Authentication Tokens - The Digital Identity Card of Modern Applications

> **"Modern attackers don't always steal passwords—they steal tokens."**

---

# 🎯 Purpose

After a user successfully authenticates, most modern applications do **not** ask for the username and password with every request.

Instead, the application issues an **authentication token**.

This token acts as a temporary digital identity card that proves the user has already authenticated.

Every subsequent request includes this token, allowing the server to recognize the user without requiring another login.

Authentication tokens are used by:

- Microsoft 365
- Azure
- AWS
- Google Workspace
- GitHub
- Slack
- Salesforce
- Banking Applications
- Mobile Apps
- APIs
- Cloud Platforms

For SOC analysts, token theft has become one of the most common methods attackers use to bypass passwords and even Multi-Factor Authentication (MFA).

---

# 💡 Simple Analogy

Imagine attending a conference.

At the entrance:

- You show your ID.
- Your ticket is verified.
- You receive a wristband.

After that, nobody asks for your ID again.

The wristband proves you have already been verified.

Authentication Tokens work exactly the same way.

---

# 🌐 Traditional Authentication

```
User

↓

Username

↓

Password

↓

Server

↓

Access Granted

↓

Next Request

↓

Username

↓

Password Again

↓

Server
```

This process would be slow and inefficient.

---

# 🚀 Token-Based Authentication

```
User

↓

Username

↓

Password

↓

MFA

↓

Authentication Successful

↓

Server Issues Token

↓

Browser Stores Token

↓

Every Future Request

↓

Authorization: Bearer <Token>

↓

Server Verifies Token

↓

Access Granted
```

No password is sent after the initial authentication.

---

# 📦 Real HTTP Example

```http
GET /api/profile HTTP/1.1

Host: portal.company.com

Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI...
```

The server reads the token, verifies it, and determines:

- Who the user is.
- Whether the token is valid.
- What permissions the user has.
- Whether the token has expired.

---

# 🧠 What Does a Token Contain?

Many authentication tokens (such as JWTs) contain claims about the authenticated user.

Examples include:

- User ID
- Username
- Email Address
- Role
- Groups
- Issued Time
- Expiration Time
- Issuing Authority

> **Important:** Some tokens are encrypted, while others (like many JWTs) are only Base64-encoded. Base64 is an encoding format, **not encryption**.

---

# 🔍 Common Token Types

| Token | Purpose |
|--------|---------|
| Session Token | Maintains authenticated web sessions |
| JWT (JSON Web Token) | Web applications & APIs |
| OAuth Access Token | Access protected resources |
| OAuth Refresh Token | Obtain new access tokens |
| API Key | Application-to-Application Authentication |
| SAML Assertion | Enterprise Single Sign-On (SSO) |
| Kerberos Ticket | Windows Active Directory Authentication |

Each serves a different purpose but ultimately proves identity or authorization.

---

# 🌍 JWT (JSON Web Token)

A JWT consists of three parts:

```
Header

↓

Payload

↓

Signature
```

Example:

```
xxxxx.yyyyy.zzzzz
```

Each section is separated by a period (`.`).

---

## JWT Header

Contains information about:

- Token Type
- Signing Algorithm

Example:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

---

## JWT Payload

Contains claims such as:

```json
{
  "name":"Alice",
  "role":"Administrator",
  "department":"SOC",
  "exp":1740000000
}
```

The payload is often readable after Base64 decoding.

Never place sensitive information like passwords inside a JWT payload.

---

## JWT Signature

The signature ensures the token has not been modified.

If an attacker changes the payload without generating a valid signature, the server should reject the token.

---

# 🔴 Red Team Perspective

Attackers target tokens because they often eliminate the need to steal passwords.

Common techniques include:

- Token Theft
- JWT Manipulation
- Token Replay
- OAuth Consent Phishing
- Browser Token Extraction
- Memory Dumping
- Session Cookie Theft
- Evilginx (AiTM)
- Refresh Token Abuse

Once an attacker possesses a valid token, they may authenticate as the victim without knowing the password.

---

# 🦠 Malware Spotlight

Many modern malware families search specifically for authentication tokens.

Examples include:

- Lumma Stealer
- RedLine
- Vidar
- StealC
- RisePro
- Raccoon
- AsyncRAT

Targets often include:

- Browser Tokens
- Microsoft 365 Tokens
- Discord Tokens
- GitHub Tokens
- AWS Credentials
- Azure Tokens
- Slack Tokens
- API Keys

---

# 🔵 Blue Team Perspective

Monitor for:

- Token reuse from different IP addresses.
- Impossible travel.
- Token use after password reset.
- Token use after logout.
- Expired tokens still being accepted.
- Multiple devices sharing the same token.
- OAuth applications requesting excessive permissions.
- Refresh token abuse.

Tokens should be treated with the same sensitivity as passwords.

---

# 🛡️ Access Tokens vs Refresh Tokens

## Access Token

- Short-lived.
- Used to access resources.
- Sent with every API request.

Example:

```
Valid for:

60 Minutes
```

---

## Refresh Token

- Long-lived.
- Used to obtain a new Access Token.
- Normally stored securely.
- Not sent with every request.

If a Refresh Token is stolen, an attacker may continuously generate new Access Tokens.

---

# ☁️ OAuth Example

```
User

↓

Login with Microsoft

↓

Microsoft Verifies Identity

↓

Access Token Issued

↓

Application Receives Token

↓

API Access Granted
```

The application never sees the user's password.

---

# 💼 Real SOC Investigation

## Alert

```
Microsoft Entra ID

User:

Alice

Authentication

08:11

India

↓

Access Token

↓

08:47

Same Token

↓

Germany

↓

No New Login

↓

No MFA
```

Identity logs show:

- No password change.
- No authentication event.
- No MFA challenge.

The same Access Token is observed from two different countries.

The investigation reveals:

- An AiTM phishing attack.
- Session cookie theft.
- Token replay.

The attacker bypassed MFA by reusing the stolen token.

---

# 🚨 Detection Opportunities

Investigate:

- Same token from multiple IP addresses.
- Token reuse after logout.
- OAuth consent granted to unknown applications.
- Long-lived refresh tokens.
- New device using an existing token.
- Access token use without recent authentication.
- Impossible travel with identical token identifiers.
- API calls using expired or revoked tokens.

---

# ❌ Common Mistakes

### Mistake 1

"JWTs are encrypted."

False.

Most JWTs are **Base64-encoded**, not encrypted.

Anyone with the token can often decode the payload.

---

### Mistake 2

"MFA prevents token theft."

False.

MFA protects the login process.

If an attacker steals an already authenticated token, they may bypass MFA entirely.

---

### Mistake 3

"Changing the password revokes all tokens."

Not always.

Many platforms require administrators or users to explicitly revoke active sessions or invalidate refresh tokens.

---

# 🎯 MITRE ATT&CK Mapping

| Observation | Technique |
|-------------|-----------|
| Access Token Theft | T1528 – Steal Application Access Token |
| Session Cookie Theft | T1539 – Steal Web Session Cookie |
| Browser Credential Theft | T1555 – Credentials from Password Stores |
| OAuth Abuse | T1098 – Account Manipulation (context dependent) |
| Valid Token Reuse | T1078 – Valid Accounts (context dependent) |

> **Note:** Token replay is often part of a broader intrusion. Always map techniques based on the complete attack chain, not on a single artifact.

---

# 🧪 SOC Investigation Challenge

## Alert

```
Cloud Application

GitHub Enterprise

↓

Access Token Used

↓

Location:

India

↓

8 Minutes Later

↓

Same Token

↓

Location:

Brazil

↓

No Authentication Event

↓

No MFA

↓

Repository Cloned
```

### Investigation Questions

1. Was the token reused?
2. Was a new authentication event generated?
3. Was MFA bypassed?
4. Was an OAuth application involved?
5. Was the token extracted from the browser?
6. Did EDR detect browser credential access?
7. Should all active sessions be revoked?
8. Should API keys also be rotated?
9. Were any repositories modified?
10. What additional logs would you correlate?

---

# ⚡ Quick Revision

✅ Authentication tokens replace repeated password authentication.

✅ Tokens prove identity after a successful login.

✅ JWTs contain a Header, Payload, and Signature.

✅ OAuth commonly uses Access Tokens and Refresh Tokens.

✅ Attackers increasingly target tokens instead of passwords.

✅ Stolen tokens can allow attackers to bypass MFA.

✅ SOC analysts should correlate token usage with authentication logs, EDR telemetry, cloud identity events, and endpoint artifacts.

---

# 🔗 Knowledge Connections

Authentication Tokens
          │
          ├── Cookies
          ├── Sessions
          ├── JWT
          ├── OAuth
          ├── SAML
          ├── OpenID Connect (OIDC)
          ├── API Authentication
          ├── Microsoft Entra ID
          ├── AWS IAM
          ├── Google Workspace
          ├── Browser Forensics
          ├── Identity Protection
          ├── Session Hijacking
          ├── Token Replay
          ├── AiTM Phishing
          ├── Cloud Security
          └── Incident Response
