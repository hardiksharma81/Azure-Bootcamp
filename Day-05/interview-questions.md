# 🌐 Azure Networking – Interview Q&A

---

## 🔹 1. What is the difference between NSG and ASG?

| Feature | NSG (Network Security Group) | ASG (Application Security Group) |
|----------|-----------------------------|----------------------------------|
| Purpose | Controls inbound/outbound traffic for subnets or NICs | Groups VMs logically for applying NSG rules collectively |
| Usage | Defines security rules directly | Used **in conjunction** with NSG rules |
| Benefit | Granular traffic control | Simplifies management in multi-tier architectures |

💡 **Example:**  
ASGs allow you to tag VMs (like *Web*, *App*, *DB*) and apply NSG rules based on those tags, making it easy to manage complex app tiers securely.

---

## 🚫 2. How can you block access to your VM from a subnet?

🔸 By default, **traffic is allowed** between subnets within a VNet due to the default NSG rule:  
> **AllowVnetInBound** (Priority: 65000)

✅ **Solution:**  
Create a **Deny rule** with a priority **less than 65000** to block traffic between the specific subnet and your VM.

---

## 🔁 3. Are Azure NSGs stateful or stateless?

🔹 **They are stateful.**

That means if you allow **inbound traffic on a port**, the **outbound response** is automatically allowed — no separate rule required.

💬 **Example:**  
If port **80 (HTTP)** is open for inbound requests, the outbound response on port 80 is automatically permitted.

---

## 🔥 4. What is the difference between Azure Firewall and NSG?

| Feature | Azure Firewall | Network Security Group |
|----------|----------------|------------------------|
| Layer | Operates at **Layer 4 & 7** | Operates at **Layer 4** |
| Purpose | Controls **inbound & outbound** traffic across VNets | Controls **intra-VNet** or **subnet-level** traffic |
| Features | Centralized logging, DNAT/SNAT, FQDN filtering | Simple rule-based filtering |
| Use Case | Enterprise-wide security | Subnet or VM-level filtering |

---

## 🧩 5. What are the advantages of Resource Groups in Azure?

- 🧭 **Logical Organization** – Group related resources for easy management.  
- 🔄 **Lifecycle Management** – Deploy, update, or delete resources together.  
- 🏷️ **Tagging** – Apply cost and environment tags for governance.  
- 🔐 **RBAC Integration** – Control access with role-based permissions.  
- 💰 **Cost Management** – View and optimize spend per group.  
- 🧱 **ARM Templates** – Automate deployments easily.  
- 🚫 **Resource Locks** – Prevent accidental deletions.

---

## ⚙️ 6. What is the difference between Azure User Data and Custom Data?

| Feature | Custom Data | User Data |
|----------|-------------|-----------|
| Accessibility | Available only during **VM creation** | Accessible and **updatable anytime** |
| Persistence | **Deleted** after first boot | **Persistent** in Azure |
| Use Case | Initial configuration scripts | Ongoing configuration or metadata updates |

💡 **User Data** is the newer, more flexible version — think of it as *Custom Data 2.0*.

# ⚙️ Azure Custom Data vs User Data

Both are used to **initialize or configure a VM automatically** during provisioning — like bootstrapping scripts, cloud-init configs, or metadata injection.  

But the **lifecycle and purpose** differ. 👇

---

## 🧾 1️⃣ Azure Custom Data

🧩 **Definition:**  
Custom Data is a **one-time** initialization mechanism that lets you pass data (like a script or configuration file) to a VM when it’s created.

🧠 Think of it like a “setup script on first boot.”

---

### 🔹 Key Characteristics

| Feature | Custom Data |
|----------|--------------|
| **Purpose** | Run initialization scripts during VM creation |
| **Availability** | Only available during the **first boot** |
| **Persistence** | ❌ **Ephemeral** – disappears after first boot |
| **Access** | Stored temporarily in `/var/lib/waagent/CustomData` |
| **Use Case** | Cloud-init, installing packages, setting up SSH keys |
| **Update Capability** | Not updatable post-creation |

---

### 💡 Example Use Case

You’re provisioning a VM and want to install Apache automatically:

```bash
#cloud-config
packages:
  - apache2
runcmd:
  - systemctl enable apache2
  - systemctl start apache2
You’d pass this file as custom data in Terraform or the Azure CLI:

bash
Copy code
az vm create \
  --name webserver01 \
  --resource-group dev-rg \
  --image UbuntuLTS \
  --custom-data cloud-init.txt
✅ Result: The script runs once when the VM boots for the first time.

☁️ 2️⃣ Azure User Data
🧩 Definition:
User Data is a newer and enhanced version of Custom Data — it persists beyond the first boot and can be accessed and updated anytime via the Azure control plane or API.

It’s like “Custom Data 2.0,” built for cloud-native automation.

🔹 Key Characteristics
Feature	User Data
Purpose	Persist and store initialization or metadata info
Availability	Accessible anytime, even after reboots
Persistence	✅ Persistent in Azure
Access	Retrieved via Azure Instance Metadata Service (IMDS)
Use Case	Dynamic configuration, metadata tagging, post-deployment scripting
Update Capability	Can be updated after VM creation

💡 Example Use Case
Suppose you deploy an app server fleet and want to:

Pass the environment info (ENV=prod)

Or dynamically update bootstrap metadata (like new API endpoints)

You can set it like:

bash
Copy code
az vm user-data set \
  --resource-group prod-rg \
  --name appserver01 \
  --data '{"ENV":"production"}'
Then inside the VM:

bash
Copy code
curl -H Metadata:true http://169.254.169.254/metadata/instance/compute/userData?api-version=2021-02-01
This returns the User Data payload — even after reboots. ⚡
```
## 🧠 Quick Comparison Table

| Feature             | Custom Data                       | User Data                          |
|--------------------|----------------------------------|-----------------------------------|
| **Purpose**         | One-time setup at first boot      | Persistent, updatable metadata    |
| **Lifecycle**       | Only during first boot            | Available anytime                  |
| **Storage**         | Ephemeral                         | Persistent in Azure                |
| **Use Case**        | Initial VM provisioning           | Dynamic configuration, updates     |
| **Access Post Boot**| ❌ No                             | ✅ Yes                             |
| **Supported OS**    | Linux & Windows                   | Initially Linux (expanding to Windows) |
| **Introduced**      | Legacy mechanism                  | Newer, modern replacement          |



---

## 🌉 7. What is the difference between Azure Application Gateway and Azure Load Balancer?

| Feature | Azure Application Gateway | Azure Load Balancer |
|----------|----------------------------|----------------------|
| OSI Layer | Layer **7 (Application)** | Layer **4 (Transport)** |
| Routing | Based on **URL, host, or header** | Based on **IP and port** |
| Features | SSL termination, WAF, session affinity | TCP/UDP load balancing |
| Use Case | Web applications | Generic network-level load balancing |

---

## 🚦 8. Explain the traffic flow to your application (ideal Azure setup)

### 🧍‍♂️ User Access
- Users access the app via the internet using its **public domain name**.  
- DNS resolves the name to a **public IP** (App Gateway / Load Balancer).

### ☁️ Internet Traffic to Azure
- Incoming traffic hits **Azure Front Door**, **App Gateway**, or **Load Balancer**.  
- These services handle **SSL termination**, **load balancing**, and **web protection**.

### 🛣️ Traffic Routing Within Azure
- Traffic is directed to the **backend pool** (web servers in the Web Subnet).

### 🛡️ Network Security Group (NSG) Enforcement
- NSGs filter inbound/outbound traffic to the web subnet.  
- Only necessary ports/protocols are allowed.

### 🌐 Virtual Network (VNet) Components
- Subnets communicate internally through **VNet routing**.  
- Isolation and segmentation ensure **security and performance**.

### 💻 Application Servers
- Web servers receive the request, process it, and respond back securely.

---

## 🔒 9. Describe the purpose of Azure Bastion

Azure Bastion provides **secure and seamless RDP/SSH access** to your VMs directly from the **Azure portal** — no public IPs required.

### ✨ Key Benefits:
- 🔐 **Secure Remote Access** – Access via browser over SSL.  
- 🌍 **No Public IP Exposure** – VMs stay private within the VNet.  
- 🛡️ **Reduced Attack Surface** – Eliminates RDP/SSH brute-force risks.  
- ⚙️ **Integrated Experience** – Access from Azure Portal UI.  
- 👥 **RBAC + MFA** – Enforce identity-based and multi-factor authentication.  
- 🧾 **Auditing & Monitoring** – All sessions are logged for compliance.  

💡 *Think of Azure Bastion as your secure “jump box” — but managed, hardened, and always up-to-date.*

---
## ⚙️ 10 . *Application Processing*

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

