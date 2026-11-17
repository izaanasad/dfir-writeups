# Windows Forensics 1 — TryHackMe

**Room:** [Windows Forensics 1](https://tryhackme.com/room/windowsforensics1) · **Focus:** Windows Registry artefacts · **Tools:** Registry Explorer, Eric Zimmerman's tools (EZtools), KAPE, FTK Imager, Autopsy, RegRipper

> Answers are not published. TryHackMe's Acceptable Use Policy (v2.0, Aug 2026) prohibits sharing answers for active rooms.

---

## Summary

Windows records a large amount of user and system activity in the Registry, mostly to improve the user experience (recent files, remembered folder views, connected devices). The room teaches how to acquire registry hives from a system and read them offline to answer four investigation questions: what the system is, which files and folders a user touched, which programs ran, and which external devices were connected. It ends with a hands-on challenge on a prepared VM.

## What I learned

### Where the evidence lives

| Hive | Location | What it holds |
|---|---|---|
| SYSTEM, SOFTWARE, SAM, SECURITY, DEFAULT | `C:\Windows\System32\Config\` | System configuration, installed software, local accounts |
| NTUSER.DAT | `C:\Users\<user>\` | Per-user activity: recent docs, typed paths, UserAssist |
| UsrClass.dat | `C:\Users\<user>\AppData\Local\Microsoft\Windows\` | ShellBags (folder access) |
| Amcache.hve | `C:\Windows\AppCompat\Programs\` | Evidence of execution with file hashes |

`HKEY_LOCAL_MACHINE` is abbreviated **HKLM**. Hives are locked on a live system, so they're acquired with tools like KAPE or FTK Imager. Offline analysis also has to account for **transaction logs** (`.LOG1`/`.LOG2`), which may hold changes not yet written to the hive.

### The four questions and the keys that answer them

**1. System information**
- OS version and build: `SOFTWARE\Microsoft\Windows NT\CurrentVersion`
- Which ControlSet is current or last-known-good: `SYSTEM\Select`
- Computer name, time zone, network interfaces (including DHCP addresses): under `SYSTEM\CurrentControlSet\...`
- Local accounts, RIDs, login counts and password hints: the **SAM** hive

**2. Files and folders a user knew about**
- `RecentDocs` and Office MRU lists in NTUSER.DAT
- **ShellBags** in UsrClass.dat, which record folder views and survive even after a folder is deleted
- Open/Save dialog MRUs and the Explorer `TypedPaths` / search history

**3. Evidence of execution**
- **UserAssist**: GUI programs launched, with run counts and last-run time
- **ShimCache (AppCompatCache)**: executables the system has seen, with full paths
- **Amcache**: executed programs with full paths *and SHA1 hashes*. This is the artefact that lets you check a binary against threat intel
- **BAM/DAM**: last execution time per user

**4. External devices**
- `SYSTEM\CurrentControlSet\Enum\USBSTOR` and `Enum\USB` give vendor, product, serial number and first/last connection times
- `SOFTWARE\Microsoft\Windows Portable Devices\Devices` maps devices to friendly names and drive letters

## How I approached the hands-on challenge

I worked through it in the same order as the questions above. First I identified the user accounts from SAM and noted which had never logged in. Then I used the user hives to find when specific files were accessed. Next I used execution artefacts to find the full path of an installer that was run. Finally I correlated USBSTOR with the Portable Devices key to put a timestamp on a specific USB device.

## Analyst takeaway

The Registry answers "did this user do this?" without depending on Windows Event Logs, which may be disabled, rolled over or cleared. In an investigation I'd pair the two. Event logs give the security-relevant events, and Registry artefacts fill in user behaviour and execution history that logging didn't capture.
