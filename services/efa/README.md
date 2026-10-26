# Elastic Fabric Adapter

[Back to index](../../README.md)

## Notes

### ENI, ENA, and EFA

| Component | Role |
|---|---|
| **Elastic Network Interface (ENI)** | A virtual network interface that supplies IP addressing and network connectivity. Every EC2 instance has at least a primary ENI. |
| **Elastic Network Adapter (ENA)** | Provides enhanced networking for supported instance types. Network throughput can reach **100 Gbps or more**, depending on the instance. Traditional TCP/IP processing still involves the operating-system kernel. |
| **Elastic Fabric Adapter (EFA)** | Adds an **OS-bypass** communication path for supported applications, reducing latency for high-performance computing, MPI workloads, and AI/ML training. |

Network throughput depends on the instance type; an ENI is not a separate universal speed tier with a fixed 10-Gbps limit.

### Operating-system and instance compatibility

The EFA OS-bypass capability described in the source is for supported Linux environments, rather than Windows. For the source’s **Windows `t3a.medium`** scenario, ENA is the applicable enhanced-networking adapter.

EFA requires a supported instance type, such as appropriate `c5n` or `p4d` configurations; an instance-family suffix alone does not establish support. `t3a.medium` supports ENA.

**Intel 82599 Virtual Function (VF)** is the older enhanced-networking technology used by selected previous-generation families such as C3, R3, and I2. Choose the adapter and driver supported by the actual instance type.
