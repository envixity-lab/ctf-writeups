# Sensitive Data Exposure Assessment

## Findings Summary

| Finding                                        | OWASP Category                                  | CWE     |
| ---------------------------------------------- | ----------------------------------------------- | ------- |
| User Credential Disclosure via Photo Wall      | A01: Broken Access Control                      | CWE-200 |
| Weak Security Question Recovery Process        | A07: Identification and Authentication Failures | CWE-640 |
| Password Disclosure in Authentication Response | A02: Cryptographic Failures                     | CWE-200 |
| Hardcoded Developer Credentials                | A05: Security Misconfiguration                  | CWE-798 |

---

## Finding 1: User Credential Disclosure via Photo Wall

### Overview

While reviewing the Photo Wall functionality, it was discovered that sensitive user information was exposed through a modified request.

By intercepting the request using Burp Suite and removing a specific parameter, the server returned additional information that should not have been accessible to standard users.

### Exposed Data

* Username
* Email Address
* Password Information

### Impact

An attacker could collect sensitive user information without authorization, potentially leading to account compromise, credential harvesting, or targeted attacks against users.

---

## Finding 2: Weak Security Question Recovery Process

### Overview

The application uses security questions as part of its password recovery mechanism.

During testing, a user account was found using the security question:

"Favorite place to go hiking?"

The same user had uploaded hiking photographs to the application.

### Discovery Method

Investigation included:

* Reviewing publicly available user content
* Examining image metadata
* Correlating location information with mapping services

Using information disclosed by the user, the security question answer was successfully identified.

### Impact

An attacker may be able to reset user passwords using publicly available information.

This demonstrates the weakness of knowledge-based authentication questions when answers can be researched or inferred.

---

## Finding 3: Password Disclosure in Authentication Response

### Overview

During login testing, the server returned sensitive account information within the authentication response.

### Observed Behavior

After successful authentication, the server returned the user's password as part of the response payload.

### Impact

Passwords should never be returned to the client after authentication.

Exposure of credentials increases the risk of:

* Credential theft
* Session compromise
* Unauthorized account access
* Credential reuse attacks

---

## Finding 4: Hardcoded Developer Credentials

### Overview

Application source code accessible through browser developer tools contained credentials for a test account.

### Discovery Method

The credentials were discovered by reviewing client-side application resources and configuration data.

### Observed Behavior

The account remained active and could still be used for authentication despite appearing to be intended for development or testing purposes.

### Impact

Hardcoded credentials can provide unauthorized access to application functionality and may be overlooked during deployment.

If reused elsewhere, they may also expose additional systems.

---

## Key Takeaways

This assessment identified multiple forms of sensitive information exposure, including:

* Unauthorized disclosure of user information
* Weak account recovery mechanisms
* Credential exposure through API responses
* Hardcoded credentials left in production environments

These findings highlight the importance of minimizing sensitive data exposure and implementing secure authentication and account recovery processes.
