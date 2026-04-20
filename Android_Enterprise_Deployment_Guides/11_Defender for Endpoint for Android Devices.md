# Managing Android Devices with Microsoft Intune

## Microsoft Defender for Endpoint for Android Devices

### Overview

Microsoft Defender for Endpoint (MDE) for Android is a mobile threat defense solution that integrates with Microsoft Intune and Conditional Access to extend security to Android Enterprise enrolled devices. It provides:

- **Anti-phishing** protection against malicious links in emails, apps, and websites.
- **Network protection** to block unsafe or suspicious network connections.
- **Malware and PUA scanning** to detect and remediate threats on the device.
- **Web Protection** delivered through a local self-looping VPN (no external traffic routing).
- **Breach prevention** capabilities through Intune and Conditional Access integration.
- **MAM support** for unenrolled devices using App Protection Policies.

All alerts and threat signals are surfaced in the Microsoft Defender portal.

#### Supported Enrollment Types

| Enrollment Type | Supported |
| :--- | :--- |
| Personally-Owned Work Profile (BYOD) | Yes |
| Corporate-Owned Work Profile | Yes |
| Fully Managed (User-Driven and Staging) | Yes |
| Dedicated / Kiosk | No (user-less/shared devices not supported) |
| Dedicated Shared Mode | No (user-less/shared devices not supported) |
| MAM (unmanaged/unenrolled) | Yes |

> **Note:** Android Go devices and devices without Google Mobile Services (GMS) are not supported.

---

### 1. Licensing Requirements

Users must have **both** a Microsoft Defender for Endpoint license and a Microsoft Intune license.

**Microsoft Defender for Endpoint license (one of the following):**
- Microsoft Defender for Endpoint Plan 1 or Plan 2
- Microsoft Defender for Business
- Microsoft 365 Business Premium

**Microsoft Intune license (one of the following):**
- Microsoft 365 E5
- Microsoft 365 E3
- Enterprise Mobility + Security E5
- Enterprise Mobility + Security E3
- Microsoft 365 Business Premium

---

### 2. Prerequisites

#### For Enrolled Devices (MDM)

- Devices are already enrolled in Intune.
- Microsoft Authenticator App and Intune Company Portal App are installed on the device.
- Android 13.0 or later.
- Microsoft Defender for Endpoint is configured as the default and only antivirus solution on the device.
- MDE is **not** supported on User-Less or Shared-Device configurations (Dedicated Kiosk, Dedicated Shared Mode).

#### For Unenrolled Devices (MAM)

- The device is registered with Microsoft Entra ID via the Microsoft Authenticator App.
- Broker App: Users must have the Company Portal app installed (it acts as the broker, though the device does not need to be enrolled).
- MDE protection is delivered through Mobile Application Management (MAM) — no device enrollment required.

#### Before You Begin

- Ensure Android enrollment is complete and users have a Defender for Endpoint license assigned.
- Company Portal and the Intune App must be installed, signed in, and enrollment completed before MDE onboarding.
- Confirm the target enrollment types are one of: Personally-Owned Work Profile, Corporate-Owned Work Profile, or Corporate-Owned Fully Managed.

---

### 3. Step 1: Enable the MDE–Intune Connector

Before deploying the app or policies, the connection between the Microsoft Defender portal and Microsoft Intune must be active on both sides.

#### A. Security Portal (Microsoft Defender)

