# DC-1
Walkthrough of the DC-1 vulnerable machine using Kali Linux, Nmap, and Metasploit.
# DC-1 Walkthrough

This repository documents the exploitation of the DC-1 vulnerable machine using Kali Linux tools such as Nmap and Metasploit.  
The goal is to practice penetration testing and privilege escalation in a controlled environment.

---

## Step 1: Nmap Scan
![Nmap Scan]<img width="1920" height="982" alt="VirtualBox_Kali-linux_31_05_2026_13_22_12" src="https://github.com/user-attachments/assets/7d6509b2-8147-4712-b4fd-004db251adbd" />

- **Why:** To discover open ports and services running on the target (`192.168.56.103`).  
- **What we found:**  
  - SSH (22), HTTP (80), PostgreSQL (5432), VNC (5900), IRC (6665–6669), and more.  
  - The web server is running **Drupal 7**.  
- **Purpose:** This tells us where to focus exploitation (Drupal).

---

## Step 2: Searching Exploits in Metasploit
![Metasploit Search] <img width="1920" height="982" alt="VirtualBox_Kali-linux_31_05_2026_13_56_26" src="https://github.com/user-attachments/assets/a0971616-ccca-472e-bd61-b1a981a7f4a5" />

- **Why:** To identify available exploit modules for Drupal 7.  
- **What we found:** Multiple exploits, including **Drupalgeddon2**.  
- **Purpose:** Choose the most reliable exploit for remote code execution.

---

## Step 3: Exploiting Drupalgeddon2
![Drupalgeddon2 Exploit]<img width="1920" height="982" alt="VirtualBox_Kali-linux_31_05_2026_13_56_47" src="https://github.com/user-attachments/assets/9fda057e-3a38-4b3c-97ca-e21fc36e9b9a" />  

- **Why:** To gain a reverse shell from the vulnerable Drupal site.  
- **What we did:**  
  - Set `RHOST` (target IP), `LHOST` (attacker IP), and ports.  
  - Executed the exploit.  
- **Result:** A **Meterpreter session** was opened, giving shell access.

---

## Step 4: Privilege Escalation
![Privilege Escalation]<img width="1920" height="982" alt="VirtualBox_Kali-linux_31_05_2026_13_57_18" src="https://github.com/user-attachments/assets/f902572d-8fdf-44ba-8c73-ecb022844f44" />

- **Why:** To move from `www-data` user to root.  
- **What we did:**  
  - Used `find` with SUID binaries to escalate privileges.  
  - Executed `/usr/bin/find` to spawn a root shell.  
- **Result:** Accessed `/root/thefinalflag.txt`.

---

## Final Flag

Well done!!!
Hopefully you've enjoyed this and learned some new skills.


---

## Conclusion
This walkthrough demonstrates:
- Reconnaissance with Nmap  
- Exploit discovery with Metasploit  
- Remote code execution via Drupalgeddon2  
- Privilege escalation to root  

Educational purpose only. Do not use these techniques outside legal environments.
