# iOS/iPadOS Intune Baseline — Settings Output Reference

CIS Apple iOS/iPadOS 26 Benchmark v1.0.0 aligned
Microsoft iOS/iPadOS Security Configuration Framework (April 2026)
Last Updated: May 2026

---

## Summary

| Category | Policy Count |
|---|---|
| Settings Catalog — Apple Intelligence & Siri | 1 |
| Settings Catalog — Apple Services | 1 |
| Settings Catalog — Data Protection | 2 |
| Settings Catalog — Device Privacy | 2 |
| Settings Catalog — App Management | 1 |
| Settings Catalog — Connectivity Controls | 1 |
| Settings Catalog — Device Pairing | 1 |
| Settings Catalog — Device Restrictions | 2 |
| Settings Catalog — Device Security | 2 |
| Settings Catalog — Accessibility & Sharing | 1 |
| Settings Catalog — iCloud & Storage | 2 |
| Settings Catalog — Lock Screen | 1 |
| Settings Catalog — Microsoft Enterprise SSO | 1 |
| Settings Catalog — Safari Browser | 2 |
| Settings Catalog — Software Update | 1 |
| Settings Catalog — Web-App-Store (EU) | 1 |
| Compliance Policies | 4 |
| App Protection (MAM) | 1 |
| App Configuration (Managed Device) | 13 |
| Defender for Endpoint | 2 |
| Conditional Access | 2 |
| **Total** | **44** |

---

## Settings Catalog

### Policy 1 — iOS_iPadOS - SC - DEV - Apple Intelligence & Siri - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | Corporate Devices (ADE/Supervised only — DDM; do NOT assign to BYOD) |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 Apple Intelligence and Siri controls for ADE/Corporate devices: External Intelligence Settings (ChatGPT/third-party AI integration), Intelligence Settings (on-device AI features including Apps sub-groups for Mail, Notes, Safari), Siri Settings. All settings delivered via Declarative Device Management (DDM) — incompatible with unsupervised BYOD devices. |

**Settings — External Intelligence Settings** (`externalintelligencesettings_externalintelligencesettings` group)

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Allow Sign In (External Intelligence) | externalintelligencesettings_allowsignin | false | Block user sign-in to external AI services (ChatGPT) — CIS Section 3.10.1 L1 |
| Allowed Workspace IDs | externalintelligencesettings_allowedworkspaceids | REPLACE | **REPLACE** with real Entra tenant GUID or approved workspace ID — OR remove if not restricting to specific approved AI workspaces. Note: `enabled=false` already blocks all External Intelligence regardless. |
| Enabled (External Intelligence) | externalintelligencesettings_enabled | false | Disable ChatGPT and third-party AI integration — CIS Section 3.10.1 L1 |

**Settings — Intelligence Settings** (`intelligencesettings_intelligencesettings` group)

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Allow Apple Intelligence Report | intelligencesettings_allowappleintelligencereport | false | Block diagnostic telemetry reporting to Apple — CIS L2 |
| Allow Genmoji | intelligencesettings_allowgenmoji | false | Block Genmoji — Operational |
| Allow Image Playground | intelligencesettings_allowimageplayground | false | Block AI image generation — CIS L2 |
| Allow Image Wand | intelligencesettings_allowimagewand | false | Block Image Wand — CIS L2 |
| Allow Personalized Handwriting Results | intelligencesettings_allowpersonalizedhandwritingresults | false | Block handwriting model training — CIS L2 |
| Allow Visual Intelligence Summary | intelligencesettings_allowvisualintelligencesummary | false | Block camera-based AI analysis — CIS Section 3.10.1 L1 |
| Allow Writing Tools | intelligencesettings_allowwritingtools | true | Intentional deviation from CIS Section 3.10.4 L1 — Writing Tools kept enabled for productivity; PCC on-device processing provides privacy safeguards. See Section 3.1 in iOS_CIS-Microsoft_Recommended_Settings_Guide.md. |
| Force On Device Only Dictation | intelligencesettings_forceondeviceonlydictation | true | Force dictation on-device only — CIS Section 3.10.1 L1 |
| Force On Device Only Translation | intelligencesettings_forceondeviceonlytranslation | true | Force translation on-device only — CIS Section 3.10.1 L1 |

**Settings — Intelligence Settings Apps Sub-groups** (nested within `intelligencesettings_apps` group)

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Apps → Mail → Allow Smart Replies | intelligencesettings_apps_mail_allowsmartreplies | false | Block AI Smart Reply suggestions in Mail — Operational |
| Apps → Mail → Allow Summary | intelligencesettings_apps_mail_allowsummary | false | Block AI-generated email summaries — **CIS Section 3.10.3 L1** |
| Apps → Notes → Allow Transcription | intelligencesettings_apps_notes_allowtranscription | false | Block Notes audio transcription — **CIS Section 3.10.2 L1** |
| Apps → Notes → Allow Transcription Summary | intelligencesettings_apps_notes_allowtranscriptionsummary | false | Block AI summary of Notes transcriptions — **CIS Section 3.10.2 L1** |
| Apps → Safari → Allow Summary | intelligencesettings_apps_safari_allowsummary | false | Block Safari AI summaries via Intelligence Settings — Operational (defense-in-depth alongside `safari_allowsummary=false` in Safari Browser policy) |

**Settings — Siri Settings** (`sirisettings_sirisettings` group)

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Allow User Generated Content (Siri) | sirisettings_allowusergeneratedcontent | false | Block Siri user-generated content learning — Operational (Siri fully disabled; no additional effect) |
| Allow While Locked (Siri) | sirisettings_allowwhilelocked | false | Block Siri while locked — CIS Section 3.2.1.2 L1 |
| Enabled (Siri) | sirisettings_enabled | false | Disable Siri entirely — Operational (CIS only requires blocking while locked; full disable is hardening choice) |
| Force Profanity Filter (Siri) | sirisettings_forceprofanityfilter | true | Force Siri profanity filter — Operational (Siri disabled; no functional effect; defense-in-depth) |

---


### Policy 2 — iOS_iPadOS - SC - DEV - Apple Services - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | Corporate Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 Apple Services restrictions for ADE/Supervised corporate devices. Requires supervision. |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Allow Book Store | com.apple.applicationaccess_allowbookstore | false | Block Apple Books store |
| Allow Book Store Erotica | com.apple.applicationaccess_allowbookstoreerotica | false | Block adult content in Books |
| Allow Explicit Content | com.apple.applicationaccess_allowexplicitcontent | false | Block explicit media content |
| Allow Find My Friends | com.apple.applicationaccess_allowfindmyfriends | false | Block Find My Friends |
| Allow Find My Friends Modification | com.apple.applicationaccess_allowfindmyfriendsmodification | false | Block modification of Find My Friends settings |
| Allow Game Center | com.apple.applicationaccess_allowgamecenter | false | Block Game Center |
| Allow iTunes | com.apple.applicationaccess_allowitunes | false | Block iTunes Store |
| Allow Music Service | com.apple.applicationaccess_allowmusicservice | false | Block Apple Music service |
| Allow News | com.apple.applicationaccess_allownews | false | Block Apple News |
| Allow Podcasts | com.apple.applicationaccess_allowpodcasts | false | Block Podcasts |

---


### Policy 4 — iOS_iPadOS - SC - DEV - Data Protection - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | Corporate Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 Data Protection for ADE/Supervised Corporate Devices. |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Allow AirDrop | com.apple.applicationaccess_allowairdrop | false | Block AirDrop |
| Allow AirPrint | com.apple.applicationaccess_allowairprint | false | Block AirPrint |
| Allow AirPrint Credentials Storage | com.apple.applicationaccess_allowairprintcredentialsstorage | false | Block AirPrint credentials in Keychain |
| Allow AirPrint iBeacon Discovery | com.apple.applicationaccess_allowairprintibeacondiscovery | false | Block iBeacon discovery of AirPrint printers |
| Allow Call Recording | com.apple.applicationaccess_allowcallrecording | false | Block call recording |
| Allow Cloud Private Relay | com.apple.applicationaccess_allowcloudprivaterelay | false | Block iCloud Private Relay |
| Allow Managed to Write Unmanaged Contacts | com.apple.applicationaccess_allowmanagedtowriteunmanagedcontacts | false | Block managed apps writing to unmanaged contacts |
| Allow Open From Managed to Unmanaged | com.apple.applicationaccess_allowopenfrommanagedtounmanaged | false | Block managed docs opening in unmanaged apps |
| Allow Open From Unmanaged to Managed | com.apple.applicationaccess_allowopenfromunmanagedtomanaged | false | Block unmanaged apps opening managed docs |
| Allow Screenshot | com.apple.applicationaccess_allowscreenshot | false | Block screen capture |
| Allow Unmanaged to Read Managed Contacts | com.apple.applicationaccess_allowunmanagedtoreadmanagedcontacts | false | Block unmanaged apps reading managed contacts |
| Force AirPlay Outgoing Requests Pairing Password | com.apple.applicationaccess_forceairplayoutgoingrequestspairingpassword | true | Require AirPlay pairing password |
| Require Managed Pasteboard | com.apple.applicationaccess_requiremanagedpasteboard | false | Managed pasteboard not required (set false) |

