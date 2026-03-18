# FUTURE_CS_01 — Vulnerability Assessment & Penetration Test
## SAIZERO — Ground Zero Defence
### *Every shadow has a hunter*

---

## Website Tested
- **Application:** OWASP Juice Shop
- **Local:** http://192.168.8.189:3000 (Docker)
- **Live:** https://juice-shop.herokuapp.com

---

## Scope
- Public-facing pages and authenticated areas (training app)
- Passive scanning + active testing on authorised application
- No exploitation of production systems
- No unauthorised access at any point

---

## Tools Used
| Tool | Purpose |
|------|---------|
| OWASP ZAP 2.14.0+ | Automated passive vulnerability scanning |
| Nmap 7.93+ | Network reconnaissance and port scanning |
| Firefox + DevTools | Manual testing, header and cookie inspection |
| curl 8.18.0 | HTTP header analysis |
| Docker | Local application hosting |
| Kali Linux | Testing platform |

---

## Vulnerabilities Found
| # | Vulnerability | Severity | CVSS | OWASP 2021 |
|---|--------------|----------|------|------------|
| 1 | SQL Injection | CRITICAL | 9.8 | A03 |
| 2 | Cross-Site Scripting (XSS) | CRITICAL | 9.6 | A03 |
| 3 | Broken Authentication | HIGH | 8.7 | A07 |
| 4 | Missing Security Headers | MEDIUM | 5.3 | A05 |
| 5 | Insecure Cookie Configuration | MEDIUM | 5.1 | A02 |
| 6 | CSRF Vulnerability | MEDIUM | 6.5 | A01 |
| 7 | Sensitive Data Exposure | MEDIUM | 5.9 | A02 |

---

## Repository Structure
- **/evidence** — curl outputs and raw tool data
- **/screenshots** — 12 numbered evidence screenshots
- **/reports** — full VAPT report PDF and ZAP HTML report

---

## Assessor
**Satheesh Nithiananthan** | SAIZERO — Ground Zero Defence
Future Interns Cybersecurity Internship 2026
