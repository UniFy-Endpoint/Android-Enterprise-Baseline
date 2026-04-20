# Managing Android Devices with Microsoft Intune

## Mobile Application Management (MAM) – Unmanaged Devices

### Overview

### 1. What is Mobile Application Management (MAM)
Mobile Application Management (MAM) in Microsoft Intune allows IT administrators to secure and manage corporate data within specific applications without requiring the user to enroll their entire device into a management service. 

This approach is ideal for **BYOD (Bring Your Own Device)** scenarios, where employees use personal Android or iOS devices to access work resources. MAM ensures that corporate data remains isolated, encrypted, and protected, while personal data (photos, personal emails, etc.) remains private and untouched by the organization.

### 2. MAM vs. MDM Comparison
The following table compares the functionalities and options of Mobile Application Management (MAM) versus Mobile Device Management (MDM) based on the core concepts of the guide.

| Feature / Option | Mobile Application Management (MAM) | Mobile Device Management (MDM) |
| :--- | :--- | :--- |
| **Scope of Control** | Manages specific apps and data only. | Manages the entire operating system and device settings. |
| **Device Ownership** | Primarily for personal/unmanaged devices (BYOD). | Primarily for corporate-owned/managed devices. |
| **User Privacy** | High: IT cannot see or touch personal apps/data. | Low: IT has visibility and control over the whole device. |
| **Deployment** | Fast and non-intrusive; no enrollment required. | Requires device enrollment and management profile. |
| **Data Security** | Uses App Protection Policies (APP) to encrypt data. | Uses device-level encryption and compliance policies. |
| **Wipe Capabilities** | **Selective Wipe:** Removes only corporate data. | **Full Wipe:** Can factory reset the entire device. |

---

### 3. Prerequisites for Configuration

Before starting the configuration, ensure you have the following:

* **User Group:** A Microsoft Entra ID security group containing all MAM-enabled users.
* **Licenses:** **Microsoft Intune license**  **Microsoft Entra ID P1** (for Conditional Access) assigned to users group.
* **Target Devices:** Unmanaged Android or iOS devices.

---

### 4. Step-by-Step Configuration Process

#### Step 1: Create Microsoft Entra ID Dynamic User Group
This group will automatically include users who have an Intune license, ensuring they are targeted by the MAM policies.

| Step | Action | Details / Values |
| :--- | :--- | :--- |
| 1 | Navigate to Microsoft Entra ID | Go to **Groups** > **All groups** > **New group**. |
| 2 | Set Group Type | Select **Security**. |
| 3 | Define Membership Type | Select **Dynamic User**. |
| 4 | Add Dynamic Query | Click **Add dynamic query** and use the rule below. |
| 5 | Input Membership Rule | `user.assignedPlans -any (assignedPlan.servicePlanId -eq "c1ec4a95-1f05-45b3-a911-aa3fa01094f5" -and assignedPlan.capabilityStatus -eq "Enabled")` |

---

#### Step 2: Create Conditional Access Policy
This policy acts as the gatekeeper, ensuring that users can only access corporate data via apps that support App Protection.

| Step | Action | Configuration Setting |
| :--- | :--- | :--- |
| 1 | Navigate to CA | **Intune Admin Center** > **Endpoint Security** > **Conditional Access**. |
| 2 | Name Policy | `CA-AllUsers-AllApps-Android-Req-AppProtectionPolicy-BYOD` |
| 3 | Assignments: Users | **Include:** All users; **Exclude:** Break-glass/Emergency accounts. |
| 4 | Target Resources | Select **All cloud apps**. |
| 5 | Conditions | **Device platforms:** Include Android. |
| 6 | Grant Access | Select **Grant access** and check **Require app protection policy**. |
| 7 | Enable Policy | Set the toggle to **On** and Save. |

---

#### Step 3: Create App Protection Policy (Level 1 Protection)
This policy defines the security boundaries within the apps. The settings below follow the **Level 1: Enterprise Basic Data Protection** recommendations.

#### A. Apps Selection
1.  Navigate to **Apps** > **App protection policies** > **Create policy** > **Android** (or iOS).
2.  **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
3.  **Description:** Level 1 enterprise basic data protection – Microsoft recommends this configuration as the minimum data protection configuration for an enterprise device.
3.  **Targeting:** Select **All Apps** or specific Microsoft 365 apps (Outlook, Teams, OneDrive).
4.  **Settings:** `Adapt and configure each setting according to your organization's and customer requirements after importing the baseline policy configuration .JSON file.`


#### B. Data Protection Settings
| Option | Setting |
| :--- | :--- |
| **Backup org data to device backups** | **Block** |
| **Send org data to other apps** | **Policy managed apps** |
| **Receive data from other apps** | **All apps** |
| **Save copies of org data** | **Block** |
| **Allow user to save copies to services** | **OneDrive for Business, SharePoint** |
| **Restrict cut, copy, and paste** | **Policy managed with paste in** |
| **Screen capture / Google Assistant** | **Block** |
| **Encrypt org data** | **Require** |
| **Printing org data** | **Block** |

#### C. Access Requirements
| Option | Setting |
| :--- | :--- |
| **PIN for access** | **Require** |
| **PIN type** | **Numeric** |
| **Simple PIN** | **Block** |
| **Minimum PIN length** | **4** |
| **Biometrics (Fingerprint/FaceID)** | **Allow** |
| **App PIN when device PIN is set** | **Require** |

#### D. Conditional Launch
| Option | Value / Action |
| :--- | :--- |
| **Max PIN attempts** | **5** (Action: Reset PIN) |
| **Offline grace period** | **720 minutes** (Action: Block access) |
| **Offline grace period (Wipe)** | **90 days** (Action: Wipe data) |
| **Disabled account** | **Action: Wipe data** |

---

### 5. Conclusion
By implementing these three pillars—**Dynamic Groups**, **Conditional Access**, and **App Protection Policies**—you successfully create a secure container for corporate data on personal devices. This ensures compliance and security without the overhead or privacy concerns associated with full device enrollment (MDM).

---

> **Android 15 — Private Space:** Android 15 introduces a Private Space feature that creates a secondary, isolated profile on the device. Microsoft Intune does not support managing or enrolling the Android 15 Private Space. Users who enable Private Space on BYOD devices can install unmanaged apps there outside of organizational control. This is a known platform limitation — communicate it to users and helpdesk as needed.

