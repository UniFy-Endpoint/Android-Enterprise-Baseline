# Managing Android Enterprise Devices with Microsoft Intune (Baseline)

| | |
|---|---|
| **Author** | Sr. Modern Work Consultant |
| **CIS Reference** | CIS Google Android Benchmark v1.6.0 |
| **Last Updated** | June 2026 (v1.5) |

---

A practical, ready-to-import baseline for managing Android Enterprise devices with Microsoft Intune, covering all major deployment models. Grounded in Microsoft's official deployment guidance, Android CIS Benchmark recommendations, and real-world enterprise experience.

- **Deployment guides** for each Android Enterprise enrollment / deployment method.
- A **baseline configuration** set (policies and building blocks) organized by Intune policy type.
- Optional **import/export automation** support via the *IntuneManagement* tool.

## Deployment models covered

This baseline covers all major Android Enterprise deployment models:

- **Personally-Owned Work Profile** (BYOD)
- **Dedicated Kiosk** (single-app and multi-app)
- **Dedicated Shared Mode** (shift workers)
- **Fully Managed** (Default/User-Driven and Staging)
- **Corporate-Owned Work Profile** (Default/User-Driven and Staging)
- **Mobile Application Management** (MAM, unmanaged devices)

## Not covered in this baseline

The following Android Enterprise scenarios are not included and have no guide or policy files:

- **AOSP (Android Open Source Project)** — For devices without Google services (e.g., Zebra, Honeywell frontline devices). Supported by Intune but not covered here. Increasingly common in manufacturing, logistics, and healthcare.
- **Zero-touch enrollment** — Google's pre-provisioning portal for large-scale deployments (devices pre-configured before shipping to users). Complements Staging enrollment but requires a separate Google Zero-touch portal setup not covered in this baseline.

## Guides (read in order)

Start with prerequisites and Managed Google Play, then follow the guide for your deployment method.

### 1) Prerequisites + Managed Google Play

- [Enrollment methods, requirements, and Managed Google Play](Android_Enterprise_Deployment_Guides/1_Android_Enrollment_and_Managed_Google_Play.md)

This guide covers:
- The **Android enrollment method landscape** (Personally-owned Work Profile, Dedicated/Kiosk, Fully Managed, Corporate-owned Work Profile, AOSP).
- Core requirements: licensing, Android Enterprise prerequisites, Managed Google Play binding.

### 2) Deployment method guides

A deployment guide exists for each Android deployment method:

- [2_Android_Personally_Owned_Work_Profile.md](Android_Enterprise_Deployment_Guides/2_Android_Personally_Owned_Work_Profile.md)
- [3_Android_Dedicated_Kiosk_Mode.md](Android_Enterprise_Deployment_Guides/3_Android_Dedicated_Kiosk_Mode.md)
- [4_Android_Dedicated_Shared_Mode.md](Android_Enterprise_Deployment_Guides/4_Android_Dedicated_Shared_Mode.md)
- [5_Android_Fully_Managed_Default_Or_Staging.md](Android_Enterprise_Deployment_Guides/5_Android_Fully_Managed_Default_Or_Staging.md)
- [6_Android_Corp_Work_Profile_Default_Or_Staging.md](Android_Enterprise_Deployment_Guides/6_Android_Corp_Work_Profile_Default_Or_Staging.md)
- [7_Android_Mobile Application Management_MAM_Unmanaged Devices.md](<Android_Enterprise_Deployment_Guides/7_Android_Mobile Application Management_MAM_Unmanaged Devices.md>)
  - Note: for this scenario, **Managed Google Play** and an **Enrollment Profile** are **not required**.

### 3) Security add-ons

Supplemental guides for security capabilities that apply across enrollment types:

- [11_Defender for Endpoint for Android Devices.md](<Android_Enterprise_Deployment_Guides/11_Defender for Endpoint for Android Devices.md>)
  - Covers: MDE–Intune connector setup, Managed Google Play deployment, App Configuration Policy (low-touch onboarding), Always-On VPN for Web Protection, optional App Protection Policy risk signals, and Conditional Access exclusions.
  - Supported enrollment types: **Personally-Owned Work Profile, Corporate-Owned Work Profile, Fully Managed**. Not supported on Dedicated/Kiosk or Shared-Device configurations.

