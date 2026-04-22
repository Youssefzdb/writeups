# writeups

> Documented vulnerability findings, pentest reports, and CTF solutions.  
> Real targets, real bugs, real impact.

---

## Index

### Web Application
| # | Target | Vulnerability | Severity | Status |
|---|--------|--------------|----------|--------|
| 01 | Private Bug Bounty | Stored XSS via SVG upload | High | Fixed |
| 02 | Private Client | IDOR — accessing other users data | Critical | Fixed |
| 03 | Private Client | Auth Bypass via JWT none algorithm | Critical | Fixed |
| 04 | HackerOne Program | SQL Injection (time-based blind) | High | Fixed |

### Network / Infrastructure
| # | Scope | Finding | Severity |
|---|-------|---------|----------|
| 01 | Internal Pentest | SMB relay attack — domain compromise | Critical |
| 02 | Internal Pentest | Kerberoasting — service account cracked | High |
| 03 | External Pentest | Open redirect to phishing | Medium |

### CTF Solutions
| CTF | Challenge | Category | Points |
|-----|-----------|----------|--------|
| HTB | Forest | AD / Kerberos | 30 |
| HTB | Monteverde | Azure AD | 30 |
| TryHackMe | Wreath | Network Pivoting | — |

---

## Format

Each writeup follows this structure:
- **Target**: what was tested
- **Discovery**: how I found it
- **Exploitation**: step-by-step PoC
- **Impact**: what an attacker could do
- **Remediation**: how to fix it

---

> All findings reported responsibly. Client names kept confidential.
