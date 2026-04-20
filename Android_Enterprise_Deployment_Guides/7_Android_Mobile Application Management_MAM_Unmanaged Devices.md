# Managing Android Devices with Microsoft Intune

## Mobile Application Management (MAM) – Unmanaged Devices

### Overview

Mobile Application Management (MAM) in Microsoft Intune allows organizations to protect corporate data within specific applications without requiring full device enrollment. Policies are applied at the app layer — corporate data is encrypted, isolated, and controlled inside managed apps, while the rest of the device remains personal and untouched.

This approach is ideal for **BYOD (Bring Your Own Device)** scenarios where employees use personal Android or iOS devices to access work resources and device enrollment is not practical or acceptable. MAM can also complement MDM enrollment by adding an additional layer of app-level data protection on enrolled devices.

---

### What Is Mobile Application Management (MAM)

MAM protects corporate data by enforcing policies within supported apps — such as Microsoft Outlook, Teams, Edge, and OneDrive. When a user opens a managed app with their work account, the App Protection Policy takes effect: data sharing, copy/paste, screenshot capture, and backup behavior are all controlled by IT policy. When the user switches to a personal app, the policy does not apply.

This model gives organizations control over corporate data without managing the personal device itself. IT can selectively wipe only the corporate data from a managed app without affecting personal data, photos, or settings on the device.

---

### MAM vs. MDM Comparison

| Feature | Mobile Application Management (MAM) | Mobile Device Management (MDM) |
| :--- | :--- | :--- |
| **Scope of Control** | Manages specific apps and corporate data only | Manages the entire device — OS, settings, and apps |
| **Device Ownership** | Primarily for personal or unmanaged BYOD devices | Primarily for corporate-owned or enrolled devices |
| **User Privacy** | High — IT cannot access or view personal apps or data | Lower — IT has visibility and control at the device level |
| **Deployment** | Non-intrusive — no device enrollment required | Requires device enrollment and a management profile |
| **Data Security** | App Protection Policies (APP) encrypt and isolate corporate data | Device-level encryption and compliance policies |
| **Wipe Capability** | **Selective wipe** — removes only corporate app data | **Full wipe** — can factory reset the entire device |
| **Conditional Access** | Enforced via "Require app protection policy" grant control | Enforced via device compliance |

---

### Prerequisites

Before configuring MAM, ensure the following are in place:

* **Microsoft Entra ID user group:** A security group containing all users who will receive MAM policies.
* **Licensing:**
  * **Microsoft Intune license** — required to assign App Protection Policies.
  * **Microsoft Entra ID P1** — required for Conditional Access enforcement.
* **Target devices:** Unmanaged (non-enrolled) Android or iOS devices.
* **Microsoft Entra ID device registration:** For Microsoft 365 apps (Outlook, Teams, OneDrive), the device must be registered with Microsoft Entra ID. Users will be prompted to register if the device is not already registered when they first sign in to a managed app.

---

### Step-by-Step Configuration

#### Step 1: Create Microsoft Entra ID Dynamic User Group

This group automatically includes all users who have an Intune license assigned, ensuring MAM policies are applied to the correct users without manual maintenance.

| Step | Action | Details |
| :--- | :--- | :--- |
| 1 | Navigate to Microsoft Entra ID | Go to **Groups** > **All groups** > **New group** |
| 2 | Set Group Type | Select **Security** |
| 3 | Define Membership Type | Select **Dynamic User** |
| 4 | Add Dynamic Query | Select **Add dynamic query** and use the rule below |
| 5 | Input Membership Rule | `user.assignedPlans -any (assignedPlan.servicePlanId -eq "c1ec4a95-1f05-45b3-a911-aa3fa01094f5" -and assignedPlan.capabilityStatus -eq "Enabled")` |

---

#### Step 2: Create Conditional Access Policy

This policy enforces that users can only access corporate data on Android through apps that have an active App Protection Policy. Without this, a user could bypass MAM by accessing corporate resources through an unmanaged browser or app.

