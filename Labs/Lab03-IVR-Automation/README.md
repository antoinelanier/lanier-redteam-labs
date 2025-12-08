````markdown
# Lab 3 — IVR / Webhook Security Simulation (Transit Voice System)

**Author:** Antoine Lanier  
**Role:** Aspiring Penetration Tester / Security Analyst  
**Date:** 2025  

---

## 1. Overview

This lab is a **custom IVR (Interactive Voice Response) system** that simulates a real transit agency phone line.

The system:

- Lets a caller dial a Twilio phone number
- Plays a voice menu (card balance, trip history, card status, representative)
- Accepts **DTMF input** (keypad)
- Looks up data from a **JSON “database”**
- Performs **basic authentication** using card-linked phone numbers
- Returns the requested information to the caller

After building it, the system is treated as a **target** and analyzed from a **pentester’s perspective** (attack surface, risks, and mitigations).

This project is **Lab 3** in my self-taught pentesting path.

---

## 2. Objectives

### Engineering Objective

- Build a working IVR backend using:
  - **Node.js + Express**
  - **Twilio Voice (webhooks + TwiML)**
  - **Ngrok** to expose localhost to the internet
  - **JSON file** as a simulated backend database

### Security / Pentest Objective

- Treat the IVR + API + webhook combination as a **real attack surface**
- Identify:
  - Exposed endpoints
  - Input vectors
  - Authentication logic weaknesses
  - Misconfigurations that could be abused
- Practice writing **clear, security-focused documentation** around a system I built myself

---

## 3. What the System Does (Call Flow)

1. **Caller dials Twilio number**
2. Twilio sends a **webhook** (`POST`) to `/voice`
3. Backend responds with TwiML menu:

   - `1` – Check card balance  
   - `2` – Recent trip history  
   - `3` – Card status  
   - `0` – Speak with a representative (simulated)

4. If user selects 1–3:
   - Call is routed to `/enterCard`
   - Caller is asked to enter a **10-digit card number**
5. Backend hits `/processCard`:
   - Looks up card in `dummy-db.json`
   - If card is not found → error message
   - If card **is** found and has an attached account:
     - Caller must enter the **10-digit phone number** on file
     - If phone matches, the IVR reads the requested data
6. If user presses `0`, call is routed to `/rep` (placeholder for live agent transfer).

---

## 4. Technical Stack

- **Node.js** – runtime environment
- **Express.js** – web framework for routing
- **Twilio Voice** – telephony, IVR menu, DTMF collection
- **Ngrok** – secure public URL → local server for Twilio webhooks
- **dotenv** – environment variable management for secrets
- **JSON file database** (`dummy-db.json`) – simulated card + account data

Example `dummy-db.json` entry:

```json
{
  "1234567890": {
    "balance": 25.50,
    "tripHistory": [
      "Bus 12 - 8:15 AM",
      "Train A - 9:40 AM",
      "Bus 7 - 5:20 PM"
    ],
    "status": "Active",
    "account": {
      "name": "John Doe",
      "phone": "5551234567"
    }
  }
}
````

---

## 5. High-Level Architecture

```text
Phone Caller
   ↓
Twilio Voice Number
   ↓  (HTTPS POST Webhook)
Ngrok Public URL
   ↓
Node.js / Express Server (server.js)
   ↓
Routes: /voice → /menu → /enterCard → /processCard → /verifyPhone → /rep
   ↓
Local JSON Database (dummy-db.json)
   ↓
