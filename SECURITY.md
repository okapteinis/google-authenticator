# Security Policy

## Security Features

This Google Authenticator plugin implements multiple layers of security to protect your WordPress/ClassicPress installation:

### 1. Cryptographically Secure Random Number Generation

**Feature:** Secret key generation uses PHP's `random_int()` function.

**Why:** The `random_int()` function provides cryptographically secure pseudo-random integers (CSPRNG), ensuring that generated 2FA secrets cannot be predicted or brute-forced.

**Location:** `create_secret()` function

### 2. Timing Attack Prevention

**Feature:** OTP verification uses constant-time comparison via `hash_equals()`.

**Why:** Direct comparison of OTP codes can leak information through timing differences, allowing attackers to potentially guess valid codes. The `hash_equals()` function prevents this by taking the same amount of time regardless of where strings differ.

**Location:** `verify()` function

### 3. Rate Limiting

**Feature:** Maximum of 5 failed authentication attempts per 15 minutes per user.

**Why:** Prevents brute-force attacks on OTP codes by limiting the number of attempts an attacker can make.

**Implementation:**
- Failed attempts are tracked using WordPress transients
- Counter resets automatically after 15 minutes
- Counter is cleared immediately upon successful authentication

**Location:** `check_otp()` function

### 4. Capability-Based Access Control

**Feature:** AJAX secret generation requires the `read` capability.

**Why:** Prevents unauthorized users from generating or regenerating 2FA secrets for other accounts.

**Location:** `ajax_callback()` function

### 5. Secure Error Handling

**Feature:** Proper bounds checking in Base32 decoder without error suppression.

**Why:** Error suppression operators (`@`) can hide security vulnerabilities. Explicit bounds checking ensures all edge cases are handled securely.

**Location:** `base32.php` decode() function

### 6. Input Sanitization

**Feature:** All user inputs are sanitized using WordPress sanitization functions.

**Why:** Prevents XSS and injection attacks by ensuring all data is properly escaped and validated.

**Location:** Throughout the plugin

## Threat Model

### Protected Against

✅ **Brute Force Attacks** - Rate limiting prevents automated guessing of OTP codes

✅ **Timing Attacks** - Constant-time comparison prevents information leakage

✅ **Predictable Secrets** - Cryptographically secure random generation

✅ **Unauthorized Access** - Capability checks on sensitive operations

✅ **Replay Attacks** - Time-slot tracking prevents OTP code reuse

✅ **Man-in-the-Middle Attacks** - Time-slot validation detects repeated logins

### Limitations

⚠️ **Physical Device Security** - Cannot protect against compromised mobile devices

⚠️ **Backup Codes** - Plugin does not provide backup codes (use Authenticator Plus on Android)

⚠️ **Recovery Access** - Administrator can disable 2FA for users via database

⚠️ **App Passwords** - When enabled, app passwords bypass 2FA for XMLRPC/API access

## Emergency Recovery Procedures

### User Locked Out (Too Many Failed Attempts)

**Wait Period:** 15 minutes

**Manual Reset:**
```sql
DELETE FROM wp_options WHERE option_name LIKE '%ga_login_attempts_%';
```

### User Lost Device

**Option 1: Administrator Disable**
1. WordPress Admin → Users → Edit User
2. Uncheck "Active" under Google Authenticator Settings
3. User can log in with password only

**Option 2: Database Reset**
```sql
UPDATE wp_usermeta
SET meta_value = 'disabled'
WHERE meta_key = 'googleauthenticator_enabled'
AND user_id = [USER_ID];
```

**Option 3: Plugin Deactivation**
- Via WP Admin: Plugins → Deactivate Google Authenticator
- Via FTP/SSH: Rename plugin directory or delete it

### Catastrophic Failure

If you're completely locked out:

1. **Via FTP/SSH:** Delete the plugin directory
   ```bash
   rm -rf wp-content/plugins/google-authenticator/
   ```

2. **Via Database:** Disable for all users
   ```sql
   UPDATE wp_usermeta
   SET meta_value = 'disabled'
   WHERE meta_key = 'googleauthenticator_enabled';
   ```

## Backup Recommendations

### Critical: Save Your Secret

