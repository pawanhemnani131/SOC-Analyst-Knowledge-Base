# 🌐 Chapter 02 – Network Protocols

> *"Understanding protocols is the foundation of network security. Every packet tells a story—your job as a SOC analyst is to understand it."*

---

## 📖 Introduction

Now that we've explored the **OSI Model**, it's time to understand the **protocols** that operate within those layers.

A **network protocol** is a set of rules that allows devices to communicate with each other. Every time you browse a website, send an email, connect through SSH, or resolve a domain name, multiple protocols work together behind the scenes.

For a SOC Analyst, protocol knowledge is essential. Firewall logs, packet captures (PCAPs), SIEM alerts, IDS detections, and EDR telemetry all revolve around understanding how protocols behave under normal and malicious conditions.

This chapter bridges networking fundamentals with real-world cybersecurity investigations.

---

# 🎯 Learning Objectives

By the end of this chapter, you will be able to:

- 🌐 Understand how common network protocols work.
- 📡 Identify protocols in packet captures and network logs.
- 🔍 Recognize normal vs. suspicious protocol behavior.
- 🛡️ Investigate protocol-based attacks from a SOC perspective.
- 🎯 Map protocol abuse to the MITRE ATT&CK framework.
- 🦈 Analyze protocol traffic using Wireshark.
- 📊 Correlate network events with SIEM and EDR telemetry.
- 💼 Apply protocol knowledge during real incident investigations.

---

# 📚 Topics Covered

| # | Topic | Description |
|---|-------|-------------|
| 01 | HTTP / HTTPS | Web communication and secure browsing |
| 02 | DNS | Domain name resolution |
| 03 | TCP | Reliable transport protocol |
| 04 | UDP | Fast connectionless communication |
| 05 | ICMP | Diagnostics and network troubleshooting |
| 06 | DHCP | Automatic IP address assignment |
| 07 | ARP | IP-to-MAC address resolution |
| 08 | FTP | File transfer protocol |
| 09 | SMTP | Email sending |
| 10 | POP3 / IMAP | Email retrieval |
| 11 | SSH | Secure remote administration |
| 12 | RDP | Remote desktop protocol |
| 13 | SMB | File and printer sharing |
| 14 | LDAP | Directory services |
| 15 | Kerberos | Authentication protocol |
| 16 | SNMP | Network monitoring |
| 17 | NTP | Time synchronization |
| 18 | TLS / SSL | Encryption and secure communication |
| 19 | QUIC | Modern transport protocol for HTTP/3 |

---

# 🛡️ Learning Approach

Each protocol will be explored from multiple perspectives:

- 🌍 Networking Fundamentals
- 🔴 Red Team Techniques
- 🔵 Blue Team Detection
- 🚨 SOC Investigation
- 🦈 Wireshark Analysis
- 📊 SIEM & EDR Correlation
- 🎯 MITRE ATT&CK Mapping
- 💼 Real-World Case Studies
- 🧪 Hands-On Labs
- 🎤 Interview Questions

This approach ensures you not only understand **how protocols work**, but also **how attackers abuse them and how defenders detect that abuse**.

---

# 🔗 Connection to Previous Chapter

In **Chapter 01 – OSI Model**, we learned **where** protocols operate within the networking stack.

In this chapter, we'll explore **how** those protocols communicate, how attackers exploit them, and how SOC analysts investigate protocol-based activity.

---

# 🚀 What's Next?

We'll begin with **HTTP & HTTPS**, the most widely used protocols on the internet and the foundation of web communication.

Understanding HTTP and HTTPS is essential for investigating phishing, malware downloads, command-and-control (C2) traffic, web attacks, and data exfiltration.

➡️ Continue to **01-HTTP-HTTPS.md**
