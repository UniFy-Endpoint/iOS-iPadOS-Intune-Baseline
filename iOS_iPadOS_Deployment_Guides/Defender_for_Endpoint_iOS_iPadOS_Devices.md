# Microsoft Defender for Endpoint on iOS/iPadOS Devices

| | |
|---|---|
| **Document Type** | Deployment Instructions |
| **Author** | Sr. Modern Work Consultant |
| **Last Updated** | May 2026 |
| **Applies To** | iOS/iPadOS 16.0 and later, Microsoft Intune, MDE |

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Licensing Requirements](#2-licensing-requirements)
3. [Prerequisites](#3-prerequisites)
4. [Deployment Verification Checklist](#4-deployment-verification-checklist)
5. [Enable the Connection Between MDE and Intune](#5-enable-the-connection-between-mde-and-intune)
6. [Supervised Company-Owned Devices](#6-supervised-company-owned-devices)
7. [Unsupervised and Personal Enrolled Devices](#7-unsupervised-and-personal-enrolled-devices)
8. [User Enrollment Devices (BYOD)](#8-user-enrollment-devices-byod)
9. [App Configuration Policy Keys Reference](#9-app-configuration-policy-keys-reference)
10. [App Protection Policy (Optional)](#10-app-protection-policy-optional)
11. [Conditional Access with MDE on iOS (Optional)](#11-conditional-access-with-mde-on-ios-optional)
12. [Jailbreak Detection](#12-jailbreak-detection)
13. [Network Protection](#13-network-protection)
14. [Privacy Controls](#14-privacy-controls)
15. [References and Resources](#15-references-and-resources)

---

## 1. Introduction

Microsoft Defender for Endpoint (MDE) for iOS/iPadOS is a mobile threat defense solution for devices running iOS 16.0 or later. It supports supervised corporate devices, unsupervised BYOD devices enrolled in Intune, and unenrolled devices managed through MAM.

**MDE protects against:**

- Phishing and unsafe connections from websites, emails, and apps
- Rogue Wi-Fi and open network threats
- Certificate-based phishing attacks
- Jailbroken device risks
- Vulnerable app detection (Threat and Vulnerability Management)

All alerts and signals are available in the **Microsoft Defender portal** at [https://security.microsoft.com](https://security.microsoft.com).

> **Note:** MDE on iOS is not supported on user-less or shared devices.

---

## 2. Licensing Requirements

### Microsoft Defender for Endpoint License (one of the following)

- Microsoft Defender for Endpoint Plan 1 or Plan 2
- Microsoft Defender for Business
- Microsoft 365 Business Premium

### Microsoft Intune License (one of the following)

- Microsoft 365 E5
- Microsoft 365 E3
- Enterprise Mobility + Security E5
- Enterprise Mobility + Security E3
- Microsoft 365 Business Premium

---

## 3. Prerequisites

### Company-Owned and Personal-BYOD Enrolled Devices (MDM)

- For Account-Driven User Enrollment, ensure the user's UPN matches their Apple ID.
- Device(s) are already enrolled in Microsoft Intune
- End users must have **Microsoft Authenticator** and **Company Portal** apps installed
- Devices must be running **iOS/iPadOS 16.0 or later**
  > **Important:** iOS/iPadOS 15 reached end of support on **January 31, 2025** and can no longer receive MDE updates or protection.
- Microsoft Defender is the default and only mobile threat defense solution used on iOS devices
- Both corporate and personal mobile devices are managed through Intune MDM

> **Note:** While iOS 16.0 is the minimum, **iOS 18.0** is the recommended baseline for optimal SSO extension and modern BYOD capabilities in 2026.

### Personal Unenrolled Devices (MAM)

- Device must be registered with Microsoft Entra ID (requires the user to sign in through Microsoft Authenticator)
- MDE protection and configuration is delivered through Mobile Application Management (MAM)
- No Intune device enrollment required

### Before You Begin

- Confirm iOS device enrollment is complete in Intune for your users
- Verify that users have a Defender for Endpoint license assigned
- Ensure Company Portal is installed, signed in, and enrollment is finalized
- Verify that Defender for Endpoint App is deployed vias VPP-Content-Token for ADE devices/or iOS-Store App for BYOD-Devices.

---

## 4. Deployment Verification Checklist

Use this table to verify that all configurations are correctly applied for the specific enrollment type before moving to production.

| Deployment Area | Requirement | Account-Driven User Enrollment (BYOD) | Web-Based / Standard Device Enrollment | Supervised (Corporate) |
| :--- | :--- | :--- | :--- | :--- |
| **SSO App Extension** | Bundle ID `com.microsoft.scmx` included | **Required** | Recommended | Recommended |
| **VPN Profile** | Custom VPN with `SilentOnboard` = `True` | **Unsupported** | **Required** | N/A (Uses Control Filter) |
| **App Config Key** | `UserEnrollmentEnabled` = `True` | **Required** | **Must be False** | N/A |
| **User Interaction** | Manual "Allow" for VPN popup | **Required** | No (Silent) | No (Silent) |
| **Web Protection** | Method of delivery | Per-App / Loopback VPN | Device-wide Loopback VPN | Control Filter (No VPN) |
| **Minimum OS** | Version baseline | iOS 17.0+ (Rec: 18.0) | iOS 16.0+ | iOS 16.0+ |

---


## 5. Enable the Connection Between MDE and Intune

### Step 1 – Verify the Intune Connector in the Defender Portal

1. Go to [https://security.microsoft.com](https://security.microsoft.com)
2. Navigate to **Settings > Endpoints > Advanced Features**
3. Confirm the following toggles are **On**:

| Setting | Status |
|---|---|
| Microsoft Intune connection | **On** |
| Device discovery | **On** |
| Preview features | **On** |

### Step 2 – Verify the MDE Connector in Intune

1. In the [Microsoft Intune admin center](https://intune.microsoft.com/), go to **Endpoint Security > Microsoft Defender for Endpoint**
2. Confirm **Connection status** is **Enabled**
 
### Step 3 – Configure Endpoint Security Profile Settings

In the same Intune MDE connector blade, configure the following:

| Setting | Recommended Value |
|---|---|
| Allow Microsoft Defender for Endpoint to enforce Endpoint Security Configurations | **On** |
| Connect iOS/iPadOS devices version 13.0 and above to Microsoft Defender for Endpoint | **On** |
| Enable App Sync (sending application inventory) for iOS/iPadOS devices | **On** |
| Send full certificate inventory data on personally owned iOS/iPadOS devices | On |
| Enable Certificate Sync for iOS/iPadOS devices | On |
| Block unsupported OS versions | On |
| Connect iOS/iPadOS devices to Microsoft Defender for Endpoint (Protection policy evaluation) | **On** |

---

## 6. Supervised Company-Owned Devices

Supervised devices managed through Apple Business Manager and enrolled via Automated Device Enrollment (ADE) provide the richest MDE experience. Web Protection is delivered through the **Control Filter Profile** — no Zero-Touch-Onboarding-VPN is required.

### Step 1 – Deploy the Microsoft Defender App via VPP

For supervised corporate devices managed through Apple Business Manager, deploy Microsoft Defender as a **Volume Purchase Program (VPP)** app — not as an iOS Store App. VPP deployment is silent, license-tracked, and the correct method for ADE-enrolled devices.

**Prerequisite:** Your Apple Business Manager VPP content token must already be synced to Intune at **Tenant administration > Connectors and tokens > Apple VPP tokens**. Acquire the Microsoft Defender app in ABM before proceeding.

1. In the Intune admin center, go to **Apps > iOS/iPadOS**
2. Locate your ABM VPP token from the list **Microsoft Defender for Endpoint** in the app list
3. Click **Properties**
4. Set **Minimum operating system** to **iOS 18.0**
5. Under **Assignments > Required**, add the target supervised device group
6. Click **Next**, then **Create**

> **Note:** If VPP is not available, you can add Microsoft Defender as an iOS store app (Apps > iOS/iPadOS > Add > iOS store app) and assign it as Required — but VPP is strongly preferred for supervised devices.

### Step 2 – Deploy the Zero Touch Control Filter Profile (Supervised Only)

The Control Filter profile enables **Web Protection without a local VPN**. The Zero Touch variant silently onboards users without any interaction.

> **Note:** The Control Filter profile does not work with Always-On VPN (AOVPN) due to platform restrictions.

1. Download the **Zero Touch (Silent) Control Filter** profile from Microsoft:
   - Zero touch (Silent) Control Filter: [`ControlFilterZeroTouch.mobileconfig`](https://download.microsoft.com/download/f/8/e/f8ed3484-b665-4c3c-9ae9-272c8a04159b/Microsoft_Defender_for_Endpoint_Control_Filter_Zerotouch.mobileconfig)
   - Standard Control Filter: [`ControlFilter.mobileconfig`](https://download.microsoft.com/download/f/8/e/f8ed3484-b665-4c3c-9ae9-272c8a04159b/Microsoft_Defender_for_Endpoint_Control_Filter_1.mobileconfig)

2. In the Intune admin center, go to **Devices > iOS/iPadOS > Configuration profiles > Create Profile**
3. Set **Platform** to `iOS/iPadOS` and **Profile type** to `Templates`
4. Select **Template name: Custom** and click **Create**
5. Provide a name for the profile (e.g., `Zero Touch Control Filter MDE`)
6. Under **Custom configuration profile**, upload the downloaded `.mobileconfig` file
7. Under **Assignments**, select the supervised device group (best practice: all managed iOS devices)
8. Click **Next**, then **Create**

### Step 3 – Deploy App Configuration Policy (Supervised Devices)

1. Go to **Apps > App configuration policies > Add > Managed devices**
2. Provide:
   - **Name:** e.g., `iOS_iPadOS - ACP - DEV - Microsoft Defender - Corporate Devices`
   - **Platform:** `iOS/iPadOS`
   - **Targeted app:** `Microsoft Defender for Endpoint`
3. Under **Configuration settings**, select **Use configuration designer**
4. Add the following keys:

| Configuration Key | Value Type | Configuration Value |
|---|---|---|
| `issupervised` | String | `{{issupervised}}` |
| `IntuneMAMUPN` | String | `{{userprincipalname}}` |
| `WebProtection` | String | `True` |
| `DefenderTVMPrivacyMode` | Integer | `0` |
| `DefenderExcludeURLInReport` | Integer | `0` |
| `DefenderNetworkProtectionEnable` | String | `True` |
| `DefenderEndUserTrustFlowEnable` | String | `True` |
| `DefenderNetworkProtectionAutoRemediation` | String | `True` |
| `DefenderNetworkProtectionPrivacy` | String | `True` |
| `DefenderOpenNetworkDetection` | Integer | `2` |
| `SuppressOSUpdateNotification` | String | `True` |
| `DisableSignOut` | String | `True` |
| `DefenderDeviceTag` | String | `iOS-iPadOS-CORP-MDM` |

5. Click **Next** through Scope tags
6. Under **Assignments**, target **All Devices** (or the appropriate supervised device group)
7. Click **Next**, then **Create**

---


## 7. Unsupervised and Personal Enrolled Devices

For unsupervised devices (standard Intune enrollment), Web Protection is delivered through a **local loopback VPN**. Traffic does not leave the device — the VPN is pass-through only.

### Step 1 – Deploy the Microsoft Defender App

For unsupervised devices enrolled via standard web-based or standard device enrollment, deploy Microsoft Defender as an **iOS Store App** through Intune. Unlike supervised devices, unsupervised devices cannot receive silent MDM app installs — users will receive a Company Portal notification to install the app.

> **Note:** If your organization uses Apple Business Manager with VPP user-based licensing (Managed Apple IDs assigned to users), you can alternatively deploy Microsoft Defender as a VPP app using the same steps as Section 6, Step 1. Confirm with your ABM administrator whether user-based VPP licensing is configured before choosing this path.

1. In the Intune admin center, go to **Apps > iOS/iPadOS > Add**
2. Select **iOS store app** and click **Select**
3. Search for **Microsoft Defender** in the App Store
4. Select the app and click **Select**
5. Set **Minimum operating system** to **iOS 16.0**
6. Under **Assignments > Required**, add the target device or user group
7. Click **Next**, then **Create**

### Step 2 – Configure Zero Touch (Silent) VPN Onboarding Profile

This profile automatically installs and activates the Defender VPN without requiring the user to open the app.

1. Go to **Devices > Configuration Profiles > Create Profile**
2. Set **Platform** to `iOS/iPadOS`, **Profile type** to `Templates`, **Template name** to `VPN`
3. Click **Create** and provide a name (e.g., `MDE – Silent VPN Onboarding – Unsupervised`)
4. Under **Configuration settings**, configure:

| Setting | Value |
|---|---|
| Connection type | `Custom VPN` |
| Connection name | `Microsoft Defender for Endpoint` |
| VPN server address | `127.0.0.1` |
| Authentication method | `Username and password` |
| Split tunneling | `Disable` |
| VPN identifier | `com.microsoft.scmx` |
| Type of Automatic VPN | `On-demand VPN` |
| On Demand Rules – Action | `Connect VPN` |
| On Demand Rules – Restrict to | `All domains` |

5. Under **Custom VPN attributes**, add the following key-value pair:

| Key | Value |
|---|---|
| `SilentOnboard` | `True` |
| `SingleSignOn` | `True` |

6. **Optional:** To prevent users from disabling the VPN, set **Block users from disabling automatic VPN** to `Yes`
7. **Optional:** To allow users to toggle VPN from within the Defender app, add:

| Key | Value |
|---|---|
| `EnableVPNToggleInApp` | `TRUE` |

8. Click **Next**, assign to the target group, then **Create**

Once synced to devices, the following happens automatically (within ~5 minutes):

- Microsoft Defender for Endpoint is deployed and silently onboarded
- The device appears in the Defender for Endpoint portal
- Web Protection and other features are activated
- A provisional notification is sent to the user

### Step 3 – Deploy App Configuration Policy (Unsupervised Devices)

1. Go to **Apps > App configuration policies > Add > Managed devices**
2. Provide:
   - **Name:** e.g., `iOS_iPadOS - ACP - DEV - Microsoft Defender - BYOD Devices`
   - **Platform:** `iOS/iPadOS`
   - **Targeted app:** `Microsoft Defender for Endpoint`
3. Under **Configuration settings**, select **Use configuration designer**
4. Add the following keys:

| Configuration Key | Value Type | Configuration Value |
|---|---|---|
| `issupervised` | String | `{{issupervised}}` |
| `IntuneMAMUPN` | String | `{{userprincipalname}}` |
| `UserEnrollmentEnabled` | String | `True` |
| `WebProtection` | String | `True` |
| `DefenderTVMPrivacyMode` | String | `True` |
| `DefenderExcludeURLInReport` | Integer | `1` |
| `DefenderNetworkProtectionEnable` | String | `True` |
| `DefenderEndUserTrustFlowEnable` | String | `True` |
| `DefenderNetworkProtectionAutoRemediation` | String | `True` |
| `DefenderNetworkProtectionPrivacy` | String | `True` |
| `DefenderOpenNetworkDetection` | Integer | `2` |
| `DisableSignOut` | String | `True` |
| `DefenderDeviceTag` | String | `iOS-iPadOS-BYOD-MDM` |

5. Click **Next** through Scope tags
6. Under **Assignments**, target **All Devices** (or the appropriate unsupervised device group)
7. Click **Next**, then **Create**

> **Note:** `UserEnrollmentEnabled` = `True` Required for Account-Driven User Enrollment (BYOD)

> **Note:** `UserEnrollmentEnabled` = `False` Required for Web-Based / Standard Device Enrollment (BYOD)

---

## 8. User Enrollment Devices (BYOD)

Apple provides different methods for enrolling personal devices. For Microsoft Defender for Endpoint (MDE), the chosen method drastically changes the onboarding experience and VPN capabilities.

### BYOD Enrollment Methods Comparison

| Enrollment Method | Supported VPN Type | Silent Onboarding | User Interaction | Notes |
|---|---|---|---|---|
| **Account-Driven User Enrollment** | Per-App / Local Loopback | **No** | **Required** (Must click "Allow") | Creates a strict privacy boundary — MDM can only push Per-App VPN, not device-wide VPN |
| **Web-Based Device Enrollment** | Device-wide Loopback | **Yes** | None (if profile is pushed) | Device is unsupervised but not in Apple's User Enrollment privacy mode; silent onboarding via the profile in Section 7 |
| **MAM-Only (Unenrolled)** | Managed App Tunnel | **N/A** | **Required** (Sign-in) | MDE protection delivered exclusively through App Protection Policies — see Section 10 |

### Deployment Steps (Account-Driven User Enrollment)

> **Note:** Because silent onboarding is blocked in this mode, users *must* manually open the MDE app at least once to trigger the Apple "Allow VPN" popup.

1. Ensure the device is enrolled via **Account-Driven User Enrollment** in Intune.
2. **Set up the SSO Plugin (Critical Step):** The Single Sign-On (SSO) app extension is the **identity bridge** for User Enrollment. If this is missing, the MDE app will hang at the "Waiting for setup" screen because it cannot link the device record to the user.
   - Create a Device Configuration profile with a Single Sign-On App Extension (under Device Features).
   - Include the Defender app bundle ID in the app bundle ID list: `com.microsoft.scmx`
   - Add the key-value pair: Key = `device_registration`, Type = `String`, Value = `{{DEVICEREGISTRATION}}`
3. Deploy the Microsoft Defender app via Intune:
   - Account-Driven User Enrollment devices have Managed Apple IDs (ABM requirement). If VPP user-based licensing is configured in your ABM, deploy Defender as a VPP app (Apps > iOS/iPadOS app (Volume Purchase Program)) and assign it as Required to the BYOD user group.
   - If VPP user-based licensing is not configured, deploy as an **iOS Store App** (Apps > iOS/iPadOS > Add > iOS store app, search for Microsoft Defender, assign as Required). Users will install it using their personal Apple ID.
   - Set **Minimum operating system** to **iOS 17.0** for this enrollment type.
4. Create an App Configuration Policy (Managed Devices) and add the `UserEnrollmentEnabled` key set to `True`. *(Note: Only use this key for Account-Driven User Enrollment. Do not use it for Web-Based Device Enrollment).*
5. Include the standard configuration keys from the **App Configuration Policy Keys Reference** table.
6. Assign the policy to the User Enrollment device group.

   > The JIT Registration SSO extension policy created for BYOD enrollment (Section 4.2 of the Playbook) can be updated to include the Defender bundle ID — no separate policy needed.

### Deployment Steps (MAM-Only — Unenrolled Devices)

For personal devices that are not enrolled in Intune, MDE protection is delivered entirely through App Protection Policies. The admin cannot push the app — the user must install it themselves.

> **Note:** This path requires the user to have Microsoft Authenticator installed and registered with their work account (Entra ID) before Defender can be configured.

**Admin steps:**

1. In the Intune admin center, go to **Apps > iOS/iPadOS > Add**
2. Select **iOS store app** and click **Select**
3. Search for **Microsoft Defender** in the App Store and select it
4. Under **Assignments > Available for enrolled devices**, add the MAM user group — this makes the app visible in Company Portal without requiring enrollment
5. Click **Next**, then **Create**
6. Create the App Protection Policy for MDE as described in Section 10 and assign it to the same user group

**User steps:**

1. Install **Microsoft Authenticator** and sign in with their work account
2. Install **Microsoft Defender** from the App Store (personal Apple ID) or from Company Portal
3. Open Defender and sign in with their work account — the App Protection Policy is applied automatically

> **Note:** Unenrolled devices do not receive an ACP (App Configuration Policy) — all MDE configuration for this path is delivered through the App Protection Policy (Section 10). The `UserEnrollmentEnabled` key is not applicable here.

---

## 9. App Configuration Policy Keys Reference

The following table lists all current MDE iOS/iPadOS App Configuration Policy keys as of 2025.

| Key | Value Type | Default | Recommended Value | Description |
|---|---|---|---|---|
| `issupervised` | String | — | `{{issupervised}}` | Identifies if device is supervised. Required for all deployments. |
| `DefenderNetworkProtectionEnable` | String | `True` (enabled) | `True` | Enables Network Protection (Wi-Fi threat detection). Set to `False` to disable. |
| `DefenderTVMPrivacyMode` | String | `True` | `True` | Privacy mode for Threat and Vulnerability Management app inventory. `True` = privacy enabled (unsupervised). |
| `DefenderExcludeURLInReport` | Boolean (MDM) / String (MAM) | `False` | `True` | Excludes domain names from phishing alert reports for user privacy. Use Boolean value type for Managed Devices (MDM); use String for Managed Apps (MAM). |
| `DefenderEndUserTrustFlowEnable` | String | `False` | `True` | Allows users to mark Wi-Fi networks as trusted or untrusted. |
| `DefenderNetworkProtectionAutoRemediation` | String | `True` | `True` | Enables automatic remediation alerts when network configuration changes are detected. |
| `DefenderNetworkProtectionPrivacy` | String | `True` | `True` | Controls whether user consent is shown before sharing malicious network data. `True` = no consent prompt shown. |
| `DefenderOpenNetworkDetection` | Integer | `2` | `2` | Open Wi-Fi network detection: `0` = Disabled, `1` = Audit only, `2` = Enabled. |
| `DefenderDeviceTag` | String | — | `iOS-iPadOS-CORP-MDM` / `iOS-iPadOS-BYOD-MDM` | Tags devices during onboarding for grouping in the Defender portal. Use track-specific values: `iOS-iPadOS-CORP-MDM` for corporate, `iOS-iPadOS-BYOD-MDM` for BYOD. GA since October 2024. |
| `SuppressOSUpdateNotification` | String | `False` | `True` (Corporate only) | Suppresses OS update notifications from Defender. Set to `True` for corporate devices where Intune DDM manages OS updates. Not included in the BYOD policy — BYOD users manage their own OS updates and should receive these notifications. |
| `DisableSignOut` | String | `False` | `False` | Hides the sign-out button in the MDE app. Set to `True` to prevent users from signing out. |
| `EnableVPNToggleInApp` | String | `FALSE` | `TRUE` | Allows users to toggle the VPN connection from within the Defender app (unsupervised devices). |
| `DefenderOptionalVPN` | Boolean | `False` | — | Allows users to skip VPN permission during onboarding. Not recommended for production. |
| `UserEnrollmentEnabled` | String | — | `True` | Required for Apple User Enrollment (Account-Driven) device support. Set this to True **only** for this specific enrollment type. If applied to Web-Based Device Enrollment, it may cause VPN profile conflicts. |
| `SilentOnboard` | String | — | `True` | VPN profile key-value pair that enables zero-touch silent onboarding (unsupervised devices). |

---

## 10. App Protection Policy (Optional)

App Protection Policies (APP) allow MDE risk signals to block or wipe access to managed apps on both MDM-enrolled and MAM-unenrolled devices. This is particularly useful for protecting Microsoft 365 apps on personal devices not enrolled in Intune.

### Create an App Protection Policy

1. In the Intune admin center, go to **Apps > App protection policies > Create policy > iOS/iPadOS**
2. Provide a name (e.g., `MDE – App Protection – iOS`)
3. Under **Apps**, select the Microsoft 365 apps you want to protect (Outlook, Teams, OneDrive, etc.)
4. Under **Conditional launch > Device conditions**, add:

| Setting | Value | Action |
|---|---|---|
| Jailbroken/rooted devices | — | Block access |
| Max allowed device threat level | `Low` | Block access |

5. Under **Assignments > Included groups**, add the relevant user groups
6. Click **Next**, then **Create**

> **Important:** Ensure the Mobile Threat Defense connector is properly configured in Intune before relying on device threat level signals.

---

## 11. Conditional Access with MDE on iOS (Optional)

Conditional Access (CA) policies enforce compliance by requiring devices to meet MDE risk score thresholds before accessing corporate resources.

### Step 1 – Create a Conditional Access Policy

1. In the Microsoft Entra admin center, go to **Protection > Conditional Access > Policies > New policy**
2. Configure **Users** — select the users or groups this policy applies to and any exclusions
3. Under **Cloud apps**, select the apps to protect (e.g., Office 365)
4. Under **Conditions > Device platforms**, select `iOS`
5. Under **Grant**, select **Require device to be marked as compliant**
6. Enable the policy

### Step 2 – Required CA Exclusions for MDE

Certain MDE background services must be excluded from restrictive CA policies to prevent Defender from being blocked when reporting signals.

| App | App ID | Purpose |
|---|---|---|
| **Xplat Broker App** | `a0e84e36-b067-4d5c-ab4a-3db38e598ae2` | Forwards Defender risk signals to the Defender backend. Required for risk signal reporting. |
| **TVM App** | `e724aa31-0f56-4018-b8be-f8cb82ca1196` | Provides vulnerability assessment for installed apps (MDVM). Required if using Vulnerability Assessment. |

**To add exclusions:**

1. In your CA policy, go to **Cloud apps > Exclude**
2. Select **Select excluded cloud apps**
3. Search by App ID and add both apps listed above
4. If these apps are not yet registered, create service principal objects using the App IDs above via Microsoft Entra ID

> **Note:** Xplat Broker App is also used by macOS and Linux platforms. If your CA policy applies to these platforms as well, create a separate iOS-specific policy to avoid unintended exclusions.

---

## 12. Jailbreak Detection

MDE performs periodic jailbreak checks on enrolled iOS/iPadOS devices. When a jailbreak is detected:

1. A **High severity alert** is sent to the Microsoft Defender portal
2. The device is **blocked from corporate data** (if Conditional Access is configured)
3. Managed app data may be **wiped** based on App Protection Policy settings
4. The MDE loopback VPN profile is **deleted**
5. **Web protection is deactivated** on the device

### Configure Intune Compliance Policy for Jailbreak

1. In the Intune admin center, go to **Devices > Compliance policies > Create Policy**
2. Set **Platform** to `iOS/iPadOS`
3. Under **Device Health**, set **Jailbroken devices** to `Block`
4. Configure **Actions for noncompliance** (e.g., mark device as noncompliant, notify user, remotely lock)
5. Under **Assignments**, target the appropriate device group
6. Click **Next**, then **Create**

---

## 13. Network Protection

Network Protection guards against Wi-Fi-based threats including:

- **Open/unsecured Wi-Fi networks**
- **Rogue Wi-Fi access points** (evil twin attacks)
- **Certificate-based phishing attacks** via HTTPS inspection

### May 2025 Update — Alert Behavior Change

> As of **May 19, 2025**, open wireless network alerts are **no longer generated in the Microsoft Defender portal**. These events now appear in the **device timeline** instead. If you had existing open network alerts in the portal, they are auto-resolved when `DefenderNetworkProtectionAutoRemediation = True` is configured. Multiple connections to the same network within a 24-hour period produce only one timeline event (deduplication).

### Configuration Keys

| Key | Value Type | Options | Description |
|---|---|---|---|
| `DefenderNetworkProtectionEnable` | String | `True` / `False` | Enable or disable Network Protection entirely |
| `DefenderOpenNetworkDetection` | Integer | `0` = Disabled, `1` = Audit, `2` = Enabled | Controls detection of open/unsecured Wi-Fi networks |
| `DefenderEndUserTrustFlowEnable` | String | `True` / `False` | Allow users to mark networks as trusted or untrusted |
| `DefenderNetworkProtectionAutoRemediation` | String | `True` / `False` | Auto-resolve open network alerts; log to device timeline |
| `DefenderNetworkProtectionPrivacy` | String | `True` / `False` | `True` = no user consent before sharing network threat data |

---

## 14. Privacy Controls

MDE provides several privacy controls that can be configured to meet organizational and regulatory requirements.

### Phishing Report Privacy

Controls whether domain names appear in phishing alerts sent to the Defender portal.

| Key | Value Type | Value | Effect |
|---|---|---|---|
| `DefenderExcludeURLInReport` | String | `True` | Domain names are **excluded** from phishing alert reports |
| `DefenderExcludeURLInReport` | String | `False` (default) | Domain names are **included** in phishing alerts |

When set to `True`, a user-facing toggle appears in **Settings > Privacy > Unsafe Site Info** within the Defender app.

> **Note:** On supervised devices, MDE has access to the full URL for phishing detection. On unsupervised devices, only the domain name is checked due to platform privacy restrictions.

### Network Protection Privacy

Controls whether a user consent prompt appears before MDE shares malicious network information.

| Key | Value | Effect |
|---|---|---|
| `DefenderNetworkProtectionPrivacy` | `True` (default) | No user consent prompt shown; data shared automatically |
| `DefenderNetworkProtectionPrivacy` | `False` | User consent required before sharing network threat data |

### Vulnerability Assessment Privacy

Controls the privacy mode for Threat and Vulnerability Management (TVM) app inventory collection.

| Key | Value | Effect |
|---|---|---|
| `DefenderTVMPrivacyMode` | `True` (default for unsupervised) | Privacy mode enabled; user approval screen shown |
| `DefenderTVMPrivacyMode` | `False` | Full app inventory collected without user prompt |

---

## 15. References and Resources

### Microsoft Learn Documentation

- [Deploy Microsoft Defender for Endpoint on iOS with Microsoft Intune](https://learn.microsoft.com/en-us/defender-endpoint/ios-install)
- [Configure Microsoft Defender for Endpoint on iOS features](https://learn.microsoft.com/en-us/defender-endpoint/ios-configure-features)
- [Microsoft Defender for Endpoint on iOS – Overview](https://learn.microsoft.com/en-us/defender-endpoint/microsoft-defender-endpoint-ios)
- [What's new in Microsoft Defender for Endpoint on iOS](https://learn.microsoft.com/en-us/defender-endpoint/ios-whatsnew)
- [Microsoft Defender Mobile App exclusion from Conditional Access Policies](https://learn.microsoft.com/en-us/defender-endpoint/ios-configure-features#exclude-microsoft-defender-mobile-app-from-conditional-access-ca-policies)

### Microsoft Tech Community

- [Zero Touch Enrollment of MDE on iOS/iPadOS devices managed by Intune](https://techcommunity.microsoft.com/blog/coreinfrastructureandsecurityblog/zero-touch-enrollment-of-mde-on-iosipados-devices-managed-by-intune/4033722)
- [Onboarding Intune Managed iOS User Enrollment Devices to MDE](https://techcommunity.microsoft.com/blog/coreinfrastructureandsecurityblog/onboarding-intune-managed-ios-user-enrollment-devices-to-microsoft-defender-for-endpoint/4020858)
- [Simplifying Compliance Remediation with Intune and Defender on iOS/iPadOS](https://techcommunity.microsoft.com/blog/intunecustomersuccess/simplifying-compliance-remediation-with-microsoft-intune-and-defender-on-iosipad/4465293)
- [Protect Unmanaged or 3rd Party MDM iOS Devices with MDE](https://techcommunity.microsoft.com/blog/coreinfrastructureandsecurityblog/protect-unmanaged-or-3rd-party-mdm-managed-iosandroid-devices-with-mde/4057691)

---

*This document is part of the UniFy Endpoint – Microsoft Intune iOS/iPadOS Baseline series. Document maintained by Yoennis Olmo - Sr. Modern Work Consultant.*
