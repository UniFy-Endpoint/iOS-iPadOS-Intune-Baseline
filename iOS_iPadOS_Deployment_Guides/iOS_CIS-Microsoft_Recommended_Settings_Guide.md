# iOS/iPadOS CIS Recommended Settings Guide – Microsoft Intune

| | |
|---|---|
| **Document Type** | Work Instruction – Configuration Reference |
| **Author** | Sr. Modern Work Consultant |
| **Last Updated** | May 2026 |
| **Version** | 1.0 |
| **Applies To** | iOS/iPadOS 26 and later, Microsoft Intune, Settings Catalog |
| **CIS Reference** | CIS Apple iOS/iPadOS 26 Benchmark v1.0.0 |
| **Microsoft Reference** | iOS/iPadOS Supervised & Personal Device Security Configurations (updated April 2026) |

---

## Table of Contents

1. [Overview & Scope](#1-overview--scope)
2. [Security Levels Explained](#2-security-levels-explained)
3. [Policy Architecture](#3-policy-architecture)
4. [Corporate Device Policies — Settings Catalog](#4-corporate-device-policies--settings-catalog)
   - 4.1 [Device Security](#41-device-security--corporate-devices)
   - 4.2 [Data Protection](#42-data-protection--corporate-devices)
   - 4.3 [iCloud & Storage](#43-icloud--storage--corporate-devices)
   - 4.4 [Device Restrictions](#44-device-restrictions--corporate-devices)
   - 4.5 [Connectivity Controls](#45-connectivity-controls--corporate-devices)
   - 4.6 [App Management](#46-app-management--corporate-devices)
   - 4.7 [Device Pairing](#47-device-pairing--corporate-devices)
   - 4.8 [Accessibility & Sharing](#48-accessibility--sharing--corporate-devices)
   - 4.9 [Apple Services](#49-apple-services--corporate-devices)
   - 4.10 [Device Privacy](#410-device-privacy--corporate-devices)
   - 4.11 [Lock Screen](#411-lock-screen--corporate-devices)
   - 4.12 [Safari Browser](#412-safari-browser--corporate-devices)
   - 4.13 [Software Update](#413-software-update--corporate-devices)
   - 4.14 [Apple Intelligence & Siri](#414-apple-intelligence--siri--corporate-devices)
   - 4.15 [Web-App-Store (EU)](#415-web-app-store-eu--corporate-devices)
   - 4.16 [Microsoft Enterprise SSO](#416-microsoft-enterprise-sso--all-devices)
5. [BYOD Policies — Settings Catalog](#5-byod-policies--settings-catalog)
   - 5.1 [Device Security](#51-device-security--byod-devices)
   - 5.2 [Data Protection](#52-data-protection--byod-devices)
   - 5.3 [iCloud & Storage](#53-icloud--storage--byod-devices)
   - 5.4 [Device Restrictions](#54-device-restrictions--byod-devices)
   - 5.5 [Device Privacy](#55-device-privacy--byod-devices)
   - 5.6 [Safari Browser](#56-safari-browser--byod-devices)
   - 5.7 [Apple Intelligence & Siri](#57-apple-intelligence--siri--byod-devices)
6. [App Protection Policy Recommendations (MAM)](#6-app-protection-policy-recommendations-mam)
   - 6.1 [Data Transfer and Protection](#61-data-transfer-and-protection)
   - 6.2 [Access Requirements](#62-access-requirements)
   - 6.3 [Conditional Launch](#63-conditional-launch)
7. [Corporate – Compliance Policy Settings](#7-corporate--compliance-policy-settings)
   - 7.1 [Device Health](#71-device-health)
   - 7.2 [Device Properties](#72-device-properties)
   - 7.3 [System Security](#73-system-security)
   - 7.4 [Actions for Noncompliance](#74-actions-for-noncompliance)
8. [BYOD – Compliance Policy Settings](#8-byod--compliance-policy-settings)
   - 8.1 [Device Health](#81-device-health)
   - 8.2 [Device Properties](#82-device-properties)
   - 8.3 [System Security](#83-system-security)
   - 8.4 [Actions for Noncompliance](#84-actions-for-noncompliance)
9. [Conditional Access Policy Recommendations](#9-conditional-access-policy-recommendations)

---

## 1. Overview & Scope

This guide provides information about the recommended **Settings Catalog**, **Software Update**, and **Compliance Policy** settings for iOS and iPadOS devices in Microsoft Intune. It serves as a baseline reference for organizations aligning with the CIS iOS Benchmark and Microsoft security framework while maintaining a practical balance between security and usability.

**This guide covers:**

- How settings are organized by functional category and security level (Section 3)
- Recommended settings for Corporate devices organized by category (Section 4)
- Recommended settings for BYOD devices organized by category (Section 5)
- App Protection Policy (MAM) recommendations from the Microsoft Security Configuration Framework (Section 6)
- Compliance policy settings for Corporate and BYOD devices (Sections 7 and 8)
- Conditional Access policy recommendations to enforce compliance (Section 9)
- CIS L1/L2 level noted per setting within each policy section

**This guide does not cover:**

- App Configuration Policies — see [iOS_App_Configuration_Policy_Guide.md](iOS_App_Configuration_Policy_Guide.md)
- Microsoft Defender for Endpoint — see [Defender_for_Endpoint_iOS_iPadOS_Devices.md](Defender_for_Endpoint_iOS_iPadOS_Devices.md)
- VPN, Wi-Fi, Email, and Certificate profile templates (configured via Intune Device Configuration Templates, not Settings Catalog)
- Phased rollout and deployment sequencing

> **Note:** Not all CIS Benchmark controls are available natively in the Settings Catalog. Settings that cannot be enforced via the Settings Catalog (VPN, Wi-Fi, email, certificates, custom MDM payloads) are configured using Intune Device Configuration profile templates and are outside the scope of this guide.

### Declarative Device Management (DDM)

For devices running iOS/iPadOS 15 and later, Intune automatically uses **Declarative Device Management (DDM)** where available, particularly for passcode and software update policies. DDM is Apple's modern, autonomous management protocol. When the same setting is delivered via both a legacy MDM profile and a DDM declaration, the **strictest value is enforced**.

> **iOS 26 critical change:** Apple deprecated legacy MDM software update commands in **iOS/iPadOS 26**. DDM is now the **only supported method** for enforcing software updates on supervised devices. Legacy update policies created under **Devices > Update policies for iOS/iPadOS** will no longer function on iOS 26+ devices. See Section 4.13 for DDM update policy configuration. DDM software update policies require **iOS/iPadOS 17.0+** and **Automated Device Enrollment (ADE)**.

---

## 2. Security Levels Explained

### CIS Benchmark Levels

| Level | Description | Target Audience |
|---|---|---|
| **L1** | Basic security controls with minimal user impact. Reduces the attack surface without significantly restricting functionality. | All devices accessing work or school data |
| **L2** | Enhanced security controls for environments handling sensitive data. Some restrictions may affect usability. | Most organizations; recommended as the standard baseline |

### Microsoft Security Framework Levels

| Level | Description |
|---|---|
| **Level 1** | Minimum recommended security for personal devices accessing work data |
| **Level 2** | Enhanced security; recommended for most mobile users and organizations |
| **Level 3** | High-security environments with sophisticated threats; strongest enforcement with minimal user flexibility |

> **Recommendation for most organizations:** Target **CIS L2 / Microsoft Level 2** as your operational baseline. Deploy L1 settings first, pilot for 1–2 weeks, then layer L2 settings after user communication.

---

## 3. Policy Architecture

Settings are organized by **functional category** — each category covers one specific area of device behavior. All settings for a category are configured together regardless of CIS tier. The CIS level of each individual setting is documented per setting throughout Sections 4 and 5. Corporate and BYOD settings are documented in separate sections because the management scope differs: corporate supervised devices support a broader set of MDM restrictions than unsupervised personal devices.

---

## 4. Corporate Device Policies — Settings Catalog

Corporate devices are enrolled via Apple Automated Device Enrollment (ADE) and are supervised, providing the broadest set of available MDM restrictions. Settings marked as supervised-only apply exclusively to this device track.

### 4.1 Device Security — Corporate Devices

Passcode enforcement, auto-lock, and authentication controls. Intune uses DDM for passcode policies on iOS/iPadOS 15+ — users receive a 60-minute grace period to set a compliant passcode after policy assignment.

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Require Passcode | Enabled | L1 | Enables hardware encryption; required for all MDM management |
| Block Simple Passcodes | Blocked | L1 | Prevents sequential and repeating patterns (e.g., 123456, 111111) |
| Minimum Passcode Length | 6 | L1 | Base brute-force resistance |
| Maximum Auto-Lock | 1 minute | L1 | Minimizes the window of unauthorized access when unattended |
| Grace Period Before Lock | Immediately | L1 | No delay before passcode required after screen lock (CIS iOS 26) |
| Maximum Failed Attempts | 5 | L1 | Device wipes after 5 failed attempts |
| Passcode Expiration | 365 days | L2 | Annual passcode rotation |
| Passcode History | 5 | L2 | Prevents reuse of the last 5 passcodes |
| Require Alphanumeric Passcode | Not Required (Numeric Allowed) | Corporate | Numeric passcode accepted on corporate devices; alphanumeric requirement removed to reduce enrollment friction |
| Block Password AutoFill | Blocked | L1 | Prevents Keychain and credential providers from auto-populating passwords |
| Block Password Proximity Requests | Blocked | L1 | Prevents iOS from suggesting nearby device passwords via AirDrop |
| Block Password Sharing | Blocked | L1 | Prevents AirDrop-style password sharing between Apple devices |
| Require Face ID / Touch ID Before AutoFill | Required | L1 | Biometric authentication required before AutoFill populates credentials or card data |

> **Note:** All passcode settings are deployed in a single flat policy. Devices assigned to this policy receive all settings, including those the CIS Benchmark documents as L1 and L2.

---

### 4.2 Data Protection — Corporate Devices

Controls data transfer between managed and unmanaged contexts: AirDrop, AirPrint, open-in restrictions, screen capture, and network relay.

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Block AirDrop | Blocked | L2 | Prevents file transfer to unknown devices via proximity AirDrop |
| Block AirPrint | Blocked | L2 | Prevents printing to unsecured printers |
| Block AirPrint Credentials in Keychain | Blocked | L1 | Printer credentials not stored on device |
| Block AirPrint iBeacon Discovery | Blocked | L1 | Prevents iBeacon-based AirPrint printer discovery |
| Block Call Recording | Blocked | Corporate | Prevents call recording on supervised devices |
| Block iCloud Private Relay | Blocked | L1 | Disables iCloud Private Relay — prevents bypassing network monitoring and content filtering |
| Block Managed Apps Writing Unmanaged Contacts | Blocked | L2 | Prevents managed apps from writing contacts to personal (unmanaged) contacts |
| Block Managed Docs in Unmanaged Apps | Blocked | L1 | Core data leakage prevention — corporate files cannot open in personal apps |
| Block Unmanaged Docs in Managed Apps | Blocked | L1 | Prevents personal files from entering managed app space |
| Block Screen Capture | Blocked | L2 | Prevents screenshots of corporate content (supervised only) |
| Block Unmanaged Apps Reading Managed Contacts | Blocked | L2 | Prevents personal apps from reading work contacts |
| Require AirPlay Pairing Password | Required | L2 | Password required before AirPlay connections are established |

> **Managed Pasteboard:** `requireManagedPasteboard` is present in this policy but set to `false`. Copy/paste between managed and unmanaged apps is not blocked at the device level — data containment relies on App Protection Policies (MAM) instead.

---

### 4.3 iCloud & Storage — Corporate Devices

iCloud synchronization controls and file storage access restrictions for supervised corporate devices.

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Block iCloud Backup | Blocked | L1 | Prevents corporate data from being backed up to personal iCloud |
| Block iCloud Drive | Blocked | L1 | Prevents corporate documents from syncing to personal iCloud Drive |
| Block iCloud Keychain Sync | Blocked | L1 | Prevents corporate credentials syncing to personal Apple ID |
| Block iCloud Photo Library | Blocked | L2 | Prevents sensitive photos from syncing to personal iCloud |
| Block iCloud Shared Photo Stream | Blocked | L2 | Prevents photos from being shared via iCloud Photo Stream |
| Block Enterprise Book Backup | Blocked | L2 | Prevents Apple Books enterprise book backups to iCloud |
| Block Enterprise Book Metadata Sync | Blocked | L2 | Prevents notes and highlights from enterprise books syncing to iCloud |
| Block Network Drives in Files | Blocked | L2 | Prevents access to SMB/cloud network drives in the Files app |
| Block USB Drives in Files | Blocked | L2 | Prevents access to USB-connected storage in the Files app |
| Block Managed Apps Cloud Sync | Blocked | L1 | Prevents managed apps from syncing documents to iCloud |
| Force Encrypted Backup | Enabled | L1 | All backups are encrypted with the device passcode |

> **CIS iOS 26:** "Block iCloud Drive" promoted from L2 to L1 for supervised corporate devices.

---

### 4.4 Device Restrictions — Corporate Devices

Supervised administrative controls: account settings, device identity, factory reset, configuration profile installation, and MDM options.

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Activation Lock Allowed While Supervised | Disabled | Corporate | Activation Lock disabled on supervised devices; IT can reset/reassign without Apple ID credentials |
| Idle Reboot Allowed | Enabled | Corporate | Allows MDM-triggered reboot on idle supervised devices |
| Block Account Settings Modification | Blocked | L2 | Prevents adding or changing accounts (email, Apple ID) outside IT control |
| Block Default Browser Modification | Blocked | Corporate | Prevents users from changing the default browser |
| Block Device Name Modification | Blocked | L2 | Maintains asset naming consistency |
| Block Enabling Screen Time Restrictions | Blocked | L2 | Prevents users from enabling Screen Time/parental controls |
| Block Erase All Content and Settings | Blocked | L1 | Prevents unauthorized factory reset of corporate device |
| Block Notification Settings Modification | Blocked | L2 | Users cannot change per-app notification behavior |
| Block Configuration Profile Installation | Blocked | L1 | Prevents users from installing unauthorized MDM or config profiles (CIS iOS 26) |
| Force Automatic Date and Time | Not Enforced | Corporate | Automatic date/time enforcement removed; users may set manually |

> **MDM Options:** The Activation Lock and Idle Reboot settings (`settings_item_mdmoptions`) are in this policy only. Do not add a second `settings_item_mdmoptions` group wrapper to any other policy assigned to the same devices — Intune treats the group container as the conflict unit and reports a conflict.

---

### 4.5 Connectivity Controls — Corporate Devices

Controls user ability to modify connectivity settings. All settings in this policy require device supervision.

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Block Personal Hotspot | Blocked | L2 | Prevents sharing cellular connection with other devices |
| Block App Cellular Data Modification | Blocked | L2 | Prevents users from changing per-app cellular data settings |
| Block Bluetooth Settings Modification | Blocked | L2 | Prevents pairing with unauthorized Bluetooth devices |
| Block Cellular Plan Modification | Blocked | L2 | Prevents modification of the SIM/eSIM cellular plan |
| Block eSIM Modification | Blocked | L2 | Prevents eSIM configuration changes; protects against SIM swap |
| Block eSIM Outgoing Transfers | Blocked | L2 | Prevents eSIM from being transferred off the corporate device |
| Block NFC | Blocked | L2 | Disables NFC; Apple Pay is unaffected |
| Block Personal Hotspot Modification | Blocked | L2 | Prevents changing Personal Hotspot settings |
| Block Untrusted TLS Certificate Prompts | Blocked | L1 | Prevents users from accepting invalid or self-signed certificates |
| Block VPN Creation | Blocked | L1 | Prevents custom VPN tunnels that could bypass security controls |
| Force Wi-Fi Always On | Enabled | L3 | Wi-Fi radio cannot be turned off; ensures connectivity for MDM check-in |

> **Bluetooth impact:** "Block Bluetooth Settings Modification" prevents pairing new Bluetooth accessories. Already-paired headphones and keyboards continue to work. New Bluetooth peripherals require IT assistance to pair.

---

### 4.6 App Management — Corporate Devices

Supervised app lifecycle controls. All settings require device supervision. Appropriate for fully managed corporate devices where all apps are IT-provisioned via VPP.

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Block App Clips | Blocked | L2 | Prevents App Clip experiences triggered by QR codes or NFC tags |
| Block App Installation | Blocked | L2 | All app installation via MDM/VPP only |
| Block App Removal | Blocked | L2 | Required managed apps cannot be uninstalled |
| Block Automatic App Downloads | Blocked | L2 | IT controls when apps are installed or updated |
| Block Enterprise App Trust | Blocked | L1 | Prevents installation of unauthorized enterprise-signed apps |
| Block In-App Purchases | Blocked | L1 | Prevents unauthorized charges through managed apps |
| Block System App Removal | Blocked | L1 | Prevents removal of pre-installed Apple system apps |
| Block App Store (UI) | Blocked | L2 | Prevents browsing and installing from the App Store UI |
| Block Specific Apps (Bundle IDs) | Blocked | Corporate | Blocks com.apple.games, com.apple.tips, com.apple.Home, com.apple.freeform, com.apple.journal, com.apple.mobilemail, com.apple.tv, com.apple.iBooks, com.apple.Bridge, com.apple.Fitness, com.apple.Music, com.apple.stocks, com.apple.Health |

> **Before deploying:** Ensure a complete VPP app catalog is ready before enforcing App Installation and App Store blocks. Users cannot install anything — including Line of Business apps — unless the app is pushed via Intune.

---

### 4.7 Device Pairing — Corporate Devices

Controls USB and wireless pairing with other devices and accessories.

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Disable Temporary Audio Accessory Pairing | Enabled | Corporate | Audio accessories require managed pairing; temporary sessions disabled |
| Audio Accessory Auto-Unpair Time | 12 hours | Corporate | Paired audio accessories auto-disconnect after 12 hours |
| Allow Paired Apple Watch | Allowed | Corporate | Apple Watch pairing is permitted on corporate devices |
| Force Apple Watch Wrist Detection | Enabled | Corporate | Apple Watch locks when removed from wrist |
| Block Non-Configurator Host Pairing | Blocked | L2 | Prevents USB pairing with unauthorized computers |
| Block Proximity Setup to New Device | Blocked | L1 | Prevents Quick Start device-to-device migration via proximity |
| USB Restricted Mode | Enabled | L1 | Blocks USB accessories (data transfer tools) after device is locked |

---

### 4.8 Accessibility & Sharing — Corporate Devices

Controls Apple ecosystem sharing and remote access features that could expose corporate content to non-managed devices.

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Block Handoff (Activity Continuation) | Blocked | L1 | Prevents tasks from continuing on a personal Mac or iPad |
| Block iPhone Mirroring | Blocked | L2 | Prevents iPhone screen from being mirrored and controlled on a Mac |
| Block iPhone Widgets on Mac | Blocked | L2 | Prevents iPhone app widgets from appearing on a Mac |
| Block Remote Screen Observation | Blocked | L2 | Prevents remote viewing of the device screen |

> **CIS iOS 26:** "Block Handoff" promoted to L1 for corporate devices.

---

### 4.9 Apple Services — Corporate Devices

Restricts Apple consumer services on supervised corporate devices. All settings require device supervision.

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Block Apple Books Store | Blocked | L2 | Prevents access to the Apple Books store |
| Block Apple Books Erotica | Blocked | L2 | Blocks adult/explicit content in Books |
| Block Explicit Content | Blocked | L2 | Blocks explicit media across Apple services |
| Block Find My Friends | Blocked | L2 | Prevents personal location sharing via Find My Friends |
| Block Find My Friends Modification | Blocked | L2 | Prevents changing Find My Friends settings |
| Block Game Center | Blocked | L2 | Disables Game Center entirely (also removes the need for individual Game Center sub-restrictions) |
| Block iTunes Store | Blocked | L2 | Prevents access to iTunes Store for media purchases |
| Block Apple Music | Blocked | L2 | Blocks Apple Music streaming service |
| Block Apple News | Blocked | L2 | Blocks Apple News app content |
| Block Podcasts | Blocked | L2 | Blocks Apple Podcasts app and podcast playback |

---

### 4.10 Device Privacy — Corporate Devices

Controls privacy-impacting telemetry and advertising features. Prevents device usage data from being sent to Apple and third-party advertisers.

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Block App Analytics Sharing (Settings) | Blocked | L1 | Blocks the app analytics toggle in Settings; prevents usage data shared with developers |
| Block Diagnostic Data Submission (Settings) | Blocked | L1 | Blocks the diagnostic submission toggle in Settings |
| Block Apple Personalized Advertising | Blocked | L1 | Opts out of Apple's personalized ad targeting |
| Block Diagnostic Submission (MDM key) | Blocked | L1 | MDM-level enforcement of diagnostic submission block (defense in depth) |
| Block Diagnostic Submission Modification | Blocked | L1 | Prevents users from re-enabling diagnostic submission |
| Enable Mail Privacy Protection | Enabled | L1 | Blocks tracking pixels in email; hides IP address from senders |
| Force Limit Ad Tracking | Enabled | L1 | Prevents apps from using the Advertising Identifier (IDFA) for tracking |
| Block Spotlight Internet Results | Blocked | L2 | Prevents Spotlight from sending search queries to Apple and third-party providers |

> **Dual control:** App Analytics and Diagnostic Submission are configured via both Settings-level keys (`settings_item_` prefix) and MDM restriction keys (`com.apple.applicationaccess_` prefix) for defense in depth. The Settings-level control blocks the toggle in the Settings app; the MDM key enforces the restriction regardless.

---

### 4.11 Lock Screen — Corporate Devices

Controls lock screen access and configures the asset tag and IT contact information displayed when the device is locked.

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Block Control Center on Lock Screen | Blocked | L1 | Prevents hardware toggles (Airplane Mode, Bluetooth, Wi-Fi) from the lock screen |
| Block Notification Center on Lock Screen | Blocked | L1 | Hides notification content when device is locked |
| Block Today View on Lock Screen | Blocked | L1 | Hides widget content (calendar, reminders) from lock screen |
| Block Wallet on Lock Screen | Blocked | L1 | Prevents Apple Pay and wallet cards without device unlock |
| Asset Tag Information | REPLACE | Corporate | Displays organization asset tag on lock screen — replace placeholder before deploying |
| Lock Screen Footnote | REPLACE | Corporate | Displays IT contact number on lock screen — replace placeholder before deploying |

> **Before deploying:** Replace both REPLACE: placeholder values with your organization's actual asset tag format and IT contact number. The placeholders will display literally on device lock screens if not updated.
>
> **CIS iOS 26:** "Block Control Center on Lock Screen" promoted to L1 for Corporate devices.

---

### 4.12 Safari Browser — Corporate Devices

Safari security controls for supervised corporate devices. `Safari Allow AutoFill` and `Allow Private Browsing` require device supervision and cannot be enforced on unsupervised BYOD devices.

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Block Third-Party Cookies | Blocked | L2 | Blocks cross-site tracking cookies; may affect some web applications |
| Block Disabling Fraud Warning | Blocked | L1 | Prevents users from disabling Safari phishing protection |
| Allow JavaScript | Allowed | N/A | JavaScript is permitted; blocking would break most web applications |
| Block Pop-ups | Blocked | L1 | Blocks malicious pop-ups and redirects |
| Block Private Browsing | Blocked | L2 | Removes the Private Tab option in Safari (supervised only) |
| Block Safari AI Summary | Blocked | Corporate | Blocks Safari-generated AI page summaries (iOS 26) |
| Safari Start Page | https://m365.cloud.microsoft/ | Corporate | Sets Microsoft 365 as the Safari new tab/start page |
| Cross-Site Tracking Relaxed Domains | microsoft.com, login.microsoftonline.com, manage.microsoft.com, sts.windows.net | Corporate | Microsoft authentication domains exempted from cross-site tracking prevention |
| Block Safari History Clearing | Blocked | L2 | Prevents users from clearing Safari browsing history |
| Block Safari AutoFill | Blocked | L1 | Prevents automatic password and form filling (supervised only) |
| Force Fraud Warning | Enabled | L1 | Enforces Safari phishing protection; cannot be disabled by user |

---

### 4.13 Software Update — Corporate Devices

DDM-based software update enforcement for supervised corporate devices. Requires iOS/iPadOS 17.0+ and ADE.

> **iOS 26 mandatory DDM:** Apple deprecated legacy MDM software update commands in iOS/iPadOS 26. Supervised devices on iOS 26+ only respond to DDM update policies. Legacy policies under **Devices > Update policies for iOS/iPadOS** are silently ignored on iOS 26+ devices. Configure updates exclusively through the Settings Catalog DDM path.

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Enforce Latest Software Update | Enabled | L1 | Devices must update by the enforcement deadline |
| Deferral Window | 14 days | L1 | IT has 14 days to validate each update before enforcement |
| Scheduled Install Time | 02:00 | L1 | Updates install at 2:00 AM local time to minimize user disruption |
| Automatic Download | Enabled | L1 | Updates download automatically in the background |
| Automatic OS Update Install | Enabled | L1 | OS updates install automatically after download |
| Install Security Update (Background Security Improvement) | AlwaysOn | L1 | Background Security Improvements install automatically; user cannot disable |
| Rapid Security Response Enable | Enabled | L1 | Enables Apple's emergency security patch delivery channel |
| Block RSR Rollback | Blocked | L1 | Prevents rolling back applied Rapid Security Responses |
| Combined Deferral Period | 14 days | L2 | Maximum combined deferral across all update types |
| Show Update Notifications | Enabled | Corporate | Notifies users when updates are available |
| Recommended Update Cadence | Monthly | Corporate | Major release cadence recommendation |

> **Deprecated settings:** The "Allow Background Security Improvement Installation (Deprecated)" and "Allow Background Security Improvement Removal (Deprecated)" settings visible in the Intune Settings Catalog are legacy RSR restriction keys. Do not use these — enforcement is handled via the `softwareupdate_automaticactions_installsecurityupdate` (AlwaysOn) setting above.

> **BYOD update enforcement:** Software update enforcement via Settings Catalog is not available on unsupervised BYOD devices. Use the Compliance Policy minimum OS version (Section 7.2) combined with Conditional Access to block access from out-of-date personal devices.

---

### 4.14 Apple Intelligence & Siri — Corporate Devices

Controls Apple Intelligence on-device AI features, external AI integration (ChatGPT), and Siri behavior on corporate devices. Apple Intelligence settings are not applied to BYOD devices — see Section 5.7 for the reason.

**External Intelligence (ChatGPT / Third-Party AI)**

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Block External AI Integration | Blocked | L1 | Disables ChatGPT and third-party AI extensions at the OS level |
| Block Sign-In to External AI Services | Blocked | L1 | Prevents sign-in to ChatGPT or other external AI via Apple Intelligence |

**Intelligence Settings (On-Device Apple Intelligence)**

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Block Visual Intelligence Summary | Blocked | L1 | Prevents camera-based AI from analyzing surroundings or documents |
| Force On-Device Only Dictation | Enabled | L1 | Voice input processed locally; no audio sent to Apple servers |
| Force On-Device Only Translation | Enabled | L1 | Translation processed locally; no text sent to Apple servers |
| Block Apple Intelligence Report | Blocked | L2 | Blocks diagnostic telemetry about Apple Intelligence from being sent to Apple |
| Block Personalized Handwriting Results | Blocked | L2 | Prevents handwriting samples from being used for model training |
| Block Image Playground | Blocked | L2 | Disables AI image generation on corporate devices |
| Block Image Wand | Blocked | L2 | Disables sketch-to-image (Image Wand) on corporate devices |
| Block Genmoji | Blocked | Corporate | Disables AI-generated emoji on corporate devices |
| Allow Writing Tools | Allowed | Corporate | AI text editing (rewrite, proofread, summarize) is permitted |

**Siri Settings**

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Block Siri While Locked | Blocked | L1 | Prevents voice-activated access when screen is locked |
| Block Siri User-Generated Content | Blocked | Corporate | Prevents Siri interaction data from being sent to Apple |
| Disable Siri | Blocked | Corporate | Siri is disabled entirely on corporate devices |

---

### 4.15 Web-App-Store (EU) — Corporate Devices

EU Digital Markets Act (DMA) compliance controls. Prevents installation of apps from sources other than the Apple App Store on supervised corporate devices.

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Block Alternative Marketplace App Installation | Blocked | Corporate | Prevents installation from third-party app marketplaces (EU DMA) |
| Block Web Distribution App Installation | Blocked | Corporate | Prevents installation of apps distributed directly via websites (EU DMA) |

> **Regional applicability:** These settings only affect devices in EU regions where Apple has enabled alternative distribution under the Digital Markets Act. Outside EU regions, the settings are accepted by Intune but have no effect.

---

### 4.16 Microsoft Enterprise SSO — All Devices

Deploys the Microsoft Authenticator SSO extension to enable single sign-on for all Entra ID-integrated Microsoft apps on enrolled devices.

| Key | Value | Notes |
|---|---|---|
| Extension Identifier | `com.microsoft.azureauthenticator.ssoextension` | Microsoft Authenticator SSO extension |
| Extension Type | Credential (1) | Credential-type SSO extension |
| Enable SSO on All Managed Apps | 1 (Enabled) | SSO applies to all MSAL-integrated Microsoft apps |
| App Prefix Allow List | `com.microsoft.,com.apple.` | Scope of SSO coverage |
| Browser SSO Interaction | 1 (Enabled) | Enables SSO for Safari and other browsers at sign-in pages |
| Disable Explicit App Prompt | 1 (Enabled) | Suppresses per-app sign-in prompts when an SSO token is available |
| SSO URLs | login.microsoftonline.com, login.microsoft.com, sts.windows.net (+ gov/china endpoints) | Entra ID authentication endpoints |

> **Deploy first:** This policy should be deployed before enabling Conditional Access. SSO is a prerequisite for a seamless user authentication experience and is required for Conditional Access device compliance registration to function correctly.

---

## 5. BYOD Policies — Settings Catalog

BYOD devices are enrolled via the Intune Company Portal app. Users can remove the MDM profile at any time, which removes all policies and corporate data. BYOD policies are deliberately scoped to protect corporate data within managed apps — they do not restrict what users do on their own device for personal purposes.

> **No supervised settings:** All settings in this section apply to unsupervised devices. Settings requiring supervision are in Section 4 (Corporate policies) and are not applicable here.

---

### 5.1 Device Security — BYOD Devices

Passcode enforcement for personal enrolled devices. Settings are lighter than the Corporate policy to reduce enrollment friction on personally-owned devices.

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Require Passcode | Enabled | L1 | Required for enrollment; most users already have a passcode |
| Block Simple Passcodes | Blocked | L1 | Prevents predictable patterns (sequential or repeating digits) |
| Minimum Passcode Length | 6 | L1 | Base brute-force resistance |
| Maximum Auto-Lock | 5 minutes | L1 | Slightly more lenient than Corporate (2 min) to reduce BYOD friction |
| Grace Period Before Lock | Immediately | L1 | No delay before passcode required after screen lock (CIS iOS 26) |
| Maximum Failed Attempts | 6 | L1 | Wipe after 6 failed attempts (1 additional vs. Corporate) |
| Passcode Expiration | 365 days | L2 | Annual rotation; lower friction appropriate for a personal device |
| Passcode History | 3 | L2 | Prevents reuse of last 3 passcodes |
| Numeric Passcode Allowed | Allowed | BYOD | Alphanumeric not required on personal devices to reduce enrollment friction |

> **Auto-lock deviation:** BYOD uses 5-minute auto-lock (Corporate: 2 minutes). The Compliance Policy enforces the lock-screen requirement independently. 5 minutes provides comparable security with better usability for users reading, watching video, or on calls on a personal device.

---

### 5.2 Data Protection — BYOD Devices

Controls data transfer between managed (corporate) and unmanaged (personal) contexts. Scoped to protecting corporate data within managed apps — personal app behavior is not restricted.

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Block Managed Docs in Unmanaged Apps | Blocked | L1 | Corporate files cannot be opened in personal apps |
| Block Unmanaged Docs in Managed Apps | Blocked | L1 | Personal files cannot enter managed app space |
| Block Managed Apps Writing Unmanaged Contacts | Blocked | L2 | Prevents managed apps from writing contacts to personal Contacts |
| Block Unmanaged Apps Reading Managed Contacts | Blocked | L2 | Prevents personal apps from reading work contacts |
| Block Screen Capture | Blocked | L2 | Prevents screenshots of managed app content |
| Block iCloud Private Relay | Blocked | L2 | Disables iCloud Private Relay on the enrolled device |
| Force AirDrop Unmanaged Mode | Enabled | L2 | Files sent via AirDrop from managed apps are treated as unmanaged |
| Require AirPlay Pairing Password | Required | L2 | Password required before AirPlay connections |

> **AirDrop design decision:** AirDrop is not blocked entirely on BYOD. `forceairdropunmanaged` ensures files from managed apps are treated as unmanaged, preventing corporate data from entering managed apps via AirDrop. A full device-level block was excluded from this baseline — personal AirDrop use is a legitimate feature on a personal device.

---

### 5.3 iCloud & Storage — BYOD Devices

Scoped iCloud controls for personal devices. Only managed app data is restricted — personal iCloud sync for personal content is unaffected.

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Force Encrypted Backup | Enabled | L1 | Device backups are encrypted with the device passcode |
| Block Managed Apps Cloud Sync | Blocked | L1 | Prevents corporate app data from syncing to personal iCloud |

> **iCloud Keychain not blocked:** iCloud Keychain is not restricted on BYOD. App Protection Policies already prevent corporate credentials from syncing to personal iCloud. Blocking Keychain on a personal device restricts personal banking, shopping, and family password management without meaningful security gain.

---

### 5.4 Device Restrictions — BYOD Devices

Lock screen access controls and behavioral restrictions for personal enrolled devices.

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Block Notification Center on Lock Screen | Blocked | L1 | Hides notification content when device is locked |
| Block Control Center on Lock Screen | Blocked | L1 | Prevents hardware toggles from the lock screen (note: requires supervision; silently ignored on unsupervised BYOD) |
| Block Lock Screen Today View | Blocked | L1 | Hides widget content (calendar, reminders) from lock screen |
| Block Wallet on Lock Screen | Blocked | L1 | Prevents Apple Pay and wallet cards without device unlock |
| Allow Auto Unlock | Allowed | Corporate | Apple Watch can auto-unlock device |
| Allow Biometric (Face ID / Touch ID) Unlock | Allowed | Corporate | Biometric unlock explicitly permitted |
| Block Untrusted TLS Certificate Prompts | Blocked | L1 | Prevents accepting invalid or self-signed TLS certificates |
| Block Handoff (Activity Continuation) | Blocked | L1 | Prevents corporate app tasks from continuing on a personal Mac or iPad |
| Force Apple Watch Wrist Detection | Enabled | Corporate | Apple Watch locks when removed from wrist |

> **Supervision note:** "Block Control Center on Lock Screen" requires device supervision and will be silently ignored on unsupervised BYOD devices. It is included to cover any supervised BYOD configurations. For standard unsupervised BYOD, App Protection Policies control managed app lock behavior.

---

### 5.5 Device Privacy — BYOD Devices

Privacy controls for personal devices. Scoped to advertising tracking and diagnostic submission. Spotlight internet results are not blocked on BYOD — personal web search is a personal device feature.

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Block App Analytics Sharing (Settings) | Blocked | L1 | Blocks app analytics toggle in Settings |
| Block Diagnostic Data Submission (Settings) | Blocked | L1 | Blocks diagnostic submission toggle in Settings |
| Block Apple Personalized Advertising | Blocked | L1 | Opts out of Apple personalized ad targeting |
| Block Diagnostic Submission (MDM key) | Blocked | L1 | MDM-level enforcement (defense in depth) |
| Enable Mail Privacy Protection | Enabled | L1 | Blocks tracking pixels in email |
| Force Limit Ad Tracking | Enabled | L1 | Prevents IDFA-based advertising tracking |

---

### 5.6 Safari Browser — BYOD Devices

Safari security controls for unsupervised personal devices. AutoFill blocking and Private Browsing blocking require device supervision and cannot be enforced on BYOD.

| Setting | Configured Value | CIS Level | Notes |
|---|---|---|---|
| Block Third-Party Cookies | Blocked | L2 | Reduces cross-site tracking; may affect some websites |
| Allow Disabling Fraud Warning | Allowed | BYOD | BYOD users may disable Safari fraud warning on personal device; `safariforcefraudwarning` (MDM key) remains enabled as a separate enforcement layer |
| Cross-Site Tracking Relaxed Domains | microsoft.com, login.microsoftonline.com, manage.microsoft.com, sts.windows.net | Corporate | Microsoft authentication domains exempted from cross-site tracking prevention |
| Force Fraud Warning | Enabled | L1 | Safari phishing protection enforced via MDM key; cannot be disabled by policy |

> **AutoFill not enforceable on BYOD:** `safariallowautofill` requires device supervision as of iOS 13 and cannot be enforced on personal enrolled devices via MDM.

> **Fraud warning deviation:** `allowdisablingfraudwarning` is set to `true` (allow) on BYOD, permitting users to turn off the Safari fraud warning toggle if they choose. The MDM-level `safariforcefraudwarning` key remains `true`, providing an independent enforcement layer. Restricting personal Safari behavior on a personal device is disproportionate — pop-up blocking has also been removed from this policy for the same reason.

---

### 5.7 Apple Intelligence — BYOD Devices (Not Applicable — DDM Incompatibility)

No Apple Intelligence policy is deployed to BYOD devices in this baseline.

**Reason:** Microsoft has migrated Apple Intelligence settings in the Intune Settings Catalog from Device Restrictions Configuration to Declarative Device Management (DDM). The new DDM-based Apple Intelligence settings apply correctly on supervised Corporate devices (ADE/ABM). However, when the same settings are assigned to unsupervised BYOD devices, Intune returns an error and the settings do not apply.

Because there is no supported DDM equivalent for Apple Intelligence controls on unsupervised devices, the BYOD Apple Intelligence & Siri policy has been removed from this baseline.

---

## 6. App Protection Policy Recommendations (MAM)

App Protection Policies (MAM) enforce data boundaries within managed apps on both enrolled and unenrolled devices. Unlike Settings Catalog policies, which configure device-level MDM restrictions, App Protection Policies operate at the app layer — they apply to Microsoft 365 apps (Outlook, Teams, OneDrive, Edge) regardless of whether the device is enrolled in Intune MDM.

CIS does not define App Protection Policy recommendations. The settings in this section are drawn from the Microsoft iOS/iPadOS Security Configuration Framework, which defines two APP tiers: **Level 1 (Enterprise Basic Security)** and **Level 2 (Enterprise Enhanced Security)**. This baseline combines both tiers into a single BYOD policy — all settings in this section apply to the combined policy.

> **Scope:** The combined App Protection Policy in this baseline targets BYOD users. Corporate supervised devices receive device-level data protection controls through Settings Catalog policies (Sections 4 and 5). App Protection Policies provide an additional layer for managed apps on corporate devices, but their primary role in this baseline is BYOD data containment.

> **CIS coverage:** App Protection Policies are not addressed in the CIS Apple iOS/iPadOS Benchmark. The "Microsoft Level" column in this section references the Microsoft iOS/iPadOS Security Configuration Framework tiers only.

---

### 6.1 Data Transfer and Protection

Controls how managed app data can move between apps and storage locations.

| Setting | Recommended Value | Microsoft Level | Notes |
|---|---|---|---|
| Backup org data to iTunes and iCloud backup | Block | L1 | Corporate app data excluded from personal iCloud or iTunes backups |
| Send org data to other apps | Policy managed apps | L1 | Corporate data stays within Intune-managed Microsoft 365 apps |
| Save copies of org data | Block | L1 | Prevents saving corporate files to unmanaged storage or personal cloud services |
| Allow user to save copies to selected services | OneDrive for Business, SharePoint | L1 | Permitted save destinations for corporate data |
| Receive data from other apps | Policy managed apps | L1 | Managed apps only accept data from other managed apps |
| Restrict cut, copy, paste between apps | Policy managed apps with paste in | L1 | Cut/copy from managed apps restricted to managed destinations; paste from any source into managed apps is allowed |
| Transfer telecommunication data to | Any dialer app | L1 | Phone numbers in managed apps open any installed dialer |
| Encrypt org data | Require | L1 | Managed app data encrypted at rest on the device |
| Sync policy managed app data with native apps or add-ins | Block | L2 | Prevents managed app data (contacts, calendar entries) from syncing to native iOS Contacts or Calendar |
| Third-party keyboards | Block | L2 | Custom keyboard apps are blocked inside managed apps to prevent input capture |
| Org data notifications | Block org data | L2 | Notification center previews for managed apps do not display corporate content |

---

### 6.2 Access Requirements

Controls authentication requirements to open and use managed apps.

| Setting | Recommended Value | Microsoft Level | Notes |
|---|---|---|---|
| PIN for access | Require | L1 | A separate PIN is required to open managed apps, independent of the device passcode |
| PIN type | Numeric (L1) / Passcode (L2) | L1/L2 | L1 uses a numeric PIN; L2 requires an alphanumeric passcode; combined policy enforces alphanumeric |
| Simple PIN | Block | L1 | Prevents sequential or repeating PIN patterns |
| Minimum PIN length | 4 (L1) / 6 (L2) | L1/L2 | Combined policy enforces minimum length of 6 |
| Number of attempts before PIN reset | 5 | L1 | After 5 incorrect PIN attempts, user must reset their PIN |
| PIN reset after number of days | Not configured (L1) / 365 days (L2) | L2 | Annual PIN rotation required in the enhanced baseline |
| Allow biometric instead of PIN | Allow | L1 | Face ID or Touch ID satisfies the PIN requirement |
| Override biometrics with PIN after timeout | Require | L2 | Periodic full PIN entry required even when biometrics are enrolled |
| Timeout (minutes of inactivity) | 30 minutes | L2 | PIN required if the managed app has been inactive for 30 minutes |
| Work or school account credentials for access | Not required (L1) / Require (L2) | L2 | L2: users must authenticate with Entra ID credentials, not just the app PIN |
| Recheck access requirements after (minutes of inactivity) | 30 minutes | L2 | Access requirements re-evaluated after 30 minutes of inactivity |

---

### 6.3 Conditional Launch

Health checks that determine whether to allow, warn, block, or wipe managed app access based on device state.

| Setting | Recommended Value | Microsoft Level | Notes |
|---|---|---|---|
| Max PIN attempts | 5 — Block access | L1 | Block access after 5 incorrect PIN attempts; requires PIN reset to regain access |
| Offline grace period | 720 hours (L1) / 168 hours (L2) | L1/L2 | Apps remain functional without checking in to Intune for this duration while offline; combined policy uses 168 hours |
| Jailbroken / rooted devices | Block access | L1 | Prevents managed app access from compromised devices; most critical conditional launch check |
| Disabled account | Block access | L1 | Block access if the user's Entra ID account is disabled |
| Minimum OS version | Warn (L1) / Block (L2) | L1/L2 | L1: user warned when OS is outdated; L2: access blocked until OS is updated; combined policy blocks access |
| Minimum app version | Warn | L1 | Users running outdated Microsoft 365 app versions receive a remediation prompt |
| Minimum Intune SDK version | Warn | L1 | Apps built against an outdated Intune MAM SDK receive a warning to update |
| Device threat level | Not configured (L1) / Secured (L2) | L2 | Requires Microsoft Defender for Endpoint; blocks managed app access if active threats are detected |

> **Combined L1 + L2 baseline:** This baseline deploys a single combined App Protection Policy that applies all L1 and L2 settings simultaneously to BYOD users. There is no separate L1-only policy — all users receive the full combined control set.

---

## 7. Corporate – Compliance Policy Settings

Compliance policies define the minimum security requirements a supervised device must meet to be considered compliant. Non-compliant devices are blocked from accessing corporate resources via Conditional Access. These settings are configured in **Intune > Devices > Compliance**.

> **Note:** Compliance passcode settings overlap with device configuration passcode settings but serve a different enforcement mechanism. Compliance triggers Conditional Access; device configuration enforces directly via MDM. Both should be configured.

---

### 7.1 Device Health

#### L1 – Device Health (Minimum Baseline)

| Setting | Recommended Value | User Impact | Security Benefit |
|---|---|---|---|
| Jailbroken Devices | `Block` (Mark as noncompliant) | None | Critical — prevents privilege escalation; jailbroken devices bypass MDM controls |
| Require Device to Be at or Under Device Threat Level | `Not configured` (L1) | None | Enable at L2 once Defender for Endpoint is deployed |

#### L2 – Device Health (Enhanced Security)

| Setting | Recommended Value | User Impact | Security Benefit |
|---|---|---|---|
| Jailbroken Devices | `Block` | None | (Reinforced from L1) |
| Require Device to Be at or Under Device Threat Level | `Secured` | High (requires Defender for Endpoint) | Integrates advanced threat detection; blocks access if active threats are found |

> **Note (Device Threat Level):** Setting threat level to `Secured` requires Microsoft Defender for Endpoint to be deployed on devices. This is a CIS L2 / Microsoft Level 2 recommendation and is increasingly considered a baseline requirement for 2025 deployments. See *Defender for Endpoint for iOS_iPadOS Devices.md* for deployment steps.

---

### 7.2 Device Properties

#### L1 – Device Properties (Minimum Baseline)

| Setting | Recommended Value | User Impact | Security Benefit |
|---|---|---|---|
| Minimum OS Version | N-1 (e.g., `25.0` when iOS 26 is current) | Medium | Ensures known CVEs on older versions are not present; Microsoft recommends the N-1 approach where N is the current iOS major release |
| Maximum OS Version | `Not configured` | None | Avoids blocking devices that updated early |

#### L2 – Device Properties (Enhanced Security)

| Setting | Recommended Value | User Impact | Security Benefit |
|---|---|---|---|
| Minimum OS Version | Current release (e.g., `26.x`) | Medium | Tighter OS currency enforcement; enforces latest major version |
| Maximum OS Version | Latest available (e.g., `26.x`) | Low | Prevents running unsupported beta/preview versions |
| Minimum OS Build Version | Match latest security patch | Low | Enforces patch level, not just major version |

> **Update guidance:** Review and update the minimum OS version in compliance policies within **30 days** of each major Apple iOS release. Use the 14-day update deferral window in update policy to validate before compliance enforcement catches up.
>
> **N-1 approach:** Microsoft recommends configuring the minimum iOS major version to match N-1, where N is the current iOS major release. Apple adopted year-based iOS naming at WWDC 2025 — the current release is iOS 26. For example, set the L1 minimum to `25.0` (or the latest 25.x patch) and update within 30 days after the next major iOS release. For minor and build versions, ensure devices are at the latest security patch per [Apple security updates](https://support.apple.com/HT201222).

---

### 7.3 System Security

#### L1 – System Security (Minimum Baseline)

| Setting | Recommended Value | User Impact | Security Benefit |
|---|---|---|---|
| Require a Password to Unlock Mobile Devices | `Require` | High (initial) | Foundation for all encryption and access control |
| Simple Passwords | `Block` | Low | Prevents predictable passwords |
| Minimum Password Length | `6` | Low | Base brute-force resistance |
| Maximum Minutes of Inactivity Before Password is Required | `2` | Medium | Limits unattended access window |
| Require Encryption on Data Storage on Device | `Require` | None | iOS encrypts automatically when a passcode is set |

#### L2 – System Security (Enhanced Security)

| Setting | Recommended Value | User Impact | Security Benefit |
|---|---|---|---|
| Minimum Password Length | `8` | Low | Stronger credential |
| Required Password Type | `Alphanumeric` | Medium | Forces mix of letters and numbers |
| Password Expiration (days) | `180` | Medium | Forces periodic rotation via compliance enforcement |
| Number of Previous Passwords to Prevent Reuse | `5` | Low | Prevents cycling back to known passwords |
| Maximum Minutes After Screen Lock Before Password is Required | `Immediately` | Medium | No grace period on supervised devices |
| Require Managed Email Profile | `Require` | Medium | Ensures only Intune-managed email account exists on device |

> **Managed Email Profile:** This setting marks devices noncompliant if they have a non-managed email account configured. Deploy an email profile (Exchange/Outlook) via device configuration **before** enabling this setting to avoid immediate mass noncompliance.

---

### 7.4 Actions for Noncompliance

| Action | Recommended Timing | Notes |
|---|---|---|
| Mark Device as Noncompliant | `Immediately` (0 days) | Signals the compliance state to Conditional Access right away |
| Send Push Notification to User | `0 days` (same day) | Notifies user of noncompliant state with remediation guidance |
| Send Email to User | `1 day` | Follow-up with step-by-step remediation instructions |
| Block Access to Corporate Resources | `3 days` | Gives users time to remediate before access is lost |
| Retire Device (Remote Wipe Managed Data) | `30 days` | Escalation for persistent noncompliance on corporate devices |

> **Grace period recommendation:** A **3-day grace period** before blocking access is appropriate for supervised corporate devices. Reduce to 0 days for high-security or regulated environments.

---

## 8. BYOD – Compliance Policy Settings

BYOD compliance policies must balance security enforcement with respect for user privacy and device ownership. Users can remove the MDM profile at any time; compliance policies combined with Conditional Access create the incentive to remain enrolled and compliant.

---

### 8.1 Device Health

#### L1 – Device Health (Minimum Baseline)

| Setting | Recommended Value | User Impact | Security Benefit |
|---|---|---|---|
| Jailbroken Devices | `Block` (Mark as noncompliant) | None | Critical — jailbroken devices bypass all MDM restrictions |
| Require Device to Be at or Under Device Threat Level | `Not configured` (L1) | None | Optional — enable at L2 if Defender is deployed to BYOD |

#### L2 – Device Health (Enhanced Security)

| Setting | Recommended Value | User Impact | Security Benefit |
|---|---|---|---|
| Jailbroken Devices | `Block` | None | (Reinforced from L1) |
| Require Device to Be at or Under Device Threat Level | `Low` or `Medium` | Medium (Defender install required) | Threat integration with softer threshold appropriate for BYOD |

> **BYOD Threat Level Recommendation:** For BYOD, use `Low` or `Medium` rather than `Secured`. Requiring `Secured` on a personal device may drive unenrollment. Evaluate based on your organization's risk tolerance and Defender deployment rate on personal devices.

---

### 8.2 Device Properties

#### L1 – Device Properties (Minimum Baseline)

| Setting | Recommended Value | User Impact | Security Benefit |
|---|---|---|---|
| Minimum OS Version | N-1 (e.g., `25.0` when iOS 26 is current) | Medium | Eliminates high-severity known vulnerabilities on older OS versions; use the N-1 approach aligned with Microsoft's recommendation |
| Maximum OS Version | `Not configured` | None | Avoids blocking users on latest releases |

#### L2 – Device Properties (Enhanced Security)

| Setting | Recommended Value | User Impact | Security Benefit |
|---|---|---|---|
| Minimum OS Version | Current release (e.g., `26.x`) | Medium | Keeps BYOD OS currency close to supervised standard |
| Maximum OS Version | Latest available | Low | Prevents running unsupported builds |

> **BYOD OS update path:** Since updates cannot be forced on BYOD, set the grace period in noncompliance actions to **7 days** before blocking access. Send a push notification and email on day 0 with update instructions. Apply the same N-1 update guidance from Section 7.2 — review and update the minimum OS version within 30 days of each major iOS release. Apple's year-based naming means the current release is iOS 26; N-1 is iOS 25.

---

### 8.3 System Security

#### L1 – System Security (Minimum Baseline)

| Setting | Recommended Value | User Impact | Security Benefit |
|---|---|---|---|
| Require a Password to Unlock Mobile Devices | `Require` | High (initial) | Foundation for encryption and access control |
| Simple Passwords | `Block` | Low | Prevents predictable passwords |
| Minimum Password Length | `6` | Low | Base brute-force resistance |
| Maximum Minutes of Inactivity Before Password is Required | `5` | Medium | Slightly more lenient than supervised; reduces BYOD friction |
| Require Encryption on Data Storage on Device | `Require` | None | iOS encrypts automatically when a passcode is set |

#### L2 – System Security (Enhanced Security)

| Setting | Recommended Value | User Impact | Security Benefit |
|---|---|---|---|
| Minimum Password Length | `8` | Low | Stronger credential |
| Required Password Type | `Alphanumeric` | Medium | Forces mix of letters and numbers |
| Password Expiration (days) | `365` | Low | Annual rotation; lower friction for BYOD users |
| Number of Previous Passwords to Prevent Reuse | `3` | Low | Basic reuse prevention |
| Maximum Minutes After Screen Lock Before Password is Required | `Immediately` | Medium | Aligns with CIS iOS 26 L1 requirement — no grace period on BYOD (corrected from previous 5 minutes) |
| Require Managed Email Profile | `Not configured` | N/A | Not recommended for BYOD — enforce email security via App Protection Policies on Outlook instead |

---

### 8.4 Actions for Noncompliance

| Action | Recommended Timing | Notes |
|---|---|---|
| Mark Device as Noncompliant | `Immediately` (0 days) | Signals noncompliant state to Conditional Access |
| Send Push Notification to User | `0 days` (same day) | Immediate notification with remediation steps |
| Send Email to User | `1 day` | Follow-up with update/passcode instructions |
| Block Access to Corporate Resources | `7 days` | Gives users reasonable time to update personal device or set passcode |
| Retire Device (Remove Managed Data Only) | `30 days` | BYOD retire removes only corporate data; does not wipe personal content |

> **BYOD Grace Period:** A **7-day grace period** before access blocking is recommended for personal devices. This respects device ownership while still enforcing compliance. For high-security environments, reduce to 3 days.

---

## 9. Conditional Access Policy Recommendations

Compliance policies alone have no enforcement power — they only signal a compliance state. **Conditional Access (CA) policies** consume that signal and determine whether to grant or block access to corporate resources. Without CA policies, a noncompliant iOS device can still access Exchange Online, SharePoint, Teams, and all other Microsoft 365 workloads.

Configure Conditional Access policies in **Microsoft Entra ID > Security > Conditional Access**.

> **Prerequisite:** Microsoft Entra ID P1 license is required for all Conditional Access policies.

### 9.1 Core Policy: Require Compliant Device for iOS

This is the foundational CA policy that enforces compliance for all iOS devices accessing corporate resources.

| Setting | Recommended Value |
|---|---|
| **Users** | All users (exclude break-glass accounts) |
| **Cloud Apps or Actions** | All cloud apps (or scope to Office 365 / Microsoft 365 suite) |
| **Conditions – Device Platforms** | iOS, iPadOS |
| **Grant** | Require device to be marked as compliant |
| **State** | Report-only first; enable after validating compliance rates |

> **Break-glass accounts:** Always exclude at least two break-glass administrator accounts from all CA policies. These accounts must have long, complex passwords and MFA registered on a different authenticator, stored securely offline.

### 9.2 BYOD Device Policy

For BYOD devices, allow access only from compliant personal devices, and optionally require App Protection Policies for specific apps.

| Setting | Recommended Value |
|---|---|
| **Users** | All users (exclude break-glass) |
| **Cloud Apps or Actions** | Office 365 (Exchange Online, SharePoint, Teams) |
| **Conditions – Device Platforms** | iOS, iPadOS |
| **Conditions – Filter for Devices** | Exclude supervised filter to target BYOD only |
| **Grant** | Require device to be marked as compliant **OR** Require App Protection Policy |
| **State** | Enable after BYOD compliance policy is broadly deployed |

> **"OR" vs "AND" for BYOD:** Using **OR** between compliant device and App Protection Policy allows BYOD users to access corporate apps through Microsoft 365 apps (Outlook, Teams) even if they choose not to fully enroll their device into MDM — as long as the App Protection Policy is satisfied. This is the recommended balance for BYOD.

### 9.3 Enrollment Flow Exclusions

> **Critical:** iOS device enrollment itself requires internet access and authentication before the device is compliant. If CA policies block unenrolled devices, new enrollments will fail.

Configure the following exclusion to allow enrollment to complete before compliance is checked:

| Setting | Value |
|---|---|
| Exclude from all iOS CA policies | `Microsoft Intune Enrollment` app (service principal) |
| Exclude from all iOS CA policies | `Microsoft Intune` app (service principal) |

These exclusions allow the enrollment handshake and initial compliance evaluation to complete before the device is subject to the require-compliant-device grant control.

---

*This document is part of the UniFy Endpoint – Microsoft Intune iOS/iPadOS Baseline series. Document maintained by Yoennis Olmo - Sr. Modern Work Consultant.*
