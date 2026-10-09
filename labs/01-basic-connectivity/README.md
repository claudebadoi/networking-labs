\# LAB 01 — Basic Network Connectivity



\*\*Category:\*\* Computer Networking  

\*\*Difficulty:\*\* Beginner  

\*\*Environment:\*\* Kathara, Docker, Linux, Windows PowerShell  

\*\*Tools:\*\* Kathara, tcpdump, Wireshark  

\*\*Protocols:\*\* Ethernet, ARP, IPv4, ICMP



\---



\## 1. Overview



This laboratory demonstrates basic network communication between two Linux hosts connected to the same virtual Local Area Network (LAN).



The network was built using \*\*Kathara\*\*, a container-based network emulation tool, and \*\*Docker\*\*.



The objective is to understand IP addressing, local network connectivity, MAC address resolution, ICMP communication, and packet analysis using tcpdump and Wireshark.



The laboratory also introduces automated network configuration using Kathara startup scripts.



\## 2. Learning Objectives



By completing this laboratory, the following networking concepts were explored:



\- Creating virtual network topologies using Kathara.

\- Understanding Linux network interfaces.

\- Configuring static IPv4 addresses.

\- Understanding IP subnets and subnet masks.

\- Testing connectivity using ICMP Echo Request and Echo Reply.

\- Understanding ARP and MAC address resolution.

\- Capturing network traffic using tcpdump.

\- Analyzing Ethernet, ARP, IPv4, and ICMP packets using Wireshark.

\- Automating network configuration using startup scripts.



\## 3. Network Topology



The network consists of two Linux hosts connected to the same virtual Ethernet collision domain.



```text

&#x20;             LAN A

&#x20;       192.168.10.0/24



&#x20;  +-------------+       +-------------+

&#x20;  |     PC1     |       |     PC2     |

&#x20;  |             |       |             |

&#x20;  | eth0        |-------| eth0        |

&#x20;  |             |       |             |

&#x20;  | 192.168.10.10       | 192.168.10.20

&#x20;  +-------------+       +-------------+

```



\### IP Addressing Table



| Device | Interface | IPv4 Address | Subnet Mask |

|---|---|---|---|

| PC1 | eth0 | 192.168.10.10 | 255.255.255.0 |

| PC2 | eth0 | 192.168.10.20 | 255.255.255.0 |



Both hosts belong to the `192.168.10.0/24` subnet.



Since they share the same IP subnet and Ethernet segment, communication can occur without a router.



\## 4. Project Structure



```text

01-basic-connectivity/

│

├── lab.conf

├── pc1.startup

├── pc2.startup

├── README.md

│

└── shared/

&#x20;   └── lab01-arp-icmp.pcap

```



\*\*File descriptions:\*\*



\- `lab.conf` — Defines the virtual network topology.

\- `pc1.startup` — Configures PC1 automatically.

\- `pc2.startup` — Configures PC2 automatically.

\- `README.md` — Laboratory documentation.

\- `shared/lab01-arp-icmp.pcap` — Captured ARP and ICMP traffic for analysis.



\## 5. Network Configuration



\### 5.1 Kathara Topology



The `lab.conf` file defines the connections between the two hosts.



```ini

pc1\[0]=A

pc2\[0]=A

```



Both hosts are connected to collision domain `A`.



Interface index `\[0]` corresponds to the Linux interface `eth0`.



\### 5.2 PC1 Configuration



File: `pc1.startup`



```bash

ip addr add 192.168.10.10/24 dev eth0

```



\### 5.3 PC2 Configuration



File: `pc2.startup`



```bash

ip addr add 192.168.10.20/24 dev eth0

```



These startup scripts allow Kathara to configure the hosts automatically when the laboratory is started.



This improves reproducibility and eliminates the need to assign IP addresses manually after recreating the containers.



\## 6. Running the Laboratory



\### Step 1 — Validate the topology



From the laboratory directory:



```powershell

kathara lstart --print

```



Expected validation message:



```text

lab.conf file is correct.

```



\### Step 2 — Start the laboratory



```powershell

kathara lstart --noterminals

```



\### Step 3 — Verify IP addresses



Check PC1:



```powershell

kathara exec pc1 "ip -br addr"

```



Check PC2:



```powershell

kathara exec pc2 "ip -br addr"

```



Expected IPv4 configuration:



```text

PC1 eth0: 192.168.10.10/24

PC2 eth0: 192.168.10.20/24

```



\### Step 4 — Test connectivity



