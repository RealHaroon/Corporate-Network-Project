
#  🚀 Corporate Network Project 
*Multi-Segment Routing using Open vSwitch & GNS3 Advanced Virtual Network Simulation for Enterprise-Grade Routing & Connectivity*
----

## 📘 **Overview**

This project demonstrates a complete **enterprise-level virtual network infrastructure**, built entirely inside **GNS3** using:

* **Open vSwitch (OVS)** appliances
* **Alpine Linux** & VPCS end hosts
* **Static Routing** for multi-subnet communication
* **SSH-Based ACL Automation** (Python application)
* **End-to-end packet flow testing (ICMP, traceroute, forwarding)**

It simulates a **real corporate routed environment**, built as a **4-router chain** with 5 routed subnets, two endpoint hosts, and full cross-network connectivity.

---

# 🧭 **4-Node OVS Chain Topology**

```
                    ┌─────────┐
                    │  VPC1   │
                    │10.0.1.2 │
                    └────┬────┘
                         │
                ┌────────┴────────┐
                │     OVS1        │
                │10.0.1.1 / eth0  │
                │10.0.2.1 / eth1  │
                └────────┬────────┘
                         │
                ┌────────┴────────┐
                │     OVS2        │
                │10.0.2.2 / eth0  │
                │10.0.3.1 / eth1  │
                └────────┬────────┘
                         │
                ┌────────┴────────┐
                │     OVS3        │
                │10.0.3.2 / eth0  │
                │10.0.4.1 / eth1  │
                └────────┬────────┘
                         │
                ┌────────┴────────┐
                │     OVS4        │
                │10.0.4.2 / eth0  │
                │10.0.5.1 / eth1  │
                └────────┬────────┘
                         │
                    ┌────┴─────┐
                    │  VPC2     │
                    │10.0.5.2   │
                    └───────────┘
```
### Topology Image
![OVS-Topology](OVS-Topology.jpg "Topology")


✔ 5 subnets
✔ Clean routing path
✔ End-to-end traversal across 4 routers

---

# ✨ **Features**

### 🔧 **Network Routing & Switching**

* Multi-hop OVS routing
* 5 routed IPv4 subnets
* Static routes (Linux `ip route`)
* Realistic enterprise-style segmentation

### 🧩 **Infrastructure Simulation**

* GNS3 VM on VMware
* Multiple OVS appliances
* Alpine Linux lightweight hosts
* SSH-enabled OVS nodes

### 🛡 **Security & ACL Automation (Python App)**

* Blackhole routes
* ICMP control
* Interface-level forwarding rules
* RP-Filter
* SSH-based command injection
* Batch rule execution

### 📊 **Testing & Verification**

* End-to-end ping
* Traceroute hop visualization
* Routing table checks
* Packet forwarding validation

---

# 📁 **Project Structure**

```
Corporate-Network-Project/
│
├── Documentation.pdf
├── code_for_acl_controlling_from_host_machine.py
├── images/
│   └── topology.png
│
└── README.md
```

---

# 🏗 **Installation Guide**

## 🖥 Step 1 — Install Ubuntu (Host Machine)

```
sudo apt update && sudo apt upgrade -y
```

---

## 🧩 Step 2 — Install GNS3

```bash
sudo add-apt-repository ppa:gns3/ppa
sudo apt update
sudo apt install gns3-gui gns3-server -y
```

✔ Allow non-root usage
✔ Allow packet capture

---

## 🗄 Step 3 — Install VMware Workstation Pro

```bash
chmod +x VMwarePro.bundle
sudo ./VMwarePro.bundle
```

---

## 🖧 Step 4 — Import & Configure GNS3 VM

* Download GNS3 VM
* Import into VMware
* Power it on
* Note IP address

---

## 🔌 Step 5 — Install Open vSwitch Appliance

```
Browse Appliances → Switches → Open vSwitch → Install on GNS3 VM
```

---

# 🐧 **Step 6 — Alpine Linux Setup (End Device Configuration)**

*(Fully integrated as requested)*

Alpine acts as a real Linux host capable of routing, SSH, and advanced packet testing.

---

## 📥 Install Alpine on GNS3

```
File → Import Appliance → alpine-linux.gns3a
```

