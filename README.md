# Smart Office Network

A Cisco Packet Tracer project that designs a small office network from scratch: four departments, each on its own VLAN, connected through two routers running OSPF.

### The scenario

Picture a small office with four groups of people: **Administration**, **Employees**, **IT**, and **Guests** connecting over Wi-Fi. On a single flat network, all of them share one broadcast domain and one address space. A guest's laptop sits in the same network as the admin team's computers, and any device can broadcast to every other device.

This project starts from that problem: how do you give each group its own space, and still let them communicate when they need to?

### The approach

**1. Give every department its own network.**
Each group gets a VLAN and its own `/24` subnet (`192.168.10.0` through `192.168.40.0`). Broadcast traffic now stays inside the department, and the address plan makes it obvious which network a device belongs to.

**2. Split the office across two switches.**
Administration and Employees connect to Switch 1. IT and Guests connect to Switch 2. Each switch carries two VLANs and three end devices per VLAN.

**3. Let the departments talk to each other.**
VLANs cannot communicate without a router, so each switch connects to a router over a single 802.1Q trunk. The router uses one subinterface per VLAN, and each subinterface acts as that VLAN's default gateway (router-on-a-stick).

**4. Connect the two halves.**
Router 1 serves VLANs 10 and 20, and Router 2 serves VLANs 30 and 40. A point-to-point `/30` link joins them, and OSPF lets each router learn the other's networks automatically instead of maintaining static routes.

**5. Check that it works.**
The last step is verification: confirm the VLANs and trunks on the switches, confirm the OSPF neighbor and learned routes on the routers, then ping across VLANs and across routers.

### The result

A four-department office network with separate broadcast domains, a gateway for each VLAN, and dynamic routing between the two router sides.

---

## Technical Summary

| Item | Detail |
| --- | --- |
| Tool | Cisco Packet Tracer |
| Devices | 2 routers, 2 switches, 12 end devices |
| Layer 2 | 4 VLANs, 802.1Q trunks |
| Layer 3 | Router-on-a-stick, OSPF |

### Topology

```text
   VLAN 10, 20                                   VLAN 30, 40
  ┌──────────┐    802.1Q     ┌──────────┐   192.168.50.0/30   ┌──────────┐    802.1Q     ┌──────────┐
  │ Switch 1 │───── trunk ───│ Router 1 │─────── OSPF ────────│ Router 2 │───── trunk ───│ Switch 2 │
  └──────────┘               └──────────┘                     └──────────┘               └──────────┘
```

### Addressing

| VLAN | Department | Switch | Subnet | Gateway | Devices |
| ---: | --- | --- | --- | --- | ---: |
| 10 | Administration | Switch 1 | `192.168.10.0/24` | `192.168.10.1` | 3 |
| 20 | Employees | Switch 1 | `192.168.20.0/24` | `192.168.20.1` | 3 |
| 30 | IT | Switch 2 | `192.168.30.0/24` | `192.168.30.1` | 3 |
| 40 | Guests / Wi-Fi | Switch 2 | `192.168.40.0/24` | `192.168.40.1` | 3 |

Router link `192.168.50.0/30`: Router 1 is `.1`, Router 2 is `.2`.

### Verification

Ping tests: VLAN 10 to VLAN 20 (same router), and VLAN 10 to VLAN 30 or 40 (across both routers and OSPF).

## Running the Project

Open the `.pkt` file in Cisco Packet Tracer and use the CLI tab on any router or switch to inspect the configuration.

## Skills Demonstrated

VLAN design, 802.1Q trunking, router-on-a-stick, IPv4 subnetting, OSPF, Cisco IOS CLI, network verification.