- [12_Android_CIS-Microsoft_Compliance_Settings_Guide.md](Android_Enterprise_Deployment_Guides/12_Android_CIS-Microsoft_Compliance_Settings_Guide.md)
  - Complete CIS and Microsoft Security Configuration Framework settings reference for all Android Enterprise enrollment types.
  - Covers: Security level classification (CIS L1/L2, MS-L1/L2/L3, ADM-EXP), intentional deviations registry, per-policy settings with Level/Source per setting (FM, COPE, Dedicated, BYOD), MAM App Protection Policy, compliance policy settings, Conditional Access recommendations, operational risk warnings (W-1 through W-25), and compliance findings gap tracker (Parts A–E).
  - Applies across all enrollment types. Settings values verified against exported Intune policy JSON files.

### 4) Supporting configuration references

- [8_Android_Assignment_Filters.md](Android_Enterprise_Deployment_Guides/8_Android_Assignment_Filters.md)
  - Documents all 4 Assignment Filters included in the baseline (AND-COFM-DEV, AND-COWP-DEV, AND-POWP-DEV, AND-UMGD-USR).
  - Explains what each filter targets, the filter rule syntax, when to use Include vs. Exclude, and how to apply filters to policy assignments in the Intune admin center.

- [9_Android_Enrollment_Restrictions.md](Android_Enterprise_Deployment_Guides/9_Android_Enrollment_Restrictions.md)
  - Documents the `AND - Allow-Android-Enterprise (BYOD)` enrollment restriction included in the baseline.
  - Explains how enrollment restrictions work alongside enrollment profiles, the baseline configuration settings, and hardening options (minimum OS version, blocked manufacturers).

- [10_Android_Managed_Home_Screen.md](Android_Enterprise_Deployment_Guides/10_Android_Managed_Home_Screen.md)
  - Configuration guide for the Microsoft Managed Home Screen (MHS) launcher app used on Dedicated Kiosk, Shared, and Fully Managed devices.
  - Covers: key configuration settings (app list, session PIN, button visibility, session timeout, branding), scenario-specific minimal configurations for Multi-App Kiosk and Shared Device, and the relationship between MHS, Device Restrictions, and the QR Code App Configuration Policy.

## Baseline content (policy sets)

The following policy and configuration areas are included in the baseline (aligned to Microsoft best practices and common enterprise patterns):

| Folder | Contents |
| :--- | :--- |
| `Android_Enterprise_Deployment_Guides/` | Step-by-step guides for each enrollment type |
| `DeviceConfiguration_Device_Restrictions/` | Device Restrictions using the classic DeviceConfiguration template |
| `SettingsCatalog_Device_Restrictions/` | Device Restrictions using the Settings Catalog |
| `CompliancePolicies/` | Compliance policy definitions |
| `AppConfigurationPolicy/` | App Configuration policies (e.g., Outlook settings for managed devices, MDE low-touch onboarding) |
| `AppProtection_MAM/` | App Protection Policies for MAM (unmanaged devices) |
| `ConditionalAccess/` | Conditional Access policy definitions |
| `AssignmentFilters/` | Intune Assignment Filters |
| `*/Groups/` | Microsoft Entra ID group definitions — stored as `Groups/` subfolders within each policy folder (e.g., `CompliancePolicies/Groups/`, `DeviceConfiguration_Device_Restrictions/Groups/`) |

**Device Configuration (DC) vs Settings Catalog (SC) — conflict and coverage note**

The Settings Catalog (SC) is the modern Intune policy engine. However, the Settings Catalog (SC) does not expose all settings available in the legacy Device Configuration (DC) template. If both a Device Configuration (DC) and Settings Catalog (SC) policy are assigned to the same device with overlapping settings, Intune reports a **conflict** for those settings — neither value is reliably applied.

