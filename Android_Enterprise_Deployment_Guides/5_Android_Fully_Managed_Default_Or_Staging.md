# Managing Android Devices with Microsoft Intune

## Android Enterprise Fully Managed – User-Driven vs. Staging

### Overview

Fully Managed devices are corporate-owned and intended exclusively for work use by a single assigned user. There is no personal profile — the organization manages the entire device. This is appropriate for frontline workers, field technicians, or any scenario requiring complete device control.

Intune supports two provisioning methods for fully managed devices: **Default (User-Driven)** and **Staging**. The choice between them determines who performs the initial device setup and what the device state is when it reaches the end user.

---

### Comparison: Default (User-Driven) vs. Staging

| Feature | Fully Managed (Default) | Fully Managed via Staging |
| :--- | :--- | :--- |
| **Enrollment Token** | Default token | Staging token |
| **Setup Responsibility** | End user completes setup on the device | Admin or vendor pre-provisions the device |
| **User Experience** | User scans QR code, downloads apps, and applies policies during initial setup | User receives a ready-to-use device and only needs to sign in |
| **Ideal For** | Small deployments, remote users, self-service scenarios | Frontline workers, large-scale bulk rollouts, zero-touch deployments |
| **Device State on Delivery** | Unenrolled — user completes enrollment | Enrolled and apps installed — user only signs in |
| **Entra ID Group Type** | Assigned (static) — supports Enrollment Time Grouping | Dynamic — Enrollment Time Grouping not supported with staging tokens |

> **Minimum OS version:** Android 10.0 or later is required for fully managed enrollment. Staging is also supported on Android 10 or later.

---

### Configuration: Fully Managed (Default/User-Driven) — Entra ID Group

#### 1. Create Microsoft Entra ID Group

1. Go to **Microsoft Entra admin center** > **Groups** > **New group**.
   * **Group type:** Security
   * **Membership type:** Assigned
   * **Name:** `AND - DEV - Android-Corporate-Fully-Managed-Devices`
   * **Description:** This group includes all Android Enterprise corporate-owned fully managed devices.

2. **Assign Owner — this step is required for Enrollment Time Grouping.** Assign the **Intune Provisioning Client** service principal (App ID: `f1346770-5b25-470b-88bd-d5744ab7952c`) as the **Owner** of this group.

---

### Configuration: Fully Managed via Staging — Entra ID Group

> **Important:** Staging tokens do not support Enrollment Time Grouping. You must use a **Dynamic Device** group scoped to the staging enrollment profile name.

1. Go to **Microsoft Entra admin center** > **Groups** > **New group**.
   * **Group type:** Security
   * **Membership type:** Dynamic Device
   * **Name:** `AND - DEV - Android-Corporate-Fully-Managed-Devices - Staging`
   * **Description:** This group includes all Android Enterprise corporate-owned fully managed devices in Staging Mode.
   * **Dynamic membership rule:**
     ```
     (device.deviceOSType -eq "AndroidEnterprise") and (device.enrollmentProfileName -eq "AND - Corporate-Fully-Managed-Devices-Enrollment - Staging")
     ```

> **Note:** The enrollment profile name in the dynamic rule must match the profile name exactly, including spaces and capitalization.

---

### Configuration: Enrollment Profiles

#### 2a. Enrollment Profile — Default (User-Driven)

1. Go to **Devices** > **Android** > **Enrollment**.
2. Select **Corporate-owned, fully managed user devices**.
3. Select **Create profile** and configure:
   * **Name:** `AND - Corporate-Fully-Managed-Devices-Enrollment`
   * **Description:** Enrollment token for Android Enterprise corporate-owned fully managed devices.
   * **Token type:** Corporate-owned, fully managed (default).
   * **Token expiration date:** Set according to your lifecycle needs. Tokens can be set up to 65 years in the future (format: `MM/DD/YYYY` or `YYYY-MM-DD`).
   * **Apply device name template:** Yes.
   * **Device name template:** e.g., `AND-FM-{{SERIAL}}`
4. **Device group:** Select the Entra ID group created for Default/User-Driven devices.
5. **Create**.

#### 2b. Enrollment Profile — Staging

1. Go to **Devices** > **Android** > **Enrollment**.
2. Select **Corporate-owned, fully managed user devices**.
3. Select **Create profile** and configure:
   * **Name:** `AND - Corporate-Fully-Managed-Devices-Enrollment - Staging`
   * **Description:** Enrollment token for Android Enterprise corporate-owned fully managed devices in Staging Mode.
   * **Token type:** Corporate-owned, fully managed, via staging.
   * **Token expiration date:** Set according to your lifecycle needs.
   * **Apply device name template:** Yes.
   * **Device name template:** e.g., `AND-SFM-{{SERIAL}}`
