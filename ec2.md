# Placement groups

- EC2 Instance Placement Strategy
    - Cluster -- clusters instances into low-latency group in a single AZ
    - Spread -- spread instances across underyling hardware (max 7 instances per group per AZ) -- Critical applications
    - Partition -- spread instances across many diff partitions (which rely on different sets of racks) with an AZ. Scales to 100s of EC2 instances per group (Hadoop, Cassandra, Kafka)

### Cluster
- Same Rack
- Same AZ
- PROS:
    - Great Network (10 Gbps bandwidth between instances)
- CONS:
    - If the rack fails, all instances fails at the same time
- USE CASE:
    - Big data job that needs to complete fast
    - App that needs extremely low latency and high network throughput

### Spread
- Diff Rack
- Diff AZ
- PROS:
    - Can span across AZ
    - Reduced risk of simultaneous failure
    - EC2 instances are on different physical hardware
- CONS:
    - Limited to 8 instances per AZ per placement group
- USE CASE:
    - App that needs to max high ava
    - Critical apps where each instance must be isolated

### Partition
- Same AZ
- Grouped by partition (Rack?)
- PROS:
    - Up to 100s of ec2 instances
    - up to 7 parts per AZ
    - Instances in a  part do not share racks w the instances in other partitions
    - A partition failure can affect many EC2 but wont affect other partitions
    - EC2 instances get access to the partition information metadata
- USE CASE:
    - Distributed big data apps

# Shutdown Behaviour

### Shutdown behaviour:

- CLI Attribute: `InstanceInitiatedShutdownBehavior`

### Termination Protection

- Enable Termination Protection (Avoid humans error)

### If both termination protection is on && Shutdown behavior is set to terminate:

- You will terminate an instance if you shut down from within the OS

# EC2 Launch Troubleshooting

-  `InstanceLimitExceeded` 
    - Error: Account limit per region. 
    - Resolution:
        - Open a ticket with AWS for them to up your limit
        - Create an instance in a different region
    - Default:
        - 20 instances
        - Limits are now counted in vCPU (instead of instances)
            - not reflected in exam yet

- `InsufficientInstanceCapacity`
    - Error: AWS does not have that much on-demand capacity in that particular AZ
    - Resolution:
        - Change the instance type (update to the right one later)
        - Wait a few mins
        - Request one at a time (if you requested more than 1)

- Instance terminates immediately (goes from pending to terminated)
    - You've reached your EBS volume limit
    - An EBS snapshot is corrupt
    - The root EBS volume is encrypted and you do not have permissions to access the KMS key for decryption
    - The instance store-backed AMI that you used to launch the instance is missing a required part (an image.part.xx file)


# EC2 SSH trouble

- Make sure the pem file has 400 permissions
- Make sure the username for the OS is given correctly when loggin via SSH or you will get a `Host key not found` error (ec2-user vs ubuntu...etc)

- Connection timeout to ec2 instance via ssh:
    - SG is not configured correctly
    - CPU load of the instance is high (ssh process cant reply to us)

# EC2 Instance Launch Types

- On Demand Instance: short workload, predictable pricing
- Reserved (Minimum 1 year)
    - Reserved Instances: Long workloads 
    - Convertible Reserved Instances: long workloads with flexible instances
    - Scheduled Reserved Instances: example Every Thursday between 3 and 6 pm

- Spot Instances:
    - Short workloads (cheap)
    - You can load the instance

- Dedicated Instances:
    - No other customers will share your hardware

- Dedicated Hosts:
    - Book an entire physical server, control instance placement

### On Demand

- Pay for what you use (Billing per second, after the first min)
- Has the highest cost but no upfront payment
- No long term commitment
- Recommended for short-term and un-interrupted workloads, where you can't predict how the application will behave

### Reserved Instances

- Save up to 75% discounts compared to On Demand
- Pay upfront for what you use with long term commitment
- Reservation period can be 1 - 3 years
- Reserve a specific instance
- Recommended for steady state usage applications (think database)

    - Convertible Reserved Instance
        - Can change the EC2 instance type
        - Up to 54% discount
    - Scheduled Reserved Instances
        - Launch within a time window you reserve
        - Require fraction of the week / day / month

### Spot Instances

- Can get up to 90% discount compared to On-Demand
- Instance that you can "lose" at any point of time if your max price is less than the current spot price
- The MOST cost-efficient instances in AWS
- Useful for workloads that are resilient to failure
    - Bach Jobs
    - Data Analysis
    - Image Processing

- Not great for critical jobs

#### Great Combo: Reserved Instances for baseline + On-Demand & Spot for peaks

### Dedicated Hosts

- Physical dedicated EC2 server for your use
- Full control of EC2 instance playment
- Visibility into the underlying sockets / physical cores of the hardware
- Allocated for your account for a 3 year period reservation
- Userful for software that have complicated licensing model (BYOL)
- Companies with strong regulatory or compliance needs

### Dedicated Instaces

- Instances running on hardware that's dedicated to you
- May share hardware with other instances in same account
- No control over instance placement (can move hardware after stop / start)


# Spot Instances & Spot Fleet

