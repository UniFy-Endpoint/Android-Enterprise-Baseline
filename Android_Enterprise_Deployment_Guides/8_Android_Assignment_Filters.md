# Managing Android Devices with Microsoft Intune

## Assignment Filters

### Overview

Assignment Filters allow you to refine how Intune policies, apps, and configurations are targeted — beyond simple group membership. Instead of creating separate groups for every possible device subset, you define a filter rule that Intune evaluates at policy assignment time against each device or app.

Filters do not replace group assignments. You still assign a policy to a group, then optionally attach a filter to that assignment to include or exclude specific devices within the group.

**How filters work:**
- **Include filter:** The policy applies only to devices in the group that match the filter rule.
- **Exclude filter:** The policy applies to all devices in the group **except** those that match the filter rule.

Filters are evaluated dynamically — a device is assessed against the filter rule each time it checks in with Intune. No static membership to maintain.

---

### Filter Types in This Baseline

This baseline includes two filter management types:

| Management Type | What it filters | Used on |
| :--- | :--- | :--- |
| **devices** | Device-level properties (`device.deviceOwnership`, `device.enrollmentProfileName`) | Device configuration profiles, compliance policies, app assignments |
| **apps** | App-level properties (`app.deviceManagementType`) | App protection policies (MAM), app assignments |

---

### Baseline Assignment Filters

#### AND-COFM-DEV — Corporate-Owned Fully Managed Devices

| Property | Value |
| :--- | :--- |
| **Display Name** | AND-COFM-DEV |
| **Platform** | androidForWork |
| **Management Type** | devices |
| **Description** | Device Assignment Filter for Android Enterprise Corporate-Owned Fully Managed Devices |
| **Rule** | `(device.deviceOwnership -eq "Corporate") and (device.enrollmentProfileName -eq "AND - Corporate-Fully-Managed-Devices-Enrollment") or (device.enrollmentProfileName -eq "AND - Corporate-Fully-Managed-Devices-Enrollment - Staging")` |

**When to use:** Apply this filter (Include) on policies targeting Fully Managed devices when you want to ensure that only devices enrolled through the Fully Managed enrollment profiles are targeted — even if a broader group is used for assignment.

> **Note:** This filter uses `or` between the two enrollment profile conditions. It matches any device that is Corporate-owned AND enrolled via the Default profile, OR any device enrolled via the Staging profile (regardless of ownership). Review the logic carefully if you have other corporate enrollment profiles in your tenant.

---

#### AND-COWP-DEV — Corporate-Owned Work Profile Devices

| Property | Value |
| :--- | :--- |
| **Display Name** | AND-COWP-DEV |
| **Platform** | androidForWork |
| **Management Type** | devices |
| **Description** | Device Assignment Filter for Android Enterprise Corporate-Owned Work Profile Devices |
| **Rule** | `(device.enrollmentProfileName -eq "AND - Corporate-Corporate-Owned-Work-Profile-Enrollment") or (device.enrollmentProfileName -eq "AND - Corporate-Corporate-Owned-Work-Profile-Enrollment - Staging")` |

**When to use:** Apply this filter (Include) on policies targeting Corporate-Owned Work Profile devices. Matches devices enrolled via either the Default or Staging Corporate-Owned Work Profile enrollment profiles.

---

#### AND-POWP-DEV — Personally-Owned Work Profile Devices (BYOD)

| Property | Value |
| :--- | :--- |
| **Display Name** | AND-POWP-DEV |
| **Platform** | androidForWork |
| **Management Type** | devices |
| **Description** | Device Assignment Filter for Personally-Owned Devices with Work Profile (BYOD) |
| **Rule** | `(device.deviceOwnership -eq "Personal")` |

**When to use:** Apply this filter (Include) to target only personally-owned (BYOD) devices within a broader group. Useful when a compliance policy or configuration profile is assigned to a mixed group containing both corporate and personal devices.

This is the simplest filter in the baseline — it relies only on device ownership, not on enrollment profile name.

---

#### AND-UMGD-USR — Unmanaged Devices (MAM / App-level)

| Property | Value |
| :--- | :--- |
| **Display Name** | AND-UMGD-USR |
| **Platform** | androidMobileApplicationManagement |
| **Management Type** | apps |
| **Description** | App Assignment Filter for Android Enterprise Personally-Owned Devices with Work-Profile BYOD Users |
| **Rule** | `(app.deviceManagementType -eq "Unmanaged")` |

**When to use:** Apply this filter (Include) on App Protection Policies and MAM-targeted app assignments to ensure they apply only to unmanaged (non-enrolled) devices. This prevents double-targeting devices that are already MDM-enrolled with a separate work profile policy.

> **Important:** This filter uses the `apps` management type. It can only be applied to app-level assignments (App Protection Policies, app assignments). It cannot be applied to device configuration profiles or compliance policies.

---

### Service Limits

| Limit | Value |
| :--- | :--- |
| **Maximum filters per tenant** | 200 |
| **Maximum characters per filter rule** | 3,072 |

> **Note:** Both limits are per-tenant and apply across all platforms (Android, iOS/iPadOS, Windows, macOS). If your tenant manages multiple platforms, filters from all platforms count toward the 200-filter limit.

---

### How to Apply a Filter in the Intune Admin Center

1. Navigate to the policy or app you want to assign.
2. Go to **Assignments**.
3. Select **Add groups** (or edit an existing group assignment).
4. Choose the target group.
5. Under **Filter**, click **Edit filter**.
6. Select **Include filtered devices in assignment** or **Exclude filtered devices from assignment**.
7. Choose the relevant filter from the list.
8. Save the assignment.

---

### Filter Reference Summary

| Filter Name | Targets | Rule Logic | Typical Use |
| :--- | :--- | :--- | :--- |
| AND-COFM-DEV | Devices | Corporate-owned + Fully Managed enrollment profile (Default or Staging) | Fully Managed device policies |
| AND-COWP-DEV | Devices | Corp-Owned Work Profile enrollment profile (Default or Staging) | Corporate Work Profile policies |
| AND-POWP-DEV | Devices | Personally-owned (`device.deviceOwnership -eq "Personal"`) | BYOD Work Profile policies |
| AND-UMGD-USR | Apps | Unmanaged device (`app.deviceManagementType -eq "Unmanaged"`) | MAM / App Protection Policies |

---

> **Tip:** Filters are evaluated at policy check-in time, not at group membership evaluation time. A device that changes enrollment profile or ownership status will automatically be re-evaluated against the filter on its next check-in.
