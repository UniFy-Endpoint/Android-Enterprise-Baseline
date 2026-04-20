# Managing Android Devices with Microsoft Intune
## Android Enterprise Dedicated Devices – Kiosk Mode

### Overview
Dedicated devices are corporate-owned devices intended for a single purpose (e.g., digital signage, ticket scanning). They are locked to specific apps.

### Token Types: Default vs. Shared

- When creating an enrollment profile, you must choose a token type.

| Feature | Corporate-owned dedicated device (Default) | Corporate-owned dedicated device with Microsoft Entra shared mode |
| :--- | :--- | :--- |
| **User Identity** | No user or single generic user. | Multiple users signing in with Entra ID. |
| **Usage** | Kiosk, Digital Signage, Task-specific. | Shift workers (Nurses, Retail) sharing devices. |
| **Session** | Continuous session. | Individual sessions with sign-in/sign-out. |
| **Data** | Data persists or is cleared via policy. | Data/tokens cleared between users. |


### Configuration: Kiosk Mode

#### 1. Create a Microsoft Entra ID Groups

- Use Enrollment Time Grouping. Create and Name the Entra ID group according to your deployment type (Single-App - Kiosk or Multi-App - Kiosk).

1.  **Create Group:** Go to Entra ID > Groups > New Group.
    * **Type:** Security. **Membership type:** Assigned
    * **Name:** `AND - DEV - Android-Corporate-Dedicated-Devices - Single-App - Kiosk` Or `AND - DEV - Android-Corporate-Dedicated-Devices - Multi-App - Kiosk`
    * **Description:**  This group includes all Android Enterprise Corporate-Owned Dedicated Devices in Kiosk-Mode.
2.  **Assign Owner:** This is critical. Assign the **Intune Provisioning Client** (AppID `f1346770-5b25-470b-88bd-d5744ab7952c`) as the **Owner** of this group.

 
#### 2. Enrollment Profile
- Create and name the Enrollment Profile according to your deployment type (Single-App - Kiosk or Multi-App - Kiosk)
1.  Go to **Devices** > **Android** > **Enrollment**.
2.  Select **Corporate-owned dedicated devices**.
3.  **Create policy**:
    * **Name:** `AND - Dedicated-Devices-Enrollment - Single-App - Kiosk` Or `AND - Dedicated-Devices-Enrollment - Multi-App - Kiosk`
    * **Description:** `Enrollment Token for Android Corporate-Owned Dedicated Devices in Single-App - Kiosk Mode.` Or `Enrollment Token for Android Corporate-Owned Dedicated Devices in Multi-App - Kiosk Mode.`
    * **Token type:** Corporate-owned dedicated device (Default).
    * **Token expiration:** Set to 90 days (max).
    * **Apply device name template:** Yes
    * **Device name template:** e.g., `AND-KIOSK-{{SERIAL}}`
4.  **Device Group:** Select the Entra ID group created in Step 1.
5.  **Create**.


#### 3. Managed Google Play Apps

- You need apps for your kiosk (e.g., Microsoft Edge or a custom app).

1.  Go to **Apps** > **Android** > **Create** > **Managed Google Play app**.
2.  Search for and approve **Microsoft Edge** 
    - Add your required applications and ensure the Microsoft Entra group is assigned to all apps that need to be published on the Multi-app Kiosk start screen. Examples include: Microsoft Outlook, Teams, Authenticator, Edge, OneDrive, and SharePoint Apps.
3.  **Select** the Managed Google Play app you want to add, then click on **Sync**.
4.  **Assign** the apps to your Entra ID group created in Step 1 as **Required**.


#### 4. Compliance Policy
- Define requirements (e.g., minimum OS, root status) for the device to be considered compliant.

