# Managing Android Devices with Microsoft Intune

## Corporate-Owned Work Profile - Default vs. Staging

### Overview
This scenario is for company-owned devices where personal use is allowed. It creates a Work Profile (like BYOD) but gives IT more control over the device level.

### Comparison: Default vs. Staging

| Feature | Corporate-Owned with Work Profile | Corporate-Owned with Work Profile via Staging |
| :--- | :--- | :--- |
| **Enrollment Token** | Generated per user or group (Standard). | Generated for staging account. |
| **Setup Responsibility** | End user completes full enrollment. | IT/Staging user sets up, then hands over. |
| **User Experience** | User scans QR, sets up work profile. | User receives device with Work Profile ready; just signs in. |
| **Ideal For** | Corporate devices with BYOD-feel. | Large deployments, pre-setup required. |
| **Device State Before User** | Factory reset state. | Device is enrolled; Work Profile created. |

---

### Configuration: Corp-Owned Work Profile (Default) Group

#### 1. Create Microsoft Entra ID Group

1. **Create Group:** Go to Entra ID > Groups > New Group.
    * **Type:** Security. **Membership type:** Assigned
    * **Name:** `AND - DEV - Android-Corporate-Work-Profile-Devices`
    * **Description:**  This group includes all Android Enterprise Corporate-Owned Devices with Work Profile.
4.  **Assign Owner:** This is critical. Assign the **Intune Provisioning Client** (AppID `f1346770-5b25-470b-88bd-d5744ab7952c`) as the **Owner** of this group.

---

### Configuration: Corp-Owned Work Profile via Staging Group

**Staging does NOT support Enrollment Time Grouping (Static Groups). You must use Dynamic Groups.**

1.  Go to **Entra ID** > **Groups** > **New Group**.
    * **Type:** Security.
    * **Name:** `AND - DEV - Android-Corporate-Work-Profile-Devices - Staging`
    * **Description:**  This group includes all Android Enterprise Corporate-Owned Devices with Work Profile in Staging Mode..
    * **Membership:** Dynamic Device.
2.  **Rule:** `(device.deviceOSType -eq "AndroidEnterprise") and (device.enrollmentProfileName -eq "AND - Corporate-Corporate-Owned-Work-Profile-Enrollment - Staging")`

> **Note:** The name in the rule must match the Enrollment Profile name exactly.

---

### Configuration: Corp-Owned Work Profile (Default/User-Driven) Profile

#### 2. Enrollment Profile

1.  Go to **Devices** > **Android** > **Enrollment**
2.  Select **Corporate-owned devices with work profile**.
3.  **Create policy**:
    * **Name:** `AND - Corporate-Corporate-Owned-Work-Profile-Enrollment`
    * **Description:** `Enrollment Token for Android Enterprise Corporate-Owned Devices with Work-Profile.`
    * **Token type:** Corporate-owned with work profile (default).
    * **Token expiration:** Set to 90 days (max).
    * **Apply device name template:** Yes
    * **Device name template:** e.g., `AND-CORP-WP-{{SERIAL}}`
4.  **Device Group:** Select the Entra ID group created for Corp-Owned Work Profile (Default/User-Driven) devices in Step 1.
5.  **Create**.

### Configuration: Corp-Owned Work Profile via (Staging)

1.  Go to **Devices** > **Android** > **Enrollment**
2.  Select **Corporate-owned devices with work profile**.
3.  **Create policy**:
    * **Name:** `AND - Corporate-Corporate-Owned-Work-Profile-Enrollment - Staging`
    * **Description:** `Enrollment Token for Android Enterprise Corporate-Owned with Work-Profile in Staging Mode.`
    * **Token type:** Corporate-owned with work profile, via staging.
    * **Token expiration:** Set to 90 days (max).
    * **Apply device name template:** Yes
    * **Device name template:** e.g., `AND-CORP-SWP-{{SERIAL}}`
4.  **Device Group:** Select **None** (Dynamic groups are not supported here).
5.  **Create**.

---

#### 3. Managed Google Play Apps

1.  Go to **Apps** > **Android** > **Create** > **Managed Google Play app**.
2.  Search for **Microsoft Authenticator** (Essential for sign-in). 
- Add your required applications that need to be published on the device. Examples include: Microsoft Outlook, Teams, Edge, OneDrive, and SharePoint Apps.
3.  **Select** the Managed Google Play app you want to add, then click on **Sync**.
4.  **Assignments:** to the Entra ID groups (Default/User-Driven) or (Staging-Mode) created in Step 1 as **Required**.

---

#### 4. Compliance Policy

- Define requirements (e.g., minimum OS, root status) for the device to be considered compliant.

1.  Go to **Devices** > **Android** > **Compliance** > **Create policy**.
2.  **Platform:** Android Enterprise.
3.  **Profile type:** Fully managed, dedicated, and corporate-owned work profile.
4.  **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
5.  **Description** This Compliance Policy applies to Android Enterprise Corporate-Owned Work-Profile users.
6.  **Compliance settings:** `Adapt and configure each setting according to your organization's and customer requirements after importing the baseline policy configuration .JSON file.`

> **Security baseline note:** The compliance settings in the imported JSON are grounded in the [Android CIS Benchmarks](https://www.cisecurity.org/benchmark/google_android) and Microsoft's [Android Enterprise security configuration framework](https://learn.microsoft.com/en-us/mem/intune/enrollment/android-configuration-framework). Review and adapt all settings to your organization's and customer's security requirements before deploying.
>
> **Google Play Integrity – September 2025 change:** As of September 30, 2025, Google's Strong Integrity check on Android 13+ requires hardware-backed security signals **and** a security patch issued within the past 12 months. The baseline compliance policies use Basic Integrity attestation. If your organization requires Strong Integrity, also configure **Minimum security patch level** in the compliance policy (recommended: rolling 12-month window, format `YYYY-MM-DD`).

7.  **Actions for noncompliance:** Set "Mark device noncompliant" to "Immediately" or "1 days".
8.  **Assignments:** Assign to the Entra ID groups (Default/User-Driven) or (Staging-Mode) created in Step 1.
9.  **Create**.


#### 5. Configuration Profile (Default/User-Driven) & (Staging)

1.  Create a **Device restrictions** profile as above.
    **Platform:** Android Enterprise.
3.  **Profile Type:** Templates > **Device restrictions** (Fully managed, dedicated, and corporate-owned work profile).
    **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
5.  **Description** Device Restrictions Configuration Policy for Android Enterprise Corporate-Owned with Work-Profile Devices.
2.  **Settings:** `Adapt and configure each setting according to your organization's and customer requirements after importing the baseline policy configuration .JSON file.`
3.  **Assign** Assign to the Entra ID groups (Default/User-Driven) or (Staging-Mode) created in Step 1.

---

### Enrollment Steps (Default/User-Driven)

1.  Tap the Welcome screen 6 times on a fresh device.
2.  Scan the **Corporate-Owned Work Profile QR Code**.
3.  Connect to Wi-Fi.
4.  Follow prompts to install work apps.
5.  **Final Step:** Tap **Set up** on the "Register device" screen.
6.  The device will configure the Corporate-Owned Work Profile.

---

### Enrollment Steps (Staging)
1.  **Admin Step:**
    * Scan Staging QR code.
    * Device sets up Work Profile automatically.
    * Install apps (Intune, Authenticator).
    * When the screen "Add a personal account" appears (or home screen), enrollment is done.
2.  **User Step:**
    * Turn on device. Agree to privacy.
    * Go to **Work Profile** folder.
    * Open **Intune** app.
    * **Sign In** and **Register**.
    * The device is now linked to the user.

---
