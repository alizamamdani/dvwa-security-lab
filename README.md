# dvwa-security-lab
CS 382 Cybersecurity Homework 02

---

## Part 1: Install Docker

Docker installed and verified to ensure containers could run correctly.

![Docker Installation](screenshots/1.png)

---

## Part 2: Deploy DVWA in Docker

After installing Docker, the next step was deploying the :contentReference[oaicite:1]{index=1} container.

![DVWA Deployment](screenshots/2.1.png)

The container was started and accessed through the browser at:

```
http://localhost:8080
```

![DVWA Running](screenshots/2.2.png)

---

## Part 3: Vulnerability Testing

In this section, vulnerabilities available in DVWA are tested to understand how web applications can be exploited when proper security mechanisms are not implemented.

### Cross-Site Request Forgery (CSRF)

Cross-Site Request Forgery (CSRF) is a web vulnerability where an attacker tricks a logged-in user’s browser into sending an unintended request to a web application. If the application does not properly validate the request, it may perform actions on behalf of the user without their knowledge.

---

#### CSRF – Low Security

At the Low security level, the application does not implement any protection against CSRF attacks. The server accepts requests without validating their origin.

I creared a malicious HTML page that automatically sends a request to change the user’s password when opened.

Payload used:

```html
<html>
<body onload="document.forms[0].submit()">

<form action="http://localhost:8080/vulnerabilities/csrf/" method="GET">
<input type="hidden" name="password_new" value="hacked">
<input type="hidden" name="password_conf" value="hacked">
<input type="hidden" name="Change" value="Change">
</form>

</body>
</html>
```

When the attack page was opened while logged into DVWA, the password change request was executed successfully. This demonstrates that the application does not validate whether the request was intentionally made by the user.

![CSRF Low](screenshots/csrf_low.png)

---

#### CSRF – Medium Security

At the Medium security level, the application attempts to protect against CSRF by checking the HTTP Referer header. This check ensures that requests appear to come from the same domain.

Initially, the attack failed when the malicious HTML page was opened locally because the browser did not send a valid Referer header. However, when the malicious page was hosted using a local web server, the request included a Referer header containing `localhost`, which allowed the request to pass the validation.

This shows that relying solely on the Referer header is not a reliable defense because it can still be bypassed.

![CSRF Medium](screenshots/csrf_med.png)

---

### CSRF – High Security

At the High security level, the application introduces a stronger protection mechanism by implementing a CSRF token (`user_token`).

A CSRF token is a randomly generated value associated with the user's session. The server expects this token to be included in each request that performs sensitive actions. If the token is missing or incorrect, the request is rejected.

When the malicious request was attempted without the correct token, the password was not changed. This demonstrates that the CSRF token effectively prevents the attack.

![CSRF High](screenshots/csrf_high.png)

---

## Summary

| Security Level | Result | Protection Mechanism |
|----------------|------|----------------------|
| Low | Attack Successful | No protection |
| Medium | Partially Prevented | Referer header validation |
| High | Attack Blocked | CSRF token validation |

This experiment demonstrates how security mechanisms improve as the security level increases. While the Low level is completely vulnerable, Medium introduces basic protection that can still be bypassed, and High implements a stronger defense using CSRF tokens.
