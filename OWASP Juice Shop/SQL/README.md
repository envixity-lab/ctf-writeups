# Vulnerability Analysis

## Overview

During testing of the application's login functionality, a SQL Injection vulnerability was identified.

The login form failed to properly validate and sanitize user-supplied input before incorporating it into a database query. As a result, crafted input was interpreted as part of the SQL statement rather than as plain text, allowing the authentication logic to be manipulated.

| Vulnerability | OWASP Category | CWE    |
| ------------- | -------------- | ------ |
| SQL Injection | A03: Injection | CWE-89 |

## Application Functionality

The application requires users to authenticate by providing a username or email address and a password.

Under normal conditions, the server verifies the supplied credentials against records stored in the database before granting access.

---

## Vulnerability Breakdown

### SQL Injection in Authentication

During testing, specially crafted input was supplied to the login form.

Instead of treating the input as ordinary text, the server interpreted part of the input as SQL syntax. This altered the logic of the authentication query and allowed the login process to be bypassed.

This behavior indicates that user input is being incorporated into SQL queries without adequate validation or the use of parameterized statements.

---

## Impact

A successful SQL Injection vulnerability can allow an attacker to:

* Bypass authentication.
* Access unauthorized accounts.
* Manipulate database queries.
* Retrieve sensitive information.
* Modify or delete stored data, depending on the application's privileges.

The overall impact depends on the permissions granted to the application's database account.

---

## Root Cause

The application fails to properly separate user input from SQL commands.

Instead of treating user input as data, the database interprets portions of the supplied input as executable SQL.

This indicates that the application is constructing SQL queries using unsanitized user input rather than using parameterized queries or prepared statements.

---

## Recommendations

* Use parameterized queries (prepared statements) for all database operations.
* Validate and sanitize user input.
* Apply the principle of least privilege to database accounts.
* Return generic authentication error messages to avoid revealing database behavior.
* Regularly test for injection vulnerabilities during development and security reviews.

---

## Root Cause Summary

The vulnerability exists because user-controlled input is incorporated directly into SQL queries without proper protection.

By failing to use parameterized queries and appropriate input handling, the application allows user input to modify the logic of database queries, resulting in a SQL Injection vulnerability.
