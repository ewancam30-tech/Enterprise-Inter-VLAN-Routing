# Enterprise Inter-VLAN Routing Project

## Router-on-a-Stick Implementation, Verification, and Troubleshooting

**Enterprise Network Engineering Portfolio**
**Project 4**

---

## Executive Summary

This project documents the design, implementation, verification, and troubleshooting of enterprise inter-VLAN routing using the **Router-on-a-Stick** model.

The network builds directly on the VLAN segmentation completed in **Project 3**. Project 3 separated departments into individual VLANs. Project 4 restores controlled communication between those VLANs by introducing Layer 3 routing through a Cisco router using IEEE 802.1Q subinterfaces.

The result is an enterprise-style network where HR, Finance, Sales, IT, Printer, Guest, and Management VLANs remain logically segmented while still being able to communicate through a centralized Layer 3 gateway.

---

## Key Takeaways

This project demonstrates the ability to:

1. Design inter-VLAN routing for a segmented enterprise network.
2. Configure Cisco router subinterfaces using 802.1Q encapsulation.
3. Assign default gateways for multiple VLANs.
4. Configure switch trunk links between routers and switches.
5. Verify routing using `show ip route`, `show ip interface brief`, `ping`, and `traceroute`.
6. Troubleshoot trunking, gateway, subinterface, and VLAN assignment issues.
7. Document a network implementation in a professional GitHub portfolio format.

---

## Engineering Change Request

| Field                 | Details                                                                                                                      |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| Change ID             | ECR-NET-004                                                                                                                  |
| Change Title          | Implement Inter-VLAN Routing Using Router-on-a-Stick                                                                         |
| Requested By          | Network Engineering                                                                                                          |
| Business Driver       | Restore controlled communication between departmental VLANs while preserving logical segmentation.                           |
| Change Type           | Planned network configuration change                                                                                         |
| Affected Systems      | Router interface, router subinterfaces, switch trunk links, endpoint gateways                                                |
| Risk Level            | Medium                                                                                                                       |
| Implementation Window | Approved maintenance window                                                                                                  |
| Rollback Plan         | Remove router subinterfaces, restore prior router and switch configurations, and revert endpoint gateway settings if needed. |
| Success Criteria      | All VLANs can communicate through the router and verification commands confirm routing operation.                            |

---

## Business Scenario

Campbell Technologies previously implemented VLAN segmentation to separate departments into individual broadcast domains.

The VLANs improved security and organization, but they also created a communication problem. Devices in one VLAN could not communicate with devices in another VLAN because VLANs are separate Layer 2 broadcast domains.

The business now requires controlled communication between departments while keeping the benefits of segmentation.

### Business Requirements

| Requirement                                   | Reason                                       |
| --------------------------------------------- | -------------------------------------------- |
| HR must communicate with Finance              | Payroll and employee documentation workflows |
| Sales must access IT-managed resources        | Business application and support access      |
| All departments must access the printer VLAN  | Shared printing services                     |
| IT must manage network devices                | Administrative and troubleshooting access    |
| Guest traffic must remain logically separated | Security and access control                  |

---

## Project Objectives

1. Configure Router-on-a-Stick inter-VLAN routing.
2. Create router subinterfaces for each VLAN.
3. Assign each subinterface an IP address to serve as the VLAN default gateway.
4. Configure trunk links between the router and switch infrastructure.
5. Verify same-VLAN and cross-VLAN connectivity.
6. Troubleshoot common inter-VLAN routing failures.
7. Document the design, configuration, evidence, and lessons learned.

---

## Network Architecture

### Architecture Overview

The network uses a Cisco router connected to the switching infrastructure through one physical interface. That interface is divided into logical subinterfaces. Each subinterface is mapped to a VLAN using IEEE 802.1Q tagging.

The switches continue to perform Layer 2 VLAN segmentation. The router performs Layer 3 forwarding between VLANs.

### Topology Diagram

<img width="625" height="311" alt="Enterprise Inter-VLAN Routing Topology" src="https://github.com/user-attachments/assets/4ab47659-bc0b-4229-acb4-6828ecfd4249" />

### Topology Summary

