# Managing Android Devices with Microsoft Intune

## Android Enterprise Personally Owned Devices with a Work Profile

### Overview

This enrollment method creates a separate Work Profile on the user's personal device. The Work Profile is an isolated container managed by Intune — the organization controls only the Work Profile, while the personal side of the device remains private and untouched by any corporate policy.

This is the primary enrollment method for BYOD (Bring Your Own Device) scenarios where employees use personal Android devices to access corporate resources.

---

### Requirements

* Compatible Android device running **Android 13.0 or later** (required by the baseline compliance policy; devices below Android 13.0 will be marked non-compliant).
* Devices must support Android Enterprise and carry **Play Protect certification**.
* Android Enterprise must be available in your region.
* An active **Managed Google Play** connection between your Intune tenant and Google (see Guide 1).
* A **Compliance Policy** targeting the Work Profile.
* A **Configuration Profile** targeting the Work Profile.

> **Platform transition — AMAPI enrollment (2025):** Microsoft is migrating Android personally-owned work profile management to Google's Android Management API (AMAPI). A new **web-based enrollment flow** is being introduced — users will enroll from a browser without installing any app first. The **Microsoft Intune app** replaces Company Portal as the primary management agent. Custom device configuration policies for personally-owned work profile were retired in April 2025. Check the [Microsoft Intune documentation](https://learn.microsoft.com/en-us/mem/intune/enrollment/android-work-profile-enroll) for the current enrollment flow and opt-in options for your tenant.

> **Android 15 — Private Space:** Android 15 introduces a Private Space feature that creates an additional isolated profile on the device. Intune cannot manage or enroll the Android 15 Private Space. Users who enable Private Space on BYOD devices can install unmanaged apps there outside of organizational control. Communicate this limitation to users and the helpdesk before deployment.

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

#### 2. Device Platform Restrictions

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
---

#### 3. Managed Google Play Apps

1. Go to **Apps** > **Android** > **Add** > **Managed Google Play app**.
2. Search for and approve the required apps. The following are recommended at minimum:
   - **Microsoft Authenticator** (required for Entra ID sign-in)
   - **Intune Company Portal** (required for current enrollment flow; may be replaced by the Intune app in the AMAPI transition)
   - **Microsoft Outlook**, **Teams**, **Edge**, **OneDrive** (as applicable)
3. After approving each app, select **Sync**.
4. Assign all apps to the Entra ID group created in Step 1 as **Required**.

---

#### 4. Import or create a new Compliance Policy

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

#### 5. Import or create a new Configuration Profile

Configure work profile restrictions such as preventing copy/paste between work and personal profiles.

1. Go to **Devices** > **Android** > **Configuration** > **Edit the imported Device Restrictions Policy**.
2. **Platform:** Android Enterprise.
3. **Profile type:** Templates > **Device restrictions** (Personally-Owned Work Profile) or **Device restrictions** (Settings Catalog).
4. **Basics:**
   * **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
   * **Description:** Device restrictions for Android Enterprise personally-owned work profile (BYOD) users.
5. **Configuration settings:** Adapt settings such as work profile password, cross-profile data sharing, and copy/paste behavior after importing the baseline JSON file.
6. **Assignments:** Assign to the Entra ID group created in Step 1.
7. **Create**.

---

### Enrollment Steps (User Experience)

The current enrollment flow uses the Intune Company Portal app. Users perform these steps on their personal device:

1. Open **Google Play Store** and search for **Intune Company Portal**.
2. Install the app and open it.
3. Tap **Sign In** and enter corporate Microsoft credentials. The user must belong to the Entra ID group configured in Step 1.
4. Follow the on-screen prompts:
   * **Create work profile:** Tap to begin.
   * **Activate work profile:** The device creates the work container.
   * **Update device settings:** If a PIN is required, the user is prompted to set one.
5. Once enrollment completes, a separate **Work** tab or folder appears in the app drawer. Managed apps display a briefcase icon to distinguish them from personal apps.

> **Note:** Microsoft is introducing a new web-based enrollment flow (in development as of 2025) where users enroll from a browser without installing Company Portal first. Once this becomes generally available in your tenant, the steps above will change. Monitor the [Intune What's New](https://learn.microsoft.com/en-us/mem/intune/fundamentals/whats-new) page for availability.
