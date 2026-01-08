# NVIDIA InfiniBand Professional Certification Guide

> **Your roadmap to mastering high-speed server-interconnect technology.**

## 📖 Course Overview

**InfiniBand** is the interconnect protocol of choice for High-Performance Computing (HPC), AI, and hyperscale cloud infrastructures. Its **ultra-low latency**, **high speed**, and **scalability** make it essential for modern data centers.

This guide is designed for anyone who **installs, configures, manages, troubleshoots, or monitors** InfiniBand fabrics. Whether you are maximizing your product experience or preparing for the **NVIDIA InfiniBand Professional Certification**, this resource provides the necessary knowledge and skills.

---

## 🏗️ Module 1: Introduction to InfiniBand

InfiniBand (IB) is an industry-standard specification that defines an input/output architecture used to interconnect servers, communications infrastructure equipment, storage, and embedded systems.

### Use Cases
*   **HPC (High-Performance Computing)**: Simulation, weather modeling, scientific research. Requires massive bandwidth and low latency.
*   **AI/ML**: Large Language Model (LLM) training, distributed inference. Relies on efficient All-Reduce operations.
*   **Cloud & Hyperscale**: Specialized instances requiring guaranteed throughput and minimal jitter.
*   **Storage**: NVMe-over-Fabrics (NVMe-oF) leverages IB for fast remote storage access.

### Key Features
*   **High Throughput**: Multi-gigabit speeds per lane (NDR 400Gb/s, XDR 800Gb/s).
*   **Ultra-Low Latency**: Microsecond-level delays crucial for tightly coupled tasks.
*   **CPU Offload**: Application bypass and RDMA free up server CPUs.
*   **Reliability**: Hardware-based flow control (Credit-Based) prevents buffer overflows.
*   **Scalability**: A single subnet can scale to tens of thousands of nodes.

---

## 🏛️ Module 2: InfiniBand Architecture

Understanding the layered architecture is critical for design and troubleshooting.

### The InfiniBand Operations Layers
Similar to the OSI model, InfiniBand defines specific layers:

1.  **Physical Layer**:
    *   **Role**: Defines mechanical/electrical specs for cables (copper DAC, active optical), connectors (QSFP, OSFP), and signaling rates.
    *   **Function**: Signal encoding/decoding, link training.
2.  **Link Layer**:
    *   **Role**: Packet addressing (Local ID - LID), error detection (CRCs), flow control (Credit-Based), and switching.
    *   **Function**: Ensures packet delivery between two devices in the same subnet.
3.  **Network Layer**:
    *   **Role**: Routing packets between different subnets using Global Routing Headers (GRH).
    *   **Function**: Handles Global IDs (GUIDs) and inter-subnet routing (rare in pure HPC clusters).
4.  **Transport Layer**:
    *   **Role**: End-to-End communication services. Segmentation and reassembly.
    *   **Function**: Manages connections, reliability (ACKs/NACKs), and RDMA operations.

### Core Components
*   **HCA (Host Channel Adapter)**: The NIC in the server (e.g., ConnectX-7).
*   **Switch**: Routes packets based on LIDs. Cut-through switching for minimal latency.
*   **Subnet**: A set of nodes managed by a single Subnet Manager (SM).
*   **RDMA**: Remote Direct Memory Access. Zero-copy transfer directly from app memory to app memory.

---

## 🌐 Module 3: Topologies & Routing

Choosing the right physical layout and routing algorithm is vital for performance.

### Main InfiniBand Topologies
| Topology | Description | Considerations |
| :--- | :--- | :--- |
| **Fat-Tree** | Tree structure where bandwidth increases towards the root. | **Pros**: Non-blocking, full bisection bandwidth, predictable. <br>**Cons**: Expensive (more cabling/switches). Best for general purpose HPC. |
| **Torus / Mesh** | Nodes connected to neighbors in a ring/grid. | **Pros**: Low cable cost, good for neighbor-traffic. <br>**Cons**: Higher latency (hops), potential congestion. Used in specific scientific codes. |
| **Dragonfly+** | Hierarchical topology (groups of fully connected routers). | **Pros**: Highly scalable, low diameter (fewer hops), cost-effective cables. <br>**Cons**: Complex routing requirement (Needs Adaptive Routing). |

