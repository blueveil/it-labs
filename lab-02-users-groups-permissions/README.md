# Lab 02 – Users, Groups, and Permissions

## Objective
To transform a basic domain into a functional corporate environment by implementing a departmental hierarchy, automating access via security groups, and enforcing the **Principle of Least Privilege (PoLP)** through granular NTFS permissions.

## Lab Setup / Environment
- **DC-01:** Windows Server 2022 (Domain Controller & File Server)
- **USER-01:** Windows 10 Pro (Domain-joined Workstation)
- **Scope:** Deployment of HR, IT, and Sales departmental structures and secure file shares.

---

## Phase 1: Organizational Structure (OUs)
I established a logical hierarchy within **Active Directory Users and Computers (ADUC)** to manage departmental objects. 
- Created a root OU named **`_Company`**.
- Created sub-OUs for **`HR`**, **`IT`**, and **`Sales`**. This structure allows for targeted Group Policy application and administrative delegation.

![AD OU Structure](screenshots/01-ad-it-hr-sales-dc-01.png)

## Phase 2: User & Group Provisioning
Following industry best practices, I implemented group-based access control. Permissions are never assigned to individuals; they are assigned to groups.
1. **Security Groups:** Created `HR_Staff`, `IT_Admins`, and `Sales_Team` (Global Security Groups).
2. **User Accounts:** Created two users per department (e.g., `s-monroe`). All accounts were configured with a standard baseline password (`P@ssword123`).
3. **Membership:** Users were nested into their respective departmental groups to automate permission inheritance.

![Group Membership](screenshots/02-group-membership-dc-01.png)

## Phase 3: File Server & Network Sharing
I configured **DC-01** to serve as the organization's file host.
1. **Network Services:** Enabled **Network Discovery** and **File and Printer Sharing**. Set the **Function Discovery Publication** service to Automatic to ensure server visibility on the network.
2. **Directory Creation:** Created `C:\_Shared` on DC-01 with departmental subfolders: `HR`, `IT`, and `Sales`.
3. **Sharing Layer:** Shared the `_Shared` folder with **Share Permissions** set to `Everyone: Full Control`. This hands off the actual "lockdown" responsibility to the NTFS Security layer.

## Phase 4: NTFS Permissions (The Lockdown)
To secure the data, I broke the default permission inheritance from the C: drive and manually defined access for each department.

1. **Inheritance Break:** For each departmental folder, I disabled inheritance and converted existing permissions to explicit entries.
2. **Access Control:** I removed the `Users (JLAB\Users)` group (which provides access to all domain users) and added only the relevant departmental group with **Modify** permissions.

![NTFS HR Lockdown](screenshots/03-ntfs-lockdown-hr-dc-01.png)
![NTFS IT Lockdown](screenshots/03-ntfs-lockdown-it-dc-01.png)
![NTFS Sales Lockdown](screenshots/03-ntfs-lockdown-sales-dc-01.png)

---

## 🛠️ Troubleshooting: The "Hallway" Access Gap
During initial testing on **USER-01**, I logged in as a Sales user and attempted to reach `\\DC-01\_Shared`. Despite having full rights to the Sales subfolder, the user was met with a **"Network Error: Access is Denied"** at the root.

**The Discovery:** The users had permission to the "room" (Sales folder) but didn't have permission to walk through the "hallway" (the root `_Shared` folder) to get there.

![Initial Root Access Error](screenshots/04-no-access-shared-folder-user-01.png)

**The Fix:** On **DC-01**, I modified the NTFS permissions of the root `_Shared` folder. I added the **`Users`** group with **"Read & Execute"** permissions but applied it to **"This folder only."** This allowed users to see the directory list without gaining any rights to folders they didn't belong to.

---

## Phase 5: Verification (The Proof)
Final verification was performed on **USER-01** using the `s-monroe` (Sales) account.

### 1. Navigation Success
With the "Hallway Fix" applied, the user can now successfully browse to the root share and see the available departmental folders.

![Network Path Verification](screenshots/06-network-path-user-01.png)

### 2. Authorized Access (Positive Test)
The Sales user successfully entered the **Sales** folder and created `Test.txt`. This confirms the **Modify** permission is functioning correctly.

![Authorized Write Success](screenshots/05-sales-create-file-test-user-01.png)

### 3. Unauthorized Access (Negative Test)
The Sales user attempted to enter the **HR** folder. The system strictly enforced the PoLP and returned an **"Access is Denied"** message, confirming the security boundary is intact.

![Unauthorized Access Denied](screenshots/07-access-denied-hr-folder-sales-user-01.png)

---
**Lab 02 Finished.**
