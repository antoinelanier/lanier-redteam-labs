# Lab 01 — SSH Brute Force Attack  
**Target:** Windows 10  
**Attacker:** Kali Linux  
**Date:** 12/02/2025  
**Author:** Antoine Lanier  

## 1. Objective
Gain SSH access to a Windows 10 machine using password brute forcing and misconfigurations.

## 2. Tools Used
- Hydra  
- OpenSSH  
- NC (netcat)
- Hyper-V networking  
- PowerShell  

## 3. Steps
In this lab, I simulated a real-world attack scenario by attempting to gain unauthorized SSH access to a Windows 10 host from a Kali Linux attacker machine. The objective was to understand how weak credentials and misconfigured SSH services can be exploited in a penetration test.

Key Steps Performed

Installed OpenSSH Server on Windows 10 manually using the GitHub Win32-OpenSSH release.

Configured the SSH service, ensured it was running, and verified port 22 was listening.

Connected the Kali attacker VM to the same virtual network as the target Windows host.

Used tools like:

Netcat (nc) to confirm the port was open

Hydra to simulate password brute forcing

SSH client to establish the final access

Successfully authenticated into the Windows 10 machine remotely via SSH using valid credentials.

Verified command execution through the remote shell.

Outcome

Successfully accessed the Windows 10 system via SSH.

Demonstrated how an attacker can enumerate services, brute force credentials, and remotely access a Windows environment.

Highlighted the security risk of weak passwords and exposed remote services.


## 4. Evidence Screenshots
(Place screenshots in `/Labs/Lab01-SSH-Bruteforce/screenshots/`)

## 5. Results
- Successful SSH login  
- Verified shell access  
- Demonstrated impact of weak credentials  

## 6. Mitigation
- Strong password policy  
- Disable password-based SSH auth  
- Enable lockouts  
