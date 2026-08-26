# TryHackMe – Pickle Rick

**Room:** [Pickle Rick](https://tryhackme.com/room/picklerick)  
**Date Completed:** August 21, 2026  
**Tools Used:** Nmap, Gobuster, Kali Linux, OpenVPN

---

## Synopsis

**Pickle Rick** is a beginner-to-intermediate penetration testing room on TryHackMe. The objective is to compromise the web server and locate **three secret ingredients** hidden across the system. This room tests foundational skills in web enumeration, Linux command-line bypasses, and privilege escalation.

---

## Methodology & Walkthrough

### 1. Reconnaissance

A standard Nmap scan was performed to identify open ports and services on the target IP:

```bash
sudo nmap -sC -sV -T4 10.80.170.147
```
Results:

    Port 22 (SSH) – Open

    Port 80 (HTTP) – Open

Given the open HTTP port, the investigation focused on the web application.
2. Web Enumeration

Visiting http://10.80.170.147 in a browser revealed a Rick and Morty themed portal.

Viewing the page source (Ctrl+U) exposed a hidden developer comment:
html

<!-- Username: R1ckRul3s -->

To uncover hidden directories, a Gobuster directory scan was launched:
```bash
gobuster dir -u http://10.80.170.147 -w /usr/share/wordlists/dirb/common.txt -x php,txt
```
This scan revealed several key paths:

    /robots.txt – Contained the string: Wubbalubbadubdub

    /login.php – The admin login portal

3. Gaining Initial Access

Using the credentials discovered earlier, I logged into the portal:

    Username: R1ckRul3s

    Password: Wubbalubbadubdub

Upon successful login, the portal presented a command execution panel, allowing system commands to be run directly.
4. Finding the First Ingredient

I ran ls to list the current directory and spotted a file named Sup3rS3cretPickl3Ingred.txt.

Attempted: cat Sup3rS3cretPickl3Ingred.txt
Result: Command blocked by the system.

Bypass: I used less as an alternative text reader:
```bash
less Sup3rS3cretPickl3Ingred.txt
```
First Ingredient: mr. meeseek hair
5. Finding the Second Ingredient

I read the clue.txt file, which instructed me to "look around the file system."

Navigating to /home/rick, I found a file named second ingredients (note the space).

Challenge: The spaced filename required proper escaping. I successfully read it using:
```bash
less "/home/rick/second ingredients"
```
Second Ingredient: 1 jerry tear
6. Privilege Escalation (Root)

To access the final ingredient, root privileges were required. I checked my sudo permissions:
```bash
sudo -l
```
The output revealed that I could run ALL commands as root without a password.

I escalated immediately:
```bash
sudo bash
```
Navigating to the /root directory, I found and read 3rd.txt:
```bash
ls /root
less /root/3rd.txt
```
Third Ingredient: fleeb juice
Conclusion

The Pickle Rick room effectively demonstrates essential penetration testing techniques, including:

    Web enumeration and source code analysis

    Directory brute-forcing with Gobuster

    Command injection and bypass techniques

    Linux privilege escalation via misconfigured sudo permissions

Successfully compromising the system and retrieving all three ingredients validates a solid understanding of these core concepts.

Writeup completed by Justin Bailat