The following settings are confirmed Device Configuration (DC)-only. Verified against the Intune Settings Catalog (SC) settings picker and Microsoft documentation on 2026-05-28.

**Fully Managed (COBO)**

| Device Configuration (DC) Setting (UI name) | Value | Importance |
| :--- | :--- | :--- |
| Personal Google Accounts | Block | High |
| Allow access to all apps in Google Play store | Block (allow-list only) | High |
| Add new users | Block | Medium |
| User removal | Block | Medium |
| Locate device | Allow (enables remote locate action) | Medium |
| System update — maintenance window | Windowed 00:00–06:00 | Low |

> **Keyguard:** Granular lock screen feature blocking (camera, trust agents, remote input, unredacted notifications) is Device Configuration (DC)-only. The Settings Catalog (SC) provides **"Disable lock screen"** which blocks all keyguard features at once. This setting is enabled in the baseline Settings Catalog (SC) FM policy (`AND - SC - Device-Restrictions - DEV - Corporate-Owned - Fully-Managed - v1.5`) and covers the keyguard security requirement.

**Corporate-Owned Work Profile (COPE)**

| Device Configuration (DC) Setting (UI name) | Value | Importance |
| :--- | :--- | :--- |
| Allow access to all apps in Google Play store | Block (allow-list only) | High |
| Add new users | Block | Medium |
| Disabled lock screen features — trust agents (work profile level) | Block | Medium |
| Locate device | Allow (enables remote locate action) | Medium |
| System update — maintenance window | Windowed 00:00–06:00 | Low |

> **Keyguard note:** Settings Catalog (SC) "Disable lock screen" is not supported on COWP — COPE is not listed as a supported enrollment type in Microsoft documentation. The setting has been removed from the COWP Settings Catalog (SC) policy. Work profile trust agents are configured exclusively via the Device Configuration (DC) policy (`passwordBlockKeyguardFeatures: trustAgents`).

**Deployment options:** (1) Settings Catalog (SC) only — no conflicts, accepts the gaps above; (2) Device Configuration (DC) only — full coverage, legacy engine; (3) Trim Device Configuration (DC) to only the DC-only settings listed above and deploy both — no conflicts and full coverage. See [SETTINGSOUTPUT.md](SETTINGSOUTPUT.md) for the full per-policy breakdown.

## Repository Structure

