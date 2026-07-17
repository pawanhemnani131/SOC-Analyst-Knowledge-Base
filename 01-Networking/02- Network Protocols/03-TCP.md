---

# 📖 What is TCP?

> **"TCP is a connection-oriented protocol that ensures data is delivered accurately, completely, and in the correct order—even if the network is unreliable."**

---

# 🎯 Purpose

The **Transmission Control Protocol (TCP)** is one of the core communication protocols of the Internet.

Its primary responsibility is to provide **reliable, ordered, and error-checked delivery of data** between two devices.

Unlike protocols that simply send data and hope it arrives, TCP establishes a connection, tracks every byte transmitted, detects missing data, and retransmits lost packets when necessary.

This reliability is why TCP is used by applications where accuracy is more important than speed.

---

# 🌍 Why Was TCP Invented?

Imagine the early Internet.

Networks were unreliable.

Packets could:

- Be lost.
- Arrive out of order.
- Be duplicated.
- Become corrupted.
- Never reach their destination.

Applications needed a protocol that could guarantee:

- Every packet arrives.
- Packets arrive in the correct order.
- Missing packets are retransmitted.
- Duplicate packets are ignored.
- Both devices know the connection status.

TCP was designed to solve exactly these problems.

It transformed unreliable packet delivery into reliable communication.

---

# 💡 Real World Analogy

Imagine sending a **100-page legal contract** through the postal service.

Instead of placing all 100 pages into one envelope,

you number every page:

```
Page 1

Page 2

Page 3

...

Page 100
```

When the recipient receives the package, they check:

- Are all pages present?
- Are the pages in order?
- Is anything missing?
- Is there a duplicate page?

If page 47 is missing,

they request only page 47 again instead of resending the entire contract.

TCP works in a very similar way.

It divides data into smaller pieces, numbers them, verifies delivery, and requests retransmission only for missing data.

---

# 🏢 Enterprise Example

An employee uploads a confidential PDF to the company's document management system.

```
Employee Laptop

↓

HTTPS

↓

TCP

↓

Corporate Server
```

The PDF is divided into many TCP segments.

TCP ensures:

- Every segment reaches the server.
- Segments are reassembled correctly.
- Missing segments are retransmitted.
- Duplicate segments are discarded.

The uploaded document is reconstructed exactly as it was sent.

---

# 🤔 Why Is TCP So Reliable?

TCP achieves reliability by using several mechanisms that we'll explore throughout this chapter:

- Connection establishment.
- Sequence numbers.
- Acknowledgments (ACKs).
- Retransmissions.
- Flow control.
- Congestion control.
- Checksums.
- Ordered delivery.

Each mechanism solves a specific networking problem.

Together, they make TCP highly dependable.

---

# 📦 TCP Does More Than Deliver Data

TCP also manages:

- Connection setup.
- Connection termination.
- Packet ordering.
- Error detection.
- Traffic regulation.
- Communication state.

Think of TCP as a traffic controller rather than just a delivery service.

---

# 📡 Common Protocols That Use TCP

Many protocols depend on TCP because they require reliable communication.

| Protocol | Default Port | Purpose |
|-----------|-------------:|---------|
| HTTP | 80 | Web browsing |
| HTTPS | 443 | Secure web traffic |
| SSH | 22 | Secure remote administration |
| FTP | 21 | File transfer |
| SMTP | 25 | Email sending |
| IMAP | 143 / 993 | Email retrieval |
| POP3 | 110 / 995 | Email retrieval |
| SMB | 445 | File sharing |
| RDP | 3389 | Remote Desktop |
| LDAP | 389 | Directory services |

Whenever you investigate these protocols,

you're also investigating TCP.

---

# 🔴 Red Team Perspective

Attackers rely heavily on TCP because it provides stable communication.

Common attacker activities using TCP include:

- Reverse shells.
- Command & Control (C2) channels.
- Remote Desktop abuse.
- SMB lateral movement.
- Data exfiltration.
- Malware downloads.
- Beaconing over HTTPS.
- File transfers.

A compromised endpoint often communicates with attacker infrastructure using TCP-based protocols.

---

# 🔵 Blue Team Perspective

SOC analysts monitor TCP traffic to identify:

- Unauthorized connections.
- Suspicious ports.
- Failed connection attempts.
- Beaconing behavior.
- Large outbound data transfers.
- Lateral movement.
- Long-lived sessions.
- Unexpected remote administration.

TCP telemetry is one of the richest sources of evidence during an investigation.

---

# 💼 Real SOC Investigation

## Incident

An EDR alert reports:

```
powershell.exe

↓

Network Connection

↓

185.231.xxx.xxx

↓

TCP Port 443
```

At first glance,

the traffic appears normal because it uses HTTPS.

However,

the analyst notices:

- The destination is not a trusted domain.
- Connections occur every 60 seconds.
- Each session transfers only a few hundred bytes.
- The pattern repeats for several hours.

These characteristics strongly suggest **Command & Control beaconing**.

TCP connection data becomes the starting point for a deeper investigation.

---

# 🛡️ Detection Opportunities

SOC teams should investigate:

- Unusual outbound TCP connections.
- Rare destination ports.
- Repeated failed connection attempts.
- Persistent long-duration sessions.
- High-volume outbound transfers.
- Internal systems communicating over unexpected ports.

Always correlate TCP activity with:

- DNS logs.
- Firewall logs.
- EDR telemetry.
- Proxy logs.
- Authentication events.

---

# ❌ Common Misconceptions

### Mistake 1

"TCP is faster than UDP."

False.

TCP prioritizes **reliability**, not speed.

The additional mechanisms that make TCP reliable also introduce overhead.

---

### Mistake 2

"TCP guarantees instant delivery."

False.

TCP guarantees reliable delivery—not immediate delivery.

Network congestion and retransmissions can introduce delays.

---

### Mistake 3

"If a TCP connection exists, the traffic is legitimate."

False.

Attackers commonly use TCP for malware, C2, phishing, and lateral movement.

Always evaluate the context of the connection.

---

# 🎯 MITRE ATT&CK Mapping

TCP is a transport protocol, not an attack technique.

However, it carries traffic associated with many ATT&CK techniques:

| Activity | MITRE Technique |
|-----------|-----------------|
| Command & Control | T1071 |
| Remote Services | T1021 |
| Data Exfiltration | T1041 |
| Ingress Tool Transfer | T1105 |

---

# ⚡ Quick Revision

✅ TCP provides reliable, ordered, and error-checked communication.

✅ It establishes a connection before transmitting data.

✅ It detects missing packets and retransmits them.

✅ Most enterprise protocols rely on TCP.

✅ TCP telemetry is essential during SOC investigations.

# 🔗 Knowledge Connection

TCP
 │
 ├── HTTP / HTTPS
 ├── DNS
 ├── TLS
 ├── SSH
 ├── SMB
 ├── RDP
 ├── Wireshark
 ├── Firewall Logs
 ├── EDR
 ├── Threat Hunting
 └── MITRE ATT&CK

---

# 🤔 Why TCP Exists - Solving the Problems of IP

> **"IP knows how to deliver packets. TCP knows how to deliver data reliably."**

---

# 🎯 Purpose

To understand why TCP exists, we first need to understand what **IP (Internet Protocol)** does—and more importantly, what it **doesn't** do.

Many beginners assume that once a packet has an IP address, it will always reach its destination correctly.

Unfortunately, networks don't work that way.

Packets can:

- Be lost
- Arrive out of order
- Be duplicated
- Be delayed
- Become corrupted
- Never arrive at all

IP makes a **best effort** to deliver packets, but it provides **no guarantee** that they will reach their destination.

This is the problem that TCP was designed to solve.

---

# 🌍 Understanding the Problem

Imagine sending 10 packets across the Internet.

```
Packet 1
Packet 2
Packet 3
Packet 4
Packet 5
Packet 6
Packet 7
Packet 8
Packet 9
Packet 10
```

Ideally, they arrive exactly like this.

```
1
2
3
4
5
6
7
8
9
10
```

But real networks are unpredictable.

They may arrive like this:

```
1
2
5
6
3
4
8
10
9
```

Or perhaps:

```
1
2
3
5
6
8
9
```

Packets 4, 7, and 10 never arrive.

IP doesn't know they're missing.

It simply forwards packets as best it can.

---

# 💡 Real World Analogy

Imagine a courier company delivering ten boxes.

The courier promises only one thing:

> **"We'll try our best."**

They do **not** promise:

- Every box will arrive.
- Boxes will arrive in order.
- Missing boxes will be resent.
- Damaged boxes will be replaced.

That is essentially how IP works.

Now imagine a premium courier service.

They provide:

✅ Tracking

✅ Delivery confirmation

✅ Missing package replacement

✅ Correct delivery order

That premium service is TCP.

---

# 🏢 Enterprise Example

A company transfers a **2 GB financial database** from one server to another.

Without TCP:

```
Database

↓

Network

↓

Some packets lost

↓

Corrupted Database
```

With TCP:

```
Database

↓

TCP Segments

↓

Lost Segment Detected

↓

Retransmission

↓

Database Successfully Reassembled
```

The transfer completes successfully because TCP detects and recovers from transmission errors.

---

# ⚠️ Problems TCP Solves

TCP was specifically designed to solve several networking challenges.

### 📦 Packet Loss

A packet disappears during transmission.

TCP detects the missing data and requests it again.

---

### 🔀 Out-of-Order Delivery

Packets arrive in the wrong order.

TCP rearranges them before passing the data to the application.

---

### 📄 Duplicate Packets

Sometimes the same packet arrives twice.

TCP identifies duplicate sequence numbers and ignores the extra copy.

---

### 🚦 Congestion

Too much traffic causes network congestion.

TCP automatically slows down transmission to reduce packet loss.

---

### 📈 Flow Control

A fast sender may overwhelm a slow receiver.

TCP adjusts its transmission speed based on the receiver's capacity.

---

# 🔴 Red Team Perspective

Attackers understand how TCP behaves.

They exploit it for:

- Reliable Command & Control communication.
- Stable reverse shells.
- Remote administration.
- Long-lived encrypted sessions.
- Data exfiltration.

TCP's reliability makes it attractive for both legitimate applications and malicious operations.

---

# 🔵 Blue Team Perspective

SOC analysts understand that reliable communication leaves reliable evidence.

TCP allows analysts to observe:

- Connection establishment.
- Session duration.
- Bytes transferred.
- Retransmissions.
- Connection failures.
- Long-lived sessions.
- Unexpected destinations.

This telemetry helps reconstruct attacker activity.

---

# 💼 Real SOC Investigation

## Alert

A SIEM generates an alert for unusual outbound traffic.

Firewall logs show:

```
Source

↓

FINANCE-PC-08

↓

Destination

↓

198.51.100.25

↓

TCP 443

↓

Data Sent

↓

2.8 GB
```