| Device   | Role                                                   |
| -------- | ------------------------------------------------------ |
| Router1  | Performs inter-VLAN routing using subinterfaces        |
| SW1      | Primary access switch and router trunk connection      |
| SW2      | Secondary access switch connected to SW1 through trunk |
| PCs      | Endpoints assigned to departmental VLANs               |
| Printer0 | Shared printer endpoint in VLAN 50                     |

### Logical Connections

| Connection    | From          | To        | Type         |
| ------------- | ------------- | --------- | ------------ |
| Router to SW1 | Router1 Gi0/0 | SW1 Gi0/1 | 802.1Q trunk |
| SW1 to SW2    | SW1 F0/24     | SW2 F0/24 | 802.1Q trunk |

---

## Enterprise Network Topology Analysis

This project represents a common small-to-medium business network design.

The VLANs provide logical separation. The router restores required communication between those VLANs. The trunk links allow multiple VLANs to travel across the same physical cable.

Router-on-a-Stick was selected because it provides a cost-effective Layer 3 solution without requiring a Layer 3 switch.

---

## Design Rationale

### Why Router-on-a-Stick Was Used

Router-on-a-Stick was chosen because it allows one router interface to route between multiple VLANs using subinterfaces.

This reduces hardware requirements while still demonstrating real enterprise networking concepts.

### Why 802.1Q Was Used

IEEE 802.1Q is the standard VLAN tagging method used on trunk links. It allows frames from multiple VLANs to travel across a single physical connection while preserving VLAN identity.

### Why VLAN 999 Was Used as the Native VLAN

VLAN 999 was used as an unused native VLAN for trunk hardening. Production users and management devices are not placed in the native VLAN.

This reduces the risk of untagged traffic being placed into a user VLAN.

---

## Engineering Decisions

| Decision                           | Rationale                                                       |
| ---------------------------------- | --------------------------------------------------------------- |
| Used Router-on-a-Stick             | Cost-effective inter-VLAN routing for a small-to-medium network |
| Used one physical router interface | Reduces cabling and hardware requirements                       |
| Created one subinterface per VLAN  | Provides a dedicated gateway for each VLAN                      |
| Used 802.1Q encapsulation          | Preserves VLAN identity across trunk links                      |
| Used VLAN 99 for management        | Separates administrative traffic from user traffic              |
| Used VLAN 999 as native VLAN       | Keeps untagged trunk traffic away from user VLANs               |
| Limited allowed VLANs on trunks    | Reduces unnecessary VLAN traffic across trunk links             |
| Verified incrementally             | Makes troubleshooting easier by isolating each layer            |

---

## Implementation Risks

| Risk                               | Impact                                    | Mitigation                                  |
| ---------------------------------- | ----------------------------------------- | ------------------------------------------- |
| Incorrect subinterface VLAN tag    | Devices in that VLAN cannot route         | Verify `encapsulation dot1Q` commands       |
| Trunk not configured correctly     | VLAN traffic cannot reach the router      | Verify `show interfaces trunk`              |
| Missing VLAN on allowed trunk list | Specific VLAN loses connectivity          | Add required VLAN to trunk allowed list     |
| Incorrect endpoint gateway         | Host cannot leave its VLAN                | Verify endpoint IP configuration            |
| Native VLAN mismatch               | Unstable or incorrect forwarding behavior | Match native VLAN on both sides of trunk    |
| Physical interface shutdown        | All subinterfaces fail                    | Use `no shutdown` on the physical interface |

---

## Equipment

| Equipment                  |  Quantity | Purpose                                        |
| -------------------------- | --------: | ---------------------------------------------- |
| Cisco 1941 Router          |         1 | Inter-VLAN routing and gateway services        |
| Cisco Catalyst 2960 Switch |         2 | Layer 2 switching, VLAN segmentation, trunking |
| End User PCs               |        10 | Test hosts across VLANs                        |
| Network Printer            |         1 | Shared printer endpoint                        |
| Ethernet Cables            | As needed | Router, switch, and endpoint connections       |
| Cisco Packet Tracer        |         1 | Lab implementation and validation environment  |

---

## IP Addressing Plan

