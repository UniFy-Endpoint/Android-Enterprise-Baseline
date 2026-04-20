# Managing Android Devices with Microsoft Intune

## Managed Home Screen (MHS) — Configuration Guide

### Overview

Managed Home Screen (MHS) is a Microsoft launcher app for **Dedicated** Android Enterprise devices (Multi-App Kiosk and Shared Device configurations). It replaces the standard Android home screen with a controlled interface that shows only the apps and settings you explicitly allow.

MHS is the recommended launcher for:
- **Multi-App Kiosk** — fixed set of apps, no personal use
- **Shared Device (Managed Home Screen)** — shift workers sign in and out via QR code or PIN

MHS is **not used** on Fully Managed, Corporate-Owned Work Profile, or BYOD Work Profile devices — those use the standard device launcher.

---

### Prerequisites

Before configuring MHS:

1. **Approve MHS in Managed Google Play** — Search for "Managed Home Screen" and approve it.
2. **Assign MHS as Required** to your Kiosk or Shared device group.
3. **Set the enrollment profile** to `dedicatedDevice` in your Device Restrictions policy.
4. **Kiosk Mode must be configured** in the Device Restrictions policy to use MHS as the launcher (for Multi-App mode). This baseline uses the DeviceConfiguration template for this.

---

### Deployment Models and MHS Role

| Deployment Model | MHS Role | Enrollment Profile Setting |
| :--- | :--- | :--- |
| Single-App Kiosk | Not used — single app runs in full screen | `dedicatedDevice` |
| Multi-App Kiosk | Primary launcher — controls which apps and settings are visible | `dedicatedDevice` |
| Shared Device (Shift Workers) | Primary launcher — handles user sign-in/sign-out sessions via QR or PIN | `dedicatedDevice` |

---

### Step 1: Add the App Configuration Policy

1. Navigate to **Apps** > **App configuration policies** > **Add** > **Managed devices**.
2. **Name:** *(See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current baseline policy name.)*
3. **Description:** Managed Home Screen configuration for Dedicated Kiosk and Shared devices.
4. **Platform:** Android Enterprise.
5. **Profile type:** Fully managed, dedicated, and corporate-owned work profile.
6. **Target app:** Select **Managed Home Screen** from Managed Google Play.
7. **Configuration settings format:** Use the **Configuration designer** to add settings via key/value, or switch to **Enter JSON data** for bulk import.
8. **Assignments:** Assign to your Kiosk and/or Shared device groups.

---

### Step 2: Configure Key Settings

The table below covers the most important Managed Home Screen configuration keys. Not all keys are required — configure only what applies to your deployment scenario.

#### General / Session Behavior

| Setting Name | Key | Type | Recommended Value | Notes |
| :--- | :--- | :--- | :--- | :--- |
| Auto-enter kiosk mode | `auto_enter_kiosk_mode` | Boolean | `true` | Device auto-launches MHS on startup |
| Enable session PIN | `com.microsoft.launcher.managed.passwordenabled` | Boolean | `true` | Requires PIN to exit kiosk or switch users |
| Session PIN | `com.microsoft.launcher.managed.password` | String | `<PIN value>` | Static PIN for exiting kiosk mode. Use with caution — store securely |
| Exit kiosk mode code | `exit_lock_task_mode_code` | String | `<PIN value>` | Alternative to session PIN for exiting lock task mode |
| Show home button | `allow_home_button` | Boolean | `false` | Hide native Android home button |
| Show back button | `allow_back_button` | Boolean | `false` | Hide native Android back button |
| Show overview / recent apps button | `allow_overview_button` | Boolean | `false` | Hide recent apps button |

#### App Visibility

| Setting Name | Key | Type | Notes |
| :--- | :--- | :--- | :--- |
| Approved app list | `com.microsoft.launcher.managed.applications` | JSON string | List of app package names approved to appear on the MHS home screen |
| App folders | `com.microsoft.launcher.managed.folders` | JSON string | Define app folders (group multiple apps under a folder tile) |
| Show app list on home screen | `show_app_list` | Boolean | Shows all approved apps as tiles |
| Allow unknown sources | `appsAllowInstallFromUnknownSources` | Boolean | Keep `false` — use MGP for all app delivery |

#### Settings Visibility

| Setting Name | Key | Type | Recommended Value | Notes |
| :--- | :--- | :--- | :--- | :--- |
| Show Wi-Fi settings | `show_wifi_setting` | Boolean | `false` | Hide for kiosk; `true` for shared if users need to switch networks |
| Show Bluetooth settings | `show_bluetooth_setting` | Boolean | `false` | Hide unless Bluetooth peripheral pairing is required by users |
| Show volume setting | `show_volume_setting` | Boolean | `true` | Recommend showing — frustrates users if locked |
| Show notifications | `show_notifications_setting` | Boolean | `false` | Hide for kiosk |
| Show managed settings | `show_managed_settings` | Boolean | `false` | Hide device management settings from users |

#### Shared Device / Session Management

| Setting Name | Key | Type | Notes |
| :--- | :--- | :--- | :--- |
| Enable sign-in session | `com.microsoft.launcher.managed.show.volume.setting` | — | See the QR Code Authentication App Configuration Policy (refer to [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current policy name) for Microsoft Authenticator configuration |
| Session timeout | `inactive_timeout` | Integer | Minutes of inactivity before session ends and device returns to sign-in screen |
| Inactivity screen | `show_managed_home_screen_inactivity_screen` | Boolean | Show a lock/screensaver screen during inactivity |
| Inactivity screen apps | `inactive_app_list` | JSON string | App(s) displayed during the inactivity screensaver (e.g., a company logo app) |

#### Wallpaper and Branding

| Setting Name | Key | Type | Notes |
| :--- | :--- | :--- | :--- |
| Custom wallpaper URL | `com.microsoft.launcher.managed.wallpaper` | String | HTTPS URL to a corporate wallpaper image (PNG or JPG) |
| Show device name on screen | `show_device_info` | Boolean | Display device serial or name on the MHS screen |

---

### Step 3: Define the Approved App List

The `com.microsoft.launcher.managed.applications` key takes a JSON array of app package names. Only apps in this list will appear on the MHS home screen.

**Example format:**

```json
[
  {
    "package": "com.microsoft.teams",
    "class": ""
  },
  {
    "package": "com.microsoft.office.outlook",
    "class": ""
  },
  {
    "package": "com.azure.authenticator",
    "class": ""
  }
]
```

> **Important:** Every app listed here must also be approved in Managed Google Play and assigned as **Required** to the device group. Apps not deployed to the device will appear as broken tiles on the home screen.

---

### Step 4: Assign the Policy

1. Go to **Assignments** in the App Configuration Policy.
2. **Include:** Assign to your Dedicated Kiosk or Shared device groups:
   - `AND - DEV - Android-Corporate-Dedicated-Devices - Multi-App - Kiosk`
   - `AND - DEV - Android-Corporate-Dedicated-Devices - Shared`
3. Do not assign to Fully Managed or Work Profile groups — MHS is not used there.

---

### Minimal Configuration by Scenario

#### Multi-App Kiosk (Frontline Productivity)

| Key | Value |
| :--- | :--- |
| `auto_enter_kiosk_mode` | `true` |
| `allow_home_button` | `false` |
| `allow_back_button` | `false` |
| `allow_overview_button` | `false` |
| `show_wifi_setting` | `false` |
| `show_bluetooth_setting` | `false` |
| `show_volume_setting` | `true` |
| `show_notifications_setting` | `false` |
| `com.microsoft.launcher.managed.applications` | JSON list of approved apps |
| `exit_lock_task_mode_code` | Set a PIN — communicate to helpdesk only |

#### Shared Device (QR Code Sign-In)

| Key | Value |
| :--- | :--- |
| `auto_enter_kiosk_mode` | `true` |
| `allow_home_button` | `false` |
| `allow_back_button` | `false` |
| `allow_overview_button` | `false` |
| `show_wifi_setting` | `true` (if users switch locations) |
| `show_volume_setting` | `true` |
| `inactive_timeout` | `5` (minutes — sign out after 5 min idle) |
| `show_managed_home_screen_inactivity_screen` | `true` |
| `com.microsoft.launcher.managed.applications` | JSON list of approved apps |

> **Note:** For Shared Device QR code authentication, the QR Code Authentication App Configuration Policy for Microsoft Authenticator must also be deployed — MHS alone does not configure the sign-in method. See [SETTINGSOUTPUT.md](../SETTINGSOUTPUT.md) for the current policy name.

---

### Relationship to Device Restrictions

MHS configuration (via App Configuration Policy) works alongside the Device Restrictions policy. The Device Restrictions policy sets the kiosk enrollment mode; MHS controls what the user sees. Both are required for a complete Kiosk or Shared device experience.

| Component | Configured in |
| :--- | :--- |
| Lock device to kiosk mode | Device Restrictions policy (`enrollmentProfile: dedicatedDevice`) |
| Which apps are visible | MHS App Configuration Policy |
| Sign-in method (QR/PIN) | QR Code App Configuration Policy (Authenticator) |
| App availability on device | Managed Google Play + Required app assignment |
| Compliance requirements | Compliance Policy |

---

> **Pilot testing:** Always test MHS configuration with a physical device before broad deployment. Some key combinations (e.g., hiding all navigation buttons with no exit PIN) can lock the device in a state requiring a factory reset to recover. Test the exit procedure with helpdesk before deploying to production.
