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
4. **Guest Additions:**
   - Mounted VBoxWindowsAdditions.iso and executed `VBoxWindowsAdditions-amd64.exe` on both machines to enable full driver support.

## Phase 1: Operating System Deployment
- **DC-01:** Installed Windows Server 2022 Standard (Desktop Experience). Set Administrator password to `P@ssword123`.
- **USER-01:** Installed Windows 10 Pro. 
  - Offline setup: Skipped internet connection (Limited Setup) as DC-01 was not yet acting as a gateway/DNS.
  - User: `j-lanes` | Password: `P@ssword123`.
  - Privacy: Disabled all telemetry, diagnostic data, and Cortana.

## Phase 2: Host Identity & Network Connectivity
### 1. Host Renaming
- Renamed Server to `DC-01`.
- Renamed Client to `USER-01`.
- Performed system restarts to apply changes.

### 2. Static IP Assignment (DC-01)
- Navigated to `ncpa.cpl`.
- **Ethernet 2 (NIC 2 - Internal):**
  - IPv4 Address: `192.168.10.10`
  - Subnet Mask: `255.255.255.0`
  - DNS Server: `127.0.0.1` (Loopback)

### 3. Static IP Assignment (USER-01)
- Navigated to `ncpa.cpl`.
- **Ethernet (Internal):**
  - IPv4 Address: `192.168.10.20`
  - Subnet Mask: `255.255.255.0`
  - DNS Server: `192.168.10.10` (Pointing to Domain Controller)

### 4. Firewall & Verification
- **DC-01:** Modified Windows Defender Firewall Inbound Rules.
  - Enabled: `File and Printer Sharing (Echo Request - ICMPv4-In)`.
- **Validation:** Successfully verified connectivity by pinging `192.168.10.10` from the `USER-01` Command Prompt.

## Phase 3: (To be continued)
  
