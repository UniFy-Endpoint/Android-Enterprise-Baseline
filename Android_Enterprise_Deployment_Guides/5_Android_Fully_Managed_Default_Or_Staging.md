# Managing Android Devices with Microsoft Intune

## Android Enterprise Fully Managed - User-Driven vs. Staging

### Overview
Fully Managed devices are corporate-owned and intended for work-only use by a single user. There are two deployment methods: **Default (User-Driven)** and **Staging**.

### Comparison: Default/User-Driven vs. Staging

| Feature | Fully Managed (Default) | Fully Managed via Staging |
| :--- | :--- | :--- |
| **Enrollment Token** | Default token | Staging token |
| **Setup Responsibility** | User | Admin/Vendor |
| **User Experience** | Full setup required (downloading apps, applying policies) | Sign-in only (Device pre-provisioned) |
| **Ideal For** | Small deployments, remote users | Frontline workers, large-scale bulk rollouts |
| **Device State Before User** | Unprovisioned | Pre-provisioned (Apps installed) |

---

### Configuration: Fully Managed (Default/User-Driven) Group

#### 1. Create Microsoft Entra ID Group

1. **Create Group:** Go to Entra ID > Groups > New Group.
    * **Type:** Security. **Membership type:** Assigned
    * **Name:** `AND - DEV - Android-Corporate-Fully-Managed-Devices`
    * **Description:**  This group includes all Android Enterprise Corporate-Owned Fully Managed Devices.
4.  **Assign Owner:** This is critical. Assign the **Intune Provisioning Client** (AppID `f1346770-5b25-470b-88bd-d5744ab7952c`) as the **Owner** of this group.

---

### Configuration: Fully Managed via (Staging) Group

**Staging does NOT support Enrollment Time Grouping (Static Groups). You must use Dynamic Groups.**

1.  Go to **Entra ID** > **Groups** > **New Group**.
    * **Type:** Security.
    * **Name:** `AND - DEV - Android-Corporate-Fully-Managed-Devices - Staging`
    * **Description:**  This group includes all Android Enterprise Corporate-Owned Fully Managed Devices - Staging Mode.
    * **Membership:** Dynamic Device.
2.  **Rule:** `(device.deviceOSType -eq "AndroidEnterprise") and (device.enrollmentProfileName -eq "AND - Corporate-Fully-Managed-Devices-Enrollment - Staging")`

> **Note:** The name in the rule must match the Enrollment Profile name exactly.

---

### Configuration: Fully Managed (Default/User-Driven) Profile

#### 2. Enrollment Profile

1.  Go to **Devices** > **Android** > **Enrollment**
2.  Select **Corporate-owned, fully managed user devices**.
3.  **Create policy**:
    * **Name:** `AND - Corporate-Fully-Managed-Devices-Enrollment`
    * **Description:** `Enrollment Token for Android Enterprise Corporate-Owned Fully Managed Devices.`
    * **Token type:** Corporate-owned, fully managed (default).
    * **Token expiration:** Set to 90 days (max).
    * **Apply device name template:** Yes
    * **Device name template:** e.g., `AND-FM-{{SERIAL}}`
4.  **Device Group:** Select the Entra ID group created for Fully Managed (Default/User-Driven) devices in Step 1.
5.  **Create**.

### Configuration: Fully Managed via (Staging)

1.  Go to **Devices** > **Android** > **Enrollment**
2.  Select **Corporate-owned, fully managed user devices**.
3.  **Create policy**:
    * **Name:** `AND - Corporate-Fully-Managed-Devices-Enrollment - Staging`
    * **Description:** `Enrollment Token for Android Enterprise Corporate-Owned Fully Managed Devices in Staging Mode.`
    * **Token type:** Corporate-owned, fully managed, via staging.
    * **Token expiration:** Set to 90 days (max).
    * **Apply device name template:** Yes
    * **Device name template:** e.g., `AND-SFM-{{SERIAL}}`
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
5.  **Description** This Compliance Policy applies to Android Enterprise Corporate-Owned Fully Managed Devices.
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
3.  **Profile Type:** Templates > **Device restrictions** (Fully Managed,...).
    **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
5.  **Description** Device Restrictions Configuration Policy for Android Enterprise Corporate-Owned Fully Managed Devices..
2.  **Settings:** `Adapt and configure each setting according to your organization's and customer requirements after importing the baseline policy configuration .JSON file.`
3.  **Assign** Assign to the Entra ID groups (Default/User-Driven) or (Staging-Mode) created in Step 1.

---

### Enrollment Steps (Default/User-Driven)

1.  Tap the Welcome screen 6 times on a fresh device.
2.  Scan the **Fully Managed QR Code**.
3.  Connect to Wi-Fi.
4.  Follow prompts to install work apps.
5.  **Final Step:** Tap **Set up** on the "Register device" screen.
6.  The device will configure Fully Managed Mode.

---

### Enrollment Steps (Staging)
1.  **Admin/Vendor Step:**
    * Tap Welcome screen 6 times.
    * Scan Staging QR code.
    * The device enrolls and installs the "Microsoft Intune" app and "Authenticator".
    * **Result:** Device is on the home screen, apps are installed. No user is logged in.
2.  **User Step:**
    * User receives the device.
    * Opens **Microsoft Intune App**
    * Signs in with credentials.
    * Device registers the specific user and becomes compliant.
