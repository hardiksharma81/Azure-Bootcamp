# ☁️ Azure Interview Q&A (Days 1–4)

---


### 💡 What are Regions and Availability Zones in Azure?
**Answer:**  
A *region* is a set of datacenters deployed within a specific geographic area, while *availability zones* are physically separate datacenters within a region.  
They provide fault tolerance — if one zone goes down, your application remains available in another.

**Scenario Example:**  
A fintech app deployed in `East US 2` uses 3 availability zones — one for the **frontend**, one for **API**, and one for **database** — ensuring `zero downtime` even during zone maintenance.

---

### 💡 Explain the difference between IaaS, PaaS, and SaaS with Azure examples.
**Answer:**
- **IaaS:** Infrastructure as a Service → VM, VNet, NSG  
- **PaaS:** Platform as a Service → App Service, Azure SQL, AKS  
- **SaaS:** Software as a Service → Microsoft 365, Dynamics CRM  

**Scenario Example:**  
Your DevOps team uses **Azure VMs (IaaS)** for Jenkins agents, **App Service (PaaS)** for the web app, and **Office 365 (SaaS)** for collaboration.

---

### 💡 Why is understanding regions and availability zones important when deploying mission-critical apps?
**Answer:**  
Because choosing the wrong region can increase latency or breach compliance. Multi-zone deployment ensures **high availability** and **geo-redundancy**.

**Scenario Example:**  
A banking client in India must host data in `Central India` for RBI compliance and replicate in `South India` for disaster recovery.

---

### 💡 What is the purpose of a Resource Group in Azure?
**Answer:**  
It acts as a logical container for managing lifecycle, access control, and cost of Azure resources.

**Scenario Example:**  
A company uses different resource groups for `dev`, `test`, and `prod` — enabling RBAC per environment and easy teardown after testing.

---

### 💡 What is Azure Resource Manager (ARM) and how is it useful?
**Answer:**  
ARM is the deployment and management service for Azure. It lets you deploy, manage, and organize resources using declarative templates (JSON or Bicep).

**Scenario Example:**  
A DevOps team uses a single ARM template to spin up identical environments (VNet, NSG, VM) across 3 regions for parallel load testing.

---

### 💡 Explain the process of creating and deploying an application on an Azure VM.
**Answer:**  
1. Choose OS image (Windows/Linux)  
2. Configure size, VNet, subnet, and NSG  
3. Connect via SSH/RDP  
4. Deploy app manually or via CI/CD  

**Scenario Example:**  
Deploy a Node.js app on a Linux VM using Azure DevOps pipeline. NSG allows inbound traffic on port 443 only, ensuring HTTPS access.

---

### 💡 What are VM Scale Sets and when should you use them?
**Answer:**  
Scale Sets allow auto-scaling of VMs based on metrics like CPU, memory, or queue length.

**Scenario Example:**  
An e-commerce app scales from 2 to 10 VMs during Black Friday automatically — no human intervention, no lost sales.

---

### 💡 How do you troubleshoot a VM that’s not reachable via RDP or SSH?
**Answer:**  
- Check NSG rules for inbound ports  
- Verify Public IP association  
- Use Azure Serial Console or Bastion  
- Review boot diagnostics  

**Scenario Example:**  
A developer’s SSH fails; NSG’s inbound port 22 was accidentally blocked. Fix by adding an allow rule with higher priority.

---

### 💡 What’s the difference between NSG and ASG?
**Answer:**  
- **NSG:** Filters traffic to/from subnet or NIC  
- **ASG:** Groups VMs logically to apply NSG rules collectively  

**Scenario Example:**  
In a 3-tier app (web/app/db), use ASGs for each tier and NSGs to allow only `web → app → db` traffic.

---

### 💡 How can you block access to your VM from another subnet?
**Answer:**  
Add a **Deny rule** with a priority lower than 65000 in the NSG to override the default `AllowVnetInBound` rule.

**Scenario Example:**  
Block test subnet from accessing production VMs to prevent accidental deployments.

---

### 💡 Are NSGs stateful or stateless?
**Answer:**  
They’re **stateful** — if inbound is allowed, the response automatically flows out.

**Scenario Example:**  
If you open inbound port 80, you don’t need an outbound rule for HTTP responses.

---

### 💡 Difference between Azure Firewall and NSG?
**Answer:**  
- **NSG:** Network-layer control within VNet  
- **Firewall:** Layer 4–7 control with logging, NAT, and FQDN filtering  

**Scenario Example:**  
Use Firewall to allow only outbound calls to `microsoft.com` and block all other internet domains.

---

### 💡 What’s the difference between Azure Load Balancer and Application Gateway?
**Answer:**  
- **LB:** Operates at Layer 4 (TCP/UDP)  
- **App Gateway:** Operates at Layer 7 (HTTP/HTTPS) + WAF  

**Scenario Example:**  
Use Load Balancer for database replicas; App Gateway for HTTPS traffic with path-based routing.

