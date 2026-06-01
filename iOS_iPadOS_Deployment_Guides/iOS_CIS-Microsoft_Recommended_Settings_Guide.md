# iOS/iPadOS CIS Recommended Settings Guide – Microsoft Intune

| | |
|---|---|
| **Document Type** | Work Instruction – Configuration Reference |
| **Author** | Sr. Modern Work Consultant |
| **Last Updated** | June 2026 |
| **Version** | 1.2 |
| **Applies To** | iOS/iPadOS 26 and later, Microsoft Intune, Settings Catalog |
| **CIS Reference** | CIS Apple iOS/iPadOS 26 Benchmark v1.0.0 |
| **Microsoft Reference** | iOS/iPadOS Supervised & Personal Device Security Configurations (updated April 2026) |

---

## Table of Contents

1. [Overview & Scope](#1-overview--scope)
2. [Security Levels & Source Classification](#2-security-levels--source-classification)
   - 2.1 [CIS Benchmark Levels](#21-cis-benchmark-levels)
   - 2.2 [Microsoft Security Framework Levels](#22-microsoft-security-framework-levels)
   - 2.3 [Admin Experience Level (ADM-EXP)](#23-admin-experience-level-adm-exp)
   - 2.4 [Setting Source Classification](#24-setting-source-classification)
   - 2.5 [Policy Architecture](#25-policy-architecture)
3. [Compliance Reference](#3-compliance-reference)
   - 3.1 [Intentional Deviations from CIS Benchmark](#31-intentional-deviations-from-cis-benchmark)
   - 3.2 [CIS L1 Settings](#32-cis-l1-settings)
   - 3.3 [CIS L2 Settings](#33-cis-l2-settings)
   - 3.4 [Microsoft Framework Settings (Non-CIS)](#34-microsoft-framework-settings-non-cis)
   - 3.5 [ADM-EXP Settings — Admin Experience](#35-adm-exp-settings--admin-experience)
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
   - 5.7 [Apple Intelligence & Siri — BYOD (DDM Gap)](#57-apple-intelligence--siri--byod-ddm-gap)
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

This guide documents recommended Settings Catalog, Software Update, and Compliance settings for iOS/iPadOS in Microsoft Intune, aligned to the **CIS Apple iOS/iPadOS 26 Benchmark v1.0.0** and the **Microsoft iOS/iPadOS Security Configuration Framework**.

| Section | Contents |
|---|---|
| **Section 2** | Security level labels — how to read each setting's Level / Source value |
| **Section 3** | Compliance reference: Deviations Registry + CIS L1 / CIS L2 / MS-only / ADM-EXP source tables |
| **Sections 4–5** | Per-policy settings for Corporate and BYOD devices with Level / Source per setting |
| **Section 6** | App Protection Policy (MAM) recommendations |
| **Sections 7–9** | Compliance policies and Conditional Access |

Not covered: App Configuration Policies, Microsoft Defender for Endpoint, VPN/Wi-Fi/Email/Certificate profile templates.

> **DDM note:** iOS/iPadOS 26 requires DDM for software update enforcement on supervised devices (ADE + iOS/iPadOS 17.0+). Legacy update policies under **Devices > Update policies for iOS/iPadOS** no longer function on iOS 26+ devices — configure updates exclusively through the Settings Catalog DDM path (Section 4.13).

---

## 2. Security Levels & Source Classification

### 2.1 CIS Benchmark Levels

| Level | Description | Target Audience |
|---|---|---|
| **L1** | Basic security controls with minimal user impact. Reduces the attack surface without significantly restricting functionality. | All devices accessing work or school data |
| **L2** | Enhanced security controls for environments handling sensitive data. Some restrictions may affect usability. | Most organizations; recommended as the standard baseline |

> **Note:** CIS iOS/iPadOS 26 Benchmark v1.0.0 defines four profiles: Level 1 End-User Owned, Level 2 End-User Owned, Level 1 Institutionally-Owned, Level 2 Institutionally-Owned. Settings labeled MS-Lx in this guide refer to Microsoft Security Framework (see Section 2.2).

### 2.2 Microsoft Security Framework Levels

| Level | Description |
|---|---|
| **MS-L1** | Minimum recommended security for personal devices accessing work data |
| **MS-L2** | Enhanced security; recommended for most mobile users and organizations |
| **MS-L3** | High-security environments with sophisticated threats; strongest enforcement with minimal user flexibility |

### 2.3 Admin Experience Level (ADM-EXP)

| Label | Description | When to Adjust |
|---|---|---|
| **ADM-EXP** | Settings not required by CIS or the Microsoft Security Framework. Configured based on professional admin deployment experience and operational best practices. | Fully adjustable — modifying or removing ADM-EXP settings does not affect CIS or Microsoft framework alignment. These are the "wiggle room" settings Nick Benton referenced. |

> **ADM-EXP settings** cover areas such as blocked app bundle IDs, Safari start page, cross-site tracking exemption domains, asset tag lock screen messages, and operational hardening choices (e.g., Activation Lock disabled for IT asset recovery). See Section 3.5 for the complete list.

---

### 2.4 Setting Source Classification

Each setting in Sections 4 and 5 includes a **Level / Source** column showing where it comes from.

| Label | Meaning |
|---|---|
| `CIS L1` | CIS Apple iOS/iPadOS 26 Benchmark v1.0.0 — Level 1 |
| `CIS L2` | CIS Benchmark — Level 2 |
| `CIS Additional` | CIS Section 4 "Additional Recommendations" — best-practice guidance, not in the L1/L2 profile |
| `MS-L1` / `MS-L2` / `MS-L3` | Microsoft iOS/iPadOS Security Configuration Framework only (not in CIS) |
| `ADM-EXP` | **Admin Experience** — not required by CIS or the Microsoft Framework; configured based on professional admin deployment experience. These settings offer the most flexibility and can be adjusted or removed without affecting CIS or Microsoft alignment. |
| `CIS x.x.x*` | Intentional deviation from CIS — see Section 3.1 Deviations Registry |

>
> All settings — CIS L1, CIS L2, MS-L1, MS-L2, MS-L3, and ADM-EXP — are combined within the same functional-area policies (e.g., `Device Security - Corporate Devices`, `Data Protection - Corporate Devices`). There is no tier separation at the policy level. The baseline ships as a single deployable set per track — import and assign as-is. Organizations targeting a lower tier (e.g., CIS L1 only) can use the Level / Source column in Sections 4 and 5 alongside Sections 3.2–3.5 to identify which settings to remove from imported policies before assignment.

Settings labeled **ADM-EXP** are the most flexible — they can be modified or removed without affecting CIS or Microsoft alignment. Settings marked **CIS x.x.x*** show where this baseline intentionally deviates from CIS — see Section 3.1 for the justification and risk for each.

### 2.5 Policy Architecture

Settings are organized by **functional category** — each category covers one specific area of device behavior. All settings for a category are configured together. The Level / Source of each individual setting is documented per setting throughout Sections 4 and 5. Corporate and BYOD settings are in separate sections because the management scope differs: supervised corporate devices support a broader set of MDM and DDM restrictions than unsupervised personal devices.

---

## 3. Compliance Reference

This section provides a cross-cutting view of the baseline organized by **source** rather than by policy category. Use this section for pre-production review, CIS audit preparation, or evaluating which settings your organization can adjust without breaking framework alignment.

For the deployment-oriented view (settings organized by policy category), go directly to Section 4 (Corporate) or Section 5 (BYOD).

---

### 3.1 Intentional Deviations from CIS Benchmark

Settings configured differently from the CIS iOS/iPadOS 26 Benchmark v1.0.0. Each deviation is intentional and marked with `*` in the Level / Source column throughout this guide.

| CIS Control | Track | CIS Requirement | Baseline Value | Risk | Notes |
|---|---|---|---|---|---|
| Require Alphanumeric Passcode | Corporate / BYOD | Enabled — L1 | Disabled — numeric PIN accepted | Low | Reduces enrollment friction; minimum length 6 enforced; Compliance Policy enforces passcode independently |
| Force Automatic Date and Time | Corporate | Enabled — L1 | Not enforced | Low | Operational flexibility for time-zone scenarios; NTP typically enforced at network level |
| Writing Tools | Corporate / BYOD | Disabled — L1 | Enabled (SC) / Not Blocked (MAM) | Medium | Productivity benefit; on-device PCC processing mitigates leakage risk; MAM governs data in managed apps |
| Maximum Auto-Lock | BYOD | 2 min max — L1 | 5 minutes | Low | Reduces BYOD friction; Compliance Policy enforces lock-screen requirement independently |
| Allow Disabling Fraud Warning | BYOD | Block disabling — L1 | Allow disabling | Low | Restricting personal Safari on a personal device is disproportionate; `safariforcefraudwarning` MDM key enforced independently |
| Apple Intelligence (CIS 2.9.1–2.9.4) | BYOD | Disabled — L1 | Not applied — DDM gap | Medium | Microsoft migrated to DDM — incompatible with unsupervised BYOD; MAM App Protection Policy prevents corporate data from AI processing in managed apps |

> **For full CIS L1 alignment:** Low-risk deviations (alphanumeric passcode, auto-lock, fraud warning) are straightforward to restore. Medium-risk deviations (Writing Tools, Apple Intelligence BYOD) require either accepting the risk or implementing an alternative control.

---

### 3.2 CIS L1 Settings

All CIS Level 1 controls from the iOS/iPadOS 26 Benchmark v1.0.0. Settings that apply to both device tracks are listed once. Where the same control applies to both tracks but with different values or implementations, two rows are shown. The **MS - Level** column indicates if the setting also appears in the Microsoft iOS/iPadOS Security Configuration Framework.

> **The CIS L1 column is the non-negotiable baseline.** Removing any CIS L1 setting breaks CIS alignment. Deviations are marked with * and documented in Section 3.1.

#### Passcode

| Setting | Intune Policy | Enrollment Type | CIS - Level | MS - Level | Notes |
|---|---|---|---|---|---|
| Require Passcode | Device Security | Corporate / BYOD | L1 | MS-L1 | Enables hardware encryption; foundation for all access control |
| Block Simple Passcode | Device Security | Corporate / BYOD | L1 | MS-L1 | `allowsimple=false` — prevents sequential or repeating patterns |
| Require Alphanumeric Passcode | Not enforced | Corporate / BYOD | L1* | — | **Intentional deviation** — numeric passcode allowed; see Section 3.1 |
| Minimum Passcode Length 6 | Device Security | Corporate / BYOD | L1 | MS-L1 | `minlength=6` |
| Maximum Auto-Lock | Device Security | Corporate | L1 | MS-L1 | Baseline: 1 min (stricter than CIS 2-min maximum) |
| Maximum Auto-Lock | Device Security | BYOD | L1* | MS-L1 | **Intentional deviation** — baseline uses 5 min; see Section 3.1 |
| Grace Period = Immediately | Device Security | Corporate / BYOD | L1 | MS-L1 | `maxgraceperiod=0` — no delay before passcode required |
| Maximum Failed Attempts | Device Security | Corporate | L1 | — | 5 attempts — device wipes after 5 |
| Maximum Failed Attempts | Device Security | BYOD | L1 | — | 6 attempts — device wipes after 6 |
| Block Proximity Password Sharing | Device Security | Corporate | L1 | — | `allowpasswordproximityrequests=false` — supervised only |
| Block Password Sharing | Device Security | Corporate | L1 | — | `allowpasswordsharing=false` — supervised only |
| Require Touch ID / Face ID Before AutoFill | Device Security | Corporate | L1 | — | `forceauthenticationbeforeautofill=true` — supervised only |

#### Lock Screen

| Setting | Intune Policy | Enrollment Type | CIS - Level | MS - Level | Notes |
|---|---|---|---|---|---|
| Block Notification Center on Lock Screen | Lock Screen Corp / Device Restrictions BYOD | Corporate / BYOD | L1 | MS-L1 | `allowlockscreennotificationsview=false` |
| Block Control Center on Lock Screen | Lock Screen Corp / Device Restrictions BYOD | Corporate / BYOD | L1 | — | `allowlockscreencontrolcenter=false` — supervised-only; silently ignored on unsupervised BYOD |
| Block Today View on Lock Screen | Lock Screen Corporate | Corporate | L1 | — | `allowlockscreentodayview=false` — supervised only |
| "If Lost, Return to..." Message | Lock Screen Corporate | Corporate | L1 | — | Asset tag + IT contact footnote configured |

#### Data Protection (Document Management)

| Setting | Intune Policy | Enrollment Type | CIS - Level | MS - Level | Notes |
|---|---|---|---|---|---|
| Block Managed Docs in Unmanaged Apps | Data Protection | Corporate / BYOD | L1 | MS-L1 | `allowopenfrommanagedtounmanaged=false` |
| Block Unmanaged Docs in Managed Apps | Data Protection | Corporate / BYOD | L1 | MS-L1 | `allowopenfromunmanagedtomanaged=false` |
| AirDrop Restriction | Data Protection | Corporate / BYOD | L1 | MS-L1 | Corporate: `allowairdrop=false` (blocked entirely); BYOD: `forceairdropunmanaged=true` (unmanaged mode only) |
| Block Handoff | Accessibility & Sharing Corp / Device Restrictions BYOD | Corporate / BYOD | L1 | MS-L1 | `allowactivitycontinuation=false` |

#### iCloud

| Setting | Intune Policy | Enrollment Type | CIS - Level | MS - Level | Notes |
|---|---|---|---|---|---|
| Force Encrypted Backup | iCloud & Storage | Corporate / BYOD | L1 | MS-L1 | `forceencryptedbackup=true` |
| Block Managed Apps Cloud Sync | iCloud & Storage | Corporate / BYOD | L1 | MS-L1 | `allowmanagedappscloudsync=false` |
| Block iCloud Backup | iCloud & Storage | Corporate | L1 | MS-L2 | `allowcloudbackup=false` — supervised only |
| Block iCloud Documents & Data | iCloud & Storage | Corporate | L1 | MS-L2 | `allowclouddocumentsync=false` — supervised only |
| Block iCloud Keychain Sync | iCloud & Storage | Corporate | L1 | MS-L2 | `allowcloudkeychainsync=false` — CIS says "review"; baseline blocks |
| Block USB Drives in Files | iCloud & Storage | Corporate | L1 | — | `allowfilesusbdriveaccess=false` — supervised only |
| Block Network Drives in Files | iCloud & Storage | Corporate | L1 | — | `allowfilesnetworkdriveaccess=false` — supervised only |

#### Privacy & Telemetry

| Setting | Intune Policy | Enrollment Type | CIS - Level | MS - Level | Notes |
|---|---|---|---|---|---|
| Block Apple Personalized Advertising | Device Privacy | Corporate / BYOD | L1 | MS-L1 | `allowapplepersonalizedadvertising=false` |
| Block Diagnostic Data Submission | Device Privacy | Corporate / BYOD | L1 | MS-L1 | `allowdiagnosticsubmission=false` + Settings-level control |

#### Network & Connectivity

| Setting | Intune Policy | Enrollment Type | CIS - Level | MS - Level | Notes |
|---|---|---|---|---|---|
| Block Untrusted TLS Certificate Prompts | Connectivity Controls Corp / Device Restrictions BYOD | Corporate / BYOD | L1 | MS-L1 | `allowuntrustedtlsprompt=false` |
| VPN Configured | Defender for Endpoint | Corporate / BYOD | L1 | MS-L2 | Corporate: MDE Control Filter (supervised); BYOD: MDE Zero-Touch VPN |
| Block VPN Creation | Connectivity Controls | Corporate | L1 | MS-L2 | `allowvpncreation=false` — prevents custom VPN tunnels |
| Block Cellular Data Settings Modification | Connectivity Controls | Corporate | L1 | — | `allowappcellulardatamodification=false` — supervised only |
| Disable MAC Address Randomization | N/A — manual | Corporate / BYOD | L1 | — | Configure per corporate Wi-Fi network profile; not a Settings Catalog setting |

#### Device Management Controls

| Setting | Intune Policy | Enrollment Type | CIS - Level | MS - Level | Notes |
|---|---|---|---|---|---|
| Profile Removal = Always | N/A — enrollment | BYOD | L1 | — | Users can unenroll at any time — web enrollment by design |
| Profile Removal = Never | N/A — ADE | Corporate | L1 | — | ADE enrollment prevents profile removal by design |
| Block Erase All Content and Settings | Device Restrictions | Corporate | L1 | MS-L1 | `allowerasecontentandsettings=false` — supervised only |
| Block Enterprise App Trust | App Management | Corporate | L1 | MS-L1 | `allowenterpriseapptrust=false` — supervised only |
| Block Configuration Profile Installation | Device Restrictions | Corporate | L1 | MS-L1 | `allowuiconfigurationprofileinstallation=false` — supervised only |
| Force Automatic Date and Time | Not enforced | Corporate / BYOD | L1* | — | **Intentional deviation** — see Section 3.1 |
| Consent Message Configured | N/A — enrollment flow | BYOD | L1 | — | Handled by Intune enrollment experience |
| Notification Settings for Managed Apps | N/A — manual | Corporate / BYOD | L1 | — | App Protection Policy controls notification content; no Settings Catalog setting |

#### Device Pairing (Corporate — Supervised Only)

| Setting | Intune Policy | Enrollment Type | CIS - Level | MS - Level | Notes |
|---|---|---|---|---|---|
| Force Apple Watch Wrist Detection | Device Pairing Corp / Device Restrictions BYOD | Corporate / BYOD | L1 | — | `forcewatchwristdetection=true` — silently ignored on unsupervised BYOD |
| USB Restricted Mode | Device Pairing | Corporate | L1 | MS-L2 | `allowusbrestrictedmode=true` — blocks USB data accessories when locked |
| Block Proximity Setup to New Device | Device Pairing | Corporate | L1 | — | `allowproximitysetuptonewdevice=false` |

#### Safari Browser

| Setting | Intune Policy | Enrollment Type | CIS - Level | MS - Level | Notes |
|---|---|---|---|---|---|
| Force Fraud Warning | Safari Browser | Corporate / BYOD | L1 | MS-L1 | `safariforcefraudwarning=true` — enforced independently of user toggle |
| Managed Safari Web Domains | Not configured | Corporate / BYOD | L1 | — | Addressed via SSO extension (auth domains) and cross-site tracking relaxed domains |
| Block Moving Mail Account Messages | Not configured | Corporate / BYOD | L1 | — | Addressed via Outlook ACP and MAM for managed mail |

#### Apple Intelligence & Siri (Corporate — DDM/Supervised Only)

| Setting | Intune Policy | Enrollment Type | CIS - Level | MS - Level | Notes |
|---|---|---|---|---|---|
| Block Siri While Locked | AI & Siri Corp / Device Security BYOD | Corporate / BYOD | L1 | MS-L1 | `sirisettings_allowwhilelocked=false` |
| External Intelligence Disabled | AI & Siri Corporate | Corporate | L1 | — | `externalintelligencesettings_enabled=false`, `allowsignin=false` — DDM |
| Notes Summarization Disabled | AI & Siri Corporate | Corporate | L1 | — | `intelligencesettings_apps_notes_allowtranscription/allowtranscriptionsummary=false` — DDM |
| Mail Summarization Disabled | AI & Siri Corporate | Corporate | L1 | — | `intelligencesettings_apps_mail_allowsummary=false` — DDM |
| Writing Tools Disabled | AI & Siri Corporate | Corporate | L1* | — | **Intentional deviation** — Writing Tools kept enabled for productivity; see Section 3.1 |
| Apple Intelligence Controls (CIS 2.9.1–2.9.4) | Not applied — DDM gap | BYOD | L1 | — | All four controls unenforceable — DDM incompatible with unsupervised BYOD; see Section 5.7 and Section 3.1 |

---

### 3.3 CIS L2 Settings

All CIS Level 2 controls from the iOS/iPadOS 26 Benchmark v1.0.0. In this baseline, CIS L2 settings apply to Corporate (supervised) devices only. CIS L1 and L2 settings are combined in the same functional-area policies — there is no separate L2 policy set. Use this table to identify L2 settings if your organization needs to remove them from imported policies to target L1 alignment only.

| Setting | Intune Policy | Enrollment Type | CIS - Level | MS - Level | Notes |
|---|---|---|---|---|---|
| Passcode Expiration | Device Security | Corporate | L2 | MS-L2 | 365-day annual rotation |
| Passcode History | Device Security | Corporate | L2 | MS-L2 | Last 5 passcodes blocked |
| Block AirDrop | Data Protection | Corporate | L2 | MS-L2 | `allowairdrop=false` — stricter than CIS (which allows unmanaged mode) |
| Block AirPrint | Data Protection | Corporate | L2 | — | `allowairprint=false` |
| Require AirPlay Pairing Password | Data Protection | Corporate | L2 | — | `forceairplayoutgoingrequestspairingpassword=true` |
| Block Unmanaged Apps Reading Managed Contacts | Data Protection | Corporate | L2 | MS-L2 | `allowunmanagedtoreadmanagedcontacts=false` |
| Block Managed Apps Writing Unmanaged Contacts | Data Protection | Corporate | L2 | MS-L2 | `allowmanagedtowriteunmanagedcontacts=false` |
| Block Screen Capture | Data Protection | Corporate | L2 | MS-L2 | `allowscreenshot=false` — supervised only |
| Block iCloud Photo Library | iCloud & Storage | Corporate | L2 | — | `allowcloudphotolibrary=false` |
| Block iCloud Shared Photo Stream | iCloud & Storage | Corporate | L2 | — | `allowsharedstream=false` |
| Block Enterprise Book Backup | iCloud & Storage | Corporate | L2 | — | `allowenterprisebookbackup=false` |
| Block Enterprise Book Metadata Sync | iCloud & Storage | Corporate | L2 | — | `allowenterprisebookmetadatasync=false` |
| Block Account Settings Modification | Device Restrictions | Corporate | L2 | MS-L2 | `allowaccountmodification=false` |
| Block Device Name Modification | Device Restrictions | Corporate | L2 | — | `allowdevicenamemodification=false` |
| Block Screen Time / Restrictions Enabling | Device Restrictions | Corporate | L2 | — | `allowenablingrestrictions=false` |
| Block Notification Settings Modification | Device Restrictions | Corporate | L2 | MS-L2 | `allownotificationsmodification=false` |
| Block Personal Hotspot | Connectivity Controls | Corporate | L2 | — | `settings_item_personalhotspot_enabled=false` |
| Block Bluetooth Settings Modification | Connectivity Controls | Corporate | L2 | MS-L2 | `allowbluetoothmodification=false` |
| Block Cellular Plan Modification | Connectivity Controls | Corporate | L2 | — | `allowcellularplanmodification=false` |
| Block eSIM Modification | Connectivity Controls | Corporate | L2 | — | `allowesimmodification=false` |
| Block eSIM Outgoing Transfers | Connectivity Controls | Corporate | L2 | — | `allowesimoutgoingtransfers=false` |
| Block NFC | Connectivity Controls | Corporate | L2 | — | `allownfc=false` |
| Block Personal Hotspot Modification | Connectivity Controls | Corporate | L2 | — | `allowpersonalhotspotmodification=false` |
| Block App Clips | App Management | Corporate | L2 | — | `allowappclips=false` |
| Block App Installation | App Management | Corporate | L2 | MS-L2 | `allowappinstallation=false` — VPP only; ensure VPP catalog is ready |
| Block App Removal | App Management | Corporate | L2 | MS-L2 | `allowappremoval=false` |
| Block Automatic App Downloads | App Management | Corporate | L2 | — | `allowautomaticappdownloads=false` |
| Block App Store (UI) | App Management | Corporate | L2 | MS-L2 | `allowuiappinstallation=false` |
| Block Non-Configurator Host Pairing | Device Pairing | Corporate | L2 | MS-L2 | `allowhostpairing=false` |
| Block iPhone Mirroring | Accessibility & Sharing | Corporate | L2 | — | `allowiphonemirroring=false` |
| Block iPhone Widgets on Mac | Accessibility & Sharing | Corporate | L2 | — | `allowiphonewidgetsonmac=false` |
| Block Remote Screen Observation | Accessibility & Sharing | Corporate | L2 | — | `allowremotescreenobservation=false` |
| Block Private Browsing (Safari) | Safari Browser | Corporate | L2 | — | `safari_allowprivatebrowsing=false` — supervised only |
| Block Third-Party Cookies (Safari) | Safari Browser | Corporate / BYOD | L2 | — | `safari_acceptcookies=1` |
| Block Safari History Clearing | Safari Browser | Corporate | L2 | — | `allowsafarihistoryclearing=false` |
| Block Mail Drop | Not configured | Corporate | L2 | — | Not applied — governed by Outlook ACP + MAM policies |
| Block Apple Intelligence Report | AI & Siri Corporate | Corporate | L2 | — | `intelligencesettings_allowappleintelligencereport=false` — DDM |
| Block Personalized Handwriting Results | AI & Siri Corporate | Corporate | L2 | — | `intelligencesettings_allowpersonalizedhandwritingresults=false` — DDM |
| Block Image Playground | AI & Siri Corporate | Corporate | L2 | — | `intelligencesettings_allowimageplayground=false` — DDM |
| Block Image Wand | AI & Siri Corporate | Corporate | L2 | — | `intelligencesettings_allowimagewand=false` — DDM |

---

### 3.4 Microsoft Framework Settings (Non-CIS)

Settings from the Microsoft iOS/iPadOS Security Configuration Framework that are **not in the CIS L1/L2 profile**. These are the settings where Microsoft goes beyond CIS — they add security but are not CIS requirements.

| Setting | Intune Policy | Enrollment Type | CIS - Level | MS - Level | Notes |
|---|---|---|---|---|---|
| Microsoft Enterprise SSO Extension | Microsoft Enterprise SSO | Corporate / BYOD | — | MS-L1 | Required before Conditional Access; enables SSO for all Entra ID-integrated apps |
| Block Password AutoFill | Device Security | Corporate | — | MS-L1 | `allowpasswordautofill=false` — CIS does not cover this separately |
| Conditional Access Policies | Conditional Access | Corporate / BYOD | — | MS-L1 | CIS requires VPN but does not define Conditional Access enforcement |
| App Protection Policy — all L1 settings | App Protection MAM | BYOD | — | MS-L1 | CIS does not address App Protection Policies; all settings from Microsoft framework |
| App Protection Policy — all L2 settings | App Protection MAM | BYOD | — | MS-L2 | MAM L2: third-party keyboards blocked, org data notifications blocked, sync blocked |
| Defender for Endpoint deployment | Defender VPN BYOD / Control Filter Corporate | Corporate / BYOD | — | MS-L2 | CIS requires VPN (Section 2.6.1 / 3.6.1) but does not specify MDE |
| iCloud Private Relay Blocked | Data Protection | Corporate / BYOD | CIS Additional | MS-L1 | CIS Section 4 "Additional Recommendation" — not in L1/L2 profile; prevents bypassing network monitoring |
| Mail Privacy Protection Enabled | Device Privacy | Corporate / BYOD | CIS Additional | MS-L1 | CIS Section 4 "Additional Recommendation" — not in L1/L2 profile; blocks email tracking pixels |
| Force Wi-Fi Always On | Connectivity Controls | Corporate | — | MS-L3 | `forcewifipoweron=true` — supervised only; high-security environments |

---

### 3.5 ADM-EXP Settings — Admin Experience

**ADM-EXP — Admin Experience.** Settings not required by CIS or the Microsoft Framework, configured based on professional admin deployment experience. These are the "wiggle room" settings — they can be modified or removed without breaking CIS or Microsoft alignment. When an admin sees `ADM-EXP` in the Level / Source column, the setting is fully adjustable.

| Setting | Intune Policy | Enrollment Type | CIS - Level | MS - Level | Notes |
|---|---|---|---|---|---|
| Activation Lock Disabled | Device Restrictions | Corporate | — | — | IT can reset/reassign without Apple ID credentials |
| Idle Reboot Allowed | Device Restrictions | Corporate | — | — | MDM-triggered reboot on idle supervised devices |
| Block Default Browser Modification | Device Restrictions | Corporate | — | — | Prevents users from changing from Microsoft Edge as default |
| Block Call Recording | Data Protection | Corporate | — | — | Prevents call recording on supervised devices |
| Block Specific Apps — 13 Apple Apps | App Management | Corporate | — | — | Games, Tips, Home, Freeform, Journal, Mail, TV, Books, Bridge, Fitness, Music, Stocks, Health |
| Disable Temporary Audio Accessory Pairing | Device Pairing | Corporate | — | — | Audio accessories require managed pairing; 12-hour auto-disconnect |
| Allow Paired Apple Watch | Device Pairing | Corporate | — | — | Apple Watch pairing explicitly permitted |
| Force Apple Watch Wrist Detection | Device Pairing | Corporate | — | — | Apple Watch locks when removed from wrist |
| Asset Tag / Lock Screen Footnote | Lock Screen | Corporate | — | — | Organization asset tag and IT contact; replace REPLACE placeholders before deploying |
| Allow JavaScript (Safari) | Safari Browser | Corporate | — | — | JavaScript permitted; blocking would break most web applications |
| Block Safari AI Summary | Safari Browser | Corporate | — | — | Blocks iOS 26 Safari-generated AI page summaries; not in CIS |
| Safari Start Page = Microsoft 365 | Safari Browser | Corporate | — | — | Sets https://m365.cloud.microsoft/ as the Safari new tab / start page |
| Cross-Site Tracking Relaxed Domains | Safari Browser | Corporate / BYOD | — | — | Microsoft auth domains exempted (microsoft.com, login.microsoftonline.com, manage.microsoft.com, sts.windows.net) |
| Show Update Notifications | Software Update | Corporate | — | — | Notifies users when updates are available |
| Allowed Workspace IDs (External Intelligence) | AI & Siri Corporate | Corporate | — | — | REPLACE placeholder; restrict to approved enterprise AI workspace, or remove if not using |
| Block Genmoji | AI & Siri Corporate | Corporate | — | — | AI-generated emoji blocked; consistent with baseline AI control stance; not in CIS |
| Block Mail Smart Replies | AI & Siri Corporate | Corporate | — | — | AI-generated Smart Reply suggestions in Mail; not in CIS |
| Block Safari AI Summary (Intelligence Settings) | AI & Siri Corporate | Corporate | — | — | Defense in depth alongside `safari_allowsummary=false` in Safari Browser policy |
| Block Siri User-Generated Content | AI & Siri Corporate | Corporate | — | — | Siri is fully disabled; this setting has no additional functional effect |
| Disable Siri Entirely | AI & Siri Corporate | Corporate | — | — | CIS only requires blocking while locked; full disable is an additional hardening choice |
| Force Siri Profanity Filter | AI & Siri Corporate | Corporate | — | — | Siri is fully disabled; no functional effect; defense-in-depth measure |
| Block Alternative Marketplace App Installation | Web-App-Store (EU) | Corporate | — | — | EU DMA compliance; no effect outside EU regions |
| Block Web Distribution App Installation | Web-App-Store (EU) | Corporate | — | — | EU DMA compliance; no effect outside EU regions |
| Allow Auto Unlock (Apple Watch) | Device Restrictions | BYOD | — | — | Explicitly permitted on personal devices |
| Allow Biometric (Face ID / Touch ID) Unlock | Device Restrictions | BYOD | — | — | Explicitly permitted; CIS does not restrict biometric unlock |
| Allow Disabling Fraud Warning | Safari Browser | BYOD | — | — | Personal Safari behavior on personal device not restricted; `safariforcefraudwarning` MDM key enforced separately |

---

## 4. Corporate Device Policies — Settings Catalog

Corporate devices are enrolled via Apple Automated Device Enrollment (ADE) and are supervised, providing the broadest set of available MDM restrictions. Settings marked as supervised-only apply exclusively to this device track.

### 4.1 Device Security — Corporate Devices

Passcode enforcement, auto-lock, and authentication controls. Intune uses DDM for passcode policies on iOS/iPadOS 15+ — users receive a 60-minute grace period to set a compliant passcode after policy assignment.

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Require Passcode | Enabled | L1 | Enables hardware encryption; required for all MDM management |
| Block Simple Passcodes | Blocked | L1 | Prevents sequential and repeating patterns (e.g., 123456, 111111) |
| Minimum Passcode Length | 6 | L1 | Base brute-force resistance |
| Maximum Auto-Lock | 1 minute | L1 | Minimizes the window of unauthorized access when unattended |
| Grace Period Before Lock | Immediately | L1 | No delay before passcode required after screen lock (CIS iOS 26) |
| Maximum Failed Attempts | 5 | L1 | Device wipes after 5 failed attempts |
| Passcode Expiration | 365 days | L2 | Annual passcode rotation |
| Passcode History | 5 | L2 | Prevents reuse of the last 5 passcodes |
| Require Alphanumeric Passcode | Not Required (Numeric Allowed) | ADM-EXP | Numeric passcode accepted on corporate devices; alphanumeric requirement removed to reduce enrollment friction |
| Block Password AutoFill | Blocked | L1 | Prevents Keychain and credential providers from auto-populating passwords |
| Block Password Proximity Requests | Blocked | L1 | Prevents iOS from suggesting nearby device passwords via AirDrop |
| Block Password Sharing | Blocked | L1 | Prevents AirDrop-style password sharing between Apple devices |
| Require Face ID / Touch ID Before AutoFill | Required | L1 | Biometric authentication required before AutoFill populates credentials or card data |

> **Note:** All passcode settings are deployed in a single flat policy. Devices assigned to this policy receive all settings, including those the CIS Benchmark documents as L1 and L2.

---

### 4.2 Data Protection — Corporate Devices

Controls data transfer between managed and unmanaged contexts: AirDrop, AirPrint, open-in restrictions, screen capture, and network relay.

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Block AirDrop | Blocked | L2 | Prevents file transfer to unknown devices via proximity AirDrop |
| Block AirPrint | Blocked | L2 | Prevents printing to unsecured printers |
| Block AirPrint Credentials in Keychain | Blocked | L1 | Printer credentials not stored on device |
| Block AirPrint iBeacon Discovery | Blocked | L1 | Prevents iBeacon-based AirPrint printer discovery |
| Block Call Recording | Blocked | ADM-EXP | Prevents call recording on supervised devices |
| Block iCloud Private Relay | Blocked | CIS Additional + MS-L1 | Disables iCloud Private Relay — prevents bypassing network monitoring and content filtering. CIS Section 4.5 "Additional Recommendation" (not in L1/L2 profile); also Microsoft Framework aligned. |
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

| Setting | Configured Value | Level / Source | Notes |
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

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Activation Lock Allowed While Supervised | Disabled | ADM-EXP | Activation Lock disabled on supervised devices; IT can reset/reassign without Apple ID credentials |
| Idle Reboot Allowed | Enabled | ADM-EXP | Allows MDM-triggered reboot on idle supervised devices |
| Block Account Settings Modification | Blocked | L2 | Prevents adding or changing accounts (email, Apple ID) outside IT control |
| Block Default Browser Modification | Blocked | ADM-EXP | Prevents users from changing the default browser |
| Block Device Name Modification | Blocked | L2 | Maintains asset naming consistency |
| Block Enabling Screen Time Restrictions | Blocked | L2 | Prevents users from enabling Screen Time/parental controls |
| Block Erase All Content and Settings | Blocked | L1 | Prevents unauthorized factory reset of corporate device |
| Block Notification Settings Modification | Blocked | L2 | Users cannot change per-app notification behavior |
| Block Configuration Profile Installation | Blocked | L1 | Prevents users from installing unauthorized MDM or config profiles (CIS iOS 26) |
| Force Automatic Date and Time | Not Enforced | ADM-EXP | Automatic date/time enforcement removed; users may set manually |

> **MDM Options:** The Activation Lock and Idle Reboot settings (`settings_item_mdmoptions`) are in this policy only. Do not add a second `settings_item_mdmoptions` group wrapper to any other policy assigned to the same devices — Intune treats the group container as the conflict unit and reports a conflict.

---

### 4.5 Connectivity Controls — Corporate Devices

Controls user ability to modify connectivity settings. All settings in this policy require device supervision.

| Setting | Configured Value | Level / Source | Notes |
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

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Block App Clips | Blocked | L2 | Prevents App Clip experiences triggered by QR codes or NFC tags |
| Block App Installation | Blocked | L2 | All app installation via MDM/VPP only |
| Block App Removal | Blocked | L2 | Required managed apps cannot be uninstalled |
| Block Automatic App Downloads | Blocked | L2 | IT controls when apps are installed or updated |
| Block Enterprise App Trust | Blocked | L1 | Prevents installation of unauthorized enterprise-signed apps |
| Block In-App Purchases | Blocked | L1 | Prevents unauthorized charges through managed apps |
| Block System App Removal | Blocked | L1 | Prevents removal of pre-installed Apple system apps |
| Block App Store (UI) | Blocked | L2 | Prevents browsing and installing from the App Store UI |
| Block Specific Apps (Bundle IDs) | Blocked | ADM-EXP | Blocks com.apple.games, com.apple.tips, com.apple.Home, com.apple.freeform, com.apple.journal, com.apple.mobilemail, com.apple.tv, com.apple.iBooks, com.apple.Bridge, com.apple.Fitness, com.apple.Music, com.apple.stocks, com.apple.Health |

> **Before deploying:** Ensure a complete VPP app catalog is ready before enforcing App Installation and App Store blocks. Users cannot install anything — including Line of Business apps — unless the app is pushed via Intune.

---

### 4.7 Device Pairing — Corporate Devices

Controls USB and wireless pairing with other devices and accessories.

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Disable Temporary Audio Accessory Pairing | Enabled | ADM-EXP | Audio accessories require managed pairing; temporary sessions disabled |
| Audio Accessory Auto-Unpair Time | 12 hours | ADM-EXP | Paired audio accessories auto-disconnect after 12 hours |
| Allow Paired Apple Watch | Allowed | ADM-EXP | Apple Watch pairing is permitted on corporate devices |
| Force Apple Watch Wrist Detection | Enabled | ADM-EXP | Apple Watch locks when removed from wrist |
| Block Non-Configurator Host Pairing | Blocked | L2 | Prevents USB pairing with unauthorized computers |
| Block Proximity Setup to New Device | Blocked | L1 | Prevents Quick Start device-to-device migration via proximity |
| USB Restricted Mode | Enabled | L1 | Blocks USB accessories (data transfer tools) after device is locked |

---

### 4.8 Accessibility & Sharing — Corporate Devices

Controls Apple ecosystem sharing and remote access features that could expose corporate content to non-managed devices.

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Block Handoff (Activity Continuation) | Blocked | L1 | Prevents tasks from continuing on a personal Mac or iPad |
| Block iPhone Mirroring | Blocked | L2 | Prevents iPhone screen from being mirrored and controlled on a Mac |
| Block iPhone Widgets on Mac | Blocked | L2 | Prevents iPhone app widgets from appearing on a Mac |
| Block Remote Screen Observation | Blocked | L2 | Prevents remote viewing of the device screen |

> **CIS iOS 26:** "Block Handoff" promoted to L1 for corporate devices.

---

### 4.9 Apple Services — Corporate Devices

Restricts Apple consumer services on supervised corporate devices. All settings require device supervision.

| Setting | Configured Value | Level / Source | Notes |
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

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Block App Analytics Sharing (Settings) | Blocked | L1 | Blocks the app analytics toggle in Settings; prevents usage data shared with developers |
| Block Diagnostic Data Submission (Settings) | Blocked | L1 | Blocks the diagnostic submission toggle in Settings |
| Block Apple Personalized Advertising | Blocked | L1 | Opts out of Apple's personalized ad targeting |
| Block Diagnostic Submission (MDM key) | Blocked | L1 | MDM-level enforcement of diagnostic submission block (defense in depth) |
| Block Diagnostic Submission Modification | Blocked | L1 | Prevents users from re-enabling diagnostic submission |
| Enable Mail Privacy Protection | Enabled | CIS Additional | Blocks tracking pixels in email; hides IP address from senders. CIS Section 4.6 "Additional Recommendation" — not in L1/L2 profile. |
| Force Limit Ad Tracking | Enabled | L1 | Prevents apps from using the Advertising Identifier (IDFA) for tracking |
| Block Spotlight Internet Results | Blocked | L2 | Prevents Spotlight from sending search queries to Apple and third-party providers |

> **Dual control:** App Analytics and Diagnostic Submission are configured via both Settings-level keys (`settings_item_` prefix) and MDM restriction keys (`com.apple.applicationaccess_` prefix) for defense in depth. The Settings-level control blocks the toggle in the Settings app; the MDM key enforces the restriction regardless.

---

### 4.11 Lock Screen — Corporate Devices

Controls lock screen access and configures the asset tag and IT contact information displayed when the device is locked.

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Block Control Center on Lock Screen | Blocked | L1 | Prevents hardware toggles (Airplane Mode, Bluetooth, Wi-Fi) from the lock screen |
| Block Notification Center on Lock Screen | Blocked | L1 | Hides notification content when device is locked |
| Block Today View on Lock Screen | Blocked | L1 | Hides widget content (calendar, reminders) from lock screen |
| Block Wallet on Lock Screen | Blocked | L1 | Prevents Apple Pay and wallet cards without device unlock |
| Asset Tag Information | REPLACE | ADM-EXP | Displays organization asset tag on lock screen — replace placeholder before deploying |
| Lock Screen Footnote | REPLACE | ADM-EXP | Displays IT contact number on lock screen — replace placeholder before deploying |

> **Before deploying:** Replace both REPLACE: placeholder values with your organization's actual asset tag format and IT contact number. The placeholders will display literally on device lock screens if not updated.
>
> **CIS iOS 26:** "Block Control Center on Lock Screen" promoted to L1 for Corporate devices.

---

### 4.12 Safari Browser — Corporate Devices

Safari security controls for supervised corporate devices. `Safari Allow AutoFill` and `Allow Private Browsing` require device supervision and cannot be enforced on unsupervised BYOD devices.

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Block Third-Party Cookies | Blocked | L2 | Blocks cross-site tracking cookies; may affect some web applications |
| Block Disabling Fraud Warning | Blocked | L1 | Prevents users from disabling Safari phishing protection |
| Allow JavaScript | Allowed | ADM-EXP | JavaScript is permitted; blocking would break most web applications |
| Block Pop-ups | Blocked | L1 | Blocks malicious pop-ups and redirects |
| Block Private Browsing | Blocked | L2 | Removes the Private Tab option in Safari (supervised only) |
| Block Safari AI Summary | Blocked | ADM-EXP | Blocks Safari-generated AI page summaries (iOS 26) |
| Safari Start Page | https://m365.cloud.microsoft/ | ADM-EXP | Sets Microsoft 365 as the Safari new tab/start page |
| Cross-Site Tracking Relaxed Domains | microsoft.com, login.microsoftonline.com, manage.microsoft.com, sts.windows.net | ADM-EXP | Microsoft authentication domains exempted from cross-site tracking prevention |
| Block Safari History Clearing | Blocked | L2 | Prevents users from clearing Safari browsing history |
| Block Safari AutoFill | Blocked | L1 | Prevents automatic password and form filling (supervised only) |
| Force Fraud Warning | Enabled | L1 | Enforces Safari phishing protection; cannot be disabled by user |

---

### 4.13 Software Update — Corporate Devices

DDM-based software update enforcement for supervised corporate devices. Requires iOS/iPadOS 17.0+ and ADE.

> **iOS 26 mandatory DDM:** Apple deprecated legacy MDM software update commands in iOS/iPadOS 26. Supervised devices on iOS 26+ only respond to DDM update policies. Legacy policies under **Devices > Update policies for iOS/iPadOS** are silently ignored on iOS 26+ devices. Configure updates exclusively through the Settings Catalog DDM path.

| Setting | Configured Value | Level / Source | Notes |
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
| Show Update Notifications | Enabled | ADM-EXP | Notifies users when updates are available |
| Recommended Update Cadence | Monthly | ADM-EXP | Major release cadence recommendation |

> **Deprecated settings:** The "Allow Background Security Improvement Installation (Deprecated)" and "Allow Background Security Improvement Removal (Deprecated)" settings visible in the Intune Settings Catalog are legacy RSR restriction keys. Do not use these — enforcement is handled via the `softwareupdate_automaticactions_installsecurityupdate` (AlwaysOn) setting above.

> **BYOD update enforcement:** Software update enforcement via Settings Catalog is not available on unsupervised BYOD devices. Use the Compliance Policy minimum OS version (Section 7.2) combined with Conditional Access to block access from out-of-date personal devices.

---

### 4.14 Apple Intelligence & Siri — Corporate Devices

Controls Apple Intelligence on-device AI features, external AI integration (ChatGPT), and Siri behavior on corporate devices. Apple Intelligence settings are not applied to BYOD devices — see Section 5.7 for the reason.

> **DDM delivery:** All settings in this section are delivered via **Declarative Device Management (DDM)** under the Intune Settings Catalog DDM category. They apply **only to supervised ADE/Corporate devices** and produce errors in Intune when assigned to unsupervised BYOD devices.

**External Intelligence (ChatGPT / Third-Party AI)**

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Block External AI Integration | Blocked | CIS L1 Section 3.10.1 | Disables ChatGPT and third-party AI extensions at the OS level |
| Block Sign-In to External AI Services | Blocked | CIS L1 Section 3.10.1 | Prevents sign-in to ChatGPT or other external AI via Apple Intelligence |
| Allowed Workspace IDs | REPLACE | ADM-EXP | **REPLACE** with real Entra tenant GUID or approved workspace ID before deploying — or remove if not restricting External Intelligence to specific approved services. Note: `Enabled=false` already blocks all External Intelligence regardless of this value. |

**Intelligence Settings (On-Device Apple Intelligence)**

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Block Visual Intelligence Summary | Blocked | CIS L1 Section 3.10.1 | Prevents camera-based AI from analyzing surroundings or documents |
| Force On-Device Only Dictation | Enabled | CIS L1 Section 3.10.1 | Voice input processed locally; no audio sent to Apple servers — supervised only |
| Force On-Device Only Translation | Enabled | CIS L1 Section 3.10.1 | Translation processed locally; no text sent to Apple servers — supervised only |
| Block Notes Transcription | Blocked | CIS L1 Section 3.10.2 | Prevents Notes app from transcribing audio recordings |
| Block Notes Transcription Summary | Blocked | CIS L1 Section 3.10.2 | Prevents AI-generated summaries of Notes audio transcriptions |
| Block Mail Summarization | Blocked | CIS L1 Section 3.10.3 | Prevents AI-generated email summaries in Mail app |
| Block Mail Smart Replies | Blocked | ADM-EXP | Blocks AI-generated Smart Reply suggestions in Mail — consistent with baseline AI control stance |
| Block Safari AI Summary (Intelligence) | Blocked | ADM-EXP | Defense in depth alongside `safari_allowsummary=false` in Safari Browser policy |
| Block Apple Intelligence Report | Blocked | CIS L2 | Blocks diagnostic telemetry about Apple Intelligence from being sent to Apple |
| Block Personalized Handwriting Results | Blocked | CIS L2 | Prevents handwriting samples from being used for model training |
| Block Image Playground | Blocked | CIS L2 | Disables AI image generation on corporate devices |
| Block Image Wand | Blocked | CIS L2 | Disables sketch-to-image (Image Wand) on corporate devices |
| Block Genmoji | Blocked | ADM-EXP | Disables AI-generated emoji on corporate devices — not in CIS |
| Allow Writing Tools | Allowed | CIS Section 3.10.4* | AI text editing (rewrite, proofread, summarize) permitted — intentional deviation from CIS Section 3.10.4 L1 which requires disabling. See Section 3.1 Deviations Registry. |

> **\* Intentional deviation from CIS.** Writing Tools is kept enabled as a productivity feature. CIS Section 3.10.4 requires it to be disabled. See Section 3.1 for justification and risk assessment.

> **Before deploying:** Replace the `Allowed Workspace IDs` placeholder value with your organization's actual Entra tenant GUID or approved AI workspace ID — or remove that setting from the policy if you are not restricting External Intelligence to specific approved workspaces. The placeholder as-is will not block the policy import, but it will appear as an unconfigured placeholder in Intune.

**Siri Settings**

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Block Siri While Locked | Blocked | CIS L1 Section 3.2.1.2 | Prevents voice-activated access when screen is locked |
| Block Siri User-Generated Content | Blocked | ADM-EXP | Prevents Siri interaction data from being sent to Apple — Siri fully disabled; no additional effect |
| Disable Siri | Blocked | ADM-EXP | Siri disabled entirely — CIS only requires blocking while locked; full disable is a hardening choice |
| Force Profanity Filter | Enabled | ADM-EXP | Forces Siri profanity filter — Operational; Siri is fully disabled so this has no functional effect; defense-in-depth |

---

### 4.15 Web-App-Store (EU) — Corporate Devices

EU Digital Markets Act (DMA) compliance controls. Prevents installation of apps from sources other than the Apple App Store on supervised corporate devices.

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Block Alternative Marketplace App Installation | Blocked | ADM-EXP | Prevents installation from third-party app marketplaces (EU DMA) |
| Block Web Distribution App Installation | Blocked | ADM-EXP | Prevents installation of apps distributed directly via websites (EU DMA) |

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

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Require Passcode | Enabled | L1 | Required for enrollment; most users already have a passcode |
| Block Simple Passcodes | Blocked | L1 | Prevents predictable patterns (sequential or repeating digits) |
| Minimum Passcode Length | 6 | L1 | Base brute-force resistance |
| Maximum Auto-Lock | 5 minutes | L1 | Slightly more lenient than Corporate (2 min) to reduce BYOD friction |
| Grace Period Before Lock | Immediately | L1 | No delay before passcode required after screen lock (CIS iOS 26) |
| Maximum Failed Attempts | 6 | L1 | Wipe after 6 failed attempts (1 additional vs. Corporate) |
| Passcode Expiration | 365 days | L2 | Annual rotation; lower friction appropriate for a personal device |
| Passcode History | 3 | L2 | Prevents reuse of last 3 passcodes |
| Numeric Passcode Allowed | Allowed | ADM-EXP | Alphanumeric not required on personal devices to reduce enrollment friction |

> **Auto-lock deviation:** BYOD uses 5-minute auto-lock (Corporate: 2 minutes). The Compliance Policy enforces the lock-screen requirement independently. 5 minutes provides comparable security with better usability for users reading, watching video, or on calls on a personal device.

---

### 5.2 Data Protection — BYOD Devices

Controls data transfer between managed (corporate) and unmanaged (personal) contexts. Scoped to protecting corporate data within managed apps — personal app behavior is not restricted.

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Block Managed Docs in Unmanaged Apps | Blocked | L1 | Corporate files cannot be opened in personal apps |
| Block Unmanaged Docs in Managed Apps | Blocked | L1 | Personal files cannot enter managed app space |
| Block Managed Apps Writing Unmanaged Contacts | Blocked | L2 | Prevents managed apps from writing contacts to personal Contacts |
| Block Unmanaged Apps Reading Managed Contacts | Blocked | L2 | Prevents personal apps from reading work contacts |
| Block Screen Capture | Blocked | L2 | Prevents screenshots of managed app content |
| Block iCloud Private Relay | Blocked | CIS Additional | Disables iCloud Private Relay on the enrolled device. CIS Section 4.5 "Additional Recommendation" — not in L1/L2 profile. |
| Force AirDrop Unmanaged Mode | Enabled | L2 | Files sent via AirDrop from managed apps are treated as unmanaged |
| Require AirPlay Pairing Password | Required | L2 | Password required before AirPlay connections |

> **AirDrop design decision:** AirDrop is not blocked entirely on BYOD. `forceairdropunmanaged` ensures files from managed apps are treated as unmanaged, preventing corporate data from entering managed apps via AirDrop. A full device-level block was excluded from this baseline — personal AirDrop use is a legitimate feature on a personal device.

---

### 5.3 iCloud & Storage — BYOD Devices

Scoped iCloud controls for personal devices. Only managed app data is restricted — personal iCloud sync for personal content is unaffected.

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Force Encrypted Backup | Enabled | L1 | Device backups are encrypted with the device passcode |
| Block Managed Apps Cloud Sync | Blocked | L1 | Prevents corporate app data from syncing to personal iCloud |

> **iCloud Keychain not blocked:** iCloud Keychain is not restricted on BYOD. App Protection Policies already prevent corporate credentials from syncing to personal iCloud. Blocking Keychain on a personal device restricts personal banking, shopping, and family password management without meaningful security gain.

---

### 5.4 Device Restrictions — BYOD Devices

Lock screen access controls and behavioral restrictions for personal enrolled devices.

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Block Notification Center on Lock Screen | Blocked | L1 | Hides notification content when device is locked |
| Block Control Center on Lock Screen | Blocked | L1 | Prevents hardware toggles from the lock screen (note: requires supervision; silently ignored on unsupervised BYOD) |
| Block Lock Screen Today View | Blocked | L1 | Hides widget content (calendar, reminders) from lock screen |
| Block Wallet on Lock Screen | Blocked | L1 | Prevents Apple Pay and wallet cards without device unlock |
| Allow Auto Unlock | Allowed | ADM-EXP | Apple Watch can auto-unlock device |
| Allow Biometric (Face ID / Touch ID) Unlock | Allowed | ADM-EXP | Biometric unlock explicitly permitted |
| Block Untrusted TLS Certificate Prompts | Blocked | L1 | Prevents accepting invalid or self-signed TLS certificates |
| Block Handoff (Activity Continuation) | Blocked | L1 | Prevents corporate app tasks from continuing on a personal Mac or iPad |
| Force Apple Watch Wrist Detection | Enabled | ADM-EXP | Apple Watch locks when removed from wrist |

> **Supervision note:** "Block Control Center on Lock Screen" requires device supervision and will be silently ignored on unsupervised BYOD devices. It is included to cover any supervised BYOD configurations. For standard unsupervised BYOD, App Protection Policies control managed app lock behavior.

---

### 5.5 Device Privacy — BYOD Devices

Privacy controls for personal devices. Scoped to advertising tracking and diagnostic submission. Spotlight internet results are not blocked on BYOD — personal web search is a personal device feature.

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Block App Analytics Sharing (Settings) | Blocked | L1 | Blocks app analytics toggle in Settings |
| Block Diagnostic Data Submission (Settings) | Blocked | L1 | Blocks diagnostic submission toggle in Settings |
| Block Apple Personalized Advertising | Blocked | L1 | Opts out of Apple personalized ad targeting |
| Block Diagnostic Submission (MDM key) | Blocked | L1 | MDM-level enforcement (defense in depth) |
| Enable Mail Privacy Protection | Enabled | CIS Additional | Blocks tracking pixels in email. CIS Section 4.6 "Additional Recommendation" — not in L1/L2 profile. |
| Force Limit Ad Tracking | Enabled | L1 | Prevents IDFA-based advertising tracking |

---

### 5.6 Safari Browser — BYOD Devices

Safari security controls for unsupervised personal devices. AutoFill blocking and Private Browsing blocking require device supervision and cannot be enforced on BYOD.

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Block Third-Party Cookies | Blocked | L2 | Reduces cross-site tracking; may affect some websites |
| Allow Disabling Fraud Warning | Allowed | ADM-EXP | BYOD users may disable Safari fraud warning on personal device; `safariforcefraudwarning` (MDM key) remains enabled as a separate enforcement layer |
| Cross-Site Tracking Relaxed Domains | microsoft.com, login.microsoftonline.com, manage.microsoft.com, sts.windows.net | ADM-EXP | Microsoft authentication domains exempted from cross-site tracking prevention |
| Force Fraud Warning | Enabled | L1 | Safari phishing protection enforced via MDM key; cannot be disabled by policy |

> **AutoFill not enforceable on BYOD:** `safariallowautofill` requires device supervision as of iOS 13 and cannot be enforced on personal enrolled devices via MDM.

> **Fraud warning deviation:** `allowdisablingfraudwarning` is set to `true` (allow) on BYOD, permitting users to turn off the Safari fraud warning toggle if they choose. The MDM-level `safariforcefraudwarning` key remains `true`, providing an independent enforcement layer. Restricting personal Safari behavior on a personal device is disproportionate — pop-up blocking has also been removed from this policy for the same reason.

---

### 5.7 Apple Intelligence & Siri — BYOD (DDM Gap)

No Apple Intelligence policy is deployed to BYOD devices in this baseline.

**CIS Benchmark requirement:** The CIS iOS/iPadOS 26 Benchmark v1.0.0 includes the following Level 1 controls for End-User Owned (BYOD) devices under Section 2.9:

| CIS Control | Section Reference | CIS Requirement | Status |
|---|---|---|---|
| External Intelligence Extensions Disabled | Section 2.9.1 | Disabled — L1 BYOD | Not implemented — DDM incompatible |
| Notes Summarization Disabled | Section 2.9.2 | Disabled — L1 BYOD | Not implemented — DDM incompatible |
| Mail Summarization Disabled | Section 2.9.3 | Disabled — L1 BYOD | Not implemented — DDM incompatible |
| Writing Tools Disabled | Section 2.9.4 | Disabled — L1 BYOD | Not implemented — DDM incompatible (also intentional deviation on Corporate) |

**Root cause — DDM migration:** Microsoft migrated all Apple Intelligence settings in the Intune Settings Catalog from the Device Restrictions Configuration category to **Declarative Device Management (DDM)**. DDM is Apple's modern, autonomous management protocol designed for supervised (ADE/ABM) devices. When the same DDM-based settings are assigned to unsupervised BYOD devices, Intune returns an error and the settings fail to apply.

**Why this cannot be worked around:** There is no legacy MDM equivalent for the new DDM-delivered Apple Intelligence controls. Custom Configuration Profiles using `com.apple.applicationaccess` keys (`allowNotesTranscription`, `allowMailSummary`, etc.) are the Apple-documented path, but Intune delivers these as DDM declarations that are incompatible with unsupervised enrollment. No supported MDM-only path exists at this time.

**Compensating controls in place:**
- **MAM App Protection Policy** — prevents corporate data from being processed by Apple AI features within managed Microsoft 365 apps (Outlook, Teams, Edge, OneDrive, etc.) on BYOD devices
- **Conditional Access + Compliance Policy** — ensures BYOD devices meet minimum OS and security requirements before accessing corporate resources
- **Scope limitation** — personal Notes and Mail content on a personal device is outside the appropriate scope of BYOD MDM management; the corporate data risk is addressed at the app layer via MAM

**Recommended action:** Monitor [Microsoft Intune release notes](https://learn.microsoft.com/en-us/mem/intune/fundamentals/whats-new) for support of DDM Apple Intelligence settings on unsupervised enrolled devices. If a supported path becomes available, create a BYOD Apple Intelligence policy using the same settingDefinitionIds confirmed for the Corporate policy and validate on a pilot group before broad assignment.

This gap is documented in Section 3.1 (Deviations Registry) and in SETTINGSOUTPUT.md (CIS Compliance Gaps section).

---

## 6. App Protection Policy Recommendations (MAM)

App Protection Policies (MAM) enforce data boundaries within managed apps on both enrolled and unenrolled devices. Unlike Settings Catalog policies, which configure device-level MDM restrictions, App Protection Policies operate at the app layer — they apply to Microsoft 365 apps (Outlook, Teams, OneDrive, Edge) regardless of whether the device is enrolled in Intune MDM.

CIS does not define App Protection Policy recommendations. The settings in this section are drawn from the Microsoft iOS/iPadOS Security Configuration Framework, which defines two APP tiers: **Level 1 (Enterprise Basic Security)** and **Level 2 (Enterprise Enhanced Security)**. This baseline combines both tiers into a single BYOD policy — all settings in this section apply to the combined policy.

> **Scope:** The combined App Protection Policy in this baseline targets BYOD users. Corporate supervised devices receive device-level data protection controls through Settings Catalog policies (Sections 4 and 5). App Protection Policies provide an additional layer for managed apps on corporate devices, but their primary role in this baseline is BYOD data containment.

> **CIS coverage:** App Protection Policies are not addressed in the CIS Apple iOS/iPadOS Benchmark. The "Microsoft Level" column in this section references the Microsoft iOS/iPadOS Security Configuration Framework tiers only.

---

### 6.1 Data Transfer and Protection

Controls how managed app data can move between apps and storage locations.

| Setting | Recommended Value | Level / Source | Notes |
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

| Setting | Recommended Value | Level / Source | Notes |
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

| Setting | Recommended Value | Level / Source | Notes |
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
| Require Managed Email Profile | `Not configured` | ADM-EXP | Not recommended for BYOD — enforce email security via App Protection Policies on Outlook instead |

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
