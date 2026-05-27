# iOS/iPadOS Shared Device and Kiosk Mode Guide

## Overview

This guide covers four optional deployment scenarios that extend the iOS/iPadOS baseline for organizations running shared or dedicated-use devices. These scenarios were intentionally not added to the core baseline scope because they represent separate deployment tracks with distinct enrollment profiles, group targeting, and policy sets. Administrators planning to deploy any of these scenarios should read this guide before creating additional Intune policies or Entra groups.

All four scenarios require supervised devices enrolled through Apple Automated Device Enrollment (ADE) via Apple Business Manager (ABM). None of these scenarios are appropriate for BYOD (personal device) enrollment.

### Scenarios Covered

| Feature | Entra Shared Device Mode | Apple Shared iPad | Single-App Kiosk | Multi-App Kiosk |
|---|---|---|---|---|
| Device type | iPhone or iPad | iPad only (32 GB minimum) | iPhone or iPad | iPhone or iPad |
| Minimum Recommended OS | iOS/iPadOS 18 | iPadOS 18 | iOS/iPadOS 18 | iOS/iPadOS 18 |
| Supervision required | Yes | Yes | Yes | Yes |
| ADE required | Yes — with user affinity (Entra SDM mode) | Yes — without user affinity | Yes — without user affinity | Yes — without user affinity |
| Entra federation with ABM | Not required | Required | Not required | Not required |
| Multiple users per device | Yes (session-based via MSAL) | Yes (per-user partition) | No — dedicated single device | No — dedicated single device |
| Conditional Access supported | Yes | No | Yes | Yes |
| App Protection Policies (MAM) | Yes | No | Not applicable | Not applicable |
| Compliance policies | Yes | No | Yes | Yes |
| Intune Company Portal | Supported | Not supported | Supported | Supported |

---

## Part A — Entra Shared Device Mode (Frontline)

### What It Is

Entra Shared Device Mode (SDM) is a Microsoft Authenticator-mediated session management model designed for frontline workers who share physical devices across shifts. A worker signs in to MSAL-integrated apps (Microsoft Teams, Outlook, Edge, OneDrive) using their work account. When the shift ends, a global sign-out through Microsoft Authenticator clears all session tokens and account state simultaneously across all MSAL-integrated apps on the device, without wiping the device itself.

SDM operates at the application layer. The device remains enrolled, supervised, and managed by Intune throughout. Device-level policies — restrictions, security settings, compliance rules — continue to apply regardless of which user session is active. This is the key distinction from Apple Shared iPad, which operates at the OS partition level.

### Prerequisites

- Apple Business Manager account with an active ADE integration in Intune
- Microsoft Authenticator deployed as a Required VPP app (device-licensed)
- ADE enrollment profile configured with the "Enroll with Microsoft Entra ID shared mode" option enabled
- Microsoft Intune Plan 1 (minimum)
- Microsoft Entra ID P1 recommended (required for Conditional Access integration)
- Microsoft Entra federation with ABM is NOT required for SDM (unlike Apple Shared iPad)
- Apps intended for shared use must be MSAL-integrated. Microsoft 365 apps (Teams, Outlook, Edge, OneDrive, Authenticator) natively support SDM. Line-of-business apps must be built with MSAL if global sign-out is required.

### Policy Coverage

Entra Shared Device Mode is fully compatible with the Corporate baseline — no policy conflicts exist and no exclusions are required. Assign all standard Corporate baseline policies to the SDM device group alongside the SDM-specific items below.

