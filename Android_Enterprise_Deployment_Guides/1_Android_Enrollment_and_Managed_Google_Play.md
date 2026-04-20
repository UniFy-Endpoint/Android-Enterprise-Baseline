# Managing Android Devices with Microsoft Intune

## Enrollment Methods, Requirements, and Managed Google Play

### Overview
This guide provides a comprehensive look at managing Android Enterprise devices using Microsoft Intune. It covers essential building blocks, from requirements and Managed Google Play setup to choosing the right enrollment method.

### Enrollment Methods
Before configuration, it is crucial to understand the available enrollment methods for Android within Microsoft Intune:

| Enrollment Method | Description | Use Case |
| :--- | :--- | :--- |
| **Android Enterprise Personally-Owned with Work-Profile** | Creates a separate work-profile on personal devices. | Personal devices (BYOD) where corporate work-profile data is secured without managing the entire personal device. |
| **Android Enterprise Dedicated Devices** | Corporate-owned devices locked to a specific set of apps. | Kiosk-style or single-purpose environments (e.g., retail, task workers). |
| **Android Enterprise Fully-Managed Devices** | Corporate-owned devices with full Android functionality managed by the company. | Frontline workers or field technicians requiring a full "company" phone. |
| **Android Enterprise Corporate-Owned Work-Profile** | Corporate-owned devices that support both personal and work use with strict separation. | Employees provided with a company device who are allowed personal use. |
| **Android Open Source Project** | Enrollment for non-GMS devices. | Teams Phone Meeting-Room devices based on Android. |

### Requirements
Ensure the following requirements are met to manage Android Enterprise devices with Microsoft Intune.

#### Licensing
Users or devices must have one of the following licenses:
* Microsoft 365 E5
* Microsoft 365 E3
* Enterprise Mobility + Security E5
* Enterprise Mobility + Security E3
* Microsoft 365 Business Premium
* Microsoft 365 F1
* Microsoft 365 F3
* Microsoft Intune Plan 1
* Microsoft Intune Plan 2 (Add-on)

> **Note:** Device-only licenses are not recommended for Fully Managed or Work Profile scenarios.

#### Managed Google Play
Your tenant must be linked to a Managed Google Play account. This is the enterprise version of the Play Store, allowing IT admins to:
* Approve apps for corporate use.
* Silently install apps.
* Restrict access to approved apps only.

#### Devices
General requirements for Android devices:
* **OS Version:** Android 13.0 or newer.
* **Google Mobile Services (GMS):** Must have GMS connectivity.
* **Android Enterprise Support:** Device must support Android Enterprise.
* **Play Protect:** Must have Play Protect certification.

---

### Configuration: Managed Google Play

The first configuration step is linking Managed Google Play to Microsoft Intune.

#### Requirements

Requirements of the Microsoft Entra ID account to connect to Managed Google Play:

**Microsoft Entra user account:** The account must exist in your Microsoft Entra tenant and be used to administer the Intune connection.
**Mailbox‑enabled account:** The Microsoft Entra ID account must have an active mailbox to complete Google’s email verification during onboarding.
**Sufficient Intune permissions:** The account must have permissions to configure Android enrollment (for example, Intune Administrator or Global Administrator).
**Ability to consent to Google permissions:** The admin must be allowed to approve Google’s required permissions to create and manage the Android Enterprise / Managed Google Play binding.
**Interactive sign‑in allowed:** The account must be able to perform interactive sign‑in (not a service account) to complete the “Sign in with Microsoft” flow.
**Not blocked by Conditional Access:** Conditional Access policies must allow access to Google services (play.google.com / enterprise.google.com) during setup. 


#### Step-by-Step Process

1.  Navigate to the **Microsoft Intune admin center**.
2.  Go to **Devices** > **Android** > **Enrollment**.
3.  Under **Android Enterprise Prerequisites**, select **Managed Google Play**.
4.  Check the box to agree to **Microsoft Permission**.
5.  Select **Launch Google to connect now**.
6.  **Google Sign-in:**
    * Enter the Microsoft 365 account (or Google account) you want to link.
    * If using a Microsoft 365 account, select **Sign in with Microsoft**.
