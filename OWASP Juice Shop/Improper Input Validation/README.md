# Vulnerability Analysis

## Overview

During testing of the account registration functionality, multiple server-side validation issues were identified. These weaknesses allow an attacker to bypass expected registration controls and perform privilege escalation during account creation.

| Vulnerability                            | OWASP Category             | CWE     |
| ---------------------------------------- | -------------------------- | ------- |
| Missing Password Confirmation Validation | A04: Insecure Design       | CWE-602 |
| Mass Assignment / Privilege Escalation   | A01: Broken Access Control | CWE-915 |
|Insecure File Upload Validation           | A03: Injection / Improper Input                    Validation | CWE-20 

## Application Functionality

When creating a new account, the client sends the following information to the server:

* Email
* Password
* Password Repeat
* Security Question
* Security Answer
* ID

After processing the request, the server returns a response containing:

* Status
* Username
* Role
* Deluxe Token
* Last Login IP
* Profile Image
* Active Status
* ID
* Email
* Created At
* Updated At
* Deleted At

During testing, several validation checks were performed to determine whether the server properly enforced security controls.

---

## Vulnerability Breakdown

### 1. Missing Password Confirmation Validation

The first test involved submitting different values for the Password and Password Repeat fields during registration.

Expected behavior:

* The server should reject the request.
* The user should be required to provide matching passwords.

Observed behavior:

* The server accepted the registration request.
* The account was successfully created despite the passwords not matching.

This indicates that password confirmation validation is either missing or performed only on the client side.

### 2. Mass Assignment / Privilege Escalation

The second test involved adding an additional Role parameter to the registration request.

Although the client application does not normally send this field, the request was modified using Burp Suite before being forwarded to the server.

Expected behavior:

* The server should ignore the Role parameter.
* New users should be assigned a default role.

Observed behavior:

* The server accepted the attacker-supplied Role value.
* The newly created account was assigned the specified role.
* By setting the role to administrator, the account received elevated privileges.

This demonstrates a Mass Assignment vulnerability where the server trusts client-supplied attributes without proper validation.


### 3. Insecure File Upload Validation

The application allows users to submit complaints and attach an invoice document as supporting evidence.

According to the application's intended functionality, only PDF files should be accepted during the upload process.

To test this control, a PDF file was selected and the request was intercepted using Burp Suite before being sent to the server.

The file extension was then modified to alternative formats, including:

* `.txt`
* `.js`

Expected behavior:

* The server should validate uploaded files.
* Non-PDF files should be rejected.

Observed behavior:

* The server accepted the modified uploads.
* The complaint was successfully submitted.
* No server-side validation appeared to enforce the PDF-only restriction.

This suggests that file type validation is either missing or relies solely on client-side controls.

While no further testing was performed to determine whether uploaded files could be executed or rendered by the application, the ability to bypass upload restrictions demonstrates improper input validation and weak file handling controls.

---

## Impact

An attacker can:

* Create accounts with elevated privileges
* Gain administrative access during registration
* Bypass intended authorization controls
* Potentially gain full control over application functionality
* Compromise the integrity of user management systems

Additionally, the lack of password confirmation validation may lead to account management issues and indicates weak server-side input validation practices.

---

## Root Cause

The application relies on client-side controls instead of enforcing validation on the server.

Specifically:

* Password confirmation is not verified server-side.
* Sensitive attributes such as Role can be supplied by the client.
* The server accepts and processes unauthorized parameters without validation.

These issues allow attackers to manipulate registration requests and obtain privileges beyond those intended for standard users.

---

## Recommendations

* Validate password and password confirmation fields on the server.
* Ignore client-supplied role assignments during registration.
* Implement an allowlist of accepted registration parameters.
* Enforce role assignment through server-side business logic only.
* Perform authorization checks on all sensitive account attributes.

By implementing proper server-side validation and access controls, these vulnerabilities can be effectively mitigated.
