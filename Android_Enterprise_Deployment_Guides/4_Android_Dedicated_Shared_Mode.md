# Managing Android Devices with Microsoft Intune

## Android Enterprise Dedicated Devices – Shared Mode

### Overview
Shared Mode allows multiple users to sign in and out of a corporate-owned device (e.g., for shift workers). It uses the Microsoft Entra Shared Device Mode to clear user data between sessions.

### Requirements
* Supported Android device (13.0+).
* Enrollment Profile (Shared mode token).
* Enrollment Time Grouping (Assigned group).
* Apps that support Shared Mode (e.g., Teams, Outlook, Edge, Managed Home Screen).

### Configuration

#### 1. Create Microsoft Entra ID Group
- Use Enrollment Time Grouping.

1. **Create Group:** Go to Entra ID > Groups > New Group.
    * **Type:** Security. **Membership type:** Assigned
    * **Name:** `AND - DEV - Android-Corporate-Dedicated-Devices - Shared`
    * **Description:**  This group includes all Android Enterprise Corporate-Owned Dedicated Devices in Shared Mode.

2.  **Assign Owner:** This is critical. Assign the **Intune Provisioning Client** (AppID `f1346770-5b25-470b-88bd-d5744ab7952c`) as the **Owner** of this group.


#### 2. Enrollment Profile

1.  Go to **Devices** > **Android** > **Enrollment** 
2.  Select **Corporate-owned dedicated devices**.
3.  **Create policy**:
    * **Name:** `AND - Dedicated-Devices-Enrollment - Shared`
    * **Description:** `Enrollment Token for Android Corporate-Owned Dedicated Devices in Shared Mode.`
    * **Token type:** Corporate-owned dedicated device with Microsoft Entra shared mode.
    * **Token expiration:** Set to 90 days (max).
    * **Apply device name template:** Yes
    * **Device name template:** e.g., `AND-SHARED-{{SERIAL}}`
4.  **Device Group:** Select the Entra ID group created in Step 1.
5.  **Create**.


#### 3. Managed Google Play Apps

1.  Go to **Apps** > **Android** > **Create** > **Managed Google Play app**.
2.  Search for and approve 
* **Managed Home Screen** (Essential).
**Microsoft Authenticator** (Essential for sign-in). 
- Add your required applications and ensure the Microsoft Entra group is assigned to all apps that need to be published on the Shared-Device start screen. Examples include: Microsoft Outlook, Teams, Authenticator, Edge, OneDrive, and SharePoint Apps.
3.  **Select** the Managed Google Play app you want to add, then click on **Sync**.
4.  **Assign** the apps to your Entra ID group created in Step 1 as **Required**.

> **Note:** Ensure selected apps support Shared Device Mode.


#### 4. Compliance Policy
- Define requirements (e.g., minimum OS, root status) for the device to be considered compliant.

1.  Go to **Devices** > **Android** > **Compliance** > **Create policy**.
2.  **Platform:** Android Enterprise.
3.  **Profile type:** Fully managed, dedicated, and corporate-owned work profile.
4.  **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
5.  **Description** This Compliance Policy applies to Android Enterprise Corporate-Owned-Dedicated-Devices in Shared Mode.
6.  **Compliance settings:** `Adapt and configure each setting according to your organization's and customer requirements after importing the baseline policy configuration .JSON file.`

> **Security baseline note:** The compliance settings in the imported JSON are grounded in the [Android CIS Benchmarks](https://www.cisecurity.org/benchmark/google_android) and Microsoft's [Android Enterprise security configuration framework](https://learn.microsoft.com/en-us/mem/intune/enrollment/android-configuration-framework). Review and adapt all settings to your organization's and customer's security requirements before deploying.
>
> **Google Play Integrity – September 2025 change:** As of September 30, 2025, Google's Strong Integrity check on Android 13+ requires hardware-backed security signals **and** a security patch issued within the past 12 months. The baseline compliance policies use Basic Integrity attestation. If your organization requires Strong Integrity, also configure **Minimum security patch level** in the compliance policy (recommended: rolling 12-month window, format `YYYY-MM-DD`).

7.  **Actions for noncompliance:** Set "Mark device noncompliant" to "Immediately" or "1 days".
8.  **Assignments:** Assign to the Entra ID group created in Step 1.
9.  **Create**.


#### 5. Configuration Profile

- Managed Home Screen must be configure.

1.  Create a **Device restrictions** profile as above.
    **Platform:** Android Enterprise.
3.  **Profile Type:** Templates > **Device restrictions** (Fully Managed, Dedicated...).
    **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
5.  **Description** Device Restrictions Configuration Policy for Android Enterprise Corporate-Owned-Dedicated Managed Home Screen Shared-Mode Devices.
2.  **Settings** > **Device Experience**: `Adapt and configure each setting according to your organization's and customer requirements after importing the baseline policy configuration .JSON file.`
    * **Device experience type:** Kiosk mode (dedicated and fully managed)
    * **Kiosk mode:** Multi-app.
    * **Custom app layout:** Enabled.
    * **Grid Size:** e.g., 4x5.
    * **Home screen:** Add the allowed apps.
    * **Device experience** `To ensure a correct sign-in/sign-out experience and layout, verify the additional settings in the imported device restrictions JSON policy file`
3.  **Assign** Assign to the the Entra ID group created in Step 1.

> **Managed Home Screen — additional configuration:** The baseline Device Restriction policy configures core Shared Mode MHS behavior (multi-app kiosk, Entra ID sign-in/sign-out, auto-signout timer, grid layout). For more granular control — such as session timeout, wallpaper, managed lock screen, or virtual home button style — you can optionally deploy a dedicated **App Configuration Policy** targeting the Managed Home Screen app (`com.microsoft.launcher.enterprise`). Use the Configuration Designer with Managed devices and target the same device group.

---

### Enrollment Steps
1.  Tap the Welcome screen 6 times on a fresh device.
2.  Scan the **Shared Mode QR Code**.
3.  Connect to Wi-Fi.
4.  Follow prompts to install work apps.
5.  **Final Step:** Tap **Set up** on the "Register shared device" screen.
6.  The device will configure Shared Mode. Once done, the Managed Home Screen appears with a **Sign In** button.


### User Experience
* User taps **Sign In**.
* Enters Microsoft 365 credentials.
* Accesses apps (Teams, Outlook) with Single Sign-On (SSO).
* When finished, user taps **Sign Out** (or uses the menu).
* Device clears session data and is ready for the next user.