The analyst asks:

- Was the transfer successful?
- Were packets retransmitted?
- Did the connection remain stable?
- Was encryption used?
- Is this legitimate business activity?

Because TCP maintains connection state,

the analyst can answer these questions using firewall logs, packet captures, and EDR telemetry.

---

# 🛡️ Detection Opportunities

Look for:

- Excessive retransmissions.
- Abnormally long TCP sessions.
- Large outbound transfers.
- Repeated failed TCP connections.
- Unexpected connections to uncommon destinations.
- Persistent encrypted sessions with unknown hosts.

These patterns often indicate malware, beaconing, or data exfiltration.

---

# ❌ Common Misconceptions

### Mistake 1

"IP guarantees delivery."

False.

IP only attempts to deliver packets.

It provides no delivery guarantee.

---

### Mistake 2

"TCP prevents attacks."

False.

TCP ensures reliable communication.

It does not determine whether the communication is legitimate or malicious.

---

### Mistake 3

"If packets arrive, the application always works."

False.

Applications still depend on proper sequencing, acknowledgments, and error recovery—all handled by TCP.

---

# 🎯 MITRE ATT&CK Mapping

TCP itself is not an attack technique.

However, attackers commonly use TCP for:

| Activity | MITRE Technique |
|-----------|-----------------|
| Command & Control | T1071 |
| Exfiltration Over C2 Channel | T1041 |
| Remote Services | T1021 |
| Ingress Tool Transfer | T1105 |

---

# ⚡ Quick Revision

✅ IP provides packet delivery but offers no guarantees.

✅ TCP adds reliability on top of IP.

✅ TCP detects lost, duplicate, and out-of-order packets.

✅ Flow control and congestion control improve communication reliability.

✅ Reliable communication also provides valuable evidence during SOC investigations.

# 🔗 Knowledge Connections

Why TCP Exists
        │
        ├── IP
        ├── Packet Loss
        ├── Sequence Numbers
        ├── Acknowledgments
        ├── Retransmissions
        ├── Flow Control
        ├── Congestion Control
        ├── Wireshark
        ├── Firewall Logs
        └── SOC Investigation
---

# 🤔 Why TCP Exists - Solving the Problems of IP

> **"IP knows how to deliver packets. TCP knows how to deliver data reliably."**

---

# 🎯 Purpose

To understand why TCP exists, we first need to understand what **IP (Internet Protocol)** does—and more importantly, what it **doesn't** do.

Many beginners assume that once a packet has an IP address, it will always reach its destination correctly.

Unfortunately, networks don't work that way.

Packets can:

- Be lost
- Arrive out of order
- Be duplicated
- Be delayed
- Become corrupted
- Never arrive at all

IP makes a **best effort** to deliver packets, but it provides **no guarantee** that they will reach their destination.

This is the problem that TCP was designed to solve.

---

# 🌍 Understanding the Problem

Imagine sending 10 packets across the Internet.

```
Packet 1
Packet 2
Packet 3
Packet 4
Packet 5
Packet 6
Packet 7
Packet 8
Packet 9
Packet 10
```

Ideally, they arrive exactly like this.

```
1
2
3
4
5
6
7
8
9
10
```

But real networks are unpredictable.

They may arrive like this:

```
1
2
5
6
3
4
8
10
9
```

Or perhaps:

```
1
2
3
5
6
8
9
```

Packets 4, 7, and 10 never arrive.

IP doesn't know they're missing.

It simply forwards packets as best it can.

---

# 💡 Real World Analogy

Imagine a courier company delivering ten boxes.

The courier promises only one thing:

> **"We'll try our best."**

They do **not** promise:

- Every box will arrive.
- Boxes will arrive in order.
- Missing boxes will be resent.
- Damaged boxes will be replaced.

That is essentially how IP works.

Now imagine a premium courier service.

They provide:

✅ Tracking

✅ Delivery confirmation

✅ Missing package replacement

✅ Correct delivery order

That premium service is TCP.

---

# 🏢 Enterprise Example

A company transfers a **2 GB financial database** from one server to another.

Without TCP:

```
Database

↓

Network

↓

Some packets lost

↓

Corrupted Database
```

With TCP:

```
Database

↓

TCP Segments

↓

Lost Segment Detected

↓

Retransmission

↓

Database Successfully Reassembled
```

The transfer completes successfully because TCP detects and recovers from transmission errors.

---

# ⚠️ Problems TCP Solves

TCP was specifically designed to solve several networking challenges.

### 📦 Packet Loss

A packet disappears during transmission.

TCP detects the missing data and requests it again.

---

### 🔀 Out-of-Order Delivery

Packets arrive in the wrong order.

TCP rearranges them before passing the data to the application.

---

### 📄 Duplicate Packets

Sometimes the same packet arrives twice.

TCP identifies duplicate sequence numbers and ignores the extra copy.

---

### 🚦 Congestion

Too much traffic causes network congestion.

TCP automatically slows down transmission to reduce packet loss.

---

### 📈 Flow Control

A fast sender may overwhelm a slow receiver.

TCP adjusts its transmission speed based on the receiver's capacity.

---

# 🔴 Red Team Perspective

Attackers understand how TCP behaves.

They exploit it for:

- Reliable Command & Control communication.
- Stable reverse shells.
- Remote administration.
- Long-lived encrypted sessions.
- Data exfiltration.

TCP's reliability makes it attractive for both legitimate applications and malicious operations.

---

# 🔵 Blue Team Perspective

SOC analysts understand that reliable communication leaves reliable evidence.

TCP allows analysts to observe:

- Connection establishment.
- Session duration.
- Bytes transferred.
- Retransmissions.
- Connection failures.
- Long-lived sessions.
- Unexpected destinations.

This telemetry helps reconstruct attacker activity.

---

# 💼 Real SOC Investigation

## Alert

A SIEM generates an alert for unusual outbound traffic.

Firewall logs show:

```
Source

↓

FINANCE-PC-08

↓

Destination

↓

198.51.100.25

↓

TCP 443

↓

Data Sent

↓

2.8 GB
```

The analyst asks:

- Was the transfer successful?
- Were packets retransmitted?
- Did the connection remain stable?
- Was encryption used?
- Is this legitimate business activity?

Because TCP maintains connection state,

the analyst can answer these questions using firewall logs, packet captures, and EDR telemetry.

---

# 🛡️ Detection Opportunities

Look for:

- Excessive retransmissions.
- Abnormally long TCP sessions.
- Large outbound transfers.
- Repeated failed TCP connections.
- Unexpected connections to uncommon destinations.
- Persistent encrypted sessions with unknown hosts.

These patterns often indicate malware, beaconing, or data exfiltration.

---

# ❌ Common Misconceptions

### Mistake 1

"IP guarantees delivery."

False.

IP only attempts to deliver packets.

It provides no delivery guarantee.

---

### Mistake 2

"TCP prevents attacks."

False.

TCP ensures reliable communication.

It does not determine whether the communication is legitimate or malicious.

---

### Mistake 3

"If packets arrive, the application always works."

False.

Applications still depend on proper sequencing, acknowledgments, and error recovery—all handled by TCP.

---

# 🎯 MITRE ATT&CK Mapping

TCP itself is not an attack technique.

However, attackers commonly use TCP for:

| Activity | MITRE Technique |
|-----------|-----------------|
| Command & Control | T1071 |
| Exfiltration Over C2 Channel | T1041 |
| Remote Services | T1021 |
| Ingress Tool Transfer | T1105 |

---

# ⚡ Quick Revision

✅ IP provides packet delivery but offers no guarantees.

✅ TCP adds reliability on top of IP.

✅ TCP detects lost, duplicate, and out-of-order packets.

✅ Flow control and congestion control improve communication reliability.

✅ Reliable communication also provides valuable evidence during SOC investigations.

# 🔗 Knowledge Connections

Why TCP Exists
        │
        ├── IP
        ├── Packet Loss
        ├── Sequence Numbers
        ├── Acknowledgments
        ├── Retransmissions
        ├── Flow Control
        ├── Congestion Control
        ├── Wireshark
        ├── Firewall Logs
        └── SOC Investigation
---

# ⚔️ TCP vs UDP - Understanding the Two Transport Protocols

> **"TCP asks, 'Did you receive the data?' UDP simply says, 'Here it is,' and moves on."**

---

# 🎯 Purpose

The two most common transport layer protocols are:

- **TCP (Transmission Control Protocol)**
- **UDP (User Datagram Protocol)**

Both are responsible for transporting data between devices.

However, they are designed with different priorities.

**TCP prioritizes reliability.**

**UDP prioritizes speed.**

Understanding the difference is essential because almost every network application uses one of these two protocols.

---

# 🌍 Why Do We Need Two Different Protocols?

Imagine you're sending information to someone.

Sometimes you need absolute certainty that every piece of information arrives correctly.

Other times, speed is far more important than perfection.

This is why both TCP and UDP exist.

---

# 💡 Real World Analogy

### 📦 TCP - Courier Service

Imagine sending an expensive laptop.

The courier provides:

- Tracking number
- Signature on delivery
- Delivery confirmation
- Insurance
- Lost package replacement

You know exactly when it was delivered.

This is TCP.

Reliable, but slightly slower because every step is verified.

---

### 📮 UDP - Regular Postcard

Now imagine sending a postcard.

You simply drop it into the mailbox.

There is:

- No tracking
- No delivery confirmation
- No signature
- No guarantee it arrives

It is much faster and requires very little overhead.

This is UDP.

---

# 🏢 Enterprise Example

A company has two applications.

### Example 1

An employee uploads payroll data.

```
Employee Laptop

↓

HTTPS

↓

TCP

↓

Payroll Server
```

Every byte must arrive correctly.

Missing data could corrupt payroll records.

TCP is the correct choice.

---

### Example 2

Employees join a Microsoft Teams meeting.

```
Employee

↓

Voice Packets

↓

UDP

↓

Meeting Server
```

If one voice packet is lost,

the conversation continues naturally.

Waiting for retransmissions would introduce noticeable delays.

UDP is the better choice.

---

# 📊 Feature Comparison

| Feature | TCP | UDP |
|----------|-----|-----|
| Connection-Oriented | ✅ Yes | ❌ No |
| Reliable Delivery | ✅ Yes | ❌ No |
| Packet Ordering | ✅ Yes | ❌ No |
| Error Recovery | ✅ Yes | ❌ No |
| Retransmissions | ✅ Yes | ❌ No |
| Flow Control | ✅ Yes | ❌ No |
| Congestion Control | ✅ Yes | ❌ No |
| Speed | Slower | Faster |
| Header Size | Larger | Smaller |
| Best For | Accuracy | Low Latency |

---

# 📡 Common Protocols

## Protocols That Use TCP

