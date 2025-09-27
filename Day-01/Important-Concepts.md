# ☁️ Cloud Computing Vocabulary

A quick-reference glossary of essential cloud terms — with clear definitions and context.

---

## 🖥️ Virtualization
The process of creating a **virtual version** of hardware (server, OS, storage, or network).

- ✅ Maximizes hardware utilization  
- ✅ Enables multiple workloads on one physical host

---

## 💻 Virtual Machine (VM)
A **software-based emulation** of a physical computer.

- 🧑‍💻 Runs its own OS & apps  
- 🔄 Hosted on a hypervisor (VMware, Hyper-V, KVM)  
- 🌍 Multiple VMs can run on a single host

---

## 🔌 API (Application Programming Interface)
A **set of rules & protocols** that allow software components to communicate.

- 🔄 Acts as a bridge between apps  
- 📡 Widely used in cloud services (e.g., AWS SDK, REST APIs)

---

## 🌍 Regions
**Geographic locations** where cloud providers host data centers.

- Each region contains multiple Availability Zones (AZs)  
- 🌐 Examples: `us-east-1`, `ap-south-1`

---

## 🏢 Availability Zones (AZs)
**Independent data centers** within a region.

- 🔄 Each has its own power, cooling, and networking  
- ⚡ Designed for high availability and fault isolation

---

## 📈 Scalability
The ability of a system to **handle growth** by adding resources.

- ⬆️ Vertical Scaling → Add more power to existing servers  
- ➕ Horizontal Scaling → Add more servers

---

## 🎚️ Elasticity
The ability to **auto-scale resources** up or down based on demand.

- 💰 Pay only for what you use  
- ⚡ Ideal for unpredictable workloads

---

## ⚡ Agility
The ability to **quickly adapt to changes**.

- 🚀 Rapid deployment of apps & infra  
- 🛠️ Key for DevOps & CI/CD

---

## 🔄 High Availability (HA)
Ensures apps/systems are **operational most of the time** (e.g., 99.9% uptime).

- 🛡️ Achieved via redundancy across AZs/regions  
- 🚫 Minimizes downtime

---

## 🛡️ Fault Tolerance
The ability of a system to **keep working even when parts fail**.

- 🧱 Built-in redundancy  
- 📌 Example: Multi-AZ RDS continues working even if one AZ fails

---

## 🌪️ Disaster Recovery (DR)
Strategies to **restore systems & data after major failures**.

- 📀 Backup & replication  
- 🌍 Cross-region failover & recovery

---

## ⚖️ Load Balancing
Distributes **traffic/workloads** across multiple servers.

- 🚦 Prevents a single server from overload  
- 🛠️ Examples: AWS ELB, Nginx, HAProxy

---

## 📊 Quick Comparison

| Term               | Focus Area          | Key Idea 🗝️           | Example Use Case        |
|--------------------|-------------------|---------------------|------------------------|
| 📈 Scalability     | Growth capacity     | Add more resources    | Handle user growth      |
| 🎚️ Elasticity     | Demand-based scaling| Auto adjust           | Flash sales traffic     |
| 🔄 High Availability | Uptime focus      | Reduce downtime       | 99.9% SLA web apps      |
| 🛡️ Fault Tolerance | Failure resilience  | Keep running          | Multi-AZ database       |
| 🌪️ Disaster Recovery | Recovery strategy | Restore
