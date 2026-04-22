# Managing Android Devices with Microsoft Intune

## Android Enterprise Baseline – CIS Compliance Hardening Guide

### Overview

This guide documents the specific Intune configuration changes needed to bring the Android Enterprise Baseline into closer alignment with the [Android CIS Benchmarks](https://www.cisecurity.org/benchmark/google_android) and Microsoft's [Android Enterprise security configuration framework](https://learn.microsoft.com/en-us/mem/intune/enrollment/android-configuration-framework).

Each change below includes:
- The **current baseline value** (from the imported JSON policy files).
- The **recommended CIS-aligned value**.
- The **exact steps** to make the change in the Microsoft Intune admin center.
- The **security framework level** it applies to.

This guide also includes a dedicated section — **Settings NOT Included in the Baseline** — documenting CIS recommendations that are intentionally excluded because they require infrastructure prerequisites (e.g., MDE deployment) or operational decisions. Administrators must evaluate and configure these manually if applicable.

> **Important:** This is a reference guide — not a one-size-fits-all checklist. Assess each recommendation against your organization's operational requirements, risk tolerance, and the specific deployment model before making changes. Always test changes with a pilot group before broad rollout.

> **Prerequisite for threat protection settings:** Settings under "Device Threat Protection" and "Max allowed device threat level" only take effect if Microsoft Defender for Endpoint (or another Mobile Threat Defense partner) is deployed and the MDE–Intune connector is active. See the [Defender for Endpoint for Android Devices guide](11_Defender%20for%20Endpoint%20for%20Android%20Devices.md) before enabling these settings.

> **Read Section 6 before deploying:** Section 6 documents settings already present in the baseline JSON policies that can block users, cause deployment failures unexpectedly — independent of the CIS hardening changes. Review it before any production rollout.

> **Policy names:** This guide uses descriptive labels (e.g., "SC Device Restrictions — Fully Managed") in place of specific policy names. Refer to [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the exact current name of each policy.

---

### Microsoft Security Configuration Framework — Level Reference

| Level | Name | Applies To |
| :--- | :--- | :--- |
| **Level 1** | Basic Enterprise Security | Personally Owned Work Profile (BYOD) |
| **Level 2** | Enhanced Enterprise Security | Corporate-Owned Work Profile, Fully Managed |
| **Level 3** | High Enterprise Security | Fully Managed — high-sensitivity environments |

---


## Hardening Summary Table

The following table provides a consolidated view of all recommended changes and their current baseline state.

| # | Policy | Setting | Current Baseline | Recommended | Level |
| :---: | :--- | :--- | :--- | :--- | :---: |
| 1.1.1 | Corporate Compliance (all) | Device threat protection | Disabled | Enabled, Low | L2 |
| 1.1.2 | Corporate Compliance (all) | SafetyNet evaluation type | Basic | Hardware-backed | L2 |
| 1.1.3 | Corporate Compliance (all) | Min security patch level | Not configured | ≤ 12 months | L2 |
| 1.1.4 | Corporate Compliance (all) | Screen lock inactivity | 15 min | 5 min | L2 |
| 1.2.1 | BYOD Compliance | Min security patch level | Not configured | ≤ 12 months | L1 |
| 1.2.2 | BYOD Compliance | Screen lock inactivity | 15 min | 15 min | L1 |
| 1.2.3 | BYOD Compliance | Require verify apps | Disabled | Enabled | L1 |
| 1.2.4 | BYOD Compliance | Certified device attestation | Disabled | Enabled | L2 |
| 1.2.5 | BYOD Compliance | Device threat protection | Disabled | Enabled, Low | L2 |
| 2.1.1 | Fully Managed DC template | Camera | Allowed | Block | L2 |
| 2.1.2 | Fully Managed SC | Apps default permission |  Prompt | Prompt | L2 |
| 2.3.1 | BYOD SC | Cross-profile data sharing |  Work to personal blocked | Work to personal blocked | L1 |
| 3.1 | MAM App Protection | Max device threat level | Not configured | Low → Block | L1 |
| 3.2 | MAM App Protection | Simple PIN | Block | Block | L1 |
| 3.3 | MAM App Protection | Device lock required | Not required (block via conditional launch) | Require | L1 |
| 3.4 | MAM App Protection | PIN reset period | 90 day | 90 days | L1 |
| 3.5 | MAM App Protection | Previous PIN block count | 5 | 5 | L1 |
| 3.6 | MAM App Protection | Min required patch version | Not configured | ≤ 12 months | L1 |
| 3.7 | MAM App Protection | SafetyNet evaluation type | Basic | Hardware-backed | L2 |

---

## What Is Already Well Configured

The following settings are already aligned with CIS recommendations and do not require changes:

| Setting | Value | Notes |
| :--- | :--- | :--- |
| Storage encryption required | Yes | All compliance policies ✓ |
| Block jailbroken / rooted devices | Yes | All compliance policies ✓ |
| Minimum OS version | 13.0 | All policies ✓ |
| Password type (corporate) | numericComplex | Corporate compliance and SC/DC policies ✓ |
| Password type (BYOD compliance) | Device default, Medium complexity | BYOD compliance uses complexity level instead of explicit type ✓ |
| Password minimum length (corporate) | 6 characters | Corporate compliance and DC/SC policies ✓ |
| Password minimum length (BYOD compliance) | Not configured (complexity-based) | BYOD compliance defers to complexity setting ✓ |
| Password expiration | 365 days | All compliance and DC/SC policies ✓ |
| Previous password block count | 5 | All policies ✓ |
| Factory reset blocked | Yes | DC + SC device restriction policies ✓ |
| USB file transfer blocked | Yes | SC device restriction policies ✓ |
| Screen capture blocked (corporate) | Yes | SC + DC corporate device restriction policies ✓ |
| Screen capture blocked (BYOD work profile) | Yes | DC BYOD policy ✓ |
| Work profile cross-profile copy/paste blocked | Yes | DC BYOD policy ✓ |
| Work profile add accounts blocked | Yes | DC BYOD policy ✓ |
| Work profile app permission policy | Prompt | DC BYOD policy ✓ |
| Work profile verify apps | Yes | DC BYOD policy ✓ |
| Work profile cross-profile data sharing | Work → Personal blocked | DC BYOD policy ✓ |
| Play Store mode | allowList | Corporate DC device restrictions ✓ |
| Developer settings disabled | Yes | Settings Catalog policies ✓ |
| Network escape hatch disabled | Yes | Settings Catalog policies ✓ |
| Private Space disallowed | Yes | Settings Catalog FM policy ✓ |
| WiFi Direct disallowed | Yes | Settings Catalog policies ✓ |
| Bluetooth contact sharing blocked | Yes | DC + SC device restrictions ✓ |
| NFC outgoing beam blocked | Yes | Settings Catalog policies ✓ |
| Common Criteria Mode enabled | Yes | SC FM policy ✓ |
| MAM Simple PIN | Blocked | MAM policy ✓ |
| MAM SafetyNet attestation type | basicIntegrityAndDeviceCertification | Already at enhanced level ✓ |
| MAM SafetyNet evaluation type | Basic | MAM policy ✓ |
| MAM Class 3 biometrics required | Yes | MAM policy ✓ |
| MAM PIN after biometric change | Yes | MAM policy ✓ |
| MAM data backup blocked | Yes | MAM policy ✓ |
| MAM outbound data transfer | Managed apps only | MAM policy ✓ |
| MAM screen capture blocked | Yes | MAM policy ✓ |
| MAM offline wipe period | 90 days | MAM policy ✓ |
| MAM PIN minimum length | 6 | MAM policy ✓ |
| MAM previous PIN block count | 5 | MAM policy ✓ |
| MAM PIN reset period | 90 days | MAM policy ✓ |
| MAM device passcode complexity | Low minimum (block if not met) | MAM conditional launch ✓ |
| MDE low-touch onboarding | Enabled | MDE ACP ✓ |
| MDE network protection | Enabled | MDE ACP ✓ |
| MDE auto-remediation | Enabled | MDE ACP ✓ |

---

## Operational Risk Warnings — Settings That Can Break Device Functionality

This section documents settings already present in the baseline JSON policies that may cause user-facing disruptions, deployment failures, or unexpected device behavior if not reviewed before rollout. These are not CIS hardening changes — they are baseline settings that administrators must understand and adapt to their environment.

> **Before deploying any policy:** Review the items below and validate each one against your organization's operational requirements and device use cases. Test with a pilot group before broad rollout.

---

### Risk Level: HIGH — Can Block Core Device Function or Cause Data Loss

---

#### W-1 — 11 PIN Failures Trigger Automatic Device Wipe (Fully Managed)

| | Value |
| :--- | :--- |
| **JSON field** | `passwordSignInFailureCountBeforeFactoryReset: 11` |
| **Risk** | 11 wrong PIN entries automatically wipe the device. |
| **Recommended** | Keep this value between **10–11** to align with the other policies and reduce accidental wipe risk. |

---

#### W-2 — Wi-Fi Editing Blocked Without a Wi-Fi Profile Deployed

| | Value |
| :--- | :--- |
| **JSON fields** | `wifiBlockEditConfigurations: true`, `wifiblockeditpolicydefinedconfigurations_true` |
| **Risk** | Users cannot add or change Wi-Fi networks. If no Intune Wi-Fi configuration profile is assigned to the device group, devices have no Wi-Fi access in any location other than where they were enrolled. This will block all corporate app functionality. |

**Required action before deploying:**
1. Create a **Wi-Fi configuration profile**: **Devices** > **Configuration** > **+ Create** > **Android Enterprise** > **Fully managed, dedicated, and corporate-owned work profile** > **Wi-Fi**.
2. Configure the corporate SSID, security type, and credentials.
3. **Assign** this profile to the Fully Managed device group.
4. Deploy the Wi-Fi profile at the same time or before the device restriction policy.

---

#### W-3 — Bluetooth Pairing Fully Blocked (Settings Catalog) — Inconsistent with DC Template

| | Value |
| :--- | :--- |
| **Policy** | SC Device Restrictions — Fully Managed |
| **JSON field** | `bluetoothblockconfiguration_true` (SC setting 13) |
| **DC template** | `bluetoothBlockConfiguration: null` (not configured — allows pairing) |
| **Risk** | The SC policy blocks all Bluetooth pairing. Workers using Bluetooth headsets for Teams calls, wireless keyboards, or hands-free devices will be completely blocked. The DC and SC templates are **inconsistent** — the SC is more restrictive. |

**Steps to review:**
1. Determine whether Bluetooth peripherals are required for this deployment.
2. If Bluetooth headsets or keyboards are needed: Go to **Devices** > **Configuration** > open the SC Device Restrictions — Fully Managed policy > **Properties** > **Configuration settings** > **Edit** > find **Block Bluetooth configuration** > set to **Not configured**.
3. Align the DC and SC templates so both have the same Bluetooth setting.

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

**Required action:** Confirm that the enrollment flow (Company Portal / Intune app sign-in) provisions the work account automatically. Validate this end-to-end in a pilot before broad rollout.

---

### Risk Level: MEDIUM — Can Frustrate Users or Create Hidden Security Gaps

---

#### W-7 — Work Profile App Permission Policy (BYOD) ✅ RESOLVED in Baseline

| | Value |
| :--- | :--- |
| **Policy** | DC Device Restrictions — Personally-Owned Work Profile |
| **JSON field** | `workProfileDefaultAppPermissionPolicy: "prompt"` |
| **Status** | **Resolved.** The baseline has been updated. Work profile app permission policy is now set to **Prompt** — users are asked before each permission is granted. No action required. |

---

#### W-8 — Managed Google Play AllowList: Missing Apps Means Users Are Blocked

| | Value |
| :--- | :--- |
| **Policy** | DC Device Restrictions — Fully Managed |
| **JSON field** | `playStoreMode: "allowList"` |
| **Risk** | Only apps explicitly approved and published in Managed Google Play can be installed. If a required line-of-business app, tool, or system utility is not added to MGP before deployment, users cannot install it themselves — the Play Store will not show unapproved apps. |

**Required action before deploying:**
1. Go to **Apps** > **Android** > **Managed Google Play**.
2. Approve all apps required for the deployment (LOB apps, Microsoft 365 apps, Authenticator, any utilities).
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
| **Policy** | DC Device Restrictions — Fully Managed |
| **JSON field** | `passwordMinutesOfInactivityBeforeScreenTimeout: 1` |
| **Risk** | Screen turns off after just 1 minute of inactivity. Workers reading documents, reviewing content on screen, or speaking with a customer without touching the device will constantly need to wake the screen. Note: `passwordRequireUnlock: "daily"` means users only enter their PIN once a day — but the screen-off frequency itself will cause significant user frustration in most roles. |
| **Recommended** | Increase to **3–5 minutes** for knowledge workers. Keep at 1–2 minutes only for high-security environments or kiosk scenarios where fast screen lock is expected. |

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
| **Risk** | When two device restriction policies with conflicting values apply to the same device, Intune enforces the **most restrictive** value for each setting. This can produce unexpected behavior that is difficult to troubleshoot. Known conflicts between the DC and SC templates: **Camera** (DC allows, SC blocks → camera BLOCKED) and **Bluetooth configuration** (DC not configured, SC blocks → Bluetooth pairing BLOCKED). |

**Recommended action:**
- Choose **one** template type (DC or SC) as the primary policy for each enrollment type.
- Remove the other, or convert it to documentation-only.
- If both must remain, explicitly document each conflict and verify the most-restrictive outcome is the intended one.

---

### Risk Level: LOW — Communicate to Users and Helpdesk Before Deployment

| # | Setting | Policy | Impact | Action |
| :--- | :--- | :--- | :--- | :--- |
| W-13 | `dataRoamingBlocked: true` | FM SC | No mobile data internationally — Wi-Fi only | Communicate to users before travel |
| W-14 | `storageBlockExternalMedia: true` | FM DC + SC | SD cards and USB OTG storage blocked | Communicate; ensure cloud storage (OneDrive) is deployed |
| W-15 | System update window: 00:00–06:00 only | FM DC | Updates install only if device is on and plugged in overnight | Verify devices remain powered on; extend window if needed |
| W-16 | `factoryResetBlocked: true` + `passwordSignInFailureCountBeforeFactoryReset: 5` | FM DC | Users cannot manually factory reset — but the device CAN still be auto-wiped by 5 failed PIN attempts. These two settings are not the same. | Train helpdesk on this distinction |
| W-17 | `googleAccountsBlocked: true` | FM DC | Personal Google services (Maps, Pay, Gmail) not available | Communicate to users before deployment |

---

---

## Settings NOT Included in the Baseline — Administrator Action Required

The following CIS and Microsoft security framework recommendations are **not configured** in the baseline JSON policies. They are intentionally omitted because they depend on your organization's infrastructure, risk tolerance, or third-party integrations. Administrators should evaluate each one and configure it manually in the Intune admin center if applicable.

> These settings are not automatically applied when importing the baseline. Each one requires a deliberate decision and manual configuration.

---

### C-1 — Device Threat Protection (All Compliance Policies) — Not in Baseline

| | |
| :--- | :--- |
| **Applies to** | All compliance policies (Corporate FM, COWP, Kiosk, Shared, BYOD) |
| **JSON field** | `deviceThreatProtectionEnabled: false` / `deviceThreatProtectionRequiredSecurityLevel: "unavailable"` |
| **CIS Level** | L2 (Corporate), L2 (BYOD) |
| **Why not in baseline** | Requires Microsoft Defender for Endpoint (or an approved MTD partner) to be fully deployed and the MDE–Intune connector to be active. Enabling this without MDE will mark all devices non-compliant. |

**Steps to enable when MDE is deployed:**
1. Go to **Devices** > **Compliance** > open each compliance policy.
2. Click **Properties** > **System Security** > **Edit**.
3. Set **Require the device to be at or under the device threat level** to **Low**.
4. Set **Microsoft Defender for Endpoint** to **Require the device to be at or under the machine risk score: Low**.
5. Click **Review + save**.

---

### C-2 — SafetyNet Hardware-Backed Evaluation (Corporate Compliance) — Not in Baseline

| | |
| :--- | :--- |
| **Applies to** | AND - Compliance - DEV - Corporate-Fully-Managed-Devices; AND - Compliance - DEV - Corporate-Owned - Work-Profile |
| **JSON field** | `securityRequiredAndroidSafetyNetEvaluationType: "basic"` |
| **CIS Level** | L2 |
| **Why not in baseline** | Hardware-backed attestation requires devices with a dedicated secure enclave (Trusted Execution Environment). Older or budget devices may not support it, causing them to be incorrectly marked non-compliant. |

**Steps to enable when device hardware supports it:**
1. Go to **Devices** > **Compliance** > open the corporate compliance policy.
2. Click **Properties** > **Device Health** > **Edit**.
3. Set **SafetyNet evaluation type** to **Hardware backed key**.
4. Click **Review + save**.

> See [Support tip: Changes to Google Play strong integrity for Android 13+](https://techcommunity.microsoft.com/blog/intunecustomersuccess/support-tip-changes-to-google-play-strong-integrity-for-android-13-or-above/4176975)

---

### C-3 — Minimum Security Patch Level (All Compliance Policies) — Not in Baseline

| | |
| :--- | :--- |
| **Applies to** | All compliance policies |
| **JSON field** | `minAndroidSecurityPatchLevel: null` |
| **CIS Level** | L2 (Corporate), L1 (BYOD) |
| **Why not in baseline** | Patch level requirements vary by manufacturer and update cadence. A fixed date will cause newly enrolled devices from slow-update manufacturers to immediately fail compliance. |

**Steps to configure:**
1. Go to **Devices** > **Compliance** > open the target compliance policy.
2. Click **Properties** > **System Security** > **Edit**.
3. Set **Minimum security patch level** to a date no older than 12 months from today.
4. Click **Review + save**.
5. Repeat for each compliance policy.

---

### C-4 — Screen Lock Inactivity 5 Minutes (Corporate Compliance) — Not in Baseline

| | |
| :--- | :--- |
| **Applies to** | AND - Compliance - DEV - Corporate-Fully-Managed-Devices; AND - Compliance - DEV - Corporate-Owned - Work-Profile |
| **JSON field** | `passwordMinutesOfInactivityBeforeLock: 15` |
| **CIS Level** | L2 |
| **Recommended** | 5 minutes |
| **Why not in baseline** | A 5-minute screen lock significantly impacts usability in most work environments. 15 minutes is retained as the baseline default. Tighten for high-security environments. |

**Steps to configure:**
1. Go to **Devices** > **Compliance** > open the corporate compliance policy.
2. Click **Properties** > **System Security** > **Edit**.
3. Set **Minutes of inactivity before the screen is locked** to **5**.
4. Click **Review + save**.

---

### C-5 — SafetyNet Certified Device Attestation (BYOD Compliance) — Not in Baseline

| | |
| :--- | :--- |
| **Applies to** | AND - Compliance - USR - Personally-Owned - Work-Profile |
| **JSON field** | `securityRequireSafetyNetAttestationCertifiedDevice: false` |
| **CIS Level** | L2 |
| **Why not in baseline** | Certified Device attestation requires the device to be on Google's list of CTS-certified hardware. Some BYOD devices (older models, regional variants) may not be certified and would be permanently blocked from enrolling. |

**Steps to enable:**
1. Go to **Devices** > **Compliance** > open the BYOD compliance policy.
2. Click **Properties** > **Device Health** > **Edit**.
3. Set **Certified device (Play Integrity)** to **Require**.
4. Click **Review + save**.

---

### C-6 — Verify Apps in BYOD Compliance Policy — Not in Baseline

| | |
| :--- | :--- |
| **Applies to** | AND - Compliance - USR - Personally-Owned - Work-Profile |
| **JSON field** | `securityRequireVerifyApps: false` (compliance policy) |
| **Note** | Verify apps IS enforced via the BYOD device configuration policy (`securityRequireVerifyApps: true`). It is not separately enforced at the compliance policy level. |
| **CIS Level** | L1 |
| **Why not in baseline** | Enforcing Verify Apps at the compliance level means a user who disables it would immediately become non-compliant and lose access. Some BYOD users disable this for privacy. The device config policy already enables it on enrollment. |

**Steps to add compliance enforcement:**
1. Go to **Devices** > **Compliance** > open the BYOD compliance policy.
2. Click **Properties** > **System Security** > **Edit**.
3. Set **Require threat scan on apps** to **Require**.
4. Click **Review + save**.

---

### C-7 — MAM Maximum Allowed Device Threat Level — Not in Baseline

| | |
| :--- | :--- |
| **Applies to** | AND - App-Protection - USR - MAM - All Microsoft Apps - BYOD |
| **JSON field** | `maximumAllowedDeviceThreatLevel: "notConfigured"` |
| **CIS Level** | L1 |
| **Why not in baseline** | Requires a Mobile Threat Defense partner (MDE or equivalent) to be active. Without MTD, setting a threat level causes unpredictable app behavior on unmanaged devices. |

**Steps to enable when MDE is deployed:**
1. Go to **Apps** > **App protection policies** > open the MAM policy.
2. Click **Properties** > **Conditional launch** > **Edit**.
3. Under **Device conditions**, set **Max allowed device threat level** to **Low** with action **Block access**.
4. Click **Review + save**.

---

### C-8 — MAM SafetyNet Hardware-Backed Evaluation — Not in Baseline

| | |
| :--- | :--- |
| **Applies to** | AND - App-Protection - USR - MAM - All Microsoft Apps - BYOD |
| **JSON field** | `requiredAndroidSafetyNetEvaluationType: "basic"` |
| **CIS Level** | L2 |
| **Why not in baseline** | Same hardware dependency as C-2. BYOD devices are user-owned and hardware variety is even broader. Hardware-backed attestation may block a large portion of the BYOD user population with older devices. |

**Steps to enable:**
1. Go to **Apps** > **App protection policies** > open the MAM policy.
2. Click **Properties** > **Conditional launch** > **Edit**.
3. Under **Device conditions**, set **SafetyNet evaluation type** to **Hardware backed key**.
4. Click **Review + save**.

---

### C-9 — MAM Minimum Security Patch Version — Not in Baseline

| | |
| :--- | :--- |
| **Applies to** | AND - App-Protection - USR - MAM - All Microsoft Apps - BYOD |
| **JSON field** | `minimumRequiredPatchVersion: null` / `minimumWipePatchVersion: null` |
| **CIS Level** | L1 |
| **Why not in baseline** | Same operational concern as C-3. Patch update cadence varies significantly across BYOD manufacturers and user behavior. |

**Steps to configure:**
1. Go to **Apps** > **App protection policies** > open the MAM policy.
2. Click **Properties** > **Conditional launch** > **Edit**.
3. Under **Device conditions**, set **Min patch version** with a date no older than 12 months and action **Warn** (use **Block** only after validating patch compliance across your user population).
4. Click **Review + save**.

---

### C-10 — MAM Device Lock Required Field — Not Explicitly Required

| | |
| :--- | :--- |
| **Applies to** | AND - App-Protection - USR - MAM - All Microsoft Apps - BYOD |
| **JSON field** | `deviceLockRequired: false` |
| **Conditional launch** | `appActionIfDeviceLockNotSet: "block"` (device lock enforced via conditional launch) |
| **CIS Level** | L1 |
| **Note** | The baseline does NOT check the **Require device lock** checkbox in the Access Requirements section. However, the **Conditional Launch** rule blocks app access if no device lock is set — achieving the same end result. If you want the compliance check to appear in the Intune UI status for the user, enable the Require device lock toggle explicitly. |

**Steps to enable explicit require:**
1. Go to **Apps** > **App protection policies** > open the MAM policy.
2. Click **Properties** > **Access requirements** > **Edit**.
3. Set **Device lock** to **Require**.
4. Click **Review + save**.

---

## References

- [Android CIS Benchmarks](https://www.cisecurity.org/benchmark/google_android)
- [Android Enterprise security configuration framework (Microsoft)](https://learn.microsoft.com/en-us/mem/intune/enrollment/android-configuration-framework)
- [Android Enterprise compliance settings in Microsoft Intune](https://learn.microsoft.com/en-us/intune/intune-service/protect/compliance-policy-create-android-for-work)
- [Android app protection policy settings – Microsoft Intune](https://learn.microsoft.com/en-us/intune/intune-service/apps/app-protection-policy-settings-android)
- [Support tip: Changes to Google Play strong integrity for Android 13+](https://techcommunity.microsoft.com/blog/intunecustomersuccess/support-tip-changes-to-google-play-strong-integrity-for-android-13-or-above/4176975)
