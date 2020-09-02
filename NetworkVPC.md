#### CIDR - IPv4

- Used for SG rules or aws networking in general
- They help define an IP address range
    - we've seen ww.xx.yy.zz/32 == one IP
    - 0.0.0.0/0 == all IPs
    - But we can also define 192.168.0.0/26
- CD has two components
    - base ip (xx.xx.xx.xx)
    - subnet mask (/26)
- Base IP represents an IP contained in the range
- The subnet masks defines how many bits can change in the IP

- Subnet mask can take two forms. examples
    - 255.255.255.0 <- less cmmon
    - /24 <- more common

- Subnet masks
    - Subnet masks basically allows part of the underlying IP to get addtnl next vals from the base IP
    - /32 allow for 1 ip = 2^0
    - /31 allows for 2 ips = 2^1
    - /30 allows for 4 ips = 2^2
    - /29 allows for 8 ips = 2^3
    - /28 allows for 16 ips = 2^4
    - /26 allows for 65536 ip = 2^16
    - /0 allows for all IPS = 2^32    

- EG
    - 192.168.0.0/24
        - 192.168.0.0
        - 192.168.0.255
        - 256 IP
    - 192.168.0.0/16
        - 192.168.0.0
        - 192.168.255.255
        - 65536 IP
    - 134.56.68.123/32
        - 1 IP
    - 0.0.0.0/0
        - All IP

- PRIVATE vs PUBLIC IP (IPv4)

- IANA established certain blocks of IPV4 addresses for the use of private and public addresses

- Private IP can only allow cert values
    - 10.0.0.0 - 10.255.255.255 (10.0.0.0/8) <- in big networks
    - 172.16.0.0 - 172.31.255.255 (172.16.0.0/12) <- default AWS one
    - 192.168.0.0 - 192.168.255.255 (192.168.0.0/16) <- home networks

 #### Default VPC
 - new accts have one
 - new instances are launched into default vpc if no subnet is spcfd
 - default vpc have internet connectivity and all instances get a public IP
 - We also get a public and a private DNS name
 

 ### VPC
 - VPC = Virtual Private Cloud
 - You can have multiple VPCs in a region (max 5 per region -- soft limit)
 - Max CIDR per VPC is 5. For each CIDR
    - min size is /28 = 16 IP Address
    - max size is /16 = 65536 Ip Address
- Because VPC is private, only the Private IP ranges are allowed
    - 10.0.0.0 10.255.255.255 10.0.0.0/8
    - 172.16.0.0 172.32.255.255 172.16.0.0/12
    - 192.168.0.0 192.168.255.255 192.168.0.0/16
- Your VPC CIDR should not overlap with your other network (corp...)

#### Subnets
- Subnets are tied to AZs
- AWS reserves 5 IPs address (first 4 and last one) in each subnet
- CIDR block 10.0.0.0/24 reserved are
    - 10.0.0.0 Network Address
    - 10.0.0.1 AWS for VPC router
    - 10.0.0.2 AWS for mapping to Amazon-Provided DNS
    - 10.0.0.3 AWS for future use
    - 10.0.0.255 Network broadcast address (aws does not support broadcast in a VPC, therefore the address is reserved)
- Exam tip (will trick on askin for 29 ips... dont chose the option that gives you 32 ips cos reserved)

#### IGW
- Helps connect with the internet
- Scales horizontally and is HA and redundant
- Must be created Separately from VPC
- One VPC can only be attached to one IGW and viceversa
- IGW is also a NAT for the instances that have a public IPv4

- IGW on their own do not allow internet access
- Route tables must also be edited

#### NAT Instances - network address translation
- Allows instances in the private subnets to connect to the internet
- Must be launched in a public subet
- Must disable EC2 flag : Source/ Destination Check
- Must have an Elastic IP attached
- Route Table must be configd to route traffic from private subnet to NAT instance

- Comments
    - AWS linux ami preconfig
    - not HA / resilient setup out of the box
    - would need to create ASG in multi-AZ + resilient user-data script
    - Internet bandwidth dependso n ec2
    - must manage SG and rules
        - inbound 
            - (http/s from vpc)
            - SSH from your home ip
            - ICMP for ping
        - outbound
            - allow HTTP/s traffic to the internet

#### NAT Gateways
- AWS managed NAT, higher bandwidth, better avail, no admin
- Pay by the hour for usage and bandw
- NAT is created in a specific AZ, uses an EIP
- Cannot be used by an instance in that subnet (only from other subnets)
- Requires an IGW (private subnet -> NAT -> IGW)
- 5 GBPS of bw with automatic scaling up to 45 GBPS
- No SG to manage/required

- Resilient within the AZ 
- If you want higher availability / fault-tolerance you need multiple NAT Gateway in multiple AZ
- No cross-az failover needed because if AZ goes down -> no NAT needed

#### DNS Res
- enableDnsSupport == DNS Resolution setting
    - Default True
    - Helps decide if DNS res is supported for the VPC
    - if True, queries the AWS DNS server at 169.254.169.253
- enableDnsHostname == DNS Hostname setting
    - False by default for newly created VPC, True by default for Default VPC
    - Wont do anything unless enableDnsSupport == true
    - if True, assign public hostname to EC2 instnace if it has a public 
- If u use custom DNS domain names in a private zone in route53, you msut set both these attributes to True

#### NACL 
- NACL firewall at the subnet level
- default NACL allows everything in/out
- One NACL per Subnet, new subnets are assigned the default nacl
- Define nacl rules
    - Rules have a number 1-32766 and higher precedence with a lower number
    - if you define `100 ALLOW IP` and `200 DENY IP`, IP will be allowed
    - 