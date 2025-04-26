# 🕵️‍♂️ IDOR on Subdomain – Mass Personal Data Exposure via User ID Enumeration

**Severity:** High  
**Status:** Retesting  
**Test Type:** Manual testing (Auth flow + API logic)  
**Bug Type:** Insecure Direct Object Reference (IDOR)

---

## 🧠 Summary

While performing a recon on a wildcard-scope target, I discovered a subdomain (`go.target.com`) through Google Dorking that exposed an internal API. This API allowed any authenticated user to enumerate user IDs and access personal information of **all registered users**, including names, emails, and contact info — without proper authorization checks.

---

## 🧨 Impact

- **Mass data exposure**: Names, emails, phone numbers of all users could be retrieved.
- **Compliance issues**: Potential violations of data privacy laws.

---

## 🔍 Steps to Reproduce
1. Login
2. Turn on Burp Suite and go to Profile
3. Observe the POST request to /api/v2/profile to host go.target.com
4. Send to Repeater and replay the request.
5. Change user_id to another number.
6. Observe you can view personal information of the user's id entered.

---

