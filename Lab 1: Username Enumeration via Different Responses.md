Lab: Username Enumeration via Different Responses
Platform: PortSwigger Web Security Academy
Category: Authentication
Difficulty: Apprentice
Date Completed: 2026-05-24
Tools Used: Burp Suite Community Edition

Vulnerability Summary
The login page disclosed whether a username was valid or not by returning different error messages in the response. When an invalid username was entered, the application returned "Invalid username", but when a valid username was entered with a wrong password, it returned "Incorrect password". This difference in responses allows an attacker to enumerate valid usernames without needing any prior knowledge of the system.

Steps to Reproduce
Step 1 — Identify the Vulnerability
Submitted incorrect credentials on the login page and observed the response body carefully. The application returned the message "Invalid username", which immediately revealed that the system distinguishes between invalid usernames and invalid passwords — a classic authentication vulnerability.
Step 2 — Enumerate Valid Usernames with Burp Intruder

Captured the login POST request using Burp Suite's Intercept feature.
Sent the request to Intruder.
Set the attack type to Sniper.
Marked the username field as the injection point (payload position).
Loaded a username wordlist as the payload.
Launched the attack.

Step 3 — Identify the Valid Username

All responses returned HTTP status 200 OK, so status codes alone were not enough.
Filtered results by response length — one response had a noticeably different (longer/shorter) length compared to the rest.
Inspected that response and confirmed the message had changed from "Invalid username" to "Incorrect password" — confirming a valid username had been found.

Step 4 — Brute Force the Password

Took the discovered username and set up a new Intruder attack.
This time marked the password field as the injection point.
Loaded a password wordlist as the payload.
Launched the attack.
Identified the correct password by filtering for a 302 redirect status code — indicating a successful login.

Step 5 — Login and Confirm
Used the discovered credentials to log in successfully and complete the lab.

Root Cause
The application returned different error messages depending on whether the username or password was incorrect. This gave an attacker a clear signal to enumerate valid usernames, effectively splitting the brute-force process into two smaller, targeted attacks.

Impact
An attacker can:

Enumerate all valid usernames on the platform
Perform targeted password brute-force attacks on confirmed accounts
Significantly reduce the effort required to gain unauthorized access


Remediation

Return a generic error message for all failed login attempts, e.g. "Invalid username or password" — regardless of which field is wrong.
Implement account lockout or rate limiting to slow down brute-force attempts.
Consider adding CAPTCHA after a number of failed attempts.
Use multi-factor authentication (MFA) to reduce the impact even if credentials are compromised.


Key Takeaway
Never let a login form reveal which part of the credentials is wrong. A single consistent error message is a simple but highly effective defense against username enumeration attacks.
