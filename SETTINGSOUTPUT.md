# Android Enterprise Baseline — Settings Output

Baseline version: **1.0** | Last updated: **2026-04-20** | Author: **Yoennis Olmo (UniFy-Endpoint)**

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

### AND - Compliance - DEV - Corporate-Dedicated-Devices - Kiosk

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - Compliance - DEV - Corporate-Dedicated-Devices - Kiosk |
| Description | Compliance Policy for Android Enterprise Corporate-Owned Dedicated Devices in Kiosk Mode |
| Profile type | androidDeviceOwnerCompliancePolicy |
| Target enrollment | Dedicated / Kiosk (Single-App + Multi-App) |
| Assigned groups | AND - DEV - Android-Corporate-Dedicated-Devices - Single-App - Kiosk; AND - DEV - Android-Corporate-Dedicated-Devices - Multi-App - Kiosk |
| Noncompliance action | Block access after 24-hour grace period |

**Settings**

| Setting | Value |
| :--- | :--- |
| Minimum OS version | 13.0 |
| Password required | Yes |
| Password type | Numeric complex |
| Minimum password length | 6 |
| Inactivity before screen lock | 15 minutes |
| Password expiration | 365 days |
| Previous passwords blocked | 5 |
| Block rooted / jailbroken devices | Yes |
| SafetyNet Basic Integrity | Required |
| SafetyNet evaluation type | Basic |
| Device threat protection enabled | No |
| Storage encryption | Required |
| Intune app integrity | Required |
| Minimum security patch level | Not configured |

---

### AND - Compliance - DEV - Corporate-Dedicated-Devices - Shared

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - Compliance - DEV - Corporate-Dedicated-Devices - Shared |
| Description | Compliance Policy for Android Enterprise Corporate-Owned Dedicated Devices in Shared Mode |
| Profile type | androidDeviceOwnerCompliancePolicy |
| Target enrollment | Dedicated / Shared (Multi-App Managed Home Screen) |
| Assigned groups | AND - DEV - Android-Corporate-Dedicated-Devices - Shared |
| Noncompliance action | Block access after 24-hour grace period |

**Settings**

| Setting | Value |
| :--- | :--- |
| Minimum OS version | 13.0 |
| Password required | Yes |
| Password type | Numeric complex |
| Minimum password length | 6 |
| Inactivity before screen lock | 15 minutes |
| Password expiration | 365 days |
| Previous passwords blocked | 5 |
| Block rooted / jailbroken devices | Yes |
| SafetyNet Basic Integrity | Required |
| SafetyNet evaluation type | Basic |
| Device threat protection enabled | No |
| Storage encryption | Required |
| Intune app integrity | Required |
| Minimum security patch level | Not configured |

---

### AND - Compliance - DEV - Corporate-Fully-Managed-Devices

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - Compliance - DEV - Corporate-Fully-Managed-Devices |
| Description | Compliance Policy for Android Enterprise Corporate-Owned Fully Managed Devices |
| Profile type | androidDeviceOwnerCompliancePolicy |
| Target enrollment | Fully Managed (Default + Staging) |
| Assigned groups | AND - DEV - Android-Corporate-Fully-Managed-Devices; AND - DEV - Android-Corporate-Fully-Managed-Devices - Staging |
| Noncompliance action | Block access after 24-hour grace period |

**Settings**

| Setting | Value |
| :--- | :--- |
| Minimum OS version | 13.0 |
| Password required | Yes |
| Password type | Numeric complex |
| Minimum password length | 6 |
| Inactivity before screen lock | 15 minutes |
| Password expiration | 365 days |
| Previous passwords blocked | 5 |
| Block rooted / jailbroken devices | Yes |
| SafetyNet Basic Integrity | Required |
| SafetyNet evaluation type | Basic |
| Device threat protection enabled | No |
| Storage encryption | Required |
| Intune app integrity | Required |
| Minimum security patch level | Not configured |

---

