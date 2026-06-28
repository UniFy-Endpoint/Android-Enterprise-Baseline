# Managing Android Devices with Microsoft Intune

## Android Enterprise Personally Owned Devices with a Work Profile

### Overview

This enrollment method creates a separate Work Profile on the user's personal device. The Work Profile is an isolated container managed by Intune — the organization controls only the Work Profile, while the personal side of the device remains private and untouched by any corporate policy.

This is the primary enrollment method for BYOD (Bring Your Own Device) scenarios where employees use personal Android devices to access corporate resources.

> **Platform transition — AMAPI enrollment (June 2026):** Microsoft has completed the migration of Android personally-owned work profile management to Google's Android Management API (AMAPI). As of June 15, 2026, **web-based enrollment is generally available** — users can enroll directly from a browser without installing any app first. The **Microsoft Intune app** is the primary management agent for AMAPI-enrolled devices. Enable web-based enrollment and deploy the migration policy for existing enrolled devices as described in the Configuration section below. Mandatory tenant-wide transition is expected in Q4 2026.

---

### Enrollment Methods

Microsoft Intune supports two enrollment methods for personally owned work profile devices. Web-based enrollment is the current recommended method.

| Enrollment Method | Policy Delivery | How Initiated | Status |
| :--- | :--- | :--- | :--- |
| **Web-based enrollment** | Android Management API (AMAPI) | Browser (URL or app redirect) | **Recommended.** Default for new tenants after opt-in. Company Portal not required for enrollment. |
| **Company Portal app** | Custom DPC | Company Portal app from Play Store | Being phased out. Migrates to web-based enrollment once web enrollment is enabled in the tenant. |

> **Note:** Enabling web-based enrollment in the tenant is a permanent action and cannot be reversed. Existing devices enrolled via Company Portal continue to function and receive policies until migrated using the Move to Android Management API profile described in [Step 5](#5-create-move-to-android-management-api-migration-policy).

---

### Requirements

* Compatible Android device running **Android 13.0 or later** (required by the baseline compliance policy; devices below Android 13.0 will be marked non-compliant).
* Devices must support Android Enterprise and carry **Play Protect certification**.
* Android Enterprise must be available in your region.
* An active **Managed Google Play** connection between your Intune tenant and Google (see Guide 1).
* A **Compliance Policy** targeting the Work Profile.
* A **Configuration Profile** targeting the Work Profile.
* For web-based enrollment: **Chrome or Edge browser** on the enrolling device. Other browsers may not support all enrollment steps.
* **Microsoft Intune app version 2025.11.01 or later.** Devices running older versions may experience sign-in failures. As of May 1, 2026, this is the minimum supported version. To identify affected devices, go to **Apps** > **Monitor** > **Discovered apps** in the Intune admin center.

> **Passkey limitation:** If passkeys are configured as the only accepted authentication method in your tenant, do not enable web-based enrollment until passkey support for web enrollment is announced by Microsoft. This limitation will be resolved in a future update.

> **Android 15 — Private Space:** Android 15 introduces a Private Space feature that creates an additional isolated profile on the device. Intune cannot manage or enroll the Android 15 Private Space. Users who enable Private Space on BYOD devices can install unmanaged apps there outside of organizational control. If a user attempts to enroll the Private Space after enrolling the work profile, Intune initiates a device administrator enrollment process, causing two enrollment records to appear in the admin center. This scenario is not supported. Communicate this limitation to users and the helpdesk before deployment.

---

### Configuration

#### 1. Create Microsoft Entra ID Group

1. Go to **Microsoft Entra admin center** > **Groups** > **New group**.
2. Configure:
   * **Group type:** Security
   * **Membership type:** Dynamic User
   * **Name:** `AND - USR - Android-Personal-Work-Profile-Users - BYOD`
   * **Description:** This group includes all Intune-licensed users permitted to enroll Android Enterprise personally-owned devices with a work profile.
   * **Dynamic membership rule:**
     ```
     (user.assignedPlans -any (assignedPlan.servicePlanId -eq "c1ec4a95-1f05-45b3-a911-aa3fa01094f5" -and assignedPlan.capabilityStatus -eq "Enabled")) and (user.accountEnabled -eq true)
     ```
3. Add owners, then select **Create**.

---

#### 2. Enable Web-Based Enrollment

Enabling web-based enrollment allows users to enroll directly from a browser or via a conditional access prompt in Teams or Outlook, without installing any app first.

> **Important:** This action is permanent and cannot be reversed once saved.

1. Sign in to the **Microsoft Intune admin center** at `intune.microsoft.com`.
2. Go to **Devices**.
3. Select the **Android** tab.
4. Expand **Device onboarding** and select **Enrollment**.
5. Under **Enrollment Profiles**, select **Personally owned devices with a work profile**.
6. Select **Use web enrollment for all users enrolling into Android personally owned work profile management**.
7. Select **Save**.

> **Note:** After enabling web-based enrollment, users can start the enrollment flow from any of the following entry points:
> - **Enrollment URL:** `https://aka.ms/enrollmyandroid` — users navigate to this address in Chrome or Edge on their Android device.
> - **Microsoft Teams or Outlook (recommended):** If Conditional Access requires a compliant or enrolled device, users are automatically prompted to enroll when they open either app.
> - **Company Portal app:** Users can open the Company Portal app and follow the prompts — enrollment proceeds via the web-based flow.

---

#### 3. Device Platform Restrictions

By default, personal device enrollment may be blocked by the tenant default restriction. Create a custom restriction that explicitly allows Android Enterprise BYOD enrollment for the user group.

1. Go to **Microsoft Intune admin center** > **Devices** > **Android** > **Enrollment**.
2. Select **Device platform restriction**.
3. Select the **Android restrictions** tab and select **Create restriction**.
4. **Basics:**
   * **Name:** `AND-Android-Enterprise-Personal-Devices`
   * **Description:** Allow Android Enterprise BYOD (Work Profile) enrollment.
5. **Platform settings:**
   * **Android Enterprise (work profile):** Set both **Platform** and **Personally owned** to **Allow**.
   * Configure other platform entries according to your security requirements.
6. **Assignments:** Assign to the Entra ID group created in Step 1.
7. **Create**.

> **Note:** Ensure this restriction has a higher priority number (lower value) than the default policy. Custom restrictions must outrank the default to take effect.

> **Note:** On Android 12 and later, enrollment restrictions that block personally-owned devices have reduced reliability and will be removed by Microsoft once all devices complete the AMAPI migration. For tighter enrollment control, use user group assignments on the enrollment profile rather than platform restrictions.

---

#### 4. Managed Google Play Apps

1. Go to **Apps** > **Android** > **Add** > **Managed Google Play app**.
2. Search for and approve the required apps. The following are recommended at minimum:
   - **Microsoft Authenticator** (required for Entra ID sign-in and SSO)
   - **Microsoft Outlook**, **Teams**, **Edge**, **OneDrive** (as applicable)
3. After approving each app, select **Sync**.
4. Assign all apps to the Entra ID group created in Step 1 as **Required**.

> **Apps installed automatically at enrollment:** When a device enrolls via web-based enrollment, Intune automatically installs the following apps in the work profile without requiring manual assignment:
>
> | App | Purpose |
> | :--- | :--- |
> | **Microsoft Intune** | Primary management agent — handles compliance checks, policy sync, diagnostic logs, and IT support contact |
> | **Company Portal** | Work app browsing and MAM scenarios. After web enrollment, both the Intune app and Company Portal are present in the work profile. |
> | **Android Device Policy** | Enforces AMAPI policies. Installed in a hidden state — users do not see it in the app drawer. |
> | **Microsoft Authenticator** | Provides single sign-on (SSO) for the user's work account |
>
> **Note:** Company Portal does not need to be assigned as a Required app to the BYOD user group because it is provisioned automatically during web enrollment. You do not need to assign it.

---

#### 5. Create Move to Android Management API Migration Policy

For devices already enrolled via the legacy Company Portal flow, deploy this configuration profile to migrate them to AMAPI policy delivery. This enables a phased, group-targeted migration rather than forcing all devices to re-enroll.

> **Note:** This step is only required for existing enrolled devices. Devices enrolling for the first time after enabling web-based enrollment (Step 2) are enrolled on AMAPI from the start.

1. Go to **Devices** > **Manage devices** > **Configuration** > **Create** > **New policy**.
2. Configure:
   * **Platform:** Android Enterprise
   * **Profile type:** Templates
   * **Template:** **Move to Android Management API**
3. Select **Create**.
4. **Basics:**
   * **Name:** `AND - Migration - Move-to-AMAPI - Personally-Owned-Work-Profile`
   * **Description:** Migrates existing personally-owned work profile devices from Custom DPC to Android Management API policy delivery.
5. **Assignments:** Assign to the Entra ID group created in Step 1, or to a staged sub-group for phased rollout.
6. **Create**.

> **Monitoring:** Track migration progress at **Devices** > **Monitor** > **Personal devices on Android Management API**. Device statuses: **AMAPI** (migrated), **Move pending**, **Error**, or **Not targeted to move**.

> **Wi-Fi connectivity warning:** Devices using username/password (credential-based) Wi-Fi authentication may lose network connectivity after migrating to AMAPI if the Wi-Fi profile is reconfigured. Transition to certificate-based Wi-Fi authentication before deploying the migration policy at scale.

---

#### 6. Import or Create a Compliance Policy

Define the minimum security requirements a device must meet to be considered compliant.

1. Go to **Devices** > **Android** > **Compliance** > **Edit the imported Compliance Policy**.
2. **Platform:** Android Enterprise.
3. **Profile type:** Personally-owned work profile.
4. **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
5. **Description:** Compliance policy for Android Enterprise personally-owned work profile (BYOD) users.
6. **Compliance settings:** Adapt and configure each setting to your organization's requirements after importing the baseline JSON file.

> **Security baseline note:** The compliance settings in the imported JSON are grounded in the [Android CIS Benchmarks](https://www.cisecurity.org/benchmark/google_android) and Microsoft's [Android Enterprise security configuration framework](https://learn.microsoft.com/en-us/mem/intune/enrollment/android-configuration-framework). Review and adapt all settings to your organization's security requirements before deploying.
>
> **Google Play Integrity — September 2025 change:** As of September 30, 2025, Google's Strong Integrity check on Android 13+ requires hardware-backed security signals and a security patch issued within the past 12 months. The baseline compliance policies use Basic Integrity attestation. If your organization requires Strong Integrity, also configure **Minimum security patch level** in the compliance policy (recommended: rolling 12-month window, format `YYYY-MM-DD`).

7. **Actions for noncompliance:** Set "Mark device noncompliant" to **Immediately** or **1 day**.
8. **Assignments:** Assign to the Entra ID group created in Step 1.
9. **Create**.

---

#### 7. Import or Create a Configuration Profile

Configure work profile restrictions such as preventing copy/paste between work and personal profiles.

1. Go to **Devices** > **Android** > **Configuration** > **Edit the imported Device Restrictions Policy**.
2. **Platform:** Android Enterprise.
3. **Profile type:** Templates > **Device restrictions** (Personally-Owned Work Profile).

> **Settings Catalog does not support personally-owned work profiles.** The Settings Catalog in Microsoft Intune is scoped exclusively to corporate-owned Android Enterprise devices (Fully Managed, Corporate-Owned Work Profile, Dedicated/Kiosk). It cannot be used to configure device restrictions for BYOD work profile devices. Always use **Templates > Device restrictions (Personally-Owned Work Profile)** for this scenario.

4. **Basics:**
   * **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
   * **Description:** Device restrictions for Android Enterprise personally-owned work profile (BYOD) users.
5. **Configuration settings:** Adapt settings such as work profile password, cross-profile data sharing, and copy/paste behavior after importing the baseline JSON file.
6. **Assignments:** Assign to the Entra ID group created in Step 1.
7. **Create**.

---

### Enrollment Steps (User Experience)

#### Web-Based Enrollment (Recommended — GA June 2026)

Web-based enrollment removes the requirement to download any app before enrolling. Users complete the entire enrollment flow from a browser.

1. On the Android device, open **Chrome** or **Microsoft Edge**.
2. Navigate to `https://aka.ms/enrollmyandroid`, or open **Microsoft Teams** or **Outlook** — if Conditional Access is configured to require enrollment, the app redirects the user to the enrollment portal automatically.
3. Sign in with corporate Microsoft credentials. The user must be a member of the Entra ID group configured in Step 1.
4. Follow the on-screen prompts to create the work profile. The browser guides the user through each step.
5. When prompted, allow the browser to download and install the required management components.
6. Once enrollment completes, the work profile appears as a separate **Work** tab or folder in the app drawer. A briefcase badge distinguishes work apps from personal apps.
7. The following apps are automatically installed in the work profile: **Microsoft Intune**, **Company Portal**, **Android Device Policy** (hidden), and **Microsoft Authenticator**.

> **Note:** If the browser prompts the user to add their work account to the browser during enrollment, they should skip this prompt and continue with the enrollment steps.
>
> **Note:** If Company Portal is older than version 2604.x on the device, the user is routed to the legacy app-based flow instead of web-based enrollment. Ensure Company Portal is up to date before enabling web enrollment.

#### Legacy Enrollment — Company Portal App (Phased Out)

For devices not yet migrated to AMAPI, the original Company Portal enrollment flow remains functional. These devices will be migrated when the Move to Android Management API profile (Step 5) is applied.

1. Open **Google Play Store** and search for **Intune Company Portal**.
2. Install the app and open it.
3. Tap **Sign In** and enter corporate Microsoft credentials.
4. Follow the on-screen prompts to create the work profile, activate it, and set a PIN if required.
5. Once enrollment completes, a separate **Work** tab or folder appears in the app drawer.

---

### Additional Capabilities (AMAPI-Enrolled Devices)

The following capabilities become available on personally-owned work profile devices after migration to AMAPI enrollment.

#### Credential Manager Permissions

On Android 14 and later, Intune can control which apps serve as system-level credential providers — handling passkey storage and password autofill within the work profile. This allows organizations to designate Microsoft Authenticator or a supported third-party password manager as the trusted credential source for work accounts.

- Configure via **Apps** > **Android** > **Configuration** > **Managed Devices** > **Android Enterprise**.
- **Known limitation:** Google Password Manager cannot act as a credential provider on BYOD devices. Only apps explicitly configured as providers are eligible.
- Reference: [Add app configuration policies for managed Android Enterprise devices](https://learn.microsoft.com/en-us/intune/app-management/configuration/configure-managed-android)

#### Strict Tunnel Mode for Microsoft Tunnel

Strict Tunnel Mode forces all network traffic from the work profile through the Microsoft Tunnel VPN. If the VPN connection is unavailable or drops, all network access from the work profile is blocked until the VPN reconnects — preventing work profile apps from accessing the internet outside the tunnel.

- Requires AMAPI enrollment (web-based enrollment or migrated devices).
- Supports an exclusion list of apps that are permitted to bypass the tunnel.
- For unenrolled MAM devices: Strict Tunnel Mode is available through a Microsoft Edge app configuration policy.
- Reference: [Overview of Microsoft Tunnel](https://learn.microsoft.com/en-us/intune/device-security/microsoft-tunnel/overview)
