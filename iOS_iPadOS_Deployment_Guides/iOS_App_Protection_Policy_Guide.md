# iOS/iPadOS App Protection Policy (MAM) Guide – Microsoft Intune

| | |
|---|---|
| **Document Type** | Work Instruction – Configuration Reference |
| **Author** | Sr. Modern Work Consultant |
| **Last Updated** | May 2026 |
| **Version** | 1.0 |
| **Applies To** | iOS/iPadOS 16.0 and later, Microsoft Intune, App Protection Policies |
| **Microsoft Reference** | Microsoft App Protection Data Protection Framework – Levels 1, 2, 3 |

---

## Table of Contents

1. [Overview & Scope](#1-overview--scope)
2. [Security Levels Explained](#2-security-levels-explained)
3. [Prerequisites](#3-prerequisites)
4. [Policy Architecture & Naming Convention](#4-policy-architecture--naming-convention)
5. [App Targeting – Which Apps to Include](#5-app-targeting--which-apps-to-include)
6. [Data Protection Settings](#6-data-protection-settings)
7. [Access Requirements](#7-access-requirements)
8. [Conditional Launch](#8-conditional-launch)
9. [MAM with Enrollment (MAM+MDM) vs MAM Without Enrollment (MAM-WE)](#9-mam-with-enrollment-mammdm-vs-mam-without-enrollment-mam-we)
10. [Per-App Recommendations](#10-per-app-recommendations)
11. [Phased Rollout](#11-phased-rollout)
12. [References](#12-references)
13. [Version History](#13-version-history)

---

## 1. Overview & Scope

App Protection Policies (APP), also called Mobile Application Management (MAM), protect corporate data in managed apps **regardless of whether the device is enrolled in Intune**. APP is the primary data protection mechanism for BYOD scenarios and a critical complement to device configuration policies on enrolled devices.

APP applies to apps that include the **Intune App SDK** — all Microsoft 365 apps (Outlook, Teams, Edge, OneDrive, Word, Excel, PowerPoint, and others) — and controls how corporate data moves within and between those apps.

**This guide covers:**

- Microsoft's three-level (L1 / L2 / L3) App Protection data protection framework for iOS
- Data Protection, Access Requirements, and Conditional Launch settings for each level
- MAM with MDM enrollment vs. MAM without enrollment (BYOD)
- Per-app recommendations for Microsoft 365 apps
- Policy naming, targeting, and phased rollout guidance

**This guide does not cover:**

- App Configuration Policies — see [iOS_App_Configuration_Policy_Guide.md](iOS_App_Configuration_Policy_Guide.md)
- Device Configuration / Settings Catalog policies — see [iOS_CIS_Recommended_Settings_Guide.md](iOS_CIS_Recommended_Settings_Guide.md)
- Third-party app MAM SDK integration

**Relationship to device configuration:**

| Device State | APP Role |
|---|---|
| Enrolled (supervised or BYOD Device Enrollment) | Works alongside device configuration policies — app-level data protection on top of device-level restrictions |
| Unenrolled BYOD (User Enrollment or MAM-WE) | APP is the **primary** protection mechanism — device policies either do not apply or do not exist |

> **Key principle:** Apply **less restrictive** APP to MDM-enrolled devices and **more restrictive** APP to unenrolled personal devices.

---

## 2. Security Levels Explained

Microsoft's App Protection Data Protection Framework defines three security levels for iOS app protection. These align broadly with CIS L1/L2 and Microsoft's device security Level 1/2/3 framework.

| Level | Name | Target Audience | CIS Alignment |
|---|---|---|---|
| **Level 1** | Enterprise Basic Data Protection | All users; minimum standard for any device accessing corporate data | CIS L1 equivalent |
| **Level 2** | Enterprise Enhanced Data Protection | Most organizations; recommended standard for the majority of mobile users | CIS L2 equivalent |
| **Level 3** | Enterprise High Data Protection | Users handling sensitive or regulated data; executives; high-risk targets | CIS L2+ / MS Level 3 |

> **Recommendation:** Deploy **Level 2** as the default APP for all users. Apply or deploy **Level 3** to users in any role with access to sensitive or regulated data.

---

## 3. Prerequisites

| Requirement | Notes |
|---|---|
| Microsoft Intune license (Plan 1 or higher) | Required for all APP management |
| Microsoft Entra ID P1 (for Conditional Access with APP) | Required to enforce "Require App Protection Policy" in CA |
| Microsoft 365 Apps deployed to devices | Target apps must include the Intune App SDK (all Microsoft 365 apps qualify) |
| Users licensed for Microsoft 365 | Apps must be activated with a work account |
| Intune App Protection Policy created and assigned | See note below for assignment targeting |
| Conditional Access policy configured (recommended) | To enforce "Require Approved Client App or APP" for BYOD |

> **Important:** App Protection Policies are assigned to **users or user groups**, not devices. A user's APP policy follows them across all devices — enrolled or not — as long as they sign in with their work account.

---

## 4. Policy Architecture & Naming Convention

Create **separate APP policies per security level and per device state** (enrolled vs. unenrolled). This allows different protection levels without over-restricting enrolled corporate devices.

| Suggested Policy Name | Target | Security Level | Device State |
|---|---|---|---|
| `iOS_iPadOS - APP - USR - App Protection - MAM - L1 - Corporate Devices` | All users with enrolled corporate devices | Level 1 – Basic | MDM-enrolled (any enrollment type) |
| `iOS_iPadOS - APP - USR - App Protection - MAM - L1 - BYOD Devices` | All users with enrolled BYOD devices | Level 2 – Enhanced | Unenrolled (MAM-WE) |
| `iOS_iPadOS - APP - USR - App Protection - MAM - L2 - Corporate Devices` | All users with unenrolled BYOD devices | Level 2 – Enhanced | MDM-enrolled (any enrollment type) |
| `iOS_iPadOS - APP - USR - App Protection - MAM - L2 - BYOD Devices` | All users with unenrolled BYOD devices | Level 2 – Enhanced | Unenrolled (MAM-WE) |
| `iOS_iPadOS - APP - USR - App Protection - MAM - L3 - Corporate Devices` | Sensitive data users | Level 3 – High | MDM-enrolled (any enrollment type) |
| `iOS_iPadOS - APP - USR - App Protection - MAM - L3 - BYOD Devices` | Sensitive data users (BYOD) | Level 3 – High | Unenrolled (MAM-WE) |

> **Layering note:** When a user has multiple APP policies assigned (e.g., via group membership), Intune enforces the **most restrictive** setting across all applicable policies.

> **Configuration path:** Intune > Apps > App Protection Policies > Create Policy > iOS/iPadOS

---

## 5. App Targeting – Which Apps to Include

### 5.1 Target All Microsoft Apps (Recommended)

When creating an APP policy, under **Apps > Target Policy to**, select **All Microsoft Apps**. This automatically includes all current and future Microsoft apps that support the Intune App SDK, without needing to maintain a manual list.

### 5.2 Core Microsoft 365 App List (If Targeting Specific Apps)

| App | App Store Bundle ID | Notes |
|---|---|---|
| Microsoft Outlook | `com.microsoft.Office.Outlook` | Primary email/calendar; highest data risk |
| Microsoft Teams | `com.microsoft.skype.teams` | Collaboration; notifications critical |
| Microsoft Edge | `com.microsoft.msedge` | Managed browser; required for web link redirect |
| Microsoft OneDrive | `com.microsoft.skydrive` | File storage |
| Microsoft Word | `com.microsoft.Office.Word` | Documents |
| Microsoft Excel | `com.microsoft.Office.Excel` | Spreadsheets |
| Microsoft PowerPoint | `com.microsoft.Office.PowerPoint` | Presentations |
| Microsoft SharePoint | `com.microsoft.sharepoint` | Intranet/document libraries |
| Microsoft OneNote | `com.microsoft.onenote` | Notes |
| Microsoft To Do | `com.microsoft.to-do-planner` | Task management |
| Microsoft Office (Mobile) | `com.microsoft.Office` | Unified Office app |

> **Note:** New Microsoft apps that include the Intune App SDK are automatically protected when using **All Microsoft Apps** targeting. This is the recommended approach to avoid coverage gaps.

---

## 6. Data Protection Settings

These settings control how corporate data moves into, out of, and between managed apps.

### 6.1 Data Transfer

| Setting | Level 1 | Level 2 | Level 3 | User Impact | Security Benefit |
|---|---|---|---|---|---|
| Backup org data to iTunes and iCloud backups | `Allow` | `Block` | `Block` | Medium | Prevents corporate data exposure in cloud backups |
| Send org data to other apps | `All apps` | `Policy managed appsPolicy managed apps with Open-In/Share filtering` | `Policy managed apps` | High | Restricts data sharing to the managed app ecosystem |
| Receive data from other apps | `All apps` | `All apps` | `Policy managed apps` | High | L3 blocks data entering from unmanaged sources |
| Save copies of org data | `Allow` | `Block` | `Block` | High | Prevents unauthorized file export |
| Allow user to save copies to selected services | `All` | `OneDrive for Business, SharePoint, Local Storage` | `OneDrive for Business, SharePoint` | Medium | Limits save destinations to corporate cloud only |
| Transfer telecommunication data to | `Any dialer app` | `Any dialer app` | `A specific dialer app` | Low | L3 restricts phone call handling |
| Restrict cut, copy, and paste between apps | `Any app` | `Policy managed apps with paste in` | `Policy managed apps with paste in` | High | Prevents clipboard data leakage to personal apps |
| Screen capture | `Allow` | `Block` | `Block` | Medium | Prevents screenshots, screen recording, AirPlay mirroring, and QuickTime capture of corporate content |
| Encrypt org data | `Require` | `Require` | `Require` | Low | Data encrypted at rest; automatically applied |
| Sync policy managed app data with native apps or add-ins | `Allow` | `Allow` | `Block` | Medium | L3 prevents contact/calendar sync to native iOS apps |
| Printing org data | `Allow` | `Block` | `Block` | Medium | Prevents physical data leakage via printing |
| Restrict web content transfer with other apps | `Not configured` | `Microsoft Edge` | `Microsoft Edge` | Medium | All web links from managed apps open in Edge only |
| Org data notifications | `Allow` | `Block org data` | `Block org data` | Low | Hides corporate data in lock screen notifications |
| Genmoji | `Allow` | `Block` | `Block` | Low | Blocks use of Apple's AI Genmoji generator with org data; requires Intune App SDK v19.7.12+ (iOS 18+) |
| Writing Tools | `Allow` | `Block` | `Block` | Low | Blocks Apple's AI Writing Tools from processing org data; requires Intune App SDK v19.7.12+ (iOS 18+) |

> **Note on "Block org data" notifications:** At Level 2+, lock screen notifications from managed apps show a generic message ("You have a new message") instead of the actual content. This prevents sensitive data from being visible on a locked screen.

> **Note on Screen Capture (Level 2+):** Blocking screen capture on iOS affects native screenshots, on-device screen recording, screen sharing via apps like Teams and Zoom mobile, AirPlay mirroring, and QuickTime recording from a connected Mac. Some accessibility tools may be affected. Evaluate before applying to all users. This setting requires Intune App SDK v19.7.12 or later.

### 6.2 Exempted Apps (Open-In Exemptions)

By default, the following Apple system URLs and schemes are exempted from data transfer restrictions and cannot be fully blocked:

| Scheme | Function |
|---|---|
| `skype://` | Initiates Skype calls |
| `app-settings://` | Opens device Settings |
| `itms://`, `itmss://`, `itms-apps://` | App Store links |
| `calshow://` | Native Calendar |
| Maps and FaceTime Universal Links | Apple system apps |

> These exemptions are built into iOS and cannot be modified through Intune. If strict data transfer control is required for these, enforce via supervised device restrictions (see *iOS_CIS_Recommended_Settings_Guide.md*, Section 7).

---

## 7. Access Requirements

These settings define how users must authenticate before accessing corporate data within managed apps.

| Setting | Level 1 | Level 2 | Level 3 | User Impact | Security Benefit |
|---|---|---|---|---|---|
| PIN for access | `Require` | `Require` | `Require` | Medium | Controls access to corporate app data |
| PIN type | `Numeric` | `Numeric` | `Passcode` | Medium | L3 requires alphanumeric PIN (letters + numbers) |
| Simple PIN | `Allow` | `Allow` | `Block` | Medium | L3 prevents sequential/repeating digits |
| Minimum PIN length | `4` | `6` | `8` | Medium | Longer PIN = stronger brute-force resistance |
| Touch ID / Face ID instead of PIN | `Allow` | `Allow` | `Allow` | Low | Biometric convenience with PIN fallback |
| Override biometrics with PIN after (minutes) | `1440` | `1440` | `1440` | Low | Requires PIN re-entry after 24-hour biometric timeout |
| PIN reset after days | `No` | `No` | `365 days` | Low | L3 forces annual APP PIN rotation |
| Work or school account credentials for access | `Not required` | `Not required` | `Not required` | Medium | Alternative to PIN; sign-in with Entra credentials |
| Recheck access requirements after (minutes of inactivity) | `30` | `30` | `30` | Low | Re-prompts for authentication after 30 minutes idle |

> **PIN vs Device Passcode:** When a device is MDM-enrolled and has a compliant device passcode, the APP PIN requirement can be optionally relaxed (the device passcode counts as the authentication factor). This is configured per-policy under "PIN for access" > "Disable app PIN when device PIN is managed." Recommended only for enrolled devices with strong device passcode enforcement (Level 1 enrolled policies only).

---

## 8. Conditional Launch

Conditional Launch settings define actions Intune takes when a device or app condition is outside the accepted security posture. Actions available are: **Warn**, **Block access**, or **Wipe data**.

### 8.1 App Conditions

| Condition | Value | L1 Action | L2 Action | L3 Action | User Impact | Security Benefit |
|---|---|---|---|---|---|---|
| Max PIN attempts | `5 attempts` | Reset PIN | Reset PIN | Reset PIN | Low | Prevents brute-force PIN attacks |
| Offline grace period (block) | `10080 minutes` (7 days) | Block access | Block access | Block access | High | Blocks access after 7 days offline |
| Offline grace period (wipe) | `90 days` | Wipe data | `30 days` – Wipe data | `90 days` – Wipe data | High | Removes corporate data after extended offline period |
| Disabled account | — | — | Block access | Block access | High | Blocks access when user's Entra account is disabled |
| Min app version | — | — | Warn | Block access | Low | Ensures patched app version is in use |

> **Wipe grace period guidance:** The offline wipe timer should be set carefully. 90 days is appropriate for L1/L3; 30 days for L2 creates a tighter window for devices that go missing without being reported. Consider your organization's device loss reporting SLA when setting this value.

### 8.2 Device Conditions

| Condition | Level 1 | Level 2 | Level 3 | User Impact | Security Benefit |
|---|---|---|---|---|---|
| Jailbroken/rooted devices | Block access | Block access | Wipe data | High | L3 wipes corporate data; L1/L2 blocks access |
| Min OS version | Not configured | iOS 16.0 – Block | iOS 16.0 – Block | Medium | Ensures minimum security patch level |
| Max OS version | Not configured | Not configured | Not configured | Low | Avoids blocking users on latest releases |
| Min SDK version | Not configured | Not configured | Not configured | None | For internal app development validation |
| Device threat level (unenrolled only) | Not configured | Not configured | `Secured` – Block access | High | Requires MDE threat signal for unenrolled BYOD |
| Primary MTD service | — | — | Microsoft Defender for Endpoint | High | MDE provides threat signal for unenrolled devices |

> **Device Threat Level for unenrolled BYOD (L3):** This requires Microsoft Defender for Endpoint to be deployed as an app to unenrolled personal devices. The threat signal flows through MAM, not device compliance. This is the L3 equivalent of the Defender integration on enrolled devices.

> **Jailbreak detection:** Intune performs jailbreak checks at app launch and periodically during app use. On jailbroken devices, the check succeeds and triggers the configured action (block or wipe). Note that sophisticated jailbreaks can sometimes evade detection — this is why device-level jailbreak detection in compliance policy is also recommended for enrolled devices.

---

## 9. MAM with Enrollment (MAM+MDM) vs MAM Without Enrollment (MAM-WE)

### 9.1 Comparison

| Capability | MAM+MDM (Enrolled) | MAM-WE (Unenrolled) |
|---|---|---|
| Device must be Intune-enrolled | Yes | No |
| APP policy applies | Yes | Yes |
| Device compliance enforced | Yes (via Compliance Policy) | No |
| Device-level restrictions apply | Yes (Settings Catalog) | No |
| App PIN can leverage device PIN | Yes (optional) | No — app PIN always required |
| Selective wipe (corporate data only) | Yes | Yes |
| Full device wipe available | Yes | No |
| Works on BYOD personal devices | Yes (with enrollment) | Yes (no enrollment needed) |
| Entra Conditional Access enforcement | Full (device + app) | App-level only |
| MDE threat signal source | Device Compliance | MAM Threat Level (separate) |

### 9.2 Recommended Configuration Per Device State

| Device State | Enrollment Type | Recommended APP Level | Key Difference |
|---|---|---|---|
| Corporate Supervised (ADE) | MDM – Supervised | Level 1 or 2 | Device controls cover most restrictions; APP adds app-layer DLP |
| Corporate BYOD (Device Enrollment) | MDM – Device Enrollment | Level 2 | APP reinforces device policies; app PIN can use device passcode |
| BYOD (User Enrollment) | MDM – User Enrollment | Level 2 | Device controls limited; APP is primary protection layer |
| BYOD (No Enrollment / MAM-WE) | None | Level 2 or 3 | APP is the only protection layer; use stricter settings |

### 9.3 IntuneMAMUPN – Identifying the Enrolled User

When deploying APP to MDM-enrolled devices, configure the **IntuneMAMUPN** key via App Configuration Policy (see *iOS_App_Configuration_Policy_Guide.md*) to link the device's enrolled identity to the APP policy. This ensures the correct policy is applied to the right work account when multiple accounts are signed into an app.

> **Note:** As of September 2024, `IntuneMAMUPN`, `IntuneMAMOID`, and `IntuneMAMDeviceID` are **automatically populated** for Outlook, Teams, Word, Excel, and PowerPoint on MDM-enrolled devices. Manual configuration is no longer required for these apps, but remains necessary for other apps or older app versions.

---

## 10. Per-App Recommendations

The following guidance supplements the framework settings above with app-specific considerations. These are configured within the same APP policy — no separate policy per app is required.

### 10.1 Microsoft Outlook

Outlook holds the highest data risk profile of any Microsoft 365 app — it is the primary entry and exit point for corporate data on mobile devices.

| Setting | Enrolled Devices | Unenrolled BYOD | Notes |
|---|---|---|---|
| Send org data to other apps | Policy managed apps | Policy managed apps | Prevents forwarding to personal mail apps |
| Receive data from other apps | All apps (L2) / Policy managed apps (L3) | Policy managed apps | BYOD stricter to prevent personal-to-work data flow |
| Sync policy managed app data with native apps | Block (L2+) | Block | Prevents Outlook contacts from syncing to native iOS Contacts |
| Org data notifications | Block org data (L2+) | Block org data | Generic lock screen notification; no content exposed |
| Printing org data | Block (L2+) | Block | Prevents printing of emails and attachments |
| Restrict web content transfer | Microsoft Edge | Microsoft Edge | All email links open in Edge, not Safari |
| Screen capture | Block (L2+) | Block | Prevents screenshots of email content |

### 10.2 Microsoft Teams

Teams primarily exposes risk through notification content and screen sharing.

| Setting | Enrolled Devices | Unenrolled BYOD | Notes |
|---|---|---|---|
| Org data notifications | Block org data (L2+) | Block org data | Teams messages appear as "New message" on lock screen; no content exposed |
| Send org data to other apps | Policy managed apps | Policy managed apps | Chat links and files stay within managed apps |
| Screen capture | Block (L2+) | Block | Prevents screenshots of meetings and chats |
| Sync with native apps | Block (L2+) | Block | Prevents meeting contacts syncing to native calendar |

### 10.3 Microsoft Edge

Edge is the required managed browser for web link redirects from other managed apps.

| Setting | Enrolled Devices | Unenrolled BYOD | Notes |
|---|---|---|---|
| Receive data from other apps | All apps | All apps | Must receive links from Outlook, Teams, etc. |
| Send org data to other apps | Policy managed apps | Policy managed apps | Links from Edge stay in managed ecosystem |
| Screen capture | Allow (L2) / Block (L3) | Block (L2+) | Edge may need to allow screen capture for usability (web content screenshots); evaluate L3 carefully before blocking |

> **Note:** If Edge is blocked from receiving data from other apps, the managed browser redirect from Outlook and Teams will fail. Always verify this setting allows "All apps" or at minimum "Policy managed apps with paste in" for Edge's receive setting.

### 10.4 Microsoft OneDrive

OneDrive's primary risk is corporate files syncing to or from personal OneDrive accounts.

| Setting | Enrolled Devices | Unenrolled BYOD | Notes |
|---|---|---|---|
| Save copies of org data | Block (L2+) | Block | Only OneDrive for Business and SharePoint allowed as save destinations |
| Allow user to save copies to | OneDrive for Business, SharePoint | OneDrive for Business, SharePoint | Explicitly allowlist corporate cloud storage only |
| Receive data from other apps | Policy managed apps | Policy managed apps | Prevents personal files from entering corporate OneDrive |
| Backup to iCloud | Block (L2+) | Block | Prevents OneDrive content from appearing in iCloud Backup |

### 10.5 Microsoft Word, Excel, PowerPoint

Office productivity apps follow the same framework settings. No app-specific deviations are required beyond the base policy.

**Key considerations:**
- Ensure "Save copies of org data" is `Block` at L2+ to prevent files from being saved to personal storage (personal iCloud, personal Dropbox, etc.)
- "Allow user to save copies to" should list `OneDrive for Business` and `SharePoint` as the only permitted destinations
- "Send org data to other apps" = `Policy managed apps` prevents documents from being shared to unmanaged apps

---

## 11. Phased Rollout

### 11.1 Rollout Sequence

| Phase | Duration | Target | Action |
|---|---|---|---|
| **Phase 1: Pilot** | 2 weeks | IT team + 10–20 volunteers | Deploy L1 enrolled + L2 unenrolled APP to pilot group; monitor access issues |
| **Phase 2: Broad Level 2** | 2 weeks | All users | Roll L2 (enrolled and unenrolled) to all users; send user communication |
| **Phase 3: Level 3 Sensitive** | 1 week | Finance, Legal, HR, Exec groups | Add L3 policy assignment to high-sensitivity user groups |

### 11.2 User Communication (Before Phase 2)

Notify users 5 business days before broad deployment explaining:
- Corporate email (Outlook), Teams, and OneDrive will require a PIN or Face ID/Touch ID to open
- Corporate files can no longer be opened in personal apps or saved to personal cloud storage (iCloud Drive, personal Dropbox, etc.)
- Web links from Outlook and Teams will open in Microsoft Edge instead of Safari
- Lock screen notifications from Outlook and Teams will no longer show message content

### 11.3 Validation Checklist (After Each Phase)

- [ ] Open Outlook on an enrolled device — confirm PIN or biometric is required
- [ ] Attempt to forward an email to a personal Gmail address via personal Mail app — confirm it is blocked
- [ ] Attempt to copy text from Outlook and paste into Notes — confirm it is blocked (L2+)
- [ ] Attempt to save an attachment to personal Files/iCloud — confirm it is blocked (L2+)
- [ ] Lock the device and check the lock screen — confirm Outlook notifications show generic text (L2+)
- [ ] Open a web link from Outlook — confirm it opens in Microsoft Edge, not Safari
- [ ] Test on a BYOD unenrolled device — confirm MAM-WE policy applies after sign-in with work account

### 11.4 Monitoring

After deployment, review the following in Intune:
- **Apps > App Protection Policies > [Policy] > Reporting > User status** — confirms how many users have checked in with each policy
- **Apps > Monitor > App Protection Status** — shows per-user compliance with each policy
- **Entra Sign-in Logs** — review for CA policy failures related to "Require App Protection Policy"

---

## 12. References

### Microsoft Learn – App Protection Policies
- [Data Protection Framework Using App Protection Policies](https://learn.microsoft.com/en-us/intune/intune-service/apps/app-protection-policies)
- [iOS/iPadOS App Protection Policy Settings Reference](https://learn.microsoft.com/en-us/intune/intune-service/apps/app-protection-policy-settings-ios)
- [Create and Assign App Protection Policies](https://learn.microsoft.com/en-us/intune/intune-service/apps/app-protection-policies-create)
- [App Protection Policy Overview](https://learn.microsoft.com/en-us/intune/intune-service/apps/app-protection-policy)
- [Mobile Threat Defense for App Protection Policy (Unenrolled Devices)](https://learn.microsoft.com/en-us/intune/intune-service/protect/mtd-app-protection-policy)
- [Manage Outlook for iOS and Android With Intune](https://learn.microsoft.com/en-us/intune/intune-service/apps/app-configuration-policies-outlook)
- [Manage Microsoft Edge on iOS and Android With Intune](https://learn.microsoft.com/en-us/intune/intune-service/apps/manage-microsoft-edge)
- [Manage Microsoft Teams for iOS and Android With Intune](https://learn.microsoft.com/en-us/intune/intune-service/apps/manage-microsoft-teams)
- [Manage Microsoft 365 Apps on iOS With Intune](https://learn.microsoft.com/en-us/intune/intune-service/apps/manage-microsoft-office)
- [App-Based Conditional Access With Intune](https://learn.microsoft.com/en-us/intune/intune-service/protect/app-based-conditional-access-intune)

---

*This document is part of the UniFy Endpoint – Microsoft Intune iOS/iPadOS Baseline series. Document maintained by Yoennis Olmo - Sr. Modern Work Consultant.*
