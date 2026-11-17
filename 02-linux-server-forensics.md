# Linux Server Forensics — TryHackMe

**Room:** [Linux Server Forensics](https://tryhackme.com/room/linuxserverforensics) · **Focus:** Web server compromise, persistence hunting · **Tools:** Apache logs, cron, `authorized_keys`, shell history, `systemctl`, `journalctl`

> Answers, flags and lab credentials are not published. TryHackMe's Acceptable Use Policy (v2.0, Aug 2026) prohibits sharing answers for active rooms.

---

## Summary

A fictional web design company has been breached. Across three separate compromised servers, the room has you work out how the attacker got in through the web application and then hunt for the different persistence mechanisms they left behind. Each machine is "smarter" than the last, so the persistence gets harder to spot.

## What I learned

### Web logs are the first stop

`/var/log/apache2/access.log` records the source IP, request, response code and size, and **user-agent** for every request. Default user-agents give scanning tools away (Nmap, Nikto, SQLmap, DirBuster), so listing the distinct user-agents is a fast way to see what tooling was pointed at the server and when.

```bash
cut -d '"' -f 6 access.log | sort | uniq -c | sort -rn   # distinct user-agents
grep -i nmap access.log                                   # one tool's requests
awk '{print $6}' access.log | sort | uniq -c              # HTTP methods (spot non-standard ones)
```

### From recon to entry

Once the scanner noise was filtered out, I reviewed the site itself for attack vectors. A file-upload feature, combined with requests in the log from one specific IP, showed how the attacker got in, with **Remote File Inclusion** as the entry point. A carelessly exposed internal note on the server also helped the attacker.

### Persistence: where to look on Linux

| Mechanism | Where I checked |
|---|---|
| Scheduled tasks | `/etc/crontab`, `/etc/cron.*`, `crontab -l` per user |
| Extra privileged accounts | `/etc/passwd` and `/etc/shadow`, looking for any account other than root with UID 0 |
| SSH keys | `~/.ssh/authorized_keys` for every user, especially root. The comment field (`user@host`) can identify the attacker's machine |
| Shell start-up | `.bashrc`, `.profile` |
| Services | `systemctl list-units --type=service --state=running`, then inspect the unit file for the command and user it runs as |
| Kernel modules | `lsmod` |

### Execution history is thin on Linux and easy to tamper with

- `~/.bash_history` is easy to edit and sometimes disabled.
- `/var/log/auth.log` records every `sudo` command.
- `/var/log/apt/history.log` shows packages installed or removed.
- **journald** logs are binary, read with `journalctl`, and can verify their own integrity, which makes them harder to alter quietly than plain-text logs.

## How I approached it

On each server I followed the same loop: **logs → entry point → privilege → persistence**. The first server's backdoor was in cron. The second used an SSH key planted in root's `authorized_keys`. That raised the question of how they got root, so I went to the command history. The third hid in a systemd service that looked like a legitimate one, and I found it by listing running services and reading the unit file of the one that didn't belong.

## Analyst takeaway

Attackers rarely use a single persistence mechanism. Removing the cron job while missing the SSH key means they're back the next day. My checklist for any compromised Linux host is now the table above, run in full every time, not just until I find the first backdoor.

**Detection angle:** file-integrity monitoring on `authorized_keys`, `/etc/passwd`, crontabs and `/etc/systemd/system/` would have alerted on every persistence step in this room.
