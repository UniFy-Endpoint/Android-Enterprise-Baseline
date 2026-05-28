# Managing Android Devices with Microsoft Intune

## Android Enterprise Baseline – CIS Compliance Hardening Guide

### Overview

This guide is the consolidated reference for aligning the Android Enterprise Baseline with the [Android CIS Benchmarks](https://www.cisecurity.org/benchmark/google_android) and Microsoft's [Android Enterprise security configuration framework](https://learn.microsoft.com/en-us/mem/intune/enrollment/android-configuration-framework).

The baseline currently achieves approximately **92% combined CIS alignment** (Level 1 and Level 2), verified against exported Intune policy JSON files (2026-05-28). A subset of CIS recommendations remains intentionally deferred or accepted as exceptions to maintain usability and accommodate operational requirements. All compliance gap findings are tracked in the [Compliance Findings & Gap Tracker](#compliance-findings--gap-tracker) section at the end of this guide.

This guide includes:
- **Compliance Score** — current state and projected improvement path.
- **Hardening Summary Table** — all recommended settings with applicability by enrollment type and current status.
- **What Is Already Well Configured** — settings fully satisfied by the current baseline.
- **Operational Risk Warnings** — settings that may cause user disruptions, deployment failures, or unexpected behavior.
- **Settings NOT Included in the Baseline** — CIS recommendations intentionally excluded, with implementation guidance where applicable.

> **Important:** This is a reference guide — not a one-size-fits-all checklist. Assess each recommendation against your organization's operational requirements, risk tolerance, and deployment model before making changes. Always test with a pilot group before broad rollout.

> **Prerequisite for threat protection settings:** Settings under "Device Threat Protection" and "Max allowed device threat level" only take effect if Microsoft Defender for Endpoint (or another Mobile Threat Defense partner) is deployed and the MDE–Intune connector is active. See the [Defender for Endpoint for Android Devices guide](11_Defender%20for%20Endpoint%20for%20Android%20Devices.md) before enabling these settings.

> **Review Operational Risk Warnings before deploying:** The Operational Risk Warnings section documents settings already present in the baseline that can block users or cause deployment failures — independent of CIS hardening changes. Review it before any production rollout.

> **Policy names:** This guide uses descriptive labels (e.g., "SC Device Restrictions — Fully Managed") in place of specific policy names. Refer to [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the exact current name of each policy.

---

## Compliance Score — Current Status

This baseline is currently estimated at **~85% alignment** with the CIS Android Benchmark (combined Level 1 and Level 2 controls). Scores are derived from verified policy JSON exports.

| Scope | Baseline v1.0 | v1.1 (Apr 2026) | v1.3 (May 2026) | Theoretical Max |
| :--- | :--- | :--- | :--- | :--- |
| CIS Level 1 controls | ~77% | ~83% | ~92% | ~95% |
| CIS Level 2 controls | ~72% | ~84% | ~91% | ~94% |
| **Combined (L1 + L2)** | **~75%** | **~85%** | **~92%** | **~95%** |

**v1.3 fixes applied (2026-05-28):** MDE threat protection enforced via dedicated compliance policies; SafetyNet basic integrity explicitly set on all corporate compliance policies; Play Store AllowList enabled on FM DC; COWP DC aligned (account modification, factory reset, cross-profile copy/paste); SC FM and SC COWP consistency alignment.

The remaining gaps are either accepted exceptions (intentional deviations with documented reason) or deferred items:

| ID | Setting | Status | Reason |
| :--- | :--- | :--- | :--- |
| L1-1 | Min security patch level — all compliance policies | **Accepted Exception** | Admin configures at deployment time based on the current Android version and manufacturer cadence; quarterly review recommended |
| L1-3 | BYOD device-level trust agents | Deferred | Personal profile is unmanaged by design; cannot enforce on personal side |
| L1-4 | Screen lock timeout ≤ 2 min — DC FM + compliance | **Accepted Exception — 5 min** | CIS recommends ≤ 2 min; 5-min setting is intentional for user productivity across shift-worker and office scenarios |
| L2-2 | MAM Play Protect app verification | Deferred | Accepted risk — deferred for BYOD usability |
| L2-5 | MAM minimum security patch version | Deferred | Accepted risk — BYOD users control their own update cadence |
| L2-7 | Location mode — FM + COPE Settings Catalog policies | Deferred | Operational apps may require user-controlled location |

---

### Microsoft Security Configuration Framework — Level Reference

| Level | Name | Applies To |
| :--- | :--- | :--- |
| **Level 1** | Basic Enterprise Security | Personally Owned Work Profile (BYOD) |
| **Level 2** | Enhanced Enterprise Security | Corporate-Owned Work Profile, Fully Managed |
| **Level 3** | High Enterprise Security | Fully Managed — high-sensitivity environments |

---

## Hardening Summary Table

All recommended settings, their current configured state, and applicability by device enrollment type.

**Status:** Implemented · Pending · Partial

**Applies To:** `FM` = Fully Managed · `COPE` = Corporate-Owned Work Profile · `Ded` = Dedicated Kiosk/Shared · `BYOD` = Personally-Owned Work Profile

| # | Policy | Setting | Current State | Recommended | Level | Applies To | Status |
| :---: | :--- | :--- | :--- | :--- | :---: | :--- | :---: |
| 1.1.1 | Corporate MDE Compliance | Device threat protection | Enabled | Enabled, Low | L2 | FM, COPE, Ded, BYOD | Implemented — AND - CP - DEV - Compliance - MDE - Corporate Devices |
| 1.1.2 | Corporate Compliance (all) | SafetyNet evaluation type | Hardware-backed | Hardware-backed | L2 | FM, COPE, Ded, BYOD | Implemented |
| 1.1.3 | Corporate Compliance (all) | Min security patch level | Not configured | ≤ 12 months | L2 | FM, COPE, Ded | Accepted Exception — admin sets at deployment time |
| 1.1.4 | Corporate Compliance (corporate) | Screen lock inactivity | 5 min | ≤ 2 min (CIS L1) | L2 | FM, COPE, Ded | Accepted Exception — 5 min intentional; see L1-4 in deferred table |
| 1.2.1 | BYOD Compliance | Min security patch level | Not configured | ≤ 12 months | L1 | BYOD | Accepted Exception — admin sets at deployment time |
| 1.2.2 | BYOD Compliance | Screen lock inactivity | 15 min (by design) | 15 min | L1 | BYOD | Implemented |
| 1.2.3 | BYOD Compliance | Require verify apps | Enabled | Enabled | L1 | BYOD | Implemented |
| 1.2.4 | BYOD Compliance | Certified device attestation | Enabled | Enabled | L2 | BYOD | Implemented |
| 1.2.5 | BYOD MDE Compliance | Device threat protection | Enabled | Enabled, Low | L2 | BYOD | Implemented — AND - CP - DEV - Compliance - MDE - Personal Devices |
| 2.1.1 | FM DC template | Keyguard camera blocked | Blocked | Block | L2 | FM, COPE, Ded | Implemented |
| 2.1.2 | FM SC | Apps default permission | Device Default | Device Default | L2 | FM, COPE | Implemented |
| 2.3.1 | BYOD DC | Cross-profile data sharing | Work → Personal blocked | Work → Personal blocked | L1 | BYOD | Implemented |
| 3.1 | MAM App Protection | Max device threat level | Not configured | Low → Block | L1 | BYOD | Pending — requires MDE MAM connector |
| 3.2 | MAM App Protection | Simple PIN | Blocked | Blocked | L1 | BYOD | Implemented |
| 3.3 | MAM App Protection | Device lock required | Enforced via conditional launch | Require | L1 | BYOD | Partial — conditional launch enforces same outcome; see C-10 |
| 3.4 | MAM App Protection | PIN reset period | 90 days | 90 days | L1 | BYOD | Implemented |
| 3.5 | MAM App Protection | Previous PIN block count | 5 | 5 | L1 | BYOD | Implemented |
| 3.6 | MAM App Protection | Min required patch version | Not configured | ≤ 12 months | L1 | BYOD | Deferred — see L2-5 |
| 3.7 | MAM App Protection | SafetyNet evaluation type | Basic | Hardware-backed | L2 | BYOD | Deferred — see L2-2 |

> **Row 3.3 note:** `deviceLockRequired` is `false` in the MAM policy, but a Conditional Launch rule (`appActionIfDeviceLockNotSet: block`) enforces the same outcome. The device lock requirement is functionally enforced — only the explicit toggle in Access Requirements is not set.

---

## What Is Already Well Configured

The following settings are aligned with CIS recommendations and do not require changes. **Applies To** indicates which device enrollment types the setting is active on.

**Applies To:** `FM` = Fully Managed · `COPE` = Corporate-Owned Work Profile · `Ded` = Dedicated Kiosk/Shared · `BYOD` = Personally-Owned Work Profile

| Setting | Value | Notes | Applies To |
| :--- | :--- | :--- | :--- |
| Storage encryption required | Yes | All compliance policies | FM, COPE, Ded, BYOD |
| Block jailbroken / rooted devices | Yes | All compliance policies | FM, COPE, Ded, BYOD |
| Minimum OS version | 13.0 | All compliance policies | FM, COPE, Ded, BYOD |
| Screen lock required | Yes | All compliance policies | FM, COPE, Ded, BYOD |
| Password type (corporate) | numericComplex | Corporate compliance and DC/SC policies | FM, COPE, Ded |
| Password type (BYOD compliance) | Device default, Medium complexity | BYOD compliance uses complexity level instead of explicit type | BYOD |
| Password minimum length | 6 characters | All compliance and DC/SC policies | FM, COPE, Ded, BYOD |
| Password expiration | 365 days | All compliance and DC/SC policies | FM, COPE, Ded, BYOD |
| Previous password block count | 5 | All compliance and DC/SC policies | FM, COPE, Ded, BYOD |
| Max failed unlock attempts | 10 | All DC policies | FM, COPE, Ded, BYOD |
| Screen lock re-auth interval (biometric) | Daily | All corporate DC policies | FM, COPE, Ded |
| Play Integrity — basic attestation | Enabled | All compliance policies | FM, COPE, Ded, BYOD |
| Play Integrity — certified device | Enabled | All compliance policies and MAM | FM, COPE, Ded, BYOD |
| Play Integrity — hardware-backed evaluation | Enabled | All compliance policies | FM, COPE, Ded, BYOD |
| Factory reset blocked | Yes | DC + SC device restriction policies | FM, COPE, Ded |
| USB file transfer blocked | Yes | SC device restriction policies | FM, COPE |
| Screen capture blocked (corporate) | Yes | SC + DC corporate device restriction policies | FM, COPE, Ded |
| Screen capture blocked (BYOD work profile) | Yes | DC BYOD policy | BYOD |
| Unknown sources blocked | Yes | DC BYOD + SC FM/COPE | FM, COPE, BYOD |
| USB debugging disabled | Yes | BYOD compliance policy | BYOD |
| App auto-update policy | Always | DC + SC corporate policies | FM, COPE |
| Play Store mode | allowList | Corporate DC device restrictions | FM, COPE, Ded |
| Developer settings disabled | Yes | Settings Catalog policies | FM, COPE |
| Network escape hatch disabled | Yes | Settings Catalog policies | FM, COPE |
| Private Space disallowed | Yes | SC FM + COPE policies | FM, COPE |
| WiFi Direct disallowed | Yes | Settings Catalog policies | FM, COPE |
| Bluetooth configuration blocked | Yes (COPE, Ded); Intentionally NOT blocked (FM) | DC COPE; DC Dedicated; **FM — accepted exception, users need to pair headsets, car systems and connect to home Wi-Fi (see DISC-02)** | COPE, Ded |
| Bluetooth contact sharing blocked | Yes | DC + SC device restrictions | FM, COPE |
| NFC outgoing beam blocked | Yes | Settings Catalog policies | FM, COPE |
| Data roaming blocked | Yes (COPE, Ded); Intentionally NOT blocked (FM) | DC COWP; DC Kiosk/Shared; SC COPE; **FM SC — accepted exception, users travel and take devices home** | COPE, Ded |
| External media blocked | Yes | DC + SC device restrictions | FM, COPE, Ded |
| Date/time configuration blocked | Yes | DC + SC device restrictions | FM, COPE, Ded |
| Account modification blocked | Yes | DC + SC FM/COPE | FM, COPE |
| Certificate credential installation disabled | Yes | DC + SC FM/COPE — deploy SCEP/PKCS before rollout | FM, COPE |
| Assist content blocked | Yes | SC FM + COPE | FM, COPE |
| Wi-Fi tethering blocked | Yes | DC + SC all corporate | FM, COPE, Ded |
| Keyguard camera blocked | Yes | FM + COPE DC | FM, COPE |
| Keyguard remote input blocked | Yes | FM DC | FM |
| Keyguard unredacted notifications blocked | Yes | FM DC | FM |
| Keyguard trust agents blocked | Yes | FM DC; COPE DC (trustAgents only — see W-24 for remaining gaps) | FM, COPE |
| Work profile notifications blocked while locked | Yes | DC BYOD policy | BYOD |
| Work profile trust agents blocked | Yes | DC BYOD policy | BYOD |
| Work profile cross-profile copy/paste blocked | Yes | DC BYOD + SC COPE | BYOD, COPE |
| Work profile add accounts blocked | Yes | DC BYOD policy | BYOD |
| Work profile app permission policy | Prompt | DC BYOD policy | BYOD |
| Work profile verify apps | Yes | DC BYOD + compliance | BYOD |
| Work profile cross-profile data sharing | Work → Personal blocked | DC BYOD + SC COPE | BYOD, COPE |
| Common Criteria Mode enabled | Yes | SC FM policy — Samsung Knox only (see W-25) | FM |
| Conditional Access — managed devices | Require compliant device | CA-User-Protection-AllUsers-AllApps-Android-Req-ComplianceDevice | FM, COPE, Ded |
| Conditional Access — MAM BYOD | Require App Protection Policy | CA-User-Protection-AllUsers-O365-MobileApps-Android-Req-AppProtectionPolicy-BYOD | BYOD |
| MAM PIN required | Yes, 6-char numeric, simple PIN blocked | MAM policy | BYOD |
| MAM data backup blocked | Yes | MAM policy | BYOD |
| MAM outbound data transfer | Managed apps only | MAM policy | BYOD |
| MAM clipboard restriction | managedAppsWithPasteIn | MAM policy | BYOD |
| MAM save-as blocked | Yes | MAM policy | BYOD |
| MAM print blocked | Yes | MAM policy | BYOD |
| MAM screen capture blocked | Yes | MAM policy | BYOD |
| MAM encryption | Yes | MAM policy | BYOD |
| MAM offline wipe period | 90 days | MAM policy | BYOD |
| MAM biometric re-auth interval | 30 minutes | MAM policy | BYOD |
| MAM Class 3 biometrics required | Yes | MAM policy | BYOD |
| MAM PIN after biometric change | Yes | MAM policy | BYOD |
| MAM previous PIN block count | 5 | MAM policy | BYOD |
| MAM PIN reset period | 90 days | MAM policy | BYOD |
| MAM device passcode complexity | Low minimum (block if not met) | MAM conditional launch | BYOD |
| MAM notification content restriction | blockOrganizationalData | MAM policy | BYOD |
| MAM Safety Net — device attestation | basicIntegrityAndDeviceCertification, Block | MAM policy | BYOD |
| MDE low-touch onboarding | Enabled | MDE App Configuration Policy | FM, COPE, BYOD |
| MDE network protection | Enabled | MDE App Configuration Policy | FM, COPE, BYOD |
| MDE auto-remediation | Enabled | MDE App Configuration Policy | FM, COPE, BYOD |

---

## Operational Risk Warnings — Settings That Can Break Device Functionality

This section documents settings present in the baseline policies that may cause user-facing disruptions, deployment failures, or unexpected device behavior. These are not CIS hardening changes — they are baseline settings and operational considerations that administrators must understand before rollout.

> **Before deploying any policy:** Review the items below and validate each one against your organization's operational requirements and device use cases. Test with a pilot group before broad rollout.

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
| `wifiBlockEditConfigurations` | `false` (users can edit Wi-Fi) | `true` (users cannot) | COPE users are more restricted than FM for Wi-Fi without stated reason |
| `usersBlockRemove` | `true` | `false` | COPE users can remove accounts, potentially initiating de-enrollment from a corporate-owned device |
| `passwordBlockKeyguardFeatures` | Full set (camera, remoteInput, trustAgents, unredactedNotifications) | trustAgents only | COPE lock screen has weaker keyguard protection than FM |
| `factoryResetBlocked` | `true` | `null` | COPE factory reset protection relies solely on the SC COPE policy; gap if SC is ever excluded |

**Recommendation:** Set `usersBlockRemove=true` and add `camera`, `remoteInput`, `unredactedNotifications` to `passwordBlockKeyguardFeatures` on the COPE DC policy to align with FM. Document the `wifiBlockEditConfigurations` difference as intentional if COPE users are expected to have tighter network controls. The `factoryResetBlocked=null` gap on COPE DC is currently covered by the SC COPE policy — no immediate action required, but worth noting.

---

### Risk Level: LOW — Communicate to Users and Helpdesk Before Deployment

| # | Setting | Policy | Impact | Action |
| :--- | :--- | :--- | :--- | :--- |
| W-13 | `dataRoamingBlocked` | COPE DC + SC; Dedicated DC | No mobile data internationally — Wi-Fi only on COPE and Dedicated devices. **FM is intentionally NOT blocked** — FM users travel and take devices home. | Communicate COPE/Ded users before international travel |
| W-14 | `storageBlockExternalMedia: true` | FM DC + SC | SD cards and USB OTG storage blocked | Communicate; ensure cloud storage (OneDrive) is deployed |
| W-15 | System update window: 00:00–06:00 only | FM DC | Updates install only if device is on and plugged in overnight | Verify devices remain powered on; extend window if needed |
| W-16 | `factoryResetBlocked: true` + `passwordSignInFailureCountBeforeFactoryReset: 10` | FM DC | Users cannot manually factory reset — but the device CAN still be auto-wiped by 10 failed PIN attempts. These two settings are not the same. | Train helpdesk on this distinction |
| W-17 | `googleAccountsBlocked: true` | FM DC | Personal Google services (Maps, Pay, Gmail) not available | Communicate to users before deployment |
| W-25 | `securityCommonCriteriaModeEnabled: true` | SC — Fully Managed | **Samsung Knox-specific.** On non-Samsung devices (Google Pixel, Motorola, etc.), this setting is silently ignored or may produce undefined behavior. On Samsung devices it disables fingerprint enrollment/unenrollment during sessions and may affect some accessibility features and camera behavior on specific firmware versions. | If the FM fleet includes non-Samsung devices, scope this setting to a Samsung-only **Assignment Filter** (`deviceManufacturer equals Samsung`). Document the hardware dependency for future procurement. |

---

---

## Settings NOT Included in the Baseline — Administrator Action Required

The following CIS and Microsoft security framework recommendations are evaluated below. Items marked **Implemented** have been applied in the current baseline. Items without this mark are not configured and require deliberate evaluation and manual configuration if applicable.

> These settings are not automatically applied when importing the baseline. Each requires a deliberate decision and manual configuration in the Intune admin center.

---

### C-1 — Device Threat Protection (All Compliance Policies) — Implemented

| | |
| :--- | :--- |
| **Applies to** | All device types via dedicated MDE compliance policies |
| **JSON field** | `deviceThreatProtectionEnabled: true` / `advancedThreatProtectionRequiredSecurityLevel: "low"` |
| **CIS Level** | L2 |
| **Status** | **Implemented (2026-05-28).** MDE threat protection is enforced via two dedicated compliance policies separated from the base device compliance policies: `AND - CP - DEV - Compliance - MDE - Corporate Devices` (covers FM, COPE, Kiosk, Shared) and `AND - CP - DEV - Compliance - MDE - Personal Devices` (covers BYOD). These policies must be assigned to the same device groups as the base compliance policies. |

> **Architecture note:** MDE enforcement is intentionally kept in separate policies so it can be assigned independently, toggled during MDE deployment phases, and excluded from devices not yet enrolled in Defender. This avoids marking all devices non-compliant if MDE deployment is partial.

**Assignment requirement:** Assign both MDE compliance policies to the same groups as the corresponding base compliance policies. Both policies must evaluate as compliant for a device to be considered compliant overall.

**If MDE is not yet deployed:** Remove the assignment from the MDE compliance policies until the MDE–Intune connector is active. See the [Defender for Endpoint for Android Devices guide](11_Defender%20for%20Endpoint%20for%20Android%20Devices.md) for deployment steps.

---

### C-2 — SafetyNet Hardware-Backed Evaluation (All Compliance Policies) — Implemented

| | |
| :--- | :--- |
| **Applies to** | All 5 compliance policies |
| **JSON field** | `securityRequiredAndroidSafetyNetEvaluationType: "hardwareBacked"` |
| **CIS Level** | L2 |
| **Status** | **Implemented** — All 5 compliance policies verified with hardware-backed evaluation type (exported JSON 2026-04-25). See W-18 for rollout risk guidance before broad deployment. |

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

### C-4 — Screen Lock Inactivity 5 Minutes (Corporate Compliance) — Implemented

| | |
| :--- | :--- |
| **Applies to** | All 4 corporate compliance policies (FM, COPE, Kiosk, Shared) |
| **JSON field** | `passwordMinutesOfInactivityBeforeLock: 5` |
| **CIS Level** | L2 |
| **Status** | **Implemented** — All 4 corporate compliance policies verified at 5-minute inactivity lock (exported JSON 2026-04-25). BYOD compliance policy remains at 15 minutes by design — aligned with Microsoft L1 BYOD guidance. |

---

### C-5 — SafetyNet Certified Device Attestation (BYOD Compliance) — Implemented

| | |
| :--- | :--- |
| **Applies to** | AND - CP - USR - Compliance - Personally-Owned - Work-Profile |
| **JSON field** | `securityRequireSafetyNetAttestationCertifiedDevice: true` |
| **CIS Level** | L2 |
| **Status** | **Implemented** — BYOD compliance policy verified with `basicIntegrityAndDeviceCertification` attestation required (exported JSON 2026-04-25). |

---

### C-6 — Verify Apps in BYOD Compliance Policy — Implemented

| | |
| :--- | :--- |
| **Applies to** | AND - CP - USR - Compliance - Personally-Owned - Work-Profile |
| **JSON field** | `securityRequireVerifyApps: true` |
| **CIS Level** | L1 |
| **Status** | **Implemented** — BYOD compliance policy verified with `securityRequireVerifyApps: true` at both compliance policy level and DC BYOD policy level (exported JSON 2026-04-25). |

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

## Compliance Findings & Gap Tracker

> Consolidated findings from the CIS compliance analysis performed 2026-05-28 against all exported baseline JSON files. This replaces the standalone `CIS_Compliance_Report.md`. All statuses reflect the current state of baseline v1.3.

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
| USB file transfer blocked | `storageBlockUsbFileTransfer: true` | DC FM; SC FM, SC COWP |
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
| Private space blocked | `privatespacepolicy_disallowed` | SC FM, SC COWP |
| Cross-profile copy/paste blocked | `crossProfilePoliciesAllowCopyPaste: false` | DC COWP; SC FM, SC COWP |
| Cross-profile data sharing blocked | Work → Personal blocked | DC COWP; SC FM, SC COWP |
| Security Common Criteria Mode | `securityCommonCriteriaModeEnabled: true` | SC FM (Samsung Knox only — see W-25) |
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

- [Android CIS Benchmarks](https://www.cisecurity.org/benchmark/google_android)
- [Android Enterprise security configuration framework (Microsoft)](https://learn.microsoft.com/en-us/mem/intune/enrollment/android-configuration-framework)
- [Android Enterprise compliance settings in Microsoft Intune](https://learn.microsoft.com/en-us/intune/intune-service/protect/compliance-policy-create-android-for-work)
- [Android app protection policy settings – Microsoft Intune](https://learn.microsoft.com/en-us/intune/intune-service/apps/app-protection-policy-settings-android)
- [Support tip: Changes to Google Play strong integrity for Android 13+](https://techcommunity.microsoft.com/blog/intunecustomersuccess/support-tip-changes-to-google-play-strong-integrity-for-android-13-or-above/4176975)
