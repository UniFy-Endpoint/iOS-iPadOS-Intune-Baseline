# iOS/iPadOS BYOD Deployment Guide – Microsoft Intune

| | |
|---|---|
| **Document Type** | Deployment Instruction – BYOD |
| **Author** | Sr. Modern Work Consultant |
| **Last Updated** | May 2026 |
| **Applies To** | iOS/iPadOS 14.0 and later (iOS 15.0+ required for Options A, B, and D), Microsoft Intune, Personal/BYOD |

---

## Table of Contents

1. [Overview & Scope](#1-overview--scope)
2. [Licensing Requirements](#2-licensing-requirements)
3. [Network and Firewall Requirements](#3-network-and-firewall-requirements)
4. [Infrastructure Setup](#4-infrastructure-setup)
   - [4.1 Apple MDM Push Certificate](#41-apple-mdm-push-certificate)
   - [4.2 Apple Business Portal (Option B Only)](#42-apple-business-portal-option-b-only)
   - [4.3 Entra ID Federation with Apple Business (Option B Only)](#43-entra-id-federation-with-apple-business-option-b-only)
5. [Groups, Filters, and Policy Assignments](#5-groups-filters-and-policy-assignments)
   - [5.1 Entra Group Architecture](#51-entra-group-architecture)
   - [5.2 Intune Assignment Filters](#52-intune-assignment-filters)
   - [5.3 Policy Assignments](#53-policy-assignments)
   - [5.4 Enrollment Restrictions](#54-enrollment-restrictions)
6. [BYOD Device Enrollment](#6-byod-device-enrollment)
   - [6.1 Enrollment Methods Overview](#61-enrollment-methods-overview)
   - [6.2 Prerequisites by Enrollment Method](#62-prerequisites-by-enrollment-method)
   - [6.3 Option A — Web-Based Device Enrollment (Recommended)](#63-option-a--web-based-device-enrollment-recommended)
   - [6.4 Option B — Account-Driven User Enrollment](#64-option-b--account-driven-user-enrollment)
   - [6.5 Option C — Device Enrollment with Company Portal](#65-option-c--device-enrollment-with-company-portal)
   - [6.6 Option D — Determine Based on User Choice](#66-option-d--determine-based-on-user-choice)
7. [App Management](#7-app-management)
   - [7.1 Authentication Broker – Authenticator vs Company Portal](#71-authentication-broker--authenticator-vs-company-portal)
   - [7.2 Microsoft Authenticator Deployment](#72-microsoft-authenticator-deployment)
   - [7.3 Available and Required Apps](#73-available-and-required-apps)
8. [References](#8-references)

---

## 1. Overview & Scope

This guide covers end-to-end enrollment and configuration for personally-owned (BYOD) iOS and iPadOS devices in Microsoft Intune. BYOD devices are **not supervised** — the user retains ownership and can remove the MDM profile at any time.

The management framework for personal devices protects organizational data without overreaching into personal content. It relies on:

- Enrollment restrictions
- Compliance policies
- App Protection Policies (MAM)
- Conditional Access

This guide covers **operational procedures** — the steps an administrator takes in the Intune admin center and Apple Business portal. For additional policy and configuration detail, refer to the supplementary guides:

| Document | Purpose |
|---|---|
| [iOS_App_Protection_Policy_Guide.md](iOS_App_Protection_Policy_Guide.md) | App Protection Policy (MAM) data protection levels |
| [iOS_App_Configuration_Policy_Guide.md](iOS_App_Configuration_Policy_Guide.md) | App Configuration key-value pairs for M365 apps |
| [Defender_for_Endpoint_iOS_iPadOS_Devices.md](Defender_for_Endpoint_iOS_iPadOS_Devices.md) | MDE onboarding for iOS |

For corporate-owned supervised device enrollment via ADE, refer to [iOS_iPadOS_Corporate_Deployment_Guide.md](iOS_iPadOS_Corporate_Deployment_Guide.md).

### What This Guide Does Not Cover

- Corporate-owned or ADE/supervised device enrollment
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
| Apple Business Account | Required for Account-Driven User Enrollment (Option B) only; not required for Options A, C, or D |

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

## 4. Infrastructure Setup

### 4.1 Apple MDM Push Certificate

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

### 4.2 Apple Business Portal (Option B Only)

> **Applies to:** Account-Driven User Enrollment (Option B) and the account-driven path within Option D only. BYOD Options A and C do not require an Apple Business account. For Option D, Apple Business is only required if the account-driven enrollment path will be offered to users.

Apple Business portal is required for Account-Driven User Enrollment because it is the only place where Managed Apple IDs can be created. These IDs are a hard requirement for this enrollment type — personal Apple IDs cannot be used.

Apple consolidated Apple Business Manager (ABM), Apple Business Essentials (ABE), and Apple Business Connect into a single unified portal at [business.apple.com](https://business.apple.com).

To register your organization:

1. Navigate to [business.apple.com](https://business.apple.com) and select **Enroll Now**
2. Your organization must have a valid **D-U-N-S number** — register at [dnb.com](https://www.dnb.com) if you do not have one (allow 5–7 business days for processing)
3. During registration, use a **corporate Apple ID** (not a personal Apple ID)
4. Once approved, create admin and manager accounts for your IT team

ABM registration is free. ABM setup typically takes 3–7 business days due to the D-U-N-S number verification process.

> **A common misconception** is that BYOD enrollment always requires Apple Business Manager. This is only true for Account-Driven User Enrollment (Option B). Options A, C, and D (without the account-driven path) do not require ABM.

---

### 4.3 Entra ID Federation with Apple Business (Option B Only)

> **Applies to:** Account-Driven User Enrollment (Option B) and the account-driven path within Option D only. This section is not required for Options A or C.

Account-Driven User Enrollment requires every enrolling user to have a **Managed Apple ID** — this is a hard requirement imposed by Apple for this enrollment type. There are two ways to provision them:

- **Manual creation** — create each Managed Apple ID individually in Apple Business portal; suitable for small deployments
- **Federated authentication (recommended)** — link Apple Business to Microsoft Entra ID so that Managed Apple IDs are automatically provisioned when users sign in with their existing M365 credentials; eliminates manual ID distribution for any size deployment

Federation is a best practice, not a hard requirement. If your organization has already registered for Apple Business portal and has a small user base, you may start with manual Managed Apple ID creation before investing in federation.

**Prerequisites before configuring federation:**

- Your organization's domain must be verified in Apple Business portal before federation can be configured
- A Global Administrator account in Microsoft Entra ID is required to grant Apple Business the permissions needed to sync users

**Federation setup steps:**

1. In Apple Business portal [business.apple.com](https://business.apple.com), go to **Organization Settings > Domains > User sign-in and directory sync > Connect**
2. Choose **Microsoft Entra ID** as the Identity Provider and select **Continue**
3. Select **Sign in with Microsoft** — this allows Apple Business to request the permissions necessary to sync your users with Microsoft Entra ID
4. Sign in with a Global Administrator account and grant the requested permissions to complete the federation
5. Complete the federation process to begin the Entra sync to Apple Business

---

## 5. Groups, Filters, and Policy Assignments

Create these groups and filters before configuring enrollment. They are used as assignment targets throughout Section 6.

---

### 5.1 Entra Group Architecture

This section defines the group structure for the BYOD device track. Use this as the source of truth for all policy assignments.

| Tool | Purpose | Speed | Where Used |
|---|---|---|---|
| **Entra Dynamic Group** | Determines which devices are in a group (membership). Used as the policy assignment target. | Slow — evaluated asynchronously, can take minutes to hours | Policy assignments, CA policies, App Protection Policies |
| **Intune Assignment Filter** | Refines which devices within an already-assigned group actually receive the policy, evaluated at device check-in | Fast — evaluated at device check-in | Settings Catalog, Compliance, App Config — not CA |

> For BYOD devices, the ownership value is `"Personal"` in both Entra dynamic group rules and Intune assignment filters.

#### Device Group

| Group Name | Membership Type | Description |
|---|---|---|
| `iOS - DEV - Personal-Owned-Devices - BYOD` | Dynamic device | This group includes all iOS personally-owned BYOD devices |

**Dynamic rule — `iOS - DEV - Personal-Owned-Devices - BYOD`:**

```
((device.deviceOSType -eq "iPad") or (device.deviceOSType -eq "iPhone")) and (device.deviceOwnership -eq "Personal") and (device.managementType -eq "MDM")
```

#### User Group

App Protection Policies (MAM) must target **user groups**, not device groups.

| Group Name | Membership Type | Description |
|---|---|---|
| `iOS - USR - Personal-Owned-Users - BYOD` | Dynamic user | This group includes all Intune-licensed users with iOS personally-owned BYOD devices |

**Dynamic rule for `iOS - USR - Personal-Owned-Users - BYOD`:**

```
(user.assignedPlans -any (assignedPlan.servicePlanId -eq "c1ec4a95-1f05-45b3-a911-aa3fa01094f5" -and assignedPlan.capabilityStatus -eq "Enabled")) and (user.accountEnabled -eq true)
```

**To create a dynamic group:**

1. In Entra admin center, go to `Groups > New group`
2. Set **Group type** to **Security** and **Membership type** to **Dynamic device** or **Dynamic user** as appropriate
3. Enter the dynamic rule in the **Rule syntax** text box (not the rule builder — device rules require direct text entry)
4. Validate the rule syntax, then save

> **Propagation timing:** Dynamic group membership is evaluated asynchronously. After enrollment, a device may take minutes to hours to appear in the group. For time-critical first deployments, you can temporarily add the device to an assigned fallback group while the dynamic group populates.

---

### 5.2 Intune Assignment Filters

Create this filter in Intune and reuse it across all BYOD policy assignments.

> **Where to configure:** Intune admin center > `Tenant administration > Filters > Create`

| Filter Name | Platform | Rule | Purpose |
|---|---|---|---|
| `iOS-BYOD-DEV` | iOS/iPadOS | `(device.deviceOwnership -eq "Personal")` | Refine BYOD policy assignments within a broad group |

**How to apply a filter at assignment time:**

1. Open the policy > **Properties > Assignments > Add group**
2. Select the target group
3. Under **Filter**, select **Include filtered devices in assignment**
4. Choose the `iOS-BYOD-DEV` filter from the list

---

### 5.3 Policy Assignments

With groups and filters in place, assign each baseline policy to its target group.

#### Settings Catalog

| Policy | Target Group | Filter |
|---|---|---|
| `iOS_iPadOS - SC - DEV - Data Protection - BYOD Devices` | `iOS - DEV - Personal-Owned-Devices - BYOD` | `iOS-BYOD-DEV` |
| `iOS_iPadOS - SC - DEV - Device Privacy - BYOD Devices` | `iOS - DEV - Personal-Owned-Devices - BYOD` | `iOS-BYOD-DEV` |
| `iOS_iPadOS - SC - DEV - Device Restrictions - BYOD Devices` | `iOS - DEV - Personal-Owned-Devices - BYOD` | `iOS-BYOD-DEV` |
| `iOS_iPadOS - SC - DEV - Device Security - BYOD Devices` | `iOS - DEV - Personal-Owned-Devices - BYOD` | `iOS-BYOD-DEV` |
| `iOS_iPadOS - SC - DEV - iCloud & Storage - BYOD Devices` | `iOS - DEV - Personal-Owned-Devices - BYOD` | `iOS-BYOD-DEV` |
| `iOS_iPadOS - SC - DEV - Safari Browser - BYOD Devices` | `iOS - DEV - Personal-Owned-Devices - BYOD` | `iOS-BYOD-DEV` |
| `iOS_iPadOS - SC - DEV - Microsoft Enterprise SSO - All Devices` | `iOS - DEV - Personal-Owned-Devices - BYOD` | `iOS-BYOD-DEV` |

> The Microsoft Enterprise SSO policy is assigned to both BYOD and Corporate device groups. The Corporate track assignment is listed in [iOS_iPadOS_Corporate_Deployment_Guide.md](iOS_iPadOS_Corporate_Deployment_Guide.md) Section 6.3.

> **Apple Intelligence — not assigned to BYOD devices (CIS Section 2.9.1–2.9.4 gap):** Microsoft has migrated Apple Intelligence settings in Intune from Device Restrictions Configuration to Declarative Device Management (DDM). The new DDM-based settings work correctly on supervised Corporate devices but produce errors in Intune when assigned to unsupervised BYOD devices. The `iOS_iPadOS - SC - DEV - Apple Intelligence & Siri - BYOD Devices` policy has been removed from the baseline and is not assigned to any BYOD group.
>
> **CIS compliance impact:** CIS iOS/iPadOS 26 Benchmark v1.0.0 includes four Level 1 BYOD controls (Section 2.9.1 External Intelligence, Section 2.9.2 Notes Summarization, Section 2.9.3 Mail Summarization, Section 2.9.4 Writing Tools) that cannot be enforced on unsupervised BYOD devices due to this DDM limitation. These are documented as a known gap in Section 3.1 (Deviations Registry) and Section 5.7 of the [iOS_CIS-Microsoft_Recommended_Settings_Guide.md](iOS_CIS-Microsoft_Recommended_Settings_Guide.md).
>
> **Compensating controls:** MAM App Protection Policies prevent corporate data from being processed by Apple AI within managed Microsoft 365 apps on BYOD devices. Monitor Microsoft Intune release notes for future support of DDM Apple Intelligence controls on unsupervised devices.

#### Compliance Policies

| Policy | Target Group | Filter |
|---|---|---|
| `iOS_iPadOS - CP - DEV - Compliance - BYOD Devices` | `iOS - DEV - Personal-Owned-Devices - BYOD` | `iOS-BYOD-DEV` |
| `iOS_iPadOS - CP - DEV - Compliance - MDE - BYOD Devices` | `iOS - DEV - Personal-Owned-Devices - BYOD` | `iOS-BYOD-DEV` |

#### App Protection

| Policy | Target Group | Filter |
|---|---|---|
| `iOS_iPadOS - APP - USR - App Protection - MAM - L2 - BYOD Devices` | `iOS - USR - Personal-Owned-Users - BYOD` | None (user-targeted) |

#### App Configuration

| Policy | Target Group | Filter |
|---|---|---|
| `iOS_iPadOS - ACP - DEV - Microsoft Defender - BYOD Devices` | `iOS - DEV - Personal-Owned-Devices - BYOD` | `iOS-BYOD-DEV` |
| `iOS_iPadOS - ACP - DEV - Microsoft Edge - BYOD Devices` | `iOS - DEV - Personal-Owned-Devices - BYOD` | `iOS-BYOD-DEV` |
| `iOS_iPadOS - ACP - DEV - Microsoft OneDrive - BYOD Devices` | `iOS - DEV - Personal-Owned-Devices - BYOD` | `iOS-BYOD-DEV` |
| `iOS_iPadOS - ACP - DEV - Microsoft Teams - BYOD Devices` | `iOS - DEV - Personal-Owned-Devices - BYOD` | `iOS-BYOD-DEV` |
| `iOS_iPadOS - ACP - DEV - Outlook Settings - BYOD Devices` | `iOS - DEV - Personal-Owned-Devices - BYOD` | `iOS-BYOD-DEV` |

#### Microsoft Defender Device Configuration

| Policy | Target Group | Filter |
|---|---|---|
| `iOS_iPadOS - DC - DEV - Zero-Touch-Onboarding-VPN - MDE - BYOD Devices` | `iOS - DEV - Personal-Owned-Devices - BYOD` | `iOS-BYOD-DEV` |

---

### 5.4 Enrollment Restrictions

| Restriction | Assignment Target | Notes |
|---|---|---|
| Device type restriction — allow iOS BYOD | `iOS - USR - Personal-Owned-Users - BYOD` | Allow personal iOS device enrollment for BYOD users; assign at user-group level so restrictions are evaluated before the device is enrolled |
| Device limit restriction | `iOS - USR - Personal-Owned-Users - BYOD` | Optional — set a maximum number of personally-owned devices per user if needed |

> Configure enrollment restrictions in Intune at: `Devices > Enrollment > Enrollment restrictions`.

---

## 6. BYOD Device Enrollment

> **Before starting enrollment:** Microsoft Authenticator must be deployed and available on user devices before JIT registration can complete. Deploy it as a Required App per Section 7.2 before users begin the enrollment steps in Sections 6.3–6.6.

### 6.1 Enrollment Methods Overview

Four enrollment methods are available for personal iOS/iPadOS devices in Microsoft Intune. Enrollment type profiles are assigned to **user groups** and apply at enrollment time before a device group is populated. If no profile is assigned to a user, Intune falls back to Device Enrollment as the default. App Protection Policies and Conditional Access apply to all four methods regardless of which option is selected.

---

| Features | **Web-Based Device Enrollment** | **Account-Driven User Enrollment** | **Device Enrollment with Company Portal** | **Determine Based on User Choice** |
|---|---|---|---|---|
| **iOS minimum** | 15+ | 15+ | 14+ | 15+ |
| **ABM required** | No | Yes (free) | No | Only if account-driven path offered |
| **Managed Apple ID** | No — personal Apple ID | Yes — or federated from Entra | No — personal Apple ID | Depends on user's selection |
| **Company Portal app** | No — browser-based | No — native Settings flow | Yes | Yes |
| **Service discovery file** | No | Yes | No | Only if account-driven path offered |
| **Management scope** | Full device | Work partition only | Full device | Depends on user's selection |
| **Data separation (personal vs work)** | No | Yes — isolated APFS volume | No | Depends on user's selection |
| **IT visibility into personal apps** | Yes — all apps visible | No — personal apps invisible | Yes — all apps visible | Depends on user's selection |
| **Device-wide MDM restrictions** | Yes — all BYOD policies apply | No — silently ignored | Yes — all BYOD policies apply | Depends on user's selection |
| **Device-wide passcode enforcement** | Yes | No — work partition only | Yes | Depends on user's selection |
| **Full device wipe (admin)** | Yes | No | Yes | Depends on user's selection |
| **Selective / corporate wipe** | Yes | Yes — work partition only | Yes | Yes |
| **Compliance evaluation** | Full | Partial — fewer attributes | Full | Depends on user's selection |

---

> **Deprecated — do not use:** Profile-based User Enrollment via Company Portal was deprecated by Apple in iOS 17 and removed from Intune support as of iOS 18. Use Account-Driven User Enrollment (Option B) instead.

> **BYOD communication requirement:** For Options A, C, and D (full device enrollment path), inform users in writing that the administrator can perform a full device wipe before they enroll. Option B is the only method where a full device wipe is not available to the administrator.

> **Policy assignment:** Options A and C support the full set of BYOD baseline policies — Settings Catalog, Compliance, App Protection, and App Configuration. Option B should receive App Protection and Conditional Access policies only — Settings Catalog device restrictions and Compliance policies are silently ignored on user-enrolled devices. Option D should receive all BYOD policies; users who select the account-driven path will silently ignore SC restrictions, but App Protection and Conditional Access remain effective.

> **Microsoft's recommendation:** Microsoft identifies **Option B — Account-Driven User Enrollment** as the privacy-preferred BYOD enrollment model for iOS/iPadOS. The cryptographic separation between work and personal data, limited IT visibility into the personal partition, and selective-wipe-only behavior align with zero-trust principles and employee privacy regulations such as GDPR and CCPA. Option B requires Apple Business Manager and Managed Apple IDs — do not assign Settings Catalog or Compliance policies to Option B device groups, as they are silently ignored by iOS. Options A and C require no additional infrastructure and support the full set of BYOD baseline policies. Choose the method that best fits your organization's infrastructure, privacy obligations, and security requirements.

---

### 6.2 Prerequisites by Enrollment Method

Review the applicable column before configuring an enrollment profile. Option B ABM registration and Entra ID federation are covered in Sections 4.2 and 4.3.

| Prerequisite | Option A | Option B | Option C | Option D |
|---|---|---|---|---|
| Apple MDM Push Certificate | Required | Required | Required | Required |
| MDM Authority set to Intune | Required | Required | Required | Required |
| Apple Business Manager account | No | Yes | No | Only if account-driven path offered |
| Managed Apple IDs for enrolling users | No | Yes | No | Only if account-driven path offered |
| Service discovery file published on domain | No | Yes | No | Only if account-driven path offered |
| Enrollment type profile configured in Intune | Yes | Yes | Yes | Yes |
| Company Portal app deployed (Available) | No | No | Yes | Yes |
| Microsoft Authenticator deployed (Required) | Yes | Yes | Yes | Yes |

---

> **IMPORTANT — Stolen Device Protection causes a 1-hour enrollment delay**
>
> Apple's Stolen Device Protection (introduced in iOS 17.3) enforces a mandatory 1-hour security delay before MDM profile installation when the device is not at a recognized trusted location (a location iOS has learned as significant, such as home or work). This affects all BYOD enrollment options below and is an Apple OS-level behavior — it cannot be bypassed or disabled via any Intune MDM policy.
>
> **Before starting enrollment, instruct users to do one of the following:**
>
> - **Enroll from a trusted location** — Start enrollment at home or work, where iOS has recognized the location as significant. No delay occurs. This is the recommended approach.

> - **Temporarily disable Stolen Device Protection** — Go to **Settings > Face ID & Passcode > Stolen Device Protection** and turn it off before starting enrollment. Re-enable it after enrollment completes.
>
> If the user sees a "Security Delay in Progress" message during enrollment, the device is at an unfamiliar location with Stolen Device Protection active. The user must wait 1 hour, then confirm Face ID a second time to complete profile installation. This is expected Apple behavior, not an Intune error.

---

### 6.3 Option A — Web-Based Device Enrollment (Recommended)

Web-based device enrollment allows users to enroll their personal iOS device by opening a link in Safari, downloading a management profile, and signing in — no Company Portal app required. Microsoft Authenticator installs automatically as part of the JIT registration flow.

> Authenticator must be installed on the device before JIT registration can complete. If a user tries to sign in to a work app before Authenticator is installed, they will receive an error. The app installs automatically within a few minutes of enrollment, but users should wait for it before opening M365 apps.

#### Step 1 – Create the Web-Based Enrollment Profile

**Navigation:** `Devices > Enrollment > Apple > Enrollment types > Create profile > iOS/iPadOS`

| Setting | Value |
|---|---|
| Name | `iOS-Enrollment-BYOD-WebBased` |
| Enrollment type | **Web based device enrollment** |
| Assignment | Assign to BYOD user groups |

After creation, check the profile priority under **Enrollment types**. If you have multiple enrollment profiles, ensure this one has a higher priority than any legacy Company Portal profile for users on iOS 15 or later.

#### Step 2 – Deploy Company Portal Web Clip

Rather than requiring users to find and install the Company Portal app, deploy a **web clip** that links directly to the Company Portal website. This gives users access to device status, compliance information, and available apps from their home screen without installing a native app.

1. In Intune, go to `Apps > iOS/iPadOS > Add > Web link`
2. Configure:
   - Name: `Company Portal`
   - URL: `https://portal.manage.microsoft.com/`
   - Pre-composed icon: Use the Company Portal icon
3. Assign as **Available** to BYOD user groups

#### Step 3 – Configure Enrollment Restrictions

Enrollment restrictions control which devices and OS versions are permitted to enroll. Set a minimum OS version to prevent outdated devices from accessing corporate resources.

**Navigation:** `Devices > Enrollment > Enrollment restrictions`

| Setting | Recommended Value |
|---|---|
| Platform | iOS/iPadOS: Allow |
| Minimum OS version | `15.0` (or your organization's minimum) |
| Maximum OS version | Leave blank (do not block future OS releases) |
| Block personally owned | **No** — this is the BYOD scenario |
| Block jailbroken devices | **Yes** (enforced via Compliance, not this restriction) |

#### Step 4 – Share the Enrollment Link with Users

When Conditional Access is configured to require enrollment, users are automatically redirected to enroll when they attempt to access a work resource. If you are not using CA, share the enrollment link directly.

Enrollment URL:
```
https://portal.manage.microsoft.com/enrollment/webenrollment/ios
```

**User experience walkthrough:**

1. User opens **Safari** on their personal iPhone or iPad
2. Navigates to the enrollment URL and signs in with their work account
3. Company Portal website prompts to download a management profile
4. User waits in Safari while the profile downloads — **do not switch apps during this step**
5. A notification appears: user opens **Settings > General > VPN & Device Management** and installs the profile
6. Returns to Safari — enrollment continues automatically
7. **Microsoft Authenticator** installs on the device within 2–3 minutes
8. User opens any M365 app (Teams, Outlook, etc.) and signs in with their work account
9. JIT registration triggers — the device is registered with Entra ID and SSO is established
10. User is prompted to check compliance status; the device becomes usable for work within a few minutes

> **Safari is required.** Web-based enrollment does not work in Chrome, Edge, or any other browser on iOS. If a user's default browser is not Safari, they must copy the enrollment URL and paste it into Safari manually. Include this note in your user communication to prevent support calls.

---

### 6.4 Option B — Account-Driven User Enrollment

Account-Driven User Enrollment (sometimes called Apple User Enrollment) is Apple's privacy-preserving enrollment model for personal devices. It creates a cryptographic separation between work and personal data — Intune manages only the work partition and cannot see personal apps, personal iCloud data, or personal files.

**Choose this method when:**
- Your organization operates in a regulated industry (healthcare, legal, financial services) where managing the full device inventory is not permitted or appropriate
- Users have negotiated or expect strict MDM privacy controls
- Your privacy policy explicitly limits IT visibility on personal devices

> Because device-wide restriction policies are silently ignored on user-enrolled devices, enforce all data protection controls through **App Protection Policies** instead of Settings Catalog restrictions. See the `iOS_App_Protection_Policy_Guide.md` for the recommended MAM configuration.

For prerequisites, see Section 6.2. Sections 4.2 and 4.3 cover ABM registration and Entra ID federation setup.

#### Step 1 – Publish the Service Discovery File

When a user enters their work email address during enrollment, Apple reads the domain (the part after the `@`) and makes an HTTPS request to a specific path on that domain to identify the MDM server. You must publish a JSON file at that path before any user can complete Option B enrollment.

**File details:**

| Property | Value |
|---|---|
| File name | `com.apple.remotemanagement` |
| No file extension | The file has no `.json` or any other extension — the name is exactly `com.apple.remotemanagement` |
| Directory | `/.well-known/` at the root of your domain's web server |
| Full URL Apple queries | `https://[yourdomain]/.well-known/com.apple.remotemanagement` |
| Required Content-Type | `application/json` |

> **Note:** File extensions are hide by default. Verify the file has no extension. The file should appear as `com.apple.remotemanagement` — not `com.apple.remotemanagement.json`.

**File content — standard Intune environments (commercial cloud):**

```json
{"Servers":[{"Version":"mdm-byod","BaseURL":"https://manage.microsoft.com/EnrollmentServer/PostReportDeviceInfoForUEV2?aadTenantId=YOUR_ENTRA_TENANT_ID"}]}
```

**File content — US Government environments (GCC High / DoD):**

```json
{"Servers":[{"Version":"mdm-byod","BaseURL":"https://manage.microsoft.us/EnrollmentServer/PostReportDeviceInfoForUEV2?aadTenantId=YOUR_ENTRA_TENANT_ID"}]}
```

Replace `YOUR_ENTRA_TENANT_ID` with your Entra Directory (Tenant) ID, found in the Entra admin center under `Overview > Tenant ID`.

---

**If your organization has a public website:**

1. Create the file named `com.apple.remotemanagement` (no extension) with the JSON content above
2. Create a `/.well-known/` directory at the root of your web server if one does not already exist
3. Upload the file into that directory so it is reachable at `https://yourdomain.com/.well-known/com.apple.remotemanagement`
4. Confirm the file is served with `Content-Type: application/json` — if your web server serves extensionless files as `application/octet-stream`, add a MIME type mapping for `com.apple.remotemanagement` → `application/json` in your server configuration (IIS: MIME Types settings or `web.config`; Apache: `AddType` directive; Nginx: `types` block)

---

**If your organization does not have a public website:**

DNS records alone cannot serve this file — DNS resolves domain names to IP addresses but cannot respond to HTTPS requests. You need a hosting service, but not a full website. The following free options can host a single file on your domain:

| Option | Cost | DNS change required |
|---|---|---|
| **Azure Static Web Apps** (recommended for Microsoft environments) | Free tier | No — add a CNAME record to your existing DNS provider |
| **GitHub Pages** | Free | No — add DNS records to your existing DNS provider |
| **Cloudflare Workers** | Free tier (100,000 requests/day) | Yes — DNS must be managed by Cloudflare |

For full setup instructions for each option, refer to `iOS_iPadOS_Service_Discovery_Account-Driven-Enrollment.md`.

---

**Validate the file before directing users to enroll:**

```
curl -I "https://yourdomain.com/.well-known/com.apple.remotemanagement"
```
```
curl -I "https://yourdomain.com/.well-known/com.apple.remotemanagement?user-identifier=firstname.surname@yourdomain.com&model-family=iPhone"
```

Both commands should return `Content-Type: application/json`. If users see the error **"Your Apple ID does not support the expected services on this device"**, the service discovery file is missing, unreachable, or served with the wrong content type.

#### Step 2 – Set Up JIT Registration

Microsoft Authenticator must be deployed before users can complete JIT registration. Deploy it as a Required app to BYOD user groups as described in Section 7.2.

#### Step 3 – Create Account-Driven User Enrollment Profile

**Navigation:** `Devices > Enrollment > Apple > Enrollment types > Create profile > iOS/iPadOS`

| Setting | Value |
|---|---|
| Name | `iOS-Enrollment-BYOD-UserEnrollment` |
| Enrollment type | **Account driven user enrollment** |

To offer both account-driven and full device enrollment from a single policy, use **Determine based on user choice** instead (see Section 6.6). Configure Option B when you want all users in the assigned group to always use account-driven user enrollment — no user choice presented.

#### Step 4 – User Enrollment Experience

1. User opens **Settings > General > VPN & Device Management**
2. Taps **Sign in with work or school account**
3. Enters their corporate email address — Apple uses the domain to locate the service discovery file and identify Intune
4. Signs in with Entra ID credentials
5. Taps **Allow Remote Management** when prompted
6. The management profile installs silently — no interaction required after approval
7. Microsoft Authenticator installs in the background
8. User opens a work app and signs in — JIT registration completes and SSO is established

---

### 6.5 Option C — Device Enrollment with Company Portal

Device Enrollment with Company Portal is a fully supported BYOD enrollment method in which the user downloads the Intune Company Portal app, signs in with their work account, and follows the guided in-app enrollment flow. It provides the same full device MDM scope as Option A — the widest set of management options available on an unsupervised personal device.

**Choose this method when:**
- Users are already familiar with the Company Portal app and have it installed
- Your helpdesk support model is designed around Company Portal-based flows
- You want an in-app guided experience with step-by-step instructions for less technical users
- You are enrolling devices running iOS 14.9 or earlier (required fallback for those OS versions)

**Management scope and wipe capabilities:** Same as Option A. The administrator can perform a full device wipe or a selective corporate wipe from the Intune admin center. Inform users of this before enrollment.

#### Step 1 – Deploy Company Portal as Available App

Ensure Company Portal is deployed as an **Available** app to BYOD user groups so users can install it from the Intune Company Portal website or from the App Store.

1. In Intune, go to `Apps > iOS/iPadOS > Add > iOS store app`
2. Search for **Company Portal** and add it
3. Set assignment type to **Available** for BYOD user groups

#### Step 2 – Configure the Device Enrollment Profile

**Navigation:** `Devices > Enrollment > Apple > Enrollment types > Create profile > iOS/iPadOS`

| Setting | Value |
|---|---|
| Name | `iOS-Enrollment-BYOD-CompanyPortal` |
| Enrollment type | **Device enrollment with Company Portal** |
| Assignment | Assign to BYOD user groups |

If you already have a Web-Based Device Enrollment profile (Option A), set the Company Portal profile at a lower priority. Devices running iOS 15 or later will use Option A automatically; iOS 14.9 and earlier devices will fall through to this profile.

#### Step 3 – User Enrollment Experience

1. User installs **Company Portal** from the App Store (or the Intune available apps catalog)
2. Opens Company Portal and taps **Sign in** with their work account
3. Follows the in-app enrollment guide — Company Portal walks through each step with on-screen instructions
4. User is prompted to install a management profile in **Settings > General > VPN & Device Management**
5. Returns to Company Portal after installing the profile
6. Company Portal confirms enrollment and checks compliance status
7. Microsoft Authenticator installs in the background; JIT registration completes when the user opens any M365 app

---

### 6.6 Option D — Determine Based on User Choice

**Determine based on user choice** is an enrollment profile setting in Intune. When selected, users are presented with a choice at Company Portal sign-in that determines which enrollment method is applied. Intune maps the user's selection to one of two outcomes: **account driven user enrollment** (work partition only) or **device enrollment** (full device management — which may be web-based or Company Portal-based depending on the device's iOS version and enrollment profile priority).

> This is the setting name as it appears in the Intune admin center. Microsoft documentation confirms the enrollment type option is literally named "Determine based on user choice."

**The user is presented with two paths:**

| User Selects | Maps To | Management Scope | Admin Wipe |
|---|---|---|---|
| Work partition / personal device with work access | Account driven user enrollment (Option B) | Work partition only | Selective wipe only — personal data preserved |
| Full device / corporate access | Device enrollment (Option A or Option C depending on device OS) | Full device | Full wipe or selective wipe available |

> **Wipe implication:** Because users self-select their management scope, IT cannot guarantee in advance which wipe capability applies to a given device. Ensure your acceptable-use policy or enrollment agreement communicates the difference in wipe scope before users enroll.

**Choose this method when:**
- Your organization supports both full-device-managed BYOD and privacy-preserving BYOD from a single enrollment policy
- You want to give employees visibility into the management scope they are accepting at enrollment time
- Your tenant has Managed Apple IDs available for users who want to enroll with work-partition-only management

**Prerequisites:** See Section 6.2. If the account-driven path will be offered, the same ABM, Managed Apple IDs, and service discovery setup as Option B applies — only the full device enrollment path will be offered if ABM is not available.

- Policy coverage varies per device depending on user selection. Monitor the enrollment type distribution in `Devices > Monitor > Enrollment failures` and device properties to understand your actual management posture.

**Company Portal app is required** — users must install it to see the enrollment choice screen.

#### Step 1 – Configure the Enrollment Profile

**Navigation:** `Devices > Enrollment > Apple > Enrollment types > Create profile > iOS/iPadOS`

| Setting | Value |
|---|---|
| Name | `iOS-Enrollment-BYOD-UserChoice` |
| Enrollment type | **Determine based on user choice** |
| Assignment | Assign to BYOD user groups |

If the account-driven path will be offered, ensure the service discovery file is published before users begin enrollment (see Section 6.4 for service discovery file setup).

#### Step 2 – JIT Registration and Authenticator

Microsoft Authenticator must be deployed before users can complete JIT registration. Deploy it as a Required app to BYOD user groups as described in Section 7.2. The same Authenticator deployment applies regardless of which enrollment path the user selects.

#### Step 3 – User Enrollment Experience

1. User installs **Company Portal** and signs in with their work account
2. Company Portal presents the enrollment choice — users select the management scope that matches their device ownership and privacy requirements
3. The selected path determines the enrollment method (account-driven or device enrollment); from that point, the experience is identical to Option B or Option A/C respectively
4. Microsoft Authenticator installs in the background; JIT registration completes when the user opens any M365 app

---

## 7. App Management

The following app and authentication configurations apply to the BYOD device track and should be in place before users begin enrollment.

---

### 7.1 Authentication Broker – Authenticator vs Company Portal

A common point of confusion in iOS Intune deployments is the respective roles of Microsoft Authenticator and Company Portal. They serve distinct functions and both are needed.

| App | Role | What It Does |
|---|---|---|
| **Microsoft Authenticator** | Authentication broker | Acquires tokens for all MSAL-integrated apps; holds the Primary Refresh Token (PRT) in Secure Enclave storage; enables device-wide SSO |
| **Company Portal** | Compliance broker | Reports device enrollment status and compliance state to Intune; syncs compliance signal to Entra ID for Conditional Access evaluation |

When a user opens an M365 app and signs in, the app calls the Microsoft Authentication Library (MSAL), which detects Authenticator via URL scheme and routes the token request through it. Authenticator retrieves the PRT and returns a token to the app. Separately, Entra ID evaluates the device's compliance state — which comes from Company Portal's reporting to Intune.

This separation matters in practice: if Company Portal is not installed or the device has not checked in recently, Conditional Access policies requiring compliant devices will block access — even if Authenticator is present and authentication succeeds. Both apps must be installed and functioning.

---

### 7.2 Microsoft Authenticator Deployment

Microsoft Authenticator is the **authentication broker** on iOS — all MSAL-based M365 apps route token requests through it. It is required for Enterprise SSO and JIT registration on BYOD devices. Deploy it as a **Required** app before users begin enrollment.

| Field | Value |
|---|---|
| **App type** | iOS Store App |
| **License type** | None |
| **Assignment type** | Required |
| **Target group** | User groups |
| **Install behavior** | User-prompted — user must approve install from App Store |
| **JIT registration** | Authenticator must be installed before JIT triggers — JIT uses Authenticator, it does not install it |
| **Admin action** | Add iOS Store App; assign Required to user groups |
| **User action** | Install Authenticator before opening any work app |

---

### 7.3 Available and Required Apps

For personal devices, use **iOS Store App** as the app type (not VPP). BYOD users install apps from the App Store using their personal Apple ID — this is the expected and correct behavior for personally-owned devices.

#### Silent Installation Is Not Available on BYOD Devices

A common assumption is that assigning an app as Required to a Device Group will cause it to install automatically without user interaction on personal devices. This is not the case:

- **Supervised (Corporate) devices** — VPP apps with device licensing install **silently in the background** via the MDM channel. No Apple ID, no prompt, no user action required.
- **BYOD (Personal) devices** — iOS does not allow MDM to silently install apps on an unsupervised personal device, regardless of whether you target a Device Group or User Group. Even a Required assignment results in a **notification or Company Portal prompt** that the user must acknowledge before the app installs.

Silent installation on personal devices is an iOS platform restriction. MDM can request installation, but the user retains control over their personal device and must approve it.

#### Assign BYOD Apps to User Groups, Not Device Groups

User Group targeting is the correct pattern for BYOD for three reasons:

- App Protection Policies are user-scoped and follow the user's identity across any enrolled device — user group targeting ensures MAM policies apply correctly when a user has multiple personal devices or switches devices
- The **Available** assignment intent does not work with device groups — Intune will not surface available apps in Company Portal if they are assigned to a device group; only Required works with device groups
- BYOD devices can unenroll and re-enroll frequently; user group targeting is resilient to device changes and does not require the device to be in a group before it receives the app

> Company Portal is not included in this table. For BYOD Option A (Web-Based Device Enrollment), a Company Portal web clip is deployed instead of the native app (see Section 6.3, Step 2). For Option C (Device Enrollment with Company Portal), users install the native app before enrollment (see Section 6.5, Step 1). Do not assign Company Portal as a Required app to BYOD groups — doing so conflicts with the enrollment profile setting and prompts users to sign in again.

| App | Deployment Type | Assignment Type | Notes |
|---|---|---|---|
| Microsoft Authenticator | iOS Store App | **Required** | Must be installed before JIT registration can complete |
| Microsoft Outlook | iOS Store App | Available | Users install from Company Portal or App Store |
| Microsoft Teams | iOS Store App | Available | Users install from Company Portal or App Store |
| Microsoft OneDrive | iOS Store App | Available | Users install from Company Portal or App Store |
| Microsoft Edge | iOS Store App | Available | Users install from Company Portal or App Store |
| Microsoft Defender for Endpoint | iOS Store App | Required | Required for MDE compliance signal on BYOD |

**Do not use Required for all apps on BYOD.** Requiring too many apps on personal devices can feel intrusive and may deter enrollment. Make core productivity apps Available and communicate their use through user onboarding materials instead of forcing installation.

App Protection Policies apply to managed apps regardless of how they were installed, as long as the app supports the Intune SDK. Users can install the app from the App Store independently of Intune, and App Protection Policies will still apply when the user signs in with their work account.

#### App Configuration Policy Targeting

When creating an App Configuration Policy for BYOD devices, select the **iOS Store App** entry from your Intune app catalog as the target app. This matches the deployment method for BYOD. The ACP policies in this baseline for BYOD devices (Edge, OneDrive, Teams, Outlook) already reference iOS Store App entries — after importing, confirm each BYOD ACP shows the iOS Store App version of its target app.

#### Using Filters to Prevent App Store Apps from Appearing on Corporate Devices

If the same user group contains both corporate and BYOD users, assigning an iOS App Store app (BYOD) and a VPP app (corporate) for the same application to that group without filters causes both versions to appear in Company Portal on every device. Corporate supervised devices would see the App Store version and receive an Apple ID prompt. There is no automatic precedence — Intune does not deduplicate assignments by bundle ID.

Prevent this by applying the existing baseline filters at assignment time. Filters evaluate **device properties at check-in time** and work correctly even when the assignment targets a user group — the evaluation logic is: user is in the group AND device matches the filter.

| App Assignment | Filter to Apply | Mode | Result |
|---|---|---|---|
| iOS App Store app (BYOD) | `iOS-BYOD-DEV` (`device.deviceOwnership -eq "Personal"`) | Include | Store App only shown to personal/BYOD devices |
| VPP app (corporate) | `iOS-ADE-DEV` (`device.deviceOwnership -eq "Corporate"`) | Include | VPP version only shown to corporate devices |

Both filters are already defined in Section 5.2 — no new filters are required.

---

## 8. References

### Microsoft Documentation

- [Set up web-based device enrollment for iOS/iPadOS](https://learn.microsoft.com/en-us/intune/device-enrollment/apple/setup-web-based-ios)
- [Set up Account-Driven Apple User Enrollment](https://learn.microsoft.com/en-us/intune/device-enrollment/apple/setup-account-driven-user)
- [Set up Just-in-Time Registration in Intune](https://learn.microsoft.com/en-us/intune/device-enrollment/apple/setup-just-in-time-registration)
- [Apple MDM Push Certificate setup](https://learn.microsoft.com/en-us/intune/device-enrollment/apple/create-mdm-push-certificate)
- [Overview of Apple device enrollment (personal devices)](https://learn.microsoft.com/en-us/intune/device-enrollment/apple/personal-device-options-ios)
- [iOS/iPadOS device enrollment guide](https://learn.microsoft.com/en-us/intune/device-enrollment/apple/guide-ios-ipados)

### Apple Resources

- [Apple Business portal](https://business.apple.com)
- [Apple Platform Deployment Guide](https://support.apple.com/guide/deployment/welcome/web)
- [Apple Developer – Implementing User Enrollment](https://developer.apple.com/documentation/devicemanagement/user_enrollment/onboarding_users_with_account_sign-in/implementing_the_simple_authentication_user-enrollment_flow)

---

*This document is part of the UniFy Endpoint – Microsoft Intune iOS/iPadOS Baseline series. Document maintained by Yoennis Olmo - Sr. Modern Work Consultant.*