Choose:
✔ “Install on GNS3 VM (recommended)”

---

## 🧩 Connect Alpine to OVS

* Drag Alpine into workspace
* Connect **eth0** to desired OVS
* Start device
* Alpine Linux is a great replacement for VPCs in your topologies

---

## ⚙ Configure Network Interface

### Assign IP

```bash
ip addr add 10.0.1.2/24 dev eth0
```

### Bring Interface Up

```bash
ip link set eth0 up
```

### Set Default Gateway

```bash
ip route add default via 10.0.1.1
```

---

## 🔐 (Optional) Enable SSH on Alpine

```bash
apk update
apk add openssh
ssh-keygen -A
rc-service sshd start
rc-update add sshd default
passwd
```

---

## 🧪 Test Alpine Connectivity

```bash
ping 10.0.1.1
ping 10.0.5.2
traceroute 10.0.5.2
```

---

# 🔧 **OVS Device Configuration**

## 🟦 OVS1

```bash
ip addr add 10.0.1.1/24 dev eth0
ip addr add 10.0.2.1/24 dev eth1
ip route add 10.0.3.0/24 via 10.0.2.2
ip route add 10.0.4.0/24 via 10.0.2.2
ip route add 10.0.5.0/24 via 10.0.2.2
```

## 🟩 OVS2

```bash
ip addr add 10.0.2.2/24 dev eth0
ip addr add 10.0.3.1/24 dev eth1
ip route add 10.0.1.0/24 via 10.0.2.1
ip route add 10.0.4.0/24 via 10.0.3.2
ip route add 10.0.5.0/24 via 10.0.3.2
```

## 🟨 OVS3

```bash
ip addr add 10.0.3.2/24 dev eth0
ip addr add 10.0.4.1/24 dev eth1
ip route add 10.0.1.0/24 via 10.0.3.1
ip route add 10.0.2.0/24 via 10.0.3.1
ip route add 10.0.5.0/24 via 10.0.4.2
```

## 🟥 OVS4

```bash
ip addr add 10.0.4.2/24 dev eth0
ip addr add 10.0.5.1/24 dev eth1
ip route add 10.0.1.0/24 via 10.0.4.1
ip route add 10.0.2.0/24 via 10.0.4.1
ip route add 10.0.3.0/24 via 10.0.4.1
```

---

# 💻 **End Host Configuration (VPC)**

### VPC1

```
ip 10.0.1.2 255.255.255.0 10.0.1.1
```

### VPC2

```
ip 10.0.5.2 255.255.255.0 10.0.5.1
```

---

# 🧪 **Testing & Verification**

### ✔ Basic Pings

```bash
ping 10.0.1.1
ping 10.0.3.1
ping 10.0.5.2
```

### ✔ End-to-End Ping

```
ping 10.0.5.2
```

### ✔ Traceroute

```
trace 10.0.5.2
```

Expected path:
OVS1 → OVS2 → OVS3 → OVS4 → VPC2

---

# 🔥 **Optional Module: Python ACL Manager**

Your project also includes an SSH-based ACL automation tool:

### Capabilities:

* Add/remove blackhole routes
* Block/unblock ICMP
* Enable/Disable forwarding
* Batch execute ACL templates
* Manage 4 OVS routers from host

### Run the script:

```bash
python3 code_for_acl_controlling_from_host_machine.py
```

---

# 🩺 **Troubleshooting**

### ❌ No ping?

```
ip route show
cat /proc/sys/net/ipv4/ip_forward
```

### ❌ Wrong gateway?

Double-check each subnet mask & route entry.

### ❌ SSH failing on OVS/Alpine?

```
rc-service sshd restart
passwd
```

---

# 👨‍💻 **Author**

**M. Haroon Abbas**
GitHub: [RealHaroon](https://github.com/RealHaroon)

---

----- 
## 📄 License This project is licensed under the MIT License 
- see the [LICENSE](https://www.google.com/search?q=LICENSE) file for details.
MIT License

Copyright (c) 2025 HAROON ABBAS

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction...

----

# ⭐ **Support this Project**

If this helped you:
→ ⭐ Star the repo
→ 📤 Share with classmates
→ 📚 Add to your CV and portfolio

---