| VLAN | Name           | Network         | Gateway      | Devices   |
| ---: | -------------- | --------------- | ------------ | --------- |
|   10 | HR             | 192.168.10.0/24 | 192.168.10.1 | PC1, PC2  |
|   20 | Finance        | 192.168.20.0/24 | 192.168.20.1 | PC3, PC4  |
|   30 | Sales          | 192.168.30.0/24 | 192.168.30.1 | PC7, PC8  |
|   40 | IT             | 192.168.40.0/24 | 192.168.40.1 | PC5, PC6  |
|   50 | Printer        | 192.168.50.0/24 | 192.168.50.1 | Printer0  |
|   60 | Guest          | 192.168.60.0/24 | 192.168.60.1 | PC9, PC10 |
|   99 | Management     | 192.168.99.0/24 | 192.168.99.1 | SW1, SW2  |
|  999 | Native Parking | No user subnet  | Not assigned | None      |

### Endpoint Addressing

| Device   | VLAN | IP Address    | Subnet Mask   | Gateway      |
| -------- | ---: | ------------- | ------------- | ------------ |
| PC1      |   10 | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| PC2      |   10 | 192.168.10.11 | 255.255.255.0 | 192.168.10.1 |
| PC3      |   20 | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |
| PC4      |   20 | 192.168.20.11 | 255.255.255.0 | 192.168.20.1 |
| PC5      |   40 | 192.168.40.10 | 255.255.255.0 | 192.168.40.1 |
| PC6      |   40 | 192.168.40.11 | 255.255.255.0 | 192.168.40.1 |
| PC7      |   30 | 192.168.30.10 | 255.255.255.0 | 192.168.30.1 |
| PC8      |   30 | 192.168.30.11 | 255.255.255.0 | 192.168.30.1 |
| PC9      |   60 | 192.168.60.10 | 255.255.255.0 | 192.168.60.1 |
| PC10     |   60 | 192.168.60.11 | 255.255.255.0 | 192.168.60.1 |
| Printer0 |   50 | 192.168.50.10 | 255.255.255.0 | 192.168.50.1 |

---

## Technology Stack

| Technology           | Role                                    |
| -------------------- | --------------------------------------- |
| Cisco IOS            | Router and switch configuration         |
| VLANs                | Layer 2 segmentation                    |
| IEEE 802.1Q          | VLAN tagging across trunk links         |
| Router Subinterfaces | Logical Layer 3 gateways for VLANs      |
| IPv4 Subnetting      | Structured addressing plan              |
| Trunking             | Carrying multiple VLANs across one link |
| Packet Tracer        | Lab simulation and verification         |

---

## Skills Demonstrated

### Technical Skills

1. Inter-VLAN routing design
2. Cisco router subinterface configuration
3. 802.1Q trunking
4. VLAN gateway assignment
5. Layer 2 and Layer 3 verification
6. Cisco IOS troubleshooting
7. Network documentation

### Professional Skills

1. Change planning
2. Risk identification
3. Technical documentation
4. Evidence-based verification
5. Troubleshooting case analysis
6. Interview-ready explanation of design decisions

---

## Project Continuity

This project builds directly on **Project 3: Enterprise VLAN Segmentation**.

Project 3 created the VLANs and access port assignments. Project 4 adds routing between those VLANs.

| Phase    | Project                                  | Status   |
| -------- | ---------------------------------------- | -------- |
| Previous | Project 3: Enterprise VLAN Segmentation  | Complete |
| Current  | Project 4: Enterprise Inter-VLAN Routing | Complete |
| Next     | Project 5: Enterprise STP Optimization   | Planned  |

---

## Port Assignment Summary

### SW1 Port Assignments

| Port  |  VLAN | Device        | IP Address    | Purpose             |
| ----- | ----: | ------------- | ------------- | ------------------- |
| F0/1  |    10 | PC1           | 192.168.10.10 | HR workstation      |
| F0/2  |    10 | PC2           | 192.168.10.11 | HR workstation      |
| F0/3  |    20 | PC3           | 192.168.20.10 | Finance workstation |
| F0/4  |    20 | PC4           | 192.168.20.11 | Finance workstation |
| F0/5  |    40 | PC5           | 192.168.40.10 | IT workstation      |
| F0/6  |    40 | PC6           | 192.168.40.11 | IT workstation      |
| Gi0/1 | Trunk | Router1 Gi0/0 | N/A           | Router uplink       |
| F0/24 | Trunk | SW2 F0/24     | N/A           | Inter-switch link   |

