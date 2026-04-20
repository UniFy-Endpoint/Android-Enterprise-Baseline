# Managing Android Devices with Microsoft Intune

## Enrollment Restrictions

### Overview

Enrollment Restrictions in Microsoft Intune control **which devices are permitted to enroll** in your tenant. They act as a gatekeeper before enrollment begins — a device that does not meet the restriction criteria is blocked before it can enroll, regardless of which enrollment profile it uses.

> **Android Device Administrator (DA) deprecation:** This baseline covers only **Android Enterprise** (`androidForWork`). Android Device Administrator management has been deprecated for GMS devices and is no longer available for new enrollments as of December 2024. If your tenant still contains DA-managed devices, those devices should be migrated to Android Enterprise before they become unmanageable.

Enrollment Restrictions are separate from Enrollment Profiles. The distinction is:

| | Enrollment Restrictions | Enrollment Profiles |
| :--- | :--- | :--- |
| **Purpose** | Controls WHO and WHAT can enroll | Defines HOW the device is enrolled |
| **Evaluated** | Before enrollment begins | During the enrollment process |
| **Configured in** | Devices > Enrollment > Enrollment restrictions | Devices > Android > Enrollment |
| **Blocks** | Entire platforms, OS versions, manufacturers | N/A (they configure, not gate) |

---

### How Enrollment Restrictions Work

Intune evaluates restrictions at the point of enrollment. If a device matches a **block** rule (platform blocked, personal enrollment blocked, OS version below minimum, blocked manufacturer), the enrollment is rejected immediately.

**Priority:** Multiple restrictions can exist in a tenant. They are evaluated in priority order (lowest number = highest priority). The default restriction (priority 2147483647) allows all platforms. Custom restrictions are assigned a lower number (higher priority) and can be scoped to specific user groups.

---

### Baseline Restriction: AND - Allow-Android-Enterprise (BYOD)

This baseline includes one enrollment restriction:

**`AND - Allow-Android-Enterprise (BYOD)`**

| Setting | Value | Notes |
| :--- | :--- | :--- |
| **Display Name** | AND - Allow-Android-Enterprise (BYOD) | |
| **Description** | Enable Android Enterprise BYOD (Work Profile) Enrollment | |
| **Priority** | 1 | Highest priority — evaluated before the tenant default |
| **Platform Type** | androidForWork | Android Enterprise (Work Profile, Fully Managed, Corporate Work Profile, Dedicated) |
| **Platform Blocked** | No | Android Enterprise enrollment is permitted |
| **Personal Device Enrollment Blocked** | No | Personally-owned (BYOD) Work Profile enrollment is allowed |
| **Minimum OS Version** | Not configured | No minimum enforced |
| **Maximum OS Version** | Not configured | No maximum enforced |
| **Blocked Manufacturers** | None | All manufacturers permitted |
| **Blocked SKUs** | None | All SKUs permitted |
| **Assigned To** | AND - USR - Android-Personal-Work-Profile-Users - BYOD | BYOD users group |

**Purpose:** This restriction explicitly permits Android Enterprise enrollment (including personal/BYOD Work Profile) for the BYOD user group. At priority 1, it overrides any more restrictive default or other policies for these users.

---

### Where to Configure in the Intune Admin Center

1. Navigate to **Devices** > **Enrollment** > **Enrollment restrictions**.
2. Select **Device type restrictions** tab.
3. Select **Create restriction** (or view the existing `AND - Allow-Android-Enterprise (BYOD)` restriction).
4. **Platform:** Android Enterprise.
5. Configure the settings per the table above.
6. **Assignments:** Assign to the BYOD user group (`AND - USR - Android-Personal-Work-Profile-Users - BYOD`).
7. Set the **priority** to 1 (or the appropriate value for your tenant).

> **Note:** After importing via IntuneManagement, verify the priority number and group assignment — priority numbers and group IDs do not transfer between tenants. Re-assign the correct group and set the priority manually.

---

### Hardening Options (Beyond the Baseline)

The baseline restriction does not enforce OS version or manufacturer restrictions. Consider these additions for production environments:

| Setting | Recommendation | Rationale |
| :--- | :--- | :--- |
| **Minimum OS Version** | Android 10.0 or higher | Android 8.0 is the enrollment minimum for most Android Enterprise types (Work Profile, Dedicated, COPE) and Android 10.0 for Fully Managed. Android 10.0 or later is recommended as the practical minimum — older devices are likely out of support and cannot receive security patches. |
| **Blocked Manufacturers** | Evaluate per org | Some organizations block manufacturers that do not participate in Android Enterprise recommended programs or have known supply-chain concerns. |
| **Personal Device Enrollment Blocked** | Do NOT block for BYOD | This restriction is specifically for BYOD enrollment. Blocking personal enrollment defeats its purpose. |

> **CIS Note:** Neither the CIS Android Benchmarks nor the Microsoft Security Configuration Framework require a specific minimum OS version in enrollment restrictions — but both assume devices capable of meeting current security patch requirements. Enforcing a minimum OS version at the restriction level prevents devices that can never be compliant from enrolling in the first place.

---

### Relationship to the Rest of the Baseline

| Component | Role |
| :--- | :--- |
| Enrollment Restriction | Gates which devices can enroll (platform, OS, manufacturer) |
| Enrollment Profile | Configures how the device enrolls (token type, name template, group assignment) |
| Compliance Policy | Defines what the device must meet to be compliant after enrollment |
| Conditional Access | Blocks access to resources if the device is not compliant |
| Assignment Filter (AND-POWP-DEV) | Scopes policies to personally-owned devices within assigned groups |

All five components work together. A BYOD user must pass the enrollment restriction, enroll via the Work Profile enrollment profile, receive the compliance and configuration policies, and only then gain access to corporate resources through Conditional Access.