TwiML XML Response → Twilio → Caller
```

Key routes:

* `POST /voice` – welcome message + main IVR menu
* `POST /menu` – handles 1/2/3/0 selection and redirects
* `POST /enterCard` – prompts user to enter 10-digit card number
* `POST /processCard` – validates card number, performs lookup, decides if verification is required
* `POST /verifyPhone` – verifies 10-digit phone number associated with card
* `POST /rep` – simulated “transfer to representative” message

---

## 6. Attack Surface (Pentester View)

This lab is intentionally built to be analyzed like a real-world target.

### External Attack Surface

* Public-facing endpoints exposed via **Ngrok**:

  * `/voice`
  * `/menu`
  * `/enterCard`
  * `/processCard`
  * `/verifyPhone`
  * `/rep`

* These endpoints:

  * Accept **HTTP POST** from Twilio
  * Can, in a misconfigured environment, be hit directly by **any external actor** who knows the URL

### Input Vectors

* **DTMF card number input**:

  * 10-digit numeric value used as key in JSON DB
* **DTMF phone number input**:

  * 10-digit numeric value used for authentication
* **Route flow**:

  * An attacker could try calling endpoints **out of order** or crafting payloads to bypass steps

### Data Handling

* JSON DB is indexed by **card number**
* Each record includes:

  * Balance
  * Trip history
  * Status
  * Account name
  * Phone number

From a pentest perspective, this is a classic **IDOR-like pattern**:

* “If I can guess someone else’s card number, can I see their data?”

---

## 7. Key Risks Identified

1. **No Twilio Signature Validation (Webhook Trust)**

   * The server currently assumes any request hitting the endpoint is from Twilio.
   * An attacker with the Ngrok URL could send custom crafted requests.

2. **IDOR-like Behavior on Card Numbers**

   * Card numbers map directly to JSON keys.
   * If card numbers are predictable, an attacker could enumerate valid cards and their associated data.

3. **Brute Force on Card / Phone Inputs**

   * No rate limiting on:

     * Card number attempts
     * Phone number attempts
   * Possible to attempt multiple combinations in a short time.

4. **Information Disclosure via Error Messages**

   * “That card number was not found” gives a clear yes/no on card validity.
   * Useful for **enumeration**.

5. **Sensitive Data in Logs**

   * Console logs may print:

     * Card numbers
     * Phone numbers

---

## 8. Mitigation Ideas (Future Hardening)

If this were moved toward production, the following controls should be added:

* **Validate Twilio signatures**

  * Verify incoming requests using Twilio’s `X-Twilio-Signature`
  * Drop any request that fails verification

* **Stronger Input Validation**

  * Enforce strict numeric-only regex
  * Enforce exact length, reject anything out-of-range

* **Rate Limiting & Lockouts**

  * Per phone number or per source IP (if behind gateway)
  * Temporary lockout after several failed attempts

* **Less Verbose Errors**

  * Use responses like:

    * “We could not process that request”
  * Avoid distinguishing between:

    * Invalid card vs. valid card / wrong phone

* **Move from JSON to Real DB**

  * Apply proper access controls, logging, and monitoring
  * Store only what’s needed for IVR

* **Log Sanitization**

  * Remove raw card numbers and phone numbers from logs
  * Use partial masking

---

## 9. How to Run the Lab (Technical Setup)

> This is a **high-level** run guide to make the project reproducible.

1. **Install dependencies**

   ```bash
   npm install
   ```

2. **Create `.env` file** with:

   ```env
   TWILIO_SID=your_twilio_sid
   TWILIO_AUTH_TOKEN=your_twilio_auth_token
   TWILIO_PHONE_NUMBER=+1xxxxxxxxxx
   PORT=3000
   ```

3. **Start server**

   ```bash
   node server.js
   ```

4. **Start Ngrok tunnel**

   ```bash
   ngrok http 3000
   ```

5. **Configure Twilio webhook**

   * In Twilio Console → Phone Numbers → your number:

     * “A Call Comes In” → Webhook
     * Method: `HTTP POST`
     * URL: `https://<your-ngrok-subdomain>.ngrok.io/voice`

6. **Test call**

   * Dial your Twilio number
   * Walk through menu
   * Use test card from `dummy-db.json` (e.g., `1234567890`)

---

## 10. Skills Demonstrated (Pentest-Relevant)

* Understanding of **API + webhook integrations** (Twilio → Express)
* Ability to map and describe an **attack surface**
* Recognition of:

  * IDOR-like issues
  * Authentication weaknesses
  * Information disclosure problems
* Secure coding mindset:

  * Input validation
  * Error handling
  * Secret management via `.env`
* Ability to:

  * Build a realistic target from scratch
  * Then step back and **analyze it like a pentester**

This lab shows I can not only exploit systems, but also **build and understand** them at a level deep enough to perform meaningful security testing.
