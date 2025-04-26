# 🧬 KYC Impersonation via JWT "none" Algorithm + GUID Enumeration

**Severity:** High  
**Status:** Unresolved  
**Test Type:** Manual logic testing + token manipulation  
**Bug Types:** JWT Bypass, Insecure Direct Object Reference, KYC Integrity Compromise

---

## 🧠 Summary

During KYC flow testing, I discovered a dangerous combination of two issues:

1. **JWT tokens used in KYC URLs were vulnerable to the "none" algorithm bypass**  
2. **GUIDs of users were exposed via a public leaderboard API**

By chaining both flaws, I could submit KYC documents and verify the identity of **any user on the platform** without authentication, leading to impersonation, KYC denial of service, and financial/legal risks.

---

## 📲 Affected Flow: QR-Based KYC

1. User scans a QR code: `https://www.target.com/kyc?token=<JWT>`

2. The JWT contains:
```
{
  "guid": "8e7b-****-x71g",
  "exp": 9999999999
}
```
3. The backend trusts this JWT to assign a KYC session to the specified user GUID.

## 🔍 Issues Identified

### 🛑 1. JWT "none" Algorithm Bypass

- The JWT token’s header could be tampered:
```
{
  "alg": "none",
  "typ": "JWT"
}
```

- No signature required; backend accepted unsigned tokens.

- Allowed arbitrary forgery of JWTs with any GUID.

### 👁️ 2. GUID Exposure in Leaderboard API

- API endpoint:
```
GET /api/leaderboard?limit=100
```

- Response:
```
[
  {
    "rank": 3,
    "name": "Alice",
    "guid": "8e7b-1234-x71g"
  },
  ...
]
```

- Allowed scraping of top 100 users' unique identifiers.

## 🔗Attack Chain

1. Scrape leaderboard to collect valid user GUIDs

2. Forge a JWT with:
```
{
  "alg": "none",
  "guid": "8e7b-1234-x71g"
}
```

3. Open: `https://www.target.com/kyc?token=<tampered-jwt>`

4. Submit personal KYC docs (attacker's), tied to victim's account

## 🔥 Impact
- Victims permanently blocked or misidentified
- Legal risk for platform (false verification records)
- Targeted sabotage of high-profile users

## 🧩 Disclosure Timeline
- Reported: April 2025
- Acknowledged: 3 days
- Resolved: Unresolved