| Protocol | Default Port |
|-----------|-------------:|
| HTTP | 80 |
| HTTPS | 443 |
| SSH | 22 |
| FTP | 21 |
| SMTP | 25 |
| SMB | 445 |
| LDAP | 389 |
| RDP | 3389 |

These protocols require reliable communication.

---

## Protocols That Use UDP

| Protocol | Default Port |
|-----------|-------------:|
| DNS | 53 |
| DHCP | 67 / 68 |
| TFTP | 69 |
| SNMP | 161 |
| NTP | 123 |
| RIP | 520 |

These protocols prioritize speed and low overhead.

---

# ⚠️ Important Note

Some protocols use **both TCP and UDP**.

A good example is DNS.

Normally:

```
DNS Query

↓

UDP 53
```

However, DNS switches to TCP when:

- Zone transfers occur.
- Responses are too large for UDP.
- DNSSEC responses exceed UDP limits.
- A response is truncated.

This is why understanding both transport protocols is important.

---

# 🔴 Red Team Perspective

Attackers choose the protocol based on their objectives.

### TCP is commonly used for:

- Reverse shells
- HTTPS Command & Control
- Data exfiltration
- Malware downloads
- SMB lateral movement
- Remote Desktop abuse

---

### UDP is commonly used for:

- DNS Tunneling
- Reflection attacks
- Amplification attacks
- Custom malware communication
- VoIP interception

Choosing the appropriate protocol helps attackers balance stealth, reliability, and speed.

---

# 🔵 Blue Team Perspective

SOC analysts examine transport protocols to understand application behavior.

Questions include:

- Is TCP expected for this service?
- Why is UDP being used here?
- Is the destination port normal?
- Are retransmissions excessive?
- Are UDP packets unusually large?
- Is DNS unexpectedly using TCP?

These observations often provide valuable investigation leads.

---

# 💼 Real SOC Investigation

## Incident

A workstation begins communicating with an unknown external IP.

Logs show:

```
Protocol

↓

UDP

↓

Destination Port

↓

53

↓

Thousands of Packets

↓

Large Packet Sizes
```

DNS normally uses UDP,

but the unusually high volume and packet size raise suspicion.

Further investigation reveals **DNS Tunneling** being used for data exfiltration.

The transport protocol becomes a key indicator during the investigation.

---

# 🛡️ Detection Opportunities

Monitor for:

### TCP

- Long-lived sessions
- Excessive retransmissions
- Unexpected outbound connections
- Large data transfers
- Beaconing over HTTPS

---

### UDP

- High DNS query rates
- Large UDP payloads
- Reflection attack patterns
- Excessive NTP traffic
- Unexpected UDP services

Different transport protocols produce different behavioral patterns.

---

# ❌ Common Misconceptions

### Mistake 1

"UDP is worse than TCP."

False.

UDP is the preferred choice for many real-time applications where low latency is more important than guaranteed delivery.

---

### Mistake 2

"TCP is always the better option."

False.

Using TCP for voice or video would introduce unnecessary delays due to acknowledgments and retransmissions.

---

### Mistake 3

"Protocols use only one transport protocol."

False.

Some protocols, such as DNS, can use both TCP and UDP depending on the situation.

---

# 🎯 MITRE ATT&CK Mapping

Attackers commonly leverage both TCP and UDP.

| Activity | MITRE Technique |
|-----------|-----------------|
| Application Layer Protocol | T1071 |
| DNS Tunneling | T1071.004 |
| Data Exfiltration | T1041 |
| Command & Control | T1071 |

---

# ⚡ Quick Revision

✅ TCP prioritizes reliability and ordered delivery.

✅ UDP prioritizes speed and low latency.

✅ HTTP, HTTPS, SSH, and SMB rely on TCP.

✅ DNS and DHCP primarily use UDP.

✅ Some protocols, including DNS, can use both depending on the situation.

# 🔗 Knowledge Connections

TCP vs UDP
      │
      ├── HTTP / HTTPS
      ├── DNS
      ├── DHCP
      ├── VoIP
      ├── SMB
      ├── SSH
      ├── DNS Tunneling
      ├── Beaconing
      ├── Wireshark
      └── SOC Investigation
---

# 📦 TCP Segments - Breaking Data into Smaller Pieces

> **"TCP doesn't send a file as one giant block. It divides the data into smaller segments so they can travel efficiently across the network."**

---

# 🎯 Purpose

When an application sends data,

TCP does **not** transmit everything at once.

Instead, TCP breaks the data into **smaller units called Segments**.

Each segment contains:

- Part of the actual data
- A TCP Header
- Sequence Numbers
- Port Numbers
- Flags
- Checksum
- Other control information

Breaking data into segments makes communication:

- More reliable
- Easier to recover from errors
- Faster to retransmit if data is lost
- More efficient across different network types

---

# 🌍 Why Can't We Send Everything at Once?

Imagine downloading a **5 GB movie**.

Without segmentation,

the sender would have to transmit:

```
5 GB

↓

One Huge Block
```

If only **one byte** was lost,

the entire 5 GB file would need to be sent again.

That would waste enormous amounts of bandwidth and time.

Instead,

TCP divides the movie into thousands of small segments.

If one segment is lost,

only that segment is retransmitted.

---

# 💡 Real World Analogy

Imagine moving a library to a new house.

Would you carry all 500 books in one trip?

Of course not.

Instead,

you pack them into many boxes.

```
Box 1

↓

Books 1-20

↓

Delivered
```

```
Box 2

↓

Books 21-40

↓

Delivered
```

```
Box 3

↓

Books 41-60

↓

Lost
```

You don't move the entire library again.

You only replace **Box 3**.

TCP Segmentation works exactly like this.

---

# 🏢 Enterprise Example

An employee uploads a **150 MB PowerPoint presentation**.

Instead of sending:

```
150 MB

↓

One Transmission
```

TCP divides it into thousands of segments.

```
Segment 1

↓

Segment 2

↓

Segment 3

↓

Segment 4

↓

...

↓

Segment N
```

Each segment travels independently through the network.

The destination later rebuilds the original file in the correct order.

---

# 📦 What Does a TCP Segment Contain?

A TCP Segment has two major parts:

```
+----------------------+
| TCP Header           |
+----------------------+
| Application Data     |
+----------------------+
```

The **TCP Header** contains important information such as:

- Source Port
- Destination Port
- Sequence Number
- Acknowledgment Number
- TCP Flags
- Window Size
- Checksum

The **Payload** contains the actual application data.

For example:

- HTTP Request
- HTTPS Data
- Email
- SMB File
- SSH Commands

---

# 📚 Segment vs Packet vs Frame

One of the most common networking interview questions is:

> "What is the difference between a Segment, Packet, and Frame?"

The answer depends on the OSI layer.

| OSI Layer | Data Unit |
|-----------|-----------|
| Application | Data |
| Transport | Segment (TCP) / Datagram (UDP) |
| Network | Packet |
| Data Link | Frame |
| Physical | Bits |

Think of it like packaging a parcel.

```
Application

↓

Your Letter
```

↓

```
TCP

↓

Places the Letter into an Envelope
```

↓

```
IP

↓

Adds Destination Address
```

↓

```
Ethernet

↓

Places Everything into a Shipping Box
```

↓

```
Electrical Signals

↓

Sent Across the Cable
```

Each layer adds its own information before passing the data to the next layer.

---

# 🔴 Red Team Perspective

Attackers understand segmentation well.

Large malware payloads,

reverse shells,

and data exfiltration

are all transmitted as many TCP segments.

They know that:

- Payloads are reconstructed at the destination.
- Encryption protects the payload.
- Multiple segments appear as one communication session.

This makes packet-level analysis important during investigations.

---

# 🔵 Blue Team Perspective

SOC analysts inspect TCP segments to identify:

- Missing segments
- Retransmissions
- Fragmented malware downloads
- Suspicious file transfers
- Beacon traffic
- Abnormal session behavior

A packet capture often reveals much more than application logs alone.

---

# 💼 Real SOC Investigation

## Incident

An EDR alert indicates:

```
powershell.exe

↓

HTTPS

↓

Downloaded Payload
```

The SOC analyst opens the PCAP.

Instead of seeing:

```
malware.exe
```

they observe:

```
Segment 1

↓

Segment 2

↓

Segment 3

↓

...

↓

Segment 258
```

Wireshark automatically reassembles these TCP segments,

allowing the analyst to reconstruct the downloaded file.

Without TCP segmentation,

this would not be possible.

---

# 🛡️ Detection Opportunities

Watch for:

- Excessive retransmissions
- Extremely large TCP sessions
- Abnormal segment sizes
- Unexpected file transfers
- Fragmented malware downloads
- Long-lived encrypted sessions

Segment-level analysis provides valuable insight into attacker activity.

---

# ❌ Common Misconceptions

### Mistake 1

"A segment is the same as a packet."

False.

A segment exists at the **Transport Layer**.

A packet exists at the **Network Layer**.

---

### Mistake 2

"Applications create TCP segments."

False.

Applications generate data.

TCP is responsible for dividing that data into segments.

---

### Mistake 3

"If one segment is lost, the entire file must be retransmitted."

False.

TCP only retransmits the missing segment.

---

# 🎯 MITRE ATT&CK Mapping

Segmentation itself is not an ATT&CK technique,

but analysts observe TCP segments during investigations involving:

| Activity | Technique |
|-----------|-----------|
| Command & Control | T1071 |
| Data Exfiltration | T1041 |
| Tool Transfer | T1105 |
| Remote Services | T1021 |

---

# ⚡ Quick Revision

✅ TCP divides data into smaller segments.

✅ Each segment contains a header and payload.

✅ Lost segments are retransmitted individually.

✅ Segmentation improves reliability and efficiency.

✅ Wireshark can reassemble TCP segments into the original data stream.

# 🔗 Knowledge Connections

TCP Segments
      │
      ├── Sequence Numbers
      ├── ACK
      ├── TCP Header
      ├── Ports
      ├── Wireshark
      ├── HTTP
      ├── HTTPS
      ├── SMB
      ├── File Transfers
      └── SOC Investigation
---

# 📋 TCP Header - Understanding Every Field

> **"Every TCP segment carries a header that tells the receiver who sent the data, where it's going, how much has been sent, what should happen next, and whether everything arrived correctly."**

---

# 🎯 Purpose

When TCP sends data across a network,

it doesn't send only the application data.

It first adds a **TCP Header**.

Think of the TCP Header as an instruction sheet attached to every TCP segment.

It tells the receiving device:

- Who sent the data
- Who should receive it
- Which application should process it
- Which data comes first
- Which data has already been received
- Whether the connection is starting or ending
- Whether retransmission is needed

Without the TCP Header,

reliable communication would not be possible.

---

# 🌍 TCP Header Structure

A TCP Header is **at least 20 bytes long**.

If optional fields are present,

it can become larger.

A simplified view looks like this:

