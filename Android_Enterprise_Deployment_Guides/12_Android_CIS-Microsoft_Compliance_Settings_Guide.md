# Android Enterprise CIS & Microsoft Recommended Settings Guide – Microsoft Intune

| | |
|---|---|
| **Document Type** | Work Instruction – Configuration Reference |
| **Author** | Sr. Modern Work Consultant |
| **Last Updated** | June 2026 |
| **Version** | 1.4 |
| **Applies To** | Android 13+, Microsoft Intune, Android Enterprise |
| **CIS Reference** | CIS Google Android Benchmark v1.6.0 |
| **Microsoft Reference** | Android Enterprise Security Configuration Framework (updated April 2026) |

---

## Table of Contents

1. [Overview & Scope](#1-overview--scope)
2. [Security Levels & Source Classification](#2-security-levels--source-classification)
   - 2.1 [CIS Benchmark Levels](#21-cis-benchmark-levels)
   - 2.2 [Microsoft Security Framework Levels](#22-microsoft-security-framework-levels)
   - 2.3 [Admin Experience Level (ADM-EXP)](#23-admin-experience-level-adm-exp)
   - 2.4 [Setting Source Classification](#24-setting-source-classification)
   - 2.5 [Policy Architecture](#25-policy-architecture)
3. [Compliance Reference](#3-compliance-reference)
   - 3.1 [Intentional Deviations from CIS Benchmark](#31-intentional-deviations-from-cis-benchmark)
   - 3.2 [CIS L1 Settings](#32-cis-l1-settings)
   - 3.3 [CIS L2 Settings](#33-cis-l2-settings)
   - 3.4 [Microsoft Framework Settings (Non-CIS)](#34-microsoft-framework-settings-non-cis)
   - 3.5 [ADM-EXP Settings — Admin Experience](#35-adm-exp-settings--admin-experience)
4. [Fully Managed (COBO) — Policy Settings](#4-fully-managed-cobo--policy-settings)
   - 4.1 [Password & Screen Lock](#41-password--screen-lock)
   - 4.2 [Device Restrictions](#42-device-restrictions)
   - 4.3 [App Management](#43-app-management)
   - 4.4 [Connectivity & Network](#44-connectivity--network)
   - 4.5 [Data Protection & Storage](#45-data-protection--storage)
   - 4.6 [System & Security](#46-system--security)
5. [Corporate-Owned Work Profile (COPE) — Policy Settings](#5-corporate-owned-work-profile-cope--policy-settings)
   - 5.1 [Password & Screen Lock](#51-password--screen-lock)
   - 5.2 [Device Restrictions](#52-device-restrictions)
   - 5.3 [Work Profile Controls](#53-work-profile-controls)
   - 5.4 [App Management](#54-app-management)
   - 5.5 [Connectivity & Network](#55-connectivity--network)
6. [Dedicated Kiosk & Shared Device — Policy Settings](#6-dedicated-kiosk--shared-device--policy-settings)
   - 6.1 [Authentication & Lock](#61-authentication--lock)
   - 6.2 [Kiosk Restrictions](#62-kiosk-restrictions)
7. [Personally-Owned Work Profile (BYOD) — Policy Settings](#7-personally-owned-work-profile-byod--policy-settings)
   - 7.1 [Work Profile Password](#71-work-profile-password)
   - 7.2 [Work Profile Restrictions](#72-work-profile-restrictions)
   - 7.3 [Cross-Profile Data Controls](#73-cross-profile-data-controls)
8. [App Protection Policy (MAM) — BYOD](#8-app-protection-policy-mam--byod)
   - 8.1 [Data Transfer & Protection](#81-data-transfer--protection)
   - 8.2 [Access Requirements](#82-access-requirements)
   - 8.3 [Conditional Launch](#83-conditional-launch)
9. [Compliance Policy Settings](#9-compliance-policy-settings)
   - 9.1 [Fully Managed (COBO)](#91-fully-managed-cobo)
   - 9.2 [Corporate-Owned Work Profile (COPE)](#92-corporate-owned-work-profile-cope)
   - 9.3 [Dedicated Kiosk & Shared](#93-dedicated-kiosk--shared)
   - 9.4 [Personally-Owned Work Profile (BYOD)](#94-personally-owned-work-profile-byod)
   - 9.5 [Actions for Noncompliance](#95-actions-for-noncompliance)
10. [Conditional Access Policy Recommendations](#10-conditional-access-policy-recommendations)
11. [Operational Risk Warnings](#11-operational-risk-warnings)
12. [Compliance Findings & Gap Tracker](#12-compliance-findings--gap-tracker)

[References](#references)

---

## 1. Overview & Scope

This guide documents the recommended Device Configuration, Settings Catalog, Compliance Policy, App Protection Policy (MAM), and Conditional Access settings for Android Enterprise in Microsoft Intune, aligned to the **CIS Google Android Benchmark v1.6.0** and the **Microsoft Android Enterprise Security Configuration Framework**.

| Section | Contents |
|---|---|
| **Section 2** | Security level labels — how to read each setting's Level / Source value |
| **Section 3** | Compliance reference: Deviations Registry + CIS L1 / CIS L2 / MS-only / ADM-EXP source tables |
| **Sections 4–7** | Per-policy settings for each enrollment type (FM, COPE, Dedicated, BYOD) with Level / Source per setting |
| **Section 8** | App Protection Policy (MAM) settings for BYOD |
| **Section 9** | Compliance policy settings by enrollment type |
| **Section 10** | Conditional Access policy recommendations |
| **Section 11** | Operational Risk Warnings (W-1 through W-25) — settings that can break device functionality |
| **Section 12** | Compliance Findings & Gap Tracker (Parts A–E) |

Not covered: App Configuration Policies, Microsoft Defender for Endpoint deployment, VPN/Wi-Fi/Email/Certificate profile templates, Managed Home Screen configuration. See dedicated guides: [11_Defender for Endpoint for Android Devices.md](<11_Defender for Endpoint for Android Devices.md>), [10_Android_Managed_Home_Screen.md](10_Android_Managed_Home_Screen.md).

> **DC vs SC note:** The baseline deploys both Device Configuration (DC) legacy templates and Settings Catalog (SC) policies for FM and COPE. The SC is the modern policy engine but does not expose all settings available in the DC template. See README.md for the full DC-only settings list and conflict guidance. All settings documented in this guide identify which policy type (DC or SC) enforces them.

> **Important:** This is a reference guide — not a one-size-fits-all checklist. Assess each recommendation against your organization's operational requirements, risk tolerance, and deployment model before making changes. Always test with a pilot group before broad rollout.

---

## 2. Security Levels & Source Classification

### 2.1 CIS Benchmark Levels

| Level | Description | Target Audience |
|---|---|---|
| **L1** | Basic security controls with minimal operational impact. Reduces the attack surface without significantly restricting device functionality. | All Android Enterprise-managed devices |
| **L2** | Enhanced security controls for environments handling sensitive data. Some restrictions may affect usability or require additional operational planning. | Most organizations; recommended as the enhanced enterprise baseline |

> **Note:** The CIS Google Android Benchmark v1.6.0 covers MDM-managed enterprise Android devices. Settings labeled MS-Lx in this guide refer to the Microsoft Android Enterprise Security Configuration Framework (see Section 2.2).

---

### 2.2 Microsoft Security Framework Levels

| Level | Description | Typical Enrollment |
|---|---|---|
| **MS-L1** | Basic enterprise security for personally-owned devices accessing work data | Personally-Owned Work Profile (BYOD) |
| **MS-L2** | Enhanced enterprise security; recommended for most corporate-owned devices | Corporate-Owned Work Profile (COPE), Fully Managed (COBO) |
| **MS-L3** | High-security environments with sophisticated threats; strongest enforcement | Fully Managed — high-sensitivity |

---

### 2.3 Admin Experience Level (ADM-EXP)

| Label | Description | When to Adjust |
|---|---|---|
| **ADM-EXP** | Settings not required by CIS or the Microsoft Security Framework. Configured based on professional admin deployment experience and operational best practices. | Fully adjustable — modifying or removing ADM-EXP settings does not affect CIS or Microsoft framework alignment. |

> **ADM-EXP settings** include operational choices such as lock screen messages, kiosk app lists, screen timeout values within the accepted exception range, and default permission policy selections that reflect environment-specific preferences rather than security requirements.

---

### 2.4 Setting Source Classification

Each setting in Sections 4–8 includes a **Level / Source** column showing where it comes from.

| Label | Meaning |
|---|---|
| `CIS L1` | CIS Google Android Benchmark v1.6.0 — Level 1 |
| `CIS L2` | CIS Google Android Benchmark v1.6.0 — Level 2 |
| `MS-L1` / `MS-L2` / `MS-L3` | Microsoft Android Enterprise Security Configuration Framework only (not in CIS, or MS places it at a different tier) |
| `ADM-EXP` | **Admin Experience** — not required by CIS or the Microsoft Framework; configured based on professional deployment experience |
| `CIS x.x*` | Intentional deviation from CIS — see Section 3.1 Deviations Registry |

> All settings — CIS L1, CIS L2, MS-L1, MS-L2, MS-L3, and ADM-EXP — are combined within the same functional-area policies. There is no tier separation at the policy level. The baseline ships as a single deployable set per enrollment type. Organizations targeting a lower tier (e.g., CIS L1 only) can use the Level / Source column alongside Sections 3.2–3.5 to identify which settings to remove before assignment.

Settings labeled **ADM-EXP** are the most flexible — they can be modified or removed without affecting CIS or Microsoft alignment. Settings marked **CIS x.x*** show where this baseline intentionally deviates from CIS — see Section 3.1 for the justification and risk for each.

---

### 2.5 Policy Architecture

Settings are organized by **enrollment type** — each major section covers one Android Enterprise deployment model (FM, COPE, Dedicated, BYOD). Within each section, settings are grouped by functional category. The Level / Source of each individual setting is documented per setting throughout Sections 4–8.

FM and COPE each deploy **two complementary policy layers**: a Device Configuration (DC) template (legacy engine) and a Settings Catalog (SC) policy (modern engine). Settings that are DC-only are called out explicitly. Where both DC and SC cover the same setting, the SC value takes precedence in Intune conflict resolution.

---

## 3. Compliance Reference

This section provides a cross-cutting view of the baseline organized by **source** rather than by policy category. Use this section for pre-production review, CIS audit preparation, or evaluating which settings your organization can adjust without breaking framework alignment.

For the deployment-oriented view (settings organized by policy category and enrollment type), go directly to Sections 4–8.

---

### 3.1 Intentional Deviations from CIS Benchmark

Settings configured differently from the CIS Google Android Benchmark v1.6.0. Each deviation is intentional and marked with `*` in the Level / Source column throughout this guide.

| CIS Control | Enrollment | CIS Requirement | Baseline Value | Risk | Notes |
|---|---|---|---|---|---|
| Screen lock timeout ≤ 2 minutes | FM / COPE / Dedicated | L1 — ≤ 2 min | 5 minutes | Low | Productivity; compliance policy enforces lock requirement independently; CIS recommends ≤ 2 min |
| Minimum security patch level enforced | All | L1 (BYOD) / L2 (Corporate) | Not configured | Low | Admin configures at deployment time based on current Android version and manufacturer cadence; quarterly review recommended |
| Bluetooth configuration (FM) | FM | L2 — Block user configuration | NOT blocked | Low | FM users need to pair Bluetooth headsets for Teams calls, connect to car systems, and home Wi-Fi on take-home devices |
| Data roaming (FM) | FM | L2 — Block | NOT blocked | Low | FM users travel and take devices home; blocking roaming would prevent all data access |
| MAM Play Integrity apps verification | BYOD (MAM) | L2 — Required | Not configured | Medium | BYOD usability concern; hardware attestation is already enforced at the compliance layer |
| MAM minimum security patch version | BYOD (MAM) | L2 — Enforce | Not configured | Low | BYOD users control their own update cadence; overly aggressive enforcement drives unenrollment |

> **For full CIS L1 alignment:** The screen timeout deviation (Low risk) is straightforward to address — set the DC/SC inactivity timeout to 1–2 minutes. The security patch level gap is resolved by setting `minAndroidSecurityPatchLevel` on all compliance policies at deployment time. All other L1 controls are fully implemented.

---

### 3.2 CIS L1 Settings

All CIS Level 1 controls from the Google Android Benchmark v1.6.0 implemented in this baseline. Settings that apply across enrollment types are listed with their applicable enrollment types. The **MS - Level** column indicates if the setting also appears in the Microsoft Android Security Configuration Framework.

#### Authentication & Screen Lock

| Setting | Intune Policy | Enrollment Type | CIS - Level | MS - Level | Notes |
|---|---|---|---|---|---|
| Require screen lock | All compliance + DC policies | FM, COPE, Ded, BYOD | L1 | MS-L1 | Foundation for device encryption and access control |
| Password type — numeric complex | Corporate compliance + DC/SC policies | FM, COPE, Ded | L1 | MS-L2 | Non-sequential, non-repeating digits required |
| Password type — medium complexity | BYOD compliance + DC BYOD | BYOD | L1 | MS-L1 | Work profile: `numericComplex`; device: `medium` complexity |
| Minimum password length ≥ 6 | All compliance + DC/SC policies | FM, COPE, Ded, BYOD | L1 | MS-L1 | Base brute-force resistance |
| Password expiration ≤ 365 days | DC FM, DC COPE, DC BYOD (work profile) | FM, COPE, BYOD | L1 | MS-L2 | Annual rotation enforced |
| Password history ≥ 5 previous | DC FM, DC COPE, DC BYOD (work profile) | FM, COPE, BYOD | L1 | MS-L2 | Prevents reuse of last 5 passwords |
| Max failed unlock attempts ≤ 10 | DC FM, DC COPE, DC Kiosk | FM, COPE, Ded | L1 | MS-L2 | Auto wipe after 10 failed PIN attempts |
| Screen lock re-authentication | DC FM / DC COPE | FM, COPE | L1 | MS-L2 | DC FM: `daily` (full PIN required at least once per 24h); DC COPE: `deviceDefault` |
| Block trust agents (keyguard) | DC FM (full set); DC COPE (trustAgents only); DC BYOD (work profile) | FM, COPE, BYOD | L1 | MS-L2 | Prevents trusted Bluetooth devices and Smart Lock from bypassing lock screen |
| Date/time configuration blocked | DC FM + SC FM; DC COPE + SC COWP; DC Kiosk/Shared | FM, COPE, Ded | L1 | MS-L2 | Prevents time manipulation to bypass certificate validity or log tampering detection |

#### Device Integrity

| Setting | Intune Policy | Enrollment Type | CIS - Level | MS - Level | Notes |
|---|---|---|---|---|---|
| Block rooted / jailbroken devices | All compliance policies | FM, COPE, Ded, BYOD | L1 | MS-L1 | Critical — rooted devices bypass all MDM controls |
| Play Integrity — basic integrity | All compliance policies + MAM | FM, COPE, Ded, BYOD | L1 | MS-L1 | `basicIntegrity` — device is unmodified |
| Play Integrity — certified device | All compliance policies + MAM | FM, COPE, Ded, BYOD | L1 | MS-L1 | `certifiedDevice` — device meets Android compatibility standards |
| Verify apps required | DC FM, DC COPE, DC BYOD (work profile); BYOD compliance | FM, COPE, BYOD | L1 | MS-L1 | Google Play Protect app verification enabled |
| USB debugging disabled | BYOD compliance | BYOD | L1 | MS-L1 | Prevents ADB access on personal devices |
| Developer settings disabled | SC FM, SC COWP | FM, COPE | L1 | MS-L2 | Prevents enabling developer mode and ADB access |

#### App Management

| Setting | Intune Policy | Enrollment Type | CIS - Level | MS - Level | Notes |
|---|---|---|---|---|---|
| Block install from unknown sources | SC FM, SC COWP; DC BYOD (work profile) | FM, COPE, BYOD | L1 | MS-L1 | Only Managed Google Play-approved apps can be installed |
| Play Store mode — allow-list | DC FM | FM | L1 | MS-L2 | Only MGP-approved apps visible and installable; DC FM enforces this |
| App auto-update — always | DC FM, SC FM; DC COPE, SC COWP | FM, COPE | L1 | MS-L2 | Keeps all managed apps current with security patches |

#### Data Protection

| Setting | Intune Policy | Enrollment Type | CIS - Level | MS - Level | Notes |
|---|---|---|---|---|---|
| Storage encryption required | All compliance policies | FM, COPE, Ded, BYOD | L1 | MS-L1 | Android encrypts automatically with a screen lock set |
| Screen capture blocked | DC FM, SC FM; DC COPE, SC COWP; DC Kiosk; DC Shared; DC BYOD (work profile) | FM, COPE, Ded, BYOD | L1 | MS-L2 | Prevents screenshots of corporate content |
| USB file transfer blocked | DC FM; SC FM, SC COWP | FM, COPE | L1 | MS-L2 | Blocks data extraction via USB cable |
| External storage blocked | DC FM; SC FM, SC COWP; DC Kiosk, DC Shared | FM, COPE, Ded | L1 | MS-L2 | SD card and USB OTG storage not accessible |

---

### 3.3 CIS L2 Settings

All CIS Level 2 controls from the Google Android Benchmark v1.6.0 implemented in this baseline. L2 settings apply primarily to corporate-owned devices (FM, COPE, Dedicated).

| Setting | Intune Policy | Enrollment Type | CIS - Level | MS - Level | Notes |
|---|---|---|---|---|---|
| Play Integrity — hardware-backed evaluation | All compliance policies | FM, COPE, Ded, BYOD | L2 | MS-L2 | TEE-backed attestation; see W-18 for rollout risk |
| Intune app integrity | Corporate compliance policies | FM, COPE, Ded | L2 | MS-L2 | Validates Company Portal app integrity |
| Factory reset blocked | DC FM + SC FM; DC COPE + SC COWP; DC Kiosk, DC Shared | FM, COPE, Ded | L2 | MS-L2 | Prevents unauthorized unenrollment via factory reset |
| Account modification blocked | DC FM + SC FM; DC COPE + SC COWP; DC Kiosk, DC Shared | FM, COPE, Ded | L2 | MS-L2 | Prevents adding/removing Google accounts and MDM enrollment accounts |
| Certificate installation disabled | DC FM; DC COPE; SC FM, SC COWP | FM, COPE | L2 | MS-L2 | Manual cert install disabled; requires SCEP/PKCS profile (see W-5) |
| NFC outgoing beam blocked | DC FM; SC FM, SC COWP | FM, COPE | L2 | MS-L2 | Prevents NFC-based data transfer and tag writing |
| Wi-Fi tethering blocked | DC FM + SC FM; DC COPE + SC COWP; DC Kiosk, DC Shared | FM, COPE, Ded | L2 | MS-L2 | Prevents device from sharing cellular connection |
| Wi-Fi Direct disallowed | SC FM, SC COWP | FM, COPE | L2 | MS-L2 | Prevents peer-to-peer Wi-Fi bypassing network controls |
| Bluetooth contact sharing blocked | DC COPE; SC COWP | COPE | L2 | MS-L2 | Prevents Bluetooth from exposing work contacts to other devices; FM exception documented |
| Data roaming blocked | DC COPE + SC COWP; DC Kiosk, DC Shared | COPE, Ded | L2 | MS-L2 | FM exception: users travel and take devices home (see Section 3.1) |
| Google accounts blocked | DC FM | FM | L2 | MS-L2 | Personal Google services blocked on fully managed devices |
| Screen lock timeout ≤ 2 min | Corporate compliance + DC policies | FM, COPE, Ded | L2* | MS-L2 | **Intentional deviation** — 5 min used; see Section 3.1 |
| Min security patch level | Not configured | All | L2 (Corp) / L1 (BYOD) | MS-L2 | **Intentional deviation** — admin sets at deployment time; see Section 3.1 |
| Private Space disallowed | SC COWP | COPE | L2 | MS-L2 | Android 15+ Private Space feature blocked on corporate devices |
| Assist content blocked | SC FM, SC COWP | FM, COPE | L2 | MS-L2 | Blocks on-screen content being sent to Google Assistant / Gemini |

---

### 3.4 Microsoft Framework Settings (Non-CIS)

Settings from the Microsoft Android Enterprise Security Configuration Framework that are **not in the CIS L1/L2 profile** or where Microsoft places them at a different tier than CIS.

| Setting | Intune Policy | Enrollment Type | CIS - Level | MS - Level | Notes |
|---|---|---|---|---|---|
| MDE threat protection — compliance | AND - CP - DEV - Compliance - MDE - Corporate Devices / Personal Devices | FM, COPE, Ded, BYOD | — | MS-L2 | Requires MDE–Intune connector; separate policy for phased deployment |
| Conditional Access — require compliant device | CA policy | FM, COPE, Ded | — | MS-L2 | Compliance signal without CA has no enforcement power |
| Conditional Access — MAM App Protection Policy | CA policy | BYOD | — | MS-L1 | OR logic allows MAM-only path for users who don't enroll MDM |
| App Protection Policy (MAM) — all settings | MAM policy | BYOD | — | MS-L1 / MS-L2 | CIS does not address MAM; all settings from Microsoft framework |
| MDE low-touch onboarding | App Configuration Policy | FM, COPE, BYOD | — | MS-L2 | Onboards MDE without requiring user interaction on corporate devices |
| MDE network protection | App Configuration Policy | FM, COPE, BYOD | — | MS-L2 | Anti-phishing and malicious connection detection at network level |
| Locate device (Lost Mode) | DC FM, DC COPE | FM, COPE | — | MS-L2 | Remote device location via Intune Lost Mode action |
| Common Criteria Mode | SC FM, SC COWP | FM, COPE | — | MS-L3 | Samsung Knox-specific; see W-25 for non-Samsung device impact |
| Lock screen message | DC FM | FM | — | ADM-EXP | Organization name displayed on device lock screen |

---

### 3.5 ADM-EXP Settings — Admin Experience

Settings not required by CIS or the Microsoft Framework, configured based on professional admin deployment experience. These can be modified or removed without breaking CIS or Microsoft alignment.

| Setting | Intune Policy | Enrollment Type | Notes |
|---|---|---|---|
| App permission policy — device default | DC FM; SC FM, SC COWP | FM, COPE | Defers runtime permission decisions to device; `prompt` used on COPE DC |
| Skip first-use hints | DC FM; SC FM, SC COWP | FM, COPE | Suppresses app onboarding wizards on managed devices |
| Lock screen message — org name | DC FM | FM | Displays organization name on lock screen — update placeholder before deploying |
| Kiosk exit code "0365" | DC Multi-App Kiosk | Dedicated | IT-only exit code to leave kiosk mode; change before deploying |
| Kiosk system error warnings enabled | DC Multi-App Kiosk | Dedicated | Allows users to see system-level error dialogs in kiosk mode |
| Kiosk Bluetooth/Wi-Fi/Flashlight/Volume controls | DC Multi-App Kiosk | Dedicated | User-accessible controls in kiosk UI — adjust based on kiosk scenario |
| Kiosk grid layout — 4×5 | DC Multi-App Kiosk | Dedicated | App grid dimensions for MHS launcher |
| Factory reset admin emails | DC Kiosk/Shared | Dedicated | Currently empty; populate before deploying to control post-wipe configuration |
| Network escape hatch (Kiosk) | DC Multi-App Kiosk | Dedicated | Enabled for Kiosk enrollment to allow Wi-Fi recovery during provisioning |

---

## 4. Fully Managed (COBO) — Policy Settings

Fully Managed (COBO) devices are corporate-owned and fully managed by IT. The device owner profile covers the entire device with no personal profile. This enrollment type supports the broadest set of MDM restrictions and is the recommended model for frontline workers and corporate-dedicated devices.

> **Two-policy architecture:** Each setting category is covered by both a DC template (`AND - DC - Device-Restrictions - DEV - Corporate-Owned - Fully-Managed`) and a Settings Catalog policy (`AND - SC - Device-Restrictions - DEV - Corporate-Owned - Fully-Managed`). See README.md for DC-only settings that have no SC equivalent.

---

### 4.1 Password & Screen Lock

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Password type | Numeric complex | CIS L1 | Non-sequential, non-repeating digits — `numericComplex` |
| Minimum password length | 6 characters | CIS L1 | Base brute-force resistance |
| Password expiration | 365 days | CIS L1 | Annual rotation; `passwordExpirationDays: 365` |
| Password history | 5 previous passwords | CIS L1 | `passwordPreviousPasswordCountToBlock: 5` |
| Max failed unlock attempts | 10 | CIS L1 | Device factory wipes after 10 failed attempts — see W-1 |
| Require full PIN unlock | Daily | CIS L1 | `passwordRequireUnlock: daily` — full PIN at least once per 24 hours; see W-21 |
| Screen inactivity timeout | 5 minutes | CIS L1* | **Intentional deviation** — CIS recommends ≤ 2 min; 5 min used for productivity; see Section 3.1 |
| Keyguard — block camera | Block | CIS L1 | Prevents camera access from lock screen |
| Keyguard — block remote input | Block | CIS L1 | Prevents voice input from lock screen when device is locked |
| Keyguard — block trust agents | Block | CIS L1 | Prevents Smart Lock from bypassing screen lock |
| Keyguard — block unredacted notifications | Block | CIS L1 | Hides notification content detail on lock screen |
| Lock screen message | "This device belongs to {Organization Name}" | ADM-EXP | Replace placeholder with actual org name before deploying |

> **DC-only keyguard settings:** Granular keyguard feature blocking (camera, remoteInput, trustAgents, unredactedNotifications) is enforced via the DC template. The SC FM policy enforces `passwordBlockKeyguard: true` (full keyguard block) as a complementary control.

---

### 4.2 Device Restrictions

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Block account modification | Block | CIS L2 | Prevents adding/removing accounts after enrollment — see W-6 |
| Block factory reset | Block | CIS L2 | Users cannot factory-reset device via Settings |
| Block Google accounts | Block | CIS L2 | Personal Google services (Maps, Pay, Gmail) not available — see W-17 |
| Block certificate installation | Block | CIS L2 | Manual cert install disabled; deploy SCEP/PKCS before rollout — see W-5 |
| Block device settings (kiosk UI) | Block | ADM-EXP | `kioskCustomizationDeviceSettingsBlocked: true` — restricts device settings access in kiosk customization context |
| Add new users | Block | MS-L2 | `usersBlockAdd: true` — DC-only; no SC equivalent |
| Remove users | Block | MS-L2 | `usersBlockRemove: true` — DC-only; no SC equivalent |
| Date/time configuration | Blocked | CIS L1 | Prevents time manipulation; `dateTimeConfigurationBlocked: true` |
| Location mode | Enforced | MS-L2 | `locationMode: enforced` — location services enabled and locked (required for MDE and Lost Mode) |
| Skip first-use hints | Enabled | ADM-EXP | Suppresses onboarding wizards on managed apps |

---

### 4.3 App Management

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Play Store mode | Allow-list | CIS L1 | Only MGP-approved apps installable; `playStoreMode: allowList` — see W-8 |
| App auto-update | Always | CIS L1 | Apps update silently at any time — see W-9 for LOB app risk |
| Block unknown sources | Block | CIS L1 | Enforced via SC FM (`appsAllowInstallFromUnknownSources: false`) |
| Verify apps | Required | CIS L1 | Google Play Protect verification for installed apps |
| Assist content | Blocked | CIS L2 | `assistContentPolicy: true` — blocks screen content from reaching Google Assistant / Gemini |
| App default permission policy | Device default | ADM-EXP | Runtime permission requests handled per-device default |

---

### 4.4 Connectivity & Network

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Wi-Fi tethering | Blocked | CIS L2 | Prevents device from acting as Wi-Fi hotspot |
| Wi-Fi Direct | Disallowed | CIS L2 | SC FM: `wifiDirectSettings: disallowed` — blocks peer-to-peer Wi-Fi |
| NFC outgoing beam | Blocked | CIS L2 | Prevents NFC-based data transfer |
| Bluetooth configuration | NOT blocked | CIS L2* | **Intentional deviation** — FM users need to pair headsets and connect to home Wi-Fi; see Section 3.1 and W-3 |
| Data roaming | NOT blocked | CIS L2* | **Intentional deviation** — FM users travel and take devices home; see Section 3.1 |
| Network escape hatch | Disabled | MS-L2 | `networkEscapeHatchAllowed: false` — no fallback network during enrollment; see W-4 |
| Wi-Fi policy-defined configurations | NOT blocked | ADM-EXP | Users can modify Wi-Fi settings — intentional for FM; SC: `wifiBlockEditPolicyDefinedConfigurations: false` |

---

### 4.5 Data Protection & Storage

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Screen capture | Blocked | CIS L1 | DC FM + SC FM both enforce; `screenCaptureBlocked: true` |
| USB file transfer | Blocked | CIS L1 | DC FM: `storageBlockUsbFileTransfer: true`; SC FM: `usbDataAccess: disallowUsbDataTransfer` |
| External storage (SD card / USB OTG) | Blocked | CIS L1 | `storageBlockExternalMedia: true` — see W-14 |
| Cross-profile data sharing | Not configured | ADM-EXP | FM has no personal profile — cross-profile settings not applicable |

---

### 4.6 System & Security

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Developer settings | Disabled | CIS L1 | SC FM: `securityDeveloperSettingsEnabled: false` |
| Common Criteria Mode | Enabled | MS-L3 | `securityCommonCriteriaModeEnabled: true` — Samsung Knox-specific; see W-25 |
| System update install type | Windowed | ADM-EXP | Updates install only during 00:00–06:00 maintenance window (DC FM) |
| Locate device (Lost Mode) | Enabled | MS-L2 | `locateDeviceLostModeEnabled: true` — enables remote location via Intune Lost Mode |
| Private Space | Not applicable | — | FM has no personal profile; Private Space not relevant |

---

## 5. Corporate-Owned Work Profile (COPE) — Policy Settings

Corporate-Owned Work Profile (COPE) devices are corporate-owned but include a personal profile that the user controls. MDM management applies to the work profile and device-level settings; the personal profile is unmanaged. COPE is the recommended model for mobile workers who also use the device personally.

> **Two-policy architecture:** DC (`AND - DC - Device-Restrictions - DEV - Corporate-Owned - Work-Profile`) and SC (`AND - SC - Device-Restrictions - DEV - Corporate-Owned - Work-Profile`) are deployed together. SC adds Private Space, cross-profile controls, and location enforcement.

---

### 5.1 Password & Screen Lock

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Password type | Numeric complex | CIS L1 | `numericComplex` — same as FM |
| Minimum password length | 6 characters | CIS L1 | |
| Password expiration | 365 days | CIS L1 | |
| Password history | 5 previous passwords | CIS L1 | |
| Max failed unlock attempts | 10 | CIS L1 | |
| Require full PIN unlock | Device default | ADM-EXP | `passwordRequireUnlock: deviceDefault` — not enforced daily on COPE (differs from FM) |
| Screen inactivity timeout | 5 minutes | CIS L1* | Same deviation as FM — see Section 3.1 |
| Keyguard — block trust agents | Block | CIS L1 | DC COPE: `passwordBlockKeyguardFeatures: [trustAgents]` only — gap vs. FM (see W-24) |

> **Keyguard gap (W-24):** DC COPE only blocks trust agents at the keyguard level. Camera, remoteInput, and unredactedNotifications are not blocked in DC COPE (unlike DC FM which blocks all four). This is an open gap documented in Section 12 / W-24. COPE work profile trust agents are also blocked via SC COWP.

---

### 5.2 Device Restrictions

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Block account modification | Block | CIS L2 | `accountsBlockModification: true` |
| Block factory reset | Block | CIS L2 | Both DC COPE (`factoryResetBlocked: true`) and SC COWP enforce |
| Block certificate installation | Block | CIS L2 | Deploy SCEP/PKCS before rollout — see W-5 |
| Add new users | Block | MS-L2 | `usersBlockAdd: true` — DC-only |
| Remove users | NOT blocked | MS-L2 | **Open gap (W-24):** `usersBlockRemove: false` on DC COPE — users can de-enroll corporate device |
| Date/time configuration | Blocked | CIS L1 | |
| Location mode | Enforced | MS-L2 | SC COWP: `locationMode: enforced` |
| Private Space | Disallowed | CIS L2 | SC COWP: `privateSpacePolicy: disallowed` — Android 15+ feature blocked |
| Developer settings | Disabled | CIS L1 | SC COWP |
| Network escape hatch | Not configured | ADM-EXP | `null` on DC COPE; SC COWP: disabled |
| Common Criteria Mode | Enabled | MS-L3 | SC COWP: `securityCommonCriteriaModeEnabled: true` — Samsung Knox only; see W-25 |

---

### 5.3 Work Profile Controls

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Cross-profile copy/paste | Blocked | CIS L2 | DC COPE + SC COWP: `crossProfilePoliciesAllowCopyPaste: false` |
| Cross-profile data sharing | Work → Personal blocked | CIS L2 | `dataSharingFromWorkToPersonalBlocked` — work data cannot be shared to personal apps |
| Work contacts visible in personal dialer | Enabled | ADM-EXP | `crossProfilePoliciesShowWorkContactsInPersonalProfile: true` — intentional UX decision |
| Bluetooth contact sharing | Blocked | CIS L2 | DC COPE + SC COWP: `bluetoothBlockContactSharing: true` — prevents work contacts via Bluetooth |

---

### 5.4 App Management

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Play Store mode | Allow-list | CIS L1 | DC COPE: `playStoreMode: allowList` |
| App auto-update | Always | CIS L1 | DC COPE + SC COWP |
| Block unknown sources | Block | CIS L1 | SC COWP |
| Verify apps | Required | CIS L1 | DC COPE |
| Assist content | Blocked | CIS L2 | SC COWP |
| App default permission policy | Device default | ADM-EXP | SC COWP; DC COPE uses `prompt` |

---

### 5.5 Connectivity & Network

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Wi-Fi tethering | Blocked | CIS L2 | DC COPE + SC COWP |
| Wi-Fi Direct | Disallowed | CIS L2 | SC COWP |
| NFC outgoing beam | Blocked | CIS L2 | DC COPE + SC COWP |
| Bluetooth configuration | NOT blocked | ADM-EXP | `bluetoothBlockConfiguration: false` — COPE users also pair personal BT devices |
| Bluetooth contact sharing | Blocked | CIS L2 | `bluetoothBlockContactSharing: true` |
| Data roaming | Blocked | CIS L2 | DC COPE + SC COWP: `dataRoamingBlocked: true` |
| USB file transfer | Blocked | CIS L1 | SC COWP: `usbDataAccess: disallowUsbDataTransfer` |
| External storage | Blocked | CIS L1 | SC COWP |

---

## 6. Dedicated Kiosk & Shared Device — Policy Settings

Dedicated devices run in kiosk mode (single-app or multi-app) or shared mode for shift workers. Three DC policies cover these scenarios: Multi-App Kiosk, Single-App Kiosk, and Shared Device. No SC policy is deployed for Dedicated devices — the DC policies cover all settings.

> **Enrollment profile:** `enrollmentProfile: dedicatedDevice` — configures the device as a dedicated Android device from the moment of enrollment.

---

### 6.1 Authentication & Lock

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Password type | Numeric complex | CIS L1 | Shared PIN for shift workers |
| Minimum password length | 6 characters | CIS L1 | |
| Password expiration | 365 days | CIS L1 | |
| Password history | 5 previous | CIS L1 | |
| Max failed unlock attempts | 10 | CIS L1 | Shared devices: see W-19 for factory wipe risk |
| Require full PIN unlock | Device default | ADM-EXP | `deviceDefault` — not daily on Dedicated |
| Screen inactivity timeout | Not configured | ADM-EXP | Multi-App Kiosk: `null` — screen stays on; Single-App Kiosk: 1 min |
| Keyguard features blocked | Camera, notifications, remote input | CIS L1 | Prevents lock screen camera, notification access, and remote voice input |

> **Shared device risk (W-19, W-21):** 10 failed unlock attempts triggers a factory wipe on all kiosk types. On shared devices where multiple shift workers use the same PIN, an unfamiliar worker approaching the threshold can wipe an operational device. See Section 11 for mitigation steps.

---

### 6.2 Kiosk Restrictions

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Account modification | Blocked | CIS L2 | |
| Factory reset | Blocked | CIS L2 | |
| Google accounts | Blocked | CIS L2 | |
| Data roaming | Blocked | CIS L2 | |
| Date/time configuration | Blocked | CIS L1 | |
| Wi-Fi tethering | Blocked | CIS L2 | |
| Screen capture | Blocked | CIS L1 | |
| USB file transfer | Blocked | CIS L1 | |
| External storage | Blocked | CIS L1 | |
| Network escape hatch | Enabled (Multi-App Kiosk) | ADM-EXP | Required for Dedicated enrollment Wi-Fi recovery; see W-4 |
| System error warnings | Enabled | ADM-EXP | Users can see critical system alerts in kiosk UI |
| Kiosk exit code | "0365" | ADM-EXP | **Replace before deploying** — change to a secure IT-only code |
| Kiosk app list | Outlook, Teams, OneDrive, SharePoint, Edge, Authenticator, Intune, MHS | ADM-EXP | Customize for your kiosk use case — this is a sample set |
| System update window | 00:00–06:00 | ADM-EXP | Updates install during overnight maintenance window |

---

## 7. Personally-Owned Work Profile (BYOD) — Policy Settings

BYOD devices are personally owned. Users enroll via the Company Portal app. MDM management is scoped to the work profile — the personal side of the device is not managed and not visible to IT. Users can unenroll at any time, removing all corporate data from the work profile.

> **Scope:** Settings in this section apply to the work profile only. Personal profile behavior (personal apps, personal storage, personal camera) is not restricted by MDM.

---

### 7.1 Work Profile Password

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Work profile require password | Required | CIS L1 | Separate work profile password required |
| Work profile password type | Numeric complex | CIS L1 | `workProfilePasswordRequiredType: numericComplex` |
| Work profile password complexity | Medium | CIS L1 | `workProfileRequiredPasswordComplexity: medium` |
| Work profile minimum length | 6 characters | CIS L1 | |
| Work profile password expiration | 365 days | CIS L1 | `workProfilePasswordExpirationDays: 365` |
| Work profile password history | 5 previous | CIS L1 | `workProfilePasswordPreviousPasswordBlockCount: 5` |
| Work profile max failed attempts | 10 | CIS L1 | Work profile wipes after 10 failures |
| Work profile screen timeout | 5 minutes | CIS L1* | Intentional deviation — see Section 3.1 |
| Block trust agents (work profile) | Block | CIS L1 | `workProfilePasswordBlockTrustAgents: true` |
| Unified password for work/personal | NOT blocked | CIS L2 | **Open gap (W-11):** `blockUnifiedPasswordForWorkProfile: false` — CIS recommends requiring separate passwords |

> **Device password:** The device-level password complexity is set to `medium` via `requiredPasswordComplexity` — this is the BYOD device password, separate from the work profile password above.

---

### 7.2 Work Profile Restrictions

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Block notifications while locked | Block | CIS L1 | `workProfileBlockNotificationsWhileDeviceLocked: true` |
| Block adding accounts | Block | CIS L1 | `workProfileBlockAddingAccounts: true` — work profile add accounts blocked |
| Work profile account use | Block all | MS-L1 | `workProfileAccountUse: blockAll` — blocks non-work accounts in work profile |
| Block widgets on home screen | Block | ADM-EXP | `workProfileAllowWidgets: false` — prevents work app widgets from appearing on personal home screen |
| Block caller ID | Block | CIS L1 | `workProfileBlockCrossProfileCallerId: true` — work number hidden on personal side |
| Block contact search | Block | CIS L2 | `workProfileBlockCrossProfileContactsSearch: true` — personal dialer cannot search work contacts |
| App default permission policy | Prompt | ADM-EXP | `workProfileDefaultAppPermissionPolicy: prompt` — users asked before permissions granted |
| Block unknown sources | Block | CIS L1 | `workProfileAllowAppInstallsFromUnknownSources: false`; personal unknown sources also blocked |
| Verify apps | Required | CIS L1 | `securityRequireVerifyApps: true` |

---

### 7.3 Cross-Profile Data Controls

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Cross-profile copy/paste | Blocked | CIS L2 | `workProfileBlockCrossProfileCopyPaste: true` — work→personal clipboard blocked |
| Cross-profile data sharing | Personal → Work allowed; Work → Personal blocked | CIS L1 | `workProfileDataSharingType: allowPersonalToWork` — users can share personal content into work apps; work content cannot leave to personal apps |
| Work profile screen capture | Blocked | CIS L1 | `workProfileBlockScreenCapture: true` |

---

## 8. App Protection Policy (MAM) — BYOD

App Protection Policies (MAM) enforce data boundaries within managed apps on personally-owned devices, regardless of MDM enrollment status. Unlike device-level policies, MAM operates at the app layer — it applies to Microsoft 365 apps (Outlook, Teams, OneDrive, Edge, etc.) on any device where the user signs in.

**Targeted apps:** Microsoft Edge, Excel, Office Hub (phone/tablet), OneNote, Outlook, PowerPoint, Word, SharePoint, OneDrive, Teams, To Do (12 core apps).

> **CIS coverage:** App Protection Policies are not addressed in the CIS Google Android Benchmark. The Level / Source column in this section references the Microsoft Android Security Configuration Framework only.

---

### 8.1 Data Transfer & Protection

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Send org data to other apps | Policy managed apps only | MS-L1 | Corporate data stays within Intune-managed Microsoft 365 apps |
| Receive data from other apps | Policy managed apps only | MS-L1 | `allowedInboundDataTransferSources: managedApps` |
| Restrict cut/copy/paste between apps | Managed apps with paste in | MS-L1 | `managedAppsWithPasteIn` — corporate content cannot be pasted to personal apps; personal content can be pasted into managed apps |
| Data transfer to dialer apps | All dialer apps | MS-L1 | Phone number taps in managed apps open any installed dialer |
| Save copies of org data | Blocked | MS-L1 | `saveAsBlocked: true` |
| Allowed save destinations | OneDrive for Business, SharePoint, Local Storage | MS-L1 | `allowedDataStorageLocations` — local storage included to support camera attachment workflows |
| Data ingestion from | OneDrive for Business, SharePoint, Camera | MS-L1 | `allowedDataIngestionLocations` — gallery/local storage NOT included (see W-23) |
| Block data ingestion from other sources | Enabled | MS-L1 | `blockDataIngestionIntoOrganizationDocuments: true` |
| Backup org data | Blocked | MS-L1 | `dataBackupBlocked: true` — prevents corporate data in Android backup |
| Contact sync to native apps | Blocked | MS-L1 | `contactSyncBlocked: true` |
| Print | Blocked | MS-L1 | `printBlocked: true` |
| Screen capture | Blocked | MS-L1 | `screenCaptureBlocked: true` |
| App data encryption | Required | MS-L1 | `encryptAppData: true` |
| Notification content restriction | Block org data | MS-L1 | `notificationRestriction: blockOrganizationalData` — notifications do not show corporate content |
| Managed browser | Microsoft Edge | MS-L1 | Links in managed apps open in Edge; see W-20 for rollout guidance |
| Third-party keyboards | NOT blocked | MS-L2 | `keyboardsRestricted: false` — L2 keyboard restriction not applied in this baseline |

---

### 8.2 Access Requirements

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| PIN for access | Required | MS-L1 | Separate app-level PIN to open managed apps |
| PIN type | Numeric | MS-L1 | `pinCharacterSet: numeric` |
| Minimum PIN length | 6 | MS-L1 | |
| Block simple PIN | Block | MS-L1 | `simplePinBlocked: true` — sequential and repeating patterns blocked |
| PIN reset period | 90 days | MS-L1 | `periodBeforePinReset: P90D` |
| Previous PIN block count | 5 | MS-L1 | `previousPinBlockCount: 5` |
| Max PIN retries before block | 5 | MS-L1 | `maximumPinRetries: 5` — access blocked after 5 incorrect attempts |
| Allow biometric instead of PIN | Allow | MS-L1 | `fingerprintBlocked: false` — Face ID / fingerprint satisfies PIN |
| Require Class 3 biometrics | Required | MS-L2 | `requireClass3Biometrics: true` — strong biometrics (fingerprint on dedicated sensor) required |
| PIN required after biometric change | Required | MS-L2 | `requirePinAfterBiometricChange: true` |
| Override biometrics with PIN timeout | 30 minutes | MS-L1 | `pinRequiredInsteadOfBiometricTimeout: PT30M` |
| Organizational credentials | Required | MS-L2 | `organizationalCredentialsRequired: true` — Entra ID sign-in required |
| Recheck access requirements | 30 minutes (offline check) | MS-L1 | `periodOnlineBeforeAccessCheck: PT30M` |
| Minimum OS version | 13.0 (Warn) | MS-L1 | `minimumRequiredOsVersion: 13.0` |
| Disable app PIN if device PIN set | Not configured | ADM-EXP | `disableAppPinIfDevicePinIsSet: false` — app PIN always required regardless of device PIN |

---

### 8.3 Conditional Launch

| Setting | Configured Value | Level / Source | Notes |
|---|---|---|---|
| Max PIN retries | 5 — Block access | MS-L1 | Block access after 5 incorrect PIN entries |
| Offline grace period | 12 hours (access check) / 90 days (wipe) | MS-L1 | `periodOfflineBeforeAccessCheck: PT12H`; `periodOfflineBeforeWipeIsEnforced: P90D` |
| Rooted / jailbroken devices | Block access | MS-L1 | `appActionIfDeviceComplianceRequired: block` (combined with Play Integrity) |
| Disabled account | Block access | MS-L1 | `appActionIfUnableToAuthenticateUser: block` |
| Play Integrity — basic integrity + certified device | Block | MS-L1 | `requiredAndroidSafetyNetDeviceAttestationType: basicIntegrityAndDeviceCertification` → block |
| Play Integrity evaluation type | Basic | MS-L1 | `requiredAndroidSafetyNetEvaluationType: basic` — NOT hardware-backed (deferred; see Section 3.1) |
| Play Protect app verification | None | MS-L1 | `requiredAndroidSafetyNetAppsVerificationType: none` — deferred (see Section 3.1) |
| Device lock not set | Block | MS-L1 | `appActionIfDeviceLockNotSet: block` — enforces device lock via conditional launch (see C-10 in Section 12) |
| Device passcode complexity | Low minimum — Block | MS-L1 | `appActionIfDevicePasscodeComplexityLessThanLow: block` |
| Max device threat level | Not configured | MS-L2 | `maximumAllowedDeviceThreatLevel: notConfigured` — requires MDE; see C-7 in Section 12 |

---

## 9. Compliance Policy Settings

Compliance policies define the minimum security requirements a device must meet to be considered compliant. Non-compliant devices are blocked from accessing corporate resources via Conditional Access. Configure compliance policies in **Intune > Devices > Compliance**.

> **MDE compliance:** MDE threat protection is enforced via separate dedicated policies (`AND - CP - DEV - Compliance - MDE - Corporate Devices` and `AND - CP - DEV - Compliance - MDE - Personal Devices`) to allow independent assignment during phased MDE deployment. See Section 9.1 and C-1 in Section 12.

---

### 9.1 Fully Managed (COBO)

Policy: `AND - CP - DEV - Compliance - Corporate-Fully-Managed-Devices`

| Setting | Recommended Value | CIS Level | MS Level | Security Benefit |
|---|---|---|---|---|
| Block jailbroken / rooted devices | `Block` | L1 | MS-L2 | Critical — rooted devices bypass MDM controls |
| Storage encryption required | `Require` | L1 | MS-L1 | Data at rest protected |
| Intune app integrity | `Require` | L2 | MS-L2 | Validates Company Portal has not been tampered with |
| Play Integrity — basic integrity | `Require` | L1 | MS-L1 | Device is unmodified |
| Play Integrity — certified device | `Require` | L1 | MS-L1 | Device meets Android compatibility |
| Play Integrity evaluation type | `Hardware backed` | L2 | MS-L2 | TEE-backed attestation — see W-18 |
| Password required | `Require` | L1 | MS-L1 | Prerequisite for encryption and access control |
| Password type | `Numeric complex` | L1 | MS-L2 | |
| Minimum password length | `6` | L1 | MS-L1 | |
| Password expiration | `365 days` | L1 | MS-L2 | |
| Password history | `5` | L1 | MS-L2 | |
| Screen lock inactivity | `5 minutes` | L1* | MS-L2 | Intentional deviation — see Section 3.1 |
| Min OS version | `13.0` | L1 | MS-L2 | Enforces minimum Android version |
| Min security patch level | `Not configured` | L1* | MS-L2 | Admin sets at deployment time — see Section 3.1 |
| MDE threat level | `Low → Block` (separate policy) | — | MS-L2 | Via `AND - CP - DEV - Compliance - MDE - Corporate Devices` |

---

### 9.2 Corporate-Owned Work Profile (COPE)

Policy: `AND - CP - DEV - Compliance - Corporate-Owned - Work-Profile`

Same settings as Section 9.1. The compliance policy type is `androidDeviceOwnerCompliancePolicy` — same as FM.

---

### 9.3 Dedicated Kiosk & Shared

Policies: `AND - CP - DEV - Compliance - Corporate-Dedicated-Devices - Kiosk` and `AND - CP - DEV - Compliance - Corporate-Dedicated-Devices - Shared`

Same core settings as Section 9.1. Dedicated device compliance also uses `androidDeviceOwnerCompliancePolicy`.

> **Kiosk attestation risk:** Hardware-backed Play Integrity (`securityRequiredAndroidSafetyNetEvaluationType: hardwareBacked`) requires a TEE. Older fixed-location kiosk hardware (pre-Android 8.0, budget models) may not have TEE support and will immediately fail attestation — see W-18.

---

### 9.4 Personally-Owned Work Profile (BYOD)

Policy: `AND - CP - USR - Compliance - Personally-Owned - Work-Profile`

| Setting | Configured Value | CIS Level | MS Level | Notes |
|---|---|---|---|---|
| Block rooted devices | `Block` | L1 | MS-L1 | |
| Storage encryption | `Require` | L1 | MS-L1 | |
| Password required | `Require` | L1 | MS-L1 | |
| Password complexity | `Medium` | L1 | MS-L1 | Device password; `requiredPasswordComplexity: medium` |
| Screen lock inactivity | `15 minutes` | L1 | MS-L1 | BYOD: lighter than corporate for usability |
| Password expiration | `365 days` | L1 | MS-L2 | |
| Password history | `5` | L1 | MS-L2 | |
| Work profile require password | `Require` | L1 | MS-L1 | Work profile has a separate lock |
| Work profile password type | `Numeric complex` | L1 | MS-L1 | |
| Work profile min length | `6` | L1 | MS-L1 | |
| USB debugging disabled | `Require` | L1 | MS-L1 | `securityDisableUsbDebugging: true` |
| Google Play Services required | `Require` | L1 | MS-L1 | Prerequisite for Play Integrity and managed app delivery |
| Up-to-date security providers | `Require` | L1 | MS-L1 | Android security provider patches required |
| Company Portal integrity | `Require` | L1 | MS-L1 | |
| Play Integrity — basic integrity | `Require` | L1 | MS-L1 | |
| Play Integrity — certified device | `Require` | L1 | MS-L1 | |
| Play Integrity evaluation type | `Hardware backed` | L2 | MS-L2 | See W-18 for BYOD device breadth risk |
| Min OS version | `13.0` | L1 | MS-L1 | |
| Min security patch level | `Not configured` | L1* | MS-L2 | Admin sets at deployment time |
| MDE threat level | `Low → Block` (separate policy) | — | MS-L2 | Via `AND - CP - DEV - Compliance - MDE - Personal Devices` |

---

### 9.5 Actions for Noncompliance

All 5 compliance policies share the same noncompliance action chain, verified in exported JSON:

| Action | Timing | Notes |
|---|---|---|
| Mark device as noncompliant | `Immediately` (0 hours) | Signals noncompliant state to Conditional Access immediately |
| Send push notification to user | `0 hours` (same moment) | Notifies user of noncompliant state with remediation guidance |
| Block access to corporate resources | `24 hours` | Gives user one day to remediate before access is cut off |

> **BYOD grace period recommendation:** Consider extending the block action to **7 days** for BYOD — personal device OS updates can take time and users may not act immediately. High-security environments can reduce to 0–3 days. Corporate devices: 24-hour grace is appropriate.

---

## 10. Conditional Access Policy Recommendations

Compliance policies alone have no enforcement power — they only signal a compliance state. **Conditional Access (CA) policies** consume that signal and determine whether to grant or block access to corporate resources. Without CA policies, a noncompliant Android device can still access Exchange Online, SharePoint, Teams, and all other Microsoft 365 workloads.

Configure Conditional Access policies in **Microsoft Entra ID > Security > Conditional Access**.

> **Prerequisite:** Microsoft Entra ID P1 license is required for all Conditional Access policies.

### 10.1 Corporate Device Policy — Require Compliant Device

Policy: `CA-User-Protection-AllUsers-AllApps-Android-Req-ComplianceDevice`

| Setting | Recommended Value |
|---|---|
| **Users** | All users (exclude break-glass accounts) |
| **Cloud Apps** | All cloud apps (or scope to Office 365 suite) |
| **Conditions — Device Platforms** | Android |
| **Conditions — Filter for Devices** | Include corporate-owned Android (manufacturer, MDM enrolled, Intune compliance) |
| **Grant** | Require device to be marked as compliant |
| **State** | Report-only first; enable after validating compliance rates |

> **Scope to Intune-managed devices:** This policy should use a device filter to target only Intune-enrolled devices. Unmanaged devices are addressed by the BYOD policy (Section 10.2).

### 10.2 BYOD Policy — Require App Protection Policy

Policy: `CA-User-Protection-AllUsers-O365-MobileApps-Android-Req-AppProtectionPolicy-BYOD`

| Setting | Recommended Value |
|---|---|
| **Users** | All users (exclude break-glass) |
| **Cloud Apps** | Office 365 (Exchange Online, SharePoint, Teams) |
| **Conditions — Device Platforms** | Android |
| **Conditions — Client Apps** | Mobile apps and desktop clients |
| **Conditions — Filter for Devices** | Exclude corporate-owned devices (to target personal BYOD only) |
| **Grant** | Require device to be marked as compliant **OR** Require App Protection Policy |
| **State** | Enable after BYOD compliance and MAM policies are broadly deployed |

> **"OR" logic for BYOD:** The OR between compliant device and App Protection Policy allows BYOD users to access Microsoft 365 apps (Outlook, Teams) via MAM even if they choose not to fully enroll in MDM — as long as the App Protection Policy is satisfied. This is the recommended balance for BYOD.

### 10.3 Enrollment Flow Exclusions

> **Critical:** Android device enrollment requires authentication before the device is compliant. If CA policies block unenrolled devices, new enrollments will fail.

Configure the following exclusions on all Android CA policies:

| Exclusion | Reason |
|---|---|
| `Microsoft Intune Enrollment` (service principal) | Allows enrollment handshake to complete before compliance check |
| `Microsoft Intune` (service principal) | Allows initial compliance evaluation and Company Portal communication |
| Break-glass accounts (2 minimum) | Emergency admin access; must have complex passwords and separate MFA |

---

## 11. Operational Risk Warnings

This section documents settings in the baseline policies that may cause user-facing disruptions, deployment failures, or unexpected device behavior. These are not CIS hardening changes — they are baseline settings and operational considerations that administrators must understand before rollout.

> **Before deploying any policy:** Review all items below and validate each against your organization's operational requirements and device use cases. Test with a pilot group before broad rollout.

---

### Risk Level: HIGH — Can Block Core Device Function or Cause Data Loss

---

#### W-1 — PIN Failure Automatic Device Wipe — Resolved in Fully Managed

| | Value |
| :--- | :--- |
| **JSON field** | `passwordSignInFailureCountBeforeFactoryReset: 10` (FM DC, updated from 11) |
| **Status** | **Resolved for Fully Managed.** FM DC policy has been updated to 10 failed attempts — aligned with all other corporate policies. |
| **Remaining risk** | 10 failed attempts still triggers a factory wipe on all device types. On shared kiosk devices, this is an operational risk — see W-19. |

---

#### W-2 — Wi-Fi Editing Blocked Without a Wi-Fi Profile Deployed

| | Value |
| :--- | :--- |
| **JSON fields** | `wifiBlockEditConfigurations: true` (COPE DC), `wifiblockeditpolicydefinedconfigurations_true` (SC FM/COPE) |
| **Risk** | COPE users and SC-managed FM devices cannot add or change Wi-Fi networks. If no Intune Wi-Fi configuration profile is assigned to the device group, devices have no Wi-Fi access outside the enrollment location. This will block all corporate app functionality. Note: FM DC has `wifiBlockEditConfigurations: false` — FM users can edit Wi-Fi, creating an inconsistency with COPE (see W-24). |

**Required action before deploying:**
1. Create a **Wi-Fi configuration profile**: **Devices** > **Configuration** > **+ Create** > **Android Enterprise** > **Fully managed, dedicated, and corporate-owned work profile** > **Wi-Fi**.
2. Configure the corporate SSID, security type, and credentials.
3. **Assign** this profile to the device group.
4. Deploy the Wi-Fi profile at the same time or before the device restriction policy.

---

#### W-3 — Bluetooth Configuration on Fully Managed — Accepted Exception

| | Value |
| :--- | :--- |
| **Policy** | SC Device Restrictions — Fully Managed; DC Device Restrictions — Fully Managed |
| **JSON fields** | `bluetoothBlockConfiguration: false` (DC FM); `bluetoothblockconfiguration_false` (SC FM) |
| **Status** | **Accepted Exception — intentionally NOT blocked.** Both the DC FM and SC FM policies are configured to allow users to manage Bluetooth settings. This is a deliberate decision: FM device users need to pair Bluetooth headsets for Teams calls, connect to car systems, and connect to home Wi-Fi on devices they take home. |
| **CIS note** | CIS Level 2 recommends restricting user Bluetooth configuration changes. This deviation is documented and accepted. |

**If your environment requires Bluetooth to be locked down:**
1. Go to **Devices** > **Configuration** > open the SC Device Restrictions — Fully Managed policy.
2. Click **Properties** > **Configuration settings** > **Edit** > find **Block Bluetooth configuration** > set to **Block**.
3. Update the DC FM policy to match (`bluetoothBlockConfiguration: true`).
4. Communicate to users before deploying — already-paired devices will continue to work but users cannot pair new peripherals.

---

#### W-4 — No Network Escape Hatch During Enrollment

| | Value |
| :--- | :--- |
| **Policy** | SC Device Restrictions — Fully Managed |
| **JSON field** | `networkescapehatchallowed_false` |
| **Risk** | During Android Enterprise enrollment, if the target Wi-Fi network is unavailable, devices cannot connect and enrollment will fail. There is no fallback option. This is a deployment blocker for large-scale rollouts or remote deployments where the corporate network is not guaranteed. |

**Recommendation:** Temporarily set **Network escape hatch** to **Allow** during the initial deployment phase. After all devices are enrolled and Wi-Fi profiles are confirmed working, set it back to **Block**.

---

#### W-5 — Manual Certificate Installation Disabled — SCEP/PKCS Profiles Required

| | Value |
| :--- | :--- |
| **Policies** | DC Device Restrictions — Fully Managed; DC Device Restrictions — Corporate Work Profile |
| **JSON field** | `certificateCredentialConfigurationDisabled: true` |
| **Risk** | Users cannot manually install certificates. Any service requiring client certificate authentication — S/MIME email signing, VPN with certificate auth, internal portals with mutual TLS — will fail silently. Users will see authentication errors with no way to resolve them. |

**Required action before deploying:**
- If your environment uses certificates: Deploy a **SCEP** or **PKCS** certificate profile via Intune **before** or **at the same time** as the device restriction policy.
- Navigate to: **Devices** > **Configuration** > **+ Create** > **Android Enterprise** > **Trusted certificate** or **SCEP certificate**.

---

#### W-6 — Account Modification Blocked — Work Account Must Be Auto-Provisioned at Enrollment

| | Value |
| :--- | :--- |
| **Policy** | DC + SC Device Restrictions — Fully Managed |
| **JSON field** | `accountsBlockModification: true` |
| **Risk** | After enrollment, users cannot add or remove accounts. If the Microsoft 365/Entra ID work account is not automatically configured during enrollment, apps like Outlook, Teams, and OneDrive will ask the user to sign in — but the user will not be able to add the account. The device will appear functional but all productivity apps will be unusable. |

**Required action:** Confirm that the enrollment flow provisions the work account automatically. Validate this end-to-end in a pilot before broad rollout.

---

#### W-18 — Hardware-Backed Attestation Rollout Risk

| | Value |
| :--- | :--- |
| **Policies** | All 5 compliance policies |
| **JSON field** | `securityRequiredAndroidSafetyNetEvaluationType: "hardwareBacked"` |
| **Risk** | Hardware-backed attestation requires a Trusted Execution Environment (TEE) in device hardware. Devices manufactured before Android 8.0, budget Android models, and emulators will fail this check immediately, becoming non-compliant with no explanation to the user. After the 24-hour grace period, access to Exchange, SharePoint, and Teams is silently cut off. This risk is particularly acute for Kiosk and Shared dedicated device policies, which may target fixed hardware that is several years old. |

**Recommendation:** Before enforcing broadly, audit your device inventory for TEE support using Intune compliance reports filtered to `securityRequiredAndroidSafetyNetEvaluationType` failures. Add a push notification compliance action (see W-22) so users receive a warning before the block fires. Scope to a pilot group first; broaden only after confirming non-compliant devices are remediated or excluded.

---

### Risk Level: MEDIUM — Can Frustrate Users or Create Hidden Security Gaps

---

#### W-7 — Work Profile App Permission Policy (BYOD) — Resolved in Baseline

| | Value |
| :--- | :--- |
| **Policy** | DC Device Restrictions — Personally-Owned Work Profile |
| **JSON field** | `workProfileDefaultAppPermissionPolicy: "prompt"` |
| **Status** | **Resolved.** Work profile app permission policy is set to **Prompt** — users are asked before each permission is granted. No action required. |

---

#### W-8 — Managed Google Play AllowList: Missing Apps Means Users Are Blocked

| | Value |
| :--- | :--- |
| **Policy** | DC Device Restrictions — Fully Managed |
| **JSON field** | `playStoreMode: "allowList"` |
| **Risk** | Only apps explicitly approved and published in Managed Google Play can be installed. If a required line-of-business app, tool, or system utility is not added to MGP before deployment, users cannot install it themselves — the Play Store will not show unapproved apps. |

**Required action before deploying:**
1. Go to **Apps** > **Android** > **Managed Google Play**.
2. Approve all apps required for the deployment (LOB apps, Microsoft 365 apps, Authenticator, utilities).
3. Assign all required apps as **Required** to the device group.
4. Verify the app list is complete before devices leave IT hands.

---

#### W-9 — Apps Always Auto-Update — LOB App Updates Are Not Controlled

| | Value |
| :--- | :--- |
| **Policies** | Fully Managed DC + Corporate Work Profile DC |
| **JSON field** | `appsAutoUpdatePolicy: "always"` |
| **Risk** | Apps update silently at any time, including during active work sessions. A major update to a line-of-business app can change the UI, break workflows, or introduce bugs — without any IT control or rollback option. |
| **Recommended** | Change to `wifiOnly` (limits updates to Wi-Fi connections, reduces disruption during shifts) or `postponed` (delays updates 90 days, giving IT time to test before rollout). |

**Steps to change:**
1. Go to **Devices** > **Configuration** > select the policy.
2. Click **Properties** > **Configuration settings** > **Edit**.
3. Locate **App auto-update policy**.
4. Change from **Always** to **Wi-Fi only** or **Postponed**.
5. Click **Review + save**.

---

#### W-10 — 1-Minute Screen Timeout Is Highly Disruptive for Most Work Scenarios

| | Value |
| :--- | :--- |
| **Policies** | DC Device Restrictions — Fully Managed, Corporate-Owned Work Profile, Single-App-Kiosk |
| **JSON field** | `passwordMinutesOfInactivityBeforeScreenTimeout: 1` |
| **Risk** | A 1-minute screen timeout is extremely aggressive for productivity workers. Any natural pause — reading a document, listening to a Teams call, pausing mid-form — locks the screen. On kiosk devices used at retail counters or assembly lines, the screen constantly locks mid-transaction. Workers must re-authenticate multiple times per hour, creating friction and interrupting active workflows. On shared kiosk devices where the PIN is entered in public, frequent relocking increases shoulder-surfing risk. Note: `passwordRequireUnlock: "daily"` means users only enter their PIN once a day — but the screen-off frequency itself will cause significant user frustration in most work roles. |
| **Recommended** | Increase to **2–3 minutes** for FM and COPE devices, and **5 minutes** for Single-App Kiosk devices. The compliance policy lock timer (5 minutes for corporate) provides the security backstop. Dropping from 1 minute to 2–3 minutes does not violate any CIS L1 or L2 requirement. |

---

#### W-11 — Unified Device and Work Profile Password Allowed (BYOD)

| | Value |
| :--- | :--- |
| **Policy** | DC Device Restrictions — Personally-Owned Work Profile |
| **JSON field** | `blockUnifiedPasswordForWorkProfile: false` |
| **Risk** | Users are allowed to use the same PIN/password for both the device unlock and the work profile unlock. A single weak password covers both personal and corporate data. CIS recommends requiring separate passwords. |

**Steps to fix:**
1. Go to **Devices** > **Configuration** > open the DC Device Restrictions — Personally-Owned Work Profile policy.
2. Click **Properties** > **Configuration settings** > **Edit**.
3. Locate **Block using unified password for work profile**.
4. Set to **Block** (requires separate passwords).
5. Click **Review + save**.

---

#### W-12 — DC Template and Settings Catalog Both Assigned to the Same Device Group

| | |
| :--- | :--- |
| **Policies** | DC Device Restrictions — Fully Managed AND SC Device Restrictions — Fully Managed |
| **Risk** | When two device restriction policies with conflicting values apply to the same device, Intune enforces the **most restrictive** value for each setting. This can produce unexpected behavior that is difficult to troubleshoot. The DC/SC Bluetooth configuration inconsistency was resolved (see W-3), but review all overlapping settings before deployment. |

**Recommended action:**
- Choose **one** template type (DC or SC) as the primary policy for each enrollment type.
- Remove the other, or convert it to documentation-only.
- If both must remain, explicitly document each conflict and verify the most-restrictive outcome is the intended one.

---

#### W-19 — Factory Wipe After 10 Failed Attempts on Shared/Kiosk Devices

| | Value |
| :--- | :--- |
| **Policies** | DC — Multi-App-Kiosk, Shared, Single-App-Kiosk |
| **JSON field** | `passwordSignInFailureCountBeforeFactoryReset: 10` |
| **Risk** | On shared kiosk devices, multiple shift workers use the same PIN. If a new worker is unfamiliar with the current PIN (or if the PIN was recently changed), 10 incorrect attempts triggers a factory wipe — deleting all local data and unenrolling the device from Intune. On a production floor or retail environment with no on-site IT staff, this takes the device offline until a technician re-enrolls it. Workers under time pressure may also enter the wrong PIN rapidly, approaching the threshold inadvertently. This risk compounds with the daily PIN re-authentication requirement (W-21), which fires at shift start — exactly when a new worker may not know the PIN. |

**Recommendation:** Consider increasing the threshold to 15 attempts for dedicated kiosk devices. Populate the `factoryResetDeviceAdministratorEmails` field on all three kiosk DC policies (currently empty on all three) so only an authorized admin account can configure the device after a wipe. Configure `deviceOwnerLockScreenMessage` on kiosk devices to display a PIN reminder or IT support contact number.

---

#### W-20 — Managed Browser Requirement May Break Link Opening on BYOD

| | Value |
| :--- | :--- |
| **Policy** | MAM — BYOD |
| **JSON fields** | `managedBrowserToOpenLinksRequired: true`, `managedBrowser: microsoftEdge` |
| **Risk** | All hyperlinks clicked inside MAM-protected apps (Outlook, Teams, SharePoint) on BYOD devices will only open in Microsoft Edge. If the user has not installed Edge on their personal device, links silently fail or redirect to the Company Portal app for installation — which most users find confusing. Users accustomed to Chrome or Firefox discover their default browser is bypassed for all corporate app links. This is particularly disruptive for meeting invite URLs in calendar items and document links shared in Teams. |

**Recommendation:** Ensure **Microsoft Edge is pushed as a required app** through an Intune App Configuration Policy scoped to BYOD users before this policy is activated, so Edge is pre-installed. Verify Edge is listed as a recommended app for the MAM policy assignment group.

---

#### W-21 — Daily Mandatory PIN Re-Authentication on Corporate Devices

| | Value |
| :--- | :--- |
| **Policies** | All corporate DC policies (FM, COPE, Kiosk variants) |
| **JSON field** | `passwordRequireUnlock: "daily"` |
| **Risk** | This setting requires full PIN/password authentication at least once every 24 hours, regardless of biometric usage. On kiosk devices in 24/7 operations (retail stores, warehouses, hospitals), the forced re-auth can trigger at the start of a night shift when an unfamiliar worker picks up the device. If the worker doesn't know the PIN, the device is effectively locked until someone with the PIN is found. This risk compounds with W-19: a worker who guesses the PIN 10 times triggers a factory wipe of an operational device. |

**Recommendation:** This is a CIS-aligned control — retain it. Mitigate the impact operationally: (1) document the device PIN in a secure vault accessible to shift supervisors, (2) configure `deviceOwnerLockScreenMessage` on kiosk devices with "Call IT support at [number] if locked out," (3) ensure all assigned workers complete PIN training before first use.

---

#### W-22 — Compliance Push Notification — Resolved: All Policies Updated

| | Value |
| :--- | :--- |
| **Policies** | All 5 compliance policies |
| **Status** | **Resolved.** All 5 compliance policies now have both a push notification action (fires immediately on detection) and a block action (24-hour grace period). Users receive a prompt warning before access is cut off. |
| **Remaining operational impact** | Users must have the Company Portal app installed and notifications enabled to receive the alert. On Kiosk and Shared dedicated devices, push notifications may not be visible if the notification bar is restricted by the Kiosk Customization profile. Ensure the `kiosk customization system error warnings` setting is enabled (it is in the baseline) so system-level alerts remain accessible. |

---

#### W-23 — MAM Data Ingestion Restriction Blocks Gallery Photo Attachments

| | Value |
| :--- | :--- |
| **Policy** | MAM — BYOD |
| **JSON fields** | `blockDataIngestionIntoOrganizationDocuments: true`, `allowedDataIngestionLocations: [oneDriveForBusiness, sharePoint, camera]` |
| **Risk** | BYOD users can take a live photo with the camera and attach it to a Teams message or Outlook email (camera is in the allowed list). However, they cannot attach photos already saved in their gallery — gallery images come from local storage, which is not in the allowed list. Users who save an image to their gallery and then try to attach it via Outlook will find the file picker empty or the attachment silently fails. This breaks a common field workflow (photographing equipment, whiteboards, documents) where the photo is taken before opening the managed app. |

**Recommendation:** If gallery photo attachment is a legitimate business workflow, add `localStorage` to `allowedDataIngestionLocations`. The existing outbound restriction (`allowedOutboundDataTransferDestinations: managedApps`) still prevents data from being shared from managed apps to personal apps, so adding gallery ingestion does not create a data exfiltration path. Evaluate against your data classification policy.

---

#### W-24 — COPE vs. FM Device Configuration Inconsistencies

| | Value |
| :--- | :--- |
| **Policies** | DC — Corporate-Owned Work Profile (COPE) vs. DC — Fully Managed (FM) |

The following settings differ between the two policies without documented justification:

| Setting | FM DC Value | COPE DC Value | Risk |
| :--- | :--- | :--- | :--- |
| `wifiBlockEditConfigurations` | `false` (users can edit Wi-Fi) | `false` (same) | Same value — not a gap |
| `usersBlockRemove` | `true` | `false` | COPE users can remove accounts, potentially initiating de-enrollment from a corporate-owned device |
| `passwordBlockKeyguardFeatures` | Full set (camera, remoteInput, trustAgents, unredactedNotifications) | trustAgents only | COPE lock screen has weaker keyguard protection than FM |
| `factoryResetBlocked` | `true` | `true` | Both correctly set |
| `bluetoothBlockContactSharing` | `false` (not blocked on FM) | `true` (blocked on COPE) | Intentional difference — COPE has stricter Bluetooth contact policy |

**Recommendation:** Set `usersBlockRemove=true` on the COPE DC policy to align with FM and prevent users from de-enrolling corporate devices. Add `camera`, `remoteInput`, `unredactedNotifications` to `passwordBlockKeyguardFeatures` on the COPE DC policy to match FM keyguard coverage.

---

### Risk Level: LOW — Communicate to Users and Helpdesk Before Deployment

| # | Setting | Policy | Impact | Action |
| :--- | :--- | :--- | :--- | :--- |
| W-13 | `dataRoamingBlocked` | COPE DC + SC; Dedicated DC | No mobile data internationally — Wi-Fi only on COPE and Dedicated devices. **FM is intentionally NOT blocked** — FM users travel and take devices home. | Communicate COPE/Ded users before international travel |
| W-14 | `storageBlockExternalMedia: true` | FM DC + SC | SD cards and USB OTG storage blocked | Communicate; ensure cloud storage (OneDrive) is deployed |
| W-15 | System update window: 00:00–06:00 only | FM DC | Updates install only if device is on and plugged in overnight | Verify devices remain powered on; extend window if needed |
| W-16 | `factoryResetBlocked: true` + `passwordSignInFailureCountBeforeFactoryReset: 10` | FM DC | Users cannot manually factory reset — but the device CAN still be auto-wiped by 10 failed PIN attempts. These two settings are not the same. | Train helpdesk on this distinction |
| W-17 | `googleAccountsBlocked: true` | FM DC | Personal Google services (Maps, Pay, Gmail) not available | Communicate to users before deployment |
| W-25 | `securityCommonCriteriaModeEnabled: true` | SC — Fully Managed; SC — Corporate-Owned Work Profile | **Samsung Knox-specific.** On non-Samsung devices (Google Pixel, Motorola, etc.), this setting is silently ignored or may produce undefined behavior. On Samsung devices it disables fingerprint enrollment/unenrollment during sessions and may affect some accessibility features and camera behavior on specific firmware versions. | If the FM/COPE fleet includes non-Samsung devices, scope this setting to a Samsung-only **Assignment Filter** (`deviceManufacturer equals Samsung`). Document the hardware dependency for future procurement. |

---

## 12. Compliance Findings & Gap Tracker

> Consolidated findings from the CIS compliance analysis performed 2026-05-28 against all exported baseline JSON files. All statuses reflect the current state of baseline v1.3.

**Status legend:**
- **Fixed** — change applied to JSON files
- **Accepted Exception** — intentional deviation with documented reason
- **Open** — pending fix or Intune verification
- **Informational** — documentation or SC-overrides-DC note only

---

### Part A — SETTINGSOUTPUT vs JSON Discrepancies

| ID | Severity | Finding | Status | Resolution |
| :--- | :---: | :--- | :---: | :--- |
| DISC-01 | Critical | Screen timeout: DC FM JSON = 5 min vs documented 1 min | Accepted Exception | **Intentional.** 5-minute screen timeout is kept for user productivity. CIS L1 recommends ≤ 2 min — acknowledged gap. SETTINGSOUTPUT.md updated to show 5 min. |
| DISC-02 | Medium | Bluetooth configuration: DC FM and SC FM both `false` (NOT blocked) vs documented "Blocked" | Accepted Exception | **Intentional.** Users need to pair Bluetooth headsets, car systems, and connect to home Wi-Fi. Both DC FM and SC FM now correctly documented as NOT blocked. See W-3. |
| DISC-03 | Low | Apps auto-update: DC FM JSON `notConfigured` — SC FM correctly enforces `always` | Fixed | User corrected DC FM in Intune (`appsAutoUpdatePolicy: always`). DC FM now matches SC FM. Documentation updated. |
| DISC-04 | Critical | Play Store mode: DC FM JSON `notConfigured` — not AllowList | Fixed | User corrected DC FM in Intune (`playStoreMode: allowList`). Both DC and SC FM now enforce AllowList. |
| DISC-05 | Low | Password require unlock: DC FM JSON `deviceDefault` — SC FM correctly enforces `daily` | Fixed | User corrected DC FM in Intune (`passwordRequireUnlock: daily`). DC FM now matches SC FM. |
| DISC-06 | Low | Network escape hatch: DC FM JSON `true` (allowed) — SC FM correctly disables it | Fixed | User corrected DC FM in Intune (`networkEscapeHatchAllowed: false`). DC FM now matches SC FM. |
| DISC-07 | High | SafetyNet Basic Integrity: `null` on 4 corporate compliance policies — `certifiedDevice` set but `basicIntegrity` not explicit | Fixed | `securityRequireSafetyNetAttestationBasicIntegrity: true` set in all 4 corporate compliance policy JSON files. |
| DISC-08 | Medium | BYOD compliance: `workProfilePreviousPasswordBlockCount` is `null` — documented as 5 | Open | Verify in Intune portal and set `workProfilePreviousPasswordBlockCount: 5` if not already configured. |
| DISC-09 | Medium | COWP cross-profile work contacts: DC COWP and SC COWP were inconsistent | Fixed | Both DC COWP and SC COWP now set to `true` (work contacts visible in personal dialler). Intentional — better user experience. |
| DISC-10 | Low | BYOD compliance: `workProfilePasswordExpirationInDays` is `null` | Open | Verify in Intune portal whether work profile expiration inherits from device-level setting or requires its own value. |

---

### Part B — CIS Benchmark Gap Summary

| ID | CIS Control | Level | Status | Resolution |
| :--- | :--- | :---: | :---: | :--- |
| CIS-01 | Screen lock timeout ≤ 2 minutes | L1 | Accepted Exception | **Intentional — 5 min.** CIS requires ≤ 2 min. Current setting is 5 min for FM/COPE/Dedicated compliance policies, 15 min for BYOD. Kept for user productivity. |
| CIS-02 | Minimum security patch level enforced | L1/L2 | Accepted Exception | **Intentional — not configured.** Admin configures `minAndroidSecurityPatchLevel` at deployment time based on the current Android version in the environment. Quarterly review recommended. |
| CIS-03 | Mobile Threat Defense enforced via compliance | L1 | Fixed | MDE threat protection enabled via dedicated compliance policies: `AND - CP - DEV - Compliance - MDE - Corporate Devices` and `AND - CP - DEV - Compliance - MDE - Personal Devices`. Full enforcement chain: MDE detects → non-compliant → CA blocks access. |
| CIS-04 | Play Store AllowList mode on FM | L1 | Fixed | `playStoreMode: allowList` now set in DC FM JSON. Only Managed Google Play-approved apps can be installed on Fully Managed devices. |
| CIS-05 | SafetyNet / Play Integrity — explicit basic integrity | L1 | Fixed | `securityRequireSafetyNetAttestationBasicIntegrity: true` explicitly set in all 4 corporate compliance policies. Hardware-backed evaluation type (`hardwareBacked`) was already correct. |

---

### Part C — Cross-Policy Consistency Findings

| ID | Finding | Status | Resolution |
| :--- | :--- | :---: | :--- |
| CONS-01 | DC FM vs SC FM overlap — multiple settings conflicting | Resolved | DC FM corrected in Intune (play store, auto-update, password unlock, escape hatch). SC FM data roaming and appsDefaultPermission aligned to DC FM intent. Both policies now complementary. |
| CONS-02 | COWP DC vs SC work contacts inconsistency | Fixed | DC COWP `crossProfilePoliciesShowWorkContactsInPersonalProfile: true` and SC COWP confirmed `true`. Both consistent — intentional for UX. |
| CONS-03 | BYOD compliance work profile settings partially null | Open | `workProfilePreviousPasswordBlockCount: null` and `workProfilePasswordExpirationInDays: null` — see DISC-08 and DISC-10. Verify in Intune portal. |

---

### Part D — Open Items Summary

The following items require admin action or Intune portal verification:

| ID | Action Required | Priority |
| :--- | :--- | :---: |
| DISC-08 | Set `workProfilePreviousPasswordBlockCount: 5` in BYOD compliance policy if not already configured in Intune | Medium |
| DISC-10 | Verify `workProfilePasswordExpirationInDays` in Intune portal for BYOD compliance policy | Low |
| CONS-03 | Verify all null compliance values for BYOD work profile are intentional or fix in Intune | Low |
| W-24 | Set `usersBlockRemove: true` and expand `passwordBlockKeyguardFeatures` on DC COPE to match FM | Medium |
| W-11 | Consider enabling `blockUnifiedPasswordForWorkProfile: true` on DC BYOD to require separate work/personal passwords | Low |
| SC FM pending | Verify in Intune SC UI: `googleAccountsBlocked`, `passwordBlockKeyguardFeatures` (camera, remoteInput, trustAgents, unredactedNotifications), `playStoreMode`, `usersBlockAdd`/`usersBlockRemove` | Medium |
| SC COWP pending | Verify in Intune SC UI: `playStoreMode`, `passwordBlockKeyguardFeatures` (trustAgents), `locateDeviceLostModeEnabled`, system update window | Medium |

---

### Part E — Verified Compliant Controls (CIS)

The following CIS Level 1 and Level 2 controls are correctly implemented across the baseline and require no changes:

| CIS Control | Setting | Policy / Location |
| :--- | :--- | :--- |
| Password complexity | `numericComplex` | All corporate compliance + DC/SC policies |
| Minimum password length ≥ 6 | `passwordMinimumLength: 6` | All compliance and DC/SC policies |
| Password expiration ≤ 365 days | `passwordExpirationDays: 365` | All compliance and DC/SC policies |
| Password history ≥ 5 | `passwordPreviousPasswordCountToBlock: 5` | All corporate compliance |
| Max sign-in failures ≤ 10 | `passwordSignInFailureCountBeforeFactoryReset: 10` | DC FM, DC Kiosk/Shared, DC BYOD |
| Screen capture blocked | `screenCaptureBlocked: true` | DC FM, DC COWP, DC Kiosk, DC Shared, SC FM, SC COWP |
| USB file transfer blocked | `storageBlockUsbFileTransfer: true` / `usbDataAccess: disallowUsbDataTransfer` | DC FM; SC FM, SC COWP |
| External storage blocked | `storageBlockExternalMedia: true` | DC FM, DC Kiosk, DC Shared; SC FM, SC COWP |
| NFC outgoing beam blocked | `nfcBlockOutgoingBeam: true` | DC FM; SC FM, SC COWP |
| Factory reset blocked | `factoryResetBlocked: true` | DC FM, DC COWP, DC Kiosk, DC Shared; SC FM, SC COWP |
| Account modification blocked | `accountsBlockModification: true` | DC FM, DC COWP, DC Kiosk, DC Shared; SC FM, SC COWP |
| Block certificate installation | `certificateCredentialConfigurationDisabled: true` | DC FM, DC COWP; SC FM, SC COWP |
| Block unknown sources | `appsAllowInstallFromUnknownSources: false` | SC FM, SC COWP |
| Block developer settings | `securityDeveloperSettingsEnabled: false` | SC FM, SC COWP |
| Block Wi-Fi Direct | `wifiDirectSettings: disallowed` | SC FM, SC COWP |
| Block Wi-Fi tethering | `cellularBlockWiFiTethering: true` | SC FM, SC COWP; DC Kiosk, DC Shared |
| Block date/time changes | `dateTimeConfigurationBlocked: true` | SC FM, SC COWP; DC Kiosk, DC Shared |
| System updates windowed | `systemUpdateInstallType: windowed` (00:00–06:00) | DC FM, DC Kiosk, DC Shared |
| Locate device (Lost Mode) | `locateDeviceLostModeEnabled: true` | DC FM, DC COWP |
| Min OS version 13.0 | `osMinimumVersion: 13.0` | All 5 compliance policies |
| Hardware-backed attestation | `securityRequiredAndroidSafetyNetEvaluationType: hardwareBacked` | All 5 compliance policies |
| SafetyNet basic integrity | `securityRequireSafetyNetAttestationBasicIntegrity: true` | All 5 compliance policies |
| SafetyNet certified device | `securityRequireSafetyNetAttestationCertifiedDevice: true` | All 5 compliance policies |
| Storage encryption | `storageRequireEncryption: true` | All 5 compliance policies |
| Block rooted/jailbroken devices | `securityBlockJailbrokenDevices: true` | All 5 compliance policies |
| Intune app integrity | `securityRequireIntuneAppIntegrity: true` | Corporate compliance policies |
| Noncompliance grace period | 24-hour grace, then block + push notification | All 5 compliance policies |
| Private space blocked | `privatespacepolicy_disallowed` | SC COWP |
| Cross-profile copy/paste blocked | `crossProfilePoliciesAllowCopyPaste: false` | DC COWP; SC FM, SC COWP |
| Cross-profile data sharing blocked | Work → Personal blocked | DC COWP; SC FM, SC COWP |
| Security Common Criteria Mode | `securityCommonCriteriaModeEnabled: true` | SC FM, SC COWP (Samsung Knox only — see W-25) |
| MDE threat protection | `deviceThreatProtectionEnabled: true`, `advancedThreatProtectionRequiredSecurityLevel: low` | AND - CP - DEV - Compliance - MDE - Corporate Devices; AND - CP - DEV - Compliance - MDE - Personal Devices |
| Conditional Access — managed devices | Require compliant device | CA-User-Protection-AllUsers-AllApps-Android-Req-ComplianceDevice |
| Conditional Access — MAM BYOD | Require App Protection Policy | CA-User-Protection-AllUsers-O365-MobileApps-Android-Req-AppProtectionPolicy-BYOD |
| MAM PIN (6-char, no simple PIN) | `minimumPinLength: 6`, `simplePinBlocked: true` | MAM policy |
| MAM data backup blocked | `dataBackupBlocked: true` | MAM policy |
| MAM outbound data transfer | Managed apps only | MAM policy |
| MAM screen capture blocked | `screenCaptureBlocked: true` | MAM policy |
| MAM offline wipe | 90 days | MAM policy |
| MDE low-touch onboarding | `EnableLowTouchOnboarding: 1` | MDE App Configuration Policy |
| MDE network protection | `DefenderNetworkProtectionEnable: 1` | MDE App Configuration Policy |

---

## References

- [CIS Google Android Benchmark v1.6.0](https://www.cisecurity.org/benchmark/google_android)
- [Android Enterprise security configuration framework (Microsoft)](https://learn.microsoft.com/en-us/mem/intune/enrollment/android-configuration-framework)
- [Android Enterprise compliance settings in Microsoft Intune](https://learn.microsoft.com/en-us/intune/intune-service/protect/compliance-policy-create-android-for-work)
- [Android app protection policy settings – Microsoft Intune](https://learn.microsoft.com/en-us/intune/intune-service/apps/app-protection-policy-settings-android)
- [Support tip: Changes to Google Play strong integrity for Android 13+](https://techcommunity.microsoft.com/blog/intunecustomersuccess/support-tip-changes-to-google-play-strong-integrity-for-android-13-or-above/4176975)
- [Microsoft Defender for Endpoint on Android – Intune deployment](https://learn.microsoft.com/en-us/defender-endpoint/android-intune)

---

*This document is part of the UniFy Endpoint – Microsoft Intune Android Enterprise Baseline series. Document maintained by Yoennis Olmo - Sr. Modern Work Consultant.*