| Policy / Configuration | Baseline Source | Baseline Coverage |
|---|---|---|
| All 22 Corporate Settings Catalog policies | Full Corporate SC baseline | Reuse as-is — all SC policies are compatible with SDM; auto-lock and passcode requirements are appropriate for frontline shared devices; `blockAccountModification = true` does not interfere with MSAL authentication |
| Compliance Policy Corporate + MDE Corporate | Baseline compliance | Reuse as-is — compliance evaluates against the device, not the active user session |
| App Protection Policy (MAM) | Baseline APP | Reuse as-is — fully supported on SDM devices |
| Conditional Access policies | Baseline CA | Reuse as-is — compliant device and app protection CA policies evaluate correctly on SDM-enrolled devices |
| Corporate App Configuration Policies | Baseline ACPs (Defender, Edge, OneDrive, Teams, SharePoint, Outlook) | Reuse as-is — assign the Corporate ACP variants to the SDM group |
| MDE Zero-Touch Control Filter | `iOS_iPadOS - DC - DEV - Zero-Touch-Control-Filter - MDE - Corporate Devices` | Reuse as-is — SDM devices are supervised; use the Corporate MDE profile |
| **Authenticator SDM ACP** | `iOS_iPadOS - ACP - DEV - Microsoft Authenticator - Shared Device Mode` | Reuse baseline policy as-is — sets `sharedDeviceMode = true`; enables global sign-out across all MSAL-integrated apps |
| **ADE Enrollment Profile** | Not in baseline | Create from scratch — Devices > Enrollment program tokens; enable "Enroll with Microsoft Entra ID shared mode"; name: `ADE-iOS-Entra-Shared-Device-Mode` |
| **Dynamic Entra Device Group** | Not in baseline | Create from scratch — rule: `device.enrollmentProfileName -eq "ADE-iOS-Entra-Shared-Device-Mode"`; suggested name: `iOS - DEV - Entra Shared Device Mode` |

---

## Part B — Apple Shared iPad (Multi-User iPadOS)

### What It Is

Apple Shared iPad is an iPadOS feature that creates isolated per-user partitions on a single device. Each user signs in with a Managed Apple ID federated to Microsoft Entra ID, and iPadOS maintains a separate app state, documents, and settings partition for each user. When a user signs out, their partition is preserved on-device (up to the cached user limit) or synced to iCloud for restoration on next sign-in. Guest sessions (temporary sessions) are also available and delete all data on sign-out.

Shared iPad operates at the OS level, not the application layer. This makes it a fundamentally different model than Entra SDM. As a result, several Microsoft Intune features that depend on per-device identity — Compliance Policies, App Protection Policies, and Conditional Access — are not supported on Shared iPad.

### Prerequisites

- iPad with at least 32 GB of storage (required; Shared iPad cannot be enabled on lower-storage devices)
- iPadOS 13.4 or later
- Apple Business Manager account with Managed Apple IDs configured
- **Microsoft Entra federation with Apple Business Manager** — This must be configured in the Entra ID portal before users can sign in with their organizational credentials. Managed Apple IDs are automatically provisioned on first sign-in after federation is established.
- ADE enrollment profile configured with: User affinity = Enroll without user affinity; Supervised = Yes; Shared iPad = Yes
- Microsoft Intune Plan 1

### Configurations Required

All configurations for Shared iPad must be assigned to a dedicated Shared iPad device group. Do not assign standard baseline policies to this group — see the conflict table below.

**Step 1 — ADE Enrollment Profile** (Intune admin center)
- Go to Devices > iOS/iPadOS > Device onboarding > Enrollment program tokens
- Create an enrollment profile with: User affinity = Enroll without user affinity; Supervised = Yes; Shared iPad = Yes. Name your profile as: `ADE-iOS-Apple-Shared-iPad`.
- Configure the maximum cached user count appropriate for the device storage and expected user volume

**Step 2 — Dynamic Entra Device Group**
```
device.enrollmentProfileName -eq "REPLACE: Shared iPad enrollment profile name"
```
Suggested group name: `iOS - DEV - Apple Shared iPad`

**Step 3 — Required Intune Configurations** (assign to the Shared iPad device group unless otherwise noted)

