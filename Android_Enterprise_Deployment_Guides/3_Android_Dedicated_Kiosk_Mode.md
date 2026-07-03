# Managing Android Devices with Microsoft Intune

## Android Enterprise Dedicated Devices – Kiosk Mode

### Overview

Dedicated devices are corporate-owned, single-purpose devices intended for a specific task or fixed set of applications. They are not associated with a single user — instead, they run continuously in a controlled environment. Typical use cases include digital signage, retail point-of-sale terminals, inventory scanners, and ticketing kiosks.

Intune supports two kiosk configurations for dedicated devices:
- **Single-App Kiosk** — the device runs one app in full screen and nothing else is accessible.
- **Multi-App Kiosk** — users can interact with a defined set of apps presented through the Managed Home Screen launcher.



---

### Token Types: Default vs. Shared Mode

When creating a dedicated device enrollment profile, you choose a token type that determines how users interact with the device.

| Feature | Corporate-Owned Dedicated Device (Default) | Corporate-Owned Dedicated Device with Microsoft Entra Shared Mode |
| :--- | :--- | :--- |
| **User Identity** | No user, or a single generic user identity. | Multiple users signing in with individual Entra ID accounts. |
| **Usage** | Kiosk, digital signage, single-purpose tasks. | Shift workers (nurses, retail staff) sharing devices. |
| **Session** | Continuous session — no sign-in or sign-out. | Individual sessions with sign-in and sign-out per user. |
| **Data** | Data persists or is cleared by policy. | Tokens and data are cleared between user sessions. |
| **Apps Installed Automatically** | Microsoft Intune app. | Microsoft Intune app, Microsoft Authenticator, and Intune Company Portal. |

---

### Configuration: Kiosk Mode

#### 1. Create Microsoft Entra ID Groups

Dedicated devices use **Enrollment Time Grouping** — the device is placed into a group automatically during enrollment based on the enrollment profile. Create a separate group for each kiosk deployment type.

1. Go to **Microsoft Entra admin center** > **Groups** > **New group**.
   * **Group type:** Security
   * **Membership type:** Assigned
   * **Name:** `AND - DEV - Android-Corporate-Dedicated-Devices - Single-App - Kiosk`
     *or* `AND - DEV - Android-Corporate-Dedicated-Devices - Multi-App - Kiosk`
   * **Description:** This group includes all Android Enterprise Corporate-Owned Dedicated Devices in Single-App - Kiosk *(or Multi-App - Kiosk)*.

2. **Assign Owner — this step is required for Enrollment Time Grouping to work.** Assign the **Intune Provisioning Client** service principal (App ID: `f1346770-5b25-470b-88bd-d5744ab7952c`) as the **Owner** of this group. Without this, the device will not be automatically placed into the group during enrollment.


#### 2. Enrollment Profile

Create a separate enrollment profile for each kiosk type (single-app or multi-app). The profile generates a token and QR code used to provision devices.

1. Go to **Devices** > **Android** > **Enrollment**.
2. Select **Corporate-owned dedicated devices**.
3. Select **Create profile** and configure:
   * **Name:** `AND - Dedicated-Devices-Enrollment - Single-App - Kiosk`
     *or* `AND - Dedicated-Devices-Enrollment - Multi-App - Kiosk`
   * **Description:** Enrollment token for Android Enterprise Corporate-Owned Dedicated Devices in Single-App - Kiosk. *(or Multi-App - Kiosk)*
   * **Token type:** Corporate-owned dedicated device (Default).
   * **Token expiration date:** Set according to your lifecycle needs. Tokens can be set up to 65 years in the future (format: `MM/DD/YYYY` or `YYYY-MM-DD`).
   * **Apply device name template:** Yes.
   * **Device name template:** e.g., `AND-KIOSK-{{SERIAL}}`
4. **Device group:** Select the Entra ID group created in Step 1.
5. **Create**.

**Accessing the enrollment token:**
After creating the profile, navigate to the profile and select **Token** to view the 20-digit token string and QR code. Token management options:
- **Replace token** — generate a new token before the current one expires.
- **Revoke token** — immediately invalidate the token (use if accidentally exposed).
- **Export token** — export the JSON payload for use with Google Zero Touch or Samsung Knox Mobile Enrollment.