---


### Policy 5 — iOS_iPadOS - SC - DEV - Data Protection - BYOD Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | BYOD Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 data transfer controls for BYOD (unsupervised) devices: unmanaged-to-managed and managed-to-unmanaged open-in restrictions, and iCloud Private Relay. |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Allow Managed to Write Unmanaged Contacts | com.apple.applicationaccess_allowmanagedtowriteunmanagedcontacts | false | Block managed apps writing to unmanaged contacts |
| Allow Open From Managed to Unmanaged | com.apple.applicationaccess_allowopenfrommanagedtounmanaged | false | Block managed docs opening in unmanaged apps |
| Allow Open From Unmanaged to Managed | com.apple.applicationaccess_allowopenfromunmanagedtomanaged | false | Block unmanaged apps opening managed docs |
| Allow Screenshot | com.apple.applicationaccess_allowscreenshot | false | Block screen capture |
| Allow Unmanaged to Read Managed Contacts | com.apple.applicationaccess_allowunmanagedtoreadmanagedcontacts | false | Block unmanaged apps reading managed contacts |
| Allow Cloud Private Relay | com.apple.applicationaccess_allowcloudprivaterelay | false | Block iCloud Private Relay |
| Force AirDrop Unmanaged | com.apple.applicationaccess_forceairdropunmanaged | true | Force AirDrop to unmanaged mode |
| Force AirPlay Outgoing Requests Pairing Password | com.apple.applicationaccess_forceairplayoutgoingrequestspairingpassword | true | Require AirPlay pairing password |
| Require Managed Pasteboard | com.apple.applicationaccess_requiremanagedpasteboard | false | Managed pasteboard not required (set false) |

---


### Policy 6 — iOS_iPadOS - SC - DEV - Device Privacy - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | Corporate Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 privacy controls for ADE/Corporate devices: app analytics sharing, Apple diagnostic submission, advertising tracking (Limit Ad Tracking), personalized advertising block, Mail Privacy Protection, and Spotlight internet results. |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Enabled (App Analytics) | settings_item_appanalytics_enabled | false | Block app analytics sharing with developers |
| Enabled (Diagnostic Submission) | settings_item_diagnosticsubmission_enabled | false | Block diagnostic data submission |
| Allow Apple Personalized Advertising | com.apple.applicationaccess_allowapplepersonalizedadvertising | false | Block Apple personalized advertising |
| Allow Diagnostic Submission | com.apple.applicationaccess_allowdiagnosticsubmission | false | Block diagnostic data submission (MDM key) |
| Allow Diagnostic Submission Modification | com.apple.applicationaccess_allowdiagnosticsubmissionmodification | false | Block modification of diagnostic submission setting |
| Allow Mail Privacy Protection | com.apple.applicationaccess_allowmailprivacyprotection | true | Enable Mail Privacy Protection |
| Force Limit Ad Tracking | com.apple.applicationaccess_forcelimitadtracking | true | Force limit ad tracking |
| Allow Spotlight Internet Results | com.apple.applicationaccess_allowspotlightinternetresults | false | Block Spotlight internet/Siri suggestions sent to Apple |

---


### Policy 8 — iOS_iPadOS - SC - DEV - Device Privacy - BYOD Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | BYOD Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 privacy controls for BYOD (unsupervised) devices: Apple diagnostic submission, app analytics sharing, advertising tracking (Limit Ad Tracking), personalized advertising block, and Mail Privacy Protection. |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Enabled (App Analytics) | settings_item_appanalytics_enabled | false | Block app analytics sharing with developers |
| Enabled (Diagnostic Submission) | settings_item_diagnosticsubmission_enabled | false | Block diagnostic data submission |
| Allow Apple Personalized Advertising | com.apple.applicationaccess_allowapplepersonalizedadvertising | false | Block Apple personalized advertising |
| Allow Diagnostic Submission | com.apple.applicationaccess_allowdiagnosticsubmission | false | Block diagnostic data submission (MDM restriction key) |
| Allow Mail Privacy Protection | com.apple.applicationaccess_allowmailprivacyprotection | true | Enable Mail Privacy Protection |
| Force Limit Ad Tracking | com.apple.applicationaccess_forcelimitadtracking | true | Force limit ad tracking |

---


### Policy 9 — iOS_iPadOS - SC - DEV - App Management - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | Corporate Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 supervised app lifecycle controls for ADE/Corporate devices: app installation, removal, automatic downloads, App Clips, in-app purchases, enterprise app trust, system app removal, and UI-based installation. |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Allow App Clips | com.apple.applicationaccess_allowappclips | false | Block App Clips |
| Allow App Installation | com.apple.applicationaccess_allowappinstallation | false | Block app installation |
| Allow App Removal | com.apple.applicationaccess_allowappremoval | false | Block app removal |
| Allow Automatic App Downloads | com.apple.applicationaccess_allowautomaticappdownloads | false | Block automatic app downloads |
| Allow Enterprise App Trust | com.apple.applicationaccess_allowenterpriseapptrust | false | Block trust of new enterprise app authors |
| Allow In-App Purchases | com.apple.applicationaccess_allowinapppurchases | false | Block in-app purchases |
| Allow System App Removal | com.apple.applicationaccess_allowsystemappremoval | false | Block removal of system apps |
| Allow UI App Installation | com.apple.applicationaccess_allowuiappinstallation | false | Block App Store installation |
| Blocked App Bundle IDs | com.apple.applicationaccess_blockedappbundleids | com.apple.games, com.apple.tips, com.apple.Home, com.apple.freeform, com.apple.journal, com.apple.mobilemail, com.apple.tv, com.apple.iBooks, com.apple.Bridge, com.apple.Fitness, com.apple.Music, com.apple.stocks, com.apple.Health | Block specific pre-installed Apple apps by bundle ID |

---


### Policy 10 — iOS_iPadOS - SC - DEV - Connectivity Controls - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | Corporate Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 supervised connectivity controls for ADE/Corporate devices: Personal Hotspot modification, Bluetooth modification, NFC, cellular plan modification, app cellular data modification, eSIM modification, eSIM outgoing transfers, VPN creation, forced Wi-Fi power on, and untrusted TLS certificate prompts. |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Enabled (Personal Hotspot) | settings_item_personalhotspot_enabled | false | Block Personal Hotspot |
| Allow App Cellular Data Modification | com.apple.applicationaccess_allowappcellulardatamodification | false | Block modification of per-app cellular data settings |
| Allow Bluetooth Modification | com.apple.applicationaccess_allowbluetoothmodification | false | Block Bluetooth settings modification |
| Allow Cellular Plan Modification | com.apple.applicationaccess_allowcellularplanmodification | false | Block cellular plan modification |
| Allow eSIM Modification | com.apple.applicationaccess_allowesimmodification | false | Block eSIM configuration modification |
| Allow eSIM Outgoing Transfers | com.apple.applicationaccess_allowesimoutgoingtransfers | false | Block outgoing eSIM transfers |
| Allow NFC | com.apple.applicationaccess_allownfc | false | Block NFC |
| Allow Personal Hotspot Modification | com.apple.applicationaccess_allowpersonalhotspotmodification | false | Block Personal Hotspot modification |
| Allow Untrusted TLS Prompt | com.apple.applicationaccess_allowuntrustedtlsprompt | false | Block untrusted TLS certificate prompts |
| Allow VPN Creation | com.apple.applicationaccess_allowvpncreation | false | Block VPN creation by user |
| Force Wi-Fi Power On | com.apple.applicationaccess_forcewifipoweron | true | Force Wi-Fi always on |

---


### Policy 11 — iOS_iPadOS - SC - DEV - Device Restrictions - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | Corporate Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 supervised device restriction controls for ADE/Corporate devices: account modification, device naming, erase content and settings, configuration profile installation, notifications modification, default browser modification, MDM activation lock (disabled), and idle reboot. |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Activation Lock Allowed While Supervised | settings_item_mdmoptions_mdmoptions_activationlockallowedwhilesupervised | false | Activation Lock disabled on supervised devices; IT can reset/reassign without Apple ID |
| Idle Reboot Allowed | settings_item_mdmoptions_mdmoptions_idlerebootallowed | true | Allow MDM-triggered idle reboot |
| Allow Account Modification | com.apple.applicationaccess_allowaccountmodification | false | Block account settings modification |
| Allow Default Browser Modification | com.apple.applicationaccess_allowdefaultbrowsermodification | false | Block default browser modification |
| Allow Device Name Modification | com.apple.applicationaccess_allowdevicenamemodification | false | Block device name modification |
| Allow Enabling Restrictions | com.apple.applicationaccess_allowenablingrestrictions | false | Block enabling Screen Time restrictions |
| Allow Erase Content and Settings | com.apple.applicationaccess_allowerasecontentandsettings | false | Block Erase All Content and Settings |
| Allow Notifications Modification | com.apple.applicationaccess_allownotificationsmodification | false | Block notification settings modification |
| Allow UI Configuration Profile Installation | com.apple.applicationaccess_allowuiconfigurationprofileinstallation | false | Block configuration profile installation by user |
| Force Automatic Date and Time | com.apple.applicationaccess_forceautomaticdateandtime | false | Automatic date/time not enforced; users may set manually |