### SW2 Port Assignments

| Port  |  VLAN | Device    | IP Address    | Purpose           |
| ----- | ----: | --------- | ------------- | ----------------- |
| F0/1  |    30 | PC7       | 192.168.30.10 | Sales workstation |
| F0/2  |    30 | PC8       | 192.168.30.11 | Sales workstation |
| F0/3  |    60 | PC9       | 192.168.60.10 | Guest workstation |
| F0/4  |    60 | PC10      | 192.168.60.11 | Guest workstation |
| F0/23 |    50 | Printer0  | 192.168.50.10 | Network printer   |
| F0/24 | Trunk | SW1 F0/24 | N/A           | Inter-switch link |

---

## Implementation Summary

The implementation followed a structured order to reduce risk and simplify troubleshooting.

1. Verified existing VLAN configuration from Project 3.
2. Connected Router1 to SW1 using a trunk link.
3. Enabled the router physical interface.
4. Created router subinterfaces for VLANs 10, 20, 30, 40, 50, 60, and 99.
5. Assigned each subinterface an IP address.
6. Configured SW1 Gi0/1 as a router trunk.
7. Configured SW1 F0/24 and SW2 F0/24 as inter-switch trunks.
8. Configured endpoint IP addresses and default gateways.
9. Verified same-VLAN connectivity.
10. Verified cross-VLAN connectivity.
11. Captured evidence using Cisco verification commands.
12. Documented troubleshooting scenarios and lessons learned.

---

## Configuration Walkthrough

### Step 1: Router Base Configuration

```text
enable
configure terminal

hostname Router1
no ip domain-lookup

enable secret cisco
service password-encryption

line console 0
 password cisco
 login
 exit

line vty 0 4
 password cisco
 login
 exit

banner motd #Unauthorized Access Prohibited#
```

### Step 2: Enable the Physical Router Interface

```text
interface gigabitEthernet0/0
 description TRUNK_TO_SW1_Gi0_1
 no ip address
 no shutdown
```

The physical interface must be enabled before any subinterface can function.

### Step 3: Configure Router Subinterfaces

```text
interface gigabitEthernet0/0.10
 description HR_VLAN_10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
 no shutdown

interface gigabitEthernet0/0.20
 description FINANCE_VLAN_20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
 no shutdown

interface gigabitEthernet0/0.30
 description SALES_VLAN_30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0
 no shutdown

interface gigabitEthernet0/0.40
 description IT_VLAN_40
 encapsulation dot1Q 40
 ip address 192.168.40.1 255.255.255.0
 no shutdown

interface gigabitEthernet0/0.50
 description PRINTER_VLAN_50
 encapsulation dot1Q 50
 ip address 192.168.50.1 255.255.255.0
 no shutdown

interface gigabitEthernet0/0.60
 description GUEST_VLAN_60
 encapsulation dot1Q 60
 ip address 192.168.60.1 255.255.255.0
 no shutdown

interface gigabitEthernet0/0.99
 description MANAGEMENT_VLAN_99
 encapsulation dot1Q 99
 ip address 192.168.99.1 255.255.255.0
 no shutdown
```

### Step 4: Configure SW1 Trunk Ports

```text
enable
configure terminal

interface gigabitEthernet0/1
 description TRUNK_TO_ROUTER_Gi0_0
 switchport mode trunk
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,40,50,60,99,999
 no shutdown

interface fastEthernet0/24
 description TRUNK_TO_SW2_F0_24
 switchport mode trunk
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,40,50,60,99,999
 no shutdown

end
write memory
```

### Step 5: Configure SW2 Trunk Port

```text
enable
configure terminal

interface fastEthernet0/24
 description TRUNK_TO_SW1_F0_24
 switchport mode trunk
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,40,50,60,99,999
 no shutdown

end
write memory
```

---

## Verification Methodology

Verification was performed in layers.