7.  **Consent:**
    * Check **Consent on behalf of your organization**.
    * Select **Accept**.
8.  **Organization Details:**
    * Fill in the requested organization information.
    * Select **Next** or **Continue**.
9.  **Settings:**
    * Choose **Android Enterprise only**.
10. Completion: Once steps are finished, the status in Intune will show as **Active**.

You are now ready to start enrolling Android devices.

---

### Upgrading Managed Google Play: From a Google Account to Microsoft Entra ID

#### Background

When Microsoft Intune first introduced the Managed Google Play integration, the only option was to bind the connection using a Google (Gmail) account. Many organizations still have this legacy binding in place today.

Since 2024, Microsoft supports using a **Microsoft Entra ID account** for the Managed Google Play connection — and this is now the recommended approach for all tenants. Organizations with an existing Gmail-based binding can upgrade without breaking their existing apps or enrollment profiles.

#### Why upgrading matters

Using a corporate Entra ID account instead of a personal or shared Gmail account offers meaningful operational and security improvements:

- **Identity governance:** The binding is tied to a known, auditable corporate identity rather than a personal Google account.
- **Security controls:** Conditional Access and MFA policies can be applied to the administrative account.
- **Lifecycle management:** Access can be controlled and revoked through your standard identity processes (RBAC, account deprovisioning).
- **Standardization:** Aligns with Microsoft's recommended configuration for all new and existing tenants.

#### Prerequisites for the Microsoft Entra ID account

The Entra ID account used for the upgrade must meet all of the following conditions:

- Exists in your Microsoft Entra tenant with Intune administrative permissions (Intune Administrator or Global Administrator).
- Has a mailbox enabled — Google requires email verification during the process.
- Has **first and last name** populated in the account properties. Missing name fields can cause the upgrade to fail.
- Supports interactive sign-in (not a service account or headless identity).
- Is permitted to consent to Google's required permissions (Cloud Application Administrator role or equivalent).
- Is not blocked by Conditional Access from accessing Google services (play.google.com / enterprise.google.com).

> **Recommendation:** Use a dedicated generic admin account for this binding rather than a named personal administrator account. This avoids dependency on a specific individual's identity for a tenant-level configuration.

#### Upgrade steps

1. Navigate to **Microsoft Intune admin center** > **Devices** > **Enrollment** > **Android** blade > **Managed Google Play**.
2. The **Upgrade** button is only visible if your current binding uses a Gmail account. Select **Upgrade**.
3. Enter the Microsoft Entra ID admin account credentials when prompted.
4. When Google asks how you want to sign in, select **Sign in with Microsoft**.
5. Grant the required permissions to the Google Workspace application.
6. Review the account information form — first and last name should be auto-populated from the Entra ID account.
7. In the subscriptions section, leave **Android Enterprise** checked.
8. Accept the terms and confirm the upgrade.
9. Wait for the process to complete. The linked account displayed in Intune will update to reflect the new Entra ID identity, and the **Upgrade** button will no longer be available.

#### Critical warnings

> **Do not disconnect the existing Google Account before upgrading.** Disconnecting the binding instead of upgrading will remove all associated apps and enrollment profiles, rendering enrolled devices unmanaged. Always use the Upgrade path.

> **Verify account properties before starting.** Confirm the Entra ID account has first and last name populated in Entra ID before initiating the upgrade. An incomplete profile may cause the process to fail mid-way.

#### Post-upgrade verification

After completing the upgrade, confirm the following in the Intune admin center:

- The **Linked account** field under Managed Google Play shows the Entra ID account (not a Gmail address).
- The **Upgrade** button is no longer available.
- Existing approved apps remain visible and assigned.
- Existing enrollment profiles are intact and functioning.