---


### Policy 12 — iOS_iPadOS - SC - DEV - Accessibility & Sharing - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | Corporate Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 supervised access and sharing controls for ADE/Corporate devices: remote screen observation, Handoff (activity continuation), iPhone Mirroring, and iPhone Widgets on Mac. |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Allow Activity Continuation | com.apple.applicationaccess_allowactivitycontinuation | false | Block Handoff/activity continuation |
| Allow iPhone Mirroring | com.apple.applicationaccess_allowiphonemirroring | false | Block iPhone Mirroring to Mac |
| Allow iPhone Widgets on Mac | com.apple.applicationaccess_allowiphonewidgetsonmac | false | Block iPhone widgets on Mac |
| Allow Remote Screen Observation | com.apple.applicationaccess_allowremotescreenobservation | false | Block remote screen observation |

---


### Policy 13 — iOS_iPadOS - SC - DEV - Device Restrictions - BYOD Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | BYOD Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 device restrictions for BYOD (unsupervised) devices: lock screen notifications view, Control Center access, Today View, Wallet access, auto-unlock, biometric unlock, untrusted TLS certificate prompt, Handoff (Activity Continuation), and Apple Watch wrist detection. |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Allow Lock Screen Notifications View | com.apple.applicationaccess_allowlockscreennotificationsview | false | Block notification center on lock screen |
| Allow Lock Screen Control Center | com.apple.applicationaccess_allowlockscreencontrolcenter | false | Block Control Center on lock screen |
| Allow Lock Screen Today View | com.apple.applicationaccess_allowlockscreentodayview | false | Block Today View on lock screen |
| Allow Passbook While Locked | com.apple.applicationaccess_allowpassbookwhilelocked | false | Block Wallet on lock screen |
| Allow Auto Unlock | com.apple.applicationaccess_allowautounlock | true | Allow Apple Watch to auto-unlock device |
| Allow Fingerprint for Unlock | com.apple.applicationaccess_allowfingerprintforunlock | true | Allow Touch ID / Face ID for device unlock |
| Allow Untrusted TLS Prompt | com.apple.applicationaccess_allowuntrustedtlsprompt | false | Block untrusted TLS certificate prompts |
| Allow Activity Continuation | com.apple.applicationaccess_allowactivitycontinuation | false | Block Handoff/activity continuation |
| Force Watch Wrist Detection | com.apple.applicationaccess_forcewatchwristdetection | false | Do not force Apple Watch wrist detection on BYOD |

---


### Policy 14 — iOS_iPadOS - SC - DEV - Device Security - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | Corporate Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 passcode and authentication controls for ADE/Corporate devices: passcode required, minimum length 6, auto-lock 1 min, maximum failed attempts, passcode age and history, password AutoFill block, password proximity requests block, password sharing block, and biometric authentication required before AutoFill. |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Allow Password AutoFill | com.apple.applicationaccess_allowpasswordautofill | false | Block password AutoFill |
| Allow Password Proximity Requests | com.apple.applicationaccess_allowpasswordproximityrequests | false | Block password proximity requests |
| Allow Password Sharing | com.apple.applicationaccess_allowpasswordsharing | false | Block password sharing |
| Force Authentication Before AutoFill | com.apple.applicationaccess_forceauthenticationbeforeautofill | true | Require Touch ID or Face ID before AutoFill |
| Allow Simple (Passcode) | com.apple.mobiledevice.passwordpolicy_allowsimple | false | Block simple passcodes |
| Force PIN | com.apple.mobiledevice.passwordpolicy_forcepin | true | Require passcode |
| Max Failed Attempts | com.apple.mobiledevice.passwordpolicy_maxfailedattempts | 5 | Maximum failed attempts before wipe |
| Max Grace Period | com.apple.mobiledevice.passwordpolicy_maxgraceperiod | 0 | Lock immediately (grace period 0 minutes) |
| Max Inactivity | com.apple.mobiledevice.passwordpolicy_maxinactivity | 1 | Auto-lock after 1 minute |
| Max PIN Age in Days | com.apple.mobiledevice.passwordpolicy_maxpinageindays | 180 | Passcode expires after 180 days |
| Min Length | com.apple.mobiledevice.passwordpolicy_minlength | 6 | Minimum passcode length 6 |
| PIN History | com.apple.mobiledevice.passwordpolicy_pinhistory | 5 | Prevent reuse of last 5 passcodes |
| Require Alphanumeric | com.apple.mobiledevice.passwordpolicy_requirealphanumeric | false | Numeric passcode accepted on corporate devices |

---


### Policy 15 — iOS_iPadOS - SC - DEV - Device Security - BYOD Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | BYOD Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 passcode and authentication controls for BYOD (unsupervised) devices: simple passcode block, auto-lock 5 min, passcode age and history. |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Allow Simple (Passcode) | com.apple.mobiledevice.passwordpolicy_allowsimple | false | Block simple passcodes |
| Force PIN | com.apple.mobiledevice.passwordpolicy_forcepin | true | Require passcode |
| Max Failed Attempts | com.apple.mobiledevice.passwordpolicy_maxfailedattempts | 6 | Maximum failed attempts before wipe |
| Max Grace Period | com.apple.mobiledevice.passwordpolicy_maxgraceperiod | 0 | Lock immediately (grace period 0 minutes) |
| Max Inactivity | com.apple.mobiledevice.passwordpolicy_maxinactivity | 5 | Auto-lock after 5 minutes |
| Max PIN Age in Days | com.apple.mobiledevice.passwordpolicy_maxpinageindays | 365 | Passcode expires after 365 days |
| Min Length | com.apple.mobiledevice.passwordpolicy_minlength | 6 | Minimum passcode length 6 |
| PIN History | com.apple.mobiledevice.passwordpolicy_pinhistory | 3 | Prevent reuse of last 3 passcodes |
| Require Alphanumeric | com.apple.mobiledevice.passwordpolicy_requirealphanumeric | false | Numeric passcode allowed on BYOD |

---


### Policy 16 — iOS_iPadOS - SC - DEV - Device Pairing - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | Corporate Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 device pairing controls for ADE/Corporate devices: audio accessory temporary pairing session (12-hour limit), Apple Watch pairing and wrist detection, host pairing restriction, proximity setup to new device, and USB Restricted Mode. |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Temporary Pairing Disabled | audioaccessory_temporarypairing_disabled | true | Disable temporary audio accessory pairing |
| Unpairing Time Hour | audioaccessory_temporarypairing_configuration_unpairingtime_hour | 12 | Auto-unpair audio accessories after 12 hours |
| Unpairing Time Policy | audioaccessory_temporarypairing_configuration_unpairingtime_policy | 1 | Policy value 1 for unpairing |
| Allow Paired Watch | com.apple.applicationaccess_allowpairedwatch | true | Allow paired Apple Watch |
| Force Watch Wrist Detection | com.apple.applicationaccess_forcewatchwristdetection | true | Force Apple Watch wrist detection |
| Allow Host Pairing | com.apple.applicationaccess_allowhostpairing | false | Block pairing with non-Configurator hosts |
| Allow Proximity Setup to New Device | com.apple.applicationaccess_allowproximitysetuptonewdevice | false | Block proximity setup to new device |
| Allow USB Restricted Mode | com.apple.applicationaccess_allowusbrestrictedmode | true | Enable USB restricted mode (block USB accessories after lock) |

---


### Policy 17 — iOS_iPadOS - SC - DEV - iCloud & Storage - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | Corporate Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 iCloud and storage controls for ADE/Corporate devices: iCloud backup, document sync, keychain sync, photo library, Shared Photo Stream, enterprise book backup/metadata sync, managed app cloud sync, encrypted backup enforcement, and Files network/USB drive access. |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Allow Cloud Backup | com.apple.applicationaccess_allowcloudbackup | false | Block iCloud backup |
| Allow Cloud Document Sync | com.apple.applicationaccess_allowclouddocumentsync | false | Block iCloud Drive document sync |
| Allow Cloud Keychain Sync | com.apple.applicationaccess_allowcloudkeychainsync | false | Block iCloud Keychain sync |
| Allow Cloud Photo Library | com.apple.applicationaccess_allowcloudphotolibrary | false | Block iCloud Photo Library |
| Allow Shared Stream | com.apple.applicationaccess_allowsharedstream | false | Block iCloud Shared Photo Stream |
| Allow Enterprise Book Backup | com.apple.applicationaccess_allowenterprisebookbackup | false | Block enterprise book backup |
| Allow Enterprise Book Metadata Sync | com.apple.applicationaccess_allowenterprisebookmetadatasync | false | Block enterprise book notes/highlights sync |
| Allow Files Network Drive Access | com.apple.applicationaccess_allowfilesnetworkdriveaccess | false | Block network drives in Files app |
| Allow Files USB Drive Access | com.apple.applicationaccess_allowfilesusbdriveaccess | false | Block USB drives in Files app |
| Allow Managed Apps Cloud Sync | com.apple.applicationaccess_allowmanagedappscloudsync | false | Block managed apps from syncing to iCloud |
| Force Encrypted Backup | com.apple.applicationaccess_forceencryptedbackup | true | Force encrypted backups |

