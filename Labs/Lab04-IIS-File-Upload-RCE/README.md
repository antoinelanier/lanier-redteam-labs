# IIS Unrestricted File Upload Vulnerability Lab  
### Web Application Security • Misconfigured IIS • Arbitrary Code Execution (Safe PoC Only)

This lab demonstrates how a misconfigured Microsoft IIS upload directory can allow an attacker to upload and execute server-side ASP.NET files. The purpose of this exercise was to safely identify, analyze, and document a high-impact web application vulnerability without using any harmful payloads.

---

## 🔍 Objective
Identify and validate an **Unrestricted File Upload** vulnerability by:

- Building and configuring an IIS web server with an upload form  
- Uploading files from a Kali Linux attacker VM  
- Diagnosing IIS misconfigurations and handler mappings  
- Enabling script execution for testing purposes  
- Executing a benign ASPX file as proof of arbitrary code execution  
- Documenting impact, risk, and mitigation in a professional format  

> **No malicious files or reverse shells were used.**  
> All testing was performed using safe proof-of-concept techniques.

---

## 🖥️ Lab Environment

| Component | Details |
|----------|---------|
| **Victim Machine** | Windows 10 + IIS + ASP.NET |
| **Attacker Machine** | Kali Linux |
| **Upload Endpoint** | `/upload.aspx` |
| **Upload Directory** | `/uploads` (converted into IIS Application) |
| **Key IIS Features Enabled** | ASP.NET 4.8, ISAPI Extensions, Script Execution |

---

## ⚠️ Vulnerability Summary

The IIS `/uploads` directory was misconfigured to:

- Allow user-uploaded files without validation  
- Execute ASPX files uploaded to the directory  
- Inherit ASP.NET handler mappings from the parent application  
- Run arbitrary code upon visiting the uploaded file  

This configuration results in a **critical Remote Code Execution (RCE) risk**.

---

## 🧪 Safe Proof-of-Concept

A harmless ASP.NET test file was uploaded:

```aspx
<%@ Page Language="C#" %>
Hello from ASP.NET in the uploads folder!
````

Accessing the uploaded file:

```
http://<victim-ip>/uploads/test.aspx
```

produced the output:

```
Hello from ASP.NET in the uploads folder!
```

### ✔ Result

The server executed the uploaded file, confirming that the directory was capable of running arbitrary server-side code.

---

## 🔥 Impact (High Severity)

If exploited maliciously, this vulnerability could allow an attacker to:

* Execute arbitrary ASP.NET code on the server
* Read, write, or delete sensitive files
* Compromise the IIS application pool identity
* Deface or modify the web application
* Move laterally inside a Windows environment
* Potentially fully compromise the Windows system

---

## 🛠️ Mitigation Recommendations

To secure the application:

* Disable script execution in file upload directories
* Store uploads outside the web root
* Implement strict file extension and MIME type validation
* Add a restrictive `web.config` removing ASP.NET handlers
* Use antivirus scanning on uploaded files
* Apply least-privilege permissions on IIS application pools
* Log and monitor all upload activity
