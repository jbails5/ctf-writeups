# TryHackMe – Pickle Rick

**Room:** [Pickle Rick](https://tryhackme.com/room/picklerick)  
**Date Completed:** 8/21/26  
**Tools Used:** Nmap, Gobuster, Kali Linux, OpenVPN  

---

## Synopsis

Pickle Rick is a beginner-to-intermediate penetration testing room on TryHackMe. The objective is to compromise the web server and locate **3 secret ingredients** hidden across the system. This room tests foundational skills in web enumeration, Linux command-line bypasses, and privilege escalation.

---

## Methodology & Walkthrough

### 1. Reconnaissance

I started with a standard Nmap scan to identify open ports and services on the target IP:

```bash
sudo nmap -sC -sV -T4 <TARGET_IP>

Results:

    Port 22 (SSH) – Open

    Port 80 (HTTP) – Open

Since port 80 was accessible, I directed my focus to the web application.
2. Web Enumeration

Visiting http://<TARGET_IP> in a browser revealed a Rick and Morty themed portal.

Viewing the page source (Ctrl+U) exposed a hidden developer comment:
html

<!-- Username: R1ckRul3s -->

To find hidden directories, I ran a Gobuster directory scan:
bash

gobuster dir -u http://<TARGET_IP> -w /usr/share/wordlists/dirb/common.txt -x php,txt

This uncovered several key paths:

    /robots.txt – Contained the string Wubbalubbadubdub

    /login.php – The admin login portal

3. Gaining Initial Access

Using the credentials found earlier, I logged into the portal:
Field	Value
Username	R1ckRul3s
Password	Wubbalubbadubdub

The portal presented a command execution panel, allowing me to run system commands directly.
4. Finding the First Ingredient

I ran ls to list the current directory and spotted Sup3rS3cretPickl3Ingred.txt.

Attempted:
bash

cat Sup3rS3cretPickl3Ingred.txt

Result: Command blocked by the system.

Bypass: I used less as an alternative text reader:
bash

less Sup3rS3cretPickl3Ingred.txt

First Ingredient: mr. meeseek hair
5. Finding the Second Ingredient

I read the clue.txt file, which instructed me to "look around the file system."

Navigating to /home/rick, I found a file named second ingredients (note the space).

Challenges faced: The spaced filename required proper escaping. I successfully read it using:
bash

less "/home/rick/second ingredients"

Second Ingredient: [Insert your found ingredient here]
6. Privilege Escalation (Root)

To access the final ingredient, I needed root privileges. I checked my sudo permissions:
bash

sudo -l

The output revealed that I could run ALL commands as root without a password.

I escalated immediately:
bash

sudo bash

Navigating to the /root directory, I found and read 3rd.txt:
bash

ls /root
less /root/3rd.txt

Third Ingredient: fleeb juice
Struggles & Solutions
Struggle	Solution
Command Restriction: The cat command was blacklisted by the web panel, preventing me from reading files directly.	I tested alternative Linux utilities. less, more, and strings all bypassed the restriction successfully.
Spaced Filename: The second ingredients file failed to open due to the space in its name.	Wrapping the path in double quotes ("...") or escaping the space with a backslash (\ ) resolved the syntax error.
Enumeration Time: Initially, my Gobuster scan using the directory-list-2.3-medium.txt wordlist took too long.	I switched to the shorter common.txt list, which still contained /login.php and /robots.txt, significantly speeding up the process.
Lessons Learned

    Never trust client-side source code: Hidden comments often leak sensitive credentials.

    Diversify your command toolkit: When cat fails, less, tac, nl, and strings are great fallbacks.

    Sudo misconfigurations are low-hanging fruit: Always run sudo -l immediately after gaining a foothold—passwordless sudo is a game-over for the defender.

Flags Summary
Ingredient	Location	Value
1st	/var/www/html/Sup3rS3cretPickl3Ingred.txt	mr. meeseek hair
2nd	/home/rick/second ingredients	[Yours here]
3rd (Root)	/root/3rd.txt	fleeb juice

Room completed on 8/21/26 – Writeup by [Your Name/GitHub Handle]