---


### Policy 18 — iOS_iPadOS - SC - DEV - iCloud & Storage - BYOD Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | BYOD Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 iCloud and storage controls for BYOD (unsupervised) devices. |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Force Encrypted Backup | com.apple.applicationaccess_forceencryptedbackup | true | Force encrypted backups |
| Allow Managed Apps Cloud Sync | com.apple.applicationaccess_allowmanagedappscloudsync | false | Block managed apps from syncing to iCloud |

---


### Policy 19 — iOS_iPadOS - SC - DEV - Lock Screen - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | Corporate Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 lock screen controls for ADE/Corporate devices: Notification Center, Today View, Control Center, and Wallet access restrictions from the lock screen. REPLACE the asset tag and footnote values with your organization's details before deploying. |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Allow Lock Screen Control Center | com.apple.applicationaccess_allowlockscreencontrolcenter | false | Block Control Center on lock screen |
| Allow Lock Screen Notifications View | com.apple.applicationaccess_allowlockscreennotificationsview | false | Block notification center on lock screen |
| Allow Lock Screen Today View | com.apple.applicationaccess_allowlockscreentodayview | false | Block Today View on lock screen |
| Allow Passbook While Locked | com.apple.applicationaccess_allowpassbookwhilelocked | false | Block Wallet on lock screen |
| Asset Tag Information | com.apple.shareddeviceconfiguration_assettaginformation | REPLACE: Company Name | Asset: {{DEVICENAME}} | Replace with your organization's asset tag format |
| Lock Screen Footnote | com.apple.shareddeviceconfiguration_lockscreenfootnote | REPLACE: If found, call IT: +31-XXX-XXX-XXXX | Replace with your IT contact number |

---


### Policy 20 — iOS_iPadOS - SC - DEV - Microsoft Enterprise SSO - All Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | All Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 Microsoft Enterprise SSO extension for all enrolled devices: Entra ID credential SSO for MSAL-integrated Microsoft apps. Deploy before other policies — prerequisite for Conditional Access. |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Extension Data Key: Enable_SSO_On_All_ManagedApps | com.apple.extensiblesso_extensiondata_generickey_keytobereplaced | Enable_SSO_On_All_ManagedApps | Key name |
| Extension Data Value: Enable_SSO_On_All_ManagedApps | com.apple.extensiblesso_extensiondata_generickey_integer | 1 | Integer 1 = enabled |
| Extension Data Key: AppPrefixAllowList | com.apple.extensiblesso_extensiondata_generickey_keytobereplaced | AppPrefixAllowList | Key name |
| Extension Data Value: AppPrefixAllowList | com.apple.extensiblesso_extensiondata_generickey_string | com.microsoft.,com.apple. | App prefix allow list |
| Extension Data Key: browser_sso_interaction_enabled | com.apple.extensiblesso_extensiondata_generickey_keytobereplaced | browser_sso_interaction_enabled | Key name |
| Extension Data Value: browser_sso_interaction_enabled | com.apple.extensiblesso_extensiondata_generickey_integer | 1 | Integer 1 = enabled |
| Extension Data Key: disable_explicit_app_prompt | com.apple.extensiblesso_extensiondata_generickey_keytobereplaced | disable_explicit_app_prompt | Key name |
| Extension Data Value: disable_explicit_app_prompt | com.apple.extensiblesso_extensiondata_generickey_integer | 1 | Integer 1 = enabled |
| Extension Data Key: device_registration | com.apple.extensiblesso_extensiondata_generickey_keytobereplaced | device_registration | Key name |
| Extension Data Value: device_registration | com.apple.extensiblesso_extensiondata_generickey_string | {{DEVICEREGISTRATION}} | Intune device registration token |
| Extension Identifier | com.apple.extensiblesso_extensionidentifier | com.microsoft.azureauthenticator.ssoextension | Microsoft Authenticator SSO extension |
| Screen Locked Behavior | com.apple.extensiblesso_screenlockedbehavior | 0 | Default screen locked behavior |
| Type | com.apple.extensiblesso_type | 1 | Credential extension type |
| URL 1 | com.apple.extensiblesso_urls | https://login.microsoftonline.com | Global endpoint |
| URL 2 | com.apple.extensiblesso_urls | https://login.microsoft.com | Global endpoint |
| URL 3 | com.apple.extensiblesso_urls | https://sts.windows.net | Global STS |
| URL 4 | com.apple.extensiblesso_urls | https://login.partner.microsoftonline.cn | China cloud |
| URL 5 | com.apple.extensiblesso_urls | https://login.microsoftonline.us | US Gov cloud |
| URL 6 | com.apple.extensiblesso_urls | https://login-us.microsoftonline.com | US Gov cloud alternate |

---


### Policy 21 — iOS_iPadOS - SC - DEV - Safari Browser - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | Corporate Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 Safari controls for ADE/Corporate devices: AutoFill, pop-ups, fraud warning, private browsing block, third-party cookie block, Safari AI summary block, Microsoft 365 start page, and Microsoft cross-site tracking exception domains. |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Accept Cookies | safari_acceptcookies | 1 | Allow current website only (block third-party cookies) |
| Allow Disabling Fraud Warning | safari_allowdisablingfraudwarning | false | Block disabling fraud warning |
| Allow JavaScript | safari_allowjavascript | true | Allow JavaScript |
| Allow Popups | safari_allowpopups | false | Block pop-ups |
| Allow Private Browsing | safari_allowprivatebrowsing | false | Block private browsing (supervised only) |
| Allow Summary | safari_allowsummary | false | Block Safari AI summary |
| New Tab Start Page URL | safari_newtabstartpage_homepageurl | https://m365.cloud.microsoft/ | Microsoft 365 as Safari start page |
| New Tab Start Page Type | safari_newtabstartpage_pagetype | 0 | Custom URL start page (type 0) |
| Cross-Site Tracking Prevention Relaxed Domains | com.apple.domains_crosssitetrackingpreventionrelaxeddomains | microsoft.com, login.microsoftonline.com, manage.microsoft.com, sts.windows.net | Allow Microsoft authentication domains to work with cross-site tracking prevention |
| Allow Safari History Clearing | com.apple.applicationaccess_allowsafarihistoryclearing | false | Block Safari history clearing |
| Safari Allow AutoFill | com.apple.applicationaccess_safariallowautofill | false | Block Safari AutoFill |
| Safari Force Fraud Warning | com.apple.applicationaccess_safariforcefraudwarning | true | Force fraud warning enabled |

---


### Policy 22 — iOS_iPadOS - SC - DEV - Safari Browser - BYOD Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | BYOD Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 Safari controls for BYOD (unsupervised) devices: fraud warning user override allowed, third-party cookie block, force fraud warning, and Microsoft cross-site tracking exception domains. |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Accept Cookies | safari_acceptcookies | 1 | Allow current website only (block third-party cookies) |
| Allow Disabling Fraud Warning | safari_allowdisablingfraudwarning | true | BYOD users may disable Safari fraud warning on personal device |
| Cross-Site Tracking Prevention Relaxed Domains | com.apple.domains_crosssitetrackingpreventionrelaxeddomains | microsoft.com, login.microsoftonline.com, manage.microsoft.com, sts.windows.net | Allow Microsoft authentication domains |
| Safari Force Fraud Warning | com.apple.applicationaccess_safariforcefraudwarning | true | Force fraud warning enabled (MDM enforcement layer) |

> Note: Safari AutoFill blocking (safariallowautofill) requires a supervised device as of iOS 13 and cannot be enforced via MDM on BYOD (unsupervised) devices. This control is implemented in the Corporate Devices policy only.

> Note: `allowdisablingfraudwarning` is set to `true` on BYOD, allowing users to turn off the Safari fraud warning toggle if they choose. `safariforcefraudwarning` (MDM key) remains `true` as a separate enforcement layer. On BYOD devices, restricting personal Safari behavior disproportionately affects personal use of a personal device.

---