### AND - Compliance - DEV - Corporate-Owned - Work-Profile

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - Compliance - DEV - Corporate-Owned - Work-Profile |
| Description | Compliance Policy for Android Enterprise Corporate-Owned Work-Profile Devices |
| Profile type | androidDeviceOwnerCompliancePolicy |
| Target enrollment | Corporate-Owned Work Profile (Default + Staging) |
| Assigned groups | AND - DEV - Android-Corporate-Work-Profile-Devices; AND - DEV - Android-Corporate-Work-Profile-Devices - Staging |
| Noncompliance action | Block access after 24-hour grace period |

**Settings**

| Setting | Value |
| :--- | :--- |
| Minimum OS version | 13.0 |
| Password required | Yes |
| Password type | Numeric complex |
| Minimum password length | 6 |
| Inactivity before screen lock | 15 minutes |
| Password expiration | 365 days |
| Previous passwords blocked | 5 |
| Block rooted / jailbroken devices | Yes |
| SafetyNet Basic Integrity | Required |
| SafetyNet evaluation type | Basic |
| Device threat protection enabled | No |
| Storage encryption | Required |
| Intune app integrity | Required |
| Minimum security patch level | Not configured |

---

### AND - Compliance - DEV - Personally-Owned - Work-Profile

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - Compliance - DEV - Personally-Owned - Work-Profile |
| Description | Compliance Policy for Android Enterprise BYOD Personally-Owned Work-Profile Devices |
| Profile type | androidWorkProfileCompliancePolicy |
| Target enrollment | Personally-Owned Work Profile (BYOD) |
| Assigned groups | AND - DEV - Android-Personal-Work-Profile-Devices - BYOD |
| Noncompliance action | Block access after 24-hour grace period |

**Settings**

| Setting | Value |
| :--- | :--- |
| Minimum OS version | 13.0 |
| Password required | Yes |
| Password type | Numeric complex |
| Required password complexity | Medium |
| Minimum password length | 6 |
| Inactivity before screen lock | 15 minutes |
| Password expiration | 365 days |
| Previous passwords blocked | 5 |
| Work profile password required | Yes |
| Work profile inactivity before screen lock | 15 minutes |
| Block rooted / jailbroken devices | Yes |
| SafetyNet Basic Integrity | Required |
| SafetyNet Certified Device (Play Protect) | Not required |
| SafetyNet evaluation type | Basic |
| Device threat protection enabled | No |
| Storage encryption | Required |
| Require Google Play Services | Yes |
| Up-to-date security providers | Required |
| Company Portal app integrity | Required |
| Block unknown sources | Yes |
| Block USB debugging | Yes |
| Minimum security patch level | Not configured |

---

## Device Configuration — DeviceConfiguration Template

---

### AND - DC - Device-Restrictions - DEV - Corporate-Owned - Fully-Managed

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - DC - Device-Restrictions - DEV - Corporate-Owned - Fully-Managed |
| Description | Device Restrictions Configuration Policy for Android Enterprise Corporate-Owned Fully Managed Devices |
| Profile type | androidDeviceOwnerGeneralDeviceConfiguration |
| Target enrollment | Fully Managed (Default + Staging) |
| Assigned groups | AND - DEV - Android-Corporate-Fully-Managed-Devices; AND - DEV - Android-Corporate-Fully-Managed-Devices - Staging |

**Settings**

| Setting | Value |
| :--- | :--- |
| Camera blocked | Not blocked (Allowed) |
| Bluetooth configuration by user | Not configured |
| Bluetooth contact sharing | Not configured |
| Certificate credential configuration | Disabled — users cannot manually install certificates |
| Google accounts | Blocked |
| Account modification | Blocked |
| Factory reset by user | Blocked |
| Wi-Fi configuration editing | Blocked |
| Wi-Fi policy-defined config editing | Blocked |
| Play Store mode | AllowList — only MGP-approved apps available |
| Apps auto-update | Always |
| Apps default permission policy | Auto-grant all permissions |
| **PIN failures before factory reset** | **5 ⚠ — see CIS Hardening Guide Section 6, W-1** |
| Screen timeout (inactivity) | 1 minute |
| Require unlock | Daily (biometric handles subsequent unlocks) |
| Data roaming | Blocked |
| External storage (SD cards / USB) | Blocked |
| Screen capture | Not configured |
| System update type | Windowed |
| Update window start | 00:00 (midnight) |
| Update window end | 06:00 (360 minutes after midnight) |
| Network escape hatch | Not configured |
| USB file transfer | Not configured |

