# SERVICE REQUEST: SR-1004
**Status:** IN PROGRESS
**Priority:** Medium
**Requester:** Network Operations / IT Infrastructure Team
**Assigned To:** [Your Name]

**Description:** 
Deploy centralized DHCP services on DC-01 to replace legacy manual static IP 
configurations for client workstations on the 'lab-net' internal network. 
Implement reservation baselines and validate pool metrics under simulated exhaustion constraints.

**Scope Parameters:**
- Target Subnet: 192.168.10.0/24
- Infrastructure Exclusions: 192.168.10.1 - 192.168.10.50
- Primary DNS Assignment: 192.168.10.10 (jlab.local)

**Tasks:**
- [ ] Install and Authorize DHCP Server Role on DC-01
- [ ] Configure Enterprise Scope & Exclusions
- [ ] Migrate USER-01 from Static to Dynamic IP allocation
- [ ] Enforce MAC-to-IP Device Reservation for USER-01
- [ ] Simulate Scope Exhaustion & Audit Pool Statistics