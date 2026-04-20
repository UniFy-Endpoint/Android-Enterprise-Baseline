# Managing Android Devices with Microsoft Intune

## Android Enrollment Overview and Managed Google Play Setup

### Overview

This is the foundational guide for Android Enterprise management in Microsoft Intune. Before any enrollment method can be configured, two prerequisites must be in place: correct licensing for the users or devices being managed, and an active Managed Google Play connection between your Intune tenant and Google. Both are covered here.

Once Managed Google Play is connected and requirements are verified, subsequent guides (2–7) cover configuration of each individual enrollment method in detail.

---

### Enrollment Methods

The table below summarizes the available Android enrollment methods in Microsoft Intune, their ownership model, recommended use case, and the minimum Android OS version required.

| Enrollment Method | Description | Ownership | Use Case | Min OS Version |
| :--- | :--- | :--- | :--- | :--- |
| **Android Enterprise Personally-Owned with Work Profile** | Creates a separate work profile on a personal device. Corporate data is isolated in the work profile; the rest of the device remains personal. | User-owned (BYOD) | Employees using their own device for work | Android 10.0 |
| **Android Enterprise Corporate-Owned Work Profile** | Corporate-owned device with both a work and personal profile. The work profile is fully managed; the personal side allows approved personal use. | Corporate-owned | Company-issued devices where limited personal use is permitted | Android 10.0 |
| **Android Enterprise Fully Managed** | Corporate-owned device used exclusively for work. The entire device is under Intune management; no personal profile exists. | Corporate-owned | Field technicians, frontline workers, or any scenario requiring full device management | Android 10.0 |
| **Android Enterprise Dedicated** | Corporate-owned, single-purpose or kiosk device. App availability and device usage is restricted to a defined scope. | Corporate-owned | Retail kiosks, digital signage, shared task devices, inventory scanners | Android 8.0 |
| **Android Open Source Project (AOSP)** | Enrollment for devices built from AOSP that do not have Google Mobile Services (GMS). Supports both user-associated and userless configurations. | Corporate-owned | Teams Rooms devices, purpose-built non-GMS hardware | Android 9.0 |

> **Important:** Android device administrator (DA) enrollment is deprecated and is no longer supported for devices with access to Google Mobile Services (GMS). Microsoft Intune ended support for DA on GMS devices in December 2024. DA-managed devices have reduced policy coverage and will lose management capability over time. All new enrollments must use one of the Android Enterprise methods listed above. If your organization has existing DA-enrolled devices, migrate them to an Android Enterprise method.

#### Factory Reset Requirements by Enrollment Type

Some enrollment methods require a factory reset before provisioning. Plan for this in device procurement and logistics.

| Enrollment Method | Factory Reset Required Before Enrollment? |
| :--- | :--- |
| Personally-Owned Work Profile (BYOD) | No — enrolls on the existing personal device; only a work profile is added |
| Corporate-Owned Work Profile | Yes — provisioning occurs during Android Setup Wizard after reset |
| Fully Managed | Yes — provisioning occurs during Android Setup Wizard after reset |
| Dedicated | Yes — provisioning occurs via QR code, NFC, or zero-touch after reset |
| AOSP | Yes — device must be reset before provisioning |

---

### Requirements

Ensure the following requirements are satisfied before configuring Android Enterprise enrollment.

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

> **Note:** Device-only licenses are not recommended for Fully Managed or Work Profile scenarios where user identity is required.

#### Managed Google Play

Managed Google Play is Google's enterprise app store for organizations. It must be connected to your Intune tenant before any Android Enterprise enrollment method can function — this is a required prerequisite for all Android Enterprise management options, including work profile, fully managed, dedicated, and corporate-owned work profile.

Connecting Managed Google Play allows IT admins to:

* Approve and publish apps for corporate use.
* Silently install apps on managed devices without user interaction.
* Restrict the work profile Play Store to approved apps only.

#### Devices

General hardware and software requirements for Android devices managed by Intune:

* **OS Version:** Android 10.0 or newer for user-based enrollment methods (Personally-Owned Work Profile, Corporate-Owned Work Profile, Fully Managed). Android 8.0 or newer for userless or dedicated enrollment (Dedicated Devices, AOSP userless). See the Enrollment Methods table above for per-method minimums.
* **Google Mobile Services (GMS):** Devices must have GMS and be able to connect to Google's GMS servers. Non-GMS devices must use the AOSP enrollment path.
* **Android Enterprise Support:** Device must support Android Enterprise. Verify with the manufacturer or the [Android Enterprise solutions directory](https://androidenterprise.google/).
* **Play Protect Certification:** Device must carry Google Play Protect certification.
* **Regional Availability:** Android Enterprise must be available in your country or region. Verify availability using [Is Android Enterprise available in my country/region?](https://support.google.com/work/android/answer/6270910). Tenants in unsupported regions cannot complete Managed Google Play binding.

> **Note:** During Managed Google Play setup, the browser used to complete the Google binding must treat `portal.azure.com`, `play.google.com`, and `enterprise.google.com` as the same browser security zone. If these domains fall in different security zones (for example, Internet Explorer Trusted Sites vs. Internet zone), the sign-in and consent flow may fail or loop. Add all three domains to the same zone before starting setup.

---

### Configuration: Managed Google Play

Linking Managed Google Play to your Intune tenant is the first configuration step and a prerequisite for all Android Enterprise enrollment. Complete this before configuring any enrollment profiles.

#### Requirements

The Microsoft Entra ID account used to create the Managed Google Play binding must meet all of the following conditions:

* **Exists in your Microsoft Entra tenant:** The account must be a member of your tenant, not a guest.
* **Mailbox-enabled:** Google requires email verification during onboarding. The account must have an active mailbox to receive this verification.
* **Sufficient Intune permissions:** The account must hold the Intune Administrator or Global Administrator role. For custom RBAC roles, the account requires organization *Read* and *Update* permissions.
* **Ability to consent to Google permissions:** The admin must be permitted to approve Google's required permissions. Cloud Application Administrator role or equivalent is sufficient.
* **Interactive sign-in capable:** The account must support interactive sign-in. Service accounts and headless identities cannot complete the "Sign in with Microsoft" flow.
* **Not blocked by Conditional Access:** Conditional Access policies must allow the account to access Google services (`play.google.com`, `enterprise.google.com`) during setup. Verify this before starting.
* **MX record on domain (if using "Sign in with Microsoft"):** If you plan to use the "Sign in with Microsoft" option during the Google binding flow, the Microsoft Entra domain must have a valid MX record. Google uses the MX record to verify the domain during federated sign-in. Without an MX record, the "Sign in with Microsoft" option may not appear; configure the MX record first or use direct Google account sign-in instead.

> **Recommendation:** Use a dedicated shared admin account (not a named personal administrator) for this binding. The Managed Google Play connection is a tenant-level configuration — tying it to an individual's identity creates a dependency risk if that person leaves or their account is disabled. A dedicated account can be managed through standard identity lifecycle processes independent of any one person.

> **Recommendation:** Register at least two accounts as owners of the Managed Google Play enterprise after the binding is created. If the primary binding account becomes unavailable due to account lockout, offboarding, or other reasons, a second owner can administer the enterprise without requiring a full disconnect and rebind. Add additional owners in the Google Admin console at `admin.google.com` after completing the steps below.

#### Step-by-Step Process

1. Navigate to the **Microsoft Intune admin center** at `intune.microsoft.com`.
2. Go to **Devices** > **Android** > **Enrollment**.
3. Under **Prerequisites**, select **Managed Google Play**.
4. Select **I agree** to grant Microsoft permission to send user and device information to Google.
5. Select **Launch Google to connect now**. The Managed Google Play website opens in a new browser tab.
6. **Google Sign-in:** On the sign-in page, confirm that the prefilled Microsoft Entra account is the account you prepared for this binding.
   - To sign in using your Microsoft Entra identity (recommended), select **Sign in with Microsoft**.
   - If the "Sign in with Microsoft" option is missing, your domain may lack an MX record. See the Requirements section above.
7. **Consent:** When Google presents a permissions dialog:
   - Check **Consent on behalf of your organization**.
   - Select **Accept**.
8. **Organization Details:** Complete the organization information form. Verify that first and last name fields are populated — incomplete name fields may cause the process to fail.
9. **Subscription type:** When prompted, select **Android Enterprise only**.
10. **Completion:** After all steps finish, return to the Intune admin center. The Managed Google Play status will show as **Active** with the linked account displayed. You are now ready to configure Android enrollment profiles.

#### Apps Added Automatically

After the Managed Google Play connection is established, Microsoft Intune automatically syncs four apps to your Managed Google Play app catalog. These apps appear under **Apps** > **Android** without requiring manual approval or search.

| App | Purpose |
| :--- | :--- |
| **Microsoft Intune** | Primary management agent — required for fully managed, dedicated, and corporate-owned work profile enrollment |
| **Microsoft Authenticator** | Entra ID authentication; also used for dedicated devices enrolling with Microsoft Entra shared device mode |
| **Intune Company Portal** | End-user enrollment app for personally-owned work profile (BYOD) and Intune app protection policies |
| **Microsoft Managed Home Screen** | Kiosk launcher used for multi-app kiosk mode on dedicated devices |

> **Note:** These four apps are added automatically — you do not need to search for, approve, or manually add them. They will appear in the Apps list within a few minutes of the Managed Google Play connection completing its first sync.

#### Play Store Mode

After connecting Managed Google Play, the work profile Play Store on enrolled devices can operate in one of two modes. The mode controls how end users browse and install approved apps.

| Mode | Behavior | When to Use |
| :--- | :--- | :--- |
| **Basic** | Admins approve individual apps; all approved apps are automatically visible to users in the work profile Play Store | Suitable for most organizations — users see a curated list of approved apps without any self-service browsing |
| **Custom (Collections)** | Admins organize approved apps into named Collections; users browse and self-install from structured Collections in the work profile Play Store | Suitable when users need to self-install from a larger app catalog organized by department, role, or function |

> **Note:** Basic mode is the default and is appropriate for most enterprise deployments. If you begin creating Collections in the Managed Google Play console, Google automatically switches the Play Store to Custom mode. To revert, select **Reset to Basic** in the Intune admin center under **Apps** > **Android** > **Managed Google Play**. Changing the mode does not affect existing app assignments or enrolled devices.

---

### Upgrading Managed Google Play: From a Google Account to Microsoft Entra ID

#### Background

When Microsoft Intune first introduced the Managed Google Play integration, the only option was to bind the connection using a Google (Gmail) account. Many organizations still have this legacy binding in place today.

Since August 2024, Microsoft supports using a **Microsoft Entra ID account** for the Managed Google Play connection — and this is now the recommended approach for all tenants. Organizations with an existing Gmail-based binding can upgrade without disrupting existing apps or enrollment profiles.

#### Why Upgrading Matters

Using a corporate Entra ID account instead of a personal or shared Gmail account provides meaningful operational and security improvements:

- **Identity governance:** The binding is tied to a known, auditable corporate identity rather than a personal Google account.
- **Security controls:** Conditional Access and MFA policies can be applied to the administrative account.
- **Lifecycle management:** Access can be controlled and revoked through standard identity processes (RBAC, account deprovisioning).
- **Standardization:** Aligns with Microsoft's recommended configuration for all new and existing tenants.

#### Prerequisites for the Microsoft Entra ID Account

The Entra ID account used for the upgrade must meet all of the following conditions:

- Exists in your Microsoft Entra tenant with Intune administrative permissions (Intune Administrator or Global Administrator).
- Has a mailbox enabled — Google requires email verification during the process.
- Has **first and last name** populated in the account properties. Missing name fields will cause the upgrade to fail.
- Supports interactive sign-in (not a service account or headless identity).
- Is permitted to consent to Google's required permissions (Cloud Application Administrator role or equivalent).
- Is not blocked by Conditional Access from accessing Google services (`play.google.com`, `enterprise.google.com`).
- **Domain has a valid MX record** if you intend to use the "Sign in with Microsoft" option during the upgrade flow. Without an MX record, the federated sign-in option may not appear.

> **Recommendation:** Use a dedicated shared admin account for this binding rather than a named personal administrator account. This avoids a dependency on any specific individual's identity for a tenant-level configuration.

#### Upgrade Steps

1. Navigate to **Microsoft Intune admin center** > **Devices** > **Android** > **Enrollment**.
2. Under **Prerequisites**, select **Managed Google Play**.
3. The **Upgrade** button is only visible when the current binding uses a Gmail account. Select **Upgrade**.
4. Select **Upgrade** again to confirm.
5. In the Google popup, follow the prompts. When given the option, select **Sign in with Microsoft**.
6. Grant the required permissions to the Google Workspace application.
7. Review the account information form — first and last name should be auto-populated from the Entra ID account. Verify these are correct before proceeding.
8. In the subscriptions section, confirm **Android Enterprise** is checked.
9. Accept the terms and confirm the upgrade.
10. Wait for the process to complete. The linked account displayed in Intune will update to reflect the new Entra ID identity, and the **Upgrade** button will no longer be available.

#### Critical Warnings

> **Important:** Do not disconnect the existing Google Account before upgrading. Disconnecting the binding removes all associated apps and enrollment profiles, rendering enrolled devices unmanaged. There is no recovery path other than re-enrolling all devices. Always use the **Upgrade** button — never **Disconnect** — when migrating from Gmail to Entra ID.

> **Important:** Verify the Entra ID account has first and last name populated before starting. An incomplete profile may cause the process to fail part-way through. Confirm the account properties in the Microsoft Entra admin center before initiating the upgrade.

#### Post-Upgrade Verification

After completing the upgrade, confirm the following in the Intune admin center:

- The **Linked account** field under **Managed Google Play** shows the Entra ID account UPN (not a Gmail address).
- The **Upgrade** button is no longer available.
- Existing approved apps remain visible and assigned under **Apps** > **Android**.
- Existing enrollment profiles are intact and functioning.
