# Managing Android Devices with Microsoft Intune

## Android Enterprise Baseline – CIS Compliance Hardening Guide

### Overview

This guide documents the specific Intune configuration changes needed to bring the Android Enterprise Baseline into closer alignment with the [Android CIS Benchmarks](https://www.cisecurity.org/benchmark/google_android) and Microsoft's [Android Enterprise security configuration framework](https://learn.microsoft.com/en-us/mem/intune/enrollment/android-configuration-framework).

Each change below includes:
- The **current baseline value** (from the imported JSON policy files).
- The **recommended CIS-aligned value**.
- The **exact steps** to make the change in the Microsoft Intune admin center.
- The **security framework level** it applies to.

> **Important:** This is a reference guide — not a one-size-fits-all checklist. Assess each recommendation against your organization's operational requirements, risk tolerance, and the specific deployment model before making changes. Always test changes with a pilot group before broad rollout.

> **Prerequisite for threat protection settings:** Settings under "Device Threat Protection" and "Max allowed device threat level" only take effect if Microsoft Defender for Endpoint (or another Mobile Threat Defense partner) is deployed and the MDE–Intune connector is active. See the [Defender for Endpoint for Android Devices guide](Defender%20for%20Endpoint%20for%20Android%20Devices.md) before enabling these settings.

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
| 1.1.4 | Corporate Compliance (all) | Screen lock inactivity | 60 min | 5 min | L2 |
| 1.2.1 | BYOD Compliance | Min security patch level | Not configured | ≤ 12 months | L1 |
| 1.2.2 | BYOD Compliance | Screen lock inactivity | 60 min | 15 min | L1 |
| 1.2.3 | BYOD Compliance | Require verify apps | Disabled | Enabled | L1 |
| 1.2.4 | BYOD Compliance | Certified device attestation | Disabled | Enabled | L2 |
| 1.2.5 | BYOD Compliance | Device threat protection | Fully disabled | Enabled, Low | L2 |
| 2.1.1 | Fully Managed DC template | Camera | Allowed | Block | L2 |
| 2.1.2 | Fully Managed SC | Apps default permission | Device default | Prompt | L2 |
| 2.3.1 | BYOD SC | Cross-profile data sharing | Not configured | Work to personal blocked | L1 |
| 3.1 | MAM App Protection | Max device threat level | Not configured | Low → Block | L1 |
| 3.2 | MAM App Protection | Simple PIN | Allowed | Block | L1 |
| 3.3 | MAM App Protection | Device lock required | Not required | Require | L1 |
| 3.4 | MAM App Protection | PIN reset period | None (PT0S) | 90 days | L1 |
| 3.5 | MAM App Protection | Previous PIN block count | 0 | 5 | L1 |
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
| Password type | numericComplex | All policies ✓ |
| Password minimum length | 6 characters | All policies ✓ |
| Password expiration | 365 days | All policies ✓ |
| Previous password block count | 5 | Corporate policies ✓ |
| Factory reset blocked | Yes | Device restriction policies ✓ |
| USB file transfer blocked | Yes | Device restriction policies ✓ |
| Screen capture blocked | Yes | Device restriction policies ✓ |
| Play Store mode | allowList | Corporate device restrictions ✓ |
| Developer settings disabled | Yes | Settings Catalog policies ✓ |
| Network escape hatch disabled | Yes | Settings Catalog policies ✓ |
| Private Space disallowed | Yes | Settings Catalog FM policy ✓ |
| WiFi Direct disallowed | Yes | Settings Catalog policies ✓ |
| Bluetooth contact sharing blocked | Yes | Device restrictions ✓ |
| MAM SafetyNet attestation type | basicIntegrityAndDeviceCertification | Already at enhanced level ✓ |
| MAM Class 3 biometrics required | Yes | MAM policy ✓ |
| MAM data backup blocked | Yes | MAM policy ✓ |
| MAM outbound data transfer | Managed apps only | MAM policy ✓ |
| MAM screen capture blocked | Yes | MAM policy ✓ |
| MAM offline wipe period | 90 days | MAM policy ✓ |
| MAM PIN minimum length | 6 | MAM policy ✓ |
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

#### W-7 — Work Profile Apps Auto-Granted All Permissions (BYOD)

| | Value |
| :--- | :--- |
| **Policy** | DC Device Restrictions — Personally-Owned Work Profile |
| **JSON field** | `workProfileDefaultAppPermissionPolicy: "autoGrant"` |
| **Risk** | Every app installed in the work profile is automatically granted **all** runtime permissions (contacts, location, camera, microphone, storage) without any user prompt. Users have zero visibility into what permissions apps are using. This is both a security gap and contrary to CIS Level 1 recommendations. |
| **Recommended** | Change to **Prompt** — users are asked before each permission is granted. |

**Steps to fix:**
1. Go to **Devices** > **Configuration** > open the DC Device Restrictions — Personally-Owned Work Profile policy.
2. Click **Properties** > **Configuration settings** > **Edit**.
3. Locate **Work profile default app permission policy**.
4. Change from **Auto grant** to **Prompt**.
5. Click **Review + save**.

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

## References

- [Android CIS Benchmarks](https://www.cisecurity.org/benchmark/google_android)
- [Android Enterprise security configuration framework (Microsoft)](https://learn.microsoft.com/en-us/mem/intune/enrollment/android-configuration-framework)
- [Android Enterprise compliance settings in Microsoft Intune](https://learn.microsoft.com/en-us/intune/intune-service/protect/compliance-policy-create-android-for-work)
- [Android app protection policy settings – Microsoft Intune](https://learn.microsoft.com/en-us/intune/intune-service/apps/app-protection-policy-settings-android)
- [Support tip: Changes to Google Play strong integrity for Android 13+](https://techcommunity.microsoft.com/blog/intunecustomersuccess/support-tip-changes-to-google-play-strong-integrity-for-android-13-or-above/4176975)
