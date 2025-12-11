# **Web Application Security Assessment Report**

### **Unrestricted File Upload → Arbitrary ASP.NET Execution (Safe Proof-of-Concept)**

**Author:** *Antoine Lanier*
**Role:** Ethical Hacker in Training
**Date:** 12/11/2025

---

## **1. Executive Summary**

A vulnerable IIS web application was assessed to identify weaknesses in file upload handling and server configuration. The `/uploads` directory was found to be misconfigured, allowing execution of uploaded ASP.NET files.

A harmless proof-of-concept (`test.aspx`) confirmed that arbitrary code placed in the upload directory executes server-side, demonstrating a **critical Remote Code Execution (RCE)** vulnerability.

All testing was performed in a controlled lab environment.
No malicious payloads or destructive techniques were used at any time.

---

## **2. Scope of Assessment**

**In-Scope Systems:**

* Windows 10 IIS Web Server (victim VM)
* Upload functionality located at `/upload.aspx`

**Testing Permissions:**

* Full permission granted (local lab environment)
* Only safe, benign proof-of-concept files permitted
* No reverse shells, exploit payloads, or destructive actions executed

**Assessment Goals:**

* Identify upload misconfigurations
* Validate whether server-side execution is possible
* Document findings, impact, and remediation

---

## **3. Methodology**

The assessment followed a structured web application testing methodology:

### **3.1 Recon & Analysis**

* Identified the upload form on `/upload.aspx`.
* Determined the upload directory location: `/uploads`.
* Inspected IIS configuration and inheritance settings.

### **3.2 Configuration Review**

* Examined handler mappings.
* Verified ASP.NET support within the uploads directory.
* Checked script execution settings.
* Confirmed the folder was converted to an IIS Application.

### **3.3 Proof-of-Concept Testing**

* Uploaded a harmless `.aspx` file.
* Accessed the uploaded file via browser.
* Recorded server execution confirmation.

### **3.4 Documentation & Reporting**

* Captured findings, risks, and mitigation strategies.
* Documented the misconfiguration chain leading to RCE risk.

---

## **4. Findings**

---

### **Finding 1: Unrestricted File Upload in IIS Uploads Directory**

**Severity:** **Critical**
**Category:** Web Application Security → File Upload Misconfiguration
**Impact:** Arbitrary Server-Side Code Execution

#### **Description**

The `/upload.aspx` page allowed files to be uploaded without:

* File type validation
* MIME type checking
* Script execution restrictions
* Handler-blocking rules
* Directory isolation outside the web root

Additionally, the `/uploads` directory:

* Was designated as an IIS Application
* Inherited ASP.NET handler mappings
* Allowed script execution (`Execute` permissions enabled)

These misconfigurations created a path for arbitrary code execution.

---

## **5. Proof of Concept (Safe & Non-Malicious)**

A harmless ASPX file was used to validate the vulnerability:

```aspx
<%@ Page Language="C#" %>
Hello from ASP.NET in the uploads folder!
```

The file was uploaded successfully from the Kali attacker machine.

Accessing the file:

```
http://<victim-ip>/uploads/test.aspx
```

Displayed:

```
Hello from ASP.NET in the uploads folder!
```

### ✔ **Outcome**

This confirmed:

* The server executed the uploaded ASPX file
* The directory was capable of running arbitrary server-side code
* The vulnerability is valid and high-risk

No harmful or exploitative commands were used.

---

## **6. Impact Assessment**

Successful exploitation of this vulnerability could allow an attacker to:

### **Direct Impact**

* Execute arbitrary ASP.NET code on the server
* Read or modify application files
* Access system-level information
* Modify critical configuration files

### **Privilege Escalation Potential**

Depending on IIS Application Pool permissions, an attacker may escalate to:

* Application Pool Identity compromise
* SYSTEM-level compromise (if misconfigured)

### **Business/Operational Impact**

* Complete web server takeover
* Data theft or tampering
* Website defacement
* Lateral movement across the network
* Loss of availability or integrity

**Overall Risk: CRITICAL**

---

## **7. Root Cause Analysis**

The vulnerability resulted from:

* Allowing dynamic file types (ASPX) to be uploaded
* Upload directory placed inside web root
* Script execution enabled
* Lack of proper `web.config` restrictions
* Directory converted to an IIS Application, inheriting handlers
* Missing file validation and sanitization controls

---

## **8. Recommendations**

### **Immediate Remediation**

1. **Disable script execution** in the `/uploads` directory.
2. Move uploaded files **outside the web root**.
3. Restrict uploads to safe file types (e.g., images only).
4. Add a restrictive `web.config` to block ASP.NET handlers:

```
<handlers>
    <remove name="PageHandlerFactory-Integrated" />
</handlers>
```

5. Enforce **server-side validation** of file extensions and MIME types.

---

### **Long-Term Hardening**

1. Implement antivirus scanning for uploaded files.
2. Apply least-privilege permissions on IIS Application Pool identities.
3. Log all upload attempts and access patterns.
4. Use Content Security Policy (CSP) and input validation frameworks.
5. Conduct recurring security reviews of IIS configuration.

---

## **9. Conclusion**

This lab demonstrates a realistic, high-impact vulnerability caused by a misconfigured IIS upload directory. Although only a safe proof-of-concept file was executed, the underlying issue represents a Remote Code Execution risk in real environments.

The successful identification and validation of this vulnerability strengthens foundational web application penetration testing skills and prepares for more advanced topics such as:

* Windows Privilege Escalation
* Web Exploitation Techniques
* Hack The Box Enumeration & Foothold Labs
* OSCP-style assessment workflows

---

## **10. Analyst Information**

**Prepared by:**
**Antoine Lanier**
Ethical Hacker in Training
Portfolio & Educational Security Lab