```powershell

kathara exec pc1 "ping -c 4 192.168.10.20"

```



The command sends four ICMP Echo Requests from PC1 to PC2.



\### Step 5 — Stop the laboratory



```powershell

kathara lclean

```



This removes the laboratory containers without deleting the local project configuration files.



\## 7. Connectivity Test Results



The connectivity test between PC1 and PC2 completed successfully.



\*\*Recorded output:\*\*



```text

4 packets transmitted, 4 received

0% packet loss



rtt min/avg/max/mdev =

0.784/0.888/1.078/0.112 ms

```



\### Results



| Metric | Measured Value |

|---|---|

| Packets transmitted | 4 |

| Packets received | 4 |

| Packet loss | 0% |

| Minimum RTT | 0.784 ms |

| Average RTT | 0.888 ms |

| Maximum RTT | 1.078 ms |



\*\*Result:\*\* Successful ICMP communication between PC1 and PC2.



These measurements were obtained in a local virtual networking environment.



\## 8. ARP Analysis



\### 8.1 What is ARP?



Address Resolution Protocol (ARP) is used to resolve an IPv4 address to a link-layer hardware address, typically an Ethernet MAC address.



Before PC1 can send Ethernet frames to PC2, it must determine the MAC address associated with `192.168.10.20`, unless that mapping is already cached.



\### 8.2 ARP Request



The captured ARP Request contained:



```text

Who has 192.168.10.20?

Tell 192.168.10.10

```



Observed Ethernet information:



| Field | Value |

|---|---|

| Source MAC | ca:1f:68:1f:c5:6e |

| Destination MAC | ff:ff:ff:ff:ff:ff |

| EtherType | 0x0806 |

| ARP Opcode | 1 — Request |

| Sender IP | 192.168.10.10 |

| Target IP | 192.168.10.20 |

| Target MAC | 00:00:00:00:00:00 |



The destination MAC is the Ethernet broadcast address because PC1 does not yet know PC2's MAC address.



\### 8.3 ARP Reply



PC2 responds with its MAC address.



```text

192.168.10.20 is at f2:9c:a6:6e:c2:13

```



| Field | Value |

|---|---|

| Source MAC | f2:9c:a6:6e:c2:13 |

| Destination MAC | ca:1f:68:1f:c5:6e |

| ARP Opcode | 2 — Reply |

| Sender IP | 192.168.10.20 |

| Target IP | 192.168.10.10 |



Unlike the initial broadcast ARP Request, this ARP Reply is sent directly to PC1 using unicast communication.



The MAC addresses shown above were observed in this laboratory session. They may differ when the containers are recreated.



\## 9. ICMP Packet Analysis



Internet Control Message Protocol (ICMP) is commonly used for network diagnostics, including the `ping` utility.



\### 9.1 ICMP Echo Request



Observed in Wireshark, packet 9:



| Field | Value |

|---|---|

| Source IP | 192.168.10.10 |

| Destination IP | 192.168.10.20 |

| IPv4 Protocol | ICMP (1) |

| TTL | 64 |

| ICMP Type | 8 — Echo Request |

| ICMP Code | 0 |



PC1 sends an Echo Request to determine whether PC2 is reachable.



\### 9.2 ICMP Echo Reply



Observed in Wireshark, packet 10:



| Field | Value |

|---|---|

| Source IP | 192.168.10.20 |

| Destination IP | 192.168.10.10 |

| IPv4 Protocol | ICMP (1) |

| TTL | 64 |

| ICMP Type | 0 — Echo Reply |

| ICMP Code | 0 |



PC2 responds to the Echo Request, confirming communication between the two hosts.



\### 9.3 Packet Encapsulation



The captured ICMP Echo Request demonstrates packet encapsulation:



```text

Ethernet II Frame

│

├── Ethernet Header (14 bytes)

│

└── IPv4 Packet (84 bytes)

&#x20;   │

&#x20;   ├── IPv4 Header (20 bytes)

&#x20;   │

&#x20;   └── ICMP Message (64 bytes)

&#x20;       │

&#x20;       ├── ICMP Header (8 bytes)

&#x20;       └── Payload (56 bytes)

```



\*\*Total Ethernet frame size: 98 bytes\*\*



The IPv4 packet is encapsulated inside an Ethernet frame, while the ICMP message is carried inside the IPv4 packet.



\## 10. Packet Capture with tcpdump



Traffic was captured on PC2 using:



