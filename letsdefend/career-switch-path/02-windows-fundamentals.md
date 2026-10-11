# Windows Fundamentals

**Platform:** LetsDefend | **Path:** Career Switch | **Status:** Completed (14 lessons + quiz)
**Goal:** Know how Windows normally works so you can recognize when something is wrong. Most corporate endpoints run Windows, so most alerts involve it.

---

## 1. Introduction to Windows
Windows is the most widely used desktop OS in companies, which also makes it the most targeted by attackers. Learning its normal behavior is the base for detecting malware, persistence, and privilege abuse.

## 2. Windows Filesystems
- **NTFS:** the modern default. Supports permissions, encryption, large files, and journaling.
- **FAT32 / exFAT:** older or portable formats (USB drives), no file permissions.
- **ReFS:** newer, built for resilience on servers.

**Why it matters:** NTFS has **Alternate Data Streams (ADS)**, which let a file hide extra data. Malware sometimes abuses this.

## 3. Directory Structure
| Path | Purpose |
|------|---------|
| `C:\Windows` | The operating system |
| `C:\Windows\System32` | Core system programs and DLLs |
| `C:\Program Files` / `(x86)` | Installed applications (64-bit / 32-bit) |
| `C:\Users\<name>` | Each user's files and settings |
| `C:\ProgramData` | Shared application data |
| `...\AppData` | Per-user app data (Roaming, Local) |
| `...\Temp` | Temporary files |

**Why it matters:** Malware loves `Temp`, `AppData`, and `ProgramData`. A "system" process running from `Downloads` or `Temp` is a red flag.

## 4. Windows Command Line
Two tools: **CMD** (older) and **PowerShell** (more powerful, scripting).

| Command | What it does |
|---------|--------------|
| `dir`, `cd` | List and move through folders |
| `ipconfig /all` | Show network settings |
| `netstat -ano` | Show connections and the process ID using them |
| `tasklist` / `taskkill` | List or stop processes |
| `whoami` | Show the current user |
| `net user` | List or manage local users |
| `ping`, `nslookup` | Test connectivity and DNS |

**Why it matters:** Attackers often use PowerShell and CMD, and analysts use the same tools to investigate. `netstat -ano` plus `tasklist` helps link a suspicious connection to a program.

## 5. Windows Users and Groups
- **Local accounts** live on one machine; **domain accounts** are managed centrally (Active Directory).
- Built-in accounts: Administrator, Guest, and others.
- **Groups** (Administrators, Users, etc.) bundle permissions so you manage groups instead of individuals.
- Every account has a unique **SID**.

**Why it matters:** A new account suddenly added to the Administrators group is a classic sign of compromise.

## 6. Permissions Management
- NTFS permissions: Full Control, Modify, Read & Execute, Read, Write.
- Permissions are stored in an **ACL** (Access Control List) and can be inherited from parent folders.
- **UAC** asks for confirmation before admin-level actions.
- **Principle of least privilege:** give only the access that's needed.

## 7. Windows Process Management
A **process** is a running program, identified by a **PID**, and has a **parent process**.

Key legitimate processes to know: `System`, `smss.exe`, `csrss.exe`, `wininit.exe`, `services.exe`, `lsass.exe`, `svchost.exe`, `explorer.exe`.

**What looks suspicious:**
- Misspelled names (`svch0st.exe`, `lssas.exe`)
- Wrong parent process (e.g., `svchost.exe` not started by `services.exe`)
- Running from the wrong folder
- Multiple copies of a process that should only run once (`lsass.exe`)

## 8. Windows Services
Services are programs that run in the background without a user logged in (managed with `services.msc` or `sc query`). Startup types: Automatic, Manual, Disabled.

**Why it matters:** Attackers install malicious services to **persist** after a reboot.

## 9. Task Scheduler
Runs programs at set times or events (`taskschd.msc`, `schtasks`).

**Why it matters:** Another favorite **persistence** method. A scheduled task launching an unknown script is worth investigating.

## 10. Windows Registry
A database of system and application settings, organized in **hives**:
- `HKEY_LOCAL_MACHINE (HKLM)`: machine-wide settings
- `HKEY_CURRENT_USER (HKCU)`: current user's settings
- `HKEY_CLASSES_ROOT (HKCR)`, `HKEY_USERS (HKU)`, `HKEY_CURRENT_CONFIG (HKCC)`

**Why it matters:** The `Run` keys (e.g., `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`) make programs start at login. Malware commonly adds entries there.

## 11. Windows Firewall
Windows Defender Firewall filters traffic by **inbound/outbound rules** and has three profiles: **Domain**, **Private**, **Public**. Managed through the GUI or `netsh advfirewall`.

