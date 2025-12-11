# Lab 05 – Windows Privilege Escalation Enumeration Report
**Author:** Antoine Lanier  
**Role:** Red Team / Offensive Security Trainee  
**Date:** 12/11/2025  

---

## 1. Executive Summary
This assessment focuses on enumerating privilege escalation opportunities on a Windows 10 workstation.  
No exploitation was conducted.  
The objective was to simulate the post-exploitation phase where an attacker has low-privileged access and begins gathering information to escalate to SYSTEM privileges.

---

## 2. Scope
The following enumeration categories were performed:

1. System information  
2. User / group membership  
3. Token privileges  
4. Running and installed services  
5. Scheduled tasks  
6. File and directory access control lists (ACLs)  
7. PATH environment variable analysis  

All tests were executed locally from a low-privileged user account (`victim`).

---

## 3. Findings

### 3.1 System Information
Command executed: `systeminfo`  
Enumerated:
- OS Version  
- Architecture  
- Hostname  
- KB patches  
- Hardware details  

This identifies potential kernel or driver-based escalation vulnerabilities.

---

### 3.2 User, Group, and Privilege Enumeration
Commands:
- `whoami /all`  
- `net users`  
- `net localgroup`  

Key findings:
- User `victim` has basic privileges only.  
- Some privileges such as `SeChangeNotifyPrivilege` were enabled (normal behavior).  
- No direct administrative membership.

---

### 3.3 Service Enumeration
Command:
- `sc queryex type= service state= all`  
- `wmic service get name,startmode,pathname`  

Purpose:
- Identify writable service paths  
- Detect unquoted service paths  
- Identify services running as SYSTEM  

*Several services had complex paths but no obviously vulnerable write locations.*

---

### 3.4 Scheduled Tasks
Command:
- `schtasks /query /fo list /v`  

Purpose:
- Identify SYSTEM-level scheduled tasks  
- Check if task actions point to user-writable directories  

*All reviewed tasks appeared correctly configured.*

---

### 3.5 ACL & File Permission Review
Commands:
- `icacls "C:\Program Files"`  
- `icacls "C:\Program Files (x86)"`  
- `icacls "C:\ProgramData"`  

Findings:
- Standard permissions appear intact  
- No writable SYSTEM binaries discovered in these core directories  

---

### 3.6 PATH Hijacking Analysis
Command:
- `set` (reviewed PATH content)

Findings:
- User-writable directories appeared in PATH:
  - `C:\Users\victim\AppData\Local\Programs\Python\...`
  - `C:\Users\victim\AppData\Local\Programs\VS Code\bin`

Risk:
If a SYSTEM-level process calls a binary without a full path (ex: `python.exe`), an attacker could plant a malicious binary in these directories.

---

## 4. Conclusion
The enumeration process successfully identified the key components required for privilege escalation analysis. While no single definitive escalation vector was confirmed, multiple areas warrant further testing:

- PATH hijacking possibilities  
- Service misconfigurations  
- Writable directories used by scheduled tasks  

---

## 5. Appendix – Commands Used
01-system-info.png
02-whoami-privileges.png
03-user-and-group-enumeration.png
04-service-enumeration.png
04-service-enumeration2.png
05-scheduled-tasks.png
06-acls.png
07-path-enumeration.png
.keep