```bash

tcpdump -i eth0 -nn -e -w /shared/lab01-arp-icmp.pcap 'arp or icmp'

```



\### Command Explanation



| Argument | Description |

|---|---|

| `-i eth0` | Capture traffic on interface eth0 |

| `-nn` | Disable host and port name resolution |

| `-e` | Include link-layer information in text output |

| `-w` | Write captured packets to a PCAP file |

| `arp or icmp` | Capture filter for ARP and ICMP traffic |



Before generating traffic, the ARP neighbor cache was cleared on PC1:



```powershell

kathara exec pc1 "ip neigh flush dev eth0"

```



Then an ICMP Echo Request was generated:



```powershell

kathara exec pc1 "ping -c 1 192.168.10.20"

```



The capture was stopped using `Ctrl + C`.



\### Capture Results



\- 12 packets were present in the Wireshark capture.

\- Both ARP and ICMP traffic were observed.

\- tcpdump reported 0 packets dropped by the kernel.



The capture is stored in:



`shared/lab01-arp-icmp.pcap`



\## 11. Wireshark Analysis



The PCAP file was opened in Wireshark to inspect the captured traffic.



The following display filters can be used:



\*\*ARP traffic:\*\*



```text

arp

```



\*\*ICMP traffic:\*\*



```text

icmp

```



\*\*ARP or ICMP:\*\*



```text

arp || icmp

```

\### ARP Request

!\[ARP Request](screenshots/01-arp-request.png)



\### ARP Reply

!\[ARP Reply](screenshots/02-arp-reply.png)



\### ICMP Echo Request

!\[ICMP Echo Request](screenshots/03-icmp-request.png)



\### ICMP Echo Reply

!\[ICMP Echo Reply](screenshots/04-icmp-reply.png)



\### Key Observations



1\. An ARP Request uses the Ethernet broadcast destination address.

2\. An ARP Reply can be delivered directly to the requesting host.

3\. ICMP Echo Request uses Type 8.

4\. ICMP Echo Reply uses Type 0.

5\. Source and destination IP addresses are reversed in the Echo Reply.

6\. Neighbor cache maintenance can generate additional ARP traffic after successful ICMP communication.



\## 12. Troubleshooting



Several issues were encountered and resolved during the laboratory.



\### Invalid Collision Domain Name



\*\*Problem:\*\*



```text

Collision domain `LAN A` contains

non-alphanumeric characters.

```



\*\*Solution:\*\*



The collision domain was renamed to `A`, a valid identifier accepted by Kathara.



\### Incorrect Interface Name



\*\*Problem:\*\*



```text

ip addr add 192.168.10.10/24 dev eth 0

```



\*\*Solution:\*\*



The interface name was corrected from `eth 0` to `eth0`.



\### Incomplete PowerShell Command



\*\*Problem:\*\*



An unclosed quotation mark caused PowerShell to display the continuation prompt `>>`.



\*\*Solution:\*\*



The incomplete command was cancelled using `Ctrl + C` and re-entered with the correct quotation marks.



\### Missing ARP Packets



\*\*Problem:\*\*



An initial capture did not show the expected ARP discovery before ICMP communication.



\*\*Explanation:\*\*



The required IP-to-MAC mapping may already have existed in the neighbor cache.



\*\*Solution:\*\*



The cache was cleared before starting a new communication test.



\## 13. Lessons Learned



This laboratory provided practical experience in basic networking and packet analysis.



The main lessons were:



\- Devices in the same subnet and Ethernet segment can communicate without a router.

\- IP addresses and MAC addresses serve different purposes.

\- ARP resolves IPv4 addresses to local link-layer addresses.

\- Ethernet broadcast and unicast communication behave differently.

\- ICMP can be used to test network reachability.

\- Wireshark enables detailed inspection of network protocol headers.

\- Linux networking tools are useful for both configuration and troubleshooting.

\- Automated startup scripts improve laboratory reproducibility.



\## 14. Future Improvements



Potential extensions of this laboratory include:



\- Adding a third host.

\- Introducing multiple subnets.

\- Configuring static routing with a Linux router.

\- Studying TTL changes across routed networks.

\- Investigating TCP and UDP traffic.

\- Creating automated connectivity tests.

\- Introducing DNS and DHCP services.



\---



\*\*Project Type:\*\* Hands-on Networking Laboratory  

\*\*Status:\*\* Connectivity and packet analysis completed; startup configuration prepared for reproducibility testing.  

\*\*Purpose:\*\* Practical networking education and technical portfolio development.