| Step | Action | Configuration |
| :--- | :--- | :--- |
| 1 | Navigate | **Intune Admin Center** > **Endpoint Security** > **Conditional Access** |
| 2 | Name the policy | `CA-AllUsers-AllApps-Android-Req-AppProtectionPolicy-BYOD` |
| 3 | Users | **Include:** All users; **Exclude:** Break-glass and emergency accounts |
| 4 | Target resources | Select **All cloud apps** |
| 5 | Conditions | **Device platforms:** Include **Android** |
| 6 | Grant access | Select **Grant access** > check **Require app protection policy** |
| 7 | Enable policy | Set to **On** and save |

---

#### Step 3: Create App Protection Policy (Level 1 — Enterprise Basic Data Protection)

App Protection Policies define the security boundaries within managed apps. The settings below follow Microsoft's **Level 1: Enterprise Basic Data Protection** recommendations, which represent the minimum recommended configuration for any enterprise deployment.

##### A. Policy Basics

1. Navigate to **Apps** > **App protection policies** > **Create policy** > **Android**.
2. Configure:
   * **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
   * **Description:** Level 1 enterprise basic data protection — minimum recommended data protection configuration for an enterprise device.
   * **Targeted apps:** Select **All Apps** or specific Microsoft 365 apps (Outlook, Teams, OneDrive).
3. **Settings:** Adapt and configure each setting to your organization's requirements after importing the baseline JSON file.

##### B. Data Protection Settings

| Setting | Value |
| :--- | :--- |
| **Backup org data to device backups** | Block |
| **Send org data to other apps** | Policy managed apps |
| **Receive data from other apps** | All apps |
| **Save copies of org data** | Block |
| **Allow user to save copies to selected services** | OneDrive for Business, SharePoint |
| **Restrict cut, copy, and paste** | Policy managed with paste in |
| **Screen capture and Google Assistant** | Block |
| **Encrypt org data** | Require |
| **Print org data** | Block |

##### C. Access Requirements

| Setting | Value |
| :--- | :--- |
| **PIN for access** | Require |
| **PIN type** | Numeric |
| **Simple PIN** | Block |
| **Minimum PIN length** | 4 |
| **Biometrics (fingerprint/Face ID)** | Allow |
| **App PIN when device PIN is set** | Require |

##### D. Conditional Launch

| Condition | Value | Action |
| :--- | :--- | :--- |
| **Max PIN attempts** | 5 | Reset PIN |
| **Offline grace period** | 720 minutes | Block access |
| **Offline grace period (wipe)** | 90 days | Wipe data |
| **Disabled account** | — | Wipe data |

---

### Microsoft App Protection Policy Framework

Microsoft defines three levels of App Protection Policy configuration, aligned to the Microsoft Security Configuration Framework:

| Level | Name | Applies To | Description |
| :--- | :--- | :--- | :--- |
| **Level 1** | Enterprise Basic Data Protection | All BYOD users | Minimum baseline — PIN, encryption, selective wipe |
| **Level 2** | Enterprise Enhanced Data Protection | BYOD users handling sensitive data | Stronger data leakage controls, advanced PIN, offline restrictions |
| **Level 3** | Enterprise High Data Protection | Users handling highly regulated data | Maximum restrictions, Mobile Threat Defense integration |

The baseline policy in this guide implements Level 1. See the [Android Enterprise security configuration framework](https://learn.microsoft.com/en-us/mem/intune/enrollment/android-configuration-framework) and the [CIS hardening guide](12_Android_CIS_Compliance_Hardening_Guide.md) for Level 2 and Level 3 recommendations.

---

> **Android 15 — Private Space:** Android 15 introduces a Private Space feature that creates an additional isolated profile on the device. Intune does not support managing or enrolling the Android 15 Private Space. Users who enable Private Space on BYOD devices can install unmanaged apps there outside of organizational control. Communicate this limitation to users and the helpdesk as needed.