---

### 💡 What is Azure WAF and where is it used?
**Answer:**  
Web Application Firewall filters and monitors HTTP(S) traffic to protect apps from OWASP Top 10 vulnerabilities.

**Scenario Example:**  
Your public e-commerce site behind App Gateway + WAF blocks SQL injection attempts before reaching the backend.

---

### 💡 Explain VNet Peering and its use.
**Answer:**  
VNet Peering connects two VNets privately using Azure backbone — no internet required.

**Scenario Example:**  
Connect `app-vnet` and `db-vnet` in different regions to reduce latency between microservices.

---

### 💡 What is the role of VPN Gateway?
**Answer:**  
It connects Azure VNets with on-prem networks via IPsec tunnels.

**Scenario Example:**  
Your on-prem DC in Bangalore connects securely to Azure VNet in Central India for hybrid workloads.

---

### 💡 How would you securely deploy an application behind Azure Firewall using Bastion?
**Answer:**  
1. Create VNet with web, app, db subnets  
2. Deploy Azure Firewall in hub network  
3. Configure UDRs to route all outbound traffic via Firewall  
4. Access VMs via Bastion (no public IPs)  
5. Allow only App Gateway public endpoint for users  

**Scenario Example:**  
Government web app with zero public IPs; users reach via HTTPS → App Gateway → internal web VM, all traffic inspected by Firewall.

---

### 💡 What’s your approach to troubleshooting blocked outbound traffic in a secure Azure environment?
**Answer:**  
1. Check Firewall logs in Log Analytics  
2. Verify route tables (UDRs)  
3. Confirm NSG rules  
4. Validate FQDN and ports allowed  

**Scenario Example:**  
A VM fails to reach Azure Storage — route directs to Firewall, but outbound rule for `*.blob.core.windows.net` was missing.

---

## 🔐 Bonus: Azure Bastion Deep Dive

### 💡 When should you use Azure Bastion?
**Answer:**  
When you need secure RDP/SSH access **without exposing public IPs**.

**Scenario Example:**  
Prod VMs are only accessible via Bastion through Azure Portal using RBAC and MFA — no open ports, zero attack surface.

---

### 💡 What is the difference between NSG and ASG?
**Answer:** 
**Network Security Group (NSG):**

Acts as a virtual firewall for controlling inbound and outbound traffic at the subnet or network interface (NIC) level.

Defines traffic rules based on source/destination IP, port, and protocol.

**Application Security Group (ASG):**

Logical grouping of VMs based on application tiers (e.g., Web, App, DB).

Used in conjunction with NSGs to apply rules to multiple VMs using tags, rather than individual IPs.

Simplifies security management in multi-tier architectures.

💡 `Example:`
Instead of writing multiple NSG rules for each web VM, assign all web-tier VMs to a “Web-ASG” and apply one NSG rule that targets that ASG.

### 💡 How can you block access to your VM from another subnet?
**Answer:**

By default, Azure allows communication between subnets within a VNet due to a built-in NSG rule:

AllowVnetInBound (Priority: 65000)


To block access:

Create a Deny rule in the NSG with a priority number less than 65000.

Specify the source as the target subnet and destination as the VM or its subnet.

`Example:`

Priority: 400
Source: 10.0.2.0/24
Destination: 10.0.3.4
Action: Deny
Direction: Inbound
Protocol: Any


This rule ensures traffic from the specified subnet cannot reach your VM.

### 💡 Are Azure NSGs stateful or stateless?
**Answer:**

NSGs are stateful, meaning they automatically allow response traffic for any allowed inbound connection and vice versa.

`Example:`
If port 80 (HTTP) is open for inbound traffic, Azure automatically allows the return traffic for that session — no need for an outbound rule.

This makes NSGs easier to manage and less error-prone than stateless firewalls.

### 💡 What is the difference between Azure Azure Application Gateway and Azure Load Balancer?

**Answer:**

| **Feature** | **Azure Application Gateway** | **Azure Load Balancer** |
|--------------|-------------------------------|--------------------------|
| **OSI Layer** | Layer 7 (Application Layer) | Layer 4 (Transport Layer) |
| **Function** | Intelligent routing, SSL termination, WAF protection | Distributes traffic based on IP and port |
| **Use Case** | Web applications needing URL/path-based routing | General-purpose TCP/UDP traffic distribution |
| **Features** | Cookie-based session affinity, SSL offload, WAF | NAT rules, inbound/outbound load balancing |

In short:

Use `NSG` for internal subnet traffic filtering, and `Azure Firewall` for centralized, enterprise-level protection and advanced filtering.

### 💡 What are the advantages of Resource Groups in Azure?
**Answer:**

Resource Groups (RGs) act as logical containers for managing and organizing related Azure resources.

**Key Advantages:**

🧩 `Logical Organization:` Groups related resources like VMs, NICs, and Storage for easy management.