---

### AND - DC - Device-Restrictions - DEV - Corporate-Owned - Work-Profile

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - DC - Device-Restrictions - DEV - Corporate-Owned - Work-Profile |
| Description | Device Restrictions Configuration Policy for Android Enterprise Corporate-Owned Work-Profile Devices |
| Profile type | androidDeviceOwnerGeneralDeviceConfiguration |
| Target enrollment | Corporate-Owned Work Profile (Default + Staging) |
| Assigned groups | AND - DEV - Android-Corporate-Work-Profile-Devices; AND - DEV - Android-Corporate-Work-Profile-Devices - Staging |

**Settings**

| Setting | Value |
| :--- | :--- |
| Data roaming | Allowed |
| Cross-profile data sharing | Work → Personal blocked; Personal → Work allowed |
| Certificate credential configuration | Disabled — users cannot manually install certificates |
| Bluetooth contact sharing | Blocked |
| Apps auto-update | Always |
| Screen capture | Not configured |
| Account modification | Not configured |
| Factory reset by user | Not configured |
| Google accounts | Not configured |

---

### AND - DC - Device-Restrictions - DEV - Corporate-Dedicated-Devices - Single-App-Kiosk

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - DC - Device-Restrictions - DEV - Corporate-Dedicated-Devices - Single-App-Kiosk |
| Description | Device Restrictions Configuration Policy for Android Enterprise Corporate-Owned Dedicated Devices Single-App Kiosk-Mode |
| Profile type | androidDeviceOwnerGeneralDeviceConfiguration |
| Target enrollment | Dedicated / Single-App Kiosk |
| Assigned groups | AND - DEV - Android-Corporate-Dedicated-Devices - Single-App - Kiosk |

**Settings**

| Setting | Value |
| :--- | :--- |
| Enrollment profile mode | dedicatedDevice |
| Apps auto-update | Always |
| Apps default permission policy | Auto-grant all permissions |
| Account modification | Not configured |
| Camera | Not configured |
| Bluetooth configuration | Not configured |
| Certificate credential configuration | Not configured |

---

### AND - DC - Device-Restrictions - DEV - Corporate-Dedicated-Devices - Multi-App-Kiosk

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - DC - Device-Restrictions - DEV - Corporate-Dedicated-Devices - Multi-App-Kiosk |
| Description | Device Restrictions Configuration Policy for Android Enterprise Corporate-Owned Dedicated Devices Multi-App Kiosk-Mode |
| Profile type | androidDeviceOwnerGeneralDeviceConfiguration |
| Target enrollment | Dedicated / Multi-App Kiosk |
| Assigned groups | AND - DEV - Android-Corporate-Dedicated-Devices - Multi-App - Kiosk |

**Settings**

| Setting | Value |
| :--- | :--- |
| Enrollment profile mode | dedicatedDevice |
| Account modification | Blocked |
| Apps auto-update | Always |
| Apps default permission policy | Auto-grant all permissions |
| Camera | Not configured |
| Bluetooth configuration | Not configured |
| Certificate credential configuration | Not configured |

---

### AND - DC - Device-Restrictions - DEV - Corporate-Dedicated-Devices - Multi-App-Shared

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - DC - Device-Restrictions - DEV - Corporate-Dedicated-Devices - Multi-App-Shared |
| Description | Device Restrictions Configuration Policy for Android Enterprise Corporate-Owned Dedicated Managed Home Screen Shared Devices |
| Profile type | androidDeviceOwnerGeneralDeviceConfiguration |
| Target enrollment | Dedicated / Multi-App Shared (Managed Home Screen) |
| Assigned groups | AND - DEV - Android-Corporate-Dedicated-Devices - Shared |

**Settings**

