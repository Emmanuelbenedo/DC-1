**DC-1 Walkthrough**

This repository walks you through the exploitation of the DC-1 vulnerable machine using Kali Linux, Nmap, and Metasploit. It’s a great way to practice reconnaissance, exploitation, and privilege escalation in a safe, controlled lab environment.

### Step 1: Nmap Scan

![Nmap Scan](https://github.com/user-attachments/assets/7d6509b2-8147-4712-b4fd-004db251adbd)

We start by running a full Nmap scan against the target at `192.168.56.103`. The goal is simple: discover which ports are open and what services are running.

The scan reveals several interesting services:
- SSH on port 22
- HTTP on port 80 (running **Drupal 7**)
- PostgreSQL on 5432
- VNC on 5900
- IRC on ports 6665–6669

The presence of Drupal 7 immediately gives us a clear attack vector to focus on.

### Step 2: Searching for Exploits in Metasploit

![Metasploit Search](https://github.com/user-attachments/assets/a0971616-ccca-472e-bd61-b1a981a7f4a5)

Next, we search Metasploit for available modules targeting Drupal 7. Several options appear, but **Drupalgeddon2** stands out as one of the most reliable for achieving remote code execution.

### Step 3: Exploiting Drupalgeddon2

![Drupalgeddon2 Exploit](https://github.com/user-attachments/assets/9fda057e-3a38-4b3c-97ca-e21fc36e9b9a)

We load the Drupalgeddon2 exploit module and configure the required options:
- `RHOST` → target IP
- `LHOST` → our Kali IP
- Appropriate ports for the reverse shell

Once launched, the exploit successfully delivers a **Meterpreter session**, giving us an initial shell on the target as the `www-data` user.

### Step 4: Privilege Escalation

![Privilege Escalation](https://github.com/user-attachments/assets/f902572d-8fdf-44ba-8c73-ecb022844f44)

With a foothold established, the next objective is to escalate from `www-data` to root. We use the classic SUID binary technique:

```bash
find / -perm -u=s -type f 2>/dev/null
```

Running `/usr/bin/find` with the right parameters spawns a root shell. From there, we can navigate to `/root/thefinalflag.txt` and capture the final flag.

---

**Well done!**  
You’ve completed the DC-1 machine. Hopefully you picked up some useful techniques along the way.

### Summary of what we covered
- Reconnaissance with Nmap  
- Exploit discovery inside Metasploit  
- Remote code execution via Drupalgeddon2  
- Privilege escalation to root using SUID binaries

Happy hacking!
