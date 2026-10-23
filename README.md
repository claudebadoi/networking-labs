<h1 align="center">Networking Labs</h1>

<p align="center"><strong>Hands-on network engineering — from first packets to routed infrastructure.</strong></p>

<p align="center">A practical portfolio of reproducible networking labs built with Linux, Kathara, Docker, and Wireshark.</p>

<p align="center">
  <img alt="One lab completed" src="https://img.shields.io/badge/Labs-01%20Completed-168653?style=flat-square" />
  <img alt="Kathara and Docker environment" src="https://img.shields.io/badge/Environment-Kathara%20%2B%20Docker-2563eb?style=flat-square" />
  <img alt="English documentation" src="https://img.shields.io/badge/Documentation-English-64748b?style=flat-square" />
</p>

---

## About the project

This repository documents hands-on exercises in **network configuration, packet analysis, routing, and troubleshooting**. Each lab is designed to be reproducible: the topology, startup configuration, test commands, and evidence are kept together.

The focus is on understanding *why* networks behave the way they do—not just making commands work.

## Laboratory index

| Lab | Topics | Evidence | Status |
| :-- | :-- | :-- | :-- |
| **[01 · Basic Network Connectivity](labs/01-basic-connectivity/README.md)** | IPv4, Ethernet, ARP, ICMP, network testing | Kathara config, Wireshark screenshots, PCAP | **Completed** |
| **02 · Inter-LAN Routing** | Default gateway, Linux forwarding, routing tables, troubleshooting | Planned lab | Planned |

## Featured lab · Basic Network Connectivity

Two Linux hosts share a virtual LAN (`192.168.10.0/24`). The lab demonstrates how they discover each other's MAC addresses using ARP and exchange ICMP Echo Request / Echo Reply packets.

**Verified outcome:** `4 packets transmitted, 4 received, 0% packet loss` · Average RTT: **0.888 ms**.

<p align="center">
  <img src="labs/01-basic-connectivity/screenshots/03-icmp-request.png" alt="Wireshark capture showing the ICMP Echo Request and IPv4 header" width="820" />
</p>

<p align="center"><em>Wireshark analysis of an ICMP Echo Request captured in LAB 01.</em></p>

**[Read the full LAB 01 report →](labs/01-basic-connectivity/README.md)** · [View packet capture](labs/01-basic-connectivity/shared/lab01-arp-icmp.pcap) · [Browse screenshots](labs/01-basic-connectivity/screenshots/)

## Tools and technologies

| Tool | How it is used |
| :-- | :-- |
| **Kathara + Docker** | Build isolated, repeatable network topologies |
| **Linux / iproute2** | Configure network interfaces, routes, and gateways |
| **tcpdump** | Capture traffic and save PCAP files |
| **Wireshark** | Inspect Ethernet, ARP, IPv4, and ICMP headers |
| **Git + GitHub** | Track changes and publish technical documentation |

## Repository structure

```text
networking-labs/
├── README.md
├── .gitignore
└── labs/
    └── 01-basic-connectivity/
        ├── README.md
        ├── lab.conf
        ├── pc1.startup
        ├── pc2.startup
        ├── screenshots/
        │   ├── 01-arp-request.png
        │   ├── 02-arp-reply.png
        │   ├── 03-icmp-request.png
        │   └── 04-icmp-reply.png
        └── shared/
            └── lab01-arp-icmp.pcap
```

## Run the first lab

**Prerequisites:** Docker and Kathara installed and working on your system.

Open a terminal in the repository directory and run:

```powershell
cd labs/01-basic-connectivity
kathara lstart --noterminals
kathara exec pc1 "ping -c 4 192.168.10.20"
kathara lclean
```

The `.startup` files assign the IPv4 addresses automatically. Detailed configuration steps, packet analysis, and troubleshooting notes are available in the [LAB 01 documentation](labs/01-basic-connectivity/README.md).

---

<sub>Documenting practical progress in networking, Linux administration, and infrastructure troubleshooting.</sub>