| Layer         | Verification Area     | Method                               |
| ------------- | --------------------- | ------------------------------------ |
| Layer 1       | Physical connectivity | Confirm cables and interface status  |
| Layer 2       | VLAN database         | Use `show vlan brief`                |
| Layer 2       | Trunk operation       | Use `show interfaces trunk`          |
| Layer 3       | Router interfaces     | Use `show ip interface brief`        |
| Layer 3       | Routing table         | Use `show ip route`                  |
| Layer 3       | Connectivity          | Use `ping` and `traceroute`          |
| Documentation | Evidence capture      | Save screenshots and command outputs |

---

## Verification Commands

| Command                   | Purpose                                           | Expected Result                                   |
| ------------------------- | ------------------------------------------------- | ------------------------------------------------- |
| `show ip interface brief` | Verifies router interface and subinterface status | Subinterfaces show `up/up`                        |
| `show ip route`           | Verifies connected routes                         | All VLAN networks appear as connected routes      |
| `show interfaces trunk`   | Verifies trunk status                             | Trunks show correct native VLAN and allowed VLANs |
| `show vlan brief`         | Verifies VLANs and access ports                   | VLANs exist with correct port assignments         |
| `ping`                    | Tests connectivity                                | Successful replies                                |
| `traceroute`              | Shows traffic path                                | Router gateway appears as the first hop           |
| `show running-config`     | Reviews active configuration                      | Configuration matches the design                  |
| `write memory`            | Saves configuration                               | Running configuration is saved                    |

---

## Expected Router Verification Output

### show ip interface brief

```text
Interface              IP-Address      OK? Method Status  Protocol
GigabitEthernet0/0     unassigned      YES manual up      up
GigabitEthernet0/0.10  192.168.10.1    YES manual up      up
GigabitEthernet0/0.20  192.168.20.1    YES manual up      up
GigabitEthernet0/0.30  192.168.30.1    YES manual up      up
GigabitEthernet0/0.40  192.168.40.1    YES manual up      up
GigabitEthernet0/0.50  192.168.50.1    YES manual up      up
GigabitEthernet0/0.60  192.168.60.1    YES manual up      up
GigabitEthernet0/0.99  192.168.99.1    YES manual up      up
```

### show ip route

```text
C    192.168.10.0/24 is directly connected, GigabitEthernet0/0.10
C    192.168.20.0/24 is directly connected, GigabitEthernet0/0.20
C    192.168.30.0/24 is directly connected, GigabitEthernet0/0.30
C    192.168.40.0/24 is directly connected, GigabitEthernet0/0.40
C    192.168.50.0/24 is directly connected, GigabitEthernet0/0.50
C    192.168.60.0/24 is directly connected, GigabitEthernet0/0.60
C    192.168.99.0/24 is directly connected, GigabitEthernet0/0.99
```

---

## Implementation Evidence

| Evidence Item           | Command or Test           | What It Proves                                         |
| ----------------------- | ------------------------- | ------------------------------------------------------ |
| Router interface status | `show ip interface brief` | Subinterfaces are up and assigned correct IP addresses |
| Routing table           | `show ip route`           | Router has connected routes for all VLANs              |
| SW1 trunk status        | `show interfaces trunk`   | Router and inter-switch trunks are operational         |
| SW2 trunk status        | `show interfaces trunk`   | SW2 trunk to SW1 is operational                        |
| VLAN database           | `show vlan brief`         | VLANs exist and ports are assigned correctly           |
| Same-VLAN ping          | `ping`                    | Devices in the same VLAN can communicate               |
| Cross-VLAN ping         | `ping`                    | Devices in different VLANs can communicate             |
| Traceroute              | `traceroute`              | Traffic is routed through the correct gateway          |
| Running configuration   | `show running-config`     | Device configuration matches the design                |

---

## Screenshot Documentation

Add the screenshots for this project in a `/screenshots` folder and update the links below.

### Router Interface Status

```
<img width="293" height="91" alt="image" src="https://github.com/user-attachments/assets/5f6ed76f-0dfa-4794-be3d-092942099af1" />

```

Purpose: Confirms all router subinterfaces are up and assigned the correct gateway addresses.

Command used:

```text
show ip interface brief
```

### Routing Table

```text
screenshots/routing-table.png
```

Purpose: Confirms the router has connected routes for every VLAN subnet.

