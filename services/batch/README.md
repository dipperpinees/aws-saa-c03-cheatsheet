# AWS Batch

[Back to index](../../README.md)

AWS Batch manages batch processing, scientific simulations, and high-performance computing (HPC) workloads. Submit jobs to a job queue; Batch schedules them on suitable compute capacity and can scale a managed EC2 compute environment up for demand and down after completion.

## Notes

### Parallel simulations with large resource requirements

A research simulation that takes several hours on a server with **32 vCPUs and 256 GiB of memory** can run as parallel Batch jobs on appropriately sized EC2 instances. Batch manages the queue, scheduling, and compute capacity, reducing infrastructure administration.

The source compares this workload with these execution limits:

| Service | Source limits | Implication for this simulation |
| --- | --- | --- |
| Lambda | 15-minute duration; 10 GB memory; up to 6 vCPUs | Insufficient duration and memory |
| Fargate | Up to 16 vCPUs and 120 GB memory | Insufficient resources for the unchanged application |

These limits reflect the source notes rather than a live quota check. Running directly on Fargate also does not supply Batch's job queue and scheduler; AWS Batch itself supports several compute backends, so choose one that meets the job's requirements.

### Tightly coupled multi-node HPC

Computational fluid dynamics and similar workloads can use **AWS Batch multi-node parallel jobs** when several EC2 nodes must cooperate using **MPI (Message Passing Interface)**.

```text
AWS Batch
    |
Multi-node parallel job
    |
EC2 nodes in a compute environment
    |
MPI communication between nodes
```

Tightly coupled workloads require low inter-node latency, stable communication, concurrent node availability, and coordinated scheduling. A multi-node parallel job spans multiple EC2 instances and lets Batch manage that deployment.
