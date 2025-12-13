Linux Privilege Escalation — Pentest Lab Report

Author: Antoine Lanier
Lab: 05
Environment: Ubuntu 20.04.6 LTS (Victim VM), Hyper-V

1. Objective

The objective of this lab is to perform privilege escalation enumeration on a Linux system after gaining initial access.
This mirrors the real workflow attackers and penetration testers follow when analyzing a compromised Linux host.

No exploits were executed — only enumeration and documentation of potential escalation vectors.

2. System Overview
Commands Executed
uname -a
hostnamectl
lsb_release -a

Findings

OS: Ubuntu 20.04.6 LTS

Kernel: 5.15.0-139-generic

Hostname: Ubuntu-Victim-Virtual-Machine

This information helps assess kernel exploitability and OS-specific weaknesses.

Screenshot: 01-system-enumeration.png

3. User & Group Enumeration
Commands
whoami
id
groups
cat /etc/passwd

Findings

Current user: victim

No unexpected privileged groups

/etc/passwd shows standard system users only

Screenshot: 02-user-and-group-enumeration.png

4. Network Enumeration
Commands
ifconfig
ip a

Findings

Single network adapter on default Hyper-V virtual switch

IPv4 address identified for remote connectivity testing

Screenshot: 03-network-enumeration.png

5. Running Process Analysis
Commands
ps aux

Findings

No suspicious processes running as root

No custom scripts in /opt, /usr/local, or writable directories

Normal GNOME + systemd processes

Screenshot: 04-process-enumeration.png

6. Writable Directory Enumeration
Command
find / -writable -type d 2>/dev/null

Findings

Writable locations: /home/victim, /tmp, /var/tmp, /run/user/1000

No insecure writable system directories detected

/tmp remains a common exploitation vector during attacks

Screenshot: 05-writable-directories.png

7. SUID Binary Discovery
Command
find / -perm -4000 -type f 2>/dev/null

Findings

Common SUID binaries found:

/usr/bin/passwd

/usr/bin/su

/usr/bin/sudo

/usr/bin/chsh

These are standard. No dangerous SUID programs (e.g. nmap, find, vim) were discovered.

Screenshot: 06-suid-enumeration.png

8. Cron Job Enumeration
Commands
crontab -l
ls -la /etc/cron*

Findings

No user cron jobs configured

System crons appear default

No writable cron scripts detected

Screenshot: 07-cron-enumeration.png

9. PATH Variable Analysis
Command
echo $PATH

Findings

PATH contains standard, safe directories.
No world-writable directories are included, reducing PATH hijacking risk.

Screenshot: 08-path-enumeration.png

10. Capability Analysis
Command
getcap -r / 2>/dev/null

Findings

No binaries with dangerous Linux capabilities

System defaults intact

11. Kernel Exploitability Check
Command
uname -r

Findings

Kernel version 5.15.0 is not known for easy root exploits in modern Ubuntu versions without additional local vulnerabilities.

Google search term used:
“Ubuntu 20.04 5.15.0 privilege escalation exploit”

12. Conclusion

The Ubuntu victim machine is properly hardened with standard defaults.
No obvious privilege escalation paths were discovered through:

SUID misconfigurations

Cron job weaknesses

PATH hijacking

Writable system directories

Abnormal capabilities

