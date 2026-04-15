# Lab 01 – Setup & Active Directory

## Objective
Set up a basic Active Directory environment using Windows Server 2022 and connect a Windows 10 client to simulate a company network.

## Lab Setup / Environment
- **DC-01 (Windows Server 2022)**
  - 2 vCPUs / 4GB RAM
  - Adapter 1: NAT
  - Adapter 2: Internal Network (lab-net)
- **USER-01 (Windows 10 Pro)**
  - 2 vCPUs / 4GB RAM
  - Adapter 1: Internal Network (lab-net)

---

## Phase 0: VM Setup & VirtualBox Configuration
1. **Machine Creation:**
   - DC-01: Windows Server 2022 ISO, 4GB RAM, 2 CPU cores.
   - USER-01: Windows 10 ISO, 4GB RAM, 2 CPU cores.
2. **General Settings:**
   - Enabled Shared Clipboard and Drag-and-Drop (Bidirectional) for both VMs.
3. **Network Configuration:**
   - **DC-01:** Adapter 1 set to NAT. Adapter 2 set to Internal Network (Name: `lab-net`).
   - **USER-01:** Adapter 1 set to Internal Network (Name: `lab-net`).

![VirtualBox Network Configuration](screenshots/01-vbox-network-dc-01.png)
     
4. **Guest Additions:**
   - Mounted VBoxWindowsAdditions.iso and executed `VBoxWindowsAdditions-amd64.exe` on both machines to enable full driver support.

## Phase 1: Operating System Deployment
- **DC-01:** Installed Windows Server 2022 Standard (Desktop Experience). Set Administrator password to `P@ssword123`.
- **USER-01:** Installed Windows 10 Pro. 
  - Offline setup: Skipped internet connection (Limited Setup) as DC-01 was not yet acting as a gateway/DNS.
  - User: `j-lanes` | Password: `P@ssword123`.
  - Privacy: Disabled all telemetry, diagnostic data, and Cortana.

![DC-01 Desktop](screenshots/02-desktop-dc-01.png) ![USER-01 Desktop](screenshots/02-desktop-user-01.png)

## Phase 2: Host Identity & Network Connectivity
### 1. Host Renaming
- Renamed Server to `DC-01`.
- Renamed Client to `USER-01`.
- Performed system restarts to apply changes.

### 2. Static IP Assignment (DC-01) & (USER-01)
- Navigated to `ncpa.cpl` to configure IPv4 properties.
- **DC-01:** IP `192.168.10.10` | DNS `127.0.0.1`
- **USER-01:** IP `192.168.10.20` | DNS `192.168.10.10`

![DC-01 IPv4 Config](screenshots/03-ipv4-config-dc-01.png) ![USER-01 IPv4 Config](screenshots/03-ipv4-config-user-01.png)

### 3. IP Verification
- Verified active configurations via `ipconfig`.

![DC-01 ipconfig](screenshots/04-ipconfig-dc-01.png) ![USER-01 ipconfig](screenshots/04-ipconfig-user-01.png)

### 4. Firewall & Verification
- **DC-01:** Modified Windows Defender Firewall Inbound Rules.
  - Enabled: `File and Printer Sharing (Echo Request - ICMPv4-In)`.

![Firewall Rule](screenshots/05-firewall-icmp-rule-dc-01.png) 
    
- **Validation:** Successfully verified connectivity by pinging `192.168.10.10` from the `USER-01` Command Prompt.

![Successful Ping Test](screenshots/06-ping-test-success-user-01.png)

## Phase 3: (To be continued)
  