```
Android-Enterprise-Baseline/
├── README.md                                                              ← This file
├── SETTINGSOUTPUT.md                                                      ← Complete per-policy settings reference for all policies
├── CIS_Google_Android_Benchmark_v1.6.0.pdf                               ← CIS source benchmark (reference only)
│
├── Android_Enterprise_Deployment_Guides/                                  ← Step-by-step configuration guides (12 guides)
│   ├── 1_Android_Enrollment_and_Managed_Google_Play.md
│   ├── 2_Android_Personally_Owned_Work_Profile.md
│   ├── 3_Android_Dedicated_Kiosk_Mode.md
│   ├── 4_Android_Dedicated_Shared_Mode.md
│   ├── 5_Android_Fully_Managed_Default_Or_Staging.md
│   ├── 6_Android_Corp_Work_Profile_Default_Or_Staging.md
│   ├── 7_Android_Mobile Application Management_MAM_Unmanaged Devices.md
│   ├── 8_Android_Assignment_Filters.md
│   ├── 9_Android_Enrollment_Restrictions.md
│   ├── 10_Android_Managed_Home_Screen.md
│   ├── 11_Defender for Endpoint for Android Devices.md
│   └── 12_Android_CIS-Microsoft_Compliance_Settings_Guide.md
│
├── DeviceConfiguration_Device_Restrictions/                               ← 8 Device Configuration (DC) policy JSON files
│   ├── AND - DC - Device-Restrictions - DEV - Corporate-Owned - Fully-Managed - v1.5.json
│   ├── AND - DC - Device-Restrictions - DEV - Corporate-Owned - Work-Profile - v1.5.json
│   ├── AND - DC - Device-Restrictions - DEV - Corporate-Dedicated-Devices - Multi-App-Kiosk - v1.5.json
│   ├── AND - DC - Device-Restrictions - DEV - Corporate-Dedicated-Devices - Single-App-Kiosk - v1.5.json
│   ├── AND - DC - Device-Restrictions - DEV - Corporate-Dedicated-Devices - Shared - v1.5.json
│   ├── AND - DC - Device-Restrictions - USR - Personally-Owned - Work-Profile - v1.5.json
│   ├── AND - DC - Defender-For-Endpoint - DEV - Corporate-Owned - Always-On-VPN - v1.5.json
│   └── AND - DC - Defender-For-Endpoint - DEV - Personally-Owned - Always-On-VPN - v1.5.json
│
├── SettingsCatalog_Device_Restrictions/                                   ← 2 Settings Catalog (SC) policy JSON files
│   ├── AND - SC - Device-Restrictions - DEV - Corporate-Owned - Fully-Managed - v1.5.json
│   └── AND - SC - Device-Restrictions - DEV - Corporate-Owned - Work-Profile - v1.5.json
│
├── CompliancePolicies/                                                    ← 7 Compliance Policy JSON files
│   ├── AND - CP - DEV - Corporate-Fully-Managed-Devices - v1.5.json
│   ├── AND - CP - DEV - Corporate-Owned - Work-Profile - v1.5.json
│   ├── AND - CP - DEV - Corporate-Dedicated-Devices - Kiosk - v1.5.json
│   ├── AND - CP - DEV - Corporate-Dedicated-Devices - Shared - v1.5.json
│   ├── AND - CP - DEV - Defender (MDE) - Corporate Devices - v1.5.json
│   ├── AND - CP - DEV - Defender (MDE) - Personal Devices - v1.5.json
│   └── AND - CP - USR - Personally-Owned - Work-Profile - v1.5.json
│
├── AppConfigurationPolicy/                                                ← 4 App Configuration Policy JSON files
│   ├── AND - App-Configuration - DEV - All-Profile-Types - Defender-For-Endpoint - v1.5.json
│   ├── AND - App-Configuration - DEV - Corporate-Owned - Outlook Settings - v1.5.json
│   ├── AND - App-Configuration - DEV - Personally-Owned - Outlook Settings - v1.5.json
│   └── AND - App-Configuration - DEV - Shared-Devices - QR Code-Authentication - v1.5.json
│
├── AppProtection_MAM/                                                     ← 1 App Protection Policy (MAM) JSON file
│   └── AND - APP - USR - App Protection - MAM - L2 - BYOD Devices - v1.5.json
│
├── ConditionalAccess/                                                     ← 2 Conditional Access policy JSON files
│   ├── CA210-USR-AllApps-Android-AnyCli-AnyLoc-ReqCompliant.json
│   └── CA213-USR-AllApps-Android-AnyCli-BYOD-ReqAppProtect-AppCtrl.json
│
├── AssignmentFilters/                                                     ← 4 Assignment Filter JSON files
│   ├── AND-COFM-DEV.json
│   ├── AND-COWP-DEV.json
│   ├── AND-POWP-DEV.json
│   └── AND-UMGD-USR.json
│
└── DevicePlatformEnrollmentRestrictions/                                  ← 1 Enrollment Restriction JSON file
    └── AND - Allow-Android-Enterprise (BYOD).json
```

---

## Importing the baseline into Intune

The policy configurations in this repository can be imported into Intune using the **IntuneManagement** tool by Mikael Karlsson (Micke-K).