### Policy 23 — iOS_iPadOS - SC - DEV - Software Update - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | Corporate Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 software update enforcement for ADE/Corporate devices: DDM enforce latest iOS/iPadOS version with 14-day deferral window and 02:00 install time; automatic download, OS update install, and Background Security Improvement install (AlwaysOn); Rapid Security Response enforced and rollback blocked; 14-day combined deferral; update notifications enabled. |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Enforce Latest Software Update Version | ddm-latestsoftwareupdate_enforcelatestsoftwareupdateversion | 0 (enforce latest) | DDM enforcement choice value 0 = enforce |
| Delay in Days | ddm-latestsoftwareupdate_delayindays | 14 | 14-day IT validation window |
| Install Time | ddm-latestsoftwareupdate_installtime | 02:00 | Overnight maintenance window |
| Automatic Actions Download | softwareupdate_automaticactions_download | 1 | Automatically download updates |
| Automatic Actions Install OS Updates | softwareupdate_automaticactions_installosupdates | 1 | Automatically install OS updates |
| Install Security Update (Background Security Improvement) | softwareupdate_automaticactions_installsecurityupdate | 1 (AlwaysOn) | Background Security Improvement installs always on; user cannot disable |
| Rapid Security Response Enable | softwareupdate_rapidsecurityresponse_enable | true | Enable Rapid Security Response |
| Rapid Security Response Enable Rollback | softwareupdate_rapidsecurityresponse_enablerollback | false | Block RSR rollback |
| Deferrals Combined Period in Days | softwareupdate_deferrals_combinedperiodindays | 14 | Combined deferral period 14 days |
| Notifications | softwareupdate_notifications | true | Show software update notifications |
| Recommended Cadence | softwareupdate_recommendedcadence | 2 | Monthly (major releases) |

---


### Policy 24 — iOS_iPadOS - SC - DEV - Web-App-Store (EU) - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | Settings Catalog |
| Target | Corporate Devices |
| Folder | SettingsCatalog\ |
| Description | CIS iOS/iPadOS 26 Benchmark v1.0.0 EU Digital Markets Act compliance controls for ADE/Corporate devices: alternative app marketplaces, web distribution, and alternative browser engines (supervised, EU region only). |

**Settings**

| Setting | MDM Key | Value | Notes |
|---|---|---|---|
| Allow Marketplace App Installation | com.apple.applicationaccess_allowmarketplaceappinstallation | false | Block third-party app marketplaces (EU DMA) |
| Allow Web Distribution App Installation | com.apple.applicationaccess_allowwebdistributionappinstallation | false | Block web distribution app installation (EU DMA) |

---

## Compliance Policies

Compliance policies use a flat model (no L1/L2/L3 tiers): one consolidated policy per device track, plus a separate MDE-only policy for Defender for Endpoint threat signal. Both the base compliance and MDE compliance are assigned to the same group — Intune evaluates each independently and the device must satisfy all assigned policies.

### Policy 25 — iOS_iPadOS - CP - DEV - Compliance - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | Compliance Policy |
| Scope Assignment | DEV |
| Target | ADE / Supervised (Corporate) |
| Folder | CompliancePolicies\ |
| File | iOS_iPadOS - CP - DEV - Compliance - Corporate Devices.json |
| Assign To | `iOS - DEV - Corporate - All` |

**Settings**

| Setting | Value | Notes |
|---|---|---|
| Jailbroken Devices | Block | |
| Require Passcode | Require | |
| Simple Passwords | Block | |
| Required Password Type | Numeric | |
| Minimum Password Length | 6 | |
| Password Expiration (days) | 180 | |
| Previous Passwords | 5 | |
| Screen Timeout Before Lock | 5 minutes | |
| Require Encryption | Require | |
| Minimum OS Version | 26.4.2 | Update as new iOS versions release |
| MDE Threat Protection | Not Configured | Enforced via separate MDE compliance policy |
| Mark Noncompliant | Immediately (0h) | Push notification sent immediately |
| Block Access After | 24 hours | 1-day grace period before access is restricted |

---


### Policy 26 — iOS_iPadOS - CP - DEV - Compliance - MDE - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | Compliance Policy — MDE Only |
| Scope Assignment | DEV |
| Target | ADE / Supervised (Corporate) |
| Folder | CompliancePolicies\ |
| File | iOS_iPadOS - CP - DEV - Compliance - MDE - Corporate Devices.json |
| Assign To | `iOS - DEV - Corporate - All` |
| Note | Contains only MDE threat signal — assign alongside Policy 25. No passcode or OS settings. |

**Settings**

| Setting | Value | Notes |
|---|---|---|
| MDE Advanced Threat Protection Level | Low or below | Machine Risk Score must be Low (or Clear) |
| Block Access After | Immediately (0h) | No grace period for active threats on corporate devices |

---


### Policy 27 — iOS_iPadOS - CP - DEV - Compliance - BYOD Devices

| Property | Value |
|---|---|
| Policy Type | Compliance Policy |
| Scope Assignment | DEV |
| Target | BYOD / Web-Based Device Enrollment |
| Folder | CompliancePolicies\ |
| File | iOS_iPadOS - CP - DEV - Compliance - BYOD Devices.json |
| Assign To | `iOS - DEV - BYOD - All` |

**Settings**

| Setting | Value | Notes |
|---|---|---|
| Jailbroken Devices | Block | |
| Require Passcode | Require | |
| Simple Passwords | Block | |
| Required Password Type | Numeric | |
| Minimum Password Length | 6 | |
| Maximum Inactivity Before Lock | 5 minutes | More lenient for personal devices |
| Screen Timeout Before Lock | 5 minutes | |
| Require Encryption | Require | |
| Minimum OS Version | 18.7.8 | Minimum currently supported iOS |
| MDE Threat Protection | Not Configured | Enforced via separate MDE compliance policy |
| Mark Noncompliant | Immediately (0h) | Push notification sent immediately |
| Block Access After | 48 hours | 2-day grace period for personal devices |

---


### Policy 28 — iOS_iPadOS - CP - DEV - Compliance - MDE - BYOD Devices

| Property | Value |
|---|---|
| Policy Type | Compliance Policy — MDE Only |
| Scope Assignment | DEV |
| Target | BYOD / Web-Based Device Enrollment |
| Folder | CompliancePolicies\ |
| File | iOS_iPadOS - CP - DEV - Compliance - MDE - BYOD Devices.json |
| Assign To | `iOS - DEV - BYOD - All` |
| Note | Contains only MDE threat signal — assign alongside Policy 27. More lenient threshold for personal devices. |

**Settings**

| Setting | Value | Notes |
|---|---|---|
| MDE Advanced Threat Protection Level | Medium or below | Machine Risk Score must be Medium or lower |
| Block Access After | 24 hours | 1-day grace period for BYOD — minor findings tolerated |

---

## App Protection Policies (MAM)

A single L2 App Protection Policy covers all BYOD users. This policy applies Microsoft Level 2 (Enterprise Enhanced Data Protection) controls to all Core Microsoft Apps on BYOD devices.

### Policy 29 — iOS_iPadOS - APP - USR - App Protection - MAM - L2 - BYOD Devices

| Property | Value |
|---|---|
| Policy Type | App Protection Policy |
| Scope Assignment | USR |
| Target | All Core Microsoft Apps — BYOD Users |
| App Scope | allCoreMicrosoftApps (11 apps: Edge, Excel, Outlook, PowerPoint, Word, Office, OneNote, SharePoint, OneDrive, Teams, To Do) |
| Folder | AppProtection_MAM\ |
| File | iOS_iPadOS - APP - USR - App Protection - MAM - L2 - BYOD Devices.json |
| Assign To | `iOS - USR - Personal-Owned-Users - BYOD` (or Pilot for initial rollout) |

**Data Protection Settings**

| Setting | Value | Notes |
|---|---|---|
| Send org data to other apps | Policy managed apps | Outbound data restricted to managed apps only |
| Receive data from other apps | All apps | Inbound from any app allowed |
| Save copies of org data | Block | |
| Allowed storage locations | OneDrive for Business, SharePoint, Local Storage | |
| Restrict cut/copy/paste | Policy managed apps with paste in | |
| Screen capture | Block | |
| Encrypt org data | When device is locked | |
| Genmoji | Block | L2 Apple Intelligence control |
| Writing Tools | Not Blocked | Writing Tools allowed (evaluated separately from Genmoji) |
| Org data notifications | Allow | Intentional BYOD deviation — notification content visible on lock screen; restricting on personal devices is too disruptive to personal use |
| Block data ingestion from unmanaged apps | Block | |
| Allowed ingest locations | OneDrive for Business, SharePoint, Camera | |
| Open links in managed browser | Require (Microsoft Edge) | |
| Print org data | Block | |
| Contact sync | Allow | |
| Filter open-in to managed apps only | Yes | |

**Access Requirements**

| Setting | Value |
|---|---|
| PIN for access | Require |
| PIN type | Numeric |
| Simple PIN | Block |
| Minimum PIN length | 6 |
| Maximum PIN attempts | 5 — then block |
| Biometric override PIN after | 30 minutes |
| Touch ID / Face ID | Allow |
| Device compliance required | Require |

**Conditional Launch**

| Setting | Value | Action |
|---|---|---|
| Offline check interval | 30 minutes online / 12 hours offline | |
| Offline wipe | 90 days | Wipe data |
| Minimum OS version | 18.7.8 | Block access |
| Jailbroken devices | — | Block access |
| Max device threat level | Not Configured | |
| MTD remediation action | Block | |

**Universal Links**

The policy includes the standard Microsoft universal link list (Teams, SharePoint, OneDrive, Power Apps, Power BI, Yammer, Zoom) to ensure org links open in managed apps.

---

## App Configuration Policies