- Can get a discount up to 90% compared to On-Demand
- Define a max spot price and get the instance `while current spot price < max`
    - The hourly spot price varies based on offer and capacity
    - If the current spot price > your max price you can shoot to stop or terminte your instance (2 mins grace period)
- Spot Block
    - "Block" spot instance during a specific time frame (1 to 6 hours) without interruptions
    - In rare situations, the instance may be reclaimed    

### How to terminate Spot Instances
- Spot Request
    - Max price
    - Desired # of instances
    - Launch spec
    - Request type: one time | persistent
    - Valid from, Valid until

- Cancel the Spot Request 
- Then terminate the instances

### Spot Fleets
- Spot Fleets = set of Spot Instances + (optional) On-Demand instances
- The Spot Fleet will try to meet the target capacity with price constraints
    - Define possible launch pools: instance type, OS, AZ
    - Can have multiple launch pools, so that the fleet can choose
    - Spot Fleet stops launching instances when reaching capacity or max cost

- Strategies to allocate Spot Instances:
    - `lowestPrice`: from the pool with the lowest price (cost opt, short work)
    - `diversified`: distributed across all pools (great for availability, long workloads)
    - `capacityOptimized`: pool with the optimal capacity for the number of instances

### Spot Fleets allow us to automatically request Spot Instances with the lowest price

### Check on Reserved Instance vs Reserved Hosts

# Instance Types

### R: Applications that needs a lot of Ram (in-memory caches)

### C: Applications that need good CPU (compute / database)

### M: Applications that are balanced - think medium - (general / webapp)

### I: Applications that need good local I/O - instance storage (databases)

### G: Applications that need a GPU (video rendering/machine learning)

### T2/T3: Burstable capacity (up to a capacity)

### T2/T3 Unlimited: Unlimited burst

### Burstable Instances 

- Burst means that overall the instance has ok performance
- Whent he machine needs to process something unexpected (a spike on load) it can burst and cpu can be very good
- If the machine bursts, it utilizes burst credits
- If all the credits are gone the CPU becomes bad
- If the machine stops bursting credits are accumulated over time
- They can be amazing way to handle unexpected traffic and getting the insurance that it will be handled correctly
- If your instance consistently runs low on credit(burst), you need to move to a diff kind of non-burstable instance
- (Unlimited) You pay extra money for the bursting

# EC2 AMIs

- Lots of base images
    - Ubuntu
    - Fedora
    - RedHat
    - Windows
    - Etc..

- Can be customized at runtime (userdata)

- Why create an AMI
    - Pre-installed packages needed
    - Faster boot time
    - Machine comes configured with monitoring/enterprise software
    - Security concerts - control over the machines in the network
    - Control of maintenance
    - AD integration out of the box
    - Installing your app ahead of time (for faster deploys when auto-scaling)
    - Using someone else's AMI that is optimized for running an app, db... etc

- AMI are built for a specific region

- Public AMIs
    - You can leverage AMIs from other people
    - You can also pay for other people's AMI by the hour
    - These people have optimized the software
    - Machine is easy to run and configure
    - You basically rent "expertise" from the AMI creator

- Do your due dilligence
    - Some AMIs can come with malware
    - Do not use an AMI you dont trust
    - Might not be secure for your enterprise

- AMI Storage:
    -  Your AMI take space and they live in s3
    - Amazon s3 is a durable resilient storage
    - By default they are private
    - You can make them public and/or sell it

- AMI Pricing:
    - You pay for the s3 space
    - Over its quite inexpensive
    - Make sure to remove the AMIs you dont use

### Cross accoutn AMI Copy
- You can share with someone elses AWS account
- Sharing an AMI does not affect ownership
- It doesnt prevent against copying (if they copy into a region)
- To copy an AMI that was shared with you from another acct, the owner of the source AMI must grant you read permissions for the storage that backs the AMI, either the associated EBS snapshot(for an amazon ebs-backed ami) or an associated s3 bucket (for an instance storage-backed ami)

- You can't copy an encrypted AMI that was shared with you from another account. Instead, if the underlying snapshot and encrpytion key were shared with you, you can copy the snapshots while reencrypting it with a key of your own. You own the copied snapshot, and can register it as a new AMI
- #### You cant copy an AMI with an associated `billingProduct` code that was shared with you from another account. This includes Windows AMIs and AMIs from the AWS Marketplace. To copy a shared AMI with a `billingProduct` code, launch an EC2 instance in your account using the shared AMI and then create an AMI from the instance

# Elastic IPs

- When you stop and start an instance IP changes
- If you need a fixed ip you need an elastic IP
- An elastic IP is a public IPv4 IP you own as long as you dont delete it
- You can attach it to one instance at a time
- You can remap it across instances

- You dont pay for the Elastic IP if its attached to a server
- You pay for the Elastic IP if it's not attached to a server

- With an Elastic IP you can mask the failure of an instance or software by rapidly remapping the address to another instance in your account 
- You can have only 5 Elastic IP in your account (soft limit)
- Try to avoid using Elastic IPs
    - DNS
    - LoadBalancer
























