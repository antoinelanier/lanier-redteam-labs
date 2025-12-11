# Lab 05 – Windows Privilege Escalation (Enumeration Phase)

**Author:** Antoine Lanier  
**Date:** 12/11/2025  
**Objective:**  
Perform systematic enumeration on a Windows 10 host to identify potential privilege escalation vectors.

This lab focuses only on *enumeration*, not exploitation.  
Goal: Learn what information attackers gather after gaining a foothold on a Windows machine.

---

## 🔍 Techniques Performed

### 1. System Information Enumeration
Collected OS version, build, architecture, hostname, and logged-in user context.

### 2. User, Group & Privileges Enumeration
Checked:
- Local users  
- Local groups  
- Administrator memberships  
- Token privileges (e.g., SeChangeNotifyPrivilege, SeShutdownPrivilege)

### 3. Service Enumeration
Extracted all system services, startup types, and binary paths to identify:
- Weak permissions  
- Unquoted paths  
- DLL hijacking candidates  
- User-writable service locations  

### 4. Scheduled Tasks Enumeration
Listed scheduled tasks and their run context to identify:
- SYSTEM-level tasks  
- Writeable task actions  
- Potential user-controlled paths  

### 5. ACL / File Permission Enumeration
Inspected ACLs on:
- Program Files  
- Program Files (x86)  
- ProgramData  
Looking for weak file rights such as:
- BUILTIN\Users:(OI)(CI)(F)  
- Write-able paths owned by USERS

### 6. PATH Variable Enumeration
Analyzed PATH ordering and found user-writable directories that could enable:
- Binary hijacking  
- DLL hijacking  

---

## 📸 Screenshots

Stored in `/screenshots`:
- `01-system-info.png`
- `02-whoami-privileges.png`
- `03-user-and-group-enumeration.png`
- `04-service-enumeration.png`
- `04-service-enumeration2.png`
- `05-scheduled-tasks.png`
- `06-acls.png`
- `07-path-enumeration.png`

---

## 🧠 Summary

This enumeration phase provides the foundation for identifying privilege escalation techniques.  
Next step (not included here):  
Test exploitability of weak file permissions, scheduled tasks, services, or PATH hijacking opportunities.

This lab demonstrates the *process* a penetration tester follows after initial access to a Windows host.

