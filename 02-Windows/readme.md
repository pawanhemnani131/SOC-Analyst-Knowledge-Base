# 🪟 Windows for SOC Analysts

> **Windows is the primary operating system used in enterprise environments, making it one of the most targeted platforms by attackers. As a SOC analyst, understanding Windows logs, processes, authentication, and attacker techniques is essential for effective detection and incident response.**

---

# 🎯 Learning Objectives

After completing this section, you will be able to:

- Understand how Windows operates from a SOC perspective.
- Navigate Windows Event Logs.
- Analyze important Windows Event IDs.
- Investigate PowerShell abuse.
- Understand Sysmon telemetry.
- Detect common persistence mechanisms.
- Investigate authentication events.
- Identify Living-off-the-Land (LOLBins) techniques.
- Analyze common attacker activity on Windows endpoints.

---

# 📚 Chapters

| File | Description |
|------|-------------|
| 01-Windows-Architecture.md | Windows components every SOC analyst should know |
| 02-Windows-Logs.md | Windows logging and Event Viewer |
| 03-Windows-Event-IDs.md | Important Event IDs for investigations |
| 04-Sysmon.md | Sysmon installation, telemetry, and Event IDs |
| 05-PowerShell.md | PowerShell logging and attacker abuse |
| 06-Windows-Services.md | Windows services and persistence |
| 07-Windows-Registry.md | Registry fundamentals and persistence |
| 08-Scheduled-Tasks.md | Scheduled Tasks used by administrators and attackers |
| 09-WMI.md | Windows Management Instrumentation |
| 10-Authentication.md | NTLM, Kerberos, and Windows logon events |
| 11-Windows-Defender.md | Defender logs and security events |
| 12-DLL-Attacks.md | DLL Hijacking, Side-Loading, and other DLL abuse |
| 13-Living-Off-the-Land.md | LOLBins and native Windows tools abused by attackers |
| 14-Windows-Investigation-Checklist.md | Step-by-step Windows investigation workflow |
| 15-Windows-Tools.md | Common Windows utilities seen during investigations |
| 16-Windows-Persistence.md | Common persistence techniques on Windows |

---

# 💡 Why This Section Matters

Most enterprise endpoints run Windows, and a large percentage of SOC alerts originate from Windows systems.

Understanding Windows telemetry helps analysts:

- Investigate security alerts.
- Detect malicious activity.
- Correlate endpoint and network events.
- Identify attacker techniques.
- Respond to incidents more effectively.
