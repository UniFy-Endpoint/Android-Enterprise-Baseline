# Managing Android Devices with Microsoft Intune

## Corporate-Owned Work Profile – Default vs. Staging

### Overview

The Corporate-Owned Work Profile (COPE) enrollment method is for company-issued devices where limited personal use is permitted. Like BYOD work profile, it creates a separate, managed Work Profile on the device. Unlike BYOD, the company owns the device and has a higher level of control — including the ability to enforce device-level settings in addition to work profile settings.

This model balances security and employee experience: corporate data is fully managed and protected within the Work Profile, while the personal side allows employees to use approved personal apps without IT visibility into them.

---

### Comparison: Default vs. Staging

| Feature | Corporate-Owned Work Profile (Default) | Corporate-Owned Work Profile via Staging |
| :--- | :--- | :--- |
| **Enrollment Token** | Standard token | Staging token |
| **Setup Responsibility** | End user scans QR code and completes enrollment | Admin or vendor completes device setup; user only signs in |
| **User Experience** | User receives a factory-reset device and performs full enrollment | User receives a device with the Work Profile already created |
| **Ideal For** | Standard corporate deployments | Large-scale rollouts, pre-configured device programs |
| **Device State on Delivery** | Factory reset — enrollment pending | Enrolled — Work Profile created, apps installed |
| **Entra ID Group Type** | Assigned (static) — supports Enrollment Time Grouping | Dynamic — Enrollment Time Grouping not supported with staging tokens |

> **Minimum OS version:** Android 8.0 or later is required for corporate-owned work profile enrollment. The baseline compliance policy enforces **Android 13.0** as the minimum — devices below 13.0 will be marked non-compliant.
>
> **NFC enrollment limitation:** The `afw#setup` method and NFC enrollment are only supported on Android 8 through Android 10. They are **not available on Android 11 or later**. Use QR code enrollment for devices running Android 11 and above.

---

### Configuration: Corp-Owned Work Profile (Default) — Entra ID Group

#### 1. Create Microsoft Entra ID Group

1. Go to **Microsoft Entra admin center** > **Groups** > **New group**.
   * **Group type:** Security
   * **Membership type:** Assigned
   * **Name:** `AND - DEV - Android-Corporate-Work-Profile-Devices`
   * **Description:** This group includes all Android Enterprise corporate-owned devices with a work profile.

2. **Assign Owner — this step is required for Enrollment Time Grouping.** Assign the **Intune Provisioning Client** service principal (App ID: `f1346770-5b25-470b-88bd-d5744ab7952c`) as the **Owner** of this group.

---

### Configuration: Corp-Owned Work Profile via Staging — Entra ID Group

> **Important:** Staging tokens do not support Enrollment Time Grouping. You must use a **Dynamic Device** group scoped to the staging enrollment profile name.

1. Go to **Microsoft Entra admin center** > **Groups** > **New group**.
   * **Group type:** Security
   * **Membership type:** Dynamic Device
   * **Name:** `AND - DEV - Android-Corporate-Work-Profile-Devices - Staging`
   * **Description:** This group includes all Android Enterprise corporate-owned work profile devices in Staging Mode.
   * **Dynamic membership rule:**
     ```
     (device.deviceOSType -eq "AndroidEnterprise") and (device.enrollmentProfileName -eq "AND - Corporate-Corporate-Owned-Work-Profile-Enrollment - Staging")
     ```

> **Note:** The enrollment profile name in the dynamic rule must match the profile name exactly, including spaces and capitalization.

---

### Configuration: Enrollment Profiles

#### 2a. Enrollment Profile — Default

1. Go to **Devices** > **Android** > **Enrollment**.
2. Select **Corporate-owned devices with work profile**.
3. Select **Create profile** and configure:
   * **Name:** `AND - Corporate-Corporate-Owned-Work-Profile-Enrollment`
   * **Description:** Enrollment token for Android Enterprise corporate-owned devices with a work profile.
   * **Token type:** Corporate-owned with work profile (default).
   * **Token expiration date:** Set according to your lifecycle needs. Tokens can be set up to 65 years in the future (format: `MM/DD/YYYY` or `YYYY-MM-DD`).
   * **Apply device name template:** Yes.
   * **Device name template:** e.g., `AND-CORP-WP-{{SERIAL}}`
4. **Device group:** Select the Entra ID group created for the Default/User-Driven scenario.
5. **Create**.

#### 2b. Enrollment Profile — Staging

