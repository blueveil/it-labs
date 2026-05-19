# Lab 04 – Network Automation & Infrastructure Stress Testing

## Objective
The objective of this lab is to transition the `jlab.local` network infrastructure from manual static IP tracking to a dynamic, centralized network management model. By deploying DHCP services on `DC-01`, automating client configuration on `USER-01`, implementing strict MAC-to-IP hardware reservations, and analyzing real-time pool constraints via scope statistics, this project demonstrates an enterprise-grade approach to managing network resources and infrastructure monitoring.

## Lab Setup / Environment
- **DC-01 (Windows Server 2022): Domain Controller & DHCP Server**
  - IP Address: `192.168.10.10`
  - Subnet Mask: `255.255.255.0`
  - Domain: `jlab.local`
  - Network Adapter: Internal Network (`lab-net`)
- **USER-01 (Windows 10 Pro): Workstation Client**
  - Current Config: Static IP (`192.168.10.20`) -> Migrating to DHCP
  - Logged in as: `JLAB\hr-giger` (HR Staff)
  - Network Adapter: Internal Network (`lab-net`)
 
---

## Change Control & Ticket Context

All infrastructure modifications in this lab were executed using a simulated ticket. The initial ticket was established as follows:

    Service Request: SR-1004

    Status: IN PROGRESS

    Priority: Medium

    Target Subnet: 192.168.10.0/24

    Infrastructure Exclusions: 192.168.10.1 - 192.168.10.50

    Primary DNS Assignment: 192.168.10.10 (jlab.local)

---

## Phase 1: DHCP Role Installation & AD Authorization
- On **DC-01**, initiated the installation of the DHCP Server role through Server Manager.
- **Path:** `Server Manager > Manage > Add Roles and Features`
- **Action:** Selected the **DHCP Server** role and confirmed the required management features.
- After the deployment succeeded, used the Post-Deployment Configuration Wizard to authorize the server.
- **Security Baseline:** Authorized the role within Active Directory using the `JLAB\Administrator` context to ensure compliance and prevent unauthorized rogue DHCP servers on the subnet.

![DHCP Role Selection](/lab-04-network-automation/screenshots/01-dhcp-role-selection-dc-01.png)
![AD Authorization Wizard](/lab-04-network-automation/screenshots/02-dhcp-authorization-dc-01.png)

---

## Phase 2: Scope Engineering & Network Pooling
- Configured a new network pool in the **DHCP Manager** (`dhcpmgmt.msc`) targeting workstation subnets.
- Expanded `DC-01.jlab.local` -> right-clicked **IPv4** -> selected **New Scope** and named it `jlab_Workstations`.
- **IP Address Range Parameters:**
  - Start IP Address: `192.168.10.1` | End IP Address: `192.168.10.254` | Length: `24` (`255.255.255.0`)
- **Infrastructure Exclusions:**
  - Added an exclusion range from `192.168.10.1` to `192.168.10.50`. This safely protects the Domain Controller (`.10`) and leaves room for firewalls, switches, and other static infrastructure.
- **DHCP Options Configuration:**
  - **Option 003 (Router/Default Gateway):** `192.168.10.1`
  - **Option 006 (DNS Servers):** Explicitly mapped to `192.168.10.10` with parent domain validation matching `jlab.local`.
- Completed the wizard and activated the scope immediately.

![DHCP Scope Configuration](/lab-04-network-automation/screenshots/03-dhcp-scope-dc-01.png)

---

## Phase 3: Client Migration (Static to Dynamic)
- Moved to **USER-01** to switch the endpoint's interface properties away from the static setup deployed in Lab 01.
- Logged in as `JLAB\hr-giger`, launched an elevated Command Prompt, and ran `ncpa.cpl`.
- **Note:** During execution, authenticating administrative rights with `JLAB\Administrator` encountered an issue; successfully pivoted and authenticated using the `JLAB\a-jones` administrative identity.
- **Path:** `Ethernet Properties > Internet Protocol Version 4 (TCP/IPv4) > Properties`
- **Action:** Switched card options to **"Obtain an IP address automatically"** and **"Obtain DNS server address automatically"**.

![Network Properties Box](/lab-04-network-automation/screenshots/04-ipv4-properties-user-01.png)

- From the elevated Command Prompt, forced a lease renewal sequence:
  ```cmd
  ipconfig /release
  ipconfig /renew
  ipconfig /all

- **Validation**: Confirmed the client dropped its old static configuration and pulled `192.168.10.51`. The exact first IP available after the exclusion block along with the correct `jlab.local` DNS assignments.
- Switched back to **DC-01** and validated that the lease was successfully logged in the server's database.

---

## Phase 4: Implementing Hardened Infrastructure Reservations
- To maintain predictable identity tracking for corporate workstations, converted the dynamic lease into a permanent hardware-bound reservation.
- In the **Address Leases** folder view on **DC-01**, right-clicked the existing lease for `USER-01` and selected **Add to Reservation**.
- Clicked on the **Reservations** folder nested directly under the Scope tree in the left pane to audit the entry, confirming that the workstation's MAC address is now permanently bound to its IP.

![MAC-to-IP Reservation Setup](/lab-04-network-automation/screenshots/07-dhcp-reservation-dc-01.png)

---

## Phase 5: Verification & Troubleshooting

### 1. Scope Exhaustion Injection
- To demonstrate how to identify and respond when core infrastructure faces constraint limits, an artificial pool capacity block was intentionally introduced.
- On **DC-01**, right-clicked **Address Pool** within the DHCP Manager window and selected **New Exclusion Range**.
- **Action:** Inputted an artificial blockage range from `192.168.10.52` to `192.168.10.254` and clicked Add.
- **Discovery:** With `.1-.50` and `.52-.254` excluded, the network pool was constricted to exactly one valid address (`.51`). Because `USER-01` already claimed `.51` via its permanent reservation, the pool allocation capacity was driven into 100% exhaustion.

![Scope Exhaustion Configuration Setup](/lab-04-network-automation/screenshots/08-address-exhaustion-simulation.png)

### 2. Operational Metrics Auditing
- Right-clicked `Scope [192.168.10.0] jlab_Workstations` in the left pane of the DHCP Manager and selected **Display Statistics**.
- **Fix / Discovery:** Confirmed the metrics dashboard instantly flagged the scope under stress, showing **Addresses In Use: 100%** and **Addresses Available: 0 (0%)**. This verified real-time network metric visibility during infrastructure constraints.

![Scope Statistics Pool Exhaustion](/lab-04-network-automation/screenshots/09-dhcp-scope-statistics.png)

### 3. Functional Confirmation
- Closed the statistics pop-up metrics window.
- Right-clicked the artificial exclusion range (`.52` to `.254`) inside the **Address Pool** folder, selected **Delete**, and confirmed the removal.
- **Result:** Safely reopened the scope allocation capabilities and successfully returned the domain infrastructure pool to standard baseline health.

---

## Phase 6: Close the Service Request Ticket

- Opened the `sr-04-dhcp-deployment.md` tracking ticket in the repository.
- Created `sr-04-dhcp-deployment-resolved.md`, then updated the document status from **IN PROGRESS** to **RESOLVED** and appended comprehensive implementation details and metric results to finalize the portfolio audit trail.

---
**Lab 04 Finished.**
