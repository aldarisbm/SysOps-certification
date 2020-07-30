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



































