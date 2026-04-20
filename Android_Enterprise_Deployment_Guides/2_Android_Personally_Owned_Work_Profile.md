# Managing Android Devices with Microsoft Intune

## Android Enterprise Personally Owned Devices with a Work Profile

### Overview
This enrollment method provides the end user's personal device with a Work Profile. This profile is separate from personal data. The organization manages only the Work Profile; personal items remain untouched.

### Requirements
* Compatible Android device (Android 13.0+).
* Compliance Policy.
* Configuration Profile.
* Intune Company Portal app (from Google Play Store).

> **Platform change (2025):** Microsoft is transitioning Android personally owned work profile management to Google's Android Management API (AMAPI). A new **web-based enrollment flow** is being introduced — users begin enrollment from a webpage instead of installing Company Portal first. The **Microsoft Intune app** replaces Company Portal as the primary enrollment app, and **Android Device Policy** installs silently to enforce policies. Custom device configuration policies for personally owned work profile were retired in April 2025. Check the [Microsoft Intune documentation](https://learn.microsoft.com/en-us/intune/intune-service/enrollment/android-work-profile-enroll) for the current enrollment flow applicable to your tenant.

---

### Configuration

#### 1. Create Microsoft Entra ID Group

1.  Go to **Microsoft Entra admin center** > **Entra ID** > **Groups** > **New group**.
2.  **Settings:**
    * **Group type:** Security **Membership type:** Dynamic User
    * **Group name:** `AND - USR - Android-Personal-Work-Profile-Users - BYOD`
    * **Description:** This group includes all Intune-licensed users allowed to enroll Android Enterprise personally-owned devices with a work profile.
    * **Dynamic Membership Rule** `(user.assignedPlans -any (assignedPlan.servicePlanId -eq "c1ec4a95-1f05-45b3-a911-aa3fa01094f5" -and assignedPlan.capabilityStatus -eq "Enabled")) and (user.accountEnabled -eq true)`
3.  Add owners and, then select **Create**.


#### 2. Device Platform Restrictions
By default, personal devices might be blocked. You need to allow them for this specific group.

1.  Go to **Microsoft Intune admin center** > **Devices** > **Android** > **Enrollment**.
2.  Select **Device platform restriction**.
3.  Select the **Android restrictions** tab and click **Create restriction**.
4.  **Basics:**
    * **Name:** `AND-Android-Enterp-Personal-Devices`
    * **Description:** Allow Android Enterprise BYOD (Work Profile) Enrollment.
5.  **Platform settings:**
    * **Android Enterprise (work profile):** Select **Allow** on **Platform** and **Personally owned** sections.
    * Ensure other settings are configured as per your security needs (usually Block for others if strictly controlling).
6.  **Assignments:** Assign to the Entra ID group created in Step 1.
7.  **Create.**

> **Note:** Ensure this policy has a higher priority than the Default policy.


#### 3. Managed Google Play Apps

1. Go to **Apps** > **Android** > **Create** > **Managed Google Play app**.
2. Search for and approve **Microsoft Authenticator** (required for Entra ID sign-in).
   - Add your required applications that need to be published in the work profile. Examples include: Microsoft Outlook, Teams, Edge, OneDrive, SharePoint, and Intune Company Portal.
3. **Select** the app you want to add, then click **Sync**.
4. **Assign** apps to the Entra ID group created in Step 1 as **Required**.


#### 4. Compliance Policy
Define requirements (e.g., minimum OS, root status) for the device to be considered compliant.

1.  Go to **Devices** > **Android** > **Compliance** > **Create policy**.
2.  **Platform:** Android Enterprise.
3.  **Profile type:** Personally-owned work profile.
4.  **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
5.  **Description** This compliance policy applies to Android Enterprise BYOD Personally-Owned Work-Profile users.
6.  **Compliance settings:** `Adapt and configure each setting according to your organization's and customer requirements after importing the baseline policy configuration .JSON file.`

> **Security baseline note:** The compliance settings in the imported JSON are grounded in the [Android CIS Benchmarks](https://www.cisecurity.org/benchmark/google_android) and Microsoft's [Android Enterprise security configuration framework](https://learn.microsoft.com/en-us/mem/intune/enrollment/android-configuration-framework). Review and adapt all settings to your organization's and customer's security requirements before deploying.
>
> **Google Play Integrity – September 2025 change:** As of September 30, 2025, Google's Strong Integrity check on Android 13+ requires hardware-backed security signals **and** a security patch issued within the past 12 months. The baseline compliance policies use Basic Integrity attestation. If your organization requires Strong Integrity, also configure **Minimum security patch level** in the compliance policy (recommended: rolling 12-month window, format `YYYY-MM-DD`).

7.  **Actions for noncompliance:** Set "Mark device noncompliant" to "Immediately" or "1 days".
8.  **Assignments:** Assign to the Entra ID group created in Step 1.
9.  **Create**.


#### 5. Configuration Profile

- For specific Android enrollment types, you can use either Device Restriction templates or the Device Restriction on the Settings Catalog.

- Configure settings inside the work profile (e.g., preventing copy/paste between work and personal).

1.  Go to **Devices** > **Android** > **Configuration** > **Create** > **New Policy**.
2.  **Platform:** Android Enterprise.
3.  **Profile type:** Templates > **Device restrictions** (under Personally-Owned Work Profile) Or **Device restrictions** (under Settings Catalog)
4.  **Basics:**
    * **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
    * **Description:** Device Restrictions Settings for Android Enterprise Personally Owned with Work Profile (BYOD) Users.
5.  **Configuration settings:** * Configure settings such as **Work profile password**, **Copy and paste between work and personal profiles** (Block/Allow), and **Data sharing** `Adapt and configure each setting according to your organization's and customer requirements after importing the baseline policy configuration .JSON file.`
6.  **Assignments:** Assign to the Entra ID group created in Step 1.
7.  **Create**.

---

### Enrollment Steps (User Experience)
The user must perform these steps on their personal device:

1.  Open **Google Play Store**.
2.  Search for and install **Intune Company Portal**.
3.  Open the app and tap **Sign In**.
4.  Log in with corporate Microsoft credentials (user must be in the Entra ID group).
5.  Follow the on-screen setup:
    * **Create work profile:** Tap to begin.
    * **Activate work profile:** The device will set up the container.
    * **Update device settings:** If a PIN is required, the user will be prompted to set one.
6.  Once finished, a separate "Work" tab or folder appears in the app drawer containing managed apps (indicated by a briefcase icon).

---

> **Android 15 — Private Space:** Android 15 introduces a Private Space feature that creates a secondary, isolated profile on the device. Microsoft Intune does not support managing or enrolling the Android 15 Private Space. Users who enable Private Space on BYOD devices can install unmanaged apps there outside of organizational control. This is a known platform limitation — communicate it to users and helpdesk as needed.