4. **Device group:** Select **None** — dynamic groups are not supported with staging tokens at enrollment time.
5. **Create**.

**Token management options:**
- **Replace token** — generate a new token before expiration.
- **Revoke token** — immediately invalidate the token.
- **Export token** — export the JSON payload for Google Zero Touch or Samsung Knox Mobile Enrollment.

> **Note:** Microsoft Authenticator is automatically installed during fully managed device enrollment and cannot be uninstalled.

---

#### 3. Managed Google Play Apps

1. Go to **Apps** > **Android** > **Add** > **Managed Google Play app**.
2. Search for and approve the required apps. At minimum:
   - **Microsoft Authenticator** (required for Entra ID sign-in)
   - Additional work apps: **Microsoft Teams**, **Outlook**, **Edge**, **OneDrive**, **SharePoint**
3. After approving each app, select **Sync**.
4. Assign all apps to the Entra ID groups (Default/User-Driven and Staging) created in Step 1 as **Required**.

---

#### 4. Compliance Policy

1. Go to **Devices** > **Android** > **Compliance** > **Create policy**.
2. **Platform:** Android Enterprise.
3. **Profile type:** Fully managed, dedicated, and corporate-owned work profile.
4. **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
5. **Description:** Compliance policy for Android Enterprise corporate-owned fully managed devices.
6. **Compliance settings:** Adapt and configure each setting to your organization's requirements after importing the baseline JSON file.

> **Security baseline note:** The compliance settings in the imported JSON are grounded in the [Android CIS Benchmarks](https://www.cisecurity.org/benchmark/google_android) and Microsoft's [Android Enterprise security configuration framework](https://learn.microsoft.com/en-us/mem/intune/enrollment/android-configuration-framework). Review and adapt all settings to your organization's requirements before deploying.
>
> **Google Play Integrity — September 2025 change:** As of September 30, 2025, Google's Strong Integrity check on Android 13+ requires hardware-backed security signals and a security patch issued within the past 12 months. The baseline compliance policies use Basic Integrity attestation. If your organization requires Strong Integrity, also configure **Minimum security patch level** in the compliance policy (recommended: rolling 12-month window, format `YYYY-MM-DD`).

7. **Actions for noncompliance:** Set "Mark device noncompliant" to **Immediately** or **1 day**.
8. **Assignments:** Assign to both the Default/User-Driven and Staging Entra ID groups.
9. **Create**.


#### 5. Configuration Profile

1. Go to **Devices** > **Android** > **Configuration** > **Create**.
2. **Platform:** Android Enterprise.
3. **Profile type:** Templates > **Device restrictions** (Fully Managed, Dedicated, and Corporate-Owned Work Profile).
4. **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
5. **Description:** Device restrictions for Android Enterprise corporate-owned fully managed devices.
6. **Settings:** Adapt and configure each setting after importing the baseline JSON file.
7. **Assignments:** Assign to both Entra ID groups (Default/User-Driven and Staging).
8. **Create**.

---

### Enrollment Steps — Default (User-Driven)

1. Start with a factory-reset device and power it on.
2. On the **Welcome** screen, tap the same spot **6 times** in quick succession to open the QR code scanner.
3. Scan the **Fully Managed QR code** from the enrollment profile token.
4. Connect to Wi-Fi when prompted.
5. Follow the on-screen prompts. Apps are downloaded and policies are applied.
6. When the **Register device** screen appears, tap **Set up**.
7. The device finalizes enrollment and the user is signed in.

---

### Enrollment Steps — Staging

**Admin/Vendor step (performed in IT or at staging facility):**
1. Start with a factory-reset device and power it on.
2. On the **Welcome** screen, tap the same spot **6 times** to open the QR code scanner.
3. Scan the **Staging QR code**.
4. The device enrolls, installs the Microsoft Intune app and Microsoft Authenticator, and applies assigned policies.
5. Once the home screen appears with apps installed and no user signed in, staging is complete. The device is ready for distribution.

**End user step (performed when the user receives the device):**
1. User powers on the device.
2. User opens the **Microsoft Intune app**.
3. User signs in with their Entra ID credentials.
4. The device registers the user identity and updates its compliance state. The device is now fully active.