| Setting | Value |
| :--- | :--- |
| Enrollment profile mode | dedicatedDevice |
| Account modification | Not configured |
| Apps auto-update | Always |
| Apps default permission policy | Auto-grant all permissions |
| Camera | Not configured |
| Bluetooth configuration | Not configured |
| Certificate credential configuration | Not configured |

---

### AND - DC - Device-Restrictions - DEV - Personally-Owned - Work-Profile

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - DC - Device-Restrictions - DEV - Personally-Owned - Work-Profile |
| Description | Device Restrictions Configuration Policy for Android Enterprise Personally-Owned Work-Profile (BYOD) Devices |
| Profile type | androidWorkProfileGeneralDeviceConfiguration |
| Target enrollment | Personally-Owned Work Profile (BYOD) |
| Assigned groups | AND - DEV - Android-Personal-Work-Profile-Devices - BYOD |

**Settings**

| Setting | Value |
| :--- | :--- |
| Work profile default app permission policy | Auto-grant all permissions ⚠ — see CIS Hardening Guide Section 6, W-7 |
| Work profile data sharing | Personal → Work allowed |
| Block unified device/work profile password | Not blocked ⚠ — see CIS Hardening Guide Section 6, W-11 |
| PIN failures before factory reset (device) | 11 |
| PIN failures before factory reset (work profile) | 11 |
| Work profile camera | Not blocked |
| Work profile screen capture | Not blocked |
| Work profile cross-profile copy/paste | Not blocked |
| Work profile notifications while locked | Not blocked |
| Work profile account modification | Not blocked |
| Unknown sources (work profile) | Blocked |

---

### AND - DC - Defender-For-Endpoint - DEV - Corporate-Owned - Always-On-VPN

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - DC - Defender-For-Endpoint - DEV - Corporate-Owned - Always-On-VPN |
| Description | Configuration profile to deliver Defender for Endpoint web protection tunnel to Corporate-Owned enrolled devices |
| Profile type | androidDeviceOwnerGeneralDeviceConfiguration |
| Target enrollment | Fully Managed + Corporate-Owned Work Profile |
| Assigned groups | AND - DEV - Android-Corporate-Fully-Managed-Devices; AND - DEV - Android-Corporate-Work-Profile-Devices (+ Staging groups) |

**Settings**

| Setting | Value |
| :--- | :--- |
| Always-On VPN package | com.microsoft.scmx (Microsoft Defender for Endpoint) |
| Always-On VPN lockdown mode | Not configured |
| All other settings | Not configured (VPN-only policy) |

---

### AND - DC - Defender-For-Endpoint - DEV - Personally-Owned - Always-On-VPN

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - DC - Defender-For-Endpoint - DEV - Personally-Owned - Always-On-VPN |
| Description | Configuration profile to deliver Defender for Endpoint web protection tunnel to Personally-Owned enrolled devices |
| Profile type | androidWorkProfileGeneralDeviceConfiguration |
| Target enrollment | Personally-Owned Work Profile (BYOD) |
| Assigned groups | AND - DEV - Android-Personal-Work-Profile-Devices - BYOD |

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

---

### AND - SC - Device-Restrictions - DEV - Corporate-Owned - Fully-Managed

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - SC - Device-Restrictions - DEV - Corporate-Owned - Fully-Managed |
| Description | Device Restrictions Settings for Android Enterprise Corporate-Owned Fully Managed Devices |
| Profile type | Settings Catalog (deviceManagementConfigurationPolicy) |
| Platform | androidEnterprise |
| Target enrollment | Fully Managed (Default + Staging) |
| Total settings configured | 31 |
| Assigned groups | AND - DEV - Android-Corporate-Fully-Managed-Devices; AND - DEV - Android-Corporate-Fully-Managed-Devices - Staging |
| Excluded groups | AND - DEV - Android-Personal-Work-Profile-Devices - BYOD |

**Key Settings**

| Setting | Value |
| :--- | :--- |
| Camera | Blocked ⚠ inconsistent with DC template (Allowed) — see CIS Hardening Guide Section 6, W-12 |
| Bluetooth configuration by user | Blocked ⚠ inconsistent with DC template (null) — see CIS Hardening Guide Section 6, W-3 |
| Network escape hatch | Disabled ⚠ — see CIS Hardening Guide Section 6, W-4 |
| System update type | Windowed |
| Apps default permission policy | Device default |

