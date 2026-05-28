# iOS/iPadOS Microsoft Intune Baseline

![GitHub Stars](https://badgen.net/github/stars/UniFy-Endpoint/iOS-iPadOS-Intune-Baseline?label=Stars)  ![GitHub Forks](https://badgen.net/github/forks/UniFy-Endpoint/iOS-iPadOS-Intune-Baseline?label=Forks)

A CIS-aligned Microsoft Intune baseline for iOS and iPadOS devices. Includes ready-to-import JSON policies and deployment guides for corporate (supervised/ADE) and personal (BYOD) device management.

---

| | |
|---|---|
| **Author** | Sr. Modern Work Consultant |
| **CIS Reference** | CIS Apple iOS/iPadOS 26 Benchmark v1.0.0 |
| **Microsoft Reference** | iOS/iPadOS Security Configuration Framework (updated April 2026) |
| **Last Updated** | May 23 2026 |

---

- Test all policy settings in a pilot group before deploying to production. 
- Adapt settings to your organization's security requirements, regulatory needs, and operational constraints. 
- The author is not responsible for any impact this baseline may have on your environment.

---

## Version History

| Version | Date | Summary |
|---|---|---|
| 1.0 | May 2026 | Initial release — CIS iOS/iPadOS 26 Benchmark v1.0.0 aligned baseline for Corporate (ADE) and BYOD devices |

---

## What This Baseline Provides

| Deliverable | Description |
|---|---|
| **Deployment Guides** | Step-by-step configuration guides for all policy types |
| **Settings Catalog Policies** | 22 ready-to-import JSON files covering app management (including blocked app bundle IDs), connectivity, device administration, network security, device security, privacy, Safari (Microsoft 365 start page, SSO exception domains), iCloud, Apple Intelligence (Corporate only), software update, and more |
| **Compliance Policies** | 4 flat-model compliance policies (base + MDE signal per device track) for Corporate and BYOD devices |
| **App Protection Policy** | 1 L2 (Enterprise Enhanced Data Protection) MAM policy for BYOD users covering data protection, access controls, and conditional launch |
| **App Configuration Policies** | 13 ACP files — Managed Device (MDM channel): Defender (Corporate + BYOD), Authenticator (SDM), Edge (Corporate + enrolled BYOD), OneDrive (Corporate + BYOD), Teams (Corporate + BYOD), SharePoint (Corporate + BYOD), Outlook (Corporate VPP + BYOD App Store) |
| **Defender Device Configuration** | 2 MDE onboarding profiles — Zero-Touch Control Filter (supervised/Corporate) and Zero-Touch VPN Onboarding (unsupervised/BYOD) |
| **Conditional Access Policies** | 2 CA policies enforcing Compliant Device and App Protection requirements to access Microsoft 365 apps on iOS/iPadOS |

---

## Deployment Models Covered

| Model | Ownership | Supervised | Description |
|---|---|---|---|
| **Automated Device Enrollment (ADE)** | Corporate | Yes | Zero-touch enrollment via Apple Business Manager. Devices enroll automatically through Setup Assistant with full supervised management. |
| **Web-based Device Enrollment** | Personal (BYOD) | No | User opens a Company Portal URL in Safari; iOS installs an MDM profile. Full device management scope. Successor to profile-based enrollment (deprecated in iOS 17). |
| **Account-driven User Enrollment** | Personal (BYOD) | No | User signs in with a Managed Apple ID in Settings. iOS creates an isolated APFS volume for work data — only work accounts and apps are visible to IT. Requires Apple Business Manager. |
| **Device Enrollment with Company Portal** | Personal (BYOD) | No | User downloads Company Portal and completes guided enrollment. Full device management scope; no data separation. |
| **Determine Based on User Choice** | Personal (BYOD) | No | Presents the user with a choice at sign-in: work-partition-only (account-driven user enrollment) or full device management. IT configures which options are available. |

---

## Repository Structure

```
iOS_iPadOS-Baseline/
├── README.md                            ← This file
├── SETTINGSOUTPUT.md                    ← Complete settings reference for all 44 policies
│
├── iOS_iPadOS_Deployment_Guides/        ← Configuration reference guides
│   ├── iOS_iPadOS_Corporate_Deployment_Guide.md
│   ├── iOS_iPadOS_BYOD_Deployment_Guide.md
│   ├── iOS_CIS_Recommended_Settings_Guide.md
│   ├── iOS_App_Protection_Policy_Guide.md
│   ├── iOS_App_Configuration_Policy_Guide.md
│   ├── Defender_for_Endpoint_iOS_iPadOS_Devices.md
│   └── iOS_iPadOS_Shared_Device_Kiosk_Guide.md
│
├── SettingsCatalog/                     ← 22 Settings Catalog and Update Policy JSON files
│   ├── iOS_iPadOS - SC - DEV - Apple Intelligence & Siri - Corporate Devices.json
│   ├── iOS_iPadOS - SC - DEV - Apple Services - Corporate Devices.json
│   ├── iOS_iPadOS - SC - DEV - App Management - Corporate Devices.json
│   ├── iOS_iPadOS - SC - DEV - Connectivity Controls - Corporate Devices.json
│   ├── iOS_iPadOS - SC - DEV - Data Protection - Corporate Devices.json
│   ├── iOS_iPadOS - SC - DEV - Data Protection - BYOD Devices.json
│   ├── iOS_iPadOS - SC - DEV - Device Pairing - Corporate Devices.json
│   ├── iOS_iPadOS - SC - DEV - Device Restrictions - Corporate Devices.json
│   ├── iOS_iPadOS - SC - DEV - Device Privacy - Corporate Devices.json
│   ├── iOS_iPadOS - SC - DEV - Device Privacy - BYOD Devices.json
│   ├── iOS_iPadOS - SC - DEV - Device Restrictions - BYOD Devices.json
│   ├── iOS_iPadOS - SC - DEV - Device Security - Corporate Devices.json
│   ├── iOS_iPadOS - SC - DEV - Device Security - BYOD Devices.json
│   ├── iOS_iPadOS - SC - DEV - iCloud & Storage - Corporate Devices.json
│   ├── iOS_iPadOS - SC - DEV - iCloud & Storage - BYOD Devices.json
│   ├── iOS_iPadOS - SC - DEV - Lock Screen - Corporate Devices.json
│   ├── iOS_iPadOS - SC - DEV - Microsoft Enterprise SSO - All Devices.json
│   ├── iOS_iPadOS - SC - DEV - Accessibility & Sharing - Corporate Devices.json
│   ├── iOS_iPadOS - SC - DEV - Safari Browser - Corporate Devices.json
│   ├── iOS_iPadOS - SC - DEV - Safari Browser - BYOD Devices.json
│   ├── iOS_iPadOS - SC - DEV - Software Update - Corporate Devices.json
│   └── iOS_iPadOS - SC - DEV - Web-App-Store (EU) - Corporate Devices.json
│
├── CompliancePolicies/                  ← 4 Compliance Policy JSON files
│   ├── iOS_iPadOS - CP - DEV - Compliance - Corporate Devices.json
│   ├── iOS_iPadOS - CP - DEV - Compliance - MDE - Corporate Devices.json
│   ├── iOS_iPadOS - CP - DEV - Compliance - BYOD Devices.json
│   └── iOS_iPadOS - CP - DEV - Compliance - MDE - BYOD Devices.json
│
├── AppProtection_MAM/                   ← 1 App Protection Policy JSON file
│   └── iOS_iPadOS - APP - USR - App Protection - MAM - L2 - BYOD Devices.json
│
├── AppConfiguration_ManagedDevice/      ← 13 App Configuration Policy JSON files (MDM channel)
│   ├── iOS_iPadOS - ACP - DEV - Microsoft Defender - Corporate Devices.json
│   ├── iOS_iPadOS - ACP - DEV - Microsoft Defender - BYOD Devices.json
│   ├── iOS_iPadOS - ACP - DEV - Microsoft Authenticator - Shared Device Mode.json
│   ├── iOS_iPadOS - ACP - DEV - Microsoft Edge - Corporate Devices.json
│   ├── iOS_iPadOS - ACP - DEV - Microsoft Edge - BYOD Devices.json
│   ├── iOS_iPadOS - ACP - DEV - Microsoft OneDrive - Corporate Devices.json
│   ├── iOS_iPadOS - ACP - DEV - Microsoft OneDrive - BYOD Devices.json
│   ├── iOS_iPadOS - ACP - DEV - Microsoft Teams - Corporate Devices.json
│   ├── iOS_iPadOS - ACP - DEV - Microsoft Teams - BYOD Devices.json
│   ├── iOS_iPadOS - ACP - DEV - SharePoint - Corporate Devices.json
│   ├── iOS_iPadOS - ACP - DEV - SharePoint - BYOD Devices.json
│   ├── iOS_iPadOS - ACP - DEV - Outlook Settings - Corporate Devices.json
│   └── iOS_iPadOS - ACP - DEV - Outlook Settings - BYOD Devices.json
│
├── DeviceConfiguration_DefenderForEndpoint/   ← 2 MDE onboarding profile JSON files
│   ├── iOS_iPadOS - CP - DEV - Zero-Touch-Control-Filter - MDE - Corporate Devices.json
│   └── iOS_iPadOS - DC - DEV - Zero-Touch-Onboarding-VPN - MDE - BYOD Devices.json
│
├── ConditionalAccess/
│   ├── CA-User-Protection-AllUsers-AllApps-iOS-Req-ComplianceDevice.json
│   └── CA-User-Protection-AllUsers-O365-MobileApps-iOS-Req-AppProtectionPolicy-BYOD.json
```

---

## Guide Reading Order

| # | Guide | Purpose |
|---|---|---|
| 1 | [Corporate Deployment Guide](iOS_iPadOS_Deployment_Guides/iOS_iPadOS_Corporate_Deployment_Guide.md) | ADE/supervised enrollment — ABM, ADE token, VPP, Setup Assistant, Managed Apple ID integration |
| 2 | [BYOD Deployment Guide](iOS_iPadOS_Deployment_Guides/iOS_iPadOS_BYOD_Deployment_Guide.md) | Personal device enrollment — all four BYOD options, groups, App Management |
| 3 | [CIS Recommended Settings Guide](iOS_iPadOS_Deployment_Guides/iOS_CIS_Recommended_Settings_Guide.md) | All Settings Catalog, compliance, and software update settings with CIS L1/L2 mapping |
| 4 | [App Protection Policy Guide](iOS_iPadOS_Deployment_Guides/iOS_App_Protection_Policy_Guide.md) | App Protection Policy (MAM) settings for BYOD apps |
| 5 | [App Configuration Policy Guide](iOS_iPadOS_Deployment_Guides/iOS_App_Configuration_Policy_Guide.md) | App Configuration Policy (ACP) settings for Microsoft apps on iOS |
| 6 | [Defender for Endpoint Guide](iOS_iPadOS_Deployment_Guides/Defender_for_Endpoint_iOS_iPadOS_Devices.md) | Microsoft Defender for Endpoint deployment and configuration |
| 7 | [Shared Device and Kiosk Mode Guide](iOS_iPadOS_Deployment_Guides/iOS_iPadOS_Shared_Device_Kiosk_Guide.md) | Optional: Entra Shared Device Mode, Apple Shared iPad, Single-App Kiosk, Multi-App Kiosk |

---

## Policy Naming Convention

```
{Platform} - {Policy Type} - {Scope Assignment} - {Scope Setting} - {Device Type}
```

| Component | Values | Description |
|---|---|---|
| **Platform** | `iOS_iPadOS` | Always first |
| **Policy Type** | `SC` `CP` `APP` `ACP` `DC` | SC = Settings Catalog · CP = Compliance Policy · APP = App Protection · ACP = App Configuration · DC = Device Configuration |
| **Scope Assignment** | `DEV` `USR` | DEV = Device-assigned · USR = User-assigned |
| **Scope Setting** | Descriptive label | Policy area (e.g., Device Security, Data Protection) |
| **Device Type** | `Corporate Devices` `BYOD Devices` | Target ownership model |

---

## Apps Deployment

**Corporate (ADE / supervised) devices — use VPP with device licensing for every app.**
Device licensing installs apps silently via the MDM channel — no Apple ID or App Store sign-in required. Assign to device groups.

**BYOD (personal / unsupervised) devices — use iOS Store App.**
Users install apps from the App Store via their personal Apple ID. App Protection Policies apply by user identity. Assign to user groups.

Assign Corporate apps to **device groups**. Assign BYOD apps to **user groups**.

| App | Corporate (ADE) | BYOD |
|---|---|---|
| **Microsoft Company Portal** | VPP — Device license — Required | iOS Store App — Available |
| **Microsoft Authenticator** | VPP — Device license — Required | iOS Store App — Required |
| **Microsoft Defender for Endpoint** | VPP — Device license — Required | iOS Store App — Required |
| **Microsoft Outlook** | VPP — Device license — Required | iOS Store App — Available |
| **Microsoft Teams** | VPP — Device license — Required | iOS Store App — Available |
| **Microsoft Edge** | VPP — Device license — Required | iOS Store App — Available |
| **Microsoft OneDrive** | VPP — Device license — Required | iOS Store App — Available |

**Company Portal on corporate ADE devices:** The ADE enrollment profile includes an **Install Company Portal** option that deploys it automatically during enrollment. If you configure that option, do not create a separate app assignment — doing so causes a conflict. See the Corporate Deployment Guide Section 7.2 for enrollment profile setup.

**Available apps must be assigned to user groups, not device groups.** Intune builds the Company Portal app list based on the signed-in user's group membership — Available assignments to device groups are silently ignored.

**VPP license reclamation is not automatic.** When a device is wiped or unenrolled, VPP licenses remain consumed until manually reclaimed. See the Corporate Deployment Guide Section 8.3 for steps.

---

## How to Use This Repository

### Importing JSON Policies

Use the **[IntuneManagement-Master](https://github.com/Micke-K/IntuneManagement)** tool to bulk-import JSON files by folder.

| Folder | Intune path |
|---|---|
| `SettingsCatalog\` | Devices > Apple Mobile > Configuration |
| `CompliancePolicies\` | Devices > Apple Mobile > Compliance |
| `AppProtection_MAM\` | Apps > iOS/iPadOS > Protection |
| `AppConfiguration_ManagedDevice\` | Apps > iOS/iPadOS > Configuration |
| `DeviceConfiguration_DefenderForEndpoint\` | Devices > Apple Mobile > Configuration |

**After importing — replace placeholders:**

| File | Placeholder | Replace With |
|---|---|---|
| `SettingsCatalog\iOS_iPadOS - SC - DEV - Lock Screen - Corporate Devices.json` | `REPLACE: Company Name...` | Your company name and IT contact number |

---

**After importing Conditional Access policies:**

CA policies in `ConditionalAccess\` use group GUIDs specific to your tenant. Create the groups below in your tenant, then update their object IDs in each CA policy after import.

| Group | Purpose |
|---|---|
| **CA-Exclusion-BreakGlass** | Break-glass emergency access accounts only. Never add regular users. |
| **CA-Exclusion-Enrollment** | New devices and users completing enrollment and MFA registration. Without this exclusion, new devices are blocked before they can enroll. |

The application exclusions (`0000000a-0000-0000-c000-000000000000` = Microsoft Intune, `d4ebce55-015a-49b5-a083-c84d1797ae8c` = Intune Enrollment) are Microsoft service principal IDs that are the same in every tenant — do not replace these.

---

### Recommended Deployment Sequence

| Phase | Actions |
|---|---|
| **Phase 0** | ABM setup · Entra groups · Enrollment types · Import policies · CA configuration and break-glass exclusions |
| **Phase 1** | Deploy all policies to pilot group |
| **Phase 2** | Expand to a subset of production devices |
| **Phase 3** | Full production rollout including L2 policies |

See [iOS_CIS_Recommended_Settings_Guide.md](iOS_iPadOS_Deployment_Guides/iOS_CIS_Recommended_Settings_Guide.md) Section 12 for the full phased rollout checklist.

---

## Compliance Coverage

Estimated coverage against CIS iOS/iPadOS 26 Benchmark v1.0.0 and the Microsoft iOS/iPadOS Security Configuration Framework (April 2026). Last updated May 2026 (Phase 8). Remaining gaps are settings not present in this tenant's Intune Settings Catalog — unrecognized IDs are silently ignored on import, not policy errors.

**CIS iOS/iPadOS 26 Benchmark v1.0.0**

| Tier | Estimated Coverage | Notes |
|---|---|---|
| L1 — Corporate (Supervised / ADE) | ~96% | All actionable L1 controls implemented; remaining gap is settings not in this tenant's catalog |
| L1 — BYOD (Personal / Unsupervised) | ~91% | All applicable BYOD L1 controls implemented; supervised-only controls excluded by design |
| L2 — Corporate | ~93% | L2 additive controls fully implemented; gaps limited to tenant catalog constraints |
| L3 — Corporate | ~89% | L3 controls implemented; gaps limited to tenant catalog constraints |

**Microsoft iOS/iPadOS Security Configuration Framework (April 2026)**

| Level | Estimated Coverage | Notes |
|---|---|---|
| Level 1 — Basic (Corporate) | ~97% | All core passcode, data protection, and connectivity controls implemented |
| Level 2 — Enhanced (Corporate) | ~93% | Advanced supervised controls, SSO, MDE, and App Protection implemented |
| Level 3 — High Security (Corporate) | ~88% | High-security supervised controls implemented; some settings not available in tenant catalog |
| BYOD (User Enrollment) | ~91% | Full BYOD track implemented; supervised-only controls excluded by design |

**App Protection Policy (MAM — BYOD):** Aligns with Microsoft Level 2 app protection recommendations with three intentional deviations:

- **Inbound data allowed from all apps** — Restricting inbound sources blocks the camera, contacts, and file sharing into managed apps, which is too disruptive for BYOD.
- **Writing Tools not blocked at the MAM layer** — Device-level Apple Intelligence controls on corporate devices already cover this. Restricting AI on a personal device goes beyond the appropriate scope of BYOD management.
- **Org data notifications allowed** — Blocking notifications on personal devices is too disruptive to personal use. Users are informed via onboarding not to send sensitive data in notification-visible fields.

---

## Implementation Considerations

Read before deploying to production. These settings have the highest user impact — plan communication and test with pilot groups first.

#### High-Impact Settings — Corporate Supervised Devices

| Setting | Impact |
|---|---|
| **Auto-lock 2 minutes (CIS L1)** | Devices lock every 2 minutes of inactivity. Users reading documents or on calls will need to unlock frequently. Cannot be relaxed without breaking CIS L1. |
| **Block Writing Tools / Apple Intelligence (L1 Corporate)** | Removes AI-assisted writing, rewriting, proofreading, and summarization on iOS 26 corporate devices. Prepare a user FAQ before any iOS 26 upgrade. |
| **Block App Store + Block App Installation** | All apps must be provisioned via VPP. Users cannot self-install anything. Ensure a VPP app catalog and help desk request process are in place before deploying. |
| **Alphanumeric passcode** | Changes the unlock keyboard from the numeric pad to a full keyboard. Expect first-day helpdesk volume. |
| **Block iCloud Drive (Data Protection L1)** | iCloud Drive is immediately inaccessible in the Files app. Deploy OneDrive as the explicit replacement before enabling. |
| **Block iCloud Keychain Sync** | Saved passwords no longer sync cross-device. Provide a corporate-approved password manager before deploying. |

---

#### BYOD Enrollment Risk

These settings may drive unenrollment if deployed without prior communication.

| Setting | Impact |
|---|---|
| **Auto-lock 5 minutes** | Most personal devices default to 30 seconds or never. Communicate at enrollment. |
| **Block Notification Center on lock screen** | Users must unlock the device to read any message or alert. |
| **Force encrypted backup** | Visible in device Settings — may prompt user questions, even though it is transparent to normal use. |

---

#### Recommended Adjustment — Organizations Using Outlook for iOS

The Data Protection Corporate policy sets `allowmanagedappstowritecontacts = false`, which blocks Outlook from exporting contacts to the native Contacts app. If your organization relies on Outlook contact sync, change this value to `true` in [SettingsCatalog/iOS_iPadOS - SC - DEV - Data Protection - Corporate Devices.json](SettingsCatalog/iOS_iPadOS%20-%20SC%20-%20DEV%20-%20Data%20Protection%20-%20Corporate%20Devices.json).

---

#### Intentional BYOD Design Decisions

These are deliberate baseline choices, not gaps.

| Decision | Reason |
|---|---|
| **AirDrop not blocked at device level** | App Protection Policies prevent corporate data from leaving via AirDrop from managed apps. A device-level block is a primary BYOD unenrollment driver. |
| **iCloud Keychain not blocked on BYOD** | App Protection Policies already prevent corporate credentials from syncing to personal iCloud. Blocking Keychain on a personal device restricts personal use without meaningful corporate security gain. |
| **No Apple Intelligence policy for BYOD devices** | Microsoft has migrated Apple Intelligence settings from Device Restrictions Configuration to Declarative Device Management (DDM) in Intune. The new DDM-based settings apply correctly to supervised Corporate devices but produce errors when assigned to unsupervised BYOD devices. The Apple Intelligence BYOD policy has been removed from the baseline as a result. MAM App Protection Policies prevent corporate data from being processed by Apple AI within managed apps — no device-level AI control is required for BYOD. |

---

## CIS and Microsoft References

| Source | Reference |
|---|---|
| CIS Apple iOS/iPadOS 26 Benchmark v1.0.0 | https://www.cisecurity.org/benchmark/apple_ios |
| CIS Apple iOS 18 for Intune Benchmark v1.0.0 | https://www.cisecurity.org/benchmark/apple_macos_ios_intune |
| Microsoft iOS/iPadOS Security Configuration Framework | https://learn.microsoft.com/en-us/intune/device-security/security-configurations/ |
| Microsoft iOS/iPadOS Deployment Guide | https://learn.microsoft.com/en-us/intune/enrollment/ios-ipados-deployment-guide |
| Apple Declarative Device Management | https://support.apple.com/guide/deployment/declarative-device-management-manage-apple-depc30268577/web |

---

## Contributing

Contributions, corrections, and improvements are welcome. If you find a discrepancy between these policies and the current CIS benchmark or Microsoft documentation, open an issue or submit a pull request.

> Review all settings in your test environment before deploying to production.

---

*This document is part of the UniFy Endpoint – Microsoft Intune iOS/iPadOS Baseline series. Document maintained by Yoennis Olmo - Sr. Modern Work Consultant.*
