# LAN-to-LAN Network Connectivity & Router Troubleshooting

**Cisco Packet Tracer | Networking Fundamentals | Cisco IOS | Interface Troubleshooting**

## Project Overview

This project documents the construction of two separate local area networks (LANs), representing **New York** and **Tokyo**, connected through simulated Internet infrastructure. I placed client PCs, servers, switches, routers, firewalls, and an external attacker endpoint in Cisco Packet Tracer.

During setup, multiple links remained down. I used `show ip interface brief` to identify **administratively disabled router interfaces**, then applied `no shutdown` on the required ports. The final topology shows active (green) link indicators.

> **Scope:** Green links show that the simulated interfaces/links are active. My original notes do **not** demonstrate configured IP addressing, working routes, firewall rules, or successful end-to-end ping tests. Those remain follow-up work.

## Lab Objectives

- Build two LAN segments and a simulated inter-site connection.
- Understand how switches, routers, and firewalls fit into the topology.
- Diagnose disabled Cisco IOS interfaces.
- Enable the required interfaces and observe their link states.
- Document troubleshooting decisions and outcomes.

## Lab Environment

| Device | Role |
| --- | --- |
| Cisco Packet Tracer | Network simulation environment |
| PC1 and PC2 | New York LAN endpoints |
| Server1 and Server2 | Tokyo LAN endpoints |
| Switch1 and Switch2 | Local access switching |
| Router1 and Router2 | Branch routing devices |
| Intermediate router ("The Internet") | Simulated inter-site transit |
| Firewall1 and Firewall2 | Security devices in the proposed path |
| Attacker endpoint | Simulated external host; no attack tests documented |

*The source notes do not record IP addresses, interface address assignments, routing protocols, or security policies.*

## Topology

```text
New York LAN                                        Tokyo LAN
PC1 --\                                               /-- Server1
      Switch1 -- Router1 -- Firewall1 -- Internet -- Router2 -- Firewall2 -- Switch2
PC2 --/                          |                    \-- Server2
                              Attacker
```

*This diagram is conceptual; consult the screenshot for the precise topology.*

![Final topology showing green link indicators](images/07-final-link-status.png)

## Implementation and Troubleshooting

### 1. Place the devices

I started by placing the required routers, switches, hosts, servers, and other devices in the Packet Tracer workspace, grouping them by branch.

![Initial device placement](images/01-device-placement.png)

### 2. Build the two LANs

I connected the New York PCs to Switch1 and the Tokyo servers to Switch2. The local Ethernet links turned green, indicating active links in the simulation. This alone does not establish IP-level reachability.

![Separate New York and Tokyo LANs](images/02-local-area-networks.png)

### 3. Add the inter-site devices

I added the branch routers, firewall appliances, intermediate Internet router, and external endpoint. Several links were red, so I investigated interface status.

Routers forward traffic between different IP networks once addressing and routes are configured. Firewalls enforce traffic rules **only when configured with appropriate policies**; merely placing them in the path is not proof of security.

![Initial full topology with red links](images/03-full-topology-initial-status.png)

### 4. Diagnose the router interfaces

I opened the Cisco IOS command-line interface and ran:

```cisco
Router> enable
Router# show ip interface brief
```

The output indicated that several interfaces were **administratively down**, meaning they were disabled through configuration.

![Router interface diagnostics](images/04-router-interface-diagnostics.png)

### 5. Enable the required branch interfaces

I entered configuration mode and enabled the relevant interfaces on both Router1 and Router2.

```cisco
Router> enable
Router# configure terminal
Router(config)# interface gigabitEthernet0/0
Router(config-if)# no shutdown
Router(config-if)# exit
Router(config)# interface gigabitEthernet0/1
Router(config-if)# no shutdown
Router(config-if)# exit
Router(config)# interface gigabitEthernet0/2
Router(config-if)# no shutdown
Router(config-if)# exit
Router(config)# end
Router# show ip interface brief
```

*The commands above are a cleaned-up example of the documented procedure. Enable only interfaces actually connected and intended for use. `no shutdown` does not by itself guarantee an `up/up` state.*

![Enabling branch-router interfaces](images/05-enabling-router-interfaces.png)

### 6. Isolate the remaining down links

The branch-router links improved, but the connections around the intermediate Internet router remained red. I identified that router as the next device requiring investigation.

![Partial connectivity restored](images/06-partial-connectivity-restored.png)

### 7. Enable the intermediate router interfaces

I repeated the interface checks and enablement process on the intermediate router. The final Packet Tracer screenshot showed all depicted link indicators green.

**Result:** The simulation's link status recovered. Successful New York-to-Tokyo packet delivery has **not** yet been demonstrated.

![Final link status](images/07-final-link-status.png)

## Troubleshooting Summary

| Issue | Diagnostic finding | Corrective action | Observed result |
| --- | --- | --- | --- |
| Red links on branch routers | Interfaces shown as administratively down | `no shutdown` on relevant router ports | Some links became active |
| Red links near Internet device | Intermediate router interfaces still required configuration | Enabled connected interfaces | Final topology showed green links |
| Inter-LAN IP connectivity | No packet-delivery test recorded | Configure addressing/routing and run pings | Not yet verified |

## Recommended Verification and Next Steps

1. Assign interface and host **IP addresses/subnet masks** and configure host default gateways.
2. Configure inter-network routing (static routes or a suitable routing protocol).
3. Configure firewall interfaces and traffic policies.
4. Test from New York hosts to Tokyo servers using `ping` and `tracert`.
5. Record results, including routing tables, successful/failed packets, and fixes.
6. Optionally conduct controlled, authorized security tests in the simulation.

### Useful verification commands

```cisco
Router# show ip interface brief
Router# show ip route
Router# show interfaces gigabitEthernet0/0
```

Packet Tracer PC command prompt:

```text
ipconfig
ping <remote-host-IP>
tracert <remote-host-IP>
```

*These are suggested future checks, not previously completed tests.*

## Skills Practiced

- Cisco Packet Tracer topology construction
- LAN architecture fundamentals
- Cisco IOS interface status checks
- Identifying `administratively down` interfaces
- Interface configuration using `no shutdown`
- Methodical network troubleshooting
- Distinguishing link status from end-to-end IP reachability

## Project Status

**Completed:** Device placement, LAN connections, branch and intermediate router interface enablement, and visual link-state verification.

**Still to verify:** Addressing, routing, firewall-policy behavior, and end-to-end communication between the two sites.

---

*Expanded and reorganized from my original Cisco Packet Tracer lab notes.*
