# 🔓 2FA Bypass on Login

**Severity:** Medium  
**Status:** Resolved  
**Test Type:** Manual post-auth flow review  
**Bug Type:** Broken Authentication Logic / 2FA Enforcement Bypass

---

## 🧠 Summary

The application implemented Two-Factor Authentication (2FA) as an added layer after login, but failed to enforce it properly. Once a user authenticated with valid credentials, they needed to enter 2FA code and a 2FA cookie was *supposed* to be set before granting access.

However, I discovered that the backend did **not actually verify the presence or validity of this cookie**. This allowed me to skip the 2FA step entirely and access the account with just the username and password.

---

## 🔍 Reproduction Steps

1. Log in with valid credentials:
```
POST /api/login Body: { "email": "user@example.com", "password": "correctpassword" }
```

2. Normally, the server responds with:
```
Set-Cookie: auth=token; HttpOnly 
```

3. And then respond with a 2FA token after successfully completing 2FA verification.
```
Set-Cookie: 2fa_passed=token; HttpOnly 
```
   
4. Attacker skips 2FA page and directly accesses protected endpoints:
```
GET /dashboard
```

✅ Response returned user dashboard data — 2FA was not enforced.

---

## 🔥 Impact

- Full account takeover with just username and password  
- 2FA rendered ineffective  
- Undermines trust in platform’s security claims

---

## 🧩 Disclosure Timeline

- Reported: March 2025  
- Fixed: Same week  
- Reward: Duplicate
