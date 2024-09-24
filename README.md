### Packet Tracer Lab: IPv6 Configuration and Routing

#### 1. **Lab Overview**
This Packet Tracer lab focuses on setting up an IPv6 network with static routing and SLAAC (Stateless Address Autoconfiguration). The topology includes three routers (R1, R2, and R3), two switches, and two PCs. The serial connections between the routers use IPv6 link-local addresses only, while global unicast IPv6 addresses are assigned to GigabitEthernet interfaces. Your task is to configure IPv6 routing, enable SLAAC for the PCs, and set up static routes to ensure end-to-end connectivity between PC1 and PC2.

<img src= "https://github.com/ro-drick/IPv6-Configuration-Part-3/blob/main/ipv6-3.PNG">

---

#### 2. **Network Topology**
- **PC1**: Connected to **SW1** (Switch 1), which is connected to **R1** (Router 1).
- **PC2**: Connected to **SW2** (Switch 2), which is connected to **R3** (Router 3).
- **R1, R2, R3**: Three routers are interconnected via serial links, with R1 and R3 being the primary communication path, and R2 serving as a backup.
  
| Device | Interface | IPv6 Address |
|--------|-----------|--------------|
| R1     | G0/0      | `2001:db8:0:1::1/64` |
| R1     | G0/1      | `2001:db8:0:13::1/64` |
| R2     | S0/0/0    | Link-local (Serial) |
| R2     | S0/0/1    | Link-local (Serial) |
| R3     | G0/1      | `2001:db8:0:13::2/64` |
| R3     | G0/0      | `2001:db8:0:3::1/64` |
| PC1    | -         | To be autoconfigured via SLAAC |
| PC2    | -         | To be autoconfigured via SLAAC |

---

#### 3. **Configuration Steps**

##### 3.1 **Enable IPv6 Routing on Each Router**

1. Access each router in the topology.
2. Enable IPv6 routing using the following command:
    ```bash
    ipv6 unicast-routing
    ```
   This will allow the routers to route IPv6 packets between interfaces.

##### 3.2 **Configure SLAAC for PCs**

1. Ensure the PCs are set to use SLAAC (Stateless Address Autoconfiguration) for IPv6. To do this:
   - On each PC, go to the Desktop > IP Configuration.
   - Set the IPv6 configuration to **Autoconfigure**.

2. Verify the IPv6 addresses assigned by SLAAC:
   - **PC1** (Connected to SW1 via R1):
     - SLAAC will assign an IPv6 address in the range of `2001:db8:0:1::/64`.
   - **PC2** (Connected to SW2 via R3):
     - SLAAC will assign an IPv6 address in the range of `2001:db8:0:3::/64`.

   Record the IPv6 address configured on each PC.

##### 3.3 **Configure Static Routes**

To allow end-to-end communication between PC1 and PC2, static routes will be configured on the routers.

1. **Configure Static Routes on R1:**
   - Configure a static route on R1 to reach PC2's network via R3 (primary path):
     ```bash
     ipv6 route 2001:db8:0:3::/64 2001:db8:0:13::2
     ```
   - Configure a backup static route via R2:
     ```bash
     ipv6 route 2001:db8:0:3::/64 S0/0/0 2001:db8:0:13::2 150
     ```
     Here, `150` is the administrative distance to make this route a backup.

2. **Configure Static Routes on R3:**
   - Configure a static route on R3 to reach PC1's network via R1 (primary path):
     ```bash
     ipv6 route 2001:db8:0:1::/64 2001:db8:0:13::1
     ```
   - Configure a backup static route via R2:
     ```bash
     ipv6 route 2001:db8:0:1::/64 S0/0/1 2001:db8:0:13::1 150
     ```

3. **Configure Static Routes on R2 (Backup Router):**
   - R2 needs to route packets between R1 and R3, so configure the following routes:
     - To PC1’s network:
       ```bash
       ipv6 route 2001:db8:0:1::/64 S0/0/0
       ```
     - To PC2’s network:
       ```bash
       ipv6 route 2001:db8:0:3::/64 S0/0/1
       ```

##### 3.4 **Test Connectivity**
1. From **PC1**, ping **PC2** using the command:
   ```bash
   ping 2001:db8:0:3::[PC2's SLAAC address]
   ```
2. From **PC2**, ping **PC1** using the command:
   ```bash
   ping 2001:db8:0:1::[PC1's SLAAC address]
   ```

---

#### 4. **Verification and Troubleshooting**

- Ensure that IPv6 routing is enabled on all routers.
- Verify that each PC has been assigned an IPv6 address using SLAAC.
- Check the routing table of each router to confirm that the static routes have been added correctly:
  ```bash
  show ipv6 route
  ```
- If the ping tests fail, check for any configuration errors, especially with the static routes and IPv6 addressing.

---

#### 5. **Conclusion**
This lab covers configuring IPv6 routing using SLAAC for host autoconfiguration and static routes to ensure connectivity. By configuring a backup path through R2, redundancy is achieved in case the primary link fails.


## Acknowledgements


Special thanks to **Jeremy's IT Lab** for providing valuable resources and tutorials that greatly contributed to the completion of this exercise. His in-depth explanations and practical demonstrations have been instrumental in enhancing my understanding of Cisco networking concepts and the effective use of Packet Tracer.

For more information and additional resources, visit [Jeremy's IT Lab](https://jeremysitlab.com/) and check out his YouTube for the full course, [Jeremy's IT Lab Free CCNA 200-301 | Complete Course](https://www.youtube.com/playlist?list=PLxbwE86jKRgMpuZuLBivzlM8s2Dk5lXBQ)
