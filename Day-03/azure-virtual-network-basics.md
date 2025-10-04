# 🌐 Azure Networking

## 🧩 Virtual Network (VNet)

An **Azure Virtual Network (VNet)** is the fundamental building block for private networking in Azure. It enables Azure resources like Virtual Machines (VMs), App Services, and Databases to securely communicate with each other, the internet, and on-premises networks.

### Key Features:
- **Network Isolation:**  
  Each VNet is logically isolated within Azure, ensuring that traffic between VNets and other tenants remains private and controlled.

- **Subnet Segmentation:**  
  VNets can be divided into multiple subnets, allowing you to organize workloads (e.g., web, app, and database tiers) and apply distinct security or routing rules per subnet.

- **Address Space (CIDR-based):**  
  VNets are defined with an **IP address space** using CIDR notation (e.g., `10.0.0.0/16`). This determines the available IP range and how subnets can be structured within it.

- **Connectivity Options:**  
  VNets can connect securely to other VNets (VNet peering), on-premises environments (via VPN Gateway or ExpressRoute), and external networks, forming hybrid infrastructures.

---

## 🧮 Subnets and CIDR

### 🔹 Subnets
A **Subnet** is a logical partition within a Virtual Network that isolates and organizes resources. Subnets help structure workloads, control traffic flow, and apply security at a granular level.

**Key benefits:**
- Enables tiered architecture (e.g., front-end, middle-tier, back-end).
- Allows application of different **Network Security Groups (NSGs)** and **Route Tables (UDRs)** to control traffic.
- Facilitates network segmentation for compliance and performance optimization.

Example:
VNet: 10.0.0.0/16

├─ Subnet A (Frontend): 10.0.1.0/24

├─ Subnet B (Backend): 10.0.2.0/24

└─ Subnet C (DB): 10.0.3.0/24


### 🔹 CIDR (Classless Inter-Domain Routing)
**CIDR notation** defines the IP address range and the number of available addresses in a network.  
Example:  
`10.0.0.0/24` → 256 IPs (254 usable)

CIDR helps manage IP allocation efficiently and avoids exhaustion by allowing flexible subnet sizes rather than fixed class-based ranges.

---

## 🚦 Routes and Route Tables

### 🔸 Routes
A **Route** determines how network traffic is directed from a source to its destination. Routes specify the **destination prefix** (IP range) and the **next hop** (e.g., Internet, Virtual Appliance, Virtual Network Gateway).

**Common Next Hop Types:**
- **Internet:** Sends traffic to the public internet.  
- **Virtual Network Gateway:** Routes traffic to on-premises through VPN or ExpressRoute.  
- **Virtual Appliance:** Directs traffic through a firewall or NVA (Network Virtual Appliance).  
- **None:** Drops traffic (used for blackholing).

### 🔸 Route Tables (User Defined Routes – UDRs)
**Route Tables** are collections of custom routes that override Azure’s default system routes.  
By associating a Route Table with a subnet, you can dictate precise traffic paths, ensuring compliance, monitoring, or security inspection.

**Use Cases:**
- Force traffic through a firewall or NVA for inspection.
- Control routing between subnets for isolation.
- Avoid asymmetric routing in complex architectures.

---

## 🔐 Network Security Groups (NSGs)

A **Network Security Group (NSG)** acts as a virtual firewall for controlling inbound and outbound traffic to Azure resources at both **subnet** and **NIC** (Network Interface) levels.

### Key Components:
- **Security Rules:**  
  NSG rules define whether traffic is **allowed** or **denied** based on:
  - Source and destination IPs or CIDR blocks
  - Port numbers
  - Protocols (TCP/UDP)
  - Direction (Inbound/Outbound)
  - Priority (lower number = higher priority)

- **Default Rules:**  
  NSGs include predefined rules that allow basic communication within VNets and Azure services. These can be overridden by user-defined rules.

- **Associations:**  
  NSGs can be linked to:
  - **Subnets:** To apply rules to all resources within.
  - **NICs:** To control traffic for individual VMs.

**Example Scenario:**  
You can apply an NSG to the frontend subnet allowing only HTTP/HTTPS (80/443) from the internet, while the backend subnet only allows traffic from the frontend subnet on port 1433 (SQL).

---

## 🧱 Application Security Groups (ASGs)

**Application Security Groups (ASGs)** simplify network security rule management by grouping virtual machines based on their application role rather than IP addresses.

### Benefits:
- **Dynamic Grouping:**  
  You can assign VMs to ASGs dynamically. When new VMs are added to an ASG, they automatically inherit the group’s network rules.

- **Simplified Rule Management:**  
  Instead of managing NSG rules per IP or subnet, you can define rules that reference ASGs, such as:
Allow: ASG-Web → ASG-App on port 8080



- **Scalability:**  
Perfect for environments where VMs frequently scale up or down, such as auto-scaling web tiers.

**Example Use Case:**  
- `ASG-Web` for all frontend VMs  
- `ASG-App` for backend API VMs  
→ Define a rule: allow inbound traffic from `ASG-Web` to `ASG-App` on port 443.

---

## 🧠 Summary

Azure Networking is built on **Virtual Networks**, which form the backbone for resource connectivity. **Subnets**, **Route Tables**, **NSGs**, and **ASGs** together provide a layered approach to security, traffic control, and manageability — enabling architects to design secure, scalable, and compliant network topologies for cloud workloads.