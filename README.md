

-----

# Corporate Network Simulation Project

**Project Type:** 6th Semester Project
**Authors:**

  * Raimal Raja (2K23/BLCS/49)
  * M. Haroon Abbas (2K23/BLCS/38)

## 📖 Overview

This project documents the complete setup and implementation of a corporate network simulation. It covers the environment setup on a Linux host, the installation of network simulation tools (GNS3, VMware), and the deployment of a specific network topology using Open vSwitch (OVS) and Alpine Linux/VPCS to implement static routing.

## 🛠️ Environment Setup & Prerequisites

### 1\. Host Operating System

  * **OS:** Ubuntu 24.04 LTS.
  * **Installation:** Interactive installation via bootable USB (Rufus) with Secure Boot disabled.
  * **Post-Install:** Update system using `sudo apt update` and `sudo apt upgrade`.

### 2\. Virtualization & Simulation Tools

  * **GNS3 GUI:** Version 2.2.54 (Ubuntu-based distribution).
      * Installed via PPA: `sudo add-apt-repository ppa:gns3/ppa`.
  * **GNS3 VM:** Version 2.2.54.
  * **Hypervisor:** VMware Workstation Pro 25H2.
      * Used to run the GNS3 VM for resource-intensive devices.
  * **Terminal Emulator:** Terminator (Recommended over default).

### 3\. Appliances

  * **Open vSwitch (OVS):** Docker-based multilayer virtual switch.
  * **Alpine Linux:** Lightweight CLI-based operating system.
  * **Cisco Routers:** Optional (requires ISO images).

## 🌐 Network Topology

The project implements a **Bus Topology** (Series connection) consisting of 4 Open vSwitches and 2 End Devices (VPCS or Alpine Linux).

### IP Addressing Schema

| Device | Interface | IP Address | Subnet |
| :--- | :--- | :--- | :--- |
| **VPC1** | eth0 | 10.0.1.2/24 | 10.0.1.0/24 |
| **OVS1** | eth0 | 10.0.1.1/24 | 10.0.1.0/24 |
| **OVS1** | eth1 | 10.0.2.1/24 | 10.0.2.0/24 |
| **OVS2** | eth0 | 10.0.2.2/24 | 10.0.2.0/24 |
| **OVS2** | eth1 | 10.0.3.1/24 | 10.0.3.0/24 |
| **OVS3** | eth0 | 10.0.3.2/24 | 10.0.3.0/24 |
| **OVS3** | eth1 | 10.0.4.1/24 | 10.0.4.0/24 |
| **OVS4** | eth0 | 10.0.4.2/24 | 10.0.4.0/24 |
| **OVS4** | eth1 | 10.0.5.1/24 | 10.0.5.0/24 |
| **VPC2** | eth0 | 10.0.5.2/24 | 10.0.5.0/24 |

## ⚙️ Configuration & Implementation

### 1\. Clean Configuration (All OVS Devices)

Before configuring, stop networking services and remove existing bridges to ensure a clean state.

```bash
rc-service networking stop 2>/dev/null
for br in $(ovs-vsctl list-br); do
  ovs-vsctl del-br $br
done
ip addr flush dev eth0
ip addr flush dev eth1
```

### 2\. Enable IP Forwarding

Required on all switches to allow routing.

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
sysctl -w net.ipv4.ip_forward=1
```

### 3\. OVS Static Routing Configuration (Example: OVS1)

Configure IP addresses, bring interfaces up, and add static routes to next hops.

```bash
# Configure IP addresses
ip addr add 10.0.1.1/24 dev eth0
ip addr add 10.0.2.1/24 dev eth1

# Bring interfaces up
ip link set eth0 up
ip link set eth1 up

# Add static routes (Example for OVS1 pointing to OVS2)
ip route add 10.0.3.0/24 via 10.0.2.2
ip route add 10.0.4.0/24 via 10.0.2.2
ip route add 10.0.5.0/24 via 10.0.2.2
```

### 4\. End Device Configuration

**For VPCS:**

```bash
ip 10.0.5.2/24 10.0.5.1
save
```

**For Alpine Linux:**

```bash
ip addr add 10.0.1.2/24 dev eth0
ip route add default via 10.0.1.1 dev eth0
```

## 🧪 Testing & Verification

### Connectivity Checks

Perform ping tests from VPC1/VPC2 to all hops in the network to verify static routing.

```bash
ping 10.0.1.1 -c 4  # OVS1
ping 10.0.2.2 -c 4  # OVS2
ping 10.0.3.2 -c 4  # OVS3
ping 10.0.4.2 -c 4  # OVS4
ping 10.0.5.2 -c 4  # VPC2
```

### Routing Verification

On any OVS device, run the following to check tables:

```bash
ip route show
ip addr show
cat /proc/sys/net/ipv4/ip_forward # Should return 1
```

## 📂 Resources

  * **Cisco/Appliance Images:** [GitHub Repository](https://github.com/Raimal-Raja/Cisco_Appliances_Router_ISO_Images).
  * **GNS3 Installation Guide:** [Official Documentation](https://docs.gns3.com/docs/getting-started/installation/linux/).

-----

**Would you like me to generate the Python script mentioned in the documentation for the ACL Manager as well?**
