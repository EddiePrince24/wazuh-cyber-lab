# wazuh-cyber-lab
Built an enterprise-grade Security Operations Center (SOC) telemetry pipeline to monitor endpoint activity, simulate attacks, and analyse Endpoint Detection and Response (EDR) data. The lab utilizes an open-source SIEM (Wazuh) to ingest kernel-level events from an isolated Windows workstation.

## Infrastructure Implementation Details

### 1. Host Optimization & WSL2 Architecture
To accommodate a hard host constraint of 16GB RAM while ensuring system stability, the lab was engineered for maximum resource efficiency:
* **WSL2 Resource Cap:** Capped via configuration files to a strict maximum of 7GB RAM and 4 vCPUs.
* **Headless Deployment:** Disabled the Linux Graphical User Interface (GUI) entirely to eliminate desktop overhead and maximize memory availability for the SIEM database.

### 2. Containerized Services (Docker)
* **Runtime:** Provisioned Docker Engine (v29.5.3) natively inside the WSL2 subsystem.
* **SIEM Core:** Deployed the full Wazuh stack using optimized multi-container microservices.

### 3. Wazuh SIEM Engine (v4.14.4)
**Performance Tuning:** JVM heap sizes for the indexing engine were locked at a balanced 1GB Minimum / 1GB Maximum. Equalizing these limits eliminates memory-allocation performance hiccups.

### 4. Windows 11 Target Endpoint (VirtualBox)
Provisioned a decoupled, isolated Windows 11 target profile inside Oracle VirtualBox with strict resource allocations to prevent host choking:
* **Memory:** 4GB RAM
* **Processing:** 4 vCPUs (Optimized to bypass severe multi-core installation hardware checks)
* **Graphics:** 128MB Video Memory

### 5. Telemetry Sensors & Shippers
* **Wazuh Agent:** Installed locally on the target endpoint to establish a secure cryptographic telemetry tunnel back to the SIEM manager over the virtual network bridge.
* **Microsoft Sysmon:** Deployed at the system kernel level to track advanced telemetry (process execution, command arguments, and network connection states).
* **Pipeline Integration:** Manually refactored the endpoint `ossec.conf` file to inject Sysmon XML blocks, routing structured Windows Event Channel data straight to the centralized dashboard.