```
+------------------------------------------------------+
| Source Port | Destination Port                      |
+------------------------------------------------------+
| Sequence Number                                    |
+------------------------------------------------------+
| Acknowledgment Number                              |
+------------------------------------------------------+
| Header Length | Flags | Window Size                |
+------------------------------------------------------+
| Checksum | Urgent Pointer                          |
+------------------------------------------------------+
| Options (Optional)                                 |
+------------------------------------------------------+
| Application Data (Payload)                         |
+------------------------------------------------------+
```

Every TCP segment contains this header before the actual data.

---

# 📌 Source Port

The **Source Port** identifies the application that sent the data.

Example:

```
Google Chrome

↓

Source Port

↓

52341
```

The operating system usually selects this port automatically.

These temporary ports are called **Ephemeral Ports**.

Example:

```
52341

49180

61522

55001
```

Each new connection typically receives a different ephemeral port.

---

# 📌 Destination Port

The **Destination Port** identifies the service that should receive the data.

Example:

```
HTTPS

↓

443
```

```
SSH

↓

22
```

```
RDP

↓

3389
```

```
SMB

↓

445
```

The destination port tells the receiving computer which application should process the incoming data.

---

# 🏢 Enterprise Example

An employee visits:

```
https://company.com
```

The connection may look like:

```
Source IP:

10.20.15.45

↓

Source Port:

52184

↓

Destination IP:

198.51.100.10

↓

Destination Port:

443
```

The browser randomly selected port **52184**,

while the web server listens on **443**.

---

# 🧠 SOC Insight

Source ports change frequently.

Destination ports usually indicate **which service is being accessed**.

When reviewing logs,

analysts often pay closer attention to the **destination port** because it provides context about the type of communication.

Examples:

- Port 22 → SSH
- Port 80 → HTTP
- Port 443 → HTTPS
- Port 445 → SMB
- Port 3389 → Remote Desktop

Unexpected destination ports may indicate unauthorized services or malware communication.

---

# 💼 Real SOC Investigation

A firewall alert shows:

```
Source

↓

FINANCE-PC-04

↓

52318

↓

Destination

↓

203.0.113.55

↓

4444
```

The analyst immediately notices:

```
Destination Port

↓

4444
```

This is **not** a common enterprise service.

Further investigation reveals that a penetration testing framework is using TCP port 4444 for a reverse shell.

The destination port becomes the first clue that the connection deserves investigation.

---

# 🔴 Red Team Perspective

Attackers often communicate over:

- Standard ports (80, 443) to blend into normal traffic.
- Non-standard ports (4444, 8081, 9001, etc.) for custom services or malware.

Modern malware increasingly prefers **TCP 443** because encrypted HTTPS traffic is less likely to be blocked or inspected.

Remember:

**A legitimate port does not guarantee legitimate activity.**

---

# 🔵 Blue Team Perspective

SOC analysts should monitor for:

- Rare destination ports.
- Unexpected services.
- Internal systems exposing unusual listening ports.
- New outbound connections to uncommon ports.
- Large volumes of traffic on ports that are rarely used in the environment.

Port context is one piece of the investigation—not the entire story.

Always correlate with:

- Process information
- DNS logs
- EDR telemetry
- Firewall logs
- Proxy logs

---

# ❌ Common Misconceptions

### Mistake 1

"Port 443 means the traffic is safe."

False.

Many malware families use HTTPS to communicate with Command & Control servers.

Always investigate the destination, certificate, process, and behavior.

---

### Mistake 2

"The source port identifies the service."

False.

The source port usually identifies the client application and is often assigned dynamically.

The destination port typically identifies the service being accessed.

---

### Mistake 3

"Malware always uses unusual ports."

False.

Sophisticated malware often uses ports such as **80** and **443** to blend in with normal web traffic.

---

# ⚡ Quick Revision

✅ Every TCP segment begins with a TCP Header.

✅ The Source Port identifies the sending application.

✅ The Destination Port identifies the receiving service.

✅ Client systems usually use ephemeral source ports.

✅ Destination ports provide valuable context during investigations.

# 🔗 Knowledge Connections

TCP Header
      │
      ├── Source Port
      ├── Destination Port
      ├── Ephemeral Ports
      ├── HTTP
      ├── HTTPS
      ├── SSH
      ├── SMB
      ├── Firewall Logs
      ├── Wireshark
      └── SOC Investigation
---

# 📋 TCP Header - Understanding Every Field

> **"Every TCP segment carries a header that tells the receiver who sent the data, where it's going, how much has been sent, what should happen next, and whether everything arrived correctly."**

---

# 🎯 Purpose

When TCP sends data across a network,

it doesn't send only the application data.

It first adds a **TCP Header**.

Think of the TCP Header as an instruction sheet attached to every TCP segment.

It tells the receiving device:

- Who sent the data
- Who should receive it
- Which application should process it
- Which data comes first
- Which data has already been received
- Whether the connection is starting or ending
- Whether retransmission is needed

Without the TCP Header,

reliable communication would not be possible.

---

# 🌍 TCP Header Structure

A TCP Header is **at least 20 bytes long**.

If optional fields are present,

it can become larger.

A simplified view looks like this:

```
+------------------------------------------------------+
| Source Port | Destination Port                      |
+------------------------------------------------------+
| Sequence Number                                    |
+------------------------------------------------------+
| Acknowledgment Number                              |
+------------------------------------------------------+
| Header Length | Flags | Window Size                |
+------------------------------------------------------+
| Checksum | Urgent Pointer                          |
+------------------------------------------------------+
| Options (Optional)                                 |
+------------------------------------------------------+
| Application Data (Payload)                         |
+------------------------------------------------------+
```

Every TCP segment contains this header before the actual data.

---

# 📌 Source Port

The **Source Port** identifies the application that sent the data.

Example:

```
Google Chrome

↓

Source Port

↓

52341
```

The operating system usually selects this port automatically.

These temporary ports are called **Ephemeral Ports**.

Example:

```
52341

49180

61522

55001
```

Each new connection typically receives a different ephemeral port.

---

# 📌 Destination Port

The **Destination Port** identifies the service that should receive the data.

Example:

```
HTTPS

↓

443
```

```
SSH

↓

22
```

```
RDP

↓

3389
```

```
SMB

↓

445
```

The destination port tells the receiving computer which application should process the incoming data.

---

# 🏢 Enterprise Example

An employee visits:

```
https://company.com
```

The connection may look like:

```
Source IP:

10.20.15.45

↓

Source Port:

52184

↓

Destination IP:

198.51.100.10

↓

Destination Port:

443
```

The browser randomly selected port **52184**,

while the web server listens on **443**.

---

# 🧠 SOC Insight

Source ports change frequently.

Destination ports usually indicate **which service is being accessed**.

When reviewing logs,

analysts often pay closer attention to the **destination port** because it provides context about the type of communication.

Examples:

- Port 22 → SSH
- Port 80 → HTTP
- Port 443 → HTTPS
- Port 445 → SMB
- Port 3389 → Remote Desktop

Unexpected destination ports may indicate unauthorized services or malware communication.

---

# 💼 Real SOC Investigation

A firewall alert shows:

```
Source

↓

FINANCE-PC-04

↓

52318

↓

Destination

↓

203.0.113.55

↓

4444
```

The analyst immediately notices:

```
Destination Port

↓

4444
```

This is **not** a common enterprise service.

Further investigation reveals that a penetration testing framework is using TCP port 4444 for a reverse shell.

The destination port becomes the first clue that the connection deserves investigation.

---

# 🔴 Red Team Perspective

Attackers often communicate over:

- Standard ports (80, 443) to blend into normal traffic.
- Non-standard ports (4444, 8081, 9001, etc.) for custom services or malware.

Modern malware increasingly prefers **TCP 443** because encrypted HTTPS traffic is less likely to be blocked or inspected.

Remember:

**A legitimate port does not guarantee legitimate activity.**

---

# 🔵 Blue Team Perspective

SOC analysts should monitor for:

- Rare destination ports.
- Unexpected services.
- Internal systems exposing unusual listening ports.
- New outbound connections to uncommon ports.
- Large volumes of traffic on ports that are rarely used in the environment.

Port context is one piece of the investigation—not the entire story.

Always correlate with:

- Process information
- DNS logs
- EDR telemetry
- Firewall logs
- Proxy logs

---

# ❌ Common Misconceptions

### Mistake 1

"Port 443 means the traffic is safe."

False.

Many malware families use HTTPS to communicate with Command & Control servers.

Always investigate the destination, certificate, process, and behavior.

---

### Mistake 2

"The source port identifies the service."

False.

The source port usually identifies the client application and is often assigned dynamically.

The destination port typically identifies the service being accessed.

---

### Mistake 3

"Malware always uses unusual ports."

False.

Sophisticated malware often uses ports such as **80** and **443** to blend in with normal web traffic.

---

# ⚡ Quick Revision

✅ Every TCP segment begins with a TCP Header.

✅ The Source Port identifies the sending application.

✅ The Destination Port identifies the receiving service.

✅ Client systems usually use ephemeral source ports.

✅ Destination ports provide valuable context during investigations.

# 🔗 Knowledge Connections

TCP Header
      │
      ├── Source Port
      ├── Destination Port
      ├── Ephemeral Ports
      ├── HTTP
      ├── HTTPS
      ├── SSH
      ├── SMB
      ├── Firewall Logs
      ├── Wireshark
      └── SOC Investigation
---

# 🔢 Sequence Numbers - How TCP Keeps Track of Data

> **"TCP doesn't number packets—it numbers every byte of data."**

---

# 🎯 Purpose

Imagine sending a 500 MB file across the Internet.

The file is divided into thousands of TCP segments.

Now imagine that one segment is lost during transmission.

How does the receiver know:

- Which data has already arrived?
- Which data is missing?
- Where should each segment be placed?
- Which segment needs to be retransmitted?

The answer is **Sequence Numbers**.

Every TCP connection uses sequence numbers to keep track of data and ensure it is reconstructed correctly at the destination.

---

# 🌍 What Is a Sequence Number?

A **Sequence Number** is a value placed in the TCP Header that identifies the position of data within a TCP stream.

Think of it as a page number in a book.

Without page numbers, you could still receive every page—but putting them back in the correct order would be extremely difficult.

TCP uses sequence numbers so the receiver always knows exactly where each piece of data belongs.

---

# 💡 Real World Analogy

Imagine a teacher handing out an exam paper.

Instead of one large sheet, the exam is split into several pages:

```
Page 1

↓

Page 2

↓

Page 3

↓

Page 4

↓

Page 5
```

If Page 3 goes missing, students immediately know:

- Which page is missing.
- Where it belongs.
- Which page should be requested again.

Sequence Numbers work in exactly the same way.

---

# 📦 TCP Numbers Bytes, Not Packets

This is one of the most misunderstood concepts in networking.