| Profile Type | Configuration | Baseline Coverage |
|---|---|---|
| Settings Catalog | **SSO extension** — assign `iOS_iPadOS - SC - DEV - Microsoft Enterprise SSO - All Devices` | Reuse baseline policy as-is |
| Settings Catalog | **Software Update (DDM)** — assign `iOS_iPadOS - SC - DEV - Software Update - Corporate Devices` | Reuse baseline policy as-is |
| Settings Catalog | **Lock Screen Message** — create a Shared iPad-specific version of the baseline Lock Screen Corporate policy with an appropriate asset tag and footnote | Shared iPad-specific version required |
| Settings Catalog | **Device Restrictions** — create a Shared iPad-specific SC policy containing: Force automatic date/time = true; Block Shared iPad temporary sessions (if guest access is not needed); Require Wi-Fi config profiles only (optional) | Shared iPad-specific version required — baseline Device Restrictions Corporate sets Force automatic date/time = false |
| Device Features | **Home screen layout** — configure app grid and folder layout; assign to user group for per-role layouts or device group for a uniform layout | Create from scratch — not in baseline |
| Device Features | **App notifications** — configure per-app notification settings appropriate for the shared device context | Create from scratch — not in baseline |
| Settings Catalog | **Web Content Filter** — create a Shared iPad-specific Web Content Filter SC policy | Create from scratch — no Web Content Filter SC policy in baseline |

**Step 4 — App Assignment**
- Assign all apps as **Required** (not Available)
- Supported app types: device-licensed VPP apps, custom apps, line-of-business apps, web apps
- User-licensed VPP apps are NOT supported on Shared iPad
- App Store apps (non-VPP) are NOT supported
- The Intune Company Portal app is NOT supported and must not be deployed

### Unsupported Scenarios — Do Not Assign These

| Baseline Policy | Reason Not to Assign |
|---|---|
| Compliance Policy — Corporate or BYOD | Device compliance is not supported on Shared iPad; assigning causes errors in reporting |
| App Protection Policy (MAM) | MAM is not supported on Shared iPad |
| CA-Req-ComplianceDevice / CA-Req-AppProtectionPolicy-BYOD (Conditional Access) | Conditional Access does not evaluate on Shared iPad; access cannot be gated by device compliance or app protection |
| Email configuration profiles | Cause assignment errors on Shared iPad; do not assign |

### Baseline Policy Conflicts

Several baseline SC policies will conflict with Shared iPad operation if assigned to the Shared iPad group. The conflict mechanism and remediation for each are:

| Baseline Policy | Conflicting Setting | Impact on Shared iPad | Remediation |
|---|---|---|---|
| Device Administration - Corporate | Block account modification = true | Prevents Managed Apple ID sign-in — users cannot authenticate to their partition | Exclude `iOS - DEV - Shared iPad - All` from the Device Administration Corporate policy assignment |
| Device Security - Corporate | Require alphanumeric passcode; minimum length 6; Force PIN | Intune cannot manage per-user partition passcodes on Shared iPad; Apple enforces an 8-character alphanumeric passcode natively; an Intune passcode policy applied here causes policy conflicts and errors | Exclude `iOS - DEV - Shared iPad - All` from the Device Security Corporate policy assignment |
| iCloud & Storage - Corporate | Block iCloud Backup = true | Shared iPad uses iCloud to persist per-user partition data; blocking iCloud Backup breaks user data restoration after device reset or when partition is evicted for storage | Exclude `iOS - DEV - Shared iPad - All` from the iCloud & Storage Corporate policy assignment |
| Apple Services - Corporate | Block App Store = true | App Store is already restricted on Shared iPad by the ADE enrollment profile; this setting is redundant but not harmful | No action required — acceptable |
| Compliance Policy - Corporate | All compliance settings | Not supported on Shared iPad | Do not assign to Shared iPad group |
| App Protection Policy — MAM - BYOD | All MAM settings | Not supported on Shared iPad | Do not assign to Shared iPad group |

### Remediation Summary

Create the `iOS - DEV - Shared iPad - All` dynamic device group. Do not include this group in `iOS - DEV - Corporate - All` if that group is used as a broad assignment target. For each of the three conflicting SC policies (Device Administration Corporate, Device Security Corporate, iCloud & Storage Corporate), add `iOS - DEV - Shared iPad - All` as an exclusion group in the policy assignment. For Compliance Policies and the APP policy, simply do not create any assignment to the Shared iPad group.

---

## Part C — Single-App Kiosk Mode

### What It Is

Single-App Kiosk Mode locks a supervised iOS/iPadOS device to a single application. The user cannot exit to the home screen, access other apps, or interact with any system UI outside of what the kiosk app exposes. This uses Apple's Autonomous Single App Mode (ASAM), enforced at the MDM level.

Common use cases include point-of-sale terminals, patient check-in kiosks, digital signage, visitor registration, and retail product demonstration devices.

