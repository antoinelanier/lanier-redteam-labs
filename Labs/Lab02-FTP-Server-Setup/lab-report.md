Lab 02 — FTP Server Setup (Windows 10)

Target: Windows 10
Attacker Machine: Kali Linux
Date: 12/3/2025
Author: Antoine Lanier

1. Objective

Configure an FTP server on Windows 10 using IIS and validate server functionality for use in future enumeration and brute-force testing.

2. Tools Used

Windows 10 IIS FTP Server

Windows Firewall

Kali Linux (for external validation)

Netcat

PowerShell

3. Steps Performed
Step 1 — Enable FTP Server on Windows 10

Installed Internet Information Services (IIS) with FTP Server and Management Tools.

Verified installation using:

inetmgr


Confirmed FTP service listed under IIS.

Step 2 — Configure FTP Root Folder

Created a dedicated FTP directory for testing.

Assigned proper permissions to the test user account.

Step 3 — Configure Firewall Rules

Added inbound rules for:

FTP Control Port (21)

FTP Passive Ports (e.g., 49152–65535)

Verified changes in Windows Firewall advanced settings.

Step 4 — Configure FTP Settings in IIS

Set binding to port 21.

Enabled basic authentication.

Enabled SSL optional mode (for initial testing).

Configured passive ports under “FTP Firewall Support.”

Step 5 — Validate Configuration

From Kali Linux, tested connectivity:

Check port 21:

nc -vz 172.20.89.126 21


Attempt FTP login:

ftp 172.20.89.126


Verified passive mode operation based on captured screenshot:
VictimFTP_PAssivePort_Config.png

4. Evidence Screenshots

Screenshots stored under:
/Labs/Lab02-FTP-Server-Setup/screenshots/

Includes:

FTP installation

Firewall rule setup

Passive port configuration

IIS FTP console view

5. Results

Successfully installed and configured an FTP server on Windows 10.

Confirmed valid listener on port 21.

Verified passive mode configuration required for proper client connectivity.

Prepared environment for future enumeration, brute-force testing, and credential auditing.

6. Mitigation (What a Defender Should Do)

Disable FTP if not needed.

Enforce FTPS (FTP over TLS) only.

Restrict allowed IP ranges in firewall rules.

Apply strong password policy for local accounts.

Monitor IIS logs for suspicious access attempts.

✅ 4. commands-used.txt

Save as:
Labs/Lab02-FTP-Server-Setup/commands-used.txt

Paste this inside:

# Connectivity test to FTP port
nc -vz 172.20.89.126 21

# FTP login attempt
ftp 172.20.89.126

# Show IP information (Windows)
ipconfig

# Open IIS Manager
inetmgr
