# iOS/iPadOS Corporate Device Deployment Guide – Microsoft Intune

| | |
|---|---|
| **Document Type** | Deployment Instruction – Corporate Track |
| **Author** | Sr. Modern Work Consultant |
| **Last Updated** | May 2026 |
| **Applies To** | iOS/iPadOS 15.0 and later, Microsoft Intune, ADE/Supervised |

---

## Table of Contents

1. [Overview & Scope](#1-overview--scope)
2. [Licensing Requirements](#2-licensing-requirements)
3. [Network and Firewall Requirements](#3-network-and-firewall-requirements)
4. [Prerequisites](#4-prerequisites)
5. [Deployment & Configuration](#5-deployment--configuration)
   - [5.1 Apple MDM Push Certificate](#51-apple-mdm-push-certificate)
   - [5.2 Apple Business Portal](#52-apple-business-portal)
   - [5.3 ADE Token – Automated Device Enrollment](#53-ade-token--automated-device-enrollment)
   - [5.4 VPP / Content Token](#54-vpp--content-token)
6. [Groups, Filters, and Policy Assignments](#6-groups-filters-and-policy-assignments)
   - [6.1 Entra Group Architecture](#61-entra-group-architecture)
   - [6.2 Intune Assignment Filters](#62-intune-assignment-filters)
   - [6.3 Policy Assignments](#63-policy-assignments)
7. [Corporate Device Enrollment](#7-corporate-device-enrollment)
   - [7.1 Step 1 – Sync ADE Token and Import Devices](#71-step-1--sync-ade-token-and-import-devices)
   - [7.2 Step 2 – Create ADE Enrollment Profile](#72-step-2--create-ade-enrollment-profile)
   - [7.3 Step 3 – Configure Setup Assistant Screens](#73-step-3--configure-setup-assistant-screens)
   - [7.4 Step 4 – Assign Enrollment Profile to Devices](#74-step-4--assign-enrollment-profile-to-devices)
   - [7.5 Device Activation Walkthrough](#75-device-activation-walkthrough)
   - [7.6 Managed Apple ID Integration](#76-managed-apple-id-integration)
8. [App Management](#8-app-management)
   - [8.1 Authentication Broker – Authenticator vs Company Portal](#81-authentication-broker--authenticator-vs-company-portal)
   - [8.2 Microsoft Authenticator Deployment](#82-microsoft-authenticator-deployment)
   - [8.3 VPP App Deployment](#83-vpp-app-deployment)
9. [Entra ID Federation Setup](#9-entra-id-federation-setup)
10. [References](#10-references)

---

## 1. Overview & Scope

This guide covers end-to-end enrollment and configuration for iOS and iPadOS corporate-owned devices in Microsoft Intune. All corporate devices are enrolled as **supervised** through Apple Automated Device Enrollment (ADE) — this gives the organization the broadest iOS/iPadOS management capabilities: locked enrollment, silent app installation, and restrictions unavailable on unsupervised devices.

This guide covers **operational procedures** — the steps an administrator takes in the Intune admin center and Apple Business portal. For additional policy and configuration detail, refer to the supplementary guides:

| Document | Purpose |
|---|---|
| [iOS_App_Protection_Policy_Guide.md](iOS_App_Protection_Policy_Guide.md) | App Protection Policy (MAM) data protection levels |
| [iOS_App_Configuration_Policy_Guide.md](iOS_App_Configuration_Policy_Guide.md) | App Configuration key-value pairs for M365 apps |
| [Defender_for_Endpoint_iOS_iPadOS_Devices.md](Defender_for_Endpoint_iOS_iPadOS_Devices.md) | MDE onboarding for iOS |

For personal/BYOD device enrollment, refer to [iOS_iPadOS_BYOD_Deployment_Guide.md](iOS_iPadOS_BYOD_Deployment_Guide.md).

### What This Guide Does Not Cover

- BYOD or personally-owned device enrollment
- macOS Enrollment
- Certificate (SCEP/PKCS) Enrollment
- Wi-Fi Network / VPN Deployment

---

## 2. Licensing Requirements

Every enrolled user or device requires **Microsoft Intune Plan 1** and **Microsoft Entra ID P1**. Both are included in most Microsoft 365 Enterprise and EMS bundles. Microsoft 365 F1 includes only the free Entra tier — Conditional Access requires an Entra P1 add-on for F1 users.

**Intune license tiers:**

| License | What it adds |
|---|---|
| Intune Plan 1 | Core MDM/MAM — enrollment, configuration, compliance, app deployment |
| Intune Plan 2 (add-on) | Microsoft Tunnel for MAM, firmware-over-the-air updates |
| Intune Suite (add-on) | Plan 2 + Endpoint Privilege Management, Remote Help, Cloud PKI, Advanced Analytics |
| Device-Only Subscription | Per-device for kiosk/shared devices with no user (no MAM, no user-based CA) |

**Common bundles — what's included:**

| Bundle | Intune | Entra ID | Defender for Endpoint |
|---|---|---|---|
| Microsoft 365 Business Premium | Plan 1 | P1 | Business edition |
| Microsoft 365 E3 | Plan 1 | P1 | — |
| Microsoft 365 E5 | Plan 1 | P2 | Plan 2 |
| Microsoft 365 E7 | Intune Suite | Entra Suite | Plan 2 |
| Microsoft 365 F1 | Plan 1 | Free | — |
| Microsoft 365 F3 | Plan 1 | P1 | — |
| EMS E3 | Plan 1 | P1 | — |
| EMS E5 | Plan 1 | P2 | Included via Defender for Identity |

**Apple MDM Requirements:**

| Requirement | Purpose |
|---|---|
| Apple MDM Push Certificate | Required for all iOS/iPadOS enrollment; renew annually |
| Apple Business Account | Required for ADE (supervised enrollment) and VPP app purchasing |
| VPP / Content Token | Required for silent app deployment on supervised devices |

---

## 3. Network and Firewall Requirements

Ensure the following endpoints are accessible from enrolled devices and from your Intune infrastructure. Block or proxy rules that intercept Apple APNs traffic will break MDM communication.

| Endpoint | Purpose |
|---|---|
| `*.push.apple.com` (TCP 443 and 2197) | Apple Push Notification service (APNs) |
| `*.manage.microsoft.com` | Intune service communication |
| `*.microsoftonline.com` | Entra ID authentication |
| `*.akamaiedge.net`, `*.appattest.apple.com` | Device attestation and app installation |
| `itunes.apple.com`, `apps.apple.com` | App Store and VPP app delivery |

> **Important:** APNs traffic must not be SSL inspected or intercepted. Devices communicate directly with Apple over a persistent TCP connection. Any proxy that terminates and re-establishes this connection will break device management.

---

## 4. Prerequisites

Licensing and network requirements are covered in Sections 2–3. The items below are operational setup steps that must be complete before configuring enrollment.

| Prerequisite | Details |
|---|---|
| Apple MDM Push Certificate | Annual renewal required |
| MDM Authority set to Intune | One-time tenant configuration |
| Microsoft Authenticator deployed | Required for JIT registration and authentication |
| Apple Business account | Requires a D-U-N-S number to register; enables ADE, device assignment, and VPP |
| ADE Token (.p7m) | Links Apple Business to Intune; enables zero-touch enrollment |
| VPP / Content Token | Required for silent app deployment |
| Devices assigned in Apple Business | Devices must be in out-of-box state and assigned to your ADE token before activation |

---

## 5. Deployment & Configuration

### 5.1 Apple MDM Push Certificate

The Apple MDM Push Certificate authorizes Microsoft Intune to communicate with Apple devices. Without it, no device can enroll or receive policy.

**Initial setup:**

1. In the Intune admin center, go to `Devices > Enrollment > Apple > Apple MDM Push certificate`
2. Select **Download your CSR** and save the `.csr` file locally
3. Open [Apple Push Certificates Portal](https://identity.apple.com/pushcert) using your corporate Apple ID
4. Select **Create a Certificate**, accept the terms, and upload the `.csr` file
5. Download the resulting `.pem` certificate file
6. Return to Intune and upload the `.pem` file to complete the configuration

**Annual renewal:**

The certificate expires every 365 days. Renew it **before** expiry using the **same Apple ID** used to create it. If the Apple ID changes or the certificate expires before renewal, all enrolled devices will lose MDM connectivity and must be re-enrolled.

> **Best practice:** Use a dedicated service account Apple ID (e.g., `apple-mdm-cert@yourcompany.com`) for the Apple MDM certificate and tokens. Personal Apple IDs tied to an individual employee create a business continuity risk if that employee leaves.

Set a calendar reminder 30–60 days before the certificate expiry date.

---

### 5.2 Apple Business Portal

Apple consolidated Apple Business Manager (ABM), Apple Business Essentials (ABE), and Apple Business Connect into a single unified portal at [business.apple.com](https://business.apple.com). The portal is organized around two areas: **Run** for IT device management operations, and **Grow** for customer-facing functions.

To register your organization:

1. Navigate to [business.apple.com](https://business.apple.com) and select **Enroll Now**
2. Your organization must have a valid **D-U-N-S number** — register at [dnb.com](https://www.dnb.com) if you do not have one (allow 5–7 business days for processing)
3. During registration, use a **corporate Apple ID** (not a personal Apple ID) — this ID is tied to your organization's Apple relationship and must remain active
4. Once approved, create admin and manager accounts for your IT team
5. Link your Apple Business account to Microsoft Intune by creating an MDM server (see Section 5.3)

> **Important:** If your organization purchases Apple devices from a reseller, ensure the reseller is set up to assign devices directly to your Apple Business account. Devices purchased from retail Apple Stores can be added manually using serial numbers or purchase order numbers within Apple Business.

---

### 5.3 ADE Token – Automated Device Enrollment

The ADE token (`.p7m` file) is a trust credential that links your Apple Business account to your Intune tenant. It is required to sync corporate-owned devices and create supervised enrollment profiles.

**Step 1 — In Intune: Download the public key**

1. In Intune admin center, go to `Devices > Enrollment`
2. Select the **Apple mobile** tab, then under **Bulk Enrollment Methods** select **Enrollment program tokens**
3. Select **Add**
4. Select **I agree** to grant Microsoft permission to send user and device information to Apple
5. Select **Download the Intune public key certificate** and save the `.pem` file locally

> Keep the Intune browser tab open throughout this process. If you close it, the downloaded certificate is invalidated and you must restart from step 3.

**Step 2 — In Apple Business: Add Intune as an MDM server**

6. In Intune, select the **Create a token via Apple Business** link — this opens Apple Business in a new browser tab. Alternatively, navigate directly to [business.apple.com](https://business.apple.com)
7. Sign in with your organization's corporate Apple ID — not a personal Apple ID. This Apple ID must remain accessible to your team for future annual token renewals
8. Go to **Devices > Management Services > External Device Management > Connect**
9. Enter a name for the server (e.g., `Microsoft Intune`) — this name is for your reference in Apple Business and does not need to match any Intune URL
10. Upload the public key `.pem` file downloaded in step 5
11. Review the **Allow this service to release devices** option — see the note below
12. Save the MDM server configuration and download the resulting server token (`.p7m` file)
13. On the MDM server detail page, select **Edit** next to **Default Device Assignment** — all device types default to **Unassigned** and must be configured manually. Select the MDM server for each device type this token will manage. For an iOS/iPadOS deployment, assign at minimum **iPhone** and **iPad**; add **Mac** only if macOS devices are also managed through this same Intune token. Device types left as Unassigned will not be automatically routed to this MDM server when they appear in Apple Business

**Step 3 — Back in Intune: Complete the token setup**

14. Return to the Intune admin center tab
15. In the **Apple ID** field, enter the Apple ID used to sign in to Apple Business in step 7
16. In the **Apple token** field, browse to and upload the `.p7m` file downloaded in step 12
17. Select **Create** — Intune will automatically sync device information from Apple Business

> **Allow this service to release devices:** This option is **disabled by default** and should remain disabled for most corporate deployments. When enabled, Intune can programmatically release a device from your organization's Apple Business account without requiring manual action in the portal. Releasing a device is a permanent and destructive action — the device is removed from Apple Business entirely. **Do not release devices that are pending Apple repair** — if Apple replaces the device during repair, the replacement will not appear in Apple Business. Leave this option disabled and perform any necessary device releases manually in Apple Business (`Devices > Inventory > Release from Organization`). Enable it only if your organization requires Intune-initiated release as part of a managed offboarding or device trade-in workflow.

**Token limits to be aware of:**

| Limit | Value |
|---|---|
| Enrollment policies per token | 1,000 |
| Devices per token | 200,000 |
| Tokens per Intune tenant | 2,000 |

Tokens expire annually — renew before expiry to avoid enrollment disruption. Intune sends alerts when a token is approaching expiry.

---

### 5.4 VPP / Content Token

The Volume Purchase Program (VPP) token, now referred to as a **Content Token** in Apple Business, allows Intune to deploy and manage apps purchased through Apple Business without requiring users to enter an Apple ID at installation.

1. In Apple Business portal, [business.apple.com](https://business.apple.com) select your name in the top-right corner, then go to **Settings > Payments & Billing > Content Tokens**
2. Download the token (`.vpptoken` file) for your location
3. In Intune, go to `Tenant administration > Connectors and tokens > Apple VPP tokens > Create`
4. Configure the following fields:

| Field | Value | Notes |
|---|---|---|
| Token name | e.g., `VPP-Corporate-[Region]` | Use a descriptive name; visible to admins in the token list |
| Apple ID | The Apple ID used to download the token | Must match the account that generated the token in Apple Business; stored for reference only |
| VPP token file | Upload the `.vpptoken` file downloaded in step 2 | Required — the configuration cannot proceed without the token file |
| Type of VPP account | **Business** or **Education** | Required — must be selected before saving; choose Business for Apple Business deployments and Education for Apple School Manager deployments |
| Automatic app updates | **Yes** | Keeps deployed apps current without manual intervention |
| Country/region | Match your Apple Business account region | Must align with the region set in Apple Business |
| I grant Microsoft permission to send both user and device information to Apple | **Checked** | Required consent — must be selected to complete the configuration. When any Apple service is enabled, Intune shares data with Apple including device serial numbers, organization details, and VPP-specific identifiers. See [Data Intune sends to Apple](https://learn.microsoft.com/en-us/intune/privacy/data-sharing/ref-intune-to-apple) for the full list of data shared. |

> **Device licensing vs user licensing:** Apps deployed via VPP use either **device licensing** (no Apple ID required, recommended for supervised devices) or **user licensing** (Apple ID required per user). Use device licensing for all corporate-owned supervised deployments. Device-licensed apps install silently through the MDM channel and update automatically — users never see an App Store prompt.

> **iOS Store Apps are not compatible with supervised device deployment:** On ADE-enrolled supervised devices, all apps must be sourced from VPP. When an iOS Store App is assigned as Required to a supervised device, iOS intercepts the installation and requires the user to authenticate with a personal Apple ID — this is an iOS platform behavior that MDM restrictions cannot override. The authentication prompt will reappear each time the device checks in until the iOS Store App assignment is replaced with a VPP assignment using device licensing.

---

## 6. Groups, Filters, and Policy Assignments

Create these groups and filters before configuring enrollment. They are used as assignment targets throughout Section 7.

---

### 6.1 Entra Group Architecture

This section defines the group structure for the corporate device track. Use this as the source of truth for all policy assignments.

| Tool | Purpose | Speed | Where Used |
|---|---|---|---|
| **Entra Dynamic Group** | Determines which devices are in a group (membership). Used as the policy assignment target. | Slow — evaluated asynchronously, can take minutes to hours | Policy assignments, CA policies, App Protection Policies |
| **Intune Assignment Filter** | Refines which devices within an already-assigned group actually receive the policy, evaluated at device check-in | Fast — evaluated at device check-in | Settings Catalog, Compliance, App Config — not CA |

> **Critical syntax difference:** In Entra dynamic groups, corporate devices use `device.deviceOwnership -eq "Company"`. In Intune assignment filters, the same devices use `device.deviceOwnership -eq "Corporate"`. Different strings, same concept — one typo silently breaks group membership.

#### Device Group

| Group Name | Membership Type | Description |
|---|---|---|
| `iOS - DEV - Corporate-Owned-Devices` | Dynamic device | This group includes all iOS ADE Corporate-Owned supervised devices |

**Dynamic rule — `iOS - DEV - Corporate-Owned-Devices`:**

```
((device.deviceOSType -eq "iPhone") or (device.deviceOSType -eq "iPad")) and (device.managementType -eq "MDM") and (device.deviceOwnership -eq "Company") and (device.enrollmentProfileName -eq "iOS_iPadOS-ADE-DP-Corporate")
```

**To create a dynamic device group:**

1. In Entra admin center, go to `Groups > New group`
2. Set **Group type** to **Security** and **Membership type** to **Dynamic device**
3. Enter the dynamic rule in the **Rule syntax** text box (not the rule builder — device rules require direct text entry)
4. Validate the rule syntax, then save

> **Propagation timing:** Dynamic group membership is evaluated asynchronously. After enrollment, a device may take minutes to hours to appear in the group. For time-critical first deployments, you can temporarily add the device to an assigned fallback group while the dynamic group populates.

---

### 6.2 Intune Assignment Filters

Create this filter in Intune and reuse it across all corporate policy assignments. Filters are evaluated at device check-in — significantly faster than dynamic group evaluation.

> **Where to configure:** Intune admin center > `Tenant administration > Filters > Create`

| Filter Name | Platform | Rule | Purpose |
|---|---|---|---|
| `iOS-ADE-DEV` | iOS/iPadOS | `(device.deviceOwnership -eq "Corporate")` | Refine corporate policy assignments within a broad group |

**How to apply a filter at assignment time:**

1. Open the policy > **Properties > Assignments > Add group**
2. Select the target group
3. Under **Filter**, select **Include filtered devices in assignment**
4. Choose the `iOS-ADE-DEV` filter from the list

---

### 6.3 Policy Assignments

With groups and filters in place, assign each baseline policy to its target group.

#### Settings Catalog

| Policy | Target Group | Filter |
|---|---|---|
| `iOS_iPadOS - SC - DEV - Accessibility & Sharing - Corporate Devices` | `iOS - DEV - Corporate-Owned-Devices` | `iOS-ADE-DEV` |
| `iOS_iPadOS - SC - DEV - App Management - Corporate Devices` | `iOS - DEV - Corporate-Owned-Devices` | `iOS-ADE-DEV` |
| `iOS_iPadOS - SC - DEV - Apple Intelligence & Siri - Corporate Devices` | `iOS - DEV - Corporate-Owned-Devices` | `iOS-ADE-DEV` |
| `iOS_iPadOS - SC - DEV - Apple Services - Corporate Devices` | `iOS - DEV - Corporate-Owned-Devices` | `iOS-ADE-DEV` |
| `iOS_iPadOS - SC - DEV - Connectivity Controls - Corporate Devices` | `iOS - DEV - Corporate-Owned-Devices` | `iOS-ADE-DEV` |
| `iOS_iPadOS - SC - DEV - Data Protection - Corporate Devices` | `iOS - DEV - Corporate-Owned-Devices` | `iOS-ADE-DEV` |
| `iOS_iPadOS - SC - DEV - Device Pairing - Corporate Devices` | `iOS - DEV - Corporate-Owned-Devices` | `iOS-ADE-DEV` |
| `iOS_iPadOS - SC - DEV - Device Privacy - Corporate Devices` | `iOS - DEV - Corporate-Owned-Devices` | `iOS-ADE-DEV` |
| `iOS_iPadOS - SC - DEV - Device Restrictions - Corporate Devices` | `iOS - DEV - Corporate-Owned-Devices` | `iOS-ADE-DEV` |
| `iOS_iPadOS - SC - DEV - Device Security - Corporate Devices` | `iOS - DEV - Corporate-Owned-Devices` | `iOS-ADE-DEV` |
| `iOS_iPadOS - SC - DEV - iCloud & Storage - Corporate Devices` | `iOS - DEV - Corporate-Owned-Devices` | `iOS-ADE-DEV` |
| `iOS_iPadOS - SC - DEV - Lock Screen - Corporate Devices` | `iOS - DEV - Corporate-Owned-Devices` | `iOS-ADE-DEV` |
| `iOS_iPadOS - SC - DEV - Safari Browser - Corporate Devices` | `iOS - DEV - Corporate-Owned-Devices` | `iOS-ADE-DEV` |
| `iOS_iPadOS - SC - DEV - Software Update - Corporate Devices` | `iOS - DEV - Corporate-Owned-Devices` | `iOS-ADE-DEV` |
| `iOS_iPadOS - SC - DEV - Web-App-Store (EU) - Corporate Devices` | `iOS - DEV - Corporate-Owned-Devices` | `iOS-ADE-DEV` |
| `iOS_iPadOS - SC - DEV - Microsoft Enterprise SSO - All Devices` | `iOS - DEV - Corporate-Owned-Devices` | `iOS-ADE-DEV` |

#### Compliance Policies

| Policy | Target Group | Filter |
|---|---|---|
| `iOS_iPadOS - CP - DEV - Compliance - Corporate Devices` | `iOS - DEV - Corporate-Owned-Devices` | `iOS-ADE-DEV` |
| `iOS_iPadOS - CP - DEV - Compliance - MDE - Corporate Devices` | `iOS - DEV - Corporate-Owned-Devices` | `iOS-ADE-DEV` |

#### App Configuration

| Policy | Target Group | Filter |
|---|---|---|
| `iOS_iPadOS - ACP - DEV - Microsoft Defender - Corporate Devices` | `iOS - DEV - Corporate-Owned-Devices` | `iOS-ADE-DEV` |
| `iOS_iPadOS - ACP - DEV - Microsoft Authenticator - Shared Device Mode` | Not assigned — assign to a dedicated shared device group if Shared Device Mode is deployed | — |

#### Microsoft Defender Device Configuration

| Policy | Target Group | Filter |
|---|---|---|
| `iOS_iPadOS - CP - DEV - Zero-Touch-Control-Filter - MDE - Corporate Devices` | `iOS - DEV - Corporate-Owned-Devices` | `iOS-ADE-DEV` |

#### Enrollment Profile

Enrollment profiles target device serial numbers directly — they are not assigned to Entra groups.

| Profile | Assignment Target | How to Assign |
|---|---|---|
| `iOS_iPadOS-ADE-DP-Corporate` | Device serial numbers | In Intune: `Devices > Enrollment > Apple > Enrollment program tokens > [token] > Devices` — select the device and choose **Assign profile**. Not assigned to Entra groups. |

#### Enrollment Restrictions

| Restriction | Assignment Target | Notes |
|---|---|---|
| Device type restriction — allow iOS Corporate | All users / default | Confirm the default restriction does not block iOS/iPadOS Corporate-owned devices. ADE enrollment fails with an "Invalid Profile" error if iOS is blocked at the platform level regardless of device ownership type. |

> **Conditional Access policies** are Entra policies — configure user scope directly in the Entra admin center, not through Intune group assignments.

---

## 7. Corporate Device Enrollment

Supervised mode gives the organization the broadest iOS/iPadOS management capabilities — locked enrollment, silent app installation, and restrictions that are unavailable on unsupervised devices.

**Before starting:** All Section 5 prerequisites must be configured in Intune and Apple Business Portal before proceeding.

- Apple MDM Push Certificate
- Apple Business ADE token and VPP token configuration
- Intune integration with Apple Business

---

### 7.1 Step 1 – Sync ADE Token and Import Devices

Syncing the ADE token pulls the device inventory from Apple Business into Intune. Only devices that have been assigned to your Intune MDM server in Apple Business will appear after the sync — devices that are unassigned or assigned to a different MDM server will not sync regardless of how many times you refresh.

**Part 1 — Assign devices in Apple Business**

Before syncing the token, confirm that all devices you want Intune to manage are assigned to your Intune MDM server in Apple Business.

1. In Apple Business, go to **Devices > Inventory**
2. Select the devices you want Intune to manage — filter by serial number and select multiple devices at once
3. Select **Edit device management** and assign the selected devices to the MDM server you created in Section 5.3

> Devices purchased through Apple or authorized resellers are typically pre-assigned to your Apple Business account. Devices acquired through other channels can be added in Apple Business using Apple Configurator.

**Part 2 — Sync the token in Intune**

1. In Intune admin center, go to `Devices > Enrollment > Apple > Enrollment program tokens`
2. Select your token and choose **Sync**
3. Wait for the sync to complete — large inventories may take a few minutes
4. Select **Devices** under the token to confirm your devices are listed with their serial numbers

> Intune performs automatic background syncs with Apple Business, but a manual sync is recommended before any bulk deployment or after newly purchased devices have been assigned. If a device still does not appear after syncing, confirm in Apple Business that it is assigned to the correct MDM server — this is the most common cause of missing devices.

---

### 7.2 Step 2 – Create ADE Enrollment Profile

The enrollment profile defines the settings applied to devices during the Setup Assistant experience. Each token can hold up to 1,000 profiles.

**Navigation:** `Devices > Enrollment > Apple > Enrollment program tokens > [your token] > Profiles > Create profile > iOS/iPadOS`

#### Basics Tab

| Setting | Recommended Value | Notes |
|---|---|---|
| Name | `iOS_iPadOS-ADE-DP-Corporate` | Use a descriptive naming convention |
| Description | Optional | Visible to admins only |

#### Settings Tab

| Setting | Recommended Value | Notes |
|---|---|---|
| User Affinity | **Enroll with User Affinity** | For devices assigned to individual users. Two additional options exist: **Enroll without User Affinity** (kiosk or shared-utility devices with no assigned user) and **Enroll with Microsoft Entra ID shared mode** (frontline shared devices — see iOS_iPadOS_Shared_Device_Kiosk_Guide.md) |
| Authentication Method | **Setup Assistant with Modern Authentication** | Recommended over legacy Setup Assistant |
| Supervised | **Yes** | Enables full management; required for most restrictions |
| Locked Enrollment | **Yes** | Prevents users from removing the MDM profile |
| Sync with computers | **Deny All** | Controls whether the device can sync with a Mac or PC via Finder or iTunes. Deny All blocks USB-based data access and sideloading. Set to Allow Apple Configurator by certificate only if your organization uses Apple Configurator alongside Intune for additional device configuration workflows |
| Apple Configurator certificates | Not applicable | Only available when Sync with computers is set to Allow Apple Configurator by certificate. Upload the Apple Configurator pairing certificate if required for your workflow |
| Await Final Configuration | **Yes** | Holds the device in a locked experience at the end of Setup Assistant while Intune pushes device configuration policies. Only device configuration policies are installed during this window — applications are not included and will install after the device reaches the home screen. Most devices are released within 15 minutes; time varies based on the number of policies assigned |
| Install Company Portal | **Yes** | Required for compliance reporting |
| Install Company Portal with VPP | Select your VPP token | Ensures silent install without App Store login |
| Shared iPad | No | Set to No for all standard corporate profiles |
| Activate with cellular plan | No | Set to Yes only for eSIM-enabled devices where the carrier has pre-provisioned activations for each device. When enabled, enter the carrier's activation server URL. Requires iOS/iPadOS 13.0+. Carriers must provision activations before this command will succeed — contact your carrier before enabling |
| Apply device name template | **Yes** | Recommended for inventory management |
| Device Name Template | `{{DEVICETYPE}}-{{SERIAL}}` | Results in names like `iPhone-F2LM123456` |

> **Authentication method note:** Setup Assistant with Modern Authentication (formerly "Setup Assistant with Azure AD") is the currently recommended method. It performs Entra ID registration during Setup Assistant before the user reaches the home screen, ensuring the device is registered and compliant from the moment it is first used. The legacy Setup Assistant method is still available but not recommended for new deployments.

> **Install Company Portal** set to Yes in the enrollment profile. Intune installs it automatically during ADE enrollment. Do not create a separate app assignment — doing so causes a conflict that prompts users to sign in.

After completing the Settings tab, select **Next** to proceed to the **Setup Assistant** tab — covered in Section 7.3 below. Do not select **Create** until both tabs are configured.

---

### 7.3 Step 3 – Configure Setup Assistant Screens

This step continues the enrollment profile wizard from Section 7.2 — you are now on the **Setup Assistant** tab within the same wizard. Configure which screens appear when the device is powered on for the first time (or after a wipe). Screens you hide are skipped during Setup Assistant, but the corresponding settings are still enforced by Intune policies after enrollment — hiding a screen does not remove the policy control.

#### Department Information

Complete the two department fields at the top of the Setup Assistant tab before configuring the screen list. These values are displayed to users on the device About screen (`Settings > General > About`).

| Field | Recommended Value | Notes |
|---|---|---|
| Department | e.g., `IT Department` | Organization or IT team name; visible to users on the About screen and in the Company Portal device details |
| Department Phone | e.g., `+XX-XXX-XX-XX` | IT helpdesk or support number displayed on the About screen; use a number that is staffed and monitored |

#### Setup Assistant Screens

| Screen | Recommendation | iOS Availability | Notes |
|---|---|---|---|
| Passcode | **Hide** | iOS 7.0+ (see note) | Passcode and password lock setup pane; prompts the user to create a device passcode. Hide on all modern devices — enforce via Compliance policy or DDM Passcode profile instead (see platform limitation note below) |
| Location Services | **Show** | iOS 7.0+ | Location services setup pane; user enables or disables location on the device. Shown to allow per-app location permission prompts to surface correctly during initial setup |
| Restore | **Hide** | iOS 7.0+ | Apps and data setup pane; user can restore from iCloud Backup or transfer data from another device. Hidden to ensure devices provision from a clean state — corporate devices should not restore from personal backups |
| Android Migration | **Hide** | iOS 9.0+ | Migration pane for users moving from an Android device. Not applicable in an enterprise context |
| Apple ID | **Show** | iOS 7.0+ | Sign-in pane for a Managed or personal Apple ID to access iCloud. Shown to allow users to authenticate with their Managed Apple ID during provisioning when the organization requires it. See Section 7.6 for the full Managed Apple ID workflow. Organizations that do not use Managed Apple IDs on corporate devices may hide this screen |
| Terms and Conditions | **Hide** | iOS 7.0+ | Apple terms and conditions pane; user must accept before proceeding. Reduces friction — T&C acceptance is implicit via ADE enrollment |
| Touch ID and Face ID | **Show** | iOS 8.1+ (see note) | Biometric setup pane; user configures fingerprint or Face ID. Shown to allow biometric setup during provisioning — Face ID / Touch ID configured at this step is available immediately for authentication and corporate app access. See platform limitation note below |
| Apple Pay | **Hide** | iOS 7.0+ | Apple Pay setup pane; user can add a payment card. Not relevant for most corporate device deployments |
| Zoom | **Hide** | iOS 7.0+ (deprecated iOS 17) | Accessibility zoom setup pane. Deprecated in iOS 17 — hide if present in your UI; no effect on current iOS versions |
| Siri | **Hide** | iOS 7.0+ | Siri setup pane. Controlled via Restrictions configuration policy |
| Diagnostics Data | **Hide** | iOS 7.0+ | Diagnostics opt-in pane; user can choose to send diagnostic data to Apple. Controlled by your organization's data policy |
| Display Tone | **Hide** | iOS 11.0+ (deprecated iOS 15) | True Tone display setup pane. Deprecated in iOS 15 — hide if present in your UI; no effect on current iOS versions |
| Home Button | **Hide** | iOS 10.0+ | Home button click speed setup pane (accessibility setting for single, double, and triple-click speed). Applicable only to devices with a physical Home button; no management value for corporate deployments |
| Privacy | **Hide** | iOS 11.3+ | Privacy information pane shown to the user. Hidden to reduce OOBE friction; corporate privacy terms are communicated through the organization's acceptable use policy, not the device Setup Assistant |
| iMessage & FaceTime | **Hide** | iOS 9.0+ | iMessage and FaceTime setup pane. Controlled via Restrictions policy |
| Onboarding | **Hide** | iOS 11.0+ | Informational screens covering iPhone usage tips (Cover Sheet, Dock navigation, Control Center). No management value |
| Screen Time | **Hide** | iOS 12.0+ | Screen Time setup pane. Managed via Intune Restrictions; no user configuration needed |
| SIM Setup | **Hide** | iOS 12.0+ | Cellular setup pane; user can add a cellular plan. Not relevant unless deploying eSIM plans |
| Software Update | **Show** | iOS 12.0+ | Mandatory software update screen. Show to ensure the device is on a supported OS before completing setup |
| Watch Migration | **Hide** | iOS 11.0+ | Apple Watch migration pane; user can migrate Watch data. Configure separately if Apple Watch is part of deployment |
| Appearance | **Hide** | iOS 13.0+ | Appearance (light/dark mode) setup pane. Not security-relevant; reduces setup steps |
| Device To Device Migration | **Hide** | iOS 12.4+ | Wireless device-to-device data transfer pane (Transfer from iPhone). Use iCloud Backup restore instead for enterprise deployments |
| Restore Completed | **Hide** | iOS 14.0+ | Confirmation screen displayed after a restore completes during Setup Assistant. No management value |
| Software Update completed | **Show** | iOS 14.0+ | Confirmation screen displayed after a software update completes during Setup Assistant. Shown for consistency with Software Update = Show — informs the user when the update finishes |
| Get Started | **Hide** | All | Final completion pane shown at the end of Setup Assistant. Not required for setup to finish — Await Final Configuration handles the policy application window |
| Action button | **Hide** | iOS 17.0+ | Action button configuration pane. Configured post-enrollment via restrictions if needed |
| Emergency SOS | **Hide** | iOS 16.0+ | Safety and Emergency SOS setup pane. Emergency contacts can be configured post-enrollment |
| Camera button | **Hide** | iOS 18.0+ | Camera button configuration pane. Reduces setup complexity |
| Web Content Filter | **Hide** | iOS 18.2+ | Web content filtering setup pane. Managed via Intune Web Content Filter profile |
| Safety and handling | **Hide** | iOS 18.4+ | Safety and handling information pane. Informational only; no management dependency |
| Multitasking | **Hide** | iOS 26.0+ (expected) | Multitasking gestures informational pane. No management value |
| OS Showcase | **Hide** | iOS 26.0+ (expected) | New OS features highlight pane. No management value |
| App Store | **Hide** | iOS 14.3+ | App Store setup pane. Apps deployed via VPP; users do not need to configure this during setup |
| Terms of Address | **Hide** | iOS 16.0+ | Pronoun/address preference pane (select languages only). Personal preference setting; no management value |
| Intelligence | **Hide** | iOS 18.0+ | Apple Intelligence setup pane; user can configure Apple Intelligence features. Controlled via Restrictions policy |

> **Note on Passcode and Face ID / Touch ID screens:** These screens do not function correctly on iOS/iPadOS 14.5 and later — this is a confirmed platform limitation documented by Microsoft, not a configuration issue. The Touch ID and Face ID screen is shown in this baseline to allow biometric setup during OOBE (the screen renders and the user can configure biometrics); however, enforce passcode requirements through a Compliance policy or a DDM Passcode device configuration profile rather than relying on the Setup Assistant prompt alone. If the Passcode screen is shown on iOS 14.5+ devices, users may set a passcode that does not meet your policy requirements before the compliance policy has been delivered and evaluated.

> When a screen is set to Hide, it is skipped during Setup Assistant. The user can still access most of these settings later through the device Settings app. Hiding a screen does not remove any Intune policy control that covers the same setting.

> **Locked enrollment:** Set to Yes for all corporate profiles. Locked enrollment prevents users from removing the MDM profile. A device wipe is the only removal method. This is required for all supervised corporate scenarios.

> **Profile name is the group membership key.** All dynamic group rules in Section 6.1 use `device.enrollmentProfileName` to match devices. The name `iOS_iPadOS-ADE-DP-Corporate` must be entered exactly as shown in Intune — it is case-sensitive in the dynamic rule evaluation. For devices with user affinity, the enrolling user must also be a member of a Microsoft Entra user group before device setup begins to ensure prompt policy and app delivery.

---

### 7.4 Step 4 – Assign Enrollment Profile to Devices

Devices must have an enrollment profile assigned before they are activated. If a device is powered on without an assigned profile, enrollment fails with an "Invalid Profile" error.

1. In `Enrollment program tokens > [your token] > Devices`, select all devices that should use the new profile
2. Select **Assign profile** and choose the profile created in Step 2
3. Confirm the assignment — the profile name should appear in the device list

**Set a default profile:**

Setting a default profile on the token ensures that any newly synced device is automatically assigned a profile without manual intervention.

1. In `Enrollment program tokens`, select your token
2. Select **Set Default Profile**
3. Choose your enrollment profile and save

> **Enrollment Restrictions prerequisite:** Before devices can enroll, confirm that `Devices > Enrollment > Enrollment restrictions` does not have the default **All Users** platform restriction set to block for iOS/iPadOS Corporate devices. If iOS/iPadOS is blocked at the platform level, ADE enrollment fails and the device displays an "Invalid Profile" error regardless of whether a profile is assigned and regardless of device ownership type.

---

### 7.5 Device Activation Walkthrough

This is what happens when a supervised device is powered on for the first time after ADE setup is complete.

1. **Device powers on** — connects to Apple's activation server — Apple returns the Intune MDM enrollment profile (because the device is registered to your ADE token)
2. **Setup Assistant launches** with the screens configured in Step 3
3. **User authenticates** with their Entra ID credentials during Setup Assistant (modern authentication method)
4. **Apple ID screen appears** (if shown) — user signs in with their Managed Apple ID; see Section 7.6 for this workflow
5. **Entra ID device registration** completes during Setup Assistant — the device is registered before the user reaches the home screen
6. **Await Final Configuration screen** appears — the device is held here while Intune pushes the assigned device configuration policies. Only device configuration policies apply during this window; applications (including Company Portal and Authenticator) are not included and begin installing after the device reaches the home screen
7. **Device is released** to the home screen once initial configuration policies have been received — typically within 15 minutes, depending on the number of assigned policies
8. **Company Portal and Microsoft Authenticator** install silently in the background via VPP after the home screen loads
9. **User opens any M365 app** — SSO is established through Authenticator; user does not need to sign in to each app individually

The total activation time (from power-on to home screen) is typically 10–20 minutes for a well-configured deployment, depending on the number of assigned policies and apps.

> **If users are held on the Await Final Configuration screen for an extended period**, it usually indicates a large number of assigned configuration policies. Applications do not contribute to the AFC hold time — they are not installed during this phase. Inform your help desk and end users during rollout so they are not alarmed by the wait. If a device is held indefinitely, check `Devices > [device] > Device configuration` for failed or pending policies.

---

### 7.6 Managed Apple ID Integration

This section describes an optional add-on scenario for organizations that restrict personal Apple IDs on corporate devices, or that require Managed Apple IDs for specific enterprise services such as FaceTime, iCloud Drive, or mail with managed identity compliance.

#### When This Applies

Managed Apple IDs on corporate ADE devices are relevant when your organization:

- Restricts or blocks personal Apple IDs on corporate devices via Apple Business account controls
- Requires Managed Apple IDs for services that mandate a managed identity (FaceTime in regulated environments, enterprise iCloud Drive, apps that enforce Apple ID compliance through Mobile Device Management)
- Wants to ensure that App Store and Apple service access on corporate devices is tied to a managed, organization-controlled identity rather than a personal Apple ID

If your deployment does not require Managed Apple IDs and apps are deployed exclusively via VPP with device licensing and Apple services are blocked via policy — the Apple ID screen in Section 7.3 can be hidden and this section does not apply.

#### Configuring Apple Business Portal to Restrict Personal Apple IDs

To enforce Managed Apple ID use on corporate devices, configure the following two settings in Apple Business portal under **Settings > Apple Services**:

| Setting | Recommended Value | Notes |
|---|---|---|
| Allow Managed Apple Account on | **Supervised Devices Only** | Controls which device scope can use Managed Apple Accounts. "Supervised Devices Only" matches the ADE/corporate scope — only devices enrolled as supervised through ADE can authenticate with a Managed Apple ID |
| Apple Account on Organization Devices | **Managed Apple Accounts Only** | Prevents users from signing in with a personal Apple ID on organization-managed devices. Only Managed Apple IDs are accepted for Apple services on enrolled devices |

> These are tenant-wide Apple Business settings that apply to all devices managed through your organization's Apple Business account. Changing "Apple Account on Organization Devices" to "Managed Apple Accounts Only" affects all managed devices — communicate the change to users and help desk before enabling it if existing users have already signed in with a personal Apple ID on an organization device.

#### Managed Apple ID Provisioning

All enrolling users must have a Managed Apple ID before their device is activated. There are two provisioning methods:

| Method | When to Use | Effort |
|---|---|---|
| **Manual creation** in Apple Business portal | Small deployments; fewer than 50 users | High — each ID created individually |
| **Entra ID federation** with Apple Business (recommended) | Any size deployment | One-time federation setup; IDs auto-provisioned on first sign-in |

Entra ID federation links Apple Business to your Microsoft Entra ID directory so that Managed Apple IDs are automatically provisioned when users sign in with their existing M365 credentials. See Section 9 for the full federation setup procedure.

> **Federation prerequisite:** Your organization's domain must be verified in Apple Business portal before federation can be configured. A Global Administrator account in Microsoft Entra ID is required to grant Apple Business the permissions needed to sync users.

#### Setup Assistant Workflow

When Managed Apple IDs are required, the ADE enrollment profile is configured to show the Apple ID screen during Setup Assistant (as set in Section 7.3). The user encounters two separate sign-in prompts during Setup Assistant:

1. **Entra ID credentials** — authenticates the user for MDM enrollment via modern authentication; this is always present regardless of Managed Apple ID use
2. **Managed Apple ID** — on the Apple ID screen, the user signs in with their Managed Apple ID (same format as their work email when federation is configured); this establishes iCloud and Apple service access tied to the managed identity

Both sign-ins occur within the same Setup Assistant session before the device reaches the home screen.

#### Post-Provisioning Lock-Down

- After Setup Assistant completes, the `Allow Account Modification = false` setting in the `iOS_iPadOS - SC - DEV - Device Restrictions - Corporate Devices` policy prevents users from adding, removing, or changing the Apple ID configuration on the device. This setting is deployed as part of this baseline and requires no additional policy configuration.

- The MDM profile itself is locked via the **Locked Enrollment** setting in the ADE enrollment profile — users cannot remove the MDM profile without a device wipe.

#### Help Desk Guidance

- If a user's Managed Apple ID is not yet provisioned when their device activates, they will be unable to sign in on the Apple ID screen during Setup Assistant. Either skip the screen during this activation and re-provision, or ensure Managed Apple IDs are pre-created before devices are distributed.
- If a user sees a sign-in error on the Apple ID screen, verify the Managed Apple ID exists in Apple Business and that federation (if configured) has completed the sync for that user.

---

## 8. App Management

The following app and authentication configurations apply to the corporate device track and should be in place before users begin activating devices.

---

### 8.1 Authentication Broker – Authenticator vs Company Portal

A common point of confusion in iOS Intune deployments is the respective roles of Microsoft Authenticator and Company Portal. They serve distinct functions and both are needed.

| App | Role | What It Does |
|---|---|---|
| **Microsoft Authenticator** | Authentication broker | Acquires tokens for all MSAL-integrated apps; holds the Primary Refresh Token (PRT) in Secure Enclave storage; enables device-wide SSO |
| **Company Portal** | Compliance broker | Reports device enrollment status and compliance state to Intune; syncs compliance signal to Entra ID for Conditional Access evaluation |

When a user opens an M365 app and signs in, the app calls the Microsoft Authentication Library (MSAL), which detects Authenticator via URL scheme and routes the token request through it. Authenticator retrieves the PRT and returns a token to the app. Separately, Entra ID evaluates the device's compliance state — which comes from Company Portal's reporting to Intune.

This separation matters in practice: if Company Portal is not installed or the device has not checked in recently, Conditional Access policies requiring compliant devices will block access — even if Authenticator is present and authentication succeeds. Both apps must be installed and functioning.

---

### 8.2 Microsoft Authenticator Deployment

Microsoft Authenticator is the **authentication broker** on iOS — all MSAL-based M365 apps route token requests through it. It is required for Enterprise SSO and JIT registration. Deploy it as a **Required** VPP app before users begin activation.

| | Supervised (Corporate ADE) |
|---|---|
| **App type** | VPP app (from Apple Business Manager) |
| **License type** | Device licensing |
| **Assignment type** | Required |
| **Target group** | Device groups |
| **Install behavior** | Silent — no Apple ID, no user prompt |
| **JIT registration** | Authenticator is present before JIT triggers |
| **Admin action** | Sync VPP token; set license type to Device; assign to device groups |
| **User action** | None |

---

### 8.3 VPP App Deployment

VPP app deployment on supervised devices is silent and user-transparent — apps install in the background without the user needing to sign in to the App Store or approve installation.

**All apps on supervised devices must be VPP with device licensing:**

ADE-enrolled supervised devices require every Required app to be deployed through VPP using device licensing. Device licensing routes the installation through the MDM channel — no Apple ID is involved, and users see no prompts. Do not use iOS Store App assignments on supervised devices: iOS treats App Store–sourced app installations differently from MDM-delivered ones, and will demand personal Apple ID authentication before installing any app that does not arrive through the MDM app deployment channel. Use device licensing specifically, not user licensing — user licensing still ties each installation to an individual Apple ID.

**Required apps (install without user approval):**

| App | License Type | Notes |
|---|---|---|
| Microsoft Authenticator | Device | Required for brokered auth and JIT registration |
| Company Portal | Device | Required for compliance reporting. Only assign manually if not configured via the enrollment profile (see Section 7.2). |
| Microsoft Outlook | Device | Deploy via VPP with matching App Config Policy |
| Microsoft Teams | Device | Deploy via VPP with matching App Config Policy |
| Microsoft Edge | Device | Deploy via VPP with matching App Config Policy |
| OneDrive | Device | Deploy via VPP with matching App Config Policy |
| Microsoft Defender for Endpoint | Device | Required for MTD compliance integration |

**Available apps (user can install from Company Portal):**

Use the Available intent for productivity apps that not all users need on every device — for example, Word, Excel, or PowerPoint. The user sees the app in Company Portal and chooses whether to install it. VPP handles the license in the background when they do.

**Required vs Available — which assignment target to use:**

| Intent | Assigned to Device Group | Assigned to User Group |
|---|---|---|
| **Required** | App installs silently on every device in the group — no user action needed | App installs silently on devices belonging to users in the group |
| **Available** | Does not work — app will not appear in Company Portal at all | App appears in Company Portal; user chooses to install it |

If you add Available apps for users to self-install, assign them to a **user group**, not a device group. Company Portal builds the available app list based on the identity of the signed-in user and checks which **user groups** that user belongs to. Assigning an app as Available to a device group is silently ignored by Intune — no error is shown, and the app never appears in Company Portal. This is one of the most common deployment mistakes with VPP apps.

**VPP license reclamation — not automatic on device removal:**

Removing a device from Intune management, retiring it, or wiping it does not automatically return VPP licenses to your pool. The licenses remain consumed by that device object until you explicitly reclaim them.

To reclaim licenses when removing a device:

- Set the app assignment intent to **Uninstall** for the device or group before retiring, or
- Use the **Revoke licenses** action on the app in `Apps > iOS/iPadOS > [app] > Device install status`

Monitor your license pool regularly under `Tenant administration > Connectors and tokens > Apple VPP tokens`.

**Enable automatic app updates** on your VPP token to ensure deployed apps stay current without requiring admin intervention for each release.

**App Configuration Policy targeting for supervised devices:**

When creating an App Configuration Policy for supervised corporate devices, select the **VPP app** entry from your Intune app catalog as the target app — not an iOS Store App entry. Both versions share the same bundle ID, so the configuration would apply either way, but targeting the VPP version is the correct and consistent practice because that is the app you actually deployed. The ACP policies in this baseline for corporate devices (Microsoft Defender – Corporate, Microsoft Authenticator – Shared Device Mode) already reference the correct VPP app GUIDs. After importing, confirm the target app in each ACP shows the VPP version of the app.

**Using filters when VPP and App Store apps target the same user group:**

If the same user group contains both corporate and BYOD users, assigning a VPP app (for corporate) and an iOS App Store app (for BYOD) to that group without filters causes both to appear in Company Portal on every device — corporate supervised devices will see the App Store version and get an Apple ID prompt.

There is no automatic precedence between a VPP assignment and an App Store assignment. Both are evaluated independently; Intune does not deduplicate them based on bundle ID.

Prevent this by applying the existing baseline filters at assignment time. Filters evaluate **device properties at check-in time** and work correctly even when the assignment targets a user group — the evaluation logic is: user is in the group AND device matches the filter.

| App Assignment | Filter to Apply | Mode | Result |
|---|---|---|---|
| VPP app (e.g., Outlook Corporate) | `iOS-ADE-DEV` (`device.deviceOwnership -eq "Corporate"`) | Include | VPP version only shown to corporate devices |
| iOS App Store app (e.g., Outlook BYOD) | `iOS-BYOD-DEV` (`device.deviceOwnership -eq "Personal"`) | Include | App Store version only shown to BYOD devices |

This ensures each device type sees exactly one version of each app in Company Portal. Corporate supervised devices receive the VPP (device-licensed, no Apple ID) version; BYOD devices receive the App Store version. Both filters are already defined in Section 6.2 — no new filters are required.

---

## 9. Entra ID Federation Setup

Federation automates Managed Apple ID provisioning and eliminates manual ID creation in Apple Business portal. It is relevant for two scenarios in this baseline:

- **Corporate ADE devices** — when Managed Apple IDs are required on corporate devices (Section 7.6)
- **BYOD Account-Driven User Enrollment** — Managed Apple IDs are a hard requirement for Account-Driven enrollment (Option B in the BYOD Deployment Guide)

Without federation, Managed Apple IDs must be created individually for each user in Apple Business portal before their device is activated.

### 9.1 Prerequisites

- Your organization's domain must be verified in Apple Business portal before federation can be configured
- A Global Administrator account in Microsoft Entra ID is required to grant Apple Business the permissions needed to sync users

### 9.2 Federation Setup Steps

1. In Apple Business portal [business.apple.com](https://business.apple.com), go to **Organization Settings > Domains > User sign-in and directory sync > Connect**
2. Choose **Microsoft Entra ID** as the Identity Provider and select **Continue**
3. Select **Sign in with Microsoft** — Apple Business requests the permissions needed to sync your users with Microsoft Entra ID
4. Sign in with a Global Administrator account and grant the requested permissions to complete the federation
5. Complete the federation process to begin the Entra sync to Apple Business

> After federation completes, Managed Apple IDs are automatically provisioned when users first authenticate with their Entra ID credentials. The Managed Apple ID format uses your organization's verified domain — the exact format is determined by Apple Business during the federation setup process.

---

## 10. References

### Microsoft Documentation

- [Set up Automated Device Enrollment for iOS/iPadOS](https://learn.microsoft.com/en-us/intune/device-enrollment/apple/setup-automated-ios)
- [Apple MDM Push Certificate setup](https://learn.microsoft.com/en-us/intune/device-enrollment/apple/create-mdm-push-certificate)
- [Apple VPP token management in Intune](https://learn.microsoft.com/en-us/intune/intune-service/apps/vpp-apps-ios)
- [Set up Just-in-Time Registration in Intune](https://learn.microsoft.com/en-us/intune/device-enrollment/apple/setup-just-in-time-registration)
- [iOS/iPadOS device enrollment guide](https://learn.microsoft.com/en-us/intune/device-enrollment/apple/guide-ios-ipados)

### Apple Resources

- [Apple Business portal](https://business.apple.com)
- [Apple Platform Deployment Guide](https://support.apple.com/guide/deployment/welcome/web)

---

*This document is part of the UniFy Endpoint – Microsoft Intune iOS/iPadOS Baseline series. Document maintained by Yoennis Olmo - Sr. Modern Work Consultant.*