🧭 `Lifecycle Management:` Delete or deploy entire environments (e.g., dev, test) as a unit.

🏷️ `Tagging:` Apply metadata tags for cost tracking and resource categorization.

🧑‍💼 `RBAC:` Implement fine-grained access control using Role-Based Access Control.

💰 `Cost Management:` Track and manage spending per resource group.

📦 `Template Support:` Deploy consistent infrastructure via ARM or Bicep templates.

🔒 `Resource Locks:` Prevent accidental deletions or modifications.

### 💡 What is the difference between Azure User Data and Custom Data?
**Answer:**

| **Feature**       | **Custom Data**                                      | **User Data**                                   |
|------------------|------------------------------------------------------|------------------------------------------------|
| **Purpose**       | Used to pass configuration scripts during VM creation | Enhanced version of custom data with added flexibility |
| **Lifecycle**     | Available only at first boot                          | Persists in the cloud; updatable anytime       |
| **Accessibility** | Accessible only during provisioning                  | Can be retrieved or modified later             |
| **Use Case**      | Bootstrapping scripts (e.g., cloud-init)            | Long-term metadata or configuration info       |

**Summary:**  
- *Custom Data* is like a one-time setup script.  
- *User Data* is persistent and can evolve with your VM lifecycle.


### 💡 What is the difference between Azure Application Gateway and Azure Load Balancer?
**Answer:**

| **Feature**     | **Azure Application Gateway**                  | **Azure Load Balancer**                         |
|-----------------|-----------------------------------------------|------------------------------------------------|
| **OSI Layer**    | Layer 7 (Application Layer)                  | Layer 4 (Transport Layer)                     |
| **Function**     | Intelligent routing, SSL termination, WAF protection | Distributes traffic based on IP and port      |
| **Use Case**     | Web applications needing URL/path-based routing | General-purpose TCP/UDP traffic distribution |
| **Features**     | Cookie-based session affinity, SSL offload, WAF | NAT rules, inbound/outbound load balancing   |

**Quick Analogy:**  
- *Application Gateway* = Smart HTTP(S) Load Balancer  
- *Azure Load Balancer* = Fast TCP/UDP Load Distributor

### 💡 Explain the traffic flow to an Azure web application deployed in a web subnet.
**Answer:**
Let’s visualize this in steps 👇

🧭 1. *User Access*

External users access the app via a domain (e.g., myapp.azurewebsites.net).

DNS resolves this domain to a public IP associated with your front-end component.

🌍 2. *Internet Traffic to Azure*

Incoming traffic enters Azure through Front Door, Application Gateway, or Load Balancer.

These services manage load balancing, SSL termination, and global routing.

🔀 3. *Traffic Routing Within Azure*

The Application Gateway or Load Balancer forwards the request to the backend pool (VMs/containers) inside the web subnet.

🔒 4. *NSG Enforcement*

NSGs filter inbound and outbound traffic for the web subnet and VMs.

Only necessary traffic (e.g., HTTPS on port 443) is allowed.

🕸️ 5. *VNet Communication*

Subnets within the VNet communicate through internal Azure routing.

Additional security layers (like firewalls or service endpoints) can restrict inter-subnet access.

⚙️ 6. *Application Processing*

The web servers process incoming requests and send responses back through the same path.

### 💡 Describe the purpose of Azure Bastion and when it is used.
**Answer:**

Azure Bastion provides secure RDP/SSH connectivity to Azure VMs directly through the Azure Portal, without exposing them to the public internet.

🚀 *Key Benefits:*

🔐 `Secure Remote Access:` Connect securely via browser-based RDP/SSH.

🌐 `No Public IP Needed:` Keeps VMs off the internet, reducing attack surface.

🧱 `Reduced Attack Surface:` Eliminates the need for open RDP/SSH ports.

⚙️ `Native Azure Integration:` Seamless deployment within VNets.

🖥️ `Simplified Connectivity:` Access from any browser via Azure Portal.

👥 `RBAC & MFA Support:` Integrates with Azure AD for secure authentication.

📊 `Auditing & Monitoring:` Logs access attempts for security compliance.

*Use Case Example:*

When managing production VMs that must remain isolated from the internet, Bastion provides secure, controlled, browser-based remote access.

## 🧠 Quick Recap Table

| **Concept**       | **Layer**          | **Key Role**                               |
|------------------|------------------|-------------------------------------------|
| NSG              | Layer 4           | Controls traffic at subnet/NIC level      |
| ASG              | N/A               | Logical VM grouping for NSG rules         |
| Azure Firewall   | L3–L7             | Centralized enterprise firewall           |
| Load Balancer    | Layer 4           | Distributes TCP/UDP traffic               |
| App Gateway      | Layer 7           | Intelligent HTTP routing + WAF            |
| Bastion          | N/A               | Secure browser-based VM access            |
| Resource Group   | N/A               | Logical organization + RBAC               |
| User Data        | Metadata Layer    | Persistent cloud config                    |