> ⚠ **This policy is assigned to the same Fully Managed device groups as the DC template. Camera and Bluetooth values conflict. See CIS Hardening Guide Section 6, W-12.**

---

### AND - SC - Device-Restrictions - DEV - Corporate-Owned - Work-Profile

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - SC - Device-Restrictions - DEV - Corporate-Owned - Work-Profile |
| Description | Device Restrictions Settings for Android Enterprise Corporate-Owned Work-Profile Devices |
| Profile type | Settings Catalog (deviceManagementConfigurationPolicy) |
| Platform | androidEnterprise |
| Target enrollment | Corporate-Owned Work Profile (Default + Staging) |
| Total settings configured | 32 |
| Assigned groups | AND - DEV - Android-Corporate-Work-Profile-Devices; AND - DEV - Android-Corporate-Work-Profile-Devices - Staging |
| Excluded groups | AND - DEV - Android-Personal-Work-Profile-Devices - BYOD |

---

### AND - SC - Device-Restrictions - DEV - Personally-Owned - Work-Profile

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - SC - Device-Restrictions - DEV - Personally-Owned - Work-Profile |
| Description | Device Restrictions Settings for Android Enterprise Personally-Owned Work-Profile Devices (BYOD) |
| Profile type | Settings Catalog (deviceManagementConfigurationPolicy) |
| Platform | androidEnterprise |
| Target enrollment | Personally-Owned Work Profile (BYOD) |
| Total settings configured | 19 |
| Assigned groups | AND - DEV - Android-Personal-Work-Profile-Devices - BYOD |

**Key Settings**

| Setting | Value |
| :--- | :--- |
| Unknown sources | Blocked |
| Apps auto-update | Configured |

---

## App Configuration Policies

---

### AND - App-Configuration - DEV - Corporate-Owned - Outlook Settings

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - App-Configuration - DEV - Corporate-Owned - Outlook Settings |
| Description | Recommended Microsoft Outlook settings for Android Enterprise Corporate-Owned devices |
| Target app | Microsoft Outlook — com.microsoft.office.outlook |
| Profile applicability | androidDeviceOwner (corporate-enrolled devices) |
| Target enrollment | Fully Managed, Corporate-Owned Work Profile, Kiosk, Shared |
| Assigned groups | AND - DEV - Android-Corporate-Fully-Managed-Devices; AND - DEV - Android-Corporate-Fully-Managed-Devices - Staging; AND - DEV - Android-Corporate-Work-Profile-Devices; AND - DEV - Android-Corporate-Work-Profile-Devices - Staging; AND - DEV - Android-Corporate-Dedicated-Devices - Shared; AND - DEV - Android-Corporate-Dedicated-Devices - Multi-App - Kiosk |

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

### AND - App-Configuration - DEV - Personally-Owned - Outlook Settings

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - App-Configuration - DEV - Personally-Owned - Outlook Settings |
| Description | Recommended Microsoft Outlook settings for Android Enterprise Personally-Owned (BYOD) devices |
| Target app | Microsoft Outlook — com.microsoft.office.outlook |
| Profile applicability | androidWorkProfile (BYOD work profile) |
| Target enrollment | Personally-Owned Work Profile (BYOD) |
| Assigned groups | AND - DEV - Android-Personal-Work-Profile-Devices - BYOD |

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

### AND - App-Configuration - DEV - Shared-Devices - QR Code-Authentication

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - App-Configuration - DEV - Shared-Devices - QR Code-Authentication |
| Description | Enables QR code authentication in Microsoft Authenticator for shared Android devices |
| Target app | Microsoft Authenticator — com.azure.authenticator |
| Profile applicability | androidDeviceOwner |
| Target enrollment | Dedicated / Shared + Kiosk |
| Assigned groups | AND - DEV - Android-Corporate-Dedicated-Devices - Shared; AND - DEV - Android-Corporate-Dedicated-Devices - Multi-App - Kiosk; AND - DEV - Android-Corporate-Dedicated-Devices - Single-App - Kiosk |
| Credential provider | Allowed |

