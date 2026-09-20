# HubSpot Email Verification & Device Binding

> Community guide and reference implementation for secure sign-in flows in HubSpot: email verification codes + trusted / bound devices.

[![HubSpot](https://img.shields.io/badge/HubSpot-FF7A59?style=for-the-badge&logo=hubspot&logoColor=white)](https://www.hubspot.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=for-the-badge)](CONTRIBUTING.md)

---

## Overview

This repository documents and demonstrates best practices for handling **email verification** and **device binding / trusted-device sign-in** when integrating with HubSpot accounts.

HubSpot protects accounts by:

1. Sending a one-time verification code to the user's primary (and backup) email addresses when a sign-in occurs from an unrecognized device or browser.
2. Allowing users to mark a device as **trusted** so future logins from that device skip the extra email challenge (while still supporting full 2FA).
3. Supporting stronger second-factor methods (HubSpot Mobile app, authenticator apps, SMS) that can be paired with device trust.

This community resource helps developers, admins, and integrators understand, troubleshoot, and implement these flows correctly — especially when building custom login experiences, mobile apps, or SSO integrations that interact with HubSpot.

---

## Key Concepts

| Concept | Description |
|---------|-------------|
| **Email Verification Code** | A short-lived code sent to the account's email addresses when HubSpot detects a new / unrecognized device or browser. |
| **Trusted / Bound Device** | A device (or browser + cookie combination) that the user has previously verified. Subsequent logins from a trusted device can skip the email challenge. |
| **Device Binding** | The process of associating a specific device fingerprint / cookie / mobile app instance with the HubSpot account so it is treated as trusted. |
| **Two-Factor Authentication (2FA)** | Optional but strongly recommended. When enabled, users verify with a second factor (app, SMS, or authenticator) instead of (or in addition to) the email code. |

---

## How HubSpot Email Verification Works

When a user signs in from a new or unrecognized device:

1. User enters email + password.
2. HubSpot detects that the current browser/device has no valid trusted-device cookie (or the mobile app instance is new).
3. A verification code is sent to **all primary and backup email addresses** on the account.
4. User enters the code (valid for a limited time, typically ~1 hour).
5. On success, HubSpot drops a trusted-device cookie (web) or registers the device (mobile app).
6. Future logins from the same device + browser profile can proceed without the email challenge (unless cookies are cleared, private browsing is used, or 2FA is enforced).

**Important notes:**
- Clearing cookies, using a different browser, or switching to private mode will usually trigger verification again.
- Browser extensions that block or clear cookies frequently can cause repeated verification prompts.
- When the HubSpot Mobile app is installed and used as a 2FA method, verification may be pushed to the app instead of email.

---

## Device Binding & Trusted Devices

### Web (Browser)
- After successful email (or 2FA) verification, HubSpot sets a long-lived cookie that marks the browser profile as trusted.
- Users can often choose “Don’t ask me again on this computer” during the verification step.
- The trust is tied to the combination of browser + cookie store. It is **not** a hardware-level device attestation.

### Mobile (HubSpot App)
- Installing and logging into the official HubSpot mobile app registers the device.
- The app can serve as a trusted second factor and receive push-style verification requests.
- Switching to a new phone requires re-setup of 2FA methods and re-binding.

### Best Practices for Device Binding
- Prefer enabling **2FA** (authenticator app or HubSpot Mobile) over relying solely on the email code + trusted-device cookie.
- Educate users that “trusted device” is cookie/browser-based on web and app-instance-based on mobile.
- For enterprise or high-security portals, enforce 2FA and consider additional controls (IP allow-lists, SSO with device posture, etc.).

---

## Recommended Security Setup

1. **Enable Two-Factor Authentication**  
   Account Settings → Security → Two-factor authentication  
   Supported methods:
   - HubSpot Mobile app (recommended)
   - Authenticator app (Google Authenticator, Authy, etc.)
   - SMS (where available)

2. **Save backup codes**  
   Always download and store the 10 backup codes securely when setting up 2FA.

3. **Use “Don’t ask again on this device”** only on personal, secured machines.

4. **Avoid frequent cookie clearing** or privacy extensions that wipe site data if you want to keep devices trusted.

---

## Troubleshooting Common Issues

| Symptom | Likely Cause | Resolution |
|---------|--------------|------------|
| Repeated email verification codes | Cookies being cleared, private browsing, or browser extension | Disable aggressive cookie cleaners; use a normal browser profile |
| No verification email received | Spam folder, incorrect email on account, or delayed delivery | Check spam/junk; confirm primary email in HubSpot; try “Resend code” |
| “Trusted device” message on mobile | Account expects verification from a previously bound device | Log in from the original trusted device or complete email/2FA challenge |
| Lost 2FA device | No backup method configured | Use backup codes or contact HubSpot Support / Super Admin for recovery |
| Code never arrives | Email deliverability or account email mismatch | Verify the email address on the HubSpot user profile |

---

## Community Goals

This repository exists to:

- Document the real-world behavior of HubSpot’s email verification and device-trust mechanisms.
- Collect community-contributed troubleshooting tips, screenshots, and edge-case reports.
- Provide clear guidance for developers building custom experiences that interact with HubSpot login (e.g., SSO, mobile wrappers, or support tooling).
- Encourage secure defaults: 2FA + thoughtful device trust rather than disabling security features.

---

## Contributing

We welcome contributions from the HubSpot community!

1. Fork the repository.
2. Create a feature branch (`git checkout -b docs/improve-device-binding`).
3. Make your changes (documentation, troubleshooting tips, diagrams, example flows).
4. Open a Pull Request with a clear description.

Please read [CONTRIBUTING.md](CONTRIBUTING.md) and our [Code of Conduct](CODE_OF_CONDUCT.md) before submitting.

### Good first contributions
- Add screenshots of the verification UI (web + mobile).
- Document behavior differences between free, Starter, Professional, and Enterprise portals.
- Share real-world support scripts or admin recovery procedures (without exposing private data).
- Improve accessibility notes or multi-language guidance.

---

## Related Resources

- [HubSpot Knowledge Base – Set up two-factor authentication](https://knowledge.hubspot.com/account-security/set-up-two-factor-authentication-for-your-hubspot-login)
- [HubSpot Community](https://community.hubspot.com/)
- Official HubSpot Mobile Apps (iOS / Android)

---

## License

This project is licensed under the MIT License – see the [LICENSE](LICENSE) file for details.

---

**Security Notice**  
Never share verification codes, backup codes, or account credentials in issues, pull requests, or public channels. If you believe you have found a security vulnerability in HubSpot’s authentication system, report it through HubSpot’s official security channels, not this repository.
