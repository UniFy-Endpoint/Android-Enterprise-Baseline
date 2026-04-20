# Managing Android Enterprise Devices with Microsoft Intune (Baseline)

There is plenty of content out there on how to implement Android Enterprise with Microsoft Intune - enrollment steps, policy walkthroughs, and platform-specific procedures. What has been missing is a ready-to-import baseline. For each deployment model, you would still need to build everything from scratch: find the right settings, create all the components, and figure out what the right configuration actually looks like.

This repository is intended to close that gap. It provides a practical, ready-to-import baseline for managing Android Enterprise devices with Microsoft Intune, covering all major deployment models. It is grounded in Microsoft's official deployment guidance, Android CIS Benchmark recommendations, and insights from real-world enterprise deployments.

- **Deployment guides** for each Android Enterprise enrollment / deployment method.
- A **baseline configuration** set (policies and building blocks) organized by Intune policy type.
- Optional **import/export automation** support via the *IntuneManagement* tool.

## Author

**Yoennis Olmo** - Modern Work Consultant.

## Sources and references

- Microsoft Deployment Guide (Android platform): [Manage Android devices in Microsoft Intune](https://learn.microsoft.com/en-us/intune/intune-service/fundamentals/deployment-guide-platform-android)
- Android CIS Benchmarks: [CIS Android Benchmarks](https://www.cisecurity.org/benchmark/google_android)
- Microsoft Android Enterprise security configuration framework: [Android Enterprise security configuration framework](https://learn.microsoft.com/en-us/mem/intune/enrollment/android-configuration-framework)

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

- [12_Android_CIS_Compliance_Hardening_Guide.md](Android_Enterprise_Deployment_Guides/12_Android_CIS_Compliance_Hardening_Guide.md)
  - Step-by-step guide to harden the baseline Intune policies toward CIS Benchmark and Microsoft Security Configuration Framework compliance.
  - Covers: Compliance policy upgrades (SafetyNet, patch level, threat protection, inactivity timeout), Device Restriction corrections (camera consistency, permission policy), App Protection Policy hardening (threat level, PIN policy, device lock, patch version).
  - Applies across all enrollment types. Each change includes the current baseline JSON value, recommended CIS-aligned value, and exact Intune admin center steps.

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

> **Note:** The `SettingsCatalog_Device_Restrictions` policies appear to work correctly, but not all settings found in the `DeviceConfiguration_Device_Restrictions` templates are present in the Settings Catalog equivalent. Review both sets and apply them according to your organization's and client's requirements.

## Importing the baseline into Intune

The policy configurations in this repository can be imported into Intune using the **IntuneManagement** tool by Mikael Karlsson (Micke-K).

- Tool repository (download + docs): [Micke-K/IntuneManagement](https://github.com/Micke-K/IntuneManagement)

### Before you import — add Managed Google Play Store apps first

Some App Configuration policies (e.g., Microsoft Defender for Endpoint low-touch onboarding) and Device Restriction configuration profiles that reference **Managed Home Screen** will fail to import or will not function correctly if the required Android apps have not yet been added to Intune as **Managed Google Play Store apps**.

It is strongly recommended to approve and sync the following apps from the Managed Google Play store into Intune **before** importing any policies from this baseline:

| App name | Platform | Type |
| :--- | :--- | :--- |
| Intune Company Portal | Android | Managed Google Play store app |
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

## How to use this repo

1. Read **Prerequisites + Managed Google Play** first.
2. Pick your **deployment method guide**.
3. Review the relevant baseline policies (restrictions, compliance, app config, app protection, CA, groups).
4. Import (optional) and then validate assignments + device experience end-to-end.

## Notes / disclaimers

- This is a baseline and should be adapted to your organization's security requirements, regulatory needs, and operational constraints.
- Always test changes with pilot groups and staged rollouts.
- The author is not responsible for any impact this baseline may have on your environment. Use it carefully and deliberately. After importing the policies, test thoroughly with a pilot group of users or devices before deploying to production.

## Naming convention

All policy names in this baseline follow a structured naming pattern:

```
[Platform] - [PolicyType] - [Category] - [Scope] - [Enrollment / Description]
```

| Segment | Values | Meaning |
| :--- | :--- | :--- |
| **Platform** | `AND` | Android. `ADN` = device-targeted policies (Compliance, DC, SC). `AND` = app-level policies (App Configuration, App Protection). |
| **Policy Type** | `DC`, `SC`, `Compliance`, `App-Configuration`, `App-Protection` | The Intune policy template type. |
| **Category** | `Device-Restrictions`, `Defender-For-Endpoint` | What the policy controls (omitted for Compliance policies). |
| **Scope** | `DEV` (Device), `USR` (User) | Assignment targeting scope. |
| **Enrollment / Description** | `Corporate-Owned - Fully-Managed`, `Personally-Owned - Work-Profile`, `MAM - All Microsoft Apps - BYOD`, etc. | The enrollment model or purpose. |

**Examples:**

| Policy Name | Type |
| :--- | :--- |
| `AND - Compliance - DEV - Corporate-Fully-Managed-Devices` | Compliance policy — Fully Managed |
| `AND - DC - Device-Restrictions - DEV - Corporate-Owned - Fully-Managed` | DeviceConfiguration Device Restrictions — Fully Managed |
| `AND - SC - Device-Restrictions - DEV - Personally-Owned - Work-Profile` | Settings Catalog Device Restrictions — BYOD |
| `AND - DC - Defender-For-Endpoint - DEV - Corporate-Owned - Always-On-VPN` | DeviceConfiguration — MDE Always-On VPN (Corporate) |
| `AND - App-Configuration - DEV - All-Profile-Types - Defender-For-Endpoint` | App Configuration — MDE low-touch onboarding |
| `AND - App-Protection - USR - MAM - All Microsoft Apps - BYOD` | App Protection Policy — MAM BYOD |

> **Assignment Filters** use an abbreviated hyphenated format: `AND-[MODEL]-[SCOPE]` (e.g., `AND-COFM-DEV` = Corporate-Owned Fully Managed Devices).

---

## Contributing

Issues and PRs are welcome (especially improvements based on field experience, OS/version caveats, or Intune behavior changes).