**Settings**

| Setting | Key | Value |
| :--- | :--- | :--- |
| Preferred authentication config | preferred_auth_config | qrpin |

---

### AND - App-Configuration - DEV - All-Profile-Types - Defender-For-Endpoint

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - App-Configuration - DEV - All-Profile-Types - Defender-For-Endpoint |
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

## App Protection Policies (MAM)

---

### AND - App-Protection - USR - MAM - All Microsoft Apps - BYOD

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | AND - App-Protection - USR - MAM - All Microsoft Apps - BYOD |
| Description | App Protection Policy for unmanaged BYOD devices — Level 1 Enterprise Basic Data Protection |
| Platform | Android (androidManagedAppProtection) |
| Target | Unmanaged / BYOD devices (MAM without enrollment) |
| Assigned groups | AND - USR - Android-Personal-Work-Profile-Users - BYOD |

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
| Simple PIN | Allowed |
| Max PIN retries | 5 (then block) |
| Biometrics | Allowed |
| Biometric PIN re-prompt timeout | 30 minutes |
| App PIN when device PIN set | Required (not waived) |
| Device compliance required | Yes (action: block) |

**Conditional Launch**

| Setting | Value |
| :--- | :--- |
| Offline grace period (block access) | 12 hours |
| Offline grace period (wipe data) | 90 days |
| Minimum required OS version | 13.0 |
| Mobile threat defense level | Not configured |
| Action if device noncompliant | Block |

---

## Conditional Access Policies

---

### CA209-User-Protection-AllUsers-AllApps-Android-Req-ComplianceDevice

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | CA209-User-Protection-AllUsers-AllApps-Android-Req-ComplianceDevice |
| State | Enabled |
| Target enrollment | All corporate-enrolled Android devices |
| Purpose | Require compliant device for all users accessing any cloud app from Android |

**Conditions**

| Setting | Value |
| :--- | :--- |
| Users | All users |
| Excluded groups | Emergency access group; CA209 exclusion group |
| Target apps | All cloud apps |
| Excluded apps | Skype for Business (0000000a-...); Intune Enrollment (d4ebce55-...) |
| Client app types | All |
| Device platforms | Android |

**Grant Controls**

| Setting | Value |
| :--- | :--- |
| Grant access | Require compliant device |

---

### CA212-User-Protection-AllUsers-AllApps-Android-Req-AppProtectionPolicy-BYOD

**Basics**

| Property | Value |
| :--- | :--- |
| Display Name | CA212-User-Protection-AllUsers-AllApps-Android-Req-AppProtectionPolicy-BYOD |
| State | Enabled |
| Target enrollment | Personally-Owned / Unmanaged Android devices (BYOD) |
| Purpose | Require App Protection Policy for BYOD users; exclude company-owned devices (covered by CA209) |

**Conditions**

| Setting | Value |
| :--- | :--- |
| Users | All users |
| Excluded groups | CA212 exclusion group |
| Excluded roles | 16 privileged admin roles (Global Admin, Intune Admin, Exchange Admin, and others) |
| Target apps | All cloud apps |
| Client app types | Mobile apps and desktop clients only |
| Device platforms | Android |
| Device filter mode | Exclude |
| Device filter rule | `device.deviceOwnership -eq "Company"` — company-owned devices excluded |

**Grant Controls**

| Setting | Value |
| :--- | :--- |
| Grant access | Require app protection policy |

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
| Assigned groups | AND - USR - Android-Personal-Work-Profile-Users - BYOD |

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
| Compliance Policies | 5 |
| Device Config — DC Template | 7 (5 restriction + 2 MDE VPN) |
| Device Config — Settings Catalog | 3 |
| App Configuration Policies | 4 |
| App Protection Policies (MAM) | 1 |
| Conditional Access Policies | 2 |
| Assignment Filters | 4 |
| Enrollment Restrictions | 1 |
| **Total** | **27** |
