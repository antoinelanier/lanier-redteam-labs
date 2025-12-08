Below is a **professional pentester-style lab report** written exactly the way real penetration testers document engagements.

You can copy & paste this **entire report** into your `Lab-Report.md` file in your GitHub repository.

---

# **Penetration Testing Lab Report – IVR / Webhook Security Simulation**

**Author:** Antoine Lanier
**Lab #:** 3
**Date:** 2025
**System Under Test:** Custom IVR Automation Backend (Node.js + Twilio + Ngrok)
**Engagement Type:** Educational / Self-Directed Pentesting Lab

---

# **1. Executive Summary**

This lab engagement evaluated the security posture of a custom-built Interactive Voice Response (IVR) backend created for a simulated transit agency.
The goal of the assessment was to identify weaknesses in voice-driven automation, webhook handling, authentication flows, and data exposure.

The system was intentionally designed with realistic components used in production telephony:

* Twilio Voice Webhooks
* Node.js + Express backend
* Public exposure via Ngrok
* JSON database simulating real customer card data
* DTMF-based authentication logic

The assessment revealed multiple security risks common in real-world IVR environments, including IDOR-like vulnerabilities, weak authentication, unvalidated webhooks, and brute-force potential.

This lab demonstrates my ability to **build**, **analyze**, and **attack** a functioning system—mirroring how real pentesters approach application and infrastructure testing.

---

# **2. Scope of Assessment**

**In-scope components:**

* `/voice` (IVR entry point)
* `/menu` (DTMF routing)
* `/enterCard`
* `/processCard`
* `/verifyPhone`
* `/rep`
* `dummy-db.json` (local database)
* Twilio → Ngrok → Server communication chain

**Out of scope:**

* Live phone number transfer
* CRM systems
* Carrier-level voice infrastructure
* Denial-of-service testing

---

# **3. System Description**

The IVR system accepts incoming calls, collects menu selections, verifies cardholder identity, and returns sensitive customer data (balance, trip history, card status).

High-level architecture:

```
Caller → Twilio Voice → Ngrok HTTPS Tunnel → Node.js Server → JSON Database
```

Authentication used:

* **Something you know:** 10-digit card number
* **Something else you know:** Phone number linked to card

No cryptographic validation, no multi-factor security, and no request integrity checks exist in this demo version.

---

# **4. Threat Model**

### **Assets**

* Cardholder personal data (name, phone)
* Transit usage history
* Card balance and card status
* Call flow logic
* Authentication logic

### **Adversaries**

* External attacker discovering Ngrok URL
* Malicious caller attempting enumeration
* Insider attacker sniffing logs or modifying backend
* Scripted bot making automated calls

### **Attack Goals**

* Retrieve cardholder data without authorization
* Bypass authentication
* Enumerate valid card numbers
* Inject crafted POST requests directly into webhook endpoints

---

# **5. Findings Summary**

| **ID** | **Risk**                                   | **Severity** |
| ------ | ------------------------------------------ | ------------ |
| F-001  | Missing webhook signature validation       | **High**     |
| F-002  | IDOR-like card number enumeration          | **High**     |
| F-003  | Weak authentication (DTMF phone only)      | **Medium**   |
| F-004  | No brute-force protections                 | **Medium**   |
| F-005  | Verbose error messages leak card existence | **Medium**   |
| F-006  | Sensitive data printed to console logs     | **Low**      |
| F-007  | JSON file database lacks access control    | **Low**      |

---

# **6. Detailed Findings**

---

## **F-001 – Webhook Trust Not Enforced**

**Severity:** High
**Category:** Authentication / Request Integrity
**Description:**
The server accepts HTTP POST requests from *any* source. Twilio signatures (`X-Twilio-Signature`) are not validated.

An attacker with the Ngrok URL could:

* Hit `/processCard` directly
* Submit crafted `Digits` values
* Bypass calling the phone number entirely
* Enumerate card numbers using automation

**Impact:**
Full compromise of “customer data” without making a phone call.

**Recommendation:**
Implement Twilio signature validation middleware.

---

## **F-002 – IDOR Through Predictable Card Numbers**

**Severity:** High
**Category:** Broken Access Control
**Description:**
Card numbers are used as **direct keys** in the JSON database.

If card numbers are guessable:

* Attacker can input sequential values
* Server reveals whether card exists
* If card is unlinked → system immediately leaks data
* If linked → system reveals existence of account

This mirrors real-world IDOR vulnerabilities in APIs.

**Recommendation:**

* Use random, non-sequential identifiers
* Implement rate limiting
* Never reveal whether a card exists

---

## **F-003 – Weak Authentication Logic**

**Severity:** Medium
**Description:**
Authentication relies solely on:

* 10-digit card number
* 10-digit phone number

No anti-automation mechanisms.
No MFAs.
Phone number transmitted over DTMF (easily brute-forced).

**Recommendation:**

* Add call-level throttling
* Add voice biometrics or PIN-based verification
* Use OAuth-like token handoff if integrated with CRM

---

## **F-004 – No Brute Force or Rate Limiting**

**Severity:** Medium
**Description:**
The system allows unlimited attempts for:

* Card numbers
* Phone numbers
* Menu navigation

**Impact:**
Automated bots could enumerate valid card numbers in minutes.

**Recommendation:**
Add request throttling or temporary lockouts.

---

## **F-005 – Verbose Error Messages**

**Severity:** Medium
**Description:**
IVR responses reveal:

* “Card not found”
* “Phone does not match our records”

This allows attackers to confirm:

* Valid card numbers
* Valid phone number associations

**Recommendation:**
Use generic failure messages.

---

## **F-006 – Sensitive Data in Logs**

**Severity:** Low
**Description:**
Console logs print:

* Raw card numbers
* Raw phone numbers
* Spoken name text (when transcription enabled)

**Risk:**
Logs may be leaked or accessed in multi-user systems.

**Recommendation:**
Mask identifiers before logging.

---

## **F-007 – JSON File Database Has No Security**

**Severity:** Low
**Description:**
The database is a local JSON file with:

* No encryption
* No access control
* Fully readable to anyone with file access

**Recommendation:**
Use secure DB backend with proper access controls.

---

# **7. Proof of Concept (PoC)**

### **PoC 1 – Direct webhook exploitation**

```
POST https://<ngrok>.ngrok.io/processCard?mode=balance
Body: Digits=1234567890
```

Server responds:

```
Your balance is 25.50 dollars.
```

### **PoC 2 – Card enumeration**

Automated script increments card numbers:

```
0000000001
0000000002
...
```

Server response differentiates valid vs. invalid, enabling enumeration.

---

# **8. Recommendations Summary**

### **High Priority**

* Validate Twilio signatures
* Implement rate limiting
* Hide existence of card numbers
* Replace JSON with proper DB

### **Medium Priority**

* Strengthen authentication
* Remove verbose errors

### **Low Priority**

* Sanitize logs
* Mask sensitive data

---

# **9. Conclusion**

This lab demonstrates a realistic example of how telephony systems, API webhook integrations, and backend services can become attack surfaces when exposed publicly.

The vulnerabilities discovered mirror weaknesses seen in:

* Banking IVR systems
* Transit agency balance check systems
* Utility companies
* Call center automation tools

As a pentester, analyzing a system you built from scratch strengthens:

* Threat modeling
* Attack surface enumeration
* Secure coding mindset
* API/webhook exploitation techniques
* Realistic remediation strategies

This lab successfully combines **software engineering**, **application security**, and **offensive testing methodology**, demonstrating readiness for real entry-level pentesting engagements.
