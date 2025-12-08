# Lab 3 – IVR / Webhook Security Simulation  
### By: Antoine Lanier  
### Role: Developer & Security Analyst  

This lab simulates a **real-world IVR system** using Twilio Voice, Node.js, Ngrok, and a local JSON database.  
After building the system, I approached it as a **pentester**, analyzing its attack surface, risks, and how to harden it.

This project demonstrates:
- Backend development skills  
- Understanding of APIs & webhooks  
- Realistic authentication logic  
- Security thinking (IDOR, validation, secrets, etc.)  
- Documentation aligned with penetration testing workflows  

---

## 📁 Repository Contents

| Folder | Description |
|--------|-------------|
| `/Documentation` | Full technical + security documentation (step-by-step). |
| `/Code` | All backend source code for the IVR system. |
| `/Screenshots` | Evidence of successful exploitation, setup, and verification steps. |
| `/Reports` | Pentest-style reporting for recruiters/engineers. |

---

## 🧪 Lab Summary

This IVR allows callers to:
- Hear a menu  
- Enter card numbers  
- Access balance, trip history, or card status  
- Verify identity via phone number  
- Simulate a call transfer to an agent  

Twilio → Ngrok → Node.js → JSON database.

---

## 🔐 Security Focus Areas

- Webhook attack surface  
- DTMF input validation  
- JSON database access & IDOR risks  
- API keys & secrets handling  
- Authentication logic & bypass attempts  

---

## ▶️ Running the Lab

```bash
npm install
node server.js
ngrok http 3000
