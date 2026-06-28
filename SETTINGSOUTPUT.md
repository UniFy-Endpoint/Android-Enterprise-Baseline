# Android Enterprise Baseline — Settings Output

Baseline version: **1.5** | Last updated: **2026-06-28** | Author: **Yoennis Olmo (UniFy-Endpoint)**

This document lists every Intune policy file included in the baseline with its key configuration settings, target enrollment type, and group assignments.

> **After importing:** Group IDs do not transfer between tenants. Reassign all groups manually. Verify assignment filter names match your enrollment profile names exactly.

---

## Table of Contents

1. [Compliance Policies](#compliance-policies)
2. [Device Configuration — DeviceConfiguration Template](#device-configuration--deviceconfiguration-template)
3. [Device Configuration — Settings Catalog](#device-configuration--settings-catalog)
4. [App Configuration Policies](#app-configuration-policies)
5. [App Protection Policies (MAM)](#app-protection-policies-mam)
6. [Conditional Access Policies](#conditional-access-policies)
7. [Assignment Filters](#assignment-filters)
8. [Enrollment Restrictions](#enrollment-restrictions)

---

## Compliance Policies

---

### AND - CP - DEV - Corporate-Dedicated-Devices - Kiosk - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - CP - DEV - Corporate-Dedicated-Devices - Kiosk - v1.5 |
| Description | Compliance Policy for Android Enterprise Corporate-Owned Dedicated Devices in Kiosk Mode |
| Profile type | androidDeviceOwnerCompliancePolicy |
| Target enrollment | Dedicated / Kiosk (Single-App + Multi-App) |
| Noncompliance action | Block access after 24-hour grace period; push notification on detection |

**Settings**

| Setting | Value |
| :--- | :--- |
| Minimum OS version | 13.0 |
| Password required | Yes |
| Password type | Numeric complex |
| Minimum password length | 6 |
| Inactivity before screen lock | 5 minutes |
| Password expiration | 365 days |
| Previous passwords blocked | 5 |
| Block rooted / jailbroken devices | Yes |
| SafetyNet Basic Integrity | Required |
| SafetyNet evaluation type | Hardware Backed |
| Storage encryption | Required |
| Intune app integrity | Required |
| Minimum security patch level | Not configured |

---

### AND - CP - DEV - Corporate-Dedicated-Devices - Shared - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - CP - DEV - Corporate-Dedicated-Devices - Shared - v1.5 |
| Description | Compliance Policy for Android Enterprise Corporate-Owned Dedicated Devices in Shared Mode |
| Profile type | androidDeviceOwnerCompliancePolicy |
| Target enrollment | Dedicated / Shared (Multi-App Managed Home Screen) |
| Noncompliance action | Block access after 24-hour grace period; push notification on detection |

**Settings**

| Setting | Value |
| :--- | :--- |
| Minimum OS version | 13.0 |
| Password required | Yes |
| Password type | Numeric complex |
| Minimum password length | 6 |
| Inactivity before screen lock | 5 minutes |
| Password expiration | 365 days |
| Previous passwords blocked | 5 |
| Block rooted / jailbroken devices | Yes |
| SafetyNet Basic Integrity | Required |
| SafetyNet evaluation type | Hardware Backed |
| Storage encryption | Required |
| Intune app integrity | Required |
| Minimum security patch level | Not configured |

---

### AND - CP - DEV - Corporate-Fully-Managed-Devices - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - CP - DEV - Corporate-Fully-Managed-Devices - v1.5 |
| Description | Compliance Policy for Android Enterprise Corporate-Owned Fully Managed Devices |
| Profile type | androidDeviceOwnerCompliancePolicy |
| Target enrollment | Fully Managed (Default + Staging) |
| Noncompliance action | Block access after 24-hour grace period; push notification on detection |

**Settings**

| Setting | Value |
| :--- | :--- |
| Minimum OS version | 13.0 |
| Password required | Yes |
| Password type | Numeric complex |
| Minimum password length | 6 |
| Inactivity before screen lock | 5 minutes |
| Password expiration | 365 days |
| Previous passwords blocked | 5 |
| Block rooted / jailbroken devices | Yes |
| SafetyNet Basic Integrity | Required |
| SafetyNet evaluation type | Hardware Backed |
| Storage encryption | Required |
| Intune app integrity | Required |
| Minimum security patch level | Not configured |

---

### AND - CP - DEV - Corporate-Owned - Work-Profile - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - CP - DEV - Corporate-Owned - Work-Profile - v1.5 |
| Description | Compliance Policy for Android Enterprise Corporate-Owned Work-Profile Devices |
| Profile type | androidDeviceOwnerCompliancePolicy |
| Target enrollment | Corporate-Owned Work Profile (Default + Staging) |
| Noncompliance action | Block access after 24-hour grace period; push notification on detection |

**Settings**

| Setting | Value |
| :--- | :--- |
| Minimum OS version | 13.0 |
| Password required | Yes |
| Password type | Numeric complex |
| Minimum password length | 6 |
| Inactivity before screen lock | 5 minutes |
| Password expiration | 365 days |
| Previous passwords blocked | 5 |
| Block rooted / jailbroken devices | Yes |
| SafetyNet Basic Integrity | Required |
| SafetyNet evaluation type | Hardware Backed |
| Storage encryption | Required |
| Intune app integrity | Required |
| Minimum security patch level | Not configured |

---

### AND - CP - USR - Personally-Owned - Work-Profile - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - CP - USR - Personally-Owned - Work-Profile - v1.5 |
| Description | This compliance policy applies to Android Enterprise BYOD Personally-Owned Work-Profile users. |
| Profile type | androidWorkProfileCompliancePolicy |
| Target enrollment | Personally-Owned Work Profile (BYOD) |
| Noncompliance action | Block access after 24-hour grace period; push notification on detection |

**Settings**

| Setting | Value |
| :--- | :--- |
| Minimum OS version | 13.0 |
| Password required | Yes |
| Password type | Device default |
| Required password complexity | Medium |
| Minimum password length | Not configured |
| Inactivity before screen lock | 15 minutes |
| Password expiration | 365 days |
| Previous passwords blocked | 5 |
| Work profile password required | Yes |
| Work profile password type | Numeric complex |
| Work profile minimum password length | 6 |
| Work profile required password complexity | Medium |
| Work profile inactivity before screen lock | Not configured |
| Block rooted / jailbroken devices | Yes |
| SafetyNet Basic Integrity | Required |
| SafetyNet Certified Device (Play Protect) | Required |
| SafetyNet evaluation type | Hardware Backed |
| Storage encryption | Required |
| Require Google Play Services | Yes |
| Up-to-date security providers | Required |
| Company Portal app integrity | Required |
| Block unknown sources | No |
| Block USB debugging | Yes |
| Minimum security patch level | Not configured |

---

### AND - CP - DEV - Defender (MDE) - Corporate Devices - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - CP - DEV - Defender (MDE) - Corporate Devices - v1.5 |
| Description | MDE threat protection compliance requirement for Android Enterprise Corporate-Owned devices (Fully Managed, Work Profile, Kiosk, Shared). Assign to the same groups as the corporate device compliance policies. |
| Profile type | androidDeviceOwnerCompliancePolicy |
| Target enrollment | Fully Managed + Corporate-Owned Work Profile + Dedicated (Kiosk + Shared) |
| Noncompliance action | Block access after 24-hour grace period; push notification on detection |

**Settings**

| Setting | Value |
| :--- | :--- |
| Device threat protection enabled | Yes |
| MDE required security level | Low |

---

### AND - CP - DEV - Defender (MDE) - Personal Devices - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - CP - DEV - Defender (MDE) - Personal Devices - v1.5 |
| Description | MDE threat protection compliance requirement for Android Enterprise BYOD Personally-Owned Work-Profile devices. Assign to the same groups as the personal device compliance policy. |
| Profile type | androidWorkProfileCompliancePolicy |
| Target enrollment | Personally-Owned Work Profile (BYOD) |
| Noncompliance action | Block access after 24-hour grace period; push notification on detection |

**Settings**

| Setting | Value |
| :--- | :--- |
| Device threat protection enabled | Yes |
| MDE required security level | Low |

---

## Device Configuration — DeviceConfiguration Template

---

### AND - DC - Device-Restrictions - DEV - Corporate-Owned - Fully-Managed - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - DC - Device-Restrictions - DEV - Corporate-Owned - Fully-Managed - v1.5 |
| Description | Device Restrictions Configuration Policy for Android Enterprise Corporate-Owned Fully Managed Devices |
| Profile type | androidDeviceOwnerGeneralDeviceConfiguration |
| Target enrollment | Fully Managed (Default + Staging) |

**Settings**

| Setting | Value |
| :--- | :--- |
| Camera blocked | Not blocked (Allowed) |
| Bluetooth configuration by user | Not blocked — users need to pair headsets and car systems |
| Bluetooth contact sharing | Not configured |
| Certificate credential configuration | Disabled — users cannot manually install certificates |
| Google accounts | Blocked |
| Account modification | Blocked |
| Factory reset by user | Blocked |
| Wi-Fi configuration editing | Not blocked |
| Wi-Fi policy-defined config editing | Blocked |
| Play Store mode | AllowList — only MGP-approved apps available |
| Apps auto-update | Always |
| Apps default permission policy | Device Default |
| **PIN failures before factory reset** | **10 — see CIS Hardening Guide W-1** |
| Screen timeout (inactivity) | 1 minute |
| Require unlock | Daily (biometric handles subsequent unlocks) |
| Data roaming | Not blocked — users travel and take devices home |
| External storage (SD cards / USB) | Blocked |
| Screen capture | Blocked |
| NFC outgoing beam | Blocked |
| Security Common Criteria Mode | Enabled (Samsung Knox only — see CIS Hardening Guide W-25) |
| Locate device (Lost Mode) | Enabled |
| Device owner lock screen message | Configured |
| System update type | Windowed |
| Update window start | 00:00 (midnight) |
| Update window end | 06:00 (360 minutes after midnight) |
| Network escape hatch | Not configured |
| USB file transfer | Blocked |

> **Note:** `wifiblockeditpolicydefinedconfigurations` (Wi-Fi policy-defined config editing) is configured in both this DC policy and the SC FM policy. If both policies are assigned to the same device, Intune will report a conflict on this setting. Remove the setting from one policy to eliminate the conflict — either trim this DC policy or remove the setting from the SC FM policy.

---

### AND - DC - Device-Restrictions - DEV - Corporate-Owned - Work-Profile - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - DC - Device-Restrictions - DEV - Corporate-Owned - Work-Profile - v1.5 |
| Description | Device Restrictions Configuration Policy for Android Enterprise Corporate-Owned Work-Profile Devices |
| Profile type | androidDeviceOwnerGeneralDeviceConfiguration |
| Target enrollment | Corporate-Owned Work Profile (Default + Staging) |

**Settings**

| Setting | Value |
| :--- | :--- |
| Data roaming | Blocked |
| Cross-profile data sharing | Work → Personal blocked; Personal → Work allowed |
| Cross-profile copy/paste | Blocked |
| Cross-profile show work contacts in personal profile | Allowed — work contacts visible in personal dialler for better user experience |
| Certificate credential configuration | Disabled — users cannot manually install certificates |
| Bluetooth contact sharing | Blocked |
| Apps auto-update | Always |
| Play Store mode | AllowList — only MGP-approved apps available |
| Screen capture | Blocked |
| NFC outgoing beam | Blocked |
| Account modification | Blocked |
| Add new users | Blocked |
| Factory reset by user | Blocked |
| Google accounts | Not configured |
| Locate device (Lost Mode) | Enabled |

> **Note:** `wifiblockeditpolicydefinedconfigurations` (Wi-Fi policy-defined config editing) is configured in both this DC policy and the SC COWP policy. If both policies are assigned to the same device, Intune will report a conflict on this setting. Remove the setting from one policy to eliminate the conflict.

---

### AND - DC - Device-Restrictions - DEV - Corporate-Dedicated-Devices - Single-App-Kiosk - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - DC - Device-Restrictions - DEV - Corporate-Dedicated-Devices - Single-App-Kiosk - v1.5 |
| Description | Device Restrictions Configuration Policy for Android Enterprise Corporate-Owned Dedicated Devices Single-App Kiosk-Mode |
| Profile type | androidDeviceOwnerGeneralDeviceConfiguration |
| Target enrollment | Dedicated / Single-App Kiosk |

**Settings**

| Setting | Value |
| :--- | :--- |
| Enrollment profile mode | Dedicated device |
| Kiosk mode | Single-app |
| Account modification | Blocked |
| Apps default permission policy | Auto-grant all permissions |
| Camera | Not blocked |
| Bluetooth configuration | Not blocked |
| Screen capture | Blocked |
| External storage (SD cards / USB) | Blocked |
| USB file transfer | Blocked |
| Wi-Fi tethering | Blocked |
| Data roaming | Blocked |
| Date/time configuration | Blocked |
| Factory reset by user | Blocked |
| Google accounts | Blocked |
| Users — add / remove | Blocked |
| System update type | Windowed |
| Update window start | 00:00 (midnight) |
| Update window end | 06:00 (360 minutes after midnight) |
| Network escape hatch | Allowed (enrollment fallback) |
| Password type | Numeric complex |
| Minimum password length | 6 |
| Password expiration | 365 days |
| Previous passwords blocked | 5 |
| PIN failures before factory reset | 10 |
| Keyguard features blocked | Camera; notifications; remote input |

---

### AND - DC - Device-Restrictions - DEV - Corporate-Dedicated-Devices - Multi-App-Kiosk - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - DC - Device-Restrictions - DEV - Corporate-Dedicated-Devices - Multi-App-Kiosk - v1.5 |
| Description | Device Restrictions Configuration Policy for Android Enterprise Corporate-Owned Dedicated Devices Multi-App Kiosk-Mode |
| Profile type | androidDeviceOwnerGeneralDeviceConfiguration |
| Target enrollment | Dedicated / Multi-App Kiosk |

**Settings**

| Setting | Value |
| :--- | :--- |
| Enrollment profile mode | Dedicated device |
| Kiosk mode | Multi-app (Managed Home Screen) |
| Account modification | Blocked |
| Apps default permission policy | Auto-grant all permissions |
| Camera | Not blocked |
| Bluetooth configuration | Not blocked |
| Screen capture | Blocked |
| External storage (SD cards / USB) | Blocked |
| USB file transfer | Blocked |
| Wi-Fi tethering | Blocked |
| Data roaming | Blocked |
| Date/time configuration | Blocked |
| Factory reset by user | Blocked |
| Google accounts | Blocked |
| Users — add / remove | Blocked |
| System update type | Windowed |
| Update window start | 00:00 (midnight) |
| Update window end | 06:00 (360 minutes after midnight) |
| Network escape hatch | Allowed (enrollment fallback) |
| Password type | Numeric complex |
| Minimum password length | 6 |
| Password expiration | 365 days |
| Previous passwords blocked | 5 |
| PIN failures before factory reset | 10 |
| Keyguard features blocked | Camera; notifications; remote input |
| Kiosk exit code | Configured |
| Kiosk grid layout | 5 rows × 4 columns |
| Kiosk screen orientation | Auto-rotate |
| Kiosk status bar | System info only |
| Kiosk system error warnings | Enabled |
| Kiosk apps (8) | Outlook, Teams, OneDrive, SharePoint, Edge, Authenticator, Intune, Managed Home Screen |

---

### AND - DC - Device-Restrictions - DEV - Corporate-Dedicated-Devices - Shared - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - DC - Device-Restrictions - DEV - Corporate-Dedicated-Devices - Shared - v1.5 |
| Description | Device Restrictions Configuration Policy for Android Enterprise Corporate-Owned Dedicated Devices in Shared Mode (Managed Home Screen) |
| Profile type | androidDeviceOwnerGeneralDeviceConfiguration |
| Target enrollment | Dedicated / Shared (Managed Home Screen with auto sign-out) |

**Settings**

| Setting | Value |
| :--- | :--- |
| Enrollment profile mode | Dedicated device |
| Kiosk mode | Multi-app (Managed Home Screen) |
| Account modification | Blocked |
| Apps default permission policy | Auto-grant all permissions |
| Camera | Not blocked |
| Bluetooth configuration | Not blocked |
| Screen capture | Blocked |
| External storage (SD cards / USB) | Blocked |
| USB file transfer | Blocked |
| Wi-Fi tethering | Blocked |
| Data roaming | Blocked |
| Date/time configuration | Blocked |
| Factory reset by user | Blocked |
| Google accounts | Blocked |
| Users — add / remove | Blocked |
| System update type | Windowed |
| Update window start | 00:00 (midnight) |
| Update window end | 06:00 (360 minutes after midnight) |
| Network escape hatch | Allowed (enrollment fallback) |
| Password type | Numeric complex |
| Minimum password length | 6 |
| Password expiration | 365 days |
| Previous passwords blocked | 5 |
| PIN failures before factory reset | 10 |
| Keyguard features blocked | Camera; notifications; remote input |
| MHS sign-in enabled | Yes |
| MHS auto sign-out | Enabled — 300 seconds inactive (60-second warning) |
| Kiosk exit code | Configured |
| Kiosk grid layout | 5 rows × 4 columns |
| Kiosk icon size | Large |
| Kiosk screen orientation | Auto-rotate |
| Kiosk status bar | System info only |
| Kiosk system error warnings | Enabled |
| Kiosk apps (8) | Authenticator, Edge, OneDrive, Outlook, SharePoint, Teams, Intune, Managed Home Screen |

---

### AND - DC - Device-Restrictions - USR - Personally-Owned - Work-Profile - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - DC - Device-Restrictions - USR - Personally-Owned - Work-Profile - v1.5 |
| Description | Device Restrictions Configuration Policy for Android Enterprise Personally Owned Work Profile (BYOD). |
| Profile type | androidWorkProfileGeneralDeviceConfiguration |
| Target enrollment | Personally-Owned Work Profile (BYOD) |

**Settings**

| Setting | Value |
| :--- | :--- |
| Work profile default app permission policy | Prompt |
| Work profile data sharing | Personal → Work allowed |
| Block unified device/work profile password | Not blocked — see CIS Hardening Guide W-11 |
| Work profile password type | Numeric complex |
| Work profile minimum password length | 6 |
| Work profile inactivity before screen timeout | 5 minutes |
| Work profile previous passwords blocked | 5 |
| Work profile password expiration | 365 days |
| PIN failures before factory reset (device) | Not configured |
| PIN failures before factory reset (work profile) | 10 |
| Work profile camera | Not blocked |
| Work profile screen capture | Blocked |
| Work profile cross-profile copy/paste | Blocked |
| Work profile notifications while locked | Not blocked |
| Work profile account modification (add accounts) | Blocked |
| Work profile account use | Block all |
| Unknown sources (work profile) | Blocked |

---

### AND - DC - Defender-For-Endpoint - DEV - Corporate-Owned - Always-On-VPN - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - DC - Defender-For-Endpoint - DEV - Corporate-Owned - Always-On-VPN - v1.5 |
| Description | Configuration profile to deliver Defender for Endpoint web protection tunnel to Corporate-Owned enrolled devices |
| Profile type | androidDeviceOwnerGeneralDeviceConfiguration |
| Target enrollment | Fully Managed + Corporate-Owned Work Profile |

**Settings**

| Setting | Value |
| :--- | :--- |
| Always-On VPN package | com.microsoft.scmx (Microsoft Defender for Endpoint) |
| Always-On VPN lockdown mode | Not configured |
| All other settings | Not configured (VPN-only policy) |

---

### AND - DC - Defender-For-Endpoint - DEV - Personally-Owned - Always-On-VPN - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - DC - Defender-For-Endpoint - DEV - Personally-Owned - Always-On-VPN - v1.5 |
| Description | Configuration profile to deliver Defender for Endpoint web protection tunnel to Personally-Owned enrolled devices |
| Profile type | androidWorkProfileGeneralDeviceConfiguration |
| Target enrollment | Personally-Owned Work Profile (BYOD) |

**Settings**

| Setting | Value |
| :--- | :--- |
| Always-On VPN package | com.microsoft.scmx (Microsoft Defender for Endpoint) |
| Always-On VPN lockdown mode | Disabled |
| Work profile data sharing | Device default |
| Work profile account use | Allow all except Google accounts |
| Block unified password | Not blocked |

---

## Device Configuration — Settings Catalog

> **DC vs SC deployment note:** The Settings Catalog (SC) is the modern Intune policy engine and is the recommended approach going forward. However, SC does not expose all settings available in the legacy DeviceConfiguration (DC) template. If both a DC policy and an SC policy are assigned to the same device with overlapping settings, Intune will report a **conflict** for those settings — neither value is reliably applied.
>
> **Options:**
> - **SC only** — No conflicts. Loses the DC-only settings listed in each section below. Recommended if Managed Google Play enrollment already restricts app installs.
> - **DC only** — Full setting coverage. No conflicts. Uses the legacy engine.
> - **Trim DC to complement SC** — Remove all overlapping settings from the DC policy, leaving only the DC-only settings. No conflicts and full coverage. Recommended long term but requires DC policy rework.
>
> The DC-only settings for each SC policy are listed at the end of each section below. These were verified against the SC settings picker in Intune Admin Center on 2026-05-28.

---

### AND - SC - Device-Restrictions - DEV - Corporate-Owned - Fully-Managed - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - SC - Device-Restrictions - DEV - Corporate-Owned - Fully-Managed - v1.5 |
| Description | Device Restrictions Settings for Android Enterprise Corporate-Owned Fully Managed Devices |
| Profile type | Settings Catalog (deviceManagementConfigurationPolicy) |
| Platform | androidEnterprise |
| Target enrollment | Fully Managed (Default + Staging) |
| Total settings configured | 31 |

**Key Settings**

| Setting | Value |
| :--- | :--- |
| Install from unknown sources | Blocked |
| App auto-updates | Always |
| Disable lock screen | Enabled — blocks all keyguard features (camera, trust agents, fingerprint, notifications) |
| Password expiration | 365 days |
| Previous passwords blocked | 5 |
| PIN failures before factory reset | 10 |
| Password required type | Numeric complex — minimum 6 characters |
| Require unlock | Daily — strong authentication required once per day; biometrics handle subsequent unlocks |
| Developer options | Disabled |
| Network escape hatch | Disabled — see CIS Hardening Guide W-4 |
| USB storage | Blocked |
| Camera | Not blocked — intentional, consistent with DC FM |
| Block Assist Content (Circle to Search) | Blocked — prevents AI assistant from capturing work screen content |
| NFC outgoing beam | Blocked |
| Bluetooth configuration by user | Not blocked — users need to pair headsets and car systems |
| Date/time configuration | Blocked |
| Factory reset by user | Blocked |
| External storage (SD cards) | Blocked |
| Data roaming | Not blocked — users travel and take devices home |
| Screen capture | Blocked |
| Wi-Fi tethering | Blocked |
| Wi-Fi Direct | Disallowed — blocks P2P Wi-Fi connections |
| Wi-Fi policy-defined config editing | Not blocked — see DC/SC conflict note below |
| Default app permission policy | Device Default |
| Location mode | Enforced — forces location services ON; prevents users from disabling; required for Locate Device remote action |
| Skip first-use hints | Enabled |
| USB data access | Disallowed — blocks USB data transfer (files/media) via cable |
| Security Common Criteria Mode | Enabled (Samsung Knox only — see CIS Hardening Guide W-25) |
| Verify apps (Play Protect) | Required |
| Certificate credential configuration | Disabled — users cannot manually install certificates |
| Block Bluetooth sharing | Blocked — prevents content/file sharing over Bluetooth; device pairing is unaffected |

> **Note:** `wifiblockeditpolicydefinedconfigurations` (Wi-Fi policy-defined config editing) is also configured in the DC FM policy. If both SC and DC policies are assigned to the same device, Intune will report a conflict on this setting. Remove the setting from one policy to eliminate the conflict — either trim the DC FM policy or remove this setting from the SC FM policy.

**SC Limitation — Settings only available in DC template (not configurable via Settings Catalog)**

The following settings from `AND - DC - Device-Restrictions - DEV - Corporate-Owned - Fully-Managed - v1.5` have no equivalent in the Settings Catalog for Android Enterprise Fully Managed. Verified against the SC settings picker (Intune Admin Center, 2026-05-28).

| DC UI Name | DC JSON key | SC status |
| :--- | :--- | :--- |
| Personal Google Accounts (Block) | `googleAccountsBlocked` | Not in SC — Users and Accounts category only has "Block users from configuring credentials" for FM/COWP ("Block account changes" is Dedicated/kiosk only in SC) |
| Allow access to all apps in Google Play store (Block) | `playStoreMode` | Not in SC — Applications only has "Allow installation from unknown sources" and "App auto-updates" |
| Add new users (Block) | `usersBlockAdd` | Not in SC — no add/remove user settings exist in SC |
| User removal (Block) | `usersBlockRemove` | Not in SC — same as above |
| Locate device (Allow) | `locateDeviceLostModeEnabled` | Not in SC — no locate device or lost mode setting in any SC category |
| System update — maintenance window | system update settings | Not in SC — no system update category exists in SC |

**Keyguard note:** The DC template provides granular keyguard control (block individual features: camera, trust agents, remote input, unredacted notifications). The SC provides **"Disable lock screen"** (Device password category) which blocks all keyguard features at once. This SC setting is enabled in this policy and covers the keyguard security requirement without needing the DC granular setting.

---

### AND - SC - Device-Restrictions - DEV - Corporate-Owned - Work-Profile - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - SC - Device-Restrictions - DEV - Corporate-Owned - Work-Profile - v1.5 |
| Description | Device Restrictions Settings for Android Enterprise Corporate-Owned Work-Profile Devices |
| Profile type | Settings Catalog (deviceManagementConfigurationPolicy) |
| Platform | androidEnterprise |
| Target enrollment | Corporate-Owned Work Profile (Default + Staging) |
| Total settings configured | 35 |

**Key Settings**

| Setting | Value |
| :--- | :--- |
| Install from unknown sources | Blocked |
| App auto-updates | Always |
| Password expiration | 365 days |
| Previous passwords blocked | 5 |
| PIN failures before factory reset | 10 |
| Password required type | Numeric complex — minimum 6 characters |
| Require unlock | Device Default |
| Developer options | Disabled |
| Cross-profile copy/paste | Blocked |
| Network escape hatch | Disabled |
| USB storage | Blocked |
| Camera | Not blocked |
| Block Assist Content (Circle to Search) | Blocked — prevents AI assistant from capturing work screen content |
| NFC outgoing beam | Blocked |
| Bluetooth configuration by user | Not blocked |
| Block Bluetooth contact sharing | Blocked |
| Date/time configuration | Blocked |
| Factory reset by user | Blocked |
| External storage (SD cards) | Blocked |
| Private Space | Disallowed — blocks creation of a private work-profile bypass space (Android 15+) |
| Data roaming | Blocked |
| Screen capture | Blocked |
| Show work contacts in personal profile | Allowed — work contacts visible in personal dialler |
| Wi-Fi tethering | Blocked |
| Wi-Fi Direct | Disallowed — blocks P2P Wi-Fi connections |
| Wi-Fi policy-defined config editing | Not blocked — see DC/SC conflict note below |
| Cross-profile data sharing | Work → Personal blocked |
| Default app permission policy | Device Default |
| Location mode | Enforced — forces location services ON; prevents users from disabling |
| Skip first-use hints | Enabled |
| USB data access | Disallowed — blocks USB data transfer via cable |
| Security Common Criteria Mode | Enabled (Samsung Knox only — elevated security standards for sensitive organisations) |
| Verify apps (Play Protect) | Required |
| Certificate credential configuration | Disabled — users cannot manually install certificates |
| Block Bluetooth sharing | Blocked — prevents content/file sharing over Bluetooth within the work profile |

> **Note:** `wifiblockeditpolicydefinedconfigurations` (Wi-Fi policy-defined config editing) is also configured in the DC COWP policy. If both SC and DC policies are assigned to the same device, Intune will report a conflict on this setting. Remove the setting from one policy to eliminate the conflict.

**SC Limitation — Settings only available in DC template (not configurable via Settings Catalog)**

The following settings from `AND - DC - Device-Restrictions - DEV - Corporate-Owned - Work-Profile - v1.5` have no equivalent in the Settings Catalog for Android Enterprise Corporate-Owned Work Profile. Verified against the SC settings picker (Intune Admin Center, 2026-05-28).

| DC UI Name | DC JSON key | SC status |
| :--- | :--- | :--- |
| Allow access to all apps in Google Play store (Block) | `playStoreMode` | Not in SC — Applications only has "Allow installation from unknown sources" and "App auto-updates (work profile-level)" |
| Add new users (Block) | `usersBlockAdd` | Not in SC — no user management settings exist in SC for COPE |
| Disabled lock screen features — trust agents (work profile level) | `passwordBlockKeyguardFeatures` (trustAgents) | Not in SC — "Disable lock screen" is not supported on COWP (COPE not listed as a supported enrollment type in MS docs). Work profile keyguard trust agents must be configured via Device Configuration (DC). |
| Locate device (Allow) | `locateDeviceLostModeEnabled` | Not in SC — no locate device or lost mode setting in any SC category |
| System update — maintenance window | system update settings | Not in SC — no system update category exists in SC |

---

## App Configuration Policies

---

### AND - App-Configuration - DEV - Corporate-Owned - Outlook Settings - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - App-Configuration - DEV - Corporate-Owned - Outlook Settings - v1.5 |
| Description | Recommended Microsoft Outlook settings for Android Enterprise Corporate-Owned devices |
| Target app | Microsoft Outlook — com.microsoft.office.outlook |
| Profile applicability | androidDeviceOwner (corporate-enrolled devices) |
| Target enrollment | Fully Managed, Corporate-Owned Work Profile, Kiosk, Shared |

**Settings**

| Setting | Key | Value |
| :--- | :--- | :--- |
| Account type | com.microsoft.outlook.EmailProfile.AccountType | ModernAuth |
| Email UPN | com.microsoft.outlook.EmailProfile.EmailUPN | `{{userprincipalname}}` |
| Email address | com.microsoft.outlook.EmailProfile.EmailAddress | `{{mail}}` |
| Allowed accounts only | IntuneMAMAllowedAccountsOnly | Enabled |
| Allowed account UPNs | com.microsoft.intune.mam.AllowedAccountUPNs | `{{userprincipalname}}` |
| Focused Inbox | com.microsoft.outlook.Mail.FocusedInbox | Disabled |
| Default signature | com.microsoft.outlook.Mail.DefaultSignatureEnabled | Disabled |
| Organize by thread | com.microsoft.outlook.Mail.OrganizeByThreadEnabled | Disabled |
| Suggested replies | com.microsoft.outlook.Mail.SuggestedRepliesEnabled | Disabled |
| External recipients tooltip | com.microsoft.outlook.Mail.ExternalRecipientsTooTipEnabled | Enabled |

---

### AND - App-Configuration - DEV - Personally-Owned - Outlook Settings - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - App-Configuration - DEV - Personally-Owned - Outlook Settings - v1.5 |
| Description | Recommended Microsoft Outlook settings for Android Enterprise Personally-Owned (BYOD) devices |
| Target app | Microsoft Outlook — com.microsoft.office.outlook |
| Profile applicability | androidWorkProfile (BYOD work profile) |
| Target enrollment | Personally-Owned Work Profile (BYOD) |

**Settings**

| Setting | Key | Value |
| :--- | :--- | :--- |
| Account type | com.microsoft.outlook.EmailProfile.AccountType | ModernAuth |
| Email UPN | com.microsoft.outlook.EmailProfile.EmailUPN | `{{userprincipalname}}` |
| Email address | com.microsoft.outlook.EmailProfile.EmailAddress | `{{mail}}` |
| Focused Inbox | com.microsoft.outlook.Mail.FocusedInbox | Disabled |
| Default signature | com.microsoft.outlook.Mail.DefaultSignatureEnabled | Disabled |
| Organize by thread | com.microsoft.outlook.Mail.OrganizeByThreadEnabled | Disabled |
| Suggested replies | com.microsoft.outlook.Mail.SuggestedRepliesEnabled | Disabled |
| External recipients tooltip | com.microsoft.outlook.Mail.ExternalRecipientsTooTipEnabled | Enabled |

---

### AND - App-Configuration - DEV - All-Profile-Types - Defender-For-Endpoint - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - App-Configuration - DEV - All-Profile-Types - Defender-For-Endpoint - v1.5 |
| Description | Required configuration keys for low-touch onboarding to Microsoft Defender for Endpoint on all Android Enterprise enrolled devices |
| Target app | Microsoft Defender for Endpoint — com.microsoft.scmx |
| Profile applicability | default (all enrollment types) |
| Target enrollment | All profile types (Fully Managed, Corp WP, BYOD WP) |

**Settings**

| Setting | Key | Value |
| :--- | :--- | :--- |
| Enable low-touch onboarding | EnableLowTouchOnboarding | 1 (Enabled) |
| Disable sign-out | DisableSignOut | 1 (Enabled) |
| Network protection | DefenderNetworkProtectionEnable | 1 (Enabled) |
| Network protection auto-remediation | DefenderNetworkProtectionAutoRemediation | 1 (Enabled) |
| Anti-phishing | antiphishing | 1 (Enabled) |
| VPN (web protection) | vpn | 1 (Enabled) |
| End-user trust flow | DefenderEndUserTrustFlowEnable | 0 (Disabled — user cannot override decisions) |
| Network protection privacy | DefenderNetworkProtectionPrivacy | 1 (Privacy-preserving mode) |
| Open network detection | DefenderOpenNetworkDetection | 0 (Disabled) |
| Certificate detection | DefenderCertificateDetection | 0 (Disabled) |
| Vulnerability management privacy (MDM) | DefenderTVMPrivacyMode | 0 (Signals shared) |
| Vulnerability management privacy (MAM) | DefenderTVMPrivacyMode-PP | 1 (Privacy-preserving) |
| Exclude app from report (MDM) | DefenderExcludeAppInReport | 0 (Apps reported) |
| Exclude app from report (MAM) | DefenderExcludeAppInReport-PP | 1 (Apps excluded) |
| Exclude URL from report (MDM) | DefenderExcludeURLInReport | 0 (URLs reported) |
| Exclude URL from report (MAM) | DefenderExcludeURLInReport-PP | 1 (URLs excluded) |
| Defender enabled (MDM) | defendertoggle | 1 (Enabled) |
| Defender enabled (MAM) | defendertoggle_PP | 1 (Enabled) |
| User UPN | UserUPN | `{{userprincipalname}}` |
| Non-APK file scan | EnableNonAPKFileScan | 1 (Enabled) |
| Storage permissions | WRITE/READ_EXTERNAL_STORAGE | Auto-granted |

---

### AND - App-Configuration - DEV - Shared-Devices - QR Code-Authentication - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - App-Configuration - DEV - Shared-Devices - QR Code-Authentication - v1.5 |
| Description | Configures Microsoft Authenticator to use QR code + PIN authentication on shared dedicated devices |
| Target app | Microsoft Authenticator — com.azure.authenticator |
| Profile applicability | androidDeviceOwner (corporate-enrolled devices) |
| Target enrollment | Dedicated / Shared (Managed Home Screen) |

**Settings**

| Setting | Key | Value |
| :--- | :--- | :--- |
| Preferred authentication method | preferred_auth_config | qrpin (QR code + PIN) |

---

## App Protection Policies (MAM)

---

### AND - APP - USR - App Protection - MAM - L2 - BYOD Devices - v1.5

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - APP - USR - App Protection - MAM - L2 - BYOD Devices - v1.5 |
| Description | App Protection Policy for unmanaged BYOD devices — Level 2 Enterprise Enhanced Data Protection |
| Platform | Android (androidManagedAppProtection) |
| Target | Unmanaged / BYOD devices (MAM without enrollment) |

**Data Protection Settings**

| Setting | Value |
| :--- | :--- |
| Allowed inbound data transfer sources | Policy-managed apps only |
| Allowed outbound data transfer destinations | Policy-managed apps only |
| Clipboard sharing | Policy-managed apps with paste-in allowed |
| Data backup | Blocked |
| Save copies of org data | Blocked |
| Allowed save locations | Local storage; OneDrive for Business; SharePoint |
| Block data ingestion from unmanaged documents | Yes |
| Contact sync | Blocked |
| Printing | Blocked |
| Open links in managed browser | Required (Microsoft Edge) |
| Managed browser | Microsoft Edge |

**Access Requirements**

| Setting | Value |
| :--- | :--- |
| PIN required | Yes |
| PIN type | Numeric |
| Minimum PIN length | 6 |
| Simple PIN | Blocked |
| Max PIN retries | 5 (then block) |
| Biometrics | Allowed |
| Require Class 3 biometrics (strong) | Yes |
| Require PIN after biometric change | Yes |
| Biometric PIN re-prompt timeout | 30 minutes |
| App PIN when device PIN set | Required (not waived) |
| Screen capture | Blocked |
| Device compliance required | Yes (action: block) |

**Conditional Launch**

| Setting | Value |
| :--- | :--- |
| Offline grace period (block access) | 12 hours |
| Offline grace period (wipe data) | 90 days |
| Minimum required OS version | 13.0 |
| SafetyNet device attestation | Basic Integrity and Device Certification (action: block) |
| SafetyNet evaluation type | Hardware Backed |
| Device passcode complexity | Low minimum (action: block) |
| Mobile threat defense level | Not configured |
| Action if device noncompliant | Block |

---

## Conditional Access Policies

---

### CA210-USR-AllApps-Android-AnyCli-AnyLoc-ReqCompliant

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | CA210-USR-AllApps-Android-AnyCli-AnyLoc-ReqCompliant |
| State | Disabled (enable after testing in your tenant) |
| Target enrollment | All Intune-managed (enrolled) Android devices |
| Purpose | Require compliant device for all users accessing any cloud app from an Intune-managed Android device |

**Conditions**

| Setting | Value |
| :--- | :--- |
| Users | All users |
| Excluded groups | Emergency access group; CA210 exclusion group |
| Excluded roles | 38 privileged admin roles (Global Admin, Intune Admin, Exchange Admin, and others) |
| Excluded external user types | All guest and external user types (internalGuest, b2bCollaboration, b2bDirectConnect, otherExternal, serviceProvider) |
| Target apps | All cloud apps |
| Excluded apps | Skype for Business (0000000a-...); Intune Enrollment (d4ebce55-...) |
| Client app types | All |
| Device platforms | Android |
| Device filter mode | Include |
| Device filter rule | `device.mdmAppId -eq "0000000a-0000-0000-c000-000000000000"` — Intune-managed devices only |

**Grant Controls**

| Setting | Value |
| :--- | :--- |
| Grant access | Require compliant device |

---

### CA213-USR-AllApps-Android-AnyCli-BYOD-ReqAppProtect-AppCtrl

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | CA213-USR-AllApps-Android-AnyCli-BYOD-ReqAppProtect-AppCtrl |
| State | Disabled (enable after testing in your tenant) |
| Target enrollment | Unmanaged Android devices (BYOD — no Intune enrollment) |
| Purpose | Require App Protection Policy and enforce Defender for Cloud Apps session control for unmanaged BYOD devices; Intune-managed devices are excluded (covered by CA210) |

**Conditions**

| Setting | Value |
| :--- | :--- |
| Users | All users |
| Excluded groups | Emergency access group; CA213 exclusion group |
| Excluded roles | 38 privileged admin roles (Global Admin, Intune Admin, Exchange Admin, and others) |
| Excluded external user types | All guest and external user types (internalGuest, b2bCollaboration, b2bDirectConnect, otherExternal, serviceProvider) |
| Target apps | All cloud apps |
| Excluded apps | Skype for Business (0000000a-...); Intune Enrollment (d4ebce55-...) |
| Client app types | Mobile apps and desktop clients; Browser |
| Device platforms | Android |
| Device filter mode | Exclude |
| Device filter rule | `device.mdmAppId -eq "0000000a-0000-0000-c000-000000000000"` — Intune-managed devices excluded; unmanaged BYOD devices targeted |

**Grant Controls**

| Setting | Value |
| :--- | :--- |
| Grant access | Require app protection policy |

**Session Controls**

| Setting | Value |
| :--- | :--- |
| Defender for Cloud Apps | Block downloads (cloudAppSecurity: blockDownloads) |

---

## Assignment Filters

| Filter Name | Platform | Type | Filter Rule |
| :--- | :--- | :--- | :--- |
| **AND-COFM-DEV** | androidForWork | Devices | `(device.deviceOwnership -eq "Corporate") and (device.enrollmentProfileName -eq "AND - Corporate-Fully-Managed-Devices-Enrollment") or (device.enrollmentProfileName -eq "AND - Corporate-Fully-Managed-Devices-Enrollment - Staging")` |
| **AND-COWP-DEV** | androidForWork | Devices | `(device.enrollmentProfileName -eq "AND - Corporate-Corporate-Owned-Work-Profile-Enrollment") or (device.enrollmentProfileName -eq "AND - Corporate-Corporate-Owned-Work-Profile-Enrollment - Staging")` |
| **AND-POWP-DEV** | androidForWork | Devices | `(device.deviceOwnership -eq "Personal")` |
| **AND-UMGD-USR** | androidMobileApplicationManagement | Apps | `(app.deviceManagementType -eq "Unmanaged")` |

See [8_Android_Assignment_Filters.md](Android_Enterprise_Deployment_Guides/8_Android_Assignment_Filters.md) for usage guidance.

---

## Enrollment Restrictions

### AND - Allow-Android-Enterprise (BYOD)

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - Allow-Android-Enterprise (BYOD) |
| Description | Enable Android Enterprise BYOD (Work Profile) Enrollment |
| Priority | 1 (highest priority) |
| Platform type | androidForWork (Android Enterprise) |

**Settings**

| Setting | Value |
| :--- | :--- |
| Platform blocked | No |
| Personal device enrollment blocked | No |
| Minimum OS version | Not configured |
| Maximum OS version | Not configured |
| Blocked manufacturers | None |
| Blocked SKUs | None |

See [9_Android_Enrollment_Restrictions.md](Android_Enterprise_Deployment_Guides/9_Android_Enrollment_Restrictions.md) for configuration guidance and hardening options.

---

## Summary

| Category | Policies |
| :--- | :--- |
| Compliance Policies | 7 (5 device + 2 MDE) |
| Device Config — DC Template | 7 (5 restriction + 2 MDE VPN) |
| Device Config — Settings Catalog | 2 |
| App Configuration Policies | 4 |
| App Protection Policies (MAM) | 1 |
| Conditional Access Policies | 2 |
| Assignment Filters | 4 |
| Enrollment Restrictions | 1 |
| **Total** | **28** |