1.  Go to **Devices** > **Android** > **Compliance** > **Create policy**.
2.  **Platform:** Android Enterprise.
3.  **Profile type:** Fully managed, dedicated, and corporate-owned work profile.
4.  **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
5.  **Description** This Compliance Policy applies to Android Enterprise Corporate-Owned-Dedicated-Devices in Kiosk Mode.
6.  **Compliance settings:** `Adapt and configure each setting according to your organization's and customer requirements after importing the baseline policy configuration .JSON file.`

> **Security baseline note:** The compliance settings in the imported JSON are grounded in the [Android CIS Benchmarks](https://www.cisecurity.org/benchmark/google_android) and Microsoft's [Android Enterprise security configuration framework](https://learn.microsoft.com/en-us/mem/intune/enrollment/android-configuration-framework). Review and adapt all settings to your organization's and customer's security requirements before deploying.
>
> **Google Play Integrity – September 2025 change:** As of September 30, 2025, Google's Strong Integrity check on Android 13+ requires hardware-backed security signals **and** a security patch issued within the past 12 months. The baseline compliance policies use Basic Integrity attestation. If your organization requires Strong Integrity, also configure **Minimum security patch level** in the compliance policy (recommended: rolling 12-month window, format `YYYY-MM-DD`).

7.  **Actions for noncompliance:** Set "Mark device noncompliant" to "Immediately" or "1 days".
8.  **Assignments:** Assign to the Entra ID group created in Step 1.
9.  **Create**.


#### 5. Configuration Profile (Single-App Kiosk)
Locks the device to one specific app.

1.  Go to **Devices** > **Android** > **Configuration** > **Create**.
2.  **Platform:** Android Enterprise.
3.  **Profile Type:** Templates > **Device restrictions** (Fully Managed, Dedicated...).
    **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
5.  **Description** Device Restrictions Configuration Policy for Android Enterprise Corporate-Owned Dedicated Devices Single-App Kiosk-Mode.
4.  **Settings** > **Device Experience**:
    * **Enrollment type:** Dedicated device.
    * **Kiosk mode:** Single app.
    * **Select app:** Microsoft Edge.
5.  **Assign** to the Entra ID group.


#### 6. Configuration Profile (Multi-App Kiosk)
Allows a menu of specific apps. Requires "Managed Home Screen" app.

1.  Create a **Device restrictions** profile as above.
    **Platform:** Android Enterprise.
3.  **Profile Type:** Templates > **Device restrictions** (Fully Managed, Dedicated...).
    **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
5.  **Description** Device Restrictions Configuration Policy for Android Enterprise Corporate-Owned Dedicated Devices Multi-App - Kiosk-Mode.
2.  **Settings** > **Device Experience**: `Adapt and configure each setting according to your organization's and customer requirements after importing the baseline policy configuration .JSON file.`
    * **Device experience type:** Kiosk mode (dedicated and fully managed)
    * **Kiosk mode:** Multi-app.
    * **Custom app layout:** Enabled.
    * **Grid Size:** e.g., 4x5.
    * **Home screen:** Add the allowed apps.
3.  **Assign** Assign to the the Entra ID group created in Step 1.

> **Managed Home Screen — additional configuration:** The baseline Device Restriction policy configures core MHS behavior (kiosk mode, app grid layout, sign-in). For more granular control — such as wallpaper, lock screen, accessibility options, or session timeout — you can optionally deploy a dedicated **App Configuration Policy** targeting the Managed Home Screen app (`com.microsoft.launcher.enterprise`). Use the Configuration Designer with Managed devices and target the same device group.
>
> **Note:** As of the Intune August 2024 (2408) service release, Managed Home Screen is also supported on **Fully Managed** devices, in addition to Dedicated devices.

---

### Enrollment Steps
1.  Turn on a factory-reset device.
2.  Tap the "Welcome" screen 6 times in the same spot.
3.  The camera will open. Scan the **QR Code** from the Enrollment Profile token.
4.  Connect to Wi-Fi.
5.  The device will download the configuration and enroll automatically into Kiosk mode.