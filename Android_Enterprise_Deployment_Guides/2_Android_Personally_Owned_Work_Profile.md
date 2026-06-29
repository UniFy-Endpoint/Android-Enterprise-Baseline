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

#### 1. Create Microsoft Entra ID Group for Personal-Work-Profile-Users & Personal-Work-Profile-Devices

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

1. Go to **Microsoft Entra admin center** > **Groups** > **New group**.
2. Configure:
   * **Group type:** Security
   * **Membership type:** Dynamic Device
   * **Name:** `AND - DEV - Android-Personal-Work-Profile-Devices - BYOD`
   * **Description:** This group includes all Android Enterprise Personal-Owned work profile devices.
   * **Dynamic membership rule:**
     ```
   (device.deviceOSType -startsWith "AndroidForWork") and (device.deviceOwnership -eq "Personal") and (device.deviceTrustType -eq "Workplace") and (device.deviceManagementAppId -eq "0000000a-0000-0000-c000-000000000000")
     ```
3. Then select **Create**.

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
   * **Name:** `AND - DC - AMAPI - USR- Personal-Work-Profile - v1.x`
   * **Description:** This policy migrates existing Android personally-owned work profile devices enrolled through the Company Portal App to Android Management API (AMAPI).
5. **Assignments:** Assign to the Entra ID group created in Step 1, with all `AND - DEV - Android-Personal-Work-Profile-Devices - BYOD`, or to a staged sub-group for phased rollout.
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

> **Settings Catalog does not support personally-owned work profiles.** Microsoft's Android Settings Catalog platform support matrix lists only corporate-owned Android Enterprise (Fully Managed, Corporate-Owned Work Profile, Dedicated/Kiosk) and AOSP. Personally-owned work profile is not included. Intune may allow a Settings Catalog policy to be created and assigned to BYOD users, but the device restriction settings are not enforced on the device — the BYOD policy channel does not process Settings Catalog device restriction objects. Assigning Settings Catalog restriction policies to BYOD user groups creates misleading policy assignment reporting without actual enforcement. Always use **Templates > Device restrictions (Personally-Owned Work Profile)** for this scenario.
>
> The AMAPI enrollment migration does not change this: AMAPI modernizes policy delivery for BYOD devices but does not extend Settings Catalog device restriction support to personally-owned work profile. This distinction is explicitly maintained in Microsoft documentation.
>
> Microsoft references: [Android configuration list for Intune settings catalog](https://learn.microsoft.com/en-us/intune/device-configuration/settings-catalog/ref-android-settings) (SC platform matrix — personally-owned work profile not listed) · [Device restriction settings for Android in Microsoft Intune](https://learn.microsoft.com/en-us/intune/device-configuration/templates/ref-device-restrictions-android-enterprise) (DC template — includes a dedicated section for personally-owned work profile)

> **When to reassess this decision:** Revisit if Microsoft adds personally-owned work profile to the Android Settings Catalog platform support matrix, or if the Intune admin center exposes Settings Catalog creation explicitly scoped to personally-owned work profile. Monitor [Intune What's New](https://learn.microsoft.com/en-us/intune/whats-new/) for announcements. Until then, the Device Configuration template remains the only supported and enforced path for BYOD device restrictions.

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

#### Credential Manager Provider

Android's Credential Manager API controls which apps serve as system-level credential providers on managed devices, responsible for **passkey storage** and **password autofill** within the work profile. By default, Android blocks third-party apps from acting as credential providers on managed devices. Intune's **Credential provider** app configuration setting overrides this default — allowing organizations to designate specific apps as trusted credential sources for work accounts.

**Microsoft Authenticator is permitted automatically** and does not require an app configuration policy.

**Supported enrollment types:**

| Enrollment Type | Support |
| :--- | :--- |
| Android Enterprise Fully Managed (COBO) | Supported |
| Android Enterprise Dedicated (COSU) | Supported |
| Android Enterprise Corporate-Owned Work Profile (COPE) | Supported |
| Android Enterprise Personally-Owned Work Profile (BYOD) | Supported — **requires AMAPI enrollment** (web-based enrollment or migrated devices) |

> **Pre-migration requirement for BYOD:** Create credential provider App Configuration Policy **before** enabling web-based enrollment or deploying the Move to Android Management API migration profile. After migration, autofill and passkey sign-in depend on the credential provider being configured. Migration cannot be reversed. If policies are not in place before migration, they can be created post-migration to restore functionality — autofill and passkey sign-in on Android 14+ may be unavailable until the policy is applied.

Common third-party credential provider apps that require a policy (Microsoft Authenticator does not):

| App |
| :--- |
| 1Password |
| Bitwarden |
| Dashlane |
| LastPass |
| Nordpass |
| Okta |
| Proton Pass |
| Roboform |

> **Important:** Google Password Manager cannot act as a credential provider on personally-owned work profile devices or corporate-owned work profile devices. It is blocked at the device level. Use one of the apps above as a workaround.

**Configuration steps:**

1. Go to **Microsoft Intune admin center** > **Apps** > **Configuration** > **Create** > **Managed devices**.
2. **Basics:**
   - **Name:** e.g., `AND - ACP - DEV - Credential-Provider - [AppName] - Personal-Owned - v1.5`
   - **Description:** Configures [App Name] as a system credential provider for BYOD work profile devices.
   - **Device enrollment type:** Managed devices
3. **Platform:** Android Enterprise
4. **Profile type:** Personally-Owned Work Profile
5. **Targeted app:** Select **Select app** and choose the credential provider app from your Managed Google Play inventory. The app must be added to Managed Google Play before it can be selected here.
6. Select **Next**.
7. On the **Settings** page, locate **Credential provider** and set it to **Enabled**.
8. Select **Next** (scope tags are optional).
9. **Assignments:** Assign to the Entra ID BYOD user group (e.g., `AND - USR - Android-Personal-Work-Profile-Users - BYOD`).
10. Select **Next** > **Create**.

> **Note:** Repeat steps for each third-party credential provider app your users rely on. Microsoft Authenticator does not require a separate policy.

**Reference:** [Add app configuration policies for managed Android Enterprise devices — Allow an app to be a credential provider](https://learn.microsoft.com/en-us/intune/app-management/configuration/configure-managed-android#allow-an-app-to-be-a-credential-provider)

---

#### Strict Tunnel Mode for Microsoft Tunnel

> **Optional — applies only if your organization uses Microsoft Tunnel.** Microsoft Tunnel is a VPN gateway solution that requires separate infrastructure setup (a Linux server running the Tunnel Gateway). If your organization does not use Microsoft Tunnel, this section does not apply. If you do use Microsoft Tunnel for BYOD, Strict Tunnel Mode is an optional hardening setting within the VPN profile — configure it when your security requirements demand that work profile traffic must never reach the internet outside the tunnel, even temporarily.

Strict Tunnel Mode is a Microsoft Tunnel VPN profile setting that **blocks all network traffic on the device** when the VPN connection is unavailable or drops. Without Strict Tunnel Mode, if the tunnel VPN disconnects, work profile apps can fall back to direct internet access — bypassing corporate network controls. With Strict Tunnel Mode enabled, all network access is blocked until the VPN reconnects.

**What it does:**
- Forces all work profile network traffic through the Microsoft Tunnel VPN.
- Blocks all network access when the VPN drops — no fallback to the public internet.
- Restores access automatically when the VPN reconnects.
- Supports an **App exclusion list**: apps added to this list can bypass the tunnel and connect directly regardless of VPN state. Apps not on the list are blocked when the VPN is unavailable.

**Requirements:**

| Requirement | Detail |
| :--- | :--- |
| Microsoft Tunnel infrastructure | Must be deployed and operational before configuring this setting |
| Always-on VPN | Must be set to **Enable** in the VPN profile — Strict Tunnel Mode is only available when Always-on VPN is enabled |
| AMAPI enrollment | Required for personally-owned work profile. Devices on the legacy EMM API (pre-AMAPI) do not support Strict Tunnel Mode until migrated |
| Microsoft Defender for Endpoint | Must be deployed as the Microsoft Tunnel client app |

**Supported platforms:**
- Android Enterprise Fully Managed (COBO)
- Android Enterprise Corporate-Owned Work Profile (COPE)
- Android Enterprise Personally-Owned Work Profile (BYOD) — AMAPI enrollment required

**Configuration steps:**

1. Go to **Devices** > **Manage devices** > **Configuration** > **Create** > **New policy**.
2. **Platform:** Android Enterprise
3. **Profile type:** VPN > **Personally-Owned Work Profile**
4. Select **Create**.
5. **Basics:** Enter a name (e.g., `AND - VPN - MicrosoftTunnel - Personal-Owned - v1.5`) and description.
6. **Configuration settings:**
   - **Connection type:** Microsoft Tunnel
   - **Connection name:** Enter a display name visible to users (e.g., `Corporate VPN`).
   - **Microsoft Tunnel Site:** Select the deployed Tunnel site.
   - **Always-on VPN:** Set to **Enable** — this must be enabled before Strict Tunnel Mode becomes available.
   - **Strict Tunnel Mode:** Set to **Enable**.
   - **App exclusion list** (optional): Add any apps that must bypass the tunnel. Apps not on this list are blocked when the VPN is unavailable.
7. **Assignments:** Assign to the BYOD user group.
8. Select **Next** > **Create**.

> **MAM (unenrolled devices):** For unenrolled devices using Microsoft Tunnel for MAM, Strict Tunnel Mode is configured through a **Microsoft Edge app configuration policy**, not through a VPN profile.

**References:**
- [Overview of Microsoft Tunnel](https://learn.microsoft.com/en-us/intune/device-security/microsoft-tunnel/overview)
- [Configure the Microsoft Tunnel VPN profile](https://learn.microsoft.com/en-us/intune/device-security/microsoft-tunnel/install#create-a-vpn-profile)
