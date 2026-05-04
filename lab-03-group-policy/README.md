# Lab 03 – Group Policy | Centralized Endpoint Management & Hardening

## Objective
The objective of this lab was to implement centralized management across the domain using Group Policy Objects (GPOs). By enforcing security baselines, automating resource mapping, and hardening the workstation environment, I demonstrated how to maintain consistency and security across an enterprise network from a single pane of glass.

## Lab Setup / Environment
- **DC-01: Domain Controller & GPO Management**
  - OS: Windows Server 2022
  - IP: `192.168.10.10`
- **USER-01: Domain Client**
  - OS: Windows 10 Pro
  - Target User: `JLAB\s-borne` (Sales Department)
  - Testing Focus: Policy application and preference processing.

---

## Phase 1: Security Baselines (Domain Level)
Security starts at the root. I configured policies that apply to every account within the `jlab.local` forest.
- **Corporate Password Policy:** Enforced a 10-character minimum and complexity requirements via the **Default Domain Policy** to mitigate brute-force risks.
- **Interactive Login Banner:** Created `SEC_Login_Banner` to display a legal notice upon login. This ensures all users are notified that "Activity is monitored," meeting standard compliance requirements.

![Login Banner Settings](screenshots/03-gpo-login-banner-dc-01.png)

---

## Phase 2: Endpoint Hardening & Environment Control
To reduce the attack surface and prevent unauthorized configuration changes, I targeted the `_Company` OU with restrictive policies.
1. **Control Panel Restriction:** Enabled a GPO to prohibit access to the Control Panel and PC Settings. This prevents standard users from modifying network settings or installing unapproved software.
2. **Idle Session Security:** Implemented a **10-minute (600s) Screen Lock** policy with password protection to secure unattended workstations.
3. **Corporate Branding:** Deployed a mandatory desktop wallpaper via a centralized UNC path (`\\DC-01\Public\`) to maintain a professional corporate image across all departments.

---

## Phase 3: Automation via Group Policy Preferences (GPP)
I utilized **Group Policy Preferences** to improve user productivity by automating resource access.
- **Dynamic Drive Mapping:** Configured a GPO to automatically map the `S:` drive (Sales Data) for users. 
- **Strategy:** This eliminates the need for manual mapping by end-users and ensures that departmental storage is always available upon login.

![Drive Map Configuration](screenshots/05-drive-map-new-dc-01.png)

---

## Phase 4: Troubleshooting & Policy Auditing
A critical part of this lab was diagnosing why certain policies failed to apply initially.

| Issue | Root Cause | Resolution |
| :--- | :--- | :--- |
| **Wallpaper Failure** | Extension Mismatch (GPO cited `.jpg`, file was `.png`). | Updated GPO path to match the source file extension. |
| **Drive Map Failure** | UNC Path Accessibility (Folder was local but not shared). | Enabled **Advanced Sharing** on the Sales folder to make the UNC path reachable. |
| **GPO Latency** | Policies didn't update immediately. | Executed `gpupdate /force` on the client to trigger an immediate pull. |

---

## Phase 5: Verification
I validated the success of the deployment through both command-line audits and visual confirmation on **USER-01**.
- **Audit:** Ran `gpresult /r` to confirm the client was successfully receiving and filtering the GPOs.
- **Functional Check:** 
  - Verified the **Access Denied** message when attempting to open the Control Panel.
  - Confirmed the **S: Drive** appeared automatically in File Explorer.
  - Confirmed the **Security Banner** appeared during the Windows logon sequence.

![Wallpaper Applied](screenshots/12-user-wallpaper-confirmed-user-01.png)
![S: Drive Visible](screenshots/14-user-drive-maps-confirmed-user-01.png)

---

## Outcome & Key Takeaways
This lab demonstrated that Group Policy is the most powerful tool in a Windows Administrator's arsenal. I learned that **UNC paths must be meticulously verified** and that the **GPMC** is essential for maintaining a secure and standardized environment. Mastering GPO allows an admin to manage 1,000 machines as easily as they manage one.

---

**Lab 03 Finished.**