TCP does **not** assign sequence numbers to packets.

Instead, it numbers every **byte** of data.

Example:

Suppose an application sends **500 bytes**.

```
Segment 1

Sequence Number:

1000

Payload:

100 Bytes
```

The next segment begins where the previous one ended.

```
Segment 2

Sequence Number:

1100

Payload:

100 Bytes
```

Then:

```
Segment 3

Sequence Number:

1200
```

Notice that the sequence number increased by **100**, because **100 bytes** were transmitted.

---

# 🏢 Enterprise Example

A user uploads a document to SharePoint.

TCP divides the document into multiple segments.

```
Segment 1

SEQ = 5000

1000 Bytes
```

↓

```
Segment 2

SEQ = 6000

1000 Bytes
```

↓

```
Segment 3

SEQ = 7000

1000 Bytes
```

If Segment 2 is lost,

the receiver still knows:

- Segment 1 arrived.
- Segment 3 arrived.
- Segment 2 is missing.

TCP requests only the missing segment instead of downloading the entire document again.

---

# 📈 Why Sequence Numbers Matter

Sequence Numbers allow TCP to:

- Detect missing data.
- Reassemble data in the correct order.
- Ignore duplicate segments.
- Support retransmissions.
- Maintain reliable communication.

Without sequence numbers,

reliable TCP communication would not exist.

---

# 🧠 SOC Insight

In Wireshark,

you'll often see entries such as:

```
Sequence Number: 34567
```

Modern versions of Wireshark usually display **relative sequence numbers** by default.

Instead of showing the large random initial value,

Wireshark starts counting from **0** to make packet analysis easier.

This is a display feature only—the actual TCP sequence numbers are still much larger.

---

# 💼 Real SOC Investigation

## Incident

An analyst notices repeated TCP retransmissions during a malware investigation.

Wireshark shows:

```
SEQ = 4000

↓

ACK Received
```

```
SEQ = 5000

↓

No ACK
```

```
SEQ = 5000

↓

Retransmitted
```

The missing acknowledgment indicates that the original segment was not successfully received.

The retransmission helps the analyst understand why the communication was delayed.

---

# 🔴 Red Team Perspective

Attackers generally don't manipulate sequence numbers manually.

However, sequence numbers play an important role in techniques such as:

- TCP Session Hijacking.
- Packet Injection.
- TCP Reset attacks.
- Traffic spoofing.

To inject malicious traffic into an existing TCP session, an attacker must accurately predict or observe valid sequence numbers.

---

# 🔵 Blue Team Perspective

SOC analysts examine sequence numbers when investigating:

- Packet loss.
- Retransmissions.
- Session hijacking attempts.
- Network instability.
- Connection anomalies.
- Malformed TCP traffic.

Although sequence numbers are rarely viewed in SIEM dashboards, they become extremely valuable during packet-level investigations in Wireshark.

---

# 🛡️ Detection Opportunities

Investigate when you observe:

- Excessive retransmissions.
- Unexpected duplicate segments.
- Abnormal TCP resets.
- Out-of-order segment delivery.
- Suspicious packet injection attempts.

These patterns may indicate network issues, misconfigurations, or malicious activity.

---

# ❌ Common Misconceptions

### Mistake 1

"Sequence Numbers identify packets."

False.

Sequence Numbers identify the position of **bytes** within the TCP data stream.

---

### Mistake 2

"Every packet increases the sequence number by one."

False.

The sequence number increases by the number of **bytes transmitted**, not by the number of packets.

---

### Mistake 3

"Large sequence numbers indicate suspicious traffic."

False.

TCP sequence numbers are expected to grow continuously throughout the connection.

Large values are completely normal.

---

# ⚡ Quick Revision

✅ TCP assigns sequence numbers to bytes, not packets.

✅ Sequence numbers help detect missing and duplicate data.

✅ They allow TCP to reorder out-of-sequence segments.

✅ Retransmissions rely on sequence numbers.

✅ Sequence numbers are essential for reliable communication.

# 🔗 Knowledge Connections

Sequence Numbers
        │
        ├── TCP Header
        ├── ACK
        ├── Retransmissions
        ├── Packet Loss
        ├── Three-Way Handshake
        ├── Session Hijacking
        ├── Wireshark
        ├── TCP Streams
        └── SOC Investigation
---

# ✅ Acknowledgment (ACK) Numbers - Confirming Reliable Delivery

> **"TCP doesn't just send data—it waits for confirmation that the data actually arrived."**

---

# 🎯 Purpose

Sending data is only half of the communication.

The sender also needs to know:

- Did the receiver actually get the data?
- Was anything lost?
- Should data be retransmitted?
- Can I send the next portion of data?

This is where **Acknowledgment (ACK) Numbers** come into play.

An ACK Number tells the sender:

> **"I have successfully received everything up to this point. You can continue sending the next bytes."**

Without acknowledgments, the sender would never know whether the transmitted data reached its destination.

---

# 🌍 How ACK Works

Imagine you send someone a 10-page document.

Instead of replying:

> "I received page 1."

They reply:

> **"I've received everything up to page 5. Please send page 6 next."**

That is exactly how TCP acknowledgments work.

ACK Numbers always indicate the **next byte the receiver expects**, not the last byte received.

This small detail is extremely important.

---

# 💡 Real World Analogy

Imagine ordering books online.

The seller ships Books 1–100.

After receiving them, you send a message:

> **"Everything up to Book 100 has arrived. Please send Book 101 next."**

You don't list every book you received.

You simply indicate what should come next.

TCP acknowledgments work the same way.

---

# 📦 ACK + Sequence Numbers Working Together

Suppose the sender begins with:

```
Sequence Number = 1000
```

The sender transmits:

```
100 Bytes
```

The receiver successfully receives all 100 bytes.

It replies:

```
ACK = 1100
```

This means:

```
"I have received bytes 1000–1099.

Now send byte 1100."
```

Notice something important.

The ACK Number is **not** saying:

> "I received byte 1100."

Instead, it is saying:

> **"1100 is the next byte I expect."**

---

# 📈 Step-by-Step Example

### Sender

```
SEQ = 1000

Payload = 500 Bytes
```

↓

Receiver gets all 500 bytes.

↓

Receiver responds:

```
ACK = 1500
```

Meaning:

```
Received:

1000–1499

Next Expected:

1500
```

Communication continues.

---

# 🏢 Enterprise Example

An employee uploads a large Excel report to a file server.

TCP sends thousands of segments.

For every group of successfully received bytes,

the server sends acknowledgments confirming successful delivery.

If one segment is lost,

the acknowledgment stops advancing.

The sender detects the missing data and retransmits only that portion.

This process happens automatically and is usually invisible to the user.

---

# 🧠 SOC Insight

One of the easiest ways to identify **packet loss** in Wireshark is by observing ACK behavior.

Example:

```
SEQ = 5000

↓

ACK = 6000
```

Normal.

Now imagine:

```
SEQ = 6000

↓

No ACK
```

↓

```
SEQ = 6000

↓

Retransmission
```

The missing ACK tells us the original data was not successfully delivered.

Wireshark often labels this as:

```
TCP Retransmission
```

This is a common indicator of packet loss, network instability, or connectivity problems.

---

# 💼 Real SOC Investigation

## Incident

A user reports that uploading files to an internal application is extremely slow.

The SOC analyst reviews a packet capture.

They observe:

```
SEQ = 20000

↓

ACK = 21000
```

Normal.

Later:

```
SEQ = 21000

↓

No ACK

↓

Retransmission

↓

ACK = 22000
```

The repeated retransmissions suggest packet loss between the client and the server.

The issue is traced to a faulty network switch rather than malware.

Understanding ACK behavior prevents the analyst from pursuing the wrong root cause.

---

# 🔴 Red Team Perspective

Attackers generally rely on TCP's acknowledgment mechanism rather than modifying it.

However, ACK behavior becomes important during:

- TCP Session Hijacking
- Packet Injection
- TCP Reset Attacks
- Traffic Manipulation
- Network Evasion Research

Successful packet injection requires accurate sequence and acknowledgment values.

---

# 🔵 Blue Team Perspective

SOC analysts examine ACK patterns to identify:

- Packet loss
- Retransmissions
- Connection quality
- Network congestion
- Session anomalies
- Abnormal TCP behavior

Although ACK Numbers are rarely visible in SIEM dashboards, they are invaluable during packet analysis.

---

# 🛡️ Detection Opportunities

Investigate when you observe:

- Large numbers of TCP Retransmissions.
- Duplicate ACKs.
- ACK values that stop progressing.
- Repeated out-of-order segments.
- Long communication delays.

These may indicate:

- Network instability
- Hardware failures
- Congestion
- Malicious interference
- Session manipulation

---

# ❌ Common Misconceptions

### Mistake 1

"ACK confirms one packet."

False.

ACK confirms successful receipt of **bytes**, not individual packets.

---

### Mistake 2

"ACK equals the last byte received."

False.

ACK always indicates the **next byte expected**.

---

### Mistake 3

"If an ACK is delayed, an attack is occurring."

False.

Delayed acknowledgments may result from normal network congestion or latency.

Always investigate the broader context.

---

# 🎯 MITRE ATT&CK Mapping

ACK Numbers are a TCP reliability mechanism.

However, analysts encounter ACK behavior during investigations involving:

| Activity | MITRE Technique |
|-----------|-----------------|
| Command & Control | T1071 |
| Exfiltration Over C2 Channel | T1041 |
| Remote Services | T1021 |
| Adversary-in-the-Middle | T1557 |

---

# ⚡ Quick Revision

✅ ACK Numbers confirm successful receipt of data.

✅ ACK indicates the next byte expected—not the last byte received.

✅ Missing ACKs often lead to retransmissions.

✅ Sequence Numbers and ACK Numbers work together to provide reliable communication.

✅ Wireshark uses ACK behavior to identify retransmissions and packet loss.

# 🔗 Knowledge Connections

Acknowledgment Numbers
          │
          ├── Sequence Numbers
          ├── TCP Header
          ├── Retransmissions
          ├── Packet Loss
          ├── Three-Way Handshake
          ├── Wireshark
          ├── TCP Streams
          ├── Network Troubleshooting
          └── SOC Investigation
---

# 🚩 TCP Flags - The Language of TCP Communication

> **"TCP devices don't talk using words. They communicate using flags."**

---

# 🎯 Purpose

Every TCP segment contains a set of control bits known as **TCP Flags**.

These flags tell the receiving device:

- Is this a new connection?
- Is this normal data?
- Should the connection end?
- Was an error detected?
- Should communication stop immediately?

Without TCP Flags,

devices would have no way of understanding what stage a connection is in.

---

# 🌍 What Are TCP Flags?

Think of TCP Flags as traffic signals.

A traffic light tells drivers:

🟢 Go

🟡 Slow Down

🔴 Stop

Similarly,

