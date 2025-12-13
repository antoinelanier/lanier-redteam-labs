Lab 05 — Linux Privilege Escalation

Author: Antoine Lanier
Tools Used: Ubuntu 20.04 VM, Terminal, Linux utilities

Overview

This lab demonstrates a full Linux privilege escalation enumeration workflow on an Ubuntu 20.04 victim machine.
The goal is to collect system information, identify misconfigurations, inspect permissions, and document all potential attack surfaces a real attacker would evaluate after gaining a foothold on a compromised Linux system.

This lab focuses on enumeration only — no exploitation code or harmful activity.
All work was performed on my own isolated virtual machines inside Hyper-V.

Key Objectives

Enumerate system, kernel, and OS information

Identify user accounts, groups, and active sessions

Inspect running processes for privilege escalation vectors

Locate SUID/SGID binaries

Analyze cron jobs and timed tasks

Identify writable directories and PATH hijacking opportunities

Check capabilities assigned to binaries

Determine kernel exploit potential

Screenshots

Screenshots are stored in:
/screenshots/

They include:

01-system-enumeration.png

02-user-and-group-enumeration.png

03-network-enumeration.png

04-process-enumeration.png

05-writable-directories.png

06-suid-enumeration.png

07-cron-enumeration.png

08-path-enumeration.png

Skills Gained

Linux system investigation

Identifying privilege escalation vectors

Understanding SUID, capabilities, cron misconfigurations

Professional documentation and reporting

Establishing a structured methodology for real-world pentesting

Status

✔ Completed
➡ Next Step: HackTheBox Linux machines + privilege escalation practice