- Tool repository (download + docs): [Micke-K/IntuneManagement](https://github.com/Micke-K/IntuneManagement)

### Before you import — add Managed Google Play Store apps first

Some App Configuration policies (e.g., Microsoft Defender for Endpoint low-touch onboarding) and Device Restriction configuration profiles that reference **Managed Home Screen** will fail to import or will not function correctly if the required Android apps have not yet been added to Intune as **Managed Google Play Store apps**.

It is strongly recommended to approve and sync the following apps from the Managed Google Play store into Intune **before** importing any policies from this baseline:

| App name | Platform | Type |
| :--- | :--- | :--- |
| Managed Home Screen | Android | Managed Google Play store app |
| Microsoft Authenticator | Android | Managed Google Play store app |
| Microsoft Defender: Antivirus | Android | Managed Google Play store app |
| Microsoft Edge: AI browser | Android | Managed Google Play store app |
| Microsoft Intune | Android | Managed Google Play store app |
| Microsoft Launcher | Android | Managed Google Play store app |
| Microsoft OneDrive | Android | Managed Google Play store app |
| Microsoft OneNote: Save Notes | Android | Managed Google Play store app |
| Microsoft Outlook | Android | Managed Google Play store app |
| Microsoft SharePoint | Android | Managed Google Play store app |
| Microsoft Teams | Android | Managed Google Play store app |
| Microsoft To Do: Lists & Tasks | Android | Managed Google Play store app |

> **Why this matters:** App Configuration policies and Device Restriction profiles that reference a specific app (such as Managed Home Screen for Kiosk/Shared Device configs, or Microsoft Defender for MDE onboarding) require that the app already exists in your Intune app inventory. If the app is not present, the policy import will either fail or the policy will be created in a broken/incomplete state.

To add these apps, go to **Intune admin center → Apps → Android** and use **Add → Managed Google Play app** to search for and approve each app above.

### Typical workflow (high level)

1. Download/clone the IntuneManagement tool from GitHub.
2. Ensure you have permissions to create/update Intune objects in your tenant.
3. Use the tool to **import** the exported configurations from this repo (policy JSON definitions / templates as provided).
4. Validate:
   - Assignments (groups),
   - App dependencies (Managed Google Play approvals),
   - Device Restrictions Configuration Profile Settings (For certain Android enrollment types, you can use either Device Restriction templates or Device Restrictions within the Settings Catalog.)
   - Compliance/CA interactions,
   - Device targeting (BYOD vs COPE vs Fully Managed vs Dedicated).

> **Tip:** Always import into a **test tenant** or a dedicated test scope first, then iterate before broad production rollout.

## Naming convention

All policy names in this baseline follow a structured naming pattern:

```
[Platform] - [PolicyType] - [Category] - [Scope] - [Enrollment / Description]
```

| Segment | Values | Meaning |
| :--- | :--- | :--- |
| **Platform** | `AND` | Android. `ADN` = device-targeted policies (Compliance, DC, SC). `AND` = app-level policies (App Configuration, App Protection). |
| **Policy Type** | `DC`, `SC`, `CP`, `App-Configuration`, `App-Protection` | The Intune policy template type. `CP` = Compliance Policy. |
| **Category** | `Device-Restrictions`, `Defender-For-Endpoint` | What the policy controls (omitted for Compliance policies). |
| **Scope** | `DEV` (Device), `USR` (User) | Assignment targeting scope. |
| **Enrollment / Description** | `Corporate-Owned - Fully-Managed`, `Personally-Owned - Work-Profile`, `MAM - All Microsoft Apps - BYOD`, etc. | The enrollment model or purpose. |

**Examples:**

| Policy Name | Type |
| :--- | :--- |
| `AND - CP - DEV - Corporate-Fully-Managed-Devices - v1.5` | Compliance policy — Fully Managed |
| `AND - DC - Device-Restrictions - DEV - Corporate-Owned - Fully-Managed - v1.5` | DeviceConfiguration Device Restrictions — Fully Managed |
| `AND - DC - Defender-For-Endpoint - DEV - Corporate-Owned - Always-On-VPN - v1.5` | DeviceConfiguration — MDE Always-On VPN (Corporate) |
| `AND - App-Configuration - DEV - All-Profile-Types - Defender-For-Endpoint - v1.5` | App Configuration — MDE low-touch onboarding |
| `AND - APP - USR - App Protection - MAM - L2 - BYOD Devices - v1.5` | App Protection Policy — MAM BYOD |

> **Assignment Filters** use an abbreviated hyphenated format: `AND-[MODEL]-[SCOPE]` (e.g., `AND-COFM-DEV` = Corporate-Owned Fully Managed Devices).

---

## Version History

| Version | Date | Summary |
|---|---|---|
| 1.5 | June 2026 | Intune June 2026 release updates: Guide 2 (BYOD) full rewrite — AMAPI web enrollment GA, migration policy, updated enrollment UX, apps-at-enrollment, Strict Tunnel Mode, Credential Manager, passkey limitation. Guide 1: removed Global Administrator from MGP requirements (Intune Administrator only); Android XR enrollment added; Direct LOB app deployment note; MGP Account Upgrade to Entra ID section added. Guide 10 (MHS): BREAKING CHANGE exit lock task mode password → Device Config Profile; Custom Top Bar; App Silencing; remote Suspend/Restore actions. Guide 11 (MDE): MTD Enhanced Security Permissions; Strict Tunnel Mode section. Guide 12 (CIS): Location setting rename documented; Block Bluetooth Sharing (new SC setting) added. Policy renames: all 22 policy files versioned with `- v1.5` suffix; Compliance policies simplified (removed `Compliance -` infix, `MDE` renamed to `Defender (MDE)`); App Protection renamed to `AND - APP - USR - App Protection - MAM - L2 - BYOD Devices - v1.5`. Conditional Access updated to match CA Baseline: CA210 (compliant device, 38 excluded roles, guest exclusions) and CA213 (BYOD App Protection, All apps, browser+mobile, Defender for Cloud Apps session control). SC FM fixed: removed irrelevant cross-profile setting, settingCount 32→31. SETTINGSOUTPUT.md: all 31 SC FM and 35 SC COWP settings fully documented; DC/SC Wi-Fi conflict notes added. CLAUDE.md added. |
| 1.4 | June 2026 | Documentation: `12_Android_CIS_Compliance_Hardening_Guide.md` replaced by `12_Android_CIS-Microsoft_Compliance_Settings_Guide.md` — full reformat to iOS-style publish-ready reference with security level classification (CIS L1/L2, MS-L1/L2/L3, ADM-EXP), per-setting Level/Source columns, deviations registry, per-enrollment policy tables verified against exported JSON, all W-xx and gap tracker entries preserved |
| 1.3 | May 2026 | Compliance: renamed all 5 policies to `AND - CP - ...` convention; extracted MDE threat protection into 2 dedicated MDE-only compliance policies (Corporate + Personal); SC FM/COWP DC-only setting limitations documented; CIS gap tracker and compliance findings merged into hardening guide |
| 1.2 | April 2026 | Conditional Access: CA-User-Protection-AllUsers-AllApps-Android-Req-ComplianceDevice scoped to Intune-managed devices; CA-User-Protection-AllUsers-O365-MobileApps-Android-Req-AppProtectionPolicy-BYOD refined for Microsoft 365 BYOD MAM only |
| 1.1 | April 2026 | Compliance: hardware-backed SafetyNet, 5-min inactivity, push notifications on all policies; Dedicated devices: new Shared Mode config with auto sign-out; QR code auth app config added; documentation sync and CIS hardening guide updates |
| 1.0 | Initial release | Initial baseline for all major Android Enterprise enrollment types (compliance, device restrictions, app config, MAM, conditional access) |

---

## CIS and Microsoft References

| Source | Reference |
|---|---|
| CIS Google Android Benchmark v1.6.0 | https://www.cisecurity.org/benchmark/google_android |
| Microsoft Android Enterprise Deployment Guide | https://learn.microsoft.com/en-us/intune/intune-service/fundamentals/deployment-guide-platform-android |
| Microsoft Android Enterprise Security Configuration Framework | https://learn.microsoft.com/en-us/mem/intune/enrollment/android-configuration-framework |

---

## Contributing

Issues and PRs are welcome (especially improvements based on field experience, OS/version caveats, or Intune behavior changes).