Command used:

```text
show ip route
```

### SW1 Trunk Status

```text
screenshots/sw1-trunk-status.png
```

Purpose: Confirms SW1 trunks are operational and carrying the correct VLANs.

Command used:

```text
show interfaces trunk
```

### SW2 Trunk Status

```text
screenshots/sw2-trunk-status.png
```

Purpose: Confirms SW2 trunk to SW1 is operational.

Command used:

```text
show interfaces trunk
```

### VLAN Database

```text
screenshots/vlan-database.png
```

Purpose: Confirms VLANs exist and access ports are assigned correctly.

Command used:

```text
show vlan brief
```

### Cross-VLAN Connectivity

```text
screenshots/cross-vlan-ping.png
```

Purpose: Confirms devices in different VLANs can communicate through Router1.

Command used:

```text
ping 192.168.20.10
```

### Traceroute

```text
screenshots/traceroute.png
```

Purpose: Confirms traffic crosses the router gateway before reaching the destination VLAN.

Command used:

```text
traceroute 192.168.20.10
```

---

## Troubleshooting Case Studies

### Case Study 1: Router Subinterface Down

| Item          | Details                                                    |
| ------------- | ---------------------------------------------------------- |
| Problem       | VLAN 10 devices could not reach their gateway              |
| Symptom       | Ping to 192.168.10.1 failed                                |
| Investigation | `show ip interface brief` showed the subinterface down     |
| Root Cause    | Physical interface Gi0/0 was administratively down         |
| Resolution    | Enabled the physical interface with `no shutdown`          |
| Verification  | Subinterface changed to `up/up` and gateway ping succeeded |

Lesson learned: A router subinterface depends on the physical interface. Always check the physical interface first.

### Case Study 2: VLAN Missing From Trunk Allowed List

| Item          | Details                                            |
| ------------- | -------------------------------------------------- |
| Problem       | Sales VLAN could not communicate with other VLANs  |
| Symptom       | VLAN 30 pings failed                               |
| Investigation | `show interfaces trunk` showed VLAN 30 was missing |
| Root Cause    | VLAN 30 was not included in the allowed VLAN list  |
| Resolution    | Added VLAN 30 to the trunk                         |
| Verification  | VLAN 30 traffic successfully routed                |

Resolution command:

```text
switchport trunk allowed vlan add 30
```

Lesson learned: If one VLAN fails while others work, check the trunk allowed VLAN list.

### Case Study 3: Incorrect Endpoint Gateway

| Item          | Details                                              |
| ------------- | ---------------------------------------------------- |
| Problem       | A Finance host could not communicate outside VLAN 20 |
| Symptom       | Ping to other VLANs failed                           |
| Investigation | Endpoint gateway was set to 192.168.20.254           |
| Root Cause    | Incorrect default gateway                            |
| Resolution    | Changed gateway to 192.168.20.1                      |
| Verification  | Gateway ping and cross-VLAN ping succeeded           |

Lesson learned: The endpoint gateway must match the router subinterface IP address for that VLAN.

### Case Study 4: Missing Subinterface Encapsulation

| Item          | Details                                     |
| ------------- | ------------------------------------------- |
| Problem       | IT VLAN devices could not communicate       |
| Symptom       | VLAN 40 route was missing                   |
| Investigation | Subinterface configuration was reviewed     |
| Root Cause    | `encapsulation dot1Q 40` was missing        |
| Resolution    | Added the missing encapsulation command     |
| Verification  | VLAN 40 route appeared in the routing table |

Resolution command:

```text
interface gigabitEthernet0/0.40
 encapsulation dot1Q 40
```

Lesson learned: Every subinterface must have the correct VLAN encapsulation.

### Case Study 5: Native VLAN Mismatch

| Item          | Details                                            |
| ------------- | -------------------------------------------------- |
| Problem       | Intermittent trunk communication                   |
| Symptom       | Native VLAN mismatch warning                       |
| Investigation | Native VLAN settings were compared                 |
| Root Cause    | One side used VLAN 1 while the other used VLAN 999 |
| Resolution    | Matched the native VLAN on both sides              |
| Verification  | Warning cleared and connectivity stabilized        |

