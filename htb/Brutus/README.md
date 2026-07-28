# HTB Sherlock: Brutus (DFIR)

## Overview

This challenge focused on investigating authentication logs to identify evidence of brute force activity, successful compromise, persistence mechanisms, and attacker post-exploitation behavior.

**Category:** DFIR / Log Analysis
**Platform:** Hack The Box
**Difficulty:** Very Easy
**Time Taken:** ~40 minutes

## Objectives

Investigate the provided `auth.log` and related artifacts to answer the following:

* Identify attacker IP address
* Determine compromised account
* Find attacker login timestamp in UTC
* Identify SSH session ID
* Detect persistence via account creation
* Map persistence to MITRE ATT&CK
* Determine first SSH session end time
* Identify privileged command executed by attacker

## Methodology

### 1. Initial Log Inspection

First, I inspected the authentication log to understand its structure:

```bash
less auth.log
```

This made the log easier to navigate and helped identify recurring keywords such as:

* `Invalid user`
* `Failed password`
* `Accepted password`
* `session opened`
* `session closed`

These indicators suggested brute force activity and successful authentication events.

### 2. Detecting Brute Force Activity

To identify suspicious failed logins:

```bash
grep "invalid" auth.log | less
grep "Failed password" auth.log | less
```

This revealed repeated login failures from the same IP address, including attempts against invalid users and a test account.

From this pattern, I identified the attacker IP responsible for brute forcing the server.

### 3. Identifying Successful Authentication

To find successful logins:

```bash
grep "Accepted" auth.log | less
```

This showed the attacker eventually gained access to the **root** account.

### 4. Finding Login Timestamp (UTC)

The challenge required the login time from the attacker’s interactive session in **UTC**, not just the authentication event.

I reviewed successful login timestamps and compared them against session artifacts.

Command used:

```bash
grep "Accepted" auth.log | less
```

This helped narrow down the successful login before checking time conversion requirements.


### 5. Locating Session ID

SSH sessions are assigned unique session numbers.

To locate the attacker’s session:

```bash
grep "session" auth.log | less
```

This allowed me to identify the session opened for the compromised root account and extract its session ID.


### 6. Detecting Persistence

To investigate account creation activity:

```bash
grep "user" auth.log | less
grep "added" auth.log | less
```

After completing the challenge, I found better filters would have reduced noise:

```bash
grep "new user" auth.log | less
grep "group added" auth.log | less
```

These commands revealed the attacker created a new privileged account as a persistence mechanism.


### 7. MITRE ATT&CK Mapping

The persistence technique mapped to:

**Create Account: Local Account**
**Technique ID:** `T1136.001`

Reference: [https://attack.mitre.org/](https://attack.mitre.org/)

Path used:

* Techniques
* Enterprise
* Create Account
* Local Account


### 8. Identifying Session End Time

To determine when the attacker’s first SSH session ended:

```bash
grep "session closed" auth.log | less
```

I correlated the first session closure event after the successful root login.

The attacker’s initial session lasted only a few seconds.


### 9. Investigating Post-Exploitation Activity

Later in the logs, I identified suspicious command execution involving a downloaded shell script.

I initially searched:

```bash
grep "https" auth.log | less
```

This revealed a suspicious URL ending in `.sh`, indicating script download/execution activity using elevated privileges.

This led to the final task: identifying the full `sudo` command executed.


## Key Takeaways

* Learned to investigate Linux authentication logs efficiently using `grep`
* Improved ability to identify brute force patterns in `auth.log`
* Practiced correlating authentication events with session activity
* Learned to detect persistence via user creation and privilege escalation
* Reinforced MITRE ATT&CK mapping during investigations
* Realized how refining grep searches dramatically reduces investigation time

## Commands Used

```bash
less auth.log   
grep "invalid" auth.log
grep "Failed password" auth.log
grep "Accepted" auth.log
grep "session" auth.log
grep "session closed" auth.log
grep "new user" auth.log
grep "group added" auth.log
grep "https" auth.log
```

## Reflection

This was my first DFIR-style investigation challenge.
It took approximately **40 minutes** to complete and gave me hands-on practice with:

* log analysis
* brute force detection
* SSH session tracking
* persistence detection
* attacker activity reconstruction