TCP Flags tell computers what action should be taken next.

For example:

```
SYN

↓

"I want to start a connection."
```

```
ACK

↓

"I received your data."
```

```
FIN

↓

"I've finished sending data."
```

```
RST

↓

"Terminate this connection immediately."
```

Every TCP connection depends on these flags.

---

# 📋 The Main TCP Flags

| Flag | Full Form | Purpose |
|------|-----------|---------|
| SYN | Synchronize | Starts a TCP connection |
| ACK | Acknowledgment | Confirms successful receipt of data |
| FIN | Finish | Gracefully closes a connection |
| RST | Reset | Immediately terminates a connection |
| PSH | Push | Deliver data to the application immediately |
| URG | Urgent | Indicates urgent data is present |
| ECE | ECN Echo | Congestion notification |
| CWR | Congestion Window Reduced | Confirms congestion handling |

In day-to-day networking,

you will most commonly encounter:

- SYN
- ACK
- FIN
- RST

The remaining flags are less common but still important.

---

# 🏢 Enterprise Example

An employee opens:

```
https://portal.company.com
```

The browser first sends:

```
SYN
```

The server responds:

```
SYN + ACK
```

The browser replies:

```
ACK
```

Only after these flags are exchanged does HTTPS communication begin.

No application data is exchanged until the TCP connection is established.

---

# 🔍 Flag-by-Flag Overview

## 🟢 SYN (Synchronize)

Purpose:

Starts a brand-new TCP connection.

Whenever a client wants to communicate,

the very first packet normally contains the SYN flag.

Without SYN,

there is no TCP connection.

---

## ✅ ACK (Acknowledgment)

Purpose:

Confirms successful receipt of data.

Nearly every packet after the connection is established contains the ACK flag.

ACK is one of the most frequently observed TCP flags in Wireshark.

---

## 🏁 FIN (Finish)

Purpose:

Gracefully closes an existing TCP connection.

FIN tells the other device:

> "I'm finished sending data, but let's close the connection properly."

Applications such as web browsers and email clients commonly end sessions using FIN.

---

## ❌ RST (Reset)

Purpose:

Immediately terminates a TCP connection.

Unlike FIN,

RST does not perform a graceful shutdown.

It simply ends communication.

RST packets may appear when:

- A service is not listening on the destination port.
- A firewall rejects the connection.
- An application crashes.
- An attacker intentionally resets the session.

---

## 📤 PSH (Push)

Purpose:

Requests that the receiving system immediately deliver the received data to the application instead of waiting for additional data.

Interactive applications such as SSH and Telnet often make use of the PSH flag.

---

## 🚨 URG (Urgent)

Purpose:

Indicates that part of the transmitted data should be treated as urgent.

Modern applications rarely use the URG flag.

---

## 🌐 ECE (ECN Echo)

Purpose:

Indicates that congestion has been detected somewhere along the network path.

This helps improve performance without relying solely on packet loss.

---

## 📉 CWR (Congestion Window Reduced)

Purpose:

Confirms that the sender has reduced its transmission rate after receiving congestion information.

ECE and CWR work together to improve TCP performance on congested networks.

---

# 🧠 SOC Insight

When investigating packet captures,

certain flags deserve immediate attention.

For example:

Large numbers of:

```
SYN
```

may indicate:

- Port Scanning
- SYN Flood attacks
- Reconnaissance

Unexpected:

```
RST
```

packets may indicate:

- Firewall rejection
- Service failure
- Session interruption
- Network instability

TCP Flags often tell the story of what happened before you even inspect the payload.

---

# 💼 Real SOC Investigation

## Incident

A firewall generates an alert for thousands of outbound connection attempts.

The PCAP shows:

```
SYN

↓

RST
```

```
SYN

↓

RST
```

```
SYN

↓

RST
```

repeating hundreds of times.

The analyst concludes that the infected system is attempting to connect to multiple ports where no service is listening.

This behavior is consistent with **port scanning** or malware searching for accessible services.

---

# 🔴 Red Team Perspective

Attackers frequently abuse TCP Flags.

Examples include:

- SYN Scan (Nmap)
- SYN Flood
- TCP Reset attacks
- Stealth Scanning
- Packet Injection
- Session Hijacking

Understanding TCP Flags helps attackers remain stealthy and helps defenders recognize those behaviors.

---

# 🔵 Blue Team Perspective

SOC analysts monitor TCP Flags to identify:

- Port scanning
- Failed connection attempts
- Service crashes
- Connection resets
- SYN floods
- Reconnaissance activity
- Abnormal session termination

Flag analysis is a core skill when reviewing packet captures.

---

# 🛡️ Detection Opportunities

Investigate when you observe:

- Large volumes of SYN packets.
- Excessive RST packets.
- Thousands of half-open connections.
- Repeated failed connection attempts.
- Unexpected FIN or RST spikes.

These patterns may indicate reconnaissance, denial-of-service attacks, or network misconfigurations.

---

# ❌ Common Misconceptions

### Mistake 1

"Every TCP packet contains only one flag."

False.

A TCP packet can contain multiple flags at the same time.

Examples include:

- SYN + ACK
- FIN + ACK
- PSH + ACK

---

### Mistake 2

"RST always indicates an attack."

False.

RST packets are common during normal networking, such as when a connection is made to a closed port or an application unexpectedly terminates.

Context is essential.

---

### Mistake 3

"FIN and RST do the same thing."

False.

FIN performs a graceful shutdown.

RST immediately terminates the connection without completing the normal closing process.

---

# ⚡ Quick Revision

✅ TCP Flags control the state of every TCP connection.

✅ SYN starts communication.

✅ ACK confirms successful receipt.

✅ FIN closes connections gracefully.

✅ RST immediately terminates a session.

✅ PSH, URG, ECE, and CWR provide additional control functions.

# 🔗 Knowledge Connections

TCP Flags
      │
      ├── TCP Header
      ├── Sequence Numbers
      ├── ACK Numbers
      ├── Three-Way Handshake
      ├── Four-Way Handshake
      ├── Port Scanning
      ├── SYN Flood
      ├── Wireshark
      ├── Firewall Logs
      └── SOC Investigation
---

# 🔌 Ports & Sockets - How Applications Communicate

> **"An IP address identifies a device. A port identifies an application. Together, they allow data to reach the correct destination."**

---

# 🎯 Purpose

Imagine a company with one public office building.

Everyone knows the building's address.

But inside the building are many different departments:

- Human Resources
- Finance
- IT Support
- Legal
- Sales

If a courier arrives with a package, knowing only the building's address isn't enough.

They also need to know **which department** should receive it.

Networking works in the same way.

- **IP Address → Identifies the device**
- **Port Number → Identifies the application**

Without ports, a computer would not know which application should receive incoming data.

---

# 🌍 Why Do We Need Ports?

A single computer often runs many services at the same time.

For example:

```
Windows Server

├── HTTPS Website
├── Remote Desktop
├── SMB File Sharing
├── DNS Server
├── SQL Database
└── SSH Server
```

All of these services use the **same IP address**.

Ports allow the operating system to send incoming traffic to the correct application.

---

# 💡 Real World Analogy

Think of an apartment building.

```
Building Address

↓

221B Baker Street
```

Inside the building are many apartments.

```
Apartment 101

Apartment 102

Apartment 103

Apartment 104
```

The building address is like an **IP address**.

The apartment number is like a **port number**.

Without the apartment number, the delivery cannot reach the correct resident.

---

# 📦 What Is a Port?

A **Port** is a logical communication endpoint used by applications.

Port numbers range from:

```
0

↓

65535
```

Each TCP or UDP connection uses a source port and a destination port.

Example:

```
Source

10.10.10.5:52184

↓

Destination

172.16.5.20:443
```

The destination port tells the server which application should receive the request.

---

# 📚 Types of Port Numbers

## Well-Known Ports (0–1023)

These are reserved for common services.

Examples:

| Port | Service |
|------|----------|
| 20/21 | FTP |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP |
| 53 | DNS |
| 80 | HTTP |
| 110 | POP3 |
| 143 | IMAP |
| 443 | HTTPS |
| 445 | SMB |

---

## Registered Ports (1024–49151)

These are commonly assigned to vendor applications and enterprise software.

Examples include:

- Microsoft SQL Server
- Oracle services
- VMware components
- Custom enterprise applications

---

## Dynamic / Ephemeral Ports (49152–65535)

These ports are usually assigned automatically by the operating system.

Example:

```
Chrome

↓

Source Port

↓

52341
```

When the browser closes,

the port becomes available for future connections.

---

# 🔗 What Is a Socket?

A **Socket** is the combination of:

```
IP Address

+

Port Number
```

Example:

```
192.168.1.15:443
```

This uniquely identifies one communication endpoint.

A complete TCP conversation is identified using four values:

```
Source IP

+

Source Port

+

Destination IP

+

Destination Port
```

Example:

```
10.1.10.15:52341

↓

203.0.113.50:443
```

This combination is often called the **4-Tuple**.

It uniquely identifies a TCP session.

---

# 🏢 Enterprise Example

An employee accesses the company intranet.

```
Laptop

IP:

10.10.20.15

↓

Browser

Source Port:

52184

↓

Web Server

IP:

172.16.50.20

↓

HTTPS

Port 443
```

Even if hundreds of employees connect to the same web server,

each connection has a different source port,

allowing the server to keep every session separate.

---

# 🧠 SOC Insight

Many analysts focus only on destination ports.

However,

source ports can also provide valuable context.

For example:

- A client normally uses a random ephemeral source port.
- A server usually listens on a fixed destination port.

If a workstation suddenly begins listening on TCP 445 or TCP 3389,

it may indicate:

- Unauthorized software
- Lateral movement
- Remote administration tools
- Malware opening a listening service

Always investigate systems exposing unexpected listening ports.

---

# 💼 Real SOC Investigation

## Incident

An EDR alert shows:

```
powershell.exe

↓

Listening

↓

TCP Port 4444
```

The analyst asks:

- Why is PowerShell listening for inbound connections?
- Which process created the socket?
- Is anything connected?
- Was a firewall rule modified?
- Does this match known malware behavior?

Further analysis reveals a reverse shell waiting for an attacker to connect.

The listening socket becomes a critical piece of evidence.

---

# 🔴 Red Team Perspective

Attackers use ports strategically.

Examples include:

- Reverse shells on TCP 4444
- HTTPS C2 on TCP 443
- SSH tunneling on TCP 22
- SMB for lateral movement
- Custom malware using high-numbered ports

Sophisticated malware often chooses common ports to blend into normal network traffic.

---

# 🔵 Blue Team Perspective

SOC analysts monitor:

- Newly opened listening ports.
- Rare destination ports.
- Unexpected inbound services.
- Unauthorized remote administration ports.
- Internal hosts exposing external services.

Port monitoring is an essential part of threat detection.

---

