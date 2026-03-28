### 1. Traffic Forwarding: Switching vs. Routing [2 Marks]
The primary difference lies in the **Layer of operation** and the **addressing** used to make forwarding decisions:
* **Switching (Layer 2):** Forwards data frames within the same local network (LAN) using **MAC addresses**. It maintains a MAC address table to map ports to devices.
* **Routing (Layer 3):** Forwards data packets between different networks (WAN/LAN) using **IP addresses**. It uses a routing table and protocols (like BGP or OSPF) to determine the best path across complex network topologies.



### 2. Preventive vs. Detective Security Mechanisms [2 Marks]
In a data center, these two function as a "Shield and Radar" system:
* **Preventive (The Shield):** Designed to stop an attack before it happens. *Example: Firewalls, MFA, and Least Privilege custom IAM roles.*
* **Detective (The Radar):** Designed to identify and alert when a security breach or anomaly occurs. *Example: IDS/IPS, Log Auditing, and SIEM.*
* **Complement:** Preventive measures reduce the overall attack surface, while Detective measures catch the "unknown unknowns" or sophisticated threats that bypass the initial shield, providing the data needed to harden the preventive controls further.

### 3. Cloud Elasticity vs. Infrastructure Planning [2 Marks]
Elasticity shifts infrastructure planning from a **Static/CapEx model** to a **Dynamic/OpEx model**:
* **Traditional Planning:** Requires "Peak-Load" forecasting, often leading to expensive over-provisioning (idle hardware) or under-provisioning (crashes during spikes).
* **Cloud Strategy:** Focuses on "Just-in-Time" provisioning. Planning shifts from *buying* hardware to *designing* automated scaling policies. This allows the infrastructure to shrink or grow based on real-time demand, ensuring cost-efficiency and high availability.

---

### 4. Capacity Planning for a 40% Traffic Spike [2 Marks]
To accommodate a 40% increase for a new feature, I would follow these steps:
1.  **Establish Baseline:** Analyze current utilization (CPU, RAM, Disk I/O, and Network) of existing services.
2.  **Load Testing (Benchmarking):** Simulate the 140% load in a staging environment to identify the "breaking point."
3.  **Identify Bottlenecks:** Check if the bottleneck is horizontal (need more pods/VMs) or vertical (database connection limits or locking).
4.  **Provision with Buffer:** Scale resources to handle the 40% increase plus a 10-20% "safety buffer" to account for unpredictable bursts.

### 5. Performance Optimization vs. Extra Capacity [2 Marks]
Optimization takes precedence when the bottleneck is **resource-contention bound** rather than **resource-exhaustion bound**.
* **Example:** A database query lacking an index. Adding more CPU or RAM (extra capacity) won't significantly speed up a full table scan on millions of rows. Optimizing the query/adding an index (performance optimization) resolves the root cause, whereas adding capacity would yield diminishing returns and wasted costs.

---

### 6. Kubernetes: kube-controller manager [3 Marks]
The **kube-controller manager** is the "brain" of the Kubernetes control plane. It runs multiple controller processes that regulate the state of the cluster by constantly comparing the **current state** to the **desired state** (the "Control Loop").

* **Example (Deployment Controller):** If you set a Deployment to have 3 replicas, the controller identifies if a pod crashes (current = 2) and commands the scheduler to start a new one to reach the desired state (3).
* **Another Controller (Node Controller):** It monitors the health of nodes. If a node becomes unreachable, it is responsible for marking the node as unhealthy and evicting pods to healthy nodes.



### 7. StatefulSet vs. Deployment [3 Marks]
* **Deployment:** Used for **stateless applications** (e.g., Web Servers, APIs). Pods are interchangeable and have random hashes in their names. If a pod dies, it’s replaced by a generic equivalent.
* **StatefulSet:** Used for **stateful applications** (e.g., Databases like MongoDB, Kafka). Pods have a stable, unique ordinal index (pod-0, pod-1) and maintain persistent identities/storage even after rescheduling.

| Feature | Deployment | StatefulSet |
| :--- | :--- | :--- |
| **Pod Identity** | Random/Interchangeable | Unique/Persistent |
| **Storage** | Shared/Ephemeral | Dedicated/Persistent per pod |
| **Use Case** | Nginx, Python Flask app | PostgreSQL, Zookeeper |

---

### 8. SLOs and SLIs for Reliability [4 Marks]
These components form the foundation of Site Reliability Engineering (SRE):
* **SLI (Service Level Indicator):** A quantitative measure of a service's performance (e.g., "The latency of the `/payment` API over the last 5 minutes").
* **SLO (Service Level Objective):** A target value or range for an SLI (e.g., "99.9% of `/payment` requests must be faster than 200ms").
* **Working Together:** The SLI tells you what the current reality is; the SLO tells you if that reality is "good enough."
* **Driving Improvements:** If the SLI consistently drops below the SLO, the team stops shipping new features and shifts focus to **technical debt** and **reliability engineering** until the service stabilizes.



---

### 9. Reliability & Security in Financial Systems [5 Marks]
In a financial system (like UPI transaction processing), reliability and security are two sides of the same coin.
* **Commonalities:** Both prioritize **Integrity** (data must not be corrupted or tampered with) and **Availability** (the system must be up for the transaction to be secure and valid).
* **Integration Approach:**
    1.  **Immutability:** Use immutable infrastructure to ensure that no manual, unauthorized changes (security) can break the system configuration (reliability).
    2.  **Redundancy & Failover:** Ensure multi-region deployments so that a DDoS attack or a data center failure doesn't halt transactions.
    3.  **Auditability:** Every transaction must be logged. Detailed logging serves as a "trail" for security forensics and a "debug log" for reliability issues.
* **Robustness:** Addressing these together ensures that a security patch doesn't crash the system and a system failover doesn't leave a "window" of unencrypted data.

### 10. Error Budgets: Innovation vs. Reliability [5 Marks]
The **Error Budget** is mathematically defined as:
$$\text{Error Budget} = 1 - \text{SLO}$$
It represents the amount of failure a service can tolerate before customers are unhappy.

* **Balancing:** * **High Budget:** If the budget is healthy, the team can take risks, such as deploying experimental features or aggressive updates.
    * **Low Budget:** If the budget is nearly spent, "Innovation" stops. The team enters a **Code Freeze** where only reliability-related patches are allowed.
* **Strategies for Exhaustion:**
    1.  **Post-Mortems:** Conduct blameless reviews to identify why the budget was spent.
    2.  **Automation:** Increase automated test coverage to catch bugs earlier.
    3.  **Rollback Speed:** Improve CI/CD pipelines to ensure faster "Time to Recovery" (MTTR) if a new release fails.
    4.  **Observability:** Enhance monitoring to catch SLI dips before they consume the remaining budget.