Thirteen App Configuration Policies use `iosMobileAppConfiguration` (Managed Device / MDM channel) and require device enrollment — stored in `AppConfiguration_ManagedDevice\`. Outlook has two policies: Corporate (VPP app) and BYOD (App Store app). Edge has two policies: Corporate (MDM) and enrolled BYOD (MDM). OneDrive, Teams, SharePoint each have Corporate and BYOD variants. The Defender BYOD policy sets `UserEnrollmentEnabled=false` (devices are web-based enrolled, not Apple User Enrollment). OneDrive Corporate and SharePoint Corporate include `REPLACE:` placeholder values for `allowedTenantID` — update with the Entra tenant GUID before import. Teams Corporate includes a `REPLACE:` placeholder for `AllowedAccounts` — update with the corporate UPN domain before import.

### Policy 30 — iOS_iPadOS - ACP - DEV - Microsoft Defender - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | App Configuration — Managed Devices (MDM channel) |
| odata.type | #microsoft.graph.iosMobileAppConfiguration |
| Scope Assignment | DEV |
| Target | ADE / Supervised (Corporate) |
| Folder | AppConfiguration_ManagedDevice\ |
| Target App | Microsoft Defender for Endpoint — VPP (App ID: 5fdbfbb7-028f-4495-9991-8104e4657b83) |
| Assign To | `iOS - DEV - Corporate - All` with `Filter - iOS - ADE-Corporate` |

**Settings**

| Configuration Key | Value Type | Value |
|---|---|---|
| issupervised | String | {{issupervised}} |
| WebProtection | String | true |
| DefenderNetworkProtectionEnable | String | true |
| DefenderTVMPrivacyMode | Integer | 0 |
| DefenderOpenNetworkDetection | Integer | 2 |
| DefenderEndUserTrustFlowEnable | String | true |
| DefenderNetworkProtectionAutoRemediation | String | true |
| DefenderNetworkProtectionPrivacy | String | true |
| DefenderExcludeURLInReport | Integer | 0 |
| DisableSignOut | String | true |
| IntuneMAMUPN | String | {{userprincipalname}} |
| SuppressOSUpdateNotification | String | true |
| DefenderDeviceTag | String | iOS-iPadOS-MDM |

---


### Policy 31 — iOS_iPadOS - ACP - DEV - Microsoft Defender - BYOD Devices

| Property | Value |
|---|---|
| Policy Type | App Configuration — Managed Devices (MDM channel) |
| odata.type | #microsoft.graph.iosMobileAppConfiguration |
| Scope Assignment | DEV |
| Target | BYOD Devices (Account-Driven User Enrollment) |
| Folder | AppConfiguration_ManagedDevice\ |
| Target App | Microsoft Defender for Endpoint (App ID: 50a78c17-ad84-4b43-b7d3-2a281675776a) |
| Assign To | `iOS - DEV - BYOD - All` |

**Settings**

| Configuration Key | Value Type | Value |
|---|---|---|
| issupervised | String | {{issupervised}} |
| UserEnrollmentEnabled | String | false |
| WebProtection | String | true |
| DefenderNetworkProtectionEnable | String | true |
| DefenderTVMPrivacyMode | String | true |
| DefenderOpenNetworkDetection | Integer | 2 |
| DefenderEndUserTrustFlowEnable | String | true |
| DefenderNetworkProtectionAutoRemediation | String | true |
| DefenderExcludeURLInReport | Integer | 1 |
| DefenderNetworkProtectionPrivacy | String | true |
| DisableSignOut | String | true |
| IntuneMAMUPN | String | {{userprincipalname}} |
| DefenderDeviceTag | String | iOS-iPadOS-BYOD-MDM |

---


### Policy 32 — iOS_iPadOS - ACP - DEV - Microsoft Authenticator - Shared Device Mode

| Property | Value |
|---|---|
| Policy Type | App Configuration — Managed Devices (MDM channel) |
| odata.type | #microsoft.graph.iosMobileAppConfiguration |
| Scope Assignment | DEV |
| Target | Entra Shared Device Mode (SDM) Devices |
| Folder | AppConfiguration_ManagedDevice\ |
| Target App | Microsoft Authenticator (GUID: 77e8db52-1e5e-421b-8b6f-2476f7509f00) |
| Assign To | `iOS - DEV - SharedDevice - All` |

**Settings**

| Configuration Key | Value Type | Value |
|---|---|---|
| sharedDeviceMode | Boolean | true |

---


### Policy 33 — iOS_iPadOS - ACP - DEV - Microsoft Edge - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | App Configuration — Managed Devices (MDM channel) |
| odata.type | #microsoft.graph.iosMobileAppConfiguration |
| Scope Assignment | DEV |
| Target | Corporate Devices |
| Folder | AppConfiguration_ManagedDevice\ |
| Target App | Microsoft Edge: AI Browser — VPP (App ID: 029db025-d0b3-4a3d-80d1-fe2a51428dd9) |
| Assign To | `iOS - DEV - Corporate-Owned-Devices` with `iOS-ADE-DEV` filter |

**Settings**

| Configuration Key | Value Type | Value | Notes |
|---|---|---|---|
| com.microsoft.intune.mam.managedbrowser.homepage | String | https://m365.cloud.microsoft/ | Sets browser home page to M365 portal |
| com.microsoft.intune.mam.managedbrowser.disabledFeatures | String | inprivate\|password\|autofill | Blocks InPrivate browsing, Edge password manager, and form autofill |
| IntuneMAMUPN | String | {{UserPrincipalName}} | Identifies enrolled user for MAM identity |
| IntuneMAMAllowedAccountsOnly | String | Enabled | Restricts Edge to corporate account only |
| com.microsoft.intune.mam.managedbrowser.SmartScreenEnabled | Boolean | true | Enables Microsoft Defender SmartScreen anti-phishing/malware protection |
| com.microsoft.intune.mam.managedbrowser.AllowSearchEngineCustomization | Boolean | false | Prevents users from changing the default search engine |
| com.microsoft.intune.mam.managedbrowser.SearchEngineDefault | String | https://duckduckgo.com/ | Sets DuckDuckGo as default search engine |
| com.microsoft.intune.mam.managedbrowser.NewTabPage.CustomURL | String | https://m365.cloud.microsoft/ | Sets M365 portal as new tab page |

---


### Policy 34 — iOS_iPadOS - ACP - DEV - Microsoft Edge - BYOD Devices

| Property | Value |
|---|---|
| Policy Type | App Configuration — Managed Devices (MDM channel) |
| odata.type | #microsoft.graph.iosMobileAppConfiguration |
| Scope Assignment | DEV |
| Target | BYOD Devices |
| Folder | AppConfiguration_ManagedDevice\ |
| Target App | Microsoft Edge: AI Browser (App ID: 5b1994c5-9482-4ad5-baf4-3deee89441b6) |
| Assign To | `iOS - DEV - Personal-Owned-Devices - BYOD` (or Pilot) |

**Settings**

| Configuration Key | Value Type | Value | Notes |
|---|---|---|---|
| IntuneMAMUPN | String | {{UserPrincipalName}} | Pre-populates sign-in |
| com.microsoft.intune.mam.managedbrowser.disabledFeatures | String | inprivate\|password\|autofill | Blocks InPrivate, password save, autofill |
| com.microsoft.intune.mam.managedbrowser.SmartScreenEnabled | Boolean | true | Enables Microsoft Defender SmartScreen anti-phishing/malware protection |

---


### Policy 35 — iOS_iPadOS - ACP - DEV - Microsoft OneDrive - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | App Configuration — Managed Devices (MDM channel) |
| odata.type | #microsoft.graph.iosMobileAppConfiguration |
| Scope Assignment | DEV |
| Target | Corporate Devices |
| Folder | AppConfiguration_ManagedDevice\ |
| Target App | Microsoft OneDrive — VPP (App ID: 27d89c62-7ef3-4d4b-9ba7-c6a89845e4eb) |
| Assign To | `iOS - DEV - Corporate - All` with `Filter - iOS - ADE-Corporate` |

**Settings**

| Configuration Key | Value Type | Value | Notes |
|---|---|---|---|
| IntuneMAMUPN | String | {{UserPrincipalName}} | Identifies enrolled user for MAM identity |
| IntuneMAMAllowedAccountsOnly | String | Enabled | Restricts OneDrive to corporate account only |
| com.microsoft.sharepoint.allowedTenantID | String | REPLACE:your-entra-tenant-guid | Restricts OneDrive sync to corporate Entra tenant; update with Entra tenant GUID before import |
| com.microsoft.sharepoint.blockPersonalOneDrive | Boolean | true | Blocks access to personal Microsoft OneDrive storage |

---


### Policy 36 — iOS_iPadOS - ACP - DEV - Microsoft OneDrive - BYOD Devices

| Property | Value |
|---|---|
| Policy Type | App Configuration — Managed Devices (MDM channel) |
| odata.type | #microsoft.graph.iosMobileAppConfiguration |
| Scope Assignment | DEV |
| Target | BYOD Devices |
| Folder | AppConfiguration_ManagedDevice\ |
| Target App | Microsoft OneDrive (App ID: 568c78cd-9aa4-47e3-adef-b709951d548c) |
| Assign To | `iOS - DEV - Personal-Owned-Devices - BYOD` (or Pilot) |

**Settings**

| Configuration Key | Value Type | Value |
|---|---|---|
| IntuneMAMUPN | String | {{UserPrincipalName}} |

---


### Policy 37 — iOS_iPadOS - ACP - DEV - Microsoft Teams - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | App Configuration — Managed Devices (MDM channel) |
| odata.type | #microsoft.graph.iosMobileAppConfiguration |
| Scope Assignment | DEV |
| Target | Corporate Devices |
| Folder | AppConfiguration_ManagedDevice\ |
| Target App | Microsoft Teams — VPP (App ID: 104dd256-e504-4c25-9bd7-24fd39b150b1) |
| Assign To | `iOS - DEV - Corporate - All` with `Filter - iOS - ADE-Corporate` |

**Settings**

| Configuration Key | Value Type | Value | Notes |
|---|---|---|---|
| IntuneMAMUPN | String | {{UserPrincipalName}} | Identifies enrolled user for MAM identity |
| IntuneMAMAllowedAccountsOnly | String | Enabled | Restricts Teams to corporate account only |
| com.microsoft.teams.account.AllowedAccounts | String | REPLACE:your-corporate-domain.com | Blocks personal Microsoft account sign-in; update with corporate UPN domain (e.g., yourdomain.com) before import |

---


### Policy 38 — iOS_iPadOS - ACP - DEV - Microsoft Teams - BYOD Devices

| Property | Value |
|---|---|
| Policy Type | App Configuration — Managed Devices (MDM channel) |
| odata.type | #microsoft.graph.iosMobileAppConfiguration |
| Scope Assignment | DEV |
| Target | BYOD Devices |
| Folder | AppConfiguration_ManagedDevice\ |
| Target App | Microsoft Teams (App ID: bdaf6149-0ad7-47f6-89b8-6fb429fa9184) |
| Assign To | `iOS - DEV - Personal-Owned-Devices - BYOD` (or Pilot) |

**Settings**

| Configuration Key | Value Type | Value |
|---|---|---|
| IntuneMAMUPN | String | {{UserPrincipalName}} |

---


### Policy 39 — iOS_iPadOS - ACP - DEV - SharePoint - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | App Configuration — Managed Devices (MDM channel) |
| odata.type | #microsoft.graph.iosMobileAppConfiguration |
| Scope Assignment | DEV |
| Target | Corporate Devices |
| Folder | AppConfiguration_ManagedDevice\ |
| Target App | Microsoft SharePoint — VPP (App ID: 6dcbb6aa-5829-41ae-af25-d71dc32b779e) |
| Assign To | `iOS - DEV - Corporate - All` with `Filter - iOS - ADE-Corporate` |

**Settings**

| Configuration Key | Value Type | Value | Notes |
|---|---|---|---|
| IntuneMAMUPN | String | {{UserPrincipalName}} | Identifies enrolled user for MAM identity |
| IntuneMAMAllowedAccountsOnly | String | Enabled | Restricts SharePoint to corporate account only |
| com.microsoft.sharepoint.allowedTenantID | String | REPLACE:your-entra-tenant-guid | Restricts SharePoint to corporate Entra tenant; update with Entra tenant GUID before import |

---


### Policy 40 — iOS_iPadOS - ACP - DEV - SharePoint - BYOD Devices

| Property | Value |
|---|---|
| Policy Type | App Configuration — Managed Devices (MDM channel) |
| odata.type | #microsoft.graph.iosMobileAppConfiguration |
| Scope Assignment | DEV |
| Target | BYOD Devices |
| Folder | AppConfiguration_ManagedDevice\ |
| Target App | Microsoft SharePoint (App ID: b6941db7-fa1b-4475-b483-31bed43bffb4) |
| Assign To | `iOS - DEV - Personal-Owned-Devices - BYOD` (or Pilot) |

**Settings**

| Configuration Key | Value Type | Value |
|---|---|---|
| IntuneMAMUPN | String | {{UserPrincipalName}} |

---


### Policy 41 — iOS_iPadOS - ACP - DEV - Outlook Settings - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | App Configuration — Managed Devices (MDM channel) |
| odata.type | #microsoft.graph.iosMobileAppConfiguration |
| Scope Assignment | DEV |
| Target | Corporate Devices |
| Folder | AppConfiguration_ManagedDevice\ |
| Target App | Microsoft Outlook — VPP (App ID: 90cad2f9-fda9-4e65-af48-c349a2491351) |
| Assign To | `iOS - DEV - Corporate - All` with `Filter - iOS - ADE-Corporate` |

**Settings**

| Configuration Key | Value Type | Value | Notes |
|---|---|---|---|
| com.microsoft.outlook.EmailProfile.AccountType | String | ModernAuth | Enforces Modern Authentication (OAuth) |
| com.microsoft.outlook.EmailProfile.EmailUPN | String | {{userprincipalname}} | Pre-populates sign-in UPN |
| com.microsoft.outlook.EmailProfile.EmailAddress | String | {{mail}} | Pre-populates email address |
| IntuneMAMAllowedAccountsOnly | String | Enabled | Restricts Outlook to corporate account only |
| com.microsoft.outlook.Mail.FocusedInbox | Boolean | false | Disables Focused Inbox (AI sorting) |
| com.microsoft.outlook.Contacts.LocalSyncEnabled | Boolean | true | Allows Outlook contacts to sync to iOS Contacts |
| com.microsoft.outlook.Contacts.LocalSyncEnabled.UserChangeAllowed | Boolean | false | Locks contact sync — user cannot change |
| com.microsoft.outlook.Mail.SuggestedRepliesEnabled | Boolean | false | Disables AI suggested replies |
| com.microsoft.outlook.Mail.ExternalRecipientsToolTipEnabled | Boolean | true | Warns user when composing to external addresses |
| com.microsoft.outlook.Mail.OrganizeByThreadEnabled | Boolean | false | Disables conversation thread view |
| com.microsoft.outlook.Mail.PlayMyEmailsEnabled | Boolean | false | Disables Play My Emails (AI audio reading) |
| com.microsoft.outlook.Mail.OfficeFeedEnabled | Boolean | false | Disables the Office Feed (activity/document suggestions) |
| com.microsoft.outlook.Auth.Biometric | Boolean | true | Requires Face ID / Touch ID to open Outlook |
| com.microsoft.outlook.Mail.BlockFileImport | Boolean | true | Blocks attaching files from personal cloud storage |
| com.microsoft.outlook.Mail.BlockPrinting | Boolean | true | Prevents printing of emails and attachments |
| IntuneMAMUPN | String | {{userprincipalname}} | Identifies enrolled user for MAM identity |

---


### Policy 42 — iOS_iPadOS - ACP - DEV - Outlook Settings - BYOD Devices

| Property | Value |
|---|---|
| Policy Type | App Configuration — Managed Devices (MDM channel) |
| odata.type | #microsoft.graph.iosMobileAppConfiguration |
| Scope Assignment | DEV |
| Target | BYOD Devices |
| Folder | AppConfiguration_ManagedDevice\ |
| Target App | Microsoft Outlook (App ID: eed2c3db-a6a9-44c7-bd64-a340fed4f69d) |
| Assign To | `iOS - DEV - Personal-Owned-Devices - BYOD` (or Pilot) |

**Settings**

| Configuration Key | Value Type | Value | Notes |
|---|---|---|---|
| com.microsoft.outlook.EmailProfile.AccountType | String | ModernAuth | Enforces Modern Authentication (OAuth) |
| com.microsoft.outlook.EmailProfile.EmailUPN | String | {{userprincipalname}} | Pre-populates sign-in UPN |
| com.microsoft.outlook.EmailProfile.EmailAddress | String | {{mail}} | Pre-populates email address |
| com.microsoft.outlook.Mail.FocusedInbox | Boolean | false | Disables Focused Inbox (AI sorting) |
| com.microsoft.outlook.Mail.SuggestedRepliesEnabled | Boolean | false | Disables AI suggested replies |
| com.microsoft.outlook.Mail.PlayMyEmailsEnabled | Boolean | false | Disables Play My Emails (AI audio reading) |
| com.microsoft.outlook.Mail.ExternalRecipientsToolTipEnabled | Boolean | true | Warns user when composing to external addresses |
| com.microsoft.outlook.Mail.OrganizeByThreadEnabled | Boolean | false | Disables conversation thread view |
| com.microsoft.outlook.Mail.OfficeFeedEnabled | Boolean | false | Disables the Office Feed (activity/document suggestions) |
| com.microsoft.outlook.Auth.Biometric | Boolean | true | Requires Face ID / Touch ID to open Outlook |
| com.microsoft.outlook.Contacts.LocalSyncEnabled | Boolean | false | Prevents Outlook contacts from syncing to iOS Contacts |

---

## Defender for Endpoint

### Policy 43 — iOS_iPadOS - CP - DEV - Zero-Touch-Control-Filter - MDE - Corporate Devices

| Property | Value |
|---|---|
| Policy Type | Custom Profile (.mobileconfig) |
| Scope Assignment | DEV |
| Target | ADE / Supervised (Corporate) |
| Folder | DeviceConfiguration_DefenderForEndpoint\ |
| Source | Adapted from Other iOS Policy Configurations — display name updated, payload unchanged |

**Settings**

| Setting | Value | Notes |
|---|---|---|
| Profile Type | com.apple.webcontentfilter | Apple Web Content Filter payload |
| Plugin Bundle ID | com.microsoft.scmx | MDE Control Filter extension |
| Delivery Method | Network Extension | No local VPN needed on supervised devices |
| Filter Type | Plug-in | MDE provides content filtering |

---


### Policy 44 — iOS_iPadOS - DC - DEV - Zero-Touch-Onboarding-VPN - MDE - BYOD Devices

| Property | Value |
|---|---|
| Policy Type | VPN Profile |
| Scope Assignment | DEV |
| Target | BYOD / Unsupervised |
| Folder | DeviceConfiguration_DefenderForEndpoint\ |
| Source | Adapted from Other iOS Policy Configurations — display name updated, VPN configuration unchanged |

**Settings**

| Setting | Value | Notes |
|---|---|---|
| Connection Name | Microsoft Defender | |
| Connection Type | Custom VPN | com.microsoft.scmx |
| Server Address | 127.0.0.1 | Local loopback — traffic does not leave the device |
| Authentication | Username and Password | |
| SilentOnboard | True | No user interaction required |
| SingleSignOn | True | SSO for silent enrollment |
| On-Demand Rule | Connect if Needed | Always-on style connection |
| Split Tunneling | Disabled | All traffic through the local filter |
| Disable On-Demand User Override | True | Users cannot disable the VPN |

---

## Conditional Access Policies

These policies are in the `ConditionalAccess\` folder. Import method differs from device policies — use Microsoft Entra admin center (Identity > Protection > Conditional Access) or the Graph API. The folder GUIDs for exclusion groups are environment-specific and must be updated before enabling.

### CA Policy 1 — CA-User-Protection-AllUsers-AllApps-iOS-Req-ComplianceDevice

| Property | Value |
|---|---|
| File | CA-User-Protection-AllUsers-AllApps-iOS-Req-ComplianceDevice.json |
| State | Enabled |
| Users | All users (exclusion groups — update GUIDs for your tenant) |
| Platform | iOS only |
| Applications | All applications (excludes O365 app) |
| Client App Types | All |
| Device Filter | Exclude: mdmAppId equals 0000000a-0000-0000-c000-000000000000 [Intune] - (BYOD/unenrolled devices only) |
| Device Filter | Include: mdmAppId equals 0000000a-0000-0000-c000-000000000000 [Intune] - (enrolled devices only) |
| Grant Control | compliantDevice |
| Applies To | Web-based enrolled devices only — devices must pass all assigned compliance policies |

### CA Policy 2 — CA-User-Protection-AllUsers-O365-MobileApps-iOS-Req-AppProtectionPolicy-BYOD

| Property | Value |
|---|---|
| File | CA-User-Protection-AllUsers-O365-MobileApps-iOS-Req-AppProtectionPolicy-BYOD.json |
| State | Enabled |
| Users | All users (exclusion groups — update GUIDs for your tenant) |
| Platform | iOS only |
| Applications | Office 365 only |
| Client App Types | Mobile apps and desktop clients |
| Device Filter | Exclude: mdmAppId equals 0000000a-0000-0000-c000-000000000000 [Intune] (BYOD/unenrolled devices only) |
| Grant Control | compliantApplication (App Protection Policy required) |
| Applies To | BYOD and unenrolled devices using O365 mobile apps — covers both web-based and user-enrolled devices, as well as MAM-only (unenrolled) |

Note: These CA polices work together — enrolled devices go through (device compliance), unenrolled/BYOD O365 access (app protection).

---

## CIS Compliance Gaps — Apple Intelligence BYOD (DDM Incompatibility)

The following CIS iOS/iPadOS 26 Benchmark Level 1 controls apply to **End-User Owned (BYOD) devices** per the benchmark but **cannot be implemented** in this baseline. Microsoft migrated all Apple Intelligence settings in the Intune Settings Catalog from Device Restrictions Configuration to Declarative Device Management (DDM). The DDM-based settings apply correctly on supervised ADE/Corporate devices but produce errors in Intune when assigned to unsupervised BYOD devices.

| CIS Control | Section Reference | CIS Level | Reason Not Implemented | Compensating Control |
|---|---|---|---|---|
| External Intelligence Extensions Disabled | Section 2.9.1 | L1 BYOD | DDM incompatible with unsupervised BYOD — Intune error on assignment | MAM App Protection Policy prevents corporate data from entering external AI within managed Microsoft 365 apps |
| Notes Summarization Disabled | Section 2.9.2 | L1 BYOD | DDM incompatible with unsupervised BYOD | No device-level compensating control; personal Notes app is outside MDM scope on personal device |
| Mail Summarization Disabled | Section 2.9.3 | L1 BYOD | DDM incompatible with unsupervised BYOD | Outlook ACP and MAM policies control corporate mail on BYOD; native Mail app is outside managed scope |
| Writing Tools Disabled | Section 2.9.4 | L1 BYOD | DDM incompatible with unsupervised BYOD (also intentional deviation on Corporate — Writing Tools kept enabled) | MAM App Protection Policy controls data processed by Writing Tools within managed apps |

> **Recommended action:** Monitor Microsoft Intune release notes for support of DDM Apple Intelligence settings on unsupervised devices. If a supported path becomes available, add these controls to a BYOD Apple Intelligence policy. See also Section 5.7 of iOS_CIS-Microsoft_Recommended_Settings_Guide.md.

---

## Baseline Compliance Assessment

### CIS iOS/iPadOS 26 Benchmark v1.0.0

| Level | Estimated Coverage | Notes |
|---|---|---|
| L1 (Corporate) | ~96% | Notes/Mail Summarization (Section 3.10.2–3.10.3) pending JSON update (Track A); Writing Tools (Section 3.10.4) is intentional deviation; all other L1 controls implemented |
| L1 (BYOD) | ~88% | BYOD Apple Intelligence gap (Section 2.9.1–2.9.4) — DDM incompatible with unsupervised BYOD; all other applicable BYOD L1 controls implemented |
| L2 (Corporate) | ~93% | L2 additions fully implemented; gaps limited to tenant catalog constraints |
| L3 (Corporate) | ~89% | L3 controls implemented; gaps limited to tenant catalog constraints |

**Controls confirmed implemented in this session:**

| Setting | Policy | Change |
|---|---|---|
| allowpasswordautofill=false | Device Security Corporate | Added |
| allowpasswordproximityrequests=false | Device Security Corporate | Added (moved from Restrictions) |
| allowpasswordsharing=false | Device Security Corporate | Added (moved from Restrictions) |
| forceauthenticationbeforeautofill=true | Device Security Corporate | Added (moved from Restrictions) |
| allowappcellulardatamodification=false | Connectivity Controls Corporate | Added |
| allowesimmodification=false | Connectivity Controls Corporate | Added |
| allowesimoutgoingtransfers=false | Connectivity Controls Corporate | Added |
| allowmarketplaceappinstallation=false | Web-App-Store (EU) Corporate | Added |
| allowwebdistributionappinstallation=false | Web-App-Store (EU) Corporate | Added |
| idlerebootallowed=true | Device Restrictions Corporate | Consolidated from Software Update |

**Deduplication actions completed in this session:**

| Setting | Removed From | Reason |
|---|---|---|
| allowpasswordproximityrequests | Device Restrictions Corporate | Duplicate — authoritative home is Device Security Corporate |
| allowpasswordsharing | Device Restrictions Corporate | Duplicate — authoritative home is Device Security Corporate |
| forceauthenticationbeforeautofill | Device Restrictions Corporate | Duplicate — authoritative home is Device Security Corporate |
| allowaddinggamecenterfriends | Device Restrictions Corporate | Redundant — Game Center fully blocked in Apple Services Corporate |
| settings_item_mdmoptions (idlerebootallowed) | Software Update Corporate | Conflict — mdmoptions group already present in Device Restrictions Corporate; consolidated |

### Microsoft iOS/iPadOS Security Configuration Framework (April 2026)

| Level | Estimated Coverage | Notes |
|---|---|---|
| Level 1 — Basic (Corporate) | ~97% | All core passcode, data protection, and connectivity controls implemented |
| Level 2 — Enhanced (Corporate) | ~93% | Advanced supervised controls, SSO, MDE, and App Protection implemented; minor catalog gaps |
| Level 3 — High Security (Corporate) | ~88% | High-security supervised controls implemented; some settings not available in tenant catalog |
| BYOD (User Enrollment) | ~91% | Full BYOD track implemented; supervised-only controls excluded by design |

---

*This document is part of the UniFy Endpoint – Microsoft Intune iOS/iPadOS Baseline series. Document maintained by Yoennis Olmo - Sr. Modern Work Consultant.*