When setting up 2FA:
1. ✅ **Write down the secret code** displayed on screen
2. ✅ **Take a screenshot** of the QR code (store securely)
3. ✅ **Test OTP generation** before logging out
4. ✅ **Keep recovery access** (don't enable 2FA on your only admin account first)

### Recommended Tools

- **Android:** [Authenticator Plus](https://play.google.com/store/apps/details?id=com.mufri.authenticatorplus) - Supports backup/restore
- **iOS:** [Authy](https://authy.com/) - Multi-device synchronization
- **Desktop:** [Authy Desktop](https://authy.com/download/) - Backup across devices

## Rate Limiting Details

### Configuration

- **Max Attempts:** 5 failures
- **Time Window:** 15 minutes
- **Scope:** Per user account
- **Storage:** WordPress transients (wp_options table)

### Transient Key Format

```
ga_login_attempts_[USER_ID]
```

### Manual Rate Limit Reset

```sql
DELETE FROM wp_options
WHERE option_name = 'ga_login_attempts_[USER_ID]';
```

## Session Security Implementation

### Two-Screen Login Flow

When two-screen authentication is enabled:
- Credentials are NOT stored in hidden form fields
- Session-based storage is used instead
- Session data is cleared immediately after successful authentication

### Session Data

Session variables used:
- `$_SESSION['ga_pending_user']` - Username
- `$_SESSION['ga_pending_pass']` - Password (hashed)
- `$_SESSION['ga_pending_remember']` - Remember me preference
- `$_SESSION['ga_pending_redirect']` - Redirect destination

**Note:** Session data is automatically cleared on successful login or session timeout.

## Reporting Security Issues

If you discover a security vulnerability:

1. **DO NOT** create a public GitHub issue
2. **Email:** security@[your-domain] (if available)
3. **Subject:** "Google Authenticator Security Issue"
4. **Include:**
   - Description of the vulnerability
   - Steps to reproduce
   - Potential impact
   - Suggested fix (if any)

### Response Time

- **Acknowledgment:** Within 48 hours
- **Initial Assessment:** Within 7 days
- **Fix Timeline:** Depends on severity (critical issues prioritized)

## Security Updates

This plugin follows semantic versioning with security considerations:

- **Major versions (X.0.0):** Breaking changes
- **Minor versions (0.X.0):** New features
- **Patch versions (0.0.X):** Bug fixes and security updates

**Subscribe to updates:**
- GitHub: Watch the repository for releases
- WordPress.org: Plugin update notifications

## Compliance & Standards

- ✅ Follows OWASP secure coding practices
- ✅ Compatible with PHP 8.0, 8.1, 8.2, 8.3, 8.4
- ✅ Tested with WordPress 6.7 and ClassicPress
- ✅ Implements TOTP (RFC 6238)
- ✅ Uses HMAC-SHA1 (RFC 2104)

## Security Changelog

### Version 0.55 (Current)
- ✅ Implemented cryptographically secure random number generation
- ✅ Added constant-time comparison for OTP verification
- ✅ Implemented rate limiting (5 attempts/15 min)
- ✅ Added capability checks to AJAX handlers
- ✅ Removed error suppression with proper bounds checking
- ✅ Enhanced input sanitization

### Version 0.54
- PHP 8.4 compatibility
- Type safety improvements
- Strict type declarations

## Best Practices

### For Site Administrators

1. **Test on non-admin account first**
2. **Keep emergency access** (another admin without 2FA)
3. **Regular security audits** of active 2FA users
4. **Monitor failed login attempts**
5. **Keep plugin updated**

### For Users

1. **Secure your mobile device** with a PIN/biometric lock
2. **Back up your secret key** in a secure location
3. **Use a reputable authenticator app**
4. **Don't share OTP codes** with anyone
5. **Report suspicious activity** immediately

## Additional Resources

- [TOTP RFC 6238](https://tools.ietf.org/html/rfc6238)
- [HOTP RFC 4226](https://tools.ietf.org/html/rfc4226)
- [Base32 RFC 4648](https://tools.ietf.org/html/rfc4648)
- [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)

## License

This security documentation is licensed under CC BY-NC-ND 4.0

---

**Last Updated:** November 2025
**Plugin Version:** 0.55
**Maintainer:** Ojārs Kapteinis