# 🛡️ Detection Opportunities

Investigate:

- New listening ports.
- Outbound traffic to uncommon ports.
- Internal SMB connections between unrelated systems.
- RDP connections outside business hours.
- High-risk administrative ports exposed to the Internet.

Always correlate with:

- Firewall logs
- EDR telemetry
- Process creation events
- Network connections
- User authentication logs

---

# ❌ Common Misconceptions

### Mistake 1

"Port 443 always means HTTPS."

False.

Any application can technically use any port.

Port numbers are conventions, not guarantees.

Always inspect the actual traffic.

---

### Mistake 2

"Every connection uses the same source port."

False.

Client operating systems normally assign a new ephemeral port for each connection.

---

### Mistake 3

"A socket is just a port."

False.

A socket combines an IP address and a port number to identify a communication endpoint.

---

# ⚡ Quick Revision

✅ IP identifies the device.

✅ Ports identify the application.

✅ A socket combines an IP address and a port.

✅ A TCP connection is uniquely identified by the 4-Tuple.

✅ Ephemeral ports are assigned dynamically by the operating system.

# 🔗 Knowledge Connections

Ports & Sockets
        │
        ├── TCP Header
        ├── Source Port
        ├── Destination Port
        ├── Three-Way Handshake
        ├── HTTP
        ├── HTTPS
        ├── SSH
        ├── SMB
        ├── Firewall Logs
        ├── Wireshark
        └── SOC Investigation
---

# 🤝 TCP Three-Way Handshake - Establishing a Reliable Connection

> **"Before two devices exchange data, they must first agree to communicate. The TCP Three-Way Handshake is that agreement."**

---

# 🎯 Purpose

Unlike UDP, TCP does not immediately begin sending data.

Before any application data is transmitted, both devices must establish a reliable connection.

This process is known as the **TCP Three-Way Handshake**.

The handshake ensures that:

- Both devices are online.
- Both devices are ready to communicate.
- Initial Sequence Numbers (ISNs) are exchanged.
- Each device can send and receive data.
- The connection is synchronized before data transfer begins.

Only after the handshake completes does the application begin transmitting data.

---

# 🌍 Where Does the Three-Way Handshake Happen?

Think about opening a website.

When you enter:

```
https://github.com
```

The communication does **not** begin with HTTP immediately.

Instead, the following steps occur:

```
User

↓

DNS Lookup

↓

github.com → IP Address

↓

TCP Three-Way Handshake

↓

TLS Handshake

↓

HTTPS Request

↓

Server Response

↓

Web Page Loads
```

This means every HTTPS connection depends on TCP first.

The same is true for:

- SSH
- RDP
- SMB
- FTP
- SMTP
- IMAP
- POP3
- LDAP

---

# 💡 Real World Analogy

Imagine making a phone call.

You dial your friend's number.

```
You:

"Hello?"
```

Your friend answers.

```
Friend:

"Hello, I can hear you."
```

You reply.

```
You:

"Great, let's talk."
```

Only after this exchange does the conversation begin.

TCP follows exactly the same pattern.

---

# 📦 Step 1 - SYN

The client wants to start communication.

It sends a packet with the **SYN** flag set.

```
Client

------------ SYN ------------>

Server
```

This packet contains:

- Source Port
- Destination Port
- Initial Sequence Number
- SYN Flag

The client is essentially saying:

> **"I'd like to establish a TCP connection."**

No application data is transmitted yet.

---

# 📦 Step 2 - SYN + ACK

The server receives the SYN packet.

If the requested service is available,

the server responds with:

```
SYN + ACK
```

```
Client

<-------- SYN + ACK ----------

Server
```

The server is saying:

> **"I received your request, and I'm ready to communicate."**

The server also sends:

- Its own Initial Sequence Number
- An ACK confirming the client's sequence number

---

# 📦 Step 3 - ACK

The client receives the server's response.

It sends one final packet:

```
ACK
```

```
Client

------------ ACK ------------>

Server
```

The client confirms:

> **"I received your response. The connection is now established."**

At this point,

both systems have synchronized their sequence numbers.

Application data can now be exchanged.

---

# 📈 Complete Flow

```
CLIENT                                   SERVER

SYN ------------------------------------>

       <----------------------- SYN + ACK

ACK ------------------------------------>

============= CONNECTION ESTABLISHED =============

HTTP

HTTPS

SSH

SMB

FTP

Application Data
```

Remember:

No application data is sent until this process is complete.

---

# 🧠 Why Three Packets?

Many beginners ask:

> "Why not use just one or two packets?"

Three packets are required because both devices must:

- Exchange Initial Sequence Numbers.
- Confirm they can send data.
- Confirm they can receive data.

The handshake verifies communication in both directions before any sensitive information is transmitted.

---

# 🏢 Enterprise Example

An employee opens Microsoft Outlook.

```
Laptop

↓

TCP Handshake

↓

Exchange Server

↓

Email Download Begins
```

Without a successful handshake,

Outlook cannot retrieve emails from the server.

The same principle applies to web browsers, file servers, VPNs, and remote desktop sessions.

---

# 🔍 Wireshark View

A normal packet capture often looks like this:

```
No.     Source          Destination      Info

1       Client          Server           SYN

2       Server          Client           SYN, ACK

3       Client          Server           ACK

4       Client          Server           HTTP GET

5       Server          Client           HTTP 200 OK
```

This is one of the most common packet sequences you'll encounter in Wireshark.

Learning to recognize it quickly is an essential networking skill.

---

# 💼 Real SOC Investigation

## Incident

Users report that an internal web application is unavailable.

The analyst reviews a packet capture.

```
SYN

↓

No Response

↓

SYN Retransmission

↓

No Response
```

Possible causes include:

- Server is offline.
- Firewall is blocking traffic.
- Routing issue.
- Service has stopped.
- Network cable disconnected.

The absence of a **SYN-ACK** immediately narrows the investigation.

---

# 🔴 Red Team Perspective

The Three-Way Handshake is frequently abused by attackers.

Examples include:

- SYN Scans (Nmap)
- SYN Flood attacks
- Half-open connections
- TCP Session Hijacking
- Service enumeration
- Firewall testing

Attackers understand that the handshake reveals whether a service is available.

---

# 🔵 Blue Team Perspective

SOC analysts monitor handshake behavior to identify:

- Failed connection attempts.
- Port scanning.
- SYN floods.
- Firewall blocks.
- Unreachable services.
- Abnormal connection rates.
- Malware beacon failures.

Even before application data is inspected, the handshake can reveal suspicious behavior.

---

# 🛡️ Detection Opportunities

Look for:

- Large volumes of SYN packets.
- High SYN-to-ACK failure rates.
- Thousands of half-open connections.
- Excessive SYN retransmissions.
- Unexpected inbound SYN traffic.
- Repeated connection attempts from the same host.

These patterns may indicate reconnaissance, denial-of-service attacks, or malware communication issues.

---

# ❌ Common Misconceptions

### Mistake 1

"The handshake transfers website data."

False.

The handshake establishes the connection.

Application data is transmitted only after the handshake completes.

---

### Mistake 2

"If I see a SYN packet, communication has already started."

False.

A SYN packet is only a request to establish communication.

The connection is not established until the final ACK is exchanged.

---

### Mistake 3

"A failed handshake always means an attack."

False.

Failed handshakes can result from:

- Network outages.
- Firewall rules.
- Server maintenance.
- Routing problems.
- Service failures.

Always investigate the surrounding context.

---

# ⚡ Quick Revision

✅ TCP uses a Three-Way Handshake before sending data.

✅ SYN starts the connection.

✅ SYN + ACK accepts the request.

✅ ACK completes the connection.

✅ Only after the handshake does application data begin to flow.

# 🔗 Knowledge Connections

Three-Way Handshake
          │
          ├── TCP Flags
          ├── Sequence Numbers
          ├── ACK Numbers
          ├── TCP Header
          ├── HTTP / HTTPS
          ├── SSH
          ├── SMB
          ├── Wireshark
          ├── Firewall Logs
          └── SOC Investigation
---

# 🦈 Packet-by-Packet Analysis of the TCP Three-Way Handshake

> **"Every packet in a TCP handshake tells a story. A SOC analyst's job is to understand that story."**

---

# 🎯 Purpose

Now that we understand the Three-Way Handshake,

let's examine what actually happens inside each packet.

Instead of simply memorizing:

```
SYN

↓

SYN + ACK

↓

ACK
```

we'll inspect every important field that appears inside these packets.

This is exactly how packet analysis is performed in Wireshark.

---

# 📦 Packet 1 — SYN

The client wants to establish a TCP connection.

```
Client

------------ SYN ------------>

Server
```

A simplified Wireshark view might look like:

```
Source IP:          192.168.1.10
Destination IP:     203.0.113.20

Source Port:        52341
Destination Port:   443

Flags:              SYN

Sequence Number:    1000

Acknowledgment:     0

Window Size:        64240
```

---

## What does this packet tell us?

### Source IP

```
192.168.1.10
```

The device initiating the connection.

---

### Destination IP

```
203.0.113.20
```

The server the client wants to communicate with.

---

### Source Port

```
52341
```

A temporary (ephemeral) port selected by the operating system.

This identifies the client application.

---

### Destination Port

```
443
```

The client wants to communicate with an HTTPS service.

---

### SYN Flag

```
SYN = 1
```

This indicates:

> "Please establish a new TCP connection."

---

### Sequence Number

```
1000
```

This is the client's Initial Sequence Number (ISN).

Future TCP communication begins from this value.

---

### ACK Number

```
0
```

No acknowledgment is sent because this is the very first packet.

The client has not yet received anything.

---

### Window Size

```
64240
```

This tells the server:

> "This is approximately how much data I can currently receive before requiring further acknowledgments."

We'll study Window Size in detail later in the chapter.

---

# 💼 SOC Investigation

Suppose a user cannot access an internal website.

The packet capture shows only:

```
SYN
```

No response follows.

Possible explanations include:

- Server offline
- Firewall silently dropping traffic
- Routing problem
- Network outage
- Incorrect destination IP

The absence of a SYN-ACK becomes the first clue.

---

# 📦 Packet 2 — SYN + ACK

The server receives the SYN request.

If the requested service is available,

it responds.

```
Client

<----------- SYN + ACK -----------

Server
```

Example:

```
Source IP:          203.0.113.20
Destination IP:     192.168.1.10

Source Port:        443
Destination Port:   52341

Flags:              SYN, ACK

Sequence Number:    5000

Acknowledgment:     1001
```

---

## What changed?

The server now sends its own Initial Sequence Number.

```
Sequence Number

5000
```

At the same time,

the server acknowledges the client's SYN.

```
ACK = 1001
```

Notice something important.

The client's Sequence Number was:

```
1000
```

The acknowledgment becomes:

```
1001
```

The SYN flag consumes one sequence number,

