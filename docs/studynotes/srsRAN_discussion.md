
## Minimum requirements to run srsRAN
1. CPU: At least a 4-core processor (e.g., Intel i5 or Ryzen 5). 
2. RAM: Minimum 8 GB
3. Storage: Reserve at least 20 GB of free space. 
4. Operating System: Ubuntu 22.04 LTS (64-bit).
5. Linux Kernel: Kernel version 5.x or newer is preferred. 
6. Network Interface: At least one Gigabit Ethernet interface or loopback support. 
7. Required Dependencies: GCC 9 or above, CMake 3.10+, Boost libraries, UHD (for USRP), and optionally ZeroMQ (for inter-process communication) or DPDK (for high-performance networking).

## System Architecture
Key components and considerations for creating a system architecture for srsRAN on a Virtual Machine (VM):

### **1. Core Components**
- **Centralized Unit (CU):**
  - Disaggregated into Control Plane (CU-CP) and User Plane (CU-UP).
  - Handles functionalities like RRC (Radio Resource Control) and PDCP (Packet Data Convergence Protocol).

- **Distributed Unit (DU):**
  - Manages lower-layer functions like MAC (Medium Access Control) and RLC (Radio Link Control).

- **Radio Unit (RU):**
  - Provides uplink and downlink functionality with radio interfaces for data transmission and reception.

- **Evolved Packet Core (EPC):**
  - Handles core network functionalities like authentication, bearer establishment, and mobility management.

### **2. Additional Components**
- **PHY Layer:**
  - Implements physical layer functionalities such as modulation, coding, and RF communication.

- **Test/Simulation Components:**
  - Includes mock or simulated user equipment (UE).
  - Tools like `trx_srsran` for RF simulation.

- **Configuration Management:**
  - `srs_configuration.h` and related files provide APIs and structures for setting up Sounding Reference Signals (SRS), DU configurations, and more.

### **3. VM-Specific Setup**
- **Resource Allocation:**
  - Allocate sufficient CPU cores, memory, and networking resources to the VM.
  - Use high-priority scheduling for real-time threads where necessary (e.g., SCHED_FIFO).

- **Networking:**
  - Configure TCP/UDP ports for communication between components (e.g., CU-DU split communication).

- **Dependencies:**
  - Ensure required libraries like ZeroMQ (ZMQ) are installed and configured.
  - Use the appropriate cmake flags for enabling features like ZMQ.

### **4. Deployment Options**
- **Monolithic gNB:**
  - Combines CU and DU into a single logical node.

- **Split CU and DU:**
  - Deploys CU and DU as separate nodes for scalability.
