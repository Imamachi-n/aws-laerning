# Amazon VPC components and terminology

## Amazon VPC

### IPv4 CIDR range

**Max**: 10.0.0.0/16 (2^16 = 65536 addresses)  
**Min**: /28 (2^4 = 16 addresses)

## Subnet

A subnet is tied to only one avalability zone (AZ).  
A VPC can span multiple AZs in a region.

In summary,

- Subnets are **AZ** specific. For **multiple AZs**, create multiple subnets
- VPC are **region** specific. For **multiple regions**, create different VPCs.

But actually,
the first 4 IP addresses and The last 1 IP address are for internal networking purposes.  
For example, in a subnet with a CIDR block of 10.0.0.0/24, the following 5 IP address are reserved: 10.0.0.0, 10.0.0.1, 10.0.0.2, 10.0.0.3, 10.0.0.255.

## Route Table

Every subnet should have a route table.  
Amazon VPC automatically creates the main route table.

**Target**: where the traffic is directed (10.0.0.0/16 etc...)  
**Distination**: specify the IP range that can be directed to targets (Local, igw-xxxxxxx etc...)

## Internet Gateway

An internet gateway (IG) is a component of VPC that allows your VPC to communicate with the internet.

## Network Address Translation (NAT)

NAT devices enables any instance in private subnets to connect to the internet.
A NAT device forwards traffic from the instances in private subnets to the internet and then returns the response to the instances.

### NAT Instances

In general, web servers are run in a public subset and database servers are run in a private subset. We need to provide the internet access to the database servers because downloading database patches or install something.

NAT instance is created in a public subnet and database servers connect the internet via the NAT instance.

Public subnet is assigned the routing table with a local network (VPC) and an internet gateway as targets. On the other hand, private subnet is assigned the routing table with a local network (VPC) and a NAT instance.

### NAT Gateway

A NAT gateway performs the same function as that of a NAT instance. But it is a managed service in AWS and does not require administration overhead.

### Egress-only internet gateway

Egress-only internet gateway is the same as NAT gateway. It allows VPC to connect with the internet for **IPv6** traffic not IPv4. If using IPv4, NAT gateway is used instead of egree-only internet gateway.

## Elastic Network Interface

ENI gives the ability to create one or more network interfaces and attach them to your instance.

The network traffic is redirected to the new instance when you move a network interface from one instance to another.
