# Managing Android Devices with Microsoft Intune

## Android Enterprise Dedicated Devices – Shared Mode

### Overview

Shared Mode allows multiple users to sign in and out of a single corporate-owned dedicated device using their individual Microsoft Entra ID accounts. Each session is isolated — when a user signs out, their tokens, credentials, and session data are cleared from the device, making it ready for the next user.

This is the recommended configuration for shift-based scenarios such as nursing stations, retail floor devices, and warehouse terminals where a device is shared across a team but each person needs their own identity and app access.

Shared Mode uses the **Microsoft Entra Shared Device Mode** capability and requires apps that support this mode (such as Microsoft Teams, Outlook, and Edge).

---

### Requirements

* Compatible Android device running **Android 8.0 or later**.
* An enrollment profile with the **Shared mode** token type.
* An assigned Entra ID group configured for **Enrollment Time Grouping**.
* Apps that support Microsoft Entra Shared Device Mode (e.g., Teams, Outlook, Edge, Managed Home Screen).
* **Microsoft Authenticator** deployed as a required app — it provides the sign-in broker for shared sessions.

---

### Configuration

#### 1. Create Microsoft Entra ID Group

Shared mode devices use **Enrollment Time Grouping**, so the device is placed into the group automatically at enrollment time based on the profile.

1. Go to **Microsoft Entra admin center** > **Groups** > **New group**.
   * **Group type:** Security
   * **Membership type:** Assigned
   * **Name:** `AND - DEV - Android-Corporate-Dedicated-Devices - Shared`
   * **Description:** This group includes all Android Enterprise corporate-owned dedicated devices in Shared Mode.

2. **Assign Owner — this step is required.** Assign the **Intune Provisioning Client** service principal (App ID: `f1346770-5b25-470b-88bd-d5744ab7952c`) as the **Owner** of this group. Without this, Enrollment Time Grouping will not function and the device will not be placed in the group automatically.


#### 2. Enrollment Profile

1. Go to **Devices** > **Android** > **Enrollment**.
2. Select **Corporate-owned dedicated devices**.
3. Select **Create profile** and configure:
   * **Name:** `AND - Dedicated-Devices-Enrollment - Shared`
   * **Description:** Enrollment token for Android Enterprise corporate-owned dedicated devices in Shared Mode.
   * **Token type:** Corporate-owned dedicated device with Microsoft Entra shared mode.
   * **Token expiration date:** Set according to your lifecycle needs. Tokens can be set up to 65 years in the future (format: `MM/DD/YYYY` or `YYYY-MM-DD`).
   * **Apply device name template:** Yes.
   * **Device name template:** e.g., `AND-SHARED-{{SERIAL}}`
4. **Device group:** Select the Entra ID group created in Step 1.
5. **Create**.

> **Note:** When the Shared Mode token type is selected, Microsoft Authenticator and Intune Company Portal are automatically installed on the device during enrollment in addition to the Microsoft Intune app. These cannot be uninstalled.


#### 3. Managed Google Play Apps

1. Go to **Apps** > **Android** > **Add** > **Managed Google Play app**.
2. Search for and approve the following apps:
   * **Microsoft Managed Home Screen** (required — provides the sign-in/sign-out launcher UI)
   * **Microsoft Authenticator** (required — acts as the sign-in broker for shared sessions)
   * Additional work apps as needed: **Microsoft Teams**, **Outlook**, **Edge**, **OneDrive**, **SharePoint**
3. After approving each app, select **Sync**.
4. Assign all apps to the Entra ID group created in Step 1 as **Required**.

> **Note:** Ensure all deployed apps support Microsoft Entra Shared Device Mode. Apps that do not support shared mode will retain session data between users and must not be deployed to shared devices.


#### 4. Compliance Policy

1. Go to **Devices** > **Android** > **Compliance** > **Create policy**.
2. **Platform:** Android Enterprise.
3. **Profile type:** Fully managed, dedicated, and corporate-owned work profile.
4. **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
5. **Description:** Compliance policy for Android Enterprise corporate-owned dedicated devices in Shared Mode.
6. **Compliance settings:** Adapt and configure each setting to your organization's requirements after importing the baseline JSON file.

> **Security baseline note:** The compliance settings in the imported JSON are grounded in the [Android CIS Benchmarks](https://www.cisecurity.org/benchmark/google_android) and Microsoft's [Android Enterprise security configuration framework](https://learn.microsoft.com/en-us/mem/intune/enrollment/android-configuration-framework). Review and adapt all settings to your organization's requirements before deploying.
>
> **Google Play Integrity — September 2025 change:** As of September 30, 2025, Google's Strong Integrity check on Android 13+ requires hardware-backed security signals and a security patch issued within the past 12 months. The baseline compliance policies use Basic Integrity attestation. If your organization requires Strong Integrity, also configure **Minimum security patch level** in the compliance policy (recommended: rolling 12-month window, format `YYYY-MM-DD`).

7. **Actions for noncompliance:** Set "Mark device noncompliant" to **Immediately** or **1 day**.
8. **Assignments:** Assign to the Entra ID group created in Step 1.
9. **Create**.


#### 5. Configuration Profile

Managed Home Screen must be configured as the device launcher for shared mode to function. The Device Restrictions policy sets this up.

1. Go to **Devices** > **Android** > **Configuration** > **Create**.
2. **Platform:** Android Enterprise.
3. **Profile type:** Templates > **Device restrictions** (Fully Managed, Dedicated, and Corporate-Owned Work Profile).
4. **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
5. **Description:** Device restrictions for Android Enterprise corporate-owned dedicated devices in Shared Mode.
6. Navigate to **Settings** > **Device Experience** and adapt settings after importing the baseline JSON file:
   * **Device experience type:** Kiosk mode (dedicated and fully managed).
   * **Kiosk mode:** Multi-app.
   * **Custom app layout:** Enabled.
   * **Grid size:** e.g., 4×5.
   * **Home screen:** Add the approved apps.
7. **Assignments:** Assign to the Entra ID group created in Step 1.
8. **Create**.

> **Managed Home Screen — additional configuration:** The Device Restrictions policy configures core Shared Mode behavior (multi-app kiosk, Entra ID sign-in and sign-out, grid layout). For more granular control — such as session timeout, auto sign-out timer, inactivity screen, wallpaper, or virtual home button style — deploy a dedicated **App Configuration Policy** targeting the Managed Home Screen app (`com.microsoft.launcher.enterprise`). Use the Configuration Designer with **Managed devices** and assign it to the same device group. See [Guide 10 — Managed Home Screen](10_Android_Managed_Home_Screen.md) for full configuration details.

---

### Enrollment Steps

1. Start with a factory-reset device and power it on.
2. On the **Welcome** screen, tap the same spot **6 times** in quick succession to open the QR code scanner.
3. Scan the **Shared Mode QR code** from the enrollment profile token.
4. Connect to Wi-Fi when prompted.
5. Follow the on-screen prompts to install work apps.
6. When the **Register shared device** screen appears, tap **Set up**.
7. The device configures Shared Mode and restarts. Once complete, the Managed Home Screen appears with a **Sign In** button.

---

### User Experience

Once enrolled, the daily user flow on a Shared Mode device is:

1. User taps **Sign In** on the Managed Home Screen.
2. User enters their Microsoft 365 credentials (or scans a QR code if configured).
3. The session loads with their apps, including Teams, Outlook, and any other assigned apps, using Single Sign-On (SSO).
4. When the shift ends, the user taps **Sign Out** from the Managed Home Screen menu.
5. The device clears all session tokens and personal data and returns to the sign-in screen, ready for the next user.
