# Amazon VPC componenets and terminology

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
