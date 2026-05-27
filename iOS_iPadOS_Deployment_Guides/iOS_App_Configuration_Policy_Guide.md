# iOS/iPadOS App Configuration Policy Guide – Microsoft Intune

| | |
|---|---|
| **Document Type** | Work Instruction – Configuration Reference |
| **Author** | Sr. Modern Work Consultant |
| **Last Updated** | May 2026 |
| **Version** | 1.0 |
| **Applies To** | iOS/iPadOS 16.0 and later, Microsoft Intune, App Configuration Policies |
| **Microsoft Reference** | Intune App Configuration Policies – Managed Devices & Managed Apps |

---

## Table of Contents

1. [Overview & Scope](#1-overview--scope)
2. [Policy Types Explained](#2-policy-types-explained)
3. [Prerequisites](#3-prerequisites)
4. [Policy Architecture & Naming Convention](#4-policy-architecture--naming-convention)
5. [Microsoft Outlook](#5-microsoft-outlook)
6. [Microsoft Edge](#6-microsoft-edge)
7. [Microsoft Teams](#7-microsoft-teams)
8. [Microsoft OneDrive](#8-microsoft-onedrive)
9. [Microsoft Defender for Endpoint](#9-microsoft-defender-for-endpoint)
10. [Company Portal](#10-company-portal)
11. [Security vs Operational Settings Classification](#11-security-vs-operational-settings-classification)
12. [Deployment Notes](#12-deployment-notes)
13. [References](#13-references)

---

## 1. Overview & Scope

App Configuration Policies (ACP) allow administrators to pre-configure app settings and behavior **before users open an app** and **without requiring users to configure settings themselves**. Unlike App Protection Policies, which control what data can leave an app, App Configuration Policies control how the app itself is set up and behaves.

**This guide covers:**

- The two ACP policy types: Managed Devices vs Managed Apps
- Configuration keys, value types, and recommended values for Microsoft Outlook, Edge, Teams, OneDrive, Defender for Endpoint, and Company Portal
- Corporate vs BYOD recommended values
- Security-focused vs operational setting classification

**This guide does not cover:**

- App Protection Policy (MAM) settings — see [iOS_App_Protection_Policy_Guide.md](iOS_App_Protection_Policy_Guide.md)
- Device Configuration / Settings Catalog policies — see [iOS_CIS_Recommended_Settings_Guide.md](iOS_CIS_Recommended_Settings_Guide.md)

> **Important:** All configuration keys are **case-sensitive**. Incorrect casing causes the setting to be silently ignored. Always use the exact key format shown in this guide.

---

## 2. Policy Types Explained

Microsoft Intune supports two types of App Configuration Policies for iOS. Choosing the correct type is critical — settings from the wrong type will not be delivered to the app.

### 2.1 Managed Devices

| Attribute | Details |
|---|---|
| **Delivery channel** | MDM OS channel (Intune MDM profile) |
| **Device enrollment required** | Yes — device must be Intune-enrolled (any enrollment type) |
| **Targeting** | Targets the device; app configuration delivered regardless of which account is signed in |
| **IntuneMAMUPN support** | Yes — identifies the enrolled user account within the app |
| **Configuration check-in** | On app launch and after each policy sync |
| **Use case** | Corporate-owned supervised devices, BYOD Device Enrollment |

### 2.2 Managed Apps

| Attribute | Details |
|---|---|
| **Delivery channel** | MAM / Intune App SDK channel |
| **Device enrollment required** | No — works on enrolled or unenrolled devices |
| **Targeting** | Targets the user's work account within the app, on any device |
| **IntuneMAMUPN support** | No — not available in Managed Apps type |
| **Configuration check-in** | Every 30 minutes when paired with an App Protection Policy; every 720 minutes (12 hours) standalone |
| **Use case** | BYOD (User Enrollment or no enrollment), any device where app-level control is needed |

### 2.3 Choosing the Right Type

| Scenario | Recommended ACP Type | Notes |
|---|---|---|
| Corporate supervised iPhone (ADE) | **Managed Devices** | MDM channel; full `com.microsoft.outlook.*` configuration available |
| BYOD with any Intune enrollment (Device, User, or Web-Based) | **Managed Devices** | MDM channel; all standard configuration keys work |
| MAM-unenrolled (no Intune enrollment, MAM-only) | **Not applicable for `com.microsoft.outlook.*` keys** | MDM channel keys cannot be delivered via Managed Apps ACP; account setup occurs via Modern Auth sign-in; data protection is enforced by App Protection Policy |

> **MDM key limitation for MAM-unenrolled users:** The `com.microsoft.outlook.*` configuration keys are MDM-channel keys delivered by the device OS. They cannot be delivered via a Managed Apps ACP (`targetedManagedAppConfiguration`). Reassigning a Managed Device ACP to user groups instead of device groups does not change this — the **policy type** determines the delivery channel, not the assignment target. For MAM-unenrolled users, App Protection Policy enforces data security, and Outlook account configuration occurs through the standard Modern Auth sign-in flow without ACP pre-configuration.

---

## 3. Prerequisites

| Requirement | Notes |
|---|---|
| Microsoft Intune license (Plan 1 or higher) | Required |
| App deployed via Intune (as Required or Available) | App must be assigned to users/devices through Intune |
| App supports Intune App SDK | All Microsoft 365 apps qualify; verify third-party apps |
| Entra user groups created for assignment | App Configuration Policies are assigned to user or device groups |
| For Managed Devices: device must be Intune-enrolled | MDM enrollment required before Managed Devices ACP is delivered |
| For Managed Apps: App Protection Policy recommended | Enables 30-minute configuration check-in cadence |

---

## 4. Policy Architecture & Naming Convention

Create **one ACP policy per App and per device management type, Corporated/BYOD**. Combine all settings for an app into a single policy of each type — no need for a separate policy per setting.

| Suggested Policy Name | App | Policy Type | Target |
|---|---|---|---|
| `iOS_iPadOS - ACP - DEV - Outlook Settings - Corporate Devices` | Microsoft Outlook | Managed Devices | Corporate enrolled devices (VPP app) |
| `iOS_iPadOS - ACP - DEV - Outlook Settings - BYOD Devices` | Microsoft Outlook | Managed Devices | BYOD enrolled devices (App Store app) |
| `iOS_iPadOS - ACP - DEV - Microsoft Edge - Corporate Devices` | Microsoft Edge | Managed Devices | Enrolled corporate devices |
| `iOS_iPadOS - ACP - DEV - Microsoft Edge - BYOD Devices` | Microsoft Edge | Managed Devices | Enrolled BYOD devices |
| `iOS_iPadOS - ACP - DEV - Microsoft Teams - Corporate Devices` | Microsoft Teams | Managed Devices | Enrolled corporate devices (VPP app) |
| `iOS_iPadOS - ACP - DEV - Microsoft Teams - BYOD Devices` | Microsoft Teams | Managed Devices | Enrolled BYOD devices |
| `iOS_iPadOS - ACP - DEV - Microsoft OneDrive - Corporate Devices` | Microsoft OneDrive | Managed Devices | Enrolled corporate devices (VPP app) |
| `iOS_iPadOS - ACP - DEV - Microsoft OneDrive - BYOD Devices` | Microsoft OneDrive | Managed Devices | Enrolled BYOD devices |
| `iOS_iPadOS - ACP - DEV - SharePoint - Corporate Devices` | Microsoft SharePoint | Managed Devices | Enrolled corporate devices (VPP app) |
| `iOS_iPadOS - ACP - DEV - SharePoint - BYOD Devices` | Microsoft SharePoint | Managed Devices | Enrolled BYOD devices |
| `iOS_iPadOS - ACP - DEV - Microsoft Defender - Corporate Devices` | Microsoft Defender for Endpoint | Managed Devices | Enrolled corporate devices (VPP app) |
| `iOS_iPadOS - ACP - DEV - Microsoft Defender - BYOD Devices` | Microsoft Defender for Endpoint | Managed Devices | Enrolled BYOD devices (App Store app) |
| `iOS_iPadOS - ACP - DEV - Microsoft Authenticator - Shared Device Mode` | Microsoft Authenticator | Managed Devices | Shared Device Mode (SDM) devices |

> **VPP vs App Store:** Corporate Devices policies target the **VPP (Volume Purchase Program)** app deployed via Intune. BYOD Devices policies target the **iOS App Store** version of the same app. Both use `iosMobileAppConfiguration` (Managed Device type). Separate policies are required because the targeted app GUIDs differ between VPP and App Store deployments.

> **Configuration path:** Intune > Apps > App Configuration Policies > Create > **iOS/iPadOS**

---

## 5. Microsoft Outlook

Outlook carries the highest data risk of any Microsoft 365 app on mobile. App Configuration Policies for Outlook control account setup, security features, external sharing warnings, and data sync behavior.

> **Two Outlook policies in this baseline:** The Corporate Devices policy targets the **VPP (Volume Purchase Program)** app deployed via Intune. The BYOD Devices policy targets the **iOS App Store** version. Settings are delivered via the MDM channel on all enrolled devices. MAM-unenrolled users receive no ACP — account setup happens through the Outlook Modern Auth sign-in flow, with data protection enforced by App Protection Policy (L2).

### 5.1 Account Configuration

| Configuration Key | Corporate (Managed Devices) | BYOD (Managed Apps) | User Impact | Notes |
|---|---|---|---|---|
| `com.microsoft.outlook.EmailProfile.EmailAddress` | `{{userprincipalname}}` | `{{userprincipalname}}` | None | Auto-populates user's work email address |
| `com.microsoft.outlook.EmailProfile.EmailAccountName` | `Corporate Email` | `Work Email` | None | Friendly display name for the account |
| `com.microsoft.outlook.EmailProfile.AccountType` | `ModernAuth` | `ModernAuth` | None | Enforces Modern Authentication (OAuth); blocks Basic Auth |

> **IntuneMAMUPN:** For Managed Device policies on enrolled devices, `IntuneMAMUPN` is configured as a standard key-value pair (`{{userprincipalname}}`) alongside other Outlook configuration keys. It identifies the enrolled user's work account within Outlook for MAM-related behaviors. Include it in Corporate Devices policies where `IntuneMAMAllowedAccountsOnly` is also set. For BYOD enrolled devices in this baseline, Outlook auto-populates the account via Modern Auth — `IntuneMAMUPN` is not required in the BYOD Managed Device policy unless single-account enforcement is needed.

### 5.2 Security Settings

| Configuration Key | Corporate Value | BYOD Value | User Impact | Security Benefit |
|---|---|---|---|---|
| `com.microsoft.outlook.Mail.ExternalRecipientsToolTipEnabled` | `true` | `true` | Low | Warns user when sending to external (non-corporate) email addresses; reduces accidental data leakage |
| `com.microsoft.outlook.Mail.BlockFileImport` | `true` | `false` | Medium | Blocks attaching files from personal cloud storage (personal iCloud, Dropbox, Google Drive); BYOD set false to allow personal files |
| `com.microsoft.outlook.Mail.SMIMEEnabled` | `true` | `false` | Medium | Enables S/MIME email signing and encryption; requires certificate deployment |
| `com.microsoft.outlook.Mail.SMIMEAllowUserChange` | `false` | `true` | Low | Prevents users from disabling S/MIME on corporate devices; BYOD allows flexibility |
| `com.microsoft.outlook.Auth.Biometric` | `true` | `true` | Medium | Requires Face ID or Touch ID to open Outlook; applied on both tracks |

> **S/MIME prerequisite:** Enabling S/MIME requires a signing/encryption certificate deployed via Intune Certificate Profile (SCEP or PKCS). See Section 13 of *iOS_CIS_Recommended_Settings_Guide.md* for certificate profile guidance. Do not enable S/MIME without first confirming certificate deployment.

### 5.3 Data & Sync Settings

| Configuration Key | Corporate Value | BYOD Value | User Impact | Notes |
|---|---|---|---|---|
| `com.microsoft.outlook.Mail.FocusedInbox` | `false` | `true` | Low | Disable Focused Inbox on corporate devices (users see all mail); enable on BYOD for convenience |
| `com.microsoft.outlook.Mail.SyncWindowInDays` | `30` | `14` | Low | Days of email to sync; limits historical data stored on device |
| `com.microsoft.outlook.Contacts.SaveContacts` | `false` | `true` | Medium | Corporate: prevents Outlook contacts from copying to native iOS Contacts; BYOD: allow for user convenience |
| `com.microsoft.outlook.Contacts.SyncEnabled` | `false` | `true` | Medium | Corporate: prevents contact sync to native iOS Contacts app (data leakage risk); BYOD: allow |
| `com.microsoft.outlook.Calendar.SyncEnabled` | `true` | `true` | Low | Enables calendar sync for scheduling; recommended enabled for all |

### 5.4 Feature Restrictions

| Configuration Key | Corporate Value | BYOD Value | User Impact | Security Benefit |
|---|---|---|---|---|
| `com.microsoft.outlook.Mail.BlockPrinting` | `true` | `false` | Low | Blocks printing of emails and attachments on corporate devices |
| `com.microsoft.outlook.Mail.OfficeFeedEnabled` | `false` | `false` | Low | Disables the Office Feed (recent activity and document suggestions); reduces data exposure and distraction |
| `com.microsoft.outlook.Mail.DataClassificationEnabled` | `true` | `false` | Low | Enforces Microsoft Purview sensitivity labels on emails; requires Information Protection licensing |

### 5.5 Deployed Outlook Configuration Summary

| Setting | Key | Corporate | BYOD |
|---|---|---|---|
| Modern Auth | `com.microsoft.outlook.EmailProfile.AccountType` | ModernAuth | ModernAuth |
| Account pre-population (UPN) | `com.microsoft.outlook.EmailProfile.EmailUPN` | `{{userprincipalname}}` | `{{userprincipalname}}` |
| Account pre-population (email) | `com.microsoft.outlook.EmailProfile.EmailAddress` | `{{mail}}` | `{{mail}}` |
| Single account enforcement | `IntuneMAMAllowedAccountsOnly` | Enabled | Not configured |
| MAM user identity | `IntuneMAMUPN` | `{{userprincipalname}}` | Not configured |
| Require Biometric | `com.microsoft.outlook.Auth.Biometric` | true | true |
| External Recipient Warning | `com.microsoft.outlook.Mail.ExternalRecipientsToolTipEnabled` | true | true |
| Block File Import (personal cloud) | `com.microsoft.outlook.Mail.BlockFileImport` | true | Not configured |
| Block Printing | `com.microsoft.outlook.Mail.BlockPrinting` | true | Not configured |
| Contacts sync to iOS Contacts | `com.microsoft.outlook.Contacts.LocalSyncEnabled` | true (locked) | false |
| Contacts sync user change | `com.microsoft.outlook.Contacts.LocalSyncEnabled.UserChangeAllowed` | false | Not configured |
| Focused Inbox | `com.microsoft.outlook.Mail.FocusedInbox` | false | false |
| Suggested Replies | `com.microsoft.outlook.Mail.SuggestedRepliesEnabled` | false | false |
| Play My Emails | `com.microsoft.outlook.Mail.PlayMyEmailsEnabled` | false | false |
| Office Feed | `com.microsoft.outlook.Mail.OfficeFeedEnabled` | false | false |
| Thread view | `com.microsoft.outlook.Mail.OrganizeByThreadEnabled` | false | false |

---

## 6. Microsoft Edge

Edge is the required managed browser for iOS in this baseline. It must be deployed and configured before enabling "Restrict web content transfer to Microsoft Edge" in App Protection Policies or "Block Safari" in device configuration.

### 6.1 Homepage & New Tab

| Configuration Key | Corporate Value | BYOD Value | User Impact | Notes |
|---|---|---|---|---|
| `com.microsoft.intune.mam.managedbrowser.homepage` | Corporate intranet URL (e.g., `https://intranet.yourdomain.com`) | Default (blank) | Low | Sets the browser home page; operational |
| `com.microsoft.intune.mam.managedbrowser.NewTabPage.CustomURL` | Corporate portal URL | Default (blank) | Low | Sets new tab page; operational |

### 6.2 URL Filtering

| Configuration Key | Corporate Value | BYOD Value | User Impact | Security Benefit |
|---|---|---|---|---|
| `com.microsoft.intune.mam.managedbrowser.AllowListURLs` | Define only if using strict allowlist (e.g., `https://yourdomain.com\|https://*.sharepoint.com`) | Not configured | High | Restricts browsing to approved sites only; all other URLs blocked |
| `com.microsoft.intune.mam.managedbrowser.BlockListURLs` | Block known risky/personal sites (e.g., `https://dropbox.com\|https://personal.cloud.com`) | Not configured | Medium | Explicitly blocks specific URLs; all other sites allowed |

> **AllowList vs BlockList:** If both `AllowListURLs` and `BlockListURLs` are defined in the same policy, **only the AllowList is honored** — the BlockList is ignored. Use one or the other. AllowList is stricter (whitelist approach); BlockList is more permissive (blocklist approach). For most organizations, a BlockList of known high-risk sites is more practical than a full AllowList.

> **URL format:** Use `|` (pipe character) to separate multiple URLs. Wildcards are supported: `*.yourdomain.com` matches all subdomains. Do not include trailing slashes.

### 6.3 Privacy & Security Features

| Configuration Key | Corporate Value | BYOD Value | User Impact | Security Benefit |
|---|---|---|---|---|
| `com.microsoft.intune.mam.managedbrowser.InPrivateEnabled` | `false` | `true` | Medium | Disables InPrivate (private) browsing on corporate devices; prevents untracked browsing sessions |
| `com.microsoft.intune.mam.managedbrowser.SmartScreenEnabled` | `true` | `true` | None | Enables Microsoft Defender SmartScreen; blocks known phishing and malware sites |
| `com.microsoft.intune.mam.managedbrowser.CertificateTransparencyEnabled` | `true` | `true` | None | Validates certificate transparency; detects fraudulent certificates |
| `com.microsoft.intune.mam.managedbrowser.CookieBlockingLevel` | `blockThirdPartyCookies` | `blockThirdPartyCookies` | Low | Blocks cross-site tracking cookies; use `blockAllCookies` for highest privacy (may break sites) |

### 6.4 Search & Bookmarks

| Configuration Key | Corporate Value | BYOD Value | User Impact | Notes |
|---|---|---|---|---|
| `com.microsoft.intune.mam.managedbrowser.SearchEngineDefault` | `https://www.bing.com/search?q={searchTerms}` | Default | Low | Sets default search engine; operational |
| `com.microsoft.intune.mam.managedbrowser.AllowSearchEngineCustomization` | `false` | `true` | Low | Prevents users from changing search engine on corporate devices |
| `com.microsoft.intune.mam.managedbrowser.ManagedBookmarks` | JSON array of bookmark objects | Not configured | Low | Pre-populates managed bookmarks (see format below) |

**Managed Bookmarks format:**

```json
[{"name":"IT Helpdesk","url":"https://helpdesk.yourdomain.com"},{"name":"Company Intranet","url":"https://intranet.yourdomain.com"},{"name":"HR Portal","url":"https://hr.yourdomain.com"}]
```

> Managed bookmarks appear in a dedicated "Managed" folder in Edge and cannot be deleted by users.

### 6.5 Password Manager

| Configuration Key | Corporate Value | BYOD Value | User Impact | Notes |
|---|---|---|---|---|
| `com.microsoft.intune.mam.managedbrowser.PasswordManagerSyncEnabled` | `true` | `true` | Low | Allows Edge to save and sync passwords; pairs with Microsoft Authenticator |

### 6.6 Recommended Edge Configuration Summary

| Setting | Corporate (Supervised) | BYOD |
|---|---|---|
| Homepage | Corporate intranet | Not set |
| InPrivate browsing | Disabled | Enabled |
| SmartScreen | Enabled | Enabled |
| Certificate Transparency | Enabled | Enabled |
| Cookie Blocking | Third-party | Third-party |
| Block List URLs | High-risk/personal sites | Not configured |
| Managed Bookmarks | IT, HR, Intranet portals | Not configured |
| Allow Search Engine Change | Disabled | Enabled |

---

## 7. Microsoft Teams

Teams configuration policies focus primarily on notification privacy and account access control.

### 7.1 Notification Configuration

> **Note:** Teams notification privacy for org data is controlled via **App Protection Policy** (Org data notifications = `Block org data`), not App Configuration Policy. The keys below handle other notification behaviors.

| Configuration Key | Corporate Value | BYOD Value | User Impact | Notes |
|---|---|---|---|---|
| `com.microsoft.teams.chat.notifications.IntuneMAMOnly` | `1` | `1` | Low | Controls chat notifications for Intune-managed accounts; `1` = on, `0` = off |
| `com.microsoft.teams.channel.notifications.IntuneMAMOnly` | `1` | `1` | Low | Controls channel notifications for managed accounts |
| `com.microsoft.teams.other.notifications.IntuneMAMOnly` | `1` | `1` | Low | Controls all other Teams notifications for managed accounts |

> These notification keys ensure Teams notifications are delivered for the work account. Setting them to `0` suppresses all notifications — only configure `0` if there is a specific operational reason.

### 7.2 Account Configuration (Managed Devices Only)

| Configuration Key | Corporate Value | BYOD Value | User Impact | Notes |
|---|---|---|---|---|
| `com.microsoft.teams.account.AllowedAccounts` | Corporate domain (e.g., `yourdomain.com`) | Not configured | High | Restricts Teams to corporate accounts only; prevents personal Microsoft accounts |
| `domain_name` | `yourdomain.com` | Not configured | Low | Pre-fills the corporate domain at sign-in; operational convenience |

> **Single account enforcement:** Setting `AllowedAccounts` to your corporate domain prevents users from signing into Teams with personal Microsoft accounts on corporate devices. This is recommended for supervised corporate devices (L2).

### 7.3 Recommended Teams Configuration Summary

| Setting | Corporate (Supervised) | BYOD |
|---|---|---|
| Chat/Channel/Other Notifications | Enabled (1) | Enabled (1) |
| Restrict to Corporate Accounts | Enabled | Not configured |
| Pre-fill Domain | Configured | Not configured |
| Org Data on Lock Screen | Blocked (via APP) | Blocked (via APP) |

---

## 8. Microsoft OneDrive

OneDrive configuration policies control which accounts can sync and what organizational data can be accessed.

### 8.1 Account & Sync Settings

| Configuration Key | Corporate Value | BYOD Value | User Impact | Security Benefit |
|---|---|---|---|---|
| `com.microsoft.sharepoint.allowedTenantID` | Corporate tenant ID (from Entra ID) | Not configured | High | Restricts OneDrive sync to the specified tenant only; blocks personal Microsoft accounts |
| `com.microsoft.sharepoint.blockPersonalOneDrive` | `true` | `false` | High | Prevents access to personal OneDrive (personal Microsoft account); forces corporate OneDrive only |

> **Finding your Tenant ID:** In Microsoft Entra ID > Overview, the Tenant ID (Directory ID) is displayed. Copy this GUID and use it as the `allowedTenantID` value.

### 8.2 Recommended OneDrive Configuration Summary

| Setting | Corporate (Supervised) | BYOD |
|---|---|---|
| Block personal OneDrive | Enabled | Disabled |
| Restrict to corporate tenant | Configured | Not configured |
| Data protection (save/receive) | Via App Protection Policy | Via App Protection Policy |

> **Note:** OneDrive file sync and Known Folder Move features are primarily Windows-focused. Most OneDrive data protection for iOS is enforced through App Protection Policy (save copies, receive data, backup restrictions) rather than App Configuration Policy.

---

## 9. Microsoft Defender for Endpoint

Defender for Endpoint App Configuration settings control the automated onboarding behavior and feature enablement for the MDE app on iOS. These settings are covered in detail in *Defender for Endpoint for iOS_iPadOS Devices.md*. The key settings are listed below for completeness.

### 9.1 Key Configuration Summary

**App Configuration Policy keys (configured under Apps > App Configuration Policies > Managed Devices):**

| Configuration Key | Value Type | Recommended Value | Notes |
|---|---|---|---|
| `issupervised` | String | `{{issupervised}}` | Required for all deployments; passes supervised status to MDE |
| `WebProtection` | String | `true` | Enables web-based threat protection; set to `false` to disable (e.g., VPN conflict scenarios) |
| `DefenderNetworkProtectionEnable` | String | `true` | Enables network protection (Wi-Fi threat detection) |
| `DefenderExcludeURLInReport` | Boolean | `true` | Excludes domain names from phishing alert reports (privacy); use Boolean for MDM, String for MAM |
| `DefenderTVMPrivacyMode` | String | `true` | Privacy mode for Threat & Vulnerability Management app inventory |
| `UserEnrollmentEnabled` | String | `true` | Required only for Apple User Enrollment (BYOD) deployments |

**VPN profile key-value pairs (configured in the VPN Device Configuration profile — not in App Configuration Policy):**

| Key | Value | Used In | Notes |
|---|---|---|---|
| `SilentOnboard` | `True` | Zero-touch VPN profile | Enables silent onboarding without user interaction (unsupervised devices) |
| `AutoOnboard` | `True` | Auto-Onboarding VPN profile | Automates VPN setup without requiring user action during onboarding |
| `EnableVPNToggleInApp` | `TRUE` | Either VPN profile | Allows users to toggle VPN from within the Defender app |

> **Important:** `SilentOnboard` and `AutoOnboard` are **VPN profile key-value pairs**, not App Configuration Policy keys. Placing them in an App Config Policy will have no effect. See *Defender for Endpoint for iOS_iPadOS Devices.md* for the full VPN profile configuration.

> Refer to *Defender for Endpoint for iOS_iPadOS Devices.md* for the complete MDE deployment procedure, including Control Filter profile (supervised), VPN profiles (unsupervised), network protection, and privacy controls.

---

## 10. Company Portal

Company Portal is the enrollment and compliance management app for BYOD devices. App Configuration Policies for Company Portal control enrollment notifications and branding.

### 10.1 Enrollment Notifications

Configure enrollment reminder notifications to prompt users who have not completed enrollment.

| Setting | Location in Intune | Recommended Value | Notes |
|---|---|---|---|
| Enrollment notifications | Devices > Enrollment > Enrollment notifications | Enabled | Sends push notification and/or email to users who have not completed enrollment |
| Notification type | Push + Email | Both | Push for immediacy; email for detailed instructions |
| Custom notification subject | Text (max 200 chars) | `Complete Your Device Enrollment – Action Required` | Clear, actionable subject line |
| Custom notification body | Text (max 2000 chars) | Include: step-by-step enrollment link, IT helpdesk contact, deadline | Guides user to self-service enrollment |

### 10.2 Branding & Contact Information

Configure Company Portal branding under **Tenant Administration > Customization** (applies to all platforms).

| Setting | Recommended Value | Notes |
|---|---|---|
| Organization name | Your organization name | Displayed throughout Company Portal |
| IT department contact name | IT Helpdesk / Support Team | Shown on "Contact IT" screen |
| IT department phone number | Helpdesk phone number | Allows users to call support directly from app |
| IT department email address | helpdesk@yourdomain.com | For email-based support requests |
| IT department website URL | Support portal URL | Links to self-service portal or knowledge base |
| Privacy statement URL | Corporate privacy policy URL | Required for compliance in some regions |
| Company logo (light background) | Corporate logo (PNG, 400×400px recommended) | Shown in Company Portal and enrollment screens |

---

## 11. Security vs Operational Settings Classification

Use this table to prioritize which settings to deploy first. Security-focused settings should be part of the initial baseline deployment; operational settings can be deployed in a second wave or as part of the standard user experience configuration.

### High Security Impact (Deploy in Baseline)

| App | Setting | Key | Risk Mitigated |
|---|---|---|---|
| Outlook | Block File Import | `com.microsoft.outlook.Mail.BlockFileImport` | Prevents personal cloud storage as email attachment source |
| Outlook | External Recipient Warning | `com.microsoft.outlook.Mail.ExternalRecipientsToolTipEnabled` | Prevents accidental data leakage to external parties |
| Outlook | Require Biometric | `com.microsoft.outlook.Auth.Biometric` | Unauthorized access to email |
| Outlook | Disable Contact Sync | `com.microsoft.outlook.Contacts.SyncEnabled` | Prevents corporate contacts from exporting to iOS Contacts |
| Outlook | S/MIME Enforcement | `com.microsoft.outlook.Mail.SMIMEEnabled` | Email interception, impersonation |
| Edge | Block InPrivate | `com.microsoft.intune.mam.managedbrowser.InPrivateEnabled` | Untracked browser sessions on corporate devices |
| Edge | SmartScreen | `com.microsoft.intune.mam.managedbrowser.SmartScreenEnabled` | Phishing and malware in browser |
| Edge | URL Block List | `com.microsoft.intune.mam.managedbrowser.BlockListURLs` | Access to risky or prohibited sites |
| Edge | Certificate Transparency | `com.microsoft.intune.mam.managedbrowser.CertificateTransparencyEnabled` | Fraudulent certificate detection |
| Teams | Single Account Mode | `com.microsoft.teams.account.AllowedAccounts` | Prevents personal account use on corporate devices |
| OneDrive | Block Personal OneDrive | `com.microsoft.sharepoint.blockPersonalOneDrive` | Corporate data syncing to personal Microsoft account |
| OneDrive | Tenant Restriction | `com.microsoft.sharepoint.allowedTenantID` | Cross-tenant data access |

### Medium Security Impact (Deploy After Baseline)

| App | Setting | Key | Benefit |
|---|---|---|---|
| Outlook | Block Printing | `com.microsoft.outlook.Mail.BlockPrinting` | Prevents physical data leakage |
| Outlook | Email Sync Window | `com.microsoft.outlook.Mail.SyncWindowInDays` | Limits historical email on device |
| Outlook | Data Classification | `com.microsoft.outlook.Mail.DataClassificationEnabled` | Purview label enforcement |
| Edge | Cookie Blocking | `com.microsoft.intune.mam.managedbrowser.CookieBlockingLevel` | Tracking reduction |
| Edge | Search Engine Lock | `com.microsoft.intune.mam.managedbrowser.AllowSearchEngineCustomization` | Prevents unapproved search providers |

### Operational / UX Settings (Deploy Separately or With Baseline)

| App | Setting | Key | Benefit |
|---|---|---|---|
| Outlook | Focused Inbox | `com.microsoft.outlook.Mail.FocusedInbox` | User email management |
| Outlook | Calendar Sync | `com.microsoft.outlook.Calendar.SyncEnabled` | Scheduling functionality |
| Edge | Homepage URL | `com.microsoft.intune.mam.managedbrowser.homepage` | Directs users to intranet |
| Edge | Managed Bookmarks | `com.microsoft.intune.mam.managedbrowser.ManagedBookmarks` | Helpdesk/HR portal access |
| Edge | Default Search Engine | `com.microsoft.intune.mam.managedbrowser.SearchEngineDefault` | Consistent search experience |
| Teams | Domain Pre-fill | `domain_name` | Faster sign-in |
| Company Portal | Branding | Tenant Administration > Customization | Professional appearance |

---

## 12. Deployment Notes

### 12.1 Configuration Designer vs. Manual Key Entry

Intune's App Configuration Policy creation offers two methods:

| Method | When to Use |
|---|---|
| **Configuration Designer** | When the key appears in the built-in UI; easier to validate; fewer errors |
| **Enter JSON data / Manual key entry** | When the key is not in the designer; required for custom or advanced keys |

For most settings in this guide, use **Configuration Designer** if the key is available in the UI. Fall back to manual entry for keys not listed in the designer.

### 12.2 Key Casing

All configuration keys are **case-sensitive**. A miscased key is silently ignored — the app will not apply the setting and no error will appear in the Intune console.

Example:
- `com.microsoft.outlook.Mail.FocusedInbox` — correct
- `com.microsoft.outlook.mail.focusedinbox` — incorrect (will be ignored)

### 12.3 Policy Check-in Timing

| Policy Type | Paired With APP | Check-in Frequency |
|---|---|---|
| Managed Devices | N/A | On app launch; on MDM policy sync |
| Managed Apps + APP | Yes | Every 30 minutes while app is active |
| Managed Apps (standalone) | No | Every 720 minutes (12 hours) |

> For Managed Apps policies, pairing with an App Protection Policy ensures settings are refreshed every 30 minutes. Deploy Managed Apps ACP alongside an APP policy for faster enforcement.

### 12.4 Validation Checklist

After deploying App Configuration Policies, validate the following:

- [ ] Open Outlook on an enrolled device — confirm account is pre-populated or signed in automatically
- [ ] Attempt to attach a file from personal iCloud Drive in Outlook — confirm it is blocked (corporate devices)
- [ ] Send a test email to an external address — confirm the external recipient warning appears
- [ ] Open Edge — confirm homepage is set to the corporate intranet URL
- [ ] Open Edge > Settings — confirm InPrivate is not available (corporate devices)
- [ ] Attempt to open a bookmark — confirm managed bookmarks appear in the Managed folder
- [ ] Open Teams — confirm notification preferences are configured as expected
- [ ] Open OneDrive — confirm personal OneDrive tab is not accessible (corporate devices)
- [ ] In Intune: Apps > App Configuration Policies > [Policy] > Device Install Status — confirm `Succeeded` status for target devices

### 12.5 Troubleshooting

| Symptom | Likely Cause | Resolution |
|---|---|---|
| Setting not applying on enrolled device | Policy type is Managed Apps instead of Managed Devices | Recreate as Managed Devices type |
| Setting not applying on unenrolled device | Policy type is Managed Devices | Recreate as Managed Apps type |
| Key not recognized (app ignores it) | Incorrect casing or key name | Verify exact key from Microsoft documentation |
| IntuneMAMUPN not working | App version too old | Update app to latest version from App Store |
| Policy shows "Not applicable" for a device | App not assigned to the device/user in Intune | Assign the app and the ACP to the same group |
| Configuration not refreshing | Managed Apps policy without paired APP | Pair with an App Protection Policy for 30-min check-in |

---

## 13. References

### Microsoft Learn – App Configuration Policies
- [App Configuration Policies Overview – Microsoft Intune](https://learn.microsoft.com/en-us/intune/intune-service/apps/app-configuration-policies-overview)
- [Add App Configuration Policies for Managed iOS/iPadOS Devices](https://learn.microsoft.com/en-us/intune/intune-service/apps/app-configuration-policies-use-ios)
- [Add App Configuration Policies for Managed Apps Without Device Enrollment](https://learn.microsoft.com/en-us/intune/intune-service/apps/app-configuration-policies-managed-app)

### Microsoft Learn – Per-App Configuration
- [Manage Outlook for iOS and Android With Intune](https://learn.microsoft.com/en-us/intune/intune-service/apps/app-configuration-policies-outlook)
- [Deploying Outlook for iOS Configuration Settings in Exchange Online](https://learn.microsoft.com/en-us/exchange/clients-and-mobile-in-exchange-online/outlook-for-ios-and-android/outlook-for-ios-and-android-configuration-with-microsoft-intune)
- [Manage Microsoft Edge on iOS and Android With Intune](https://learn.microsoft.com/en-us/intune/intune-service/apps/manage-microsoft-edge)
- [Manage Microsoft Teams for iOS and Android With Intune](https://learn.microsoft.com/en-us/intune/intune-service/apps/manage-microsoft-teams)
- [Manage Microsoft 365 Apps on iOS With Intune](https://learn.microsoft.com/en-us/intune/intune-service/apps/manage-microsoft-office)
- [Deploy OneDrive Apps Using Intune](https://learn.microsoft.com/en-us/sharepoint/deploy-intune)
- [Configure Microsoft Defender for Endpoint on iOS Features](https://learn.microsoft.com/en-us/defender-endpoint/ios-configure-features)
- [Set Up Enrollment Notifications in Intune](https://learn.microsoft.com/en-us/mem/intune/enrollment/enrollment-notifications)

---

*This document is part of the UniFy Endpoint – Microsoft Intune iOS/iPadOS Baseline series. Document maintained by Yoennis Olmo - Sr. Modern Work Consultant.*
