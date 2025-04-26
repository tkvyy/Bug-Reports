# 🧷 Session Fixation in Checkout Flow – PII and Credit Card Data Theft

**Severity:** High  
**Status:** Unesolved  
**Test Type:** Manual session flow testing  
**Bug Type:** Session Fixation  
**Scope:** Main app + checkout subdomain

---

## 🧠 Summary

The application used a URL with a session token to authenticate users on a separate `checkout` subdomain eg https://checkout.target.com?authToken=abc123. When a user visited this link, the token was consumed and a session cookie was assigned — without verifying whether the token originated from a trusted user. This allowed an attacker to pre-generate a checkout session and send it to a victim. If the victim followed the link, any information they submitted during checkout (including credit card and PII) was stored in the attacker's session.

---

## 🌐 Affected Flow

1. Attacker visits: https://checkout.target.com?authToken=abc123
→ This sets a cookie like `session=abc123` in the victim's browser.

2. Victim fills in checkout details, including:
- Name
- Phone
- Email
- Address
- Credit Card (stored if account enabled)

3. The data is tied to the session owned by the attacker.

---

## 🔍 Steps to Reproduce

1. Attacker logs into their account, grabs the checkout tokenized URL: https://checkout.target.com?authToken=attackerSessionToken

2. Attacker sends this link to the victim (e.g., via phishing, social engineering).

3. Victim opens the link and is transparently assigned the attacker's session cookie.

4. Victim proceeds to checkout, assuming they are in their own session.

5. All entered data is stored under attacker's account — including saved card.

---

## 🧨 Impact

- Victim PII stored under attacker session
- Credit card data stolen
- Fully undetectable to the victim (no login step)
- High risk for fraud, identity theft, and trust damage

---

## 🛠️ Root Cause

- Trust placed in `authToken` passed via URL
- No session binding to IP or fingerprint
- No warning or validation on session ownership switch
- Insecure design assumption on cross-domain token use

---

## ✅ Recommendations

- Avoid session initialization from URL tokens
- Use short-lived, one-time tokens with strict validation
- Bind token to user agent, IP, or device fingerprint
- Warn and invalidate existing sessions when switching

---

## 🧩 Disclosure Timeline

- Reported: October 2024  
- Acknowledged: 14 day later  
- Fixed: unresolved  

---

