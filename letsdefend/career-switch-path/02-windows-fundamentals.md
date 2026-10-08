# Windows Fundamentals

**Platform:** LetsDefend | **Path:** Career Switch | **Status:** Completed
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

## Persistence cheat sheet (connects topics 8-10)
Where attackers hide to survive a reboot: **Services**, **Scheduled Tasks**, **Registry Run keys**. Remember these three; you will see them again.

## My own takeaways
- (Add what clicked for you, what confused you, and your quiz score)

## Related notes
- [Network Fundamentals](01-network-fundamentals.md)