**Why it matters:** Attackers may disable the firewall or add allow rules. Unexpected rule changes are an indicator.

---

## 12. Event Logs
Windows records what happens on the system as **events**, viewed in **Event Viewer** (`eventvwr.msc`). Logs are stored as `.evtx` files in `C:\Windows\System32\winevt\Logs`.

**Main log types**
- **Security:** logons, account changes, access to objects (the analyst's favorite)
- **System:** OS and service events (drivers, service installs, startup/shutdown)
- **Application:** events from installed programs
- **Setup / Forwarded Events:** installation events and logs collected from other machines

**Event IDs worth memorizing**
| Event ID | Log | Meaning |
|----------|-----|---------|
| 4624 | Security | Successful logon |
| 4625 | Security | Failed logon (many in a row suggests brute force) |
| 4634 / 4647 | Security | Logoff |
| 4672 | Security | Special (admin-level) privileges assigned to a logon |
| 4688 | Security | New process created (needs auditing enabled) |
| 4720 | Security | User account created |
| 4732 | Security | User added to a local group (e.g., Administrators) |
| 4698 | Security | Scheduled task created |
| 7045 | System | New service installed |
| 1102 | Security | Audit log was cleared |

**Logon types inside 4624/4625:** type **2** = at the keyboard, **3** = over the network, **10** = Remote Desktop (RDP).

**Command line:** `wevtutil qe Security /c:5 /rd:true /f:text` or PowerShell `Get-WinEvent -LogName Security -MaxEvents 5`.

**Why it matters:** Event logs are the main evidence in an investigation, and a SIEM mostly collects these. Attackers know this, so **clearing logs (1102)** is itself a strong alert. Many of the persistence tricks above show up here: a new service (7045), a new scheduled task (4698), a new admin user (4720 then 4732).

## 13. Windows Management Instrumentation (WMI)
WMI is Windows' built-in system for **querying and managing a computer, locally or remotely**: list processes, read hardware info, start programs, and more. Think of it as a database of system information you can ask questions with a SQL-like language called **WQL**.

**Common ways to use it**
```powershell
Get-CimInstance Win32_OperatingSystem          # OS info
Get-CimInstance Win32_Process                  # running processes
Get-CimInstance Win32_Service                  # services
```
The older `wmic` command does similar things but is deprecated.

**Why it matters (attackers love WMI because it is built in and quiet)**
- **Reconnaissance:** collecting system info without installing tools
- **Lateral movement:** running commands on another machine remotely
- **Persistence:** a **WMI event subscription** (a filter + a consumer + a binding) runs code whenever a trigger happens, with no file in the usual startup places
- **Fileless attacks:** nothing obvious written to disk

**What defenders look for**
- `WmiPrvSE.exe` (the WMI host process) starting `cmd.exe` or `powershell.exe`
- Events in the log **Microsoft-Windows-WMI-Activity/Operational**
- Sysmon events 19, 20, 21 (WMI filter, consumer, binding created), if Sysmon is installed

---

## Persistence cheat sheet (connects topics 8-10 and 13)
Where attackers hide to survive a reboot: **Services**, **Scheduled Tasks**, **Registry Run keys**, and **WMI event subscriptions**. Remember these four; you will see them again, and Event Logs (topic 12) is where you find the evidence.

## My own takeaways
- To spot something abnormal, I first have to know what normal looks like: the real system processes, their usual parents, and the folders they run from.
- Suspicious signs I can check quickly: misspelled process names (`svch0st.exe`), the wrong parent process, an odd file path (`Temp`, `AppData`, `Downloads`), or duplicates of a process that should run once.
- Attackers survive a reboot using a handful of places: **services, scheduled tasks, registry Run keys, and WMI event subscriptions**. Those are the first places I check for persistence.
- Event Logs are the evidence trail. The IDs I want to recognize on sight are 4624/4625 (logons), 4688 (new process), 4720 and 4732 (new user, added to a group), 4698 (new scheduled task), 7045 (new service), and 1102 (log cleared).
- A cleared log (1102) is suspicious by itself, because someone is trying to hide what they did.
- WMI, PowerShell, and CMD are built into Windows, so attackers can use them without installing anything. Seeing `WmiPrvSE.exe` launch `cmd.exe` or `powershell.exe` is worth a closer look.
- `netstat -ano` plus `tasklist` lets me connect a suspicious network connection to the program responsible.

**Quiz score:** _(add yours)_
**What confused me / what I want to revisit:** _(add yours, for example registry hives or logon types)_

## Related notes
- [Network Fundamentals](01-network-fundamentals.md)
