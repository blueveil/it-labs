# Lab 02 – Users, Groups, and Permissions | Implementing Least Privilege

## Objective
The goal of this lab was to transform a flat Active Directory environment into a simulated corporate hierarchy. By establishing departmental Organizational Units (OUs), managing security groups, and configuring complex NTFS permissions, I implemented a "Zero Trust" approach to file sharing. This project demonstrates proficiency in identity management and securing data at rest.

## Lab Setup / Environment
- **DC-01 (Windows Server 2022: Domain Controller & File Server**
  - IP: `192.168.10.10`
  - Roles: AD DS, DNS, File & Storage Services
- **USER-01 (Windows 10 Pro): Domain Client**
  - IP: `192.168.10.20`
- **Domain:** `jlab.local`
 
---

## Phase 1: Organizational Unit (OU) & Departmental Design
To avoid "Active Directory clutter," I designed a logical hierarchy that mirrors a real-world corporate office.
1. **Top-Level OU:** Created `_Company` to house all custom objects.
2. **Sub-OUs:** Established `IT`, `HR`, and `Sales` containers. 
3. **Purpose:** This structure ensures that future Group Policies (GPOs) can be targeted specifically to departments (e.g., mapping the HR drive only to HR staff).

![OU Structure](screenshots/01-ad-it-hr-sales-dc-01.png)

---

## Phase 2: Identity & Access Management (IAM)
I followed the industry standard of **AGP (Accounts > Global Groups > Permissions)** for managing access.
- **Security Groups:** Created `IT_Admins`, `HR_Staff`, and `Sales_Team`.
- **User Provisioning:** Created two users per department (e.g., `s-monroe`, `hr-giger`) to test concurrent access and group inheritance.
- **Group Nesting:** Assigned users to their respective security groups, ensuring that permissions are never assigned to individual users, but rather to the roles they hold.

![Group Membership](screenshots/02-group-membership-dc-01.png)

---

## Phase 3: Secure File Share Architecture
I configured a centralized file repository on **DC-01** designed for departmental isolation.
1. **Directory Structure:** Created `C:\_Shared` with subdirectories for `HR`, `IT`, and `Sales`.
2. **Service Optimization:** Started the **Function Discovery Publication** service to ensure the file server is discoverable over the network.
3. **Share Permissions:** Set Shared Permissions to `Everyone: Full Control`. 
   - *Strategy: I intentionally used broad share permissions to allow the more granular NTFS permissions to act as the definitive security layer.*

---

## Phase 4: NTFS Hardening & Inheritance
This phase focused on the "Principle of Least Privilege" by breaking permission inheritance to prevent "permission creep."
- **Explicit Permissions:** Disabled inheritance via `Advanced Security Settings` and converted existing permissions to explicit.
- **Administrative Lockdown:** Removed the default `Users` group to block general access and added the specific departmental Security Group with **Modify** rights.

![HR Lockdown](screenshots/03-ntfs-lockdown-hr-dc-01.png)

---

## Phase 5: Troubleshooting Traversal Rights
During testing, I encountered a "permission paradox" where users had explicit rights to their sub-folders but were blocked at the root level of the share.

| Issue | Root Cause | Resolution |
| :--- | :--- | :--- |
| **"Access Denied" at Root** | Users lacked permissions to navigate through `\\DC-01\_Shared`. | Added the **Users** group to the root folder with **Read & Execute** permissions. |
| **Data Visibility Risk** | Standard Read access would allow users to see all other folder names. | Scoped the permission to **"This folder only"** via Advanced NTFS settings to allow pass-through access without visibility into other OUs. |

---

## Phase 6: Verification & Security Testing
To validate the security posture, I performed both positive and negative testing from **USER-01**.

1. **Functional Test (Success):** Logged in as `s-borne` (Sales). Verified the ability to browse to `\\DC-01\_Shared\Sales` and successfully performed a "Write" test by creating `Test.txt`.
2. **Security Test (Failure):** Attempted to access `\\DC-01\_Shared\HR` while logged in as the Sales user.
   - **Result:** Access was successfully denied by the NTFS ACLs, confirming that the departmental "silos" were secure.

![Access Denied Proof](screenshots/07-access-denied-hr-folder-sales-user-01.png)

---

## Outcome & Key Takeaways
This lab successfully demonstrated the importance of **NTFS Inheritance** and the **AGP** method. I learned that security is a balance: denying all access is easy, but configuring "Traversal Rights" ensures that security doesn't break usability. This foundation is now prepared for automated management via Group Policy.

---

**Lab 02 Finished.**