#### 3. Managed Google Play Apps

Add the apps that should appear on the kiosk device.

1. Go to **Apps** > **Android** > **Add** > **Managed Google Play app**.
2. Search for and approve the apps required for your kiosk scenario. Examples:
   - **Microsoft Edge** (browser-based kiosk)
   - **Microsoft Teams**, **Outlook**, **Authenticator**, **OneDrive** (productivity kiosk)
   - Any approved line-of-business apps
3. After approving each app, select **Sync**.
4. Assign apps to the Entra ID group created in Step 1 as **Required**.

> **Note:** Only apps assigned as **Required** can be installed on dedicated devices. Apps not in the allowlist will not be accessible even if they appear in Managed Google Play.

> **Important:** Make sure you deploy the Android **Managed Home Screen** App and assign it to the `AND - Dedicated-Devices-Enrollment - Multi-App - Kiosk` Microsoft Entra group. The **Managed Home Screen** App is require to be deployed as the launcher for the Multi-App - Kiosk Devices.


#### 4. Compliance Policy

Define minimum security requirements for the device to be considered compliant.

1. **Import** the baseline JSON file for this compliance policy from the repository using the [IntuneManagement](https://github.com/Micke-K/IntuneManagement) tool or Microsoft Graph API. Once imported, review and adapt each setting to your organization's security requirements before assigning.
2. **Platform:** Android Enterprise.
3. **Profile type:** Fully managed, dedicated, and corporate-owned work profile.
4. **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
5. **Description:** Compliance policy for Android Enterprise corporate-owned dedicated devices in kiosk mode.
6. **Compliance settings:** Adapt and configure each setting to your organization's requirements after importing the baseline JSON file.

> **Security baseline note:** The compliance settings in the imported JSON are grounded in the [Android CIS Benchmarks](https://www.cisecurity.org/benchmark/google_android) and Microsoft's [Android Enterprise security configuration framework](https://learn.microsoft.com/en-us/mem/intune/enrollment/android-configuration-framework). Review and adapt all settings to your organization's requirements before deploying.
>
> **Google Play Integrity — September 2025 change:** As of September 30, 2025, Google's Strong Integrity check on Android 13+ requires hardware-backed security signals and a security patch issued within the past 12 months. The baseline compliance policies use Basic Integrity attestation. If your organization requires Strong Integrity, also configure **Minimum security patch level** in the compliance policy (recommended: rolling 12-month window, format `YYYY-MM-DD`).

7. **Actions for noncompliance:** Set "Mark device noncompliant" to **Immediately** or **1 day**.
8. **Assignments:** Assign to the Entra ID group created in Step 1.
9. **Create**.


#### 5. Configuration Profile — Single-App Kiosk

> **Note:** To allow users to sign in on Microsoft 365 apps (Outlook, Teams, Edge) on an Android Dedicated device, you must use Single-App Kiosk Mode or Shared Device Mode. These apps require a "Broker" (Microsoft Authenticator) to handle the login. In a strict Single-App Kiosk, the system blocks the Authenticator from appearing.

Locks the device to one specific app running in full screen. No other app or system UI is accessible.

As of Intune June 2026 (2606), Settings Catalog (SC) support was extended to Dedicated Devices. The baseline device restrictions are now split across **5 SC subcategory policies** and **1 DC Additional-Settings policy** per kiosk type. Import all 6 policies from the `ConfigurationsPolicies/Dedicated-Devices - Single-App-Kiosk/` folder and assign each to the same Entra ID group.

**SC subcategory policies — import via Settings Catalog:**

For each of the 5 SC policies (`Applications`, `Connectivity`, `Device-Password`, `General-Settings`, `System-Security`):

1. **Import** the baseline JSON file using [IntuneManagement](https://github.com/Micke-K/IntuneManagement) or Microsoft Graph API.
2. **Platform:** Android Enterprise.
3. **Profile type:** Settings Catalog.
4. **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for each policy name.)*
5. **Assignments:** Assign to the Entra ID group created in Step 1.
6. **Create**.

**DC Additional-Settings policy — import via Templates:**

1. **Import** the baseline JSON file (`AND - DC - DR - DEV - Additional-Settings - Corp-Dedicated - Single-App-Kiosk - v1.5`) using [IntuneManagement](https://github.com/Micke-K/IntuneManagement) or Microsoft Graph API.
2. **Platform:** Android Enterprise.
3. **Profile type:** Templates > **Device restrictions** (Fully Managed, Dedicated, and Corporate-Owned Work Profile).
4. Navigate to **Settings** > **Device Experience**:
   * **Enrollment type:** Dedicated device.
   * **Kiosk mode:** Single app.
   * **Select an app to use for kiosk mode:** Choose the kiosk app (e.g., Microsoft Edge).
5. **Assignments:** Assign to the Entra ID group created in Step 1.
6. **Create**.

> **Important:** Do not assign both an SC and a DC policy that configure the same setting to the same device. The SC subcategory policies and the DC Additional-Settings policy are designed to have no overlapping settings. All SC-managed settings are explicitly set to `null` in the DC policy.


#### 6. Configuration Profile — Multi-App Kiosk

Managed Home Screen must be previously configured as the device launcher for Multi-App - Kiosk mode to function.

The baseline device restrictions are split across **5 SC subcategory policies** and **1 DC Additional-Settings policy**. Import all 6 policies from the `ConfigurationsPolicies/Dedicated-Devices - Multi-App-Kiosk/` folder and assign each to the same Entra ID group.

**SC subcategory policies — import via Settings Catalog:**

For each of the 5 SC policies (`Applications`, `Connectivity`, `Device-Password`, `General-Settings`, `System-Security`):

1. **Import** the baseline JSON file using [IntuneManagement](https://github.com/Micke-K/IntuneManagement) or Microsoft Graph API.
2. **Platform:** Android Enterprise.
3. **Profile type:** Settings Catalog.
4. **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for each policy name.)*
5. **Assignments:** Assign to the Entra ID group created in Step 1.
6. **Create**.

**DC Additional-Settings policy — import via Templates:**

1. **Import** the baseline JSON file (`AND - DC - DR - DEV - Additional-Settings - Corp-Dedicated - Multi-App-Kiosk - v1.5`) using [IntuneManagement](https://github.com/Micke-K/IntuneManagement) or Microsoft Graph API.
2. **Platform:** Android Enterprise.
3. **Profile type:** Templates > **Device restrictions** (Fully Managed, Dedicated, and Corporate-Owned Work Profile).
4. Navigate to **Settings** > **Device Experience** and adapt settings after importing:
   * **Device experience type:** Kiosk mode (dedicated and fully managed).
   * **Kiosk mode:** Multi-app.
   * **Custom app layout:** Enabled.
   * **Grid size:** e.g., 4×5.
   * **Home screen:** Add the approved apps to the launcher.
5. **Assignments:** Assign to the Entra ID group created in Step 1.
6. **Create**.

> **Managed Home Screen — additional configuration:** The DC Additional-Settings policy configures core kiosk behavior (launcher mode, app grid). For more granular control — such as wallpaper, session timeout, sign-in method, accessibility options, or virtual navigation buttons — deploy a dedicated **App Configuration Policy** targeting the Managed Home Screen app (`com.microsoft.launcher.enterprise`). Use the Configuration Designer with **Managed devices** and assign it to the same device group. See [Guide 10 — Managed Home Screen](10_Android_Managed_Home_Screen.md) for full configuration details.
>

---

### Enrollment Steps

1. Start with a factory-reset device and power it on.
2. On the **Welcome** screen, tap the same spot **6 times** in quick succession. This opens the QR code scanner.
3. Scan the **QR code** from the enrollment profile token (available in **Devices** > **Android** > **Enrollment** > **Corporate-owned dedicated devices** > select the profile > **Token**).
4. Connect to Wi-Fi when prompted.
5. The device downloads the configuration and enrolls automatically. It will restart and launch into kiosk mode without requiring a user account.
