# CCNA-Day-08-EtherChannel-LACP-Lab
# CCNA Lab Day 8 – EtherChannel Fundamentals (LACP)

## Overview

This lab introduces EtherChannel, a technology that combines multiple physical links into a single logical connection. EtherChannel increases bandwidth, provides redundancy, and prevents Spanning Tree Protocol (STP) from blocking redundant links.

In this lab, LACP (Link Aggregation Control Protocol) is used to bundle multiple switch links into one logical Port-Channel.

---

## Objectives

After completing this lab, you will be able to:

- Understand EtherChannel concepts
- Understand LACP operation
- Configure EtherChannel using LACP
- Verify Port-Channel status
- Understand redundancy and load balancing
- Troubleshoot EtherChannel issues

---

## Network Topology

```text
              SW1
          Fa0/1====Fa0/1
          Fa0/2====Fa0/2
              SW2

        PC1          PC2
         |            |
       Fa0/10      Fa0/10
```

---

## Devices Used

- 2 Cisco Switches
- 2 PCs
- Copper Straight-Through Cables

---

## Physical Connections

| Device | Interface | Connected To |
|----------|----------|----------|
| SW1 | Fa0/1 | SW2 Fa0/1 |
| SW1 | Fa0/2 | SW2 Fa0/2 |
| PC1 | Fa0 | SW1 Fa0/10 |
| PC2 | Fa0 | SW2 Fa0/10 |

---

## IP Addressing

| Device | IP Address | Subnet Mask |
|----------|----------|----------|
| PC1 | 192.168.10.10 | 255.255.255.0 |
| PC2 | 192.168.10.20 | 255.255.255.0 |

No default gateway is required because both PCs are in the same VLAN.

---

## Step 1: Create VLAN 10

Configure on both switches:

```bash
enable
configure terminal

vlan 10
name USERS
```

---

## Step 2: Configure Access Ports

### SW1

```bash
interface fa0/10
switchport mode access
switchport access vlan 10
```

### SW2

```bash
interface fa0/10
switchport mode access
switchport access vlan 10
```

---

## Step 3: Configure LACP EtherChannel

### SW1

```bash
interface range fa0/1 - 2
channel-group 1 mode active

exit

interface port-channel 1
switchport mode trunk
```

### SW2

```bash
interface range fa0/1 - 2
channel-group 1 mode active

exit

interface port-channel 1
switchport mode trunk
```

---

## Understanding LACP Modes

### Active Mode

Actively sends LACP packets to establish an EtherChannel.

### Passive Mode

Waits for LACP packets from another device.

### LACP Combinations

| Switch 1 | Switch 2 | Result |
|-----------|-----------|---------|
| Active | Active | Success |
| Active | Passive | Success |
| Passive | Passive | Fail |

---

## Verification Commands

### Verify EtherChannel Status

```bash
show etherchannel summary
```

Expected Output:

```text
Group  Port-channel  Protocol

1      Po1(SU)       LACP
```

Meaning:

- S = Layer 2 EtherChannel
- U = Port-Channel is operational

---

### Verify Port-Channel Interface

```bash
show interfaces port-channel 1
```

---

### Verify Trunk Status

```bash
show interfaces trunk
```

---

## Connectivity Testing

From PC1:

```bash
ping 192.168.10.20
```

Expected Result:

Success

---

## How EtherChannel Works

Without EtherChannel:

```text
SW1 ----- SW2
SW1 ----- SW2

STP blocks one link
```

Result:

- One link forwards traffic
- One link remains unused

With EtherChannel:

```text
SW1 == Port-Channel1 == SW2
```

Result:

- Both links are bundled
- Increased bandwidth
- Link redundancy
- STP sees only one logical connection

---

## Link Failure Test

Disconnect one cable:

```text
SW1 Fa0/1 ↔ SW2 Fa0/1
```

Result:

- Port-Channel remains operational
- Traffic automatically uses remaining link
- No connectivity loss

This demonstrates EtherChannel redundancy.

---

## Static EtherChannel Example

### SW1

```bash
interface range fa0/1 - 2
channel-group 1 mode on
```

### SW2

```bash
interface range fa0/1 - 2
channel-group 1 mode on
```

Static EtherChannel does not use negotiation protocols.

---

## PAgP Overview

PAgP is a Cisco proprietary EtherChannel protocol.

### PAgP Modes

| Mode | Function |
|--------|--------|
| desirable | Actively negotiates |
| auto | Waits for negotiation |

### PAgP Combinations

| Switch 1 | Switch 2 | Result |
|-----------|-----------|---------|
| desirable | desirable | Success |
| desirable | auto | Success |
| auto | auto | Fail |

---

## Troubleshooting Scenarios

### Scenario 1: Different EtherChannel Modes

SW1:

```bash
channel-group 1 mode active
```

SW2:

```bash
channel-group 1 mode on
```

Result:

- EtherChannel fails to form

---

### Scenario 2: Different Port Modes

SW1:

```bash
switchport mode trunk
```

SW2:

```bash
switchport mode access
```

Result:

- EtherChannel fails

---

### Scenario 3: Different Interface Speeds

SW1:

```text
100 Mbps
```

SW2:

```text
1 Gbps
```

Result:

- EtherChannel fails

---

## Mini Challenge

Expand the EtherChannel to three physical links.

### Topology

```text
          SW1
      Fa0/1====Fa0/1
      Fa0/2====Fa0/2
      Fa0/3====Fa0/3
          SW2
```

### Tasks

1. Create VLAN 10
2. Configure LACP EtherChannel
3. Bundle three interfaces
4. Verify Port-Channel status
5. Test connectivity
6. Disconnect one cable and verify operation

---

## Skills Learned

- EtherChannel Fundamentals
- LACP Configuration
- PAgP Concepts
- Static EtherChannel
- Port-Channel Verification
- Link Redundancy
- Load Balancing Concepts
- EtherChannel Troubleshooting

---

## Conclusion

This lab introduced EtherChannel and demonstrated how multiple physical links can be combined into a single logical connection. EtherChannel improves bandwidth utilization, increases redundancy, and simplifies network design by presenting multiple links as one connection to STP.

---

## Next Lab

### Day 9 – DHCPv4 Configuration

Topics:

- DHCP Server Configuration
- DHCP Pools
- Excluded Addresses
- Default Gateway Distribution
- DNS Distribution
- DHCP Verification
- DHCP Troubleshooting

---

## Author

Muhammad Kausar

CCNA Enterprise Networking Lab Series