Lesson learned: Native VLANs must match on both ends of a trunk.

### Case Study 6: Wrong VLAN on Access Port

| Item          | Details                                     |
| ------------- | ------------------------------------------- |
| Problem       | PC7 could not communicate with PC8          |
| Symptom       | Same-VLAN ping failed                       |
| Investigation | `show vlan brief` showed PC8 in VLAN 1      |
| Root Cause    | PC8 access port was not assigned to VLAN 30 |
| Resolution    | Assigned PC8 port to VLAN 30                |
| Verification  | PC7 successfully pinged PC8                 |

Resolution command:

```text
interface fastEthernet0/2
 switchport mode access
 switchport access vlan 30
```

Lesson learned: Always verify access port assignment before troubleshooting routing.

---

## Engineering Reflection

This project helped connect VLAN theory to real enterprise network design.

In Project 3, VLANs were used to separate departments into different broadcast domains. In Project 4, I learned how routing restores controlled communication between those separated networks.

The most important lesson was that VLANs alone do not allow communication between departments. A Layer 3 device must route traffic between subnets.

Router-on-a-Stick also showed how one physical router interface can support multiple logical networks through subinterfaces and 802.1Q tagging.

This project strengthened my understanding of:

1. Layer 2 segmentation
2. Layer 3 routing
3. Trunking
4. Default gateways
5. Subinterface configuration
6. Incremental troubleshooting
7. Enterprise change documentation

The next logical improvement is to add access control lists so that routing is allowed only where the business requires it.

---

## Interview Questions and Model Answers

### 1. What is Router-on-a-Stick?

Router-on-a-Stick is an inter-VLAN routing method where one physical router interface is divided into multiple logical subinterfaces. Each subinterface is mapped to a VLAN using 802.1Q encapsulation and acts as the default gateway for that VLAN.

### 2. Why use Router-on-a-Stick instead of a Layer 3 switch?

Router-on-a-Stick is cost-effective for small-to-medium networks. A Layer 3 switch is usually better for larger networks that require higher routing performance.

### 3. What is a router subinterface?

A subinterface is a logical interface created under a physical router interface. It allows one physical interface to support multiple VLANs.

### 4. What command maps a subinterface to a VLAN?

```text
encapsulation dot1Q 10
```

This command tells the router that the subinterface belongs to VLAN 10.

### 5. Why do endpoints need a default gateway?

Endpoints need a default gateway to communicate outside their local subnet. In this project, each VLAN uses the router subinterface IP address as its gateway.

### 6. How do you verify inter-VLAN routing?

Use:

```text
show ip route
show ip interface brief
ping
traceroute
```

These commands confirm interface status, connected routes, and end-to-end connectivity.

### 7. Why can devices in different VLANs not communicate automatically?

VLANs are separate broadcast domains. A Layer 3 device is required to route traffic between them.

### 8. What happens if a trunk does not allow a VLAN?

Traffic from that VLAN cannot cross the trunk. Devices in that VLAN may lose connectivity to the router or other switches.

### 9. What causes a native VLAN mismatch?

A native VLAN mismatch occurs when each side of a trunk uses a different native VLAN. This can cause warnings and unpredictable traffic forwarding.

### 10. What is the most important troubleshooting step for Router-on-a-Stick?

Start with the physical interface, then verify subinterfaces, encapsulation, trunk status, VLAN assignments, endpoint IP settings, and routing.

---

## Command Reference

| Command                          | Purpose                                       |
| -------------------------------- | --------------------------------------------- |
| `interface gigabitEthernet0/0.x` | Enters router subinterface configuration mode |
| `encapsulation dot1Q <vlan-id>`  | Maps a subinterface to a VLAN                 |
| `ip address <ip> <mask>`         | Assigns an IP address to an interface         |
| `no shutdown`                    | Enables an interface                          |
| `show ip interface brief`        | Displays router interface status              |
| `show ip route`                  | Displays routing table entries                |
| `show interfaces trunk`          | Displays switch trunk status                  |
| `show vlan brief`                | Displays VLANs and assigned ports             |
| `ping`                           | Tests connectivity                            |
| `traceroute`                     | Shows packet path                             |
| `show running-config`            | Displays active configuration                 |
| `write memory`                   | Saves running configuration                   |
| `switchport mode trunk`          | Configures a switch port as a trunk           |
| `switchport trunk allowed vlan`  | Limits VLANs allowed on a trunk               |
| `switchport trunk native vlan`   | Sets the native VLAN                          |
| `switchport mode access`         | Configures an access port                     |
| `switchport access vlan`         | Assigns an access port to a VLAN              |
| `spanning-tree portfast`         | Enables faster access port convergence        |

