# Amazon VPC components and terminology

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

## Elastic IP Address

Elastic IP address is a static IP address. EIP can be easily tranferred from one instance to another. EIP can be dissociated from a resource and then re-associated with a different resource.

## Security Group

A security group is like a virtual firewall that can assigned to any instance running in a virtual private cloud.  
The security group is applied at the instance level and not at the subnet level. The changes of security group roles are reflected in the instances immediately and automatically. So you don't need to reboot the instance to reflect the changes.

- **Inbound**: HTTP on port 80 from the internet, SSH on port 22 only from the corporate network and MySQL/Aurora on port 3306.
- **Outbound**: HTTP on port 80 to the internet and MySQL/Aurora on port 3306 only to the database tier on corporate network.

## Network Access Control List

A network access control list (NACL) is a layer of security that acts as a firewall at the **subnet** level.

NACL is a stateless; the rules of inbound traffic are reflected to outbound traffic rules.

### The difference between security group and network access control list

- A security group can be applied only at the instance level, whereas an NACL can be applied at the subnet level.

- Security group is stateful (return traffic is allowed by default), whereas an NACL is stateless (return traffic is not allowed by default).

- A security group supports allow rules only, whereas an NACL allows both allow and deny rules.

- In a security group, all the rules are evaluated before deciding whether to allow the traffic, whereas in NACL, the rule number gets precedence.

## Amazon VPC Peering

A VPC peering connection is a one-to-one relationship between two VPCs.

The owner of each VPC (c.f. VPC A) needs to add a route to one or more of their VPC's route tables that point to the IP address range of the other VPC (c.f. VPC B. It means that the route table for one VPC cannot be overlapped/conflicted to the other VPC).
VPC peering connectivity is also controlled via route tables referencing the peering connection as a target for routes.

## Amazon VPC Endpoint

There are many AWS services running outside VPC such as S3. VPC endpoint enables us to connect to VPC and S3 directly using a private connection. For example, EC2 and S3 are directly connected via VPC endpoint. Currently, VPC endpoint is available only for S3 and DynamoDB.

## DNS and VPC

The Domain Name System (DNS) server translate a domain name into IP addresses. EC2 intance has a public DNS hostname corresponding to the public IPv4 address and a private DNS hostname corresponding to the private IPv4 address.

## DHCP Option Sets

Dynamic Host Configuration Protocol (DHCP) option sets are used to specify host configurations for instances in the VPC.

DHCP option set is automatically created for the VPC.
DHCP contains the several configuration parameters.

- **domain-name-servers**: The IP addresses of domain name servers or AmazonProvidedDNS. The default DHCP option set specifies AmazonProvidedDNS.
- **domain-name**: The domain name such as ec2.internal.
- **ntp-servers**: The IP addresses of up to 4 Network Time Protocol (NTP) servers.
- **netbios-name-servers**: The IP addresses of NetBIOS name servers.
- **netbios-node-type**: The NetBIOS node type (1, 2, 4 or 8).

## Connecting to a VPC

### Virtual private gateway

You can enable access to your network from your VPC by attaching a virtual private gateway to the VPC.

### Customer gateway

A customer gateway is the anchor on your side of that connection. The anchor on the AWS side of the VPN connection is called a virtual private gateway.

### 4 main private connectivity options (for a VPC and your corporate data center)

#### AWS hardware VPN

On the AWS side of the VPN connection, a virtual private gateway provides two VPN endpoints for automatic failover. On the remote side of the VPN connection, a customer gateway, which is the physical device or software application, are configured.

AWS supports both static and dynamic BGP-based and VPN connection. BGP is the protocal used to exchange routing information on the internet.

#### AWS Direct Connect

AWS direct connect provides a dedicated private connnection from your corporate data center to your VPC. Using this, you get consistent network performance.

#### VPN CloudHub

AWS VPN cloudhub enables multiple sites to access your VPC or securely accesss each other using a simple hub-and-spoke model.

#### Software VPN

You can create a VPN connection to your remote network by using an Amazon EC2 instance in your VPC running a software VPNN appliance.

Many customers initially start with a virtual private network and gradually establish a direct connection when their traffic increases.

## VPC Flow Logs

Amazon VPC flow logs enable you to capture information about the IP traffic going to and from network interfaces in your VPC. Flow log data is stored using Amazon **CloudWatch** logs.

## Default VPC

In every account, a VPC is created in each region by default. This is created to provide simplicity and convinience and to help you jumpstart t o AWS.
