# 🐞 Bug Reports

Welcome to my collection of real-world security findings discovered during professional and private web application testing. These reports showcase vulnerabilities I’ve identified and responsibly disclosed.

> 🔐 All reports have been anonymized or modified to remove sensitive details, in accordance with responsible disclosure policies.

---

## 🔍 Highlights

| Bug Type                                | Severity  | Focus Area          |
|-----------------------------------------|-----------|---------------------|
| Session Fixation                        | Medium    | Checkout Flow       |
| IDOR (Insecure Direct Object Reference) | High      | User Enumeration    |
| JWT None Algorithm Abuse                | High      | KYC & Auth          |
| OAuth CSRF Reuse                        | Medium    | Google Social Login |
| 2FA Bypass                              | Medium    | Account Protection  |

---

## 📄 Reports

- [`session-fixation-checkout.md`](./session-fixation-checkout.md)  
  Victim uses attacker’s session unknowingly, resulting in PII + credit card leak to attacker.

- [`idor-subdomain-leak.md`](./idor-user-info-subdomain.md)  
  Exposed personal information of all registered users due to IDOR on wildcard subdomain.

- [`jwt-none-kyc-bypass.md`](./kyc-jwt-guid-takeover.md)  
  JWT algorithm tampering + GUID enumeration to complete KYC for other users.

- [`2fa-bypass-cookie.md`](./2fa-bypass-cookie-missing.md)  
  App failed to validate 2FA completion cookie, rendering 2FA checks useless.

- [`google-auth-csrf-reuse.md`](./google-auth-csrf.md)  
  OAuth flow accepted attacker tokens via CSRF, allowing full session compromise.

---

## 🛠 Tools & Approach

I combine manual testing with targeted automation, focusing on:

- Web app business logic flaws
- Token handling & session management
- Authentication & authorization weaknesses
- Recon using dorking, subdomain analysis, and endpoint fuzzing

Common tools: `Burp Suite`, `JWT Editor`, `Autorize`, `Google Dorking`, `gobuster`, `dig`.

---

## 💼 Looking to Hire?

I'm open to freelance or part-time penetration testing roles, especially focused on:

- Recon-heavy testing
- Black-box web application security
- Private bug bounty programs

📫 Contact me via [email](tkkagotho1@gmail.com).

