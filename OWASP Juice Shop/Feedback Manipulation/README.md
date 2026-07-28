# Vulnerability Analysis

## Overview

This exploit chain combines three separate vulnerabilities that, when used together, allow an attacker to submit fraudulent reviews while impersonating other users.

| Vulnerability                        | OWASP Category                                  | CWE     |
| ------------------------------------ | ----------------------------------------------- | ------- |
| Feedback Manipulation                | A01: Broken Access Control                      | CWE-285 |
| Broken CAPTCHA Implementation        | A05: Security Misconfiguration                  | CWE-804 |
| Anonymous User Trust (UserID = null) | A07: Identification and Authentication Failures | CWE-308 |

## Application Functionality

The application allows users to submit reviews consisting of:

* A username
* A rating
* A comment
* A CAPTCHA challenge

If a user is not logged in, the application automatically assigns the username **"anonymous"**. Before submitting a review, the user must correctly answer the CAPTCHA challenge.

---

## Vulnerability Breakdown

### 1. Broken CAPTCHA Validation

While intercepting requests with Burp Suite, it was observed that the server sends the CAPTCHA answer directly to the client.

As a result, users can view the correct answer before submitting the review, defeating the purpose of the CAPTCHA entirely.

### 2. Replay Attack

After successfully submitting a review, the session token remains valid.

Because the server does not invalidate the request or CAPTCHA after use, an attacker can simply resend the same request multiple times, creating duplicate reviews without solving a new CAPTCHA challenge.

### 3. User Impersonation

The application trusts the username value supplied by the client.

By intercepting the request and modifying the username field, an attacker can submit reviews while appearing to be another user.

The server does not verify that the submitted username belongs to the authenticated user, resulting in a broken access control vulnerability.

---

## Impact

An attacker can:

* Bypass CAPTCHA protections
* Submit unlimited reviews using replayed requests
* Impersonate other users
* Manipulate ratings and feedback
* Damage the integrity and trustworthiness of the review system

---

## Root Cause

The application relies on client-supplied data for security decisions:

* CAPTCHA answers are disclosed to the client
* CAPTCHA tokens are not invalidated after use
* User identity is trusted from request parameters instead of being validated server-side

These issues allow an attacker to combine multiple weaknesses into a single exploit chain capable of manipulating the review system.