### Policy Type

Single-App Kiosk is configured using a **Device Configuration profile** in the Intune admin center — specifically the **Device restrictions** template under iOS/iPadOS Configuration profiles. This is a legacy Device Configuration profile type, not a Settings Catalog policy. The kiosk settings are located in the Kiosk section of the Device restrictions template (Devices > iOS/iPadOS > Configuration profiles > Create profile > Templates > Device restrictions).

This is an important distinction: the existing baseline uses the Settings Catalog exclusively for its 24 SC policies. Kiosk Mode requires creating a Device Configuration profile through the legacy template path.

### Prerequisites

- Supervised device enrolled via ADE (without user affinity for unattended kiosk devices)
- Apple Business Manager account with ADE integration
- Target application deployed as a **Required** app before the kiosk profile is applied; the app must be installed on the device before kiosk mode activates
- App bundle ID of the kiosk application (e.g., `com.microsoft.msedge`, `com.microsoft.teams`, or the bundle ID of the organization's LOB app)

### Policy Coverage

| Policy / Configuration | Baseline Source | Baseline Coverage |
|---|---|---|
| App Management Corporate | Baseline SC | Reuse as-is — blocks App Store, app installation, app removal; reinforces kiosk isolation |
| Connectivity Controls Corporate | Baseline SC | Reuse as-is — blocks VPN creation; prevents bypassing network controls |
| Device Administration Corporate | Baseline SC | Reuse as-is — blocks account modification and configuration profile installation |
| Apple Intelligence & Siri Corporate | Baseline SC | Reuse as-is — Siri disabled; appropriate for a dedicated-use device |
| Data Protection Corporate | Baseline SC | Reuse as-is — blocks AirDrop and screenshots; evaluate screenshot block if the kiosk app requires screen mirroring |
| iCloud & Storage Corporate | Baseline SC | Reuse as-is — blocks iCloud Drive, backup, and sync; appropriate for a dedicated device |
| Lock Screen Corporate | Baseline SC | Reuse as-is — blocks Notification Center, Today View, and Wallet; reinforces the locked experience |
| Software Update Corporate (DDM) | Baseline SC | Reuse as-is — 14-day IT deferral provides time to validate OS updates against kiosk app compatibility |
| Microsoft Enterprise SSO | `iOS_iPadOS - SC - DEV - Microsoft Enterprise SSO - All Devices` | Reuse as-is — assign if the kiosk app requires authenticated Entra access |
| Compliance Policy Corporate + MDE Corporate | Baseline compliance | Reuse as-is |
| Conditional Access policies | Baseline CA | Reuse as-is |
| MDE Zero-Touch Control Filter | `iOS_iPadOS - DC - DEV - Zero-Touch-Control-Filter - MDE - Corporate Devices` | Reuse as-is — kiosk devices are supervised |
| **Device Security** | `iOS_iPadOS - SC - DEV - Device Security - Corporate Devices` | Kiosk-specific version required — baseline sets `forcePin = true` and `maxInactivity = 1`; create `Device Security - Kiosk` SC policy with `forcePin = false` and `maxInactivity = 0`; exclude kiosk group from Device Security Corporate |
| **Microsoft Edge ACP** (if Edge is the kiosk browser) | `iOS_iPadOS - ACP - DEV - Microsoft Edge - Corporate Devices` | Kiosk-specific version required — create a kiosk Edge ACP with URL allow list and lockdown keys; see configuration table below |
| **Device restrictions profile — Kiosk section** | Not in baseline — legacy Device Configuration template | Create from scratch — see configuration table below |
| **ADE Enrollment Profile** | Not in baseline | Create from scratch — no user affinity; supervised; locked enrollment; name: `ADE-iOS-Single-App-Kiosk` |
| **Dynamic Entra Device Group** | Not in baseline | Create from scratch — rule: `device.enrollmentProfileName -eq "ADE-iOS-Single-App-Kiosk"`; suggested name: `iOS - DEV - Single-App Kiosk` |
| **Kiosk app** | Not in baseline | Deploy as Required VPP app to `iOS - DEV - Single-App Kiosk` before the Device restrictions profile is applied |

### Device Restrictions Profile — Kiosk Section

Configure in Intune admin center: Devices > iOS/iPadOS > Configuration profiles > Create profile > Templates > Device restrictions > Kiosk section.

| Setting | Recommended Value | Notes |
|---|---|---|
| App to run in kiosk mode | Select app type and specify bundle ID | Managed App (for Intune-deployed LOB/VPP apps) or Store App for App Store apps |
| Touch | Enabled | Disable only if touch input must be fully blocked (e.g., digital signage) |
| Auto lock | Disabled | Prevents the device from locking during kiosk operation |
| Volume buttons | Configure as needed | Disable for silent kiosk environments |
| Screen rotation | Block or Allow | Block for fixed-orientation displays |
| Sleep/wake button | Disabled for fully locked kiosks | Prevents users from putting the device to sleep |
| AssistiveTouch | Disabled unless required for accessibility | |

### Microsoft Edge App Configuration Policy (Kiosk Browser)

If Microsoft Edge is deployed as the kiosk browser, create a kiosk-specific App Configuration Policy (Managed Device) with these keys:

| Key | Type | Value |
|---|---|---|
| `com.microsoft.intune.mam.managedbrowser.NewTabPage.CustomURL` | String | REPLACE: kiosk landing page URL |
| `com.microsoft.intune.mam.managedbrowser.AllowListURLs` | String | REPLACE: comma-separated list of allowed URLs |
| `IntuneMAMAllowedAccountsOnly` | Boolean | true (if sign-in is required) |
| `com.microsoft.intune.mam.managedbrowser.AllowTransitionOnBlock` | Boolean | false — prevents restricted sites from redirecting to the personal browser context |

---

## Part D — Multi-App Kiosk Mode

### What It Is

iOS and iPadOS do not have a native multi-app kiosk mode equivalent to Android Enterprise Dedicated Devices or Windows Assigned Access (multi-app kiosk). The closest supported approach in Intune is a constrained multi-app environment constructed from three policy layers:

1. A **Device Restrictions profile** (Device Configuration template) using the "Show or hide apps" feature to restrict visible apps to an approved list
2. A **Device Features profile** (Device Configuration template) to configure a home screen layout showing only the approved apps
3. The baseline's existing supervised controls — blocking App Store, app installation, app removal, account modification, and VPN creation — which prevent users from expanding the available app set or modifying system configuration

This creates a managed multi-app environment rather than a true kiosk lockdown. Users can reach the home screen and some areas of the Settings app; the environment is not fully isolated. For deployments requiring full lockdown (unattended kiosks, point-of-sale terminals), use Single-App Kiosk Mode instead.

### Policy Types

Multi-App Kiosk uses **Device Configuration profiles** — not Settings Catalog. Two templates are used:

- **Device Restrictions template** — "Show or hide apps" section (or "Allowed apps / Blocked apps" depending on the iOS version and Intune UI version)
- **Device Features template** — Home Screen Layout section

### Policy Coverage

| Policy / Configuration | Baseline Source | Baseline Coverage |
|---|---|---|
| App Management Corporate | Baseline SC | Reuse as-is — App Store block, app installation block, and app removal block reinforce the constrained environment |
| Connectivity Controls Corporate | Baseline SC | Reuse as-is — blocks VPN creation; prevents bypassing network controls |
| Device Administration Corporate | Baseline SC | Reuse as-is — blocks account modification and configuration profile installation |
| Apple Intelligence & Siri Corporate | Baseline SC | Reuse as-is — Siri disabled; appropriate for a dedicated-use device |
| Data Protection Corporate | Baseline SC | Reuse as-is — blocks AirDrop and screenshots; appropriate for most kiosk deployments |
| iCloud & Storage Corporate | Baseline SC | Reuse as-is — blocks iCloud Drive, backup, and sync; appropriate for a dedicated device |
| Lock Screen Corporate | Baseline SC | Reuse as-is — blocks Notification Center, Today View, and Wallet |
| Software Update Corporate (DDM) | Baseline SC | Reuse as-is — 14-day IT deferral provides time to validate OS updates against kiosk app compatibility |
| Microsoft Enterprise SSO | `iOS_iPadOS - SC - DEV - Microsoft Enterprise SSO - All Devices` | Reuse as-is — assign if any kiosk app requires authenticated Entra access |
| Compliance Policy Corporate + MDE Corporate | Baseline compliance | Reuse as-is |
| Conditional Access policies | Baseline CA | Reuse as-is |
| MDE Zero-Touch Control Filter | `iOS_iPadOS - DC - DEV - Zero-Touch-Control-Filter - MDE - Corporate Devices` | Reuse as-is — kiosk devices are supervised |
| **Device Security** | `iOS_iPadOS - SC - DEV - Device Security - Corporate Devices` | Kiosk-specific version required — baseline sets `forcePin = true` and `maxInactivity = 1`; create `Device Security - Kiosk` SC policy with `forcePin = false` and `maxInactivity = 0`; exclude kiosk group from Device Security Corporate |
| **Microsoft Edge ACP** (if Edge is deployed as the browser) | `iOS_iPadOS - ACP - DEV - Microsoft Edge - Corporate Devices` | Kiosk-specific version required — create a kiosk Edge ACP with URL restrictions; see configuration table below |
| **Device Restrictions profile — App Visibility** | Not in baseline — legacy Device Configuration template | Create from scratch — "Show or hide apps" section; see configuration below |
| **Device Features profile — Home Screen Layout** | Not in baseline — legacy Device Configuration template | Create from scratch — configure home screen grid to show only approved apps |
| **ADE Enrollment Profile** | Not in baseline | Create from scratch — no user affinity; supervised; locked enrollment; name: `ADE-iOS-Multi-App-Kiosk` |
| **Dynamic Entra Device Group** | Not in baseline | Create from scratch — rule: `device.enrollmentProfileName -eq "ADE-iOS-Multi-App-Kiosk"`; suggested name: `iOS - DEV - Multi-App Kiosk` |

### Device Restrictions Profile — App Visibility

Configure in Intune admin center: Devices > iOS/iPadOS > Configuration profiles > Create profile > Templates > Device restrictions > Show or hide apps.

Add the bundle IDs of all apps that should be visible to users. All other apps will be hidden from the home screen. Note that Settings, built-in Apple apps that cannot be removed, and some system UI elements may remain accessible depending on the iOS version.

Recommended visible apps for a Microsoft 365 frontline multi-app environment:

| App | Bundle ID |
|---|---|
| Microsoft Teams | `com.microsoft.teams` |
| Microsoft Edge | `com.microsoft.msedge` |
| Microsoft Outlook | `com.microsoft.office.outlook` |
| SharePoint | `com.microsoft.SharePointMobile` |
| OneDrive | `com.microsoft.OneDrive` |
| Additional LOB apps | REPLACE: bundle ID |

### Device Features Profile — Home Screen Layout

Configure in Intune admin center: Devices > iOS/iPadOS > Configuration profiles > Create profile > Templates > Device features > Home Screen Layout.

Configure the home screen grid to display only the approved apps in a logical layout. Combined with the app visibility restrictions above, users will see only the curated set of icons. Assign to the device group for a uniform layout across all kiosk devices, or to a user group to support per-role layouts.

### Microsoft Edge App Configuration Policy (Multi-App Browser)

If Microsoft Edge is deployed as the browser in the multi-app environment, create a kiosk-specific App Configuration Policy (Managed Device) with these keys:

| Key | Type | Value |
|---|---|---|
| `com.microsoft.intune.mam.managedbrowser.AllowListURLs` | String | REPLACE: comma-separated list of allowed URLs |
| `com.microsoft.intune.mam.managedbrowser.BlockListURLs` | String | REPLACE: comma-separated list of blocked URL patterns |
| `com.microsoft.intune.mam.managedbrowser.NewTabPage.CustomURL` | String | REPLACE: default home page URL |

### Limitations

- Users can still navigate to the iOS home screen — there is no full-screen lockdown as in Single-App Kiosk Mode
- Some built-in Settings panels remain accessible even with the supervised restriction set applied; the baseline's supervised controls (block account modification, block VPN creation, block config profile changes) significantly reduce the accessible surface but do not eliminate it
- The "Show or hide apps" feature hides app icons from the home screen but does not prevent deep-link URL schemes from opening a hidden app if the app is installed on the device
- For deployments requiring full lockdown (unattended kiosks, point-of-sale terminals, digital signage), use Single-App Kiosk Mode instead

---

## Baseline Conflict Summary

| Baseline Policy | Conflicting Setting | Affects | Remediation |
|---|---|---|---|
| Device Administration - Corporate | Block account modification | Apple Shared iPad | Exclude Shared iPad group from assignment |
| Device Security - Corporate | Require alphanumeric passcode; Force PIN; Auto-lock 2 min | Kiosk (Single-App and Multi-App); Apple Shared iPad | Kiosk: exclude kiosk group; create Device Security - Kiosk override. Shared iPad: exclude Shared iPad group. |
| iCloud & Storage - Corporate | Block iCloud Backup | Apple Shared iPad | Exclude Shared iPad group from assignment |
| Compliance Policy - Corporate | All settings | Apple Shared iPad | Do not assign to Shared iPad group |
| App Protection Policy - MAM - BYOD | All settings | Apple Shared iPad | Do not assign to Shared iPad group |

Entra Shared Device Mode has no conflicts with the existing baseline. All standard Corporate L1/L2/L3 policies are compatible with SDM-enrolled devices.

---

## Group Assignment Architecture

The following Entra group structure extends the existing baseline group architecture for these optional deployment tracks. These groups are in addition to the existing groups documented in the baseline (see the Entra Group Structure section in the iOS/iPadOS MDM Deployment Playbook for the full group naming convention).

| Group Name | Type | Membership Rule | Policies to Assign | Exclude From |
|---|---|---|---|---|
| `iOS - DEV - Entra SDM - All` | Dynamic device | `device.enrollmentProfileName -eq "ADE-iOS-SharedDevice"` | All Corporate L1 baseline policies; Authenticator SDM ACP; MDE policies | Nothing — fully compatible |
| `iOS - DEV - Shared iPad - All` | Dynamic device | `device.enrollmentProfileName -eq "REPLACE: Shared iPad profile name"` | Shared iPad-specific profiles only (Device Features, Web Filter, Lock Screen Message, SSO, DDM Software Update) | Device Administration Corporate; Device Security Corporate; iCloud & Storage Corporate; Compliance Policy Corporate; App Protection Policy |
| `iOS - DEV - Single-App Kiosk` | Dynamic device | `device.enrollmentProfileName -eq "ADE-iOS-Single-App-Kiosk"` | Device Security - Kiosk override; Device restrictions Kiosk profile (Single-App); App Config for kiosk app; all other Corporate L1 SC policies except Device Security | Device Security - Corporate |
| `iOS - DEV - Multi-App Kiosk` | Dynamic device | `device.enrollmentProfileName -eq "ADE-iOS-Multi-App-Kiosk"` | Device Security - Kiosk override; Device Restrictions App Visibility profile; Device Features Home Screen Layout profile; App Config for kiosk apps; all other Corporate L1 SC policies except Device Security | Device Security - Corporate |

Enrollment profile names for SDM (`ADE-iOS-SharedDevice`) are documented in the iOS/iPadOS MDM Deployment Playbook. Recommended enrollment profile names for Shared iPad (`ADE-iOS-Apple-Shared-iPad`), Single-App Kiosk (`ADE-iOS-Single-App-Kiosk`), and Multi-App Kiosk (`ADE-iOS-Multi-App-Kiosk`) are listed in the membership rules above. Update the dynamic group rules if you use different profile names in your environment.

---

## References

- Microsoft Learn — Shared device solutions for iOS/iPadOS: `https://learn.microsoft.com/en-us/intune/device-enrollment/apple/shared-device-solutions-ios`
- Microsoft Learn — Shared iPad configuration: `https://learn.microsoft.com/en-us/intune/device-enrollment/apple/shared-ipad`
- Apple Deployment Guide — Shared iPad overview: `https://support.apple.com/guide/deployment/shared-ipad-overview-dep9a34c2ba2/web`

---

*This document is part of the UniFy Endpoint – Microsoft Intune iOS/iPadOS Baseline series. Document maintained by Yoennis Olmo - Sr. Modern Work Consultant.*
