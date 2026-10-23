# DC-1 — VulnHub Walkthrough

A clear, step-by-step penetration testing writeup of the DC-1 Capture The Flag (CTF) machine.

## About this machine

| | |
|---|---|
| Name | DC-1 |
| Author | DCAU7 |
| Source | [VulnHub — DC-1](https://www.vulnhub.com/entry/dc-1,292/) |
| Target OS | Debian GNU/Linux (Drupal 7 CMS) |
| Goal | Get a shell, escalate to `root`, and read the final flag |

DC-1 is a deliberately vulnerable virtual machine made for learning. The whole lab runs in a private, isolated network. Everything below was done on my own lab; never use these techniques against a system you do not own or do not have written permission to test.

---

## Step 1 — Reconnaissance (Nmap)

Before attacking, I need to know which doors are open. A port scan tells me what services run on the target and where to look first.

I ran a full Nmap scan against the target `192.168.56.103`:

![Nmap Scan](https://github.com/user-attachments/assets/7d6509b2-8147-4712-b4fd-004db251adbd)

The scan revealed SSH on port 22, HTTP on port 80 running Drupal 7, PostgreSQL on 5432, VNC on 5900, and IRC on 6665–6669. The Drupal 7 website immediately stood out: it is a well-known CMS with famous, reliable exploits, so it became my main target.

---

## Step 2 — Finding an exploit (Metasploit)

Instead of writing an exploit by hand, I first check whether a ready and tested one already exists. Metasploit is a large toolbox of exploits, perfect for a known CMS like Drupal 7.

Inside Metasploit, I searched for Drupal modules:

![Metasploit Search](https://github.com/user-attachments/assets/a0971616-ccca-472e-bd61-b1a981a7f4a5)

Several modules appeared, but Drupalgeddon2 stood out — it is one of the most reliable ways to get remote code execution on Drupal 7.

---

## Step 3 — Remote code execution (Drupalgeddon2)

Drupalgeddon2 abuses a flaw in old Drupal 7 versions to run commands on the server without any password. This gives me my first foothold inside the machine.

I loaded the Drupalgeddon2 module, set `RHOST` to the target, `LHOST` to my Kali IP, and the ports for the reverse connection, then launched it:

![Drupalgeddon2 Exploit](https://github.com/user-attachments/assets/9fda057e-3a38-4b3c-97ca-e21fc36e9b9a)

The exploit delivered a Meterpreter session — a shell on the target as the web user `www-data`.

---

## Step 4 — Privilege escalation to root

`www-data` is a low-power user. To fully own the machine I need to become root. A common path is to look for SUID binaries: programs that run with the power of their owner (often root), even when a normal user starts them. If one of them can run other commands, it becomes a way to a root shell.

I searched for SUID files:

```bash
find / -perm -u=s -type f 2>/dev/null
```

![Privilege Escalation](https://github.com/user-attachments/assets/f902572d-8fdf-44ba-8c73-ecb022844f44)

The `find` binary itself was SUID root. `find` has an option that can run a command, so I used it to spawn a root shell, then read the final flag:

```bash
cat /root/thefinalflag.txt
```

The machine was fully owned.

---

## Summary

| # | Step | Technique | Result |
|---|------|-----------|--------|
| 1 | Recon | Nmap port scan | Found Drupal 7 on port 80 |
| 2 | Exploit search | Metasploit | Picked Drupalgeddon2 |
| 3 | Access | Drupalgeddon2 RCE | Meterpreter as `www-data` |
| 4 | Privilege escalation | SUID `find` binary | root + final flag |

---

## What I learned (defender's view)

The same mistakes that let me in are easy to fix in real life:

- Keep software updated. Drupalgeddon2 only works on old, unpatched Drupal; a simple update closes this door.
- Remove unused services. Many open ports (VNC, IRC, PostgreSQL) mean a bigger attack surface.
- Audit SUID binaries. A program like `find` set as SUID root is a direct path to full control.
- Run web apps with low privileges and isolate them, so a web flaw does not instantly lead to root.
