## Install the latest version srsRAN from the github
**1. Install Dependencies and all the library to support the srsRAN** 

``sudo apt install cmake g++ libsctp-dev libsctp1 \
libboost-all-dev libfftw3-dev libmbedtls-dev \
libconfig++-dev libuhd-dev libpcsclite-dev``

![image](https://github.com/user-attachments/assets/a703ef96-3438-4a6f-9500-8a36c8909297)

 **2. Gitclone**

 ``git clone https://github.com/srsRAN/srsRAN_Project.git``

![image](https://github.com/user-attachments/assets/b314a7be-c86c-46f5-ad05-da31b7fc761c)


 **3. Download and build srsRAN Project (we try the vanilla one)**

``
mkdir build
cd build
cmake ../
make -j$(nproc)
make test -j$(nproc)
``

![image](https://github.com/user-attachments/assets/1dbfa33d-b4ef-4e71-a2ff-0162342c08c0)

![image](https://github.com/user-attachments/assets/0d06b015-4632-4925-8ad1-af76dc816a18)

![image](https://github.com/user-attachments/assets/ccf9ac1c-c38a-4372-86b5-797f2756a08c)

 **4. Install the srsRAN Project gNB**

 ``sudo make install``

![image](https://github.com/user-attachments/assets/fb41cac9-71e5-490e-be2d-51d68ac820f5)

![image](https://github.com/user-attachments/assets/a47f5eed-606e-464a-bac2-216361c151b8)


**5. Execute**

![image](https://github.com/user-attachments/assets/ace86e9f-0b60-4129-b30a-f6d6098a2ce2)





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
