# Public and Private Subnets

[Back to VPC](../README.md) · [Back to index](../../../README.md)

This example uses VPC **`10.0.0.0/16`** with two subnets:

- **Public:** `10.0.1.0/24`, for a web server.
- **Private:** `10.0.2.0/24`, for a database.

## Configure the public subnet

1. Create an **internet gateway (IGW)** and attach it to the VPC.
2. Create a route table named **`Public-RT`**.
3. Configure these routes.
4. Associate `Public-RT` with subnet **`10.0.1.0/24`**.

| Destination | Target | Purpose |
|---|---|---|
| `10.0.0.0/16` | `local` | Communication within the VPC, subject to security rules. |
| `0.0.0.0/0` | `igw-xxxxxx` | Default route to the internet gateway. |

The direct IGW route makes this a public subnet. A web server also needs a **public IPv4 address or Elastic IP**, along with suitable security-group and NACL rules, for direct IPv4 internet connectivity.

## Configure private outbound access

1. Create a **public NAT gateway** in the public subnet and allocate its **Elastic IP**.
2. Create a separate route table named **`Private-RT`**.
3. Configure these routes.
4. Associate `Private-RT` with subnet **`10.0.2.0/24`**.

| Destination | Target | Purpose |
|---|---|---|
| `10.0.0.0/16` | `local` | Communication within the VPC, subject to security rules. |
| `0.0.0.0/0` | `nat-xxxxxx` | Default route through the public NAT gateway. |

The NAT gateway uses the public subnet’s route to the IGW. The private subnet has no direct IGW route and its instances do not need public IP addresses to initiate outbound connections.

## Traffic flow

```text
Public web server with public IPv4 address
    → IGW → internet

Private instance
    → NAT gateway in public subnet → IGW → internet
```

NAT allows replies to connections initiated by private instances. It prevents unsolicited internet connections from initiating a session through that NAT path; it does not block the return traffic for an outbound request.

## Notes

### Cost and availability

The IGW has no separate hourly gateway charge; normal data-transfer charges can apply. NAT gateways charge by gateway-hour and data processed. A **NAT instance** is an EC2 alternative that requires managing capacity, availability, and software.

For the source’s two-AZ deployment, create **one NAT gateway in each AZ** and route each private subnet to the NAT gateway in its own AZ. A single zonal gateway otherwise makes both private subnets depend on that gateway’s AZ.

### Addressing and access control

A public NAT gateway needs an **Elastic IP** to use the IGW path. The private database’s security group should permit only the required database protocol and port from the web tier, rather than allowing unrestricted access.
