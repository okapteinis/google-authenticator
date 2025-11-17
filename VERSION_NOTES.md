# Version 0.56 - Comprehensive Security Release

**Release Date:** 2025-11-17
**Supersedes:** Version 0.55

---

## Why Version 0.56 Supersedes 0.55

Version 0.55 included some important security improvements (random_int, hash_equals, type hints). However, **Version 0.56 provides SIGNIFICANTLY MORE comprehensive security fixes** that were missing from 0.55.

### Additional Critical Security Features in 0.56 (Not in 0.55)

1. **✅ Rate Limiting** - Prevents brute force attacks (5 attempts per 15 minutes)
2. **✅ CSRF Protection** - Complete nonce verification on all forms
3. **✅ Session-Based Auth** - Eliminates password exposure in HTML forms
4. **✅ Enhanced Input Validation** - Comprehensive type and format checking
5. **✅ File Existence Checks** - Prevents fatal errors from missing dependencies

### Security Features Common to Both 0.55 and 0.56

- ✅ Cryptographically secure secret generation (random_int)
- ✅ Timing attack protection (hash_equals)
- ✅ PHP 8.0+ strict types
- ✅ Base32 error suppression removal

---

## Complete Feature Matrix

| Security Feature | v0.54 | v0.55 | v0.56 |
|-----------------|-------|-------|-------|
| **Crypto secure secrets (random_int)** | ❌ | ✅ | ✅ |
| **Timing attack protection (hash_equals)** | ❌ | ✅ | ✅ |
| **Rate limiting (5/15min)** | ❌ | ❌ | ✅ |
| **CSRF protection (setup page)** | ❌ | ❌ | ✅ |
| **Session-based secondary login** | ❌ | ❌ | ✅ |
| **Enhanced input validation** | ❌ | ❌ | ✅ |
| **File existence checks** | ❌ | ❌ | ✅ |
| **PHP 8.0+ strict types** | ❌ | ✅ | ✅ |
| **Comprehensive security audit** | ❌ | Partial | ✅ |

---

## Upgrade Path

### From v0.54 → v0.56
Direct upgrade recommended. All critical vulnerabilities fixed.

### From v0.55 → v0.56
**HIGHLY RECOMMENDED UPGRADE**. Version 0.56 adds critical security features missing from 0.55:
- Rate limiting to prevent brute force
- CSRF protection
- Session-based authentication
- Enhanced validation

---

## Documentation

- **SECURITY_AUDIT_REPORT.md** - Detailed vulnerability analysis
- **SECURITY_FIXES_CHANGELOG.md** - Complete fix documentation
- **README.md** - General plugin information

---

## Testing Status

All features tested with:
- ✅ WordPress 5.6 - 6.7
- ✅ PHP 8.0, 8.1, 8.2, 8.3, 8.4
- ✅ ClassicPress compatibility
- ✅ Multi-site installations

---

**Recommendation:** Use version 0.56 for production deployments as it provides the most comprehensive security improvements.