1. Go to **Devices** > **Android** > **Enrollment**.
2. Select **Corporate-owned devices with work profile**.
3. Select **Create profile** and configure:
   * **Name:** `AND - Corporate-Corporate-Owned-Work-Profile-Enrollment - Staging`
   * **Description:** Enrollment token for Android Enterprise corporate-owned devices with a work profile in Staging Mode.
   * **Token type:** Corporate-owned with work profile, via staging.
   * **Token expiration date:** Set according to your lifecycle needs.
   * **Apply device name template:** Yes.
   * **Device name template:** e.g., `AND-CORP-SWP-{{SERIAL}}`
4. **Device group:** Select **None** — dynamic groups are not supported with staging tokens at enrollment time.
5. **Create**.

**Token management options:**
- **Replace token** — generate a new token before expiration.
- **Revoke token** — immediately invalidate the token.
- **Export token** — export the JSON payload for Google Zero Touch or Samsung Knox Mobile Enrollment.

> **Note:** Microsoft Intune app and Microsoft Authenticator are automatically installed during corporate-owned work profile enrollment and cannot be uninstalled.

---

#### 3. Managed Google Play Apps

1. Go to **Apps** > **Android** > **Add** > **Managed Google Play app**.
2. Search for and approve the required apps. At minimum:
   - **Microsoft Authenticator** (required for Entra ID sign-in)
   - Additional work apps: **Microsoft Teams**, **Outlook**, **Edge**, **OneDrive**, **SharePoint**
3. After approving each app, select **Sync**.
4. Assign all apps to both Entra ID groups (Default and Staging) as **Required**.

---

#### 4. Compliance Policy

1. Go to **Devices** > **Android** > **Compliance** > **Create policy**.
2. **Platform:** Android Enterprise.
3. **Profile type:** Fully managed, dedicated, and corporate-owned work profile.
4. **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
5. **Description:** Compliance policy for Android Enterprise corporate-owned work profile devices.
6. **Compliance settings:** Adapt and configure each setting to your organization's requirements after importing the baseline JSON file.

> **Security baseline note:** The compliance settings in the imported JSON are grounded in the [Android CIS Benchmarks](https://www.cisecurity.org/benchmark/google_android) and Microsoft's [Android Enterprise security configuration framework](https://learn.microsoft.com/en-us/mem/intune/enrollment/android-configuration-framework). Review and adapt all settings to your organization's requirements before deploying.
>
> **Google Play Integrity — September 2025 change:** As of September 30, 2025, Google's Strong Integrity check on Android 13+ requires hardware-backed security signals and a security patch issued within the past 12 months. The baseline compliance policies use Basic Integrity attestation. If your organization requires Strong Integrity, also configure **Minimum security patch level** in the compliance policy (recommended: rolling 12-month window, format `YYYY-MM-DD`).

7. **Actions for noncompliance:** Set "Mark device noncompliant" to **Immediately** or **1 day**.
8. **Assignments:** Assign to both Entra ID groups (Default and Staging).
9. **Create**.


#### 5. Configuration Profile

1. Go to **Devices** > **Android** > **Configuration** > **Create**.
2. **Platform:** Android Enterprise.
3. **Profile type:** Templates > **Device restrictions** (Fully managed, dedicated, and corporate-owned work profile).
4. **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
5. **Description:** Device restrictions for Android Enterprise corporate-owned work profile devices.
6. **Settings:** Adapt and configure each setting after importing the baseline JSON file.
7. **Assignments:** Assign to both Entra ID groups (Default and Staging).
8. **Create**.

---

### Enrollment Steps — Default (User-Driven)

1. Start with a factory-reset device and power it on.
2. On the **Welcome** screen, tap the same spot **6 times** in quick succession to open the QR code scanner.
3. Scan the **Corporate-Owned Work Profile QR code** from the enrollment profile token.
4. Connect to Wi-Fi when prompted.
5. Follow the on-screen prompts. Apps are downloaded and the Work Profile is created.
6. When the **Register device** screen appears, tap **Set up**.
7. Enrollment completes. The user has a Work Profile with managed apps alongside the personal side of the device.

---

### Enrollment Steps — Staging

**Admin step (performed in IT or at staging facility):**
1. Start with a factory-reset device and power it on.
2. Scan the **Staging QR code**.
3. The device enrolls automatically. It installs the Microsoft Intune app, Microsoft Authenticator, and creates the Work Profile.
4. When the home screen appears with apps installed and no user signed in, staging is complete.

**End user step (performed when the user receives the device):**
1. User powers on the device and accepts the privacy notice.
2. User opens the **Work Profile** folder in the app drawer.
3. User opens the **Microsoft Intune app**.
4. User taps **Sign In** and enters their Entra ID credentials.
5. The device registers the user identity and becomes compliant. The Work Profile is now linked to the user.