1. Navigate to [https://security.microsoft.com](https://security.microsoft.com).
2. Go to **Settings** > **Endpoints** > **Advanced Features**.
3. Locate **Microsoft Intune connection** and toggle it to **On**.

#### B. Intune Portal (Endpoint Security)

1. Navigate to the **Microsoft Intune admin center**.
2. Go to **Endpoint Security** > **Microsoft Defender for Endpoint**.
3. Verify the **Connection status** shows **Enabled**.
4. Configure the following settings:

| Setting | Value |
| :--- | :--- |
| Allow Microsoft Defender for Endpoint to enforce Endpoint Security Configurations | On |
| Connect Android devices (v6.0+) to Microsoft Defender for Endpoint (Compliance) | On |
| Connect Android devices to Microsoft Defender for Endpoint (App Protection) | On |

---

### 4. Step 2: Deploy the Microsoft Defender App via Managed Google Play

The MDE app must be approved in Managed Google Play and assigned to the relevant device groups before policy configuration begins.

1. In the Microsoft Intune admin center, go to **Apps** > **Android** > **Add** > **Managed Google Play App**.
2. In the Managed Google Play search, type **Microsoft Defender**.
3. Select **Microsoft Defender: Antivirus** from the results, then click **Select** and **Sync**.
4. Return to the **Android Apps** list and click **Refresh** to confirm the app is visible.
5. Open the app and set the **Assignment** to the relevant device groups as **Required**.

> **Tip:** Assign the MDE app to the same Entra ID groups used for the enrollment profiles (e.g., `AND - DEV - Android-Corporate-Work-Profile-Devices`, `AND - DEV - Android-Personally-Owned-Work-Profile-Devices`).

---

### 5. Step 3: App Configuration Policy – Low-Touch Onboarding

The App Configuration Policy enables low-touch onboarding by pre-configuring MDE settings and permissions silently, without requiring user interaction during setup.

**Baseline policy name:** See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current policy name.

#### A. Policy Creation

1. Go to **Apps** > **App configuration policies** > **Add** > **Managed devices**.
2. Configure the following:

| Field | Value |
| :--- | :--- |
| **Name** | *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)* |
| **Description** | App Configuration Policy for Microsoft Defender for Endpoint – Low-Touch Onboarding |
| **Platform** | Android Enterprise |
| **Profile Type** | Fully Managed, Dedicated, and Corporate-Owned Work Profile **or** Personally-owned Work Profile (create separate policy per profile type if needed) |
| **Targeted App** | Microsoft Defender (select from Managed Google Play) |

#### B. Permissions

Navigate to the **Permissions** tab and add the following with **Auto grant** state:

| Permission | Permission Name | Group |
| :--- | :--- | :--- |
| Post notifications | POST_NOTIFICATIONS | NOTIFICATIONS |
| Location access (fine) | ACCESS_FINE_LOCATION | LOCATION |
| Location access (background) | ACCESS_BACKGROUND_LOCATION | LOCATION |
| External storage (read) | READ_EXTERNAL_STORAGE | STORAGE |
| External storage (write) | WRITE_EXTERNAL_STORAGE | STORAGE |

> **Note:** These permissions are not all documented by Microsoft but are required by the Defender app. Auto-granting them prevents the user from being prompted during onboarding.

#### C. Configuration Settings

In the **Configuration Settings** section, select **Use configuration designer** and add the following keys:

| Configuration Key | Value Type | Value |
| :--- | :--- | :--- |
| Anti-phishing | integer | 1 |
| Microsoft Defender | integer | 1 |
| VPN | integer | 1 |
| Enable Network Protection in Microsoft Defender | integer | 1 |
| User UPN | string | `{{userprincipalname}}` |
| Low touch onboarding | integer | 1 |
| Disable sign out | integer | 1 |
| Automatic Remediation of Network Protection Alerts | integer | 1 |
| Enable Users to Trust Networks and Certificates | integer | 0 |

> **Important:** Only one App Configuration Policy should target the same app and user. Multiple ACPs with conflicting values for the same key will not be resolved automatically, which can lead to unpredictable behavior.

#### D. Assignments

Assign to the same device groups as the MDE app (e.g., Corporate-Owned and Personally-Owned Android device groups).

---

### 6. Step 4: Always-On VPN – Device Configuration Policies

MDE on Android uses a local, self-looping VPN to deliver the Web Protection feature. This VPN does **not** route device traffic to an external server — it operates entirely on the device. It is required for network threat protection and anti-phishing to function.

> **Warning:** Only one Always-On VPN client can be active on a device at a time. Ensure no conflicting Always-On VPN policy is deployed to the same devices.

Two separate Device Configuration policies are included in the baseline — one per profile type:

- **MDE Always-On VPN — Corporate-Owned** (For: Fully Managed, Dedicated, and Corporate-Owned Work Profile devices)

- **MDE Always-On VPN — Personally-Owned** (For: Personally-Owned Work Profile devices)

*(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for current policy names.)*