### Routing Engines
The Subnet Manager calculates paths. Choosing the engine depends on the topology.
*   **MinHop**: Finds shortest path. Simple, but not deadlock-free on complex topologies.
*   **Up/Down**: Deadlock-free for general graphs (prevents routing "down" then "up"). Good default for non-Fat-Trees.
*   **FatTree**: Specialized for Fat-Tree topologies. Balances traffic evenly across the root switches. **Recommended for Fat-Trees.**
*   **LASH/DOR**: Specialized for Torus/Mesh topologies to avoid deadlocks.

---

## 🎮 Module 4: Fabric Management

An InfiniBand network must be actively managed.

### Management Concepts
*   **Subnet Manager (SM)**: The entity that initializes the fabric.
    *   **Discovery**: Finds all nodes.
    *   **LID Assignment**: Assigns a unique 16-bit address to every port.
    *   **Routing**: Calculates tables (LFT) for every switch.
    *   **Sweeping**: Periodically checks for topology changes.
*   **Subnet Administrator (SA)**: Database of path records (services queries from nodes).

### Fabric Initialization Process
1.  **Sweep**: SM probes the network relative to itself.
2.  **LID Assignment**: SM assigns base LIDs.
3.  **Path Calculation**: SM runs the chosen Routing Engine (e.g., FatTree).
4.  **Configuration**: SM writes LFTs (Linear Forwarding Tables) to all switches.
5.  **Steady State**: Fabric is "Active".

---

## 🛠️ Module 5: Bring-up, Monitoring & Utilities

Practical skills for daily operations.

### Basic Management Operations
*   **Start/Stop SM**: `systemctl start opensm`
*   **Check SM Status**: `sminfo` (shows priority, state, master SM GUID).
*   **Change Priority**: Edit `/etc/rdma/opensm.conf` (higher priority becomes Master).

### Common Utilities & Usage

| Category | Command | Usage |
| :--- | :--- | :--- |
| **Status** | `ibstat` | View HCA ports, state (Active/Down), physical state, and rate. |
| **Status** | `ibv_devinfo` | Detailed HCA info (FW version, GUID). |
| **Topology** | `ibnetdiscover` | Scans fabric and outputs connectivity map. |
| **Topology** | `ibswitches` / `ibhosts` | Simple lists of fabric nodes. |
| **Link Info** | `iblinkinfo` | Shows status of all links in the fabric (speed, width, errors). |
| **Diagnostics** | `ibdiagnet` | **Systematic health check**. Checks counters, routing, cabling rules. |
| **Perf** | `ib_write_bw` | Bandwidth benchmark between two nodes. |
| **Perf** | `ib_write_lat` | Latency benchmark. |

### Diagnostic Process
1.  **Physical Link Check**: Run `iblinkinfo -e` to see error counters on links.
2.  **Fabric Health**: Run `ibdiagnet`. Check `ibdiagnet.log` for "Symbol Errors" or "Link Down".
3.  **Topology Check**: Run `ibnetdiscover` to verify all expected nodes are present.
4.  **Route Verification**: Use `ibtracert <SLID> <DLID>` to see the path a packet takes.

---

## 📚 curated References

*   **[InfiniBand Trade Association (IBTA)](https://www.infinibandta.org/)**: Specifications.
*   **[Mellanox OFED User Manual](https://docs.nvidia.com/networking/category/mlnxofed)**: The definitive guide for driver stack and tools.
*   **[OpenSM GitHub](https://github.com/linux-rdma/opensm)**: Source code for the Subnet Manager.
