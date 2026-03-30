# OWASP Juice Shop — VAPT Assessment Report

![Security Assessment](https://img.shields.io/badge/Assessment-VAPT-red)
![OWASP](https://img.shields.io/badge/OWASP-Top%2010%3A2025-orange)
![Status](https://img.shields.io/badge/Status-Complete-green)
![Risk](https://img.shields.io/badge/Overall%20Risk-HIGH-critical)
![Platform](https://img.shields.io/badge/Platform-Kali%20Linux-blue)

> **Assessor:** Satheesh Nithiananthan   
> **Target:** OWASP Juice Shop (Docker localhost + Heroku)  
> **Date:** March 17–18, 2026  
> **Type:** Vulnerability Assessment & Penetration Testing (VAPT)  
> **Classification:** CONFIDENTIAL — Training Purpose Only  

---

## Overview

This repository contains a full VAPT assessment of **OWASP Juice Shop** — a deliberately vulnerable web application used for security training.

All findings are mapped to the latest **OWASP Top 10:2025** categories.

---

## Vulnerability Summary

| # | Vulnerability | Severity | CVSS | OWASP Top 10:2025 |
|---|---|---|---|---|
| 1 | SQL Injection | 🔴 CRITICAL | 9.8 | **A05:2025** — Injection |
| 2 | Cross-Site Scripting (XSS) | 🔴 CRITICAL | 9.6 | **A05:2025** — Injection |
| 3 | Broken Authentication | 🟠 HIGH | 8.7 | **A07:2025** — Authentication Failures |
| 4 | Missing Security Headers | 🟡 MEDIUM | 5.3 | **A02:2025** — Security Misconfiguration |
| 5 | Insecure Cookie Config | 🟡 MEDIUM | 5.1 | **A04:2025** — Cryptographic Failures |
| 6 | CSRF Vulnerability | 🟡 MEDIUM | 6.5 | **A01:2025** — Broken Access Control |
| 7 | Sensitive Data Exposure | 🟡 MEDIUM | 5.9 | **A04:2025** — Cryptographic Failures |

> **Total: 7 findings · 2 Critical · 1 High · 4 Medium · Overall Risk: HIGH**

---

## OWASP Top 10:2025 Coverage
```
A01:2025 — Broken Access Control           ✅  CSRF (Finding #6)
A02:2025 — Security Misconfiguration       ✅  Missing Security Headers (#4)
A03:2025 — Software Supply Chain Failures  ⚠️  External scripts loaded without SRI
A04:2025 — Cryptographic Failures          ✅  Insecure Cookies (#5), Data Exposure (#7)
A05:2025 — Injection                       ✅  SQL Injection (#1), XSS (#2)
A06:2025 — Insecure Design                 ➖  Not tested in this scope
A07:2025 — Authentication Failures         ✅  Broken Authentication (#3)
A08:2025 — Software/Data Integrity         ⚠️  No SRI on external cookieconsent script
A09:2025 — Security Logging & Alerting    ⚠️  No monitoring or alerting detected
A10:2025 — Mishandling of Exceptions       ⚠️  Verbose error messages expose internals
```

---

## Key Findings

### 🔴 1. SQL Injection — A05:2025 (CVSS 9.8)

Login bypass without password — proven via browser testing:
```
URL:      http://localhost:3000/#/login
Email:    admin@juice-sh.op'--
Password: anything
Result:   Full admin access granted
```

The `'--` closes the SQL string and comments out the password check entirely.

---

### 🔴 2. Cross-Site Scripting (XSS) — A05:2025 (CVSS 9.6)

JavaScript execution via search and profile fields:
```html
<iframe src="javascript:alert(`xss`)">
```

No Content-Security-Policy present — injected scripts execute freely in the browser.

---

### 🟠 3. Broken Authentication — A07:2025 (CVSS 8.7)

Default credentials work with no lockout and no MFA:
```
admin@juice-sh.op / admin123  →  Full admin access
```

---

### 🟡 4. Missing Security Headers — A02:2025 (CVSS 5.3)

Confirmed via `curl -v` on both Docker and Heroku:

| Header | Status | Risk |
|---|---|---|
| `Content-Security-Policy` | ❌ Missing | XSS executes freely |
| `Strict-Transport-Security` | ❌ Missing | MITM possible |
| `Access-Control-Allow-Origin` | ⚠️ Wildcard `*` | Any site reads API responses |
| `X-Frame-Options` | ⚠️ SAMEORIGIN | Partial — DENY is stronger |
| `X-Content-Type-Options` | ✅ nosniff | Correctly configured |

---

## TLS Analysis (from curl -v)
```
Protocol:    TLSv1.2  (TLSv1.3 offered by client but server downgraded)
Cipher:      ECDHE-RSA-AES128-GCM-SHA256 / secp256r1
Certificate: CN=*.herokuapp.com  (Amazon RSA 2048)
Valid:        Jan 1 2026 → Jan 29 2027
HTTP:         HTTP/1.1  (HTTP/2 not supported — ALPN negotiation failed)
```

> Finding: Server does not support TLSv1.3 or HTTP/2 — both are current industry standard.

---

## Evidence Collected
```
juice_shop_assessment/
├── evidence/
│   ├── headers.txt              curl -i  (headers + body)
│   ├── headers_check.txt        curl -I  (headers only — cleanest)
│   └── headers_verbose2.txt     curl -v  (full TLS + headers + body)
└── screenshots/
    ├── 01_homepage.png
    ├── 02_sqli_login.png
    ├── 03_auth_login.png
    ├── 04_xss_alert.png
    ├── 05_sqli_success.png
    ├── 06_headers_curl.png
    ├── 07_cookies_storage.png
    ├── 08_csrf_no_form.png
    ├── 09_ftp_directory.png
    ├── 10_csrf_cookies.png
    ├── 11_csrf_network.png
    └── 12_zap_scan_results.png
```

---

## Remediation Timeline

| Priority | Finding | Action | Deadline |
|---|---|---|---|
| P1 🔴 | SQL Injection | Use parameterised queries | 7 days |
| P1 🔴 | XSS | Output encoding + CSP header | 7 days |
| P1 🔴 | Broken Auth | Remove defaults, enforce MFA | 7 days |
| P2 🟠 | CSRF | Add CSRF tokens to all state-changing forms | 30 days |
| P2 🟠 | Security Headers | Add CSP, HSTS, restrict CORS | 30 days |
| P2 🟠 | Data Exposure | Restrict /ftp/ directory, require auth | 30 days |
| P3 🟡 | Cookie Flags | Add Secure, HttpOnly, SameSite=Strict | 90 days |
| P3 🟡 | TLS | Enforce HTTPS, upgrade to TLS 1.3 | 90 days |

---

## Tools Used

| Tool | Purpose | Version |
|---|---|---|
| OWASP ZAP | Automated vulnerability scanning | 2.14.0+ |
| Nmap | Network reconnaissance | 7.93+ |
| Firefox + DevTools | Manual browser testing | Latest |
| curl | HTTP header and TLS analysis | 8.18.0 |
| Docker | Local Juice Shop instance | Latest |
| Kali Linux | Testing platform | Latest |

---

## Disclaimer

> This assessment was performed on a **deliberately vulnerable training application** (OWASP Juice Shop) in a fully controlled, authorised environment. All findings are strictly for educational purposes. No real production systems were accessed or harmed.

---

## Author

**Satheesh Nithiananthan**  


---

*Mapped to OWASP Top 10:2025 — the latest published OWASP web application security standard*