#### Configuration Steps

1. Go to **Devices** > **Configuration Profiles** > **Create Profile**.
2. **Platform:** Android Enterprise.
3. **Profile type:** Templates > **Device restrictions**, then select the appropriate profile type:
   - **Fully Managed, Dedicated, and Corporate-Owned Work Profile** (for corporate devices)
   - **Personally owned Work Profile** (for BYOD)
4. Configure the **Connectivity** settings:

| Category | Setting | Value |
| :--- | :--- | :--- |
| Connectivity | Always-on VPN (work profile-level) | **Enabled** |
| Connectivity | VPN client | **Custom** |
| Connectivity | Package ID | **com.microsoft.scmx** |
| Connectivity | Lockdown mode | **Not configured** |

5. **Assignments:** Assign to the same device groups as the MDE app.

> **Note:** `com.microsoft.scmx` is the package ID for the Microsoft Defender for Endpoint app in the Google Play Store.

---

### 7. (Optional) App Protection Policy – MDE Risk Signals

MDE risk signals can be integrated into App Protection Policies to block or wipe app data based on the device's threat level. This configuration applies to both MDM-enrolled and MAM unenrolled devices.

- **MDM enrolled:** Configure Defender risk signals in App Protection Policies targeting enrolled devices.
- **MAM unenrolled:** Configure Defender risk signals via MAM App Protection Policies — Defender can be deployed to unenrolled devices to evaluate and report risk.

#### Configuration

1. Go to **Apps** > **App protection policies** > **Create policy** (or edit an existing one).
2. In the **Apps** section:
   - **Target policy to:** All device types (or select Android specifically).
   - **Target policy to apps:** Core Microsoft Apps (or specific apps).
3. In the **Conditional Launch** section, under **Device Conditions**, add:

| Setting | Value | Action |
| :--- | :--- | :--- |
| Max allowed device threat level | Low | Block access |
| Jailbroken/rooted devices | — | Block access |

4. Assign to the relevant user groups (e.g., Intune License Users dynamic group).

---

### 8. (Optional) Conditional Access Considerations

MDE integrates with Microsoft Entra ID and Intune Conditional Access to enforce device risk-based access control. When App Protection Policies or Compliance Policies include MDE risk signals, ensure the following CA exclusions are in place to avoid blocking MDE's own reporting channels.

#### Recommended CA Exclusions

Two service principals must be excluded from restrictive Conditional Access policies:

| App | App ID | Purpose |
| :--- | :--- | :--- |
| MicrosoftDefenderATP XPlat | `a0e84e36-b067-4d5c-ab4a-3db38e598ae2` | Xplat Broker App — forwards Defender risk signals to the Defender backend. Blocking this prevents MDE from reporting signals. |
| Microsoft Threat Protection TVM | `e724aa31-0f56-4018-b8be-f8cb82ca1196` | Provides vulnerability assessment for installed apps (MDVM). Required if Defender Vulnerability Management is used. |

> **Note:** Create these as service principal objects in Entra ID using the app IDs above, then exclude them from the relevant CA policies. Because XPlat Broker is also used on Mac and Linux, consider creating a separate, Android-scoped CA policy for these exclusions rather than modifying a broad multi-platform policy.

#### Recommended CA Policy (if using App Protection Policy)

If App Protection Policies are enforced via Conditional Access, the CA policy should:
- **Target:** Office 365 (or all cloud apps)
- **Platform condition:** Android
- **Client apps:** Mobile apps and desktop clients
- **Grant:** Require app protection policy
- **Excluded apps:** MicrosoftDefenderATP XPlat + Microsoft Threat Protection TVM (as service principals)

---

### References

- [Microsoft Defender for Endpoint on Android](https://learn.microsoft.com/en-us/defender-endpoint/microsoft-defender-endpoint-android)
- [Deploy Microsoft Defender for Endpoint on Android with Microsoft Intune](https://learn.microsoft.com/en-us/defender-endpoint/android-intune)
- [Microsoft Defender Mobile App exclusion from Conditional Access Policies](https://learn.microsoft.com/en-us/defender-endpoint/android-configure#microsoft-defender-mobile-app-exclusion-from-conditional-access-ca-policies)