---

## Production Considerations

### Change Management

In a production network, this change should be submitted through a formal change management process.

The change plan should include:

1. Business justification
2. Risk assessment
3. Implementation steps
4. Verification steps
5. Rollback plan
6. Maintenance window approval

### Security

Production security improvements should include:

1. Use SSH instead of Telnet.
2. Disable unused switch ports.
3. Move unused ports to an unused VLAN.
4. Use a non-user native VLAN.
5. Restrict trunk allowed VLANs.
6. Apply ACLs to control inter-VLAN traffic.
7. Protect management access with strong authentication.

### Resilience

This design uses one router as the Layer 3 gateway. In production, that router becomes a single point of failure.

Possible improvements include:

1. Redundant routers
2. Layer 3 switching
3. First-hop redundancy protocols
4. Backup configuration files
5. Documented replacement procedures

### Monitoring

A production deployment should monitor:

1. Router interface status
2. Trunk status
3. Subinterface state changes
4. Interface utilization
5. Syslog messages
6. Routing changes
7. Connectivity to critical VLANs

---

## Future Enhancements

The next improvements to this network include:

1. Configure ACLs to control traffic between VLANs.
2. Add DHCP relay using `ip helper-address`.
3. Replace static endpoint addressing with centralized DHCP.
4. Add Layer 3 switching for improved performance.
5. Add redundant gateways.
6. Implement SSH for secure management.
7. Add SNMP and syslog monitoring.
8. Document baseline configuration backups.
9. Add dynamic routing in future WAN integration projects.

---

## Project Roadmap

| Project   | Focus                         | Status   |
| --------- | ----------------------------- | -------- |
| Project 1 | Basic Cisco Network Setup     | Complete |
| Project 2 | Static Routing                | Complete |
| Project 3 | Enterprise VLAN Segmentation  | Complete |
| Project 4 | Enterprise Inter-VLAN Routing | Complete |
| Project 5 | Enterprise STP Optimization   | Planned  |
| Project 6 | ACL-Based Traffic Control     | Planned  |
| Project 7 | DHCP Services and Relay       | Planned  |
| Project 8 | Dynamic Routing               | Planned  |

---

## Final Outcome

The Router-on-a-Stick implementation was completed successfully.

The router now provides gateway services for each VLAN, and the switch infrastructure carries VLAN traffic across properly configured trunk links.

The network now supports controlled communication between departmental VLANs while preserving logical segmentation.

### Final Validation

| Validation Area                 | Result     |
| ------------------------------- | ---------- |
| Router subinterfaces configured | Successful |
| VLAN gateways assigned          | Successful |
| SW1 router trunk configured     | Successful |
| SW1 to SW2 trunk configured     | Successful |
| VLANs allowed on trunks         | Successful |
| Same-VLAN connectivity tested   | Successful |
| Cross-VLAN connectivity tested  | Successful |
| Routing table verified          | Successful |
| Troubleshooting documented      | Successful |

---

## Lessons Learned

This project reinforced that enterprise networking is not just about entering commands. It requires understanding how Layer 2 segmentation and Layer 3 routing work together.

The most important lessons were:

1. VLANs separate traffic, but routing is required for communication between VLANs.
2. Router subinterfaces allow one physical router interface to support multiple VLANs.
3. 802.1Q tagging is required so the router can identify VLAN traffic.
4. The physical router interface must be enabled for subinterfaces to work.
5. Trunk links must carry all required VLANs.
6. Endpoint gateways must match the router subinterface address.
7. Incremental verification makes troubleshooting faster and more accurate.
8. Documentation is part of professional network engineering.

Project 4 demonstrates the ability to design, configure, verify, troubleshoot, and explain enterprise inter-VLAN routing using Cisco technologies.