which is why the ACK increases by one.

This detail often appears in networking interviews.

---

# 💼 SOC Investigation

Suppose Wireshark shows:

```
SYN

↓

SYN + ACK

↓

No ACK
```

The server accepted the connection,

but the client never completed it.

Possible causes:

- Client crash
- Firewall block
- Malware interruption
- Network instability
- Deliberate half-open scan

The analyst now knows exactly where communication failed.

---

# 📦 Packet 3 — ACK

The client receives the server's response.

It confirms successful receipt.

```
Client

------------ ACK ------------>

Server
```

Example:

```
Source Port:        52341

Destination Port:   443

Flags:              ACK

Sequence Number:    1001

Acknowledgment:     5001
```

Notice:

```
Client ISN

1000
```

↓

```
Client ACK

1001
```

Server:

```
ISN

5000
```

↓

```
ACK

5001
```

Both devices have now synchronized their sequence numbers.

The TCP connection is officially established.

---

# 📊 Complete Packet Flow

```
Packet 1

Client

SEQ = 1000

SYN

---------------------------->

Packet 2

Server

SEQ = 5000

ACK = 1001

SYN + ACK

<----------------------------

Packet 3

Client

SEQ = 1001

ACK = 5001

ACK

---------------------------->

Connection Established
```

This is one of the most recognizable packet sequences in networking.

Every SOC analyst should be able to identify it immediately.

---

# 🧠 SOC Insight

When reviewing Wireshark,

ask yourself:

- Did the SYN leave the client?
- Did the server respond?
- Did the client acknowledge?
- Where did communication stop?

Answering these four questions often identifies the problem within minutes.

---

# 🔴 Red Team Perspective

Attackers closely observe handshake behavior.

Why?

Because it tells them:

- Is the host alive?
- Is the port open?
- Is a firewall present?
- Is the service responding?
- Is the application available?

Tools such as Nmap rely heavily on handshake responses.

---

# 🔵 Blue Team Perspective

SOC analysts investigate:

- Missing SYN-ACK packets
- Large numbers of SYN packets
- Incomplete handshakes
- Half-open connections
- Connection failures
- SYN retransmissions

These behaviors may indicate reconnaissance, malware, or network issues.
# ⚡ Quick Revision

✅ Packet 1 starts the connection.

✅ Packet 2 accepts the connection.

✅ Packet 3 confirms the connection.

✅ Both devices exchange Initial Sequence Numbers.

✅ The handshake completes before any application data is sent.
---

# 👋 TCP Four-Way Handshake - Closing a TCP Connection

> **"Just as TCP carefully establishes a connection, it also closes it gracefully to ensure no data is lost."**

---

# 🎯 Purpose

Once communication between two devices is complete, the TCP connection must be closed.

Unlike establishing a connection, closing it requires **four packets** because TCP is **full-duplex**.

This means:

- The client and server can send data independently.
- Each side must indicate when it has finished sending data.

---

# 🌍 How the Four-Way Handshake Works

Imagine ending a phone call.

You say:

> "I'm done talking."

Your friend replies:

> "Okay, I heard you."

A few seconds later your friend says:

> "I'm done too."

You reply:

> "Okay, goodbye."

Only then is the call completely over.

TCP closes a connection in the same way.

---

# 📦 Step 1 - FIN

The client has finished sending data.

It sends:

```
FIN
```

Meaning:

> "I have no more data to send."

The client can still receive data from the server.

---

# 📦 Step 2 - ACK

The server acknowledges the FIN.

```
ACK
```

Meaning:

> "I received your FIN."

The connection remains partially open because the server may still have data to send.

---

# 📦 Step 3 - FIN

Once the server has finished sending its remaining data,

it sends:

```
FIN
```

Meaning:

> "I'm also finished."

---

# 📦 Step 4 - ACK

The client acknowledges the server's FIN.

```
ACK
```

The TCP connection is now fully closed.

---

# 📈 Complete Flow

```
CLIENT                            SERVER

FIN ----------------------------->

      <-------------------- ACK

      <-------------------- FIN

ACK ----------------------------->

Connection Closed
```

---

# 💼 Real SOC Investigation

A user reports that file transfers randomly fail.

The packet capture shows:

```
FIN

↓

ACK

↓

FIN

↓

ACK
```

This is a **normal** TCP session termination.

Now compare it with:

```
RST
```

Instead of a graceful shutdown,

the connection was terminated immediately.

This could indicate:

- An application crash
- Firewall interference
- Network interruption
- Service failure

Understanding the difference between **FIN** and **RST** helps analysts determine whether a connection ended normally or unexpectedly.

---

# 🔴 Red Team Perspective

Attackers generally don't abuse the Four-Way Handshake itself.

However, they may trigger **RST** packets to abruptly terminate sessions, evade logging, or interrupt communications.

---

# 🔵 Blue Team Perspective

SOC analysts should distinguish between:

- Normal FIN-based connection closures.
- Unexpected RST-based connection resets.
- Repeated abnormal session terminations.

Frequent RST packets may indicate application issues, firewall actions, malware, or scanning activity.

---

# ⚡ Quick Revision

✅ TCP uses a Four-Way Handshake to close connections.

✅ FIN indicates a device has finished sending data.

✅ ACK confirms receipt of the FIN.

✅ RST is different from FIN—it immediately terminates a connection.

# 🔗 Knowledge Connections

Four-Way Handshake
        │
        ├── TCP Flags
        ├── FIN
        ├── ACK
        ├── RST
        ├── Wireshark
        └── SOC Investigation
---

# 🦈 TCP in Wireshark - What a SOC Analyst Should Look For

> **"Wireshark is one of the most valuable tools for understanding TCP communication during incident response."**

---

# 🎯 Why This Matters

When investigating a PCAP, a SOC analyst should be able to quickly answer:

- Was the TCP connection established successfully?
- Was data transferred?
- Was the connection closed normally?
- Were there retransmissions?
- Were there resets?
- Is there evidence of scanning or abnormal behavior?

TCP analysis often provides the first indication of what happened during an incident.

---

# 🔍 Important TCP Fields

The following fields should always be reviewed during packet analysis:

| Field | Why It Matters |
|--------|----------------|
| Source IP | Who initiated the connection? |
| Destination IP | Which system was contacted? |
| Source Port | Which client application started the connection? |
| Destination Port | Which service was accessed? |
| TCP Flags | Connection state and behavior |
| Sequence Number | Track transmitted data |
| ACK Number | Confirm successful delivery |
| Packet Length | Identify unusually large or small packets |

---

# 🚨 Signs of Suspicious TCP Activity

While reviewing TCP traffic, investigate:

- Large numbers of SYN packets
- Numerous RST packets
- Excessive retransmissions
- Repeated failed connection attempts
- Long-lived connections with little or no data
- Unexpected communication over administrative ports (22, 3389, 445)

One indicator alone is rarely enough. Always correlate with logs from EDR, firewall, proxy, or SIEM.

---

# 💼 SOC Investigation Example

## Incident

Users report they cannot access an internal application.

The packet capture shows:

```
SYN

↓

SYN, ACK

↓

ACK

↓

Application Data

↓

RST
```

Questions a SOC analyst should ask:

- Which side sent the RST?
- Did an application crash?
- Did a firewall terminate the session?
- Did an endpoint security product interrupt the connection?
- Was this expected behavior?

The RST packet becomes the starting point of the investigation—not the conclusion.

---

# 🛠️ Useful Wireshark Display Filters

| Filter | Purpose |
|---------|---------|
| tcp | Show only TCP traffic |
| tcp.flags.syn == 1 | Find connection attempts |
| tcp.flags.reset == 1 | Find reset packets |
| tcp.flags.fin == 1 | Find normal session closures |
| tcp.analysis.retransmission | Find retransmitted packets |
| tcp.stream == X | Follow a single TCP conversation |

> Replace **X** with the stream number shown by Wireshark.

---

# 🔵 Blue Team Checklist

When analyzing TCP traffic, verify:

- Was the handshake completed?
- Was the correct service contacted?
- Were any packets retransmitted?
- Did the session close normally (FIN) or abruptly (RST)?
- Are multiple hosts showing the same behavior?
- Does the traffic align with endpoint and firewall logs?

Never rely solely on packet captures. Correlate network evidence with host telemetry.

# ⚡ Quick Revision

✅ Verify the handshake.

✅ Review TCP Flags.

✅ Look for retransmissions.

✅ Investigate unexpected RST packets.

✅ Correlate packet captures with other security logs.

---

# 🚨 TCP Investigation Checklist for SOC Analysts

> **"During an investigation, don't just look at packets. Ask the right questions."**

---

# 🎯 Investigation Workflow

When analyzing TCP traffic in Wireshark or a PCAP, use the following checklist.

---

## 1️⃣ Was the TCP Connection Established?

Look for the Three-Way Handshake.

```
SYN

↓

SYN, ACK

↓

ACK
```

If the handshake never completes:

- Server may be offline.
- Firewall may be blocking traffic.
- Network connectivity issue.
- Port may be closed.

---

## 2️⃣ Which Service Is Being Accessed?

Check the destination port.

Examples:

| Port | Service |
|------|---------|
| 22 | SSH |
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS |
| 445 | SMB |
| 3389 | RDP |

Then ask:

- Is this service expected?
- Is the user allowed to access it?
- Is the destination internal or external?

---

## 3️⃣ Was Data Successfully Delivered?

Look for:

- Retransmissions
- Duplicate ACKs
- Packet loss
- Out-of-order packets

These may indicate:

- Poor network connectivity
- Congestion
- Network hardware issues
- Interrupted communication

---

## 4️⃣ How Did the Connection End?

Look for:

Normal:

```
FIN

↓

ACK
```

Unexpected:

```
RST
```

An unexpected reset may indicate:

- Application failure
- Firewall interruption
- Service crash
- Malware terminating connections

---

## 5️⃣ Does the Traffic Match the Alert?

Always correlate the packet capture with:

- Firewall logs
- SIEM alerts
- EDR telemetry
- DNS logs
- Authentication logs
- Proxy logs

A PCAP provides one piece of the investigation—not the complete picture.

---

# 💼 Example Investigation

## Alert

```
Suspicious outbound HTTPS connection
```

Questions to answer:

- Was the TCP handshake completed?
- Which process initiated the connection?
- Which destination IP was contacted?
- Was DNS resolution successful?
- How much data was transferred?
- Did the session close normally?
- Do endpoint logs confirm the same activity?

By answering these questions, analysts can quickly determine whether the activity is expected or requires deeper investigation.

---

# 🎯 Key Takeaways

A SOC analyst should be comfortable identifying:

✅ Successful TCP handshakes

✅ Failed TCP handshakes

✅ Normal session termination

✅ Reset connections

✅ Retransmissions

✅ Common service ports

✅ Basic packet flow in Wireshark

