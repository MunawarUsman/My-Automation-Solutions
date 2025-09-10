
## 🚀 Core Workflows & Guides

### 1. Windows Device Lifecycle Management
- **[Autopilot Setup & Configuration](./docs/autopilot-setup.md)**
  - How to configure Microsoft Autopilot for zero-touch device deployment.
  - Creating and assigning Autopilot profiles.

- **[Software Packaging & Deployment](./docs/software-packaging.md)**
  - Step-by-step guide to creating Win32 app packages for Intune.
  - Deploying 64-bit software applications.
  - Configuring detection rules (MSI product codes, registry keys, custom scripts).

### 2. Security Hardening & Compliance
- **[Removing Bloatware](./docs/remove-bloatware.md)**
  - Comprehensive list of pre-installed app Package Family Names for removal.
  - Configuring Intune policies to uninstall built-in apps.

- **[Multi-Factor Authentication (MFA) Enforcement](./docs/mfa-enforcement.md)**
  - Configuring Azure AD Conditional Access policies.
  - Setting up Windows Hello for Business (biometric/pin login).

### 3. Automated User Remediation
- **[Self-Service Password Reset (SSPR) & Account Lockout Automation](./docs/account-lockout-automation.md)**
  - **Full workflow:** Automated detection, notification, and self-remediation for locked user accounts.
  - **Splunk Query:** Alert configuration for Windows Security Event ID 4740.
  - **Power Automate Flow:** Template for sending user notification emails with reset links.
  - **Azure AD Configuration:** SSPR and account lockout threshold policies.

## 🛠️ Prerequisites & Setup

To use the automation and configurations in this repo, ensure you have the following:

1.  **Licenses:**
    - Microsoft Intune license.
    - Azure AD Premium P1 or P2 (for Conditional Access & SSPR).
2.  **Tools & Access:**
    - Access to the [Microsoft Intune admin center](https://intune.microsoft.com/).
    - Access to the [Microsoft Entra admin center](https://entra.microsoft.com/).
    - A Splunk instance (for SIEM alerts).
    - Microsoft Power Automate (for workflow automation).

## 📋 Quick Start: Account Lockout Automation

This is the flagship automated workflow documented in this repository.

```mermaid
sequenceDiagram
    participant User
    participant Azure AD
    participant Splunk
    participant PowerAutomate
    participant IT Help Desk

    User->>Azure AD: Enters wrong password (3x)
    Azure AD->>Azure AD: Locks account<br>Logs Event ID 4740
    Splunk->>Splunk: Detects Event ID 4740
    Splunk->>PowerAutomate: Triggers Webhook with user details
    PowerAutomate->>User: Sends notification email with SSPR link
    User->>Azure AD: Clicks link, verifies identity, resets password
    Azure AD->>Azure AD: Automatically unlocks account
    User->>Azure AD: Attempts login with new password
    Azure AD->>User: Prompts for MFA (Conditional Access)
    alt MFA Success
        User->>Azure AD: Provides correct MFA token
        Azure AD->>User: Grants access 🎉
    else MFA Failure
        User->>Azure AD: Provides wrong/no MFA token
        Azure AD->>User: Blocks login, account remains locked
        User->>IT Help Desk: Contacts for manual support
    end