# Lab 03 – Group Policy | Centralized Endpoint Management & Hardening

## Objective
The objective of this lab was to implement centralized management across the domain using Group Policy Objects (GPOs). By enforcing security baselines, automating resource mapping, and hardening the workstation environment, I demonstrated how to maintain consistency and security across an enterprise network from a single pane of glass.

## Lab Setup / Environment
- **DC-01 (Windows Server 2022): Domain Controller & GPO Management**
  - IP: `192.168.10.10`
- **USER-01 (Windows 10 Pro): Domain Client**
  - IP: `192.168.10.20`
  - Logged in as: `JLAB\s-borne` (Sales Team)

---

## Phase 1: Security Baselines (Domain Level)
Security starts at the root. I configured policies that apply to every account within the `jlab.local` forest to establish core security controls.
- **Corporate Password Policy:** Enforced a 10-character minimum and complexity requirements via the **Default Domain Policy** to mitigate brute-force risks at the identity level.
- **Interactive Login Banner:** Created and linked `SEC_Login_Banner` at the domain root to display a legal notice upon logon. This ensures all users are notified that access is authorized only and activity is actively monitored, meeting standard regulatory compliance.

![Login Banner Settings](/lab-03-group-policy/screenshots/03-gpo-login-banner-dc-01.png)

---

## Phase 2: Endpoint Hardening & Environment Control
To reduce the local attack surface and prevent unauthorized configuration drift, I targeted the `_Company` OU with restrictive administrative policies.
1. **Control Panel Restriction:** Enabled a user configuration policy to completely prohibit access to the Control Panel and PC Settings, blocking standard users from altering network configurations or viewing administrative menus.
2. **Idle Session Security:** Implemented a **10-minute (600s) Screen Lock** policy with mandatory password re-authentication to secure unattended corporate workstations.
3. **Corporate Branding:** Deployed an enforced desktop wallpaper via a centralized network UNC path (`\\DC-01\Public\`) to establish a standardized workspace environment.

---

## Phase 3: Automation via Group Policy Preferences (GPP)
I utilized **Group Policy Preferences** to improve user workflows and productivity by automating resource provisioning dynamically.
- **Dynamic Drive Mapping:** Configured a preference policy to automatically map the `S:` drive (Sales Data network share) for targeted corporate identities. 
- **Strategy:** This eliminates the need for manual script mapping or end-user intervention, ensuring that critical departmental storage is reliably mounted upon user initialization.

![Drive Map Configuration](/lab-03-group-policy/screenshots/05-drive-map-new-dc-01.png)

---

## Phase 4: Troubleshooting & Policy Auditing
An essential part of this deployment was utilizing systemic diagnostic tracking to determine why specific elements failed to apply during initial client checks.

| Issue | Root Cause | Resolution |
| :--- | :--- | :--- |
| **Wallpaper Failure** | **Extension Mismatch:** The GPO parameter targeted a `.jpg` string, while the actual asset in the public share was a `.png`. | Updated the GPO source path to accurately match the file system extension. |
| **Drive Map Failure** | **UNC Path Accessibility:** The destination folder existed locally on the server filesystem but lacked an active SMB share instance. | Configured **Advanced Sharing** on the target subfolder to expose the UNC path to the network. |
| **GPO Latency** | **Refresh Interval Delay:** The workstation had not yet requested an update check from the Domain Controller. | Executed `gpupdate /force` from the client Command Prompt to trigger an immediate pull. |

---

## Phase 5: Verification & Security Testing
I validated the success of the deployment through both command-line policy evaluation and visual environment confirmation on **USER-01**.
- **Policy Audit:** Ran `gpresult /r` on the client workstation to confirm the OS was actively receiving, processing, and executing the administrative GPO baseline.
- **Functional Checks:** 
  - Verified the **Access Denied** message blocks standard users from launching system settings.
  - Confirmed the **S: Drive** automatically populates within File Explorer with write privileges.
  - Confirmed the **Security Banner** intercepts the user session during the Windows logon sequence.

![Wallpaper Applied](/lab-03-group-policy/screenshots/12-user-wallpaper-confirmed-user-01.png)
![S: Drive Visible](/lab-03-group-policy/screenshots/14-user-drive-maps-confirmed-user-01.png)

---

## Outcome & Key Takeaways
This lab successfully demonstrated that Group Policy is the most powerful tool in a Windows Administrator's arsenal. I learned that **UNC paths and file extensions must be meticulously verified** during stage planning, and that the **GPMC** is the definitive framework for maintaining a secure, standardized endpoint environment. Mastering GPO design allows an administrator to manage 1,000 machines as efficiently as they manage one.

---
**Lab 03 Finished.**
