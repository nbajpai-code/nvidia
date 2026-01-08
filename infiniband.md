# NVIDIA InfiniBand Professional Certification Guide

> **Your roadmap to mastering high-speed server-interconnect technology.**

## 📖 Course Overview

**InfiniBand** is the interconnect protocol of choice for High-Performance Computing (HPC), AI, and hyperscale cloud infrastructures. Its **ultra-low latency**, **high speed**, and **scalability** make it essential for modern data centers.

This guide is designed for anyone who **installs, configures, manages, troubleshoots, or monitors** InfiniBand fabrics. Whether you are maximizing your product experience or preparing for the **NVIDIA InfiniBand Professional Certification**, this resource provides the necessary knowledge and skills.

---

## 🏗️ Module 1: Introduction to InfiniBand

InfiniBand (IB) is an industry-standard specification that defines an input/output architecture used to interconnect servers, communications infrastructure equipment, storage, and embedded systems.

### Key Advantages
*   **High Throughput**: Multi-gigabit speeds (NDR 400Gb/s and beyond).
*   **Ultra-Low Latency**: Microsecond-level delays crucial for AI/HPC.
*   **Scalability**: Supports thousands of nodes in a single subnet.
*   **CPU Offload**: Frees up server CPU cycles for applications via RDMA.

### Use Cases
*   **HPC**: Simulation, weather modeling, scientific research.
*   **AI/ML**: Large Language Model training, distributed inference.
*   **Cloud**: Hyperscale infrastructure requiring massive bandwidth.

---

## 🏛️ Module 2: InfiniBand Architecture

Understanding the building blocks of an InfiniBand fabric is critical for design and troubleshooting.

### Core Components
1.  **HCA (Host Channel Adapter)**: The network interface card (NIC) resident in the server (e.g., NVIDIA ConnectX series). Connects the compute node to the fabric.
2.  **TCA (Target Channel Adapter)**: Connects storage or other I/O devices to the fabric.
3.  **Switch**: Routes packets based on Local Identifiers (LIDs). Provides cut-through switching for minimal latency.
4.  **Cable/Transceiver**: Copper (DAC) for short reach, Optical for long reach. Essential for signal integrity at high speeds.

### Key Concepts
*   **RDMA (Remote Direct Memory Access)**: Allows data transfer directly from the memory of one computer to another without involving either one's operating system. Zero-copy networking.
*   **Subnet**: A set of IB nodes managed by a single Subnet Manager.
*   **GUID (Global Unique Identifier)**: 64-bit address burned into the hardware (like a MAC address).
*   **LID (Local Identifier)**: 16-bit address assigned by the Subnet Manager used for local routing.

---

## 🎮 Module 3: InfiniBand Fabric Management

An InfiniBand network is *managed*, meaning a central entity controls the routing and configuration.

### Subnet Manager (SM)
The **Subnet Manager** is the "brain" of the fabric. It must be running for traffic to flow.
*   **Responsibilities**:
    *   Discovering the topology.
    *   Assigning LIDs to every port.
    *   Calculating and installing routing tables (LFT/MFT) on switches.
    *   Handling sweep and changes (nodes joining/leaving).
*   **OpenSM**: The open-source implementation of the Subnet Manager.

### UFM (Unified Fabric Manager)
NVIDIA's advanced management platform for monitoring, managing, and optimizing InfiniBand networks.
*    **Features**:
    *   Automated provisioning and discovery.
    *   Real-time telemetry and congestion monitoring.
    *   Fabric health diagnostics.
    *   "Cyber-AI" for anomaly detection.

---

## 🛠️ Module 4: Fabric Bring-up & Monitoring

Practical skills for getting a fabric running and keeping it healthy.

### Bring-up Process
1.  **Installation**: Install HCAs and cabling.
2.  **Driver Setup**: Install **OFED (OpenFabrics Enterprise Distribution)** or **MLNX_OFED** stack.
    *   *Command*: `/etc/init.d/openibd start`
3.  **Start Subnet Manager**: Ensure OpenSM is running on a switch or a server node.
    *   *Command*: `/etc/init.d/opensm start`
4.  **Verification**: Check link status.
    *   *Command*: `ibstat` (Should show State: Active, PhysState: LinkUp).

### Monitoring & Troubleshooting Tools
Detailed command-line tools are essential for diagnostics.

| Command | Purpose |
| :--- | :--- |
| **`ibstat`** | Shows HCA status, firmware version, LIDs, and port state. |
| **`ibnetdiscover`** | Scans the fabric and lists all nodes and connections (Topology view). |
| **`ibswitches`** | Lists all switches in the fabric. |
| **`ibhosts`** | Lists all host nodes in the fabric. |
| **`ibdiagnet`** | Comprehensive diagnostic tool; checks for errors, cable issues, and routing credit loops. |
| **`ib_write_bw`** | Performance test: Measures RDMA Write **bandwidth**. |
| **`ib_write_lat`** | Performance test: Measures RDMA Write **latency**. |
| **`perfquery`** | Queries Performance Management (PortCounter) errors/counters. |

---

## 📚 curated References & Resources

### Official Documentation & Specs
*   **[InfiniBand Trade Association (IBTA)](https://www.infinibandta.org/specification-faq/)**: The official specs and architectural roadmap.
*   **[NVIDIA Networking Docs (Mellanox)](https://docs.nvidia.com/networking/)**: Detailed user manuals for ConnectX, Quantum Switches, and UFM.
*   **[OpenFabrics Alliance (OFA)](https://www.openfabrics.org/)**: Home of the open-source OFED stack.

### GitHub Repositories (Code & Tools)
*   **[linux-rdma/opensm](https://github.com/linux-rdma/opensm)**: The official source for OpenSM. Essential for understanding how the fabric manager works under the hood.
*   **[jumanjihouse/docker-opensm](https://github.com/jumanjihouse/docker-opensm)**: Run OpenSM in a lightweight Docker container. Great for lab environments.
*   **[linux-rdma/ibsim](https://github.com/linux-rdma/ibsim)**: InfiniBand Fabric Simulator. Allows you to simulate large fabrics for testing routing algorithms without physical hardware.
*   **[meier/opensm-smt](https://github.com/meier/opensm-smt)**: Subnet Monitoring Tools – extensions for better fabric visibility.

### Community & Utility Tools
*   **[ib-traffic-monitor](https://github.com/alanturing-ru/ib-traffic-monitor)**: TUI (Terminal UI) tool for monitoring IB traffic in real-time.
*   **[Cluster-Shell](https://github.com/clush-project/clustershell)**: While not IB-specific, essential for running diagnostics (`ibstat`, `ibcheck`) across hundreds of nodes simultaneously.
