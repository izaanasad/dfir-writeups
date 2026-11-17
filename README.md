# DFIR Writeups

Digital forensics and incident response writeups from TryHackMe rooms: evidence acquisition, chain of custody, Windows Registry artefacts, and hunting for persistence on compromised Linux servers.

By **Izaan Ullah Asad**. Blue-team focus. Related work: [soc-writeups](https://github.com/izaanasad/soc-writeups) · [sentinel-home-lab](https://github.com/izaanasad/sentinel-home-lab).

## Index

| # | Room | Area | Key skills |
|---|---|---|---|
| 01 | [Windows Forensics 1](01-windows-forensics-1.md) | Windows artefacts | Registry hives, ShellBags, UserAssist, ShimCache, Amcache, USB device history |
| 02 | [Linux Server Forensics](02-linux-server-forensics.md) | Incident response | Apache log analysis, cron / SSH-key / systemd persistence, execution history |
| 03 | [Digital Forensics Case B4DM755](03-digital-forensics-case-b4dm755.md) | Forensic process | Chain of custody, FTK Imager, file recovery, EXIF/metadata analysis |
| 04 | [Forensic Imaging](04-forensic-imaging.md) | Acquisition | Bit-for-bit imaging, hash verification, read-only mounting |

## Format

Each writeup covers what the room is about, what I learned (artefact locations, commands, the process), how I approached the investigation, and an **analyst takeaway**: what I'd do differently or carry into a real investigation.

**No answers, flags or lab credentials.** TryHackMe's Acceptable Use Policy prohibits publishing them for active rooms.
