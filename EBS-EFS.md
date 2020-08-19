### Whats an EBS volume?

- Persist your data
- It's a network drive (not physical)
    - communicates over the network
- It can be detached from one ec2 and attached to another one
- Its locked to an AZ
    - an ebs vol in us-east-1a cannot be attached to us-east-1b
    - To move a volume across, you first need to snapshot

- Have a provisioned capacity
    - you get billed for the provisioned cap
    - you can increase capacity of the drive over time

- Types
    - GPS (SSD) - General purpose
    - IO1 (SSD) - Highest performance SSD vol for mission-critical low-latency or high-throughput
    - ST1 (HDD) - Low cost HDD vol designed for freq accessed, throughput intensive workloads
    - SC1 (HDD) - Lowest cost HDD vol designed for less frq access workloads

### Only GPS and IO1 can be used as boot vols


## EBS Volume Type Use cases

### GP2
- Rec for most workloads
- System boot vols
- Virtual Desktops
- Low-latency interactive apps
- Development and test envs

- 1 GiB - 16 TiB
- Small gp2 vols can burst IOPS to 3000
- Max IOPS is 16k
- 3 IOPS per GB, means at 5,334 we are at the max IOPS

### IO1
- Crit business apps that req sustain IOPS performance, or more than 16k IOPS per vol (gp2 lim)
- Large db workloads such as:
    - mongo cassandra sql mysql postgresql

- 4 GiB - 16 TiB
- IOPS is provisioned (PIOPS) - MIN 100 - MAX 64000 (nitro instances) else MAX 32000
- The max ratio of provisioned IOPS to the req vol is 50:1

### ST1
- Streaming workloads req consistent fast throughput
- big data, data warehouses, log proces
- kafka
- cannot b a boot vol

- 500 GiB - 16 TiB
- Max IOPS of 500
- Max throughput of 500 MiB/s

### SC1
- throughput-oriented for large vols of data that is infreq accessed
- Scenarios where the lowest storage cost is important
- cannot be a boot vol
- 500 Gib - 16 TiB
- Max IOPS is 250
- Max Throughput of 250MiB/s - can burst

## EBS Burst

### GP2
- If your GPS vol is less than 1000 GiB, it can 'burst' to 3000 IOPS performance
- This is similar to the t2 instances with their CPU burst
- You accumulate 'burst credit over time', which allows your volume to have good performance when needed
- The bigger the volume the faster you fill up your 'burst credit balance'
- What happens if I empty my I/O credit balance?
    - The max I/O you get becomes the baseline you paid for
    - If you see the balance being 0 all the time, increase the GP2 volume or switch to Io1
    - Use cloudwatch to monitor the I/O creditbalance

- Note: burst concept also applies to st1 or sc1 (for inc througput)

### Computing throughput MB/s based on IOPS
- GP2
    - Throughput in MiB/s = (vol size in GiB) x (IOPS per GiB) x (I/O size in KiB)
        - eg: 300 IO ops per second * 256 KiB per I/O op = 75MiB/s
        - Limit to a max of 250 MiB/s (means vol >= 334 GiB won't incr throuhgput)
- IO1
    - Throuhgput in MiB/s = (Prov IOPS) x (I/O size in KiB)
    - The throughput limit of io1 vols is 256KiB/s for each IOPS prov
    - Limit to a max of 500 MiB/s (at 32000 IOPS) and 1000 MiB/s (64k)

### EBS Snapshots

- Incremental - only backup changed blocks
- EBS backups use IO and you shouldnt run them while your app is handling a lot of traffic
- Snapshot will be stored in s3 (you wont see them)
- Not necessary to detatch vol to do snapshot, but recommended
- Max 100,000 snapshots
- Can copy snapshots across AZ or Region
- Can make AMI from snapshot
- EBS vols restored by snapshot need to be pre-warmed (using fio or dd command to read the entire volume)

### EBS Encryption
- When you create an enc you get a data at rest and in transit encrypted
- all snapshots are encrypted
- all vols are encrypted
- everything is handled for you
- encryption has minimal impact on latency
- encryption leverages KMS
- copying an unencrypted vol allows encryption
    - create a ebs snapshot
    - encrypt ebs snapshot
    - create a new ebs vol
    - attach the encrypted vol to the instance

### EBS vs Instance store
- Some instances do not come with root EBS
- Instance store == ephemeral
- Instance store == physically attached to the machine
    - Better IO (no network latency)
    - Good for buffer/cache/scratch data/ temp content
    - Data survives reboots

    - On stop or termination, the instance store is lost
    - You cant resize the instance store
    - Backups must be operated by the user

- Instance store:
    - Very hight IOPS
    - disks up to 7.5 TiB (stripped to reach 30 TiB)
    - Block storage (like EBS)
    - Cannot be increased in size
    - Risks of data loss if hardware fails

- EBS == network drive

### EBS for SysOps
- If you plan to use the root vol of an instance after its terminated:
    - Set delete on termination flag to no
    - you can see this option when creating the EC2 instance
- if you use EBS for high perf, use EBS-optimized instance types
- if an EBS is unused you still pay for provisioning
- For cost saving over a long period, it can be cheaper to snapshot a volume and then restore it
- High wait time or slow response for SSD -> increase IOPS
- EC2 won't start with EBS vol as root: make sure vol names are properly mapped (eg: dev/xvdb vs dev/xvdba)
- After increasing vol size ( you still need to repartion to use incremental storage eg: growfsxfs)

### EBS RAID options
- EBS is already redundant storage (replicated within an AZ)
- But what if oyu wanted to increase IOPS to 100 000?
- What if you want to mirror your EBS vols?
- You would mount volumes in parallel in RAID settings!
- RAID is possible as long as your OS supports it
- Some RAID options are:
    - RAID 0
    - RAID 1
    - RAID 5 (not rcmndd for ebs)
    - RAID 6 (not rcmndd for ebs)

- RAID 0:
    - means performance
    - One logical vol
        - backed by 2 EBS vols
        - Get total disk space, and I/O (2 50gb EBS vols will net you 100gb)
        - Using this, we can have a very big disk with a lot of IOPS
        - eg:
            - 2 500GiB ebs with 4000 IOPS nets you:
                - 1000 GiB RAID 0 array w an avail bandwidth of 8000 IOPS and 1000MB/s throughput
                
- RAID 1:
    - Fault tolerance
    - One logical vol
        - backed by 2 EBS vols
        - if one disk fails, our logical vol is still working
        - we have to send the data to two EBS vol at the same time (2x network) - make sure you have a ec2 instance that can handle the network throughput
        -  Use case:
            - App needs to increase volume fault tol
            - Application where you need to service disks
        - eg:
            - 2 500GiB ebs with 4000 IOPS nets you:
                - 500 GiB RAID 1 array with an available bandwidth of 4000 IOPS and 500 MB/s throughput

### Cloudwatch and EBS
#### Important EBS vol cloudwatch metrics:
- VolumeIdleTime: number of secs when no r/w is submitted
- VolumeQueueLength: number of ops waiting to be executed (high number means probably IOPS or app issue)
- BurstBalance: if it becomes 0, we need a volume with more IOPS
- GP2 every 5 mins
- IO1 every 1 mins
- EBS vols have status check
    - ok - vol is performing good
    - warning - performance below expected
    - impaired - stalled, performance serverely degraded
    - insufficient data - metric data collection in progress

### What's an EFS vol

- Managed NFS (network file system) that can be mounted on many EC2
- EFS works with EC2 instances in multi-AZ
- Highly Avail, scalable, expensive (3x gps), pay per use
- Attached Security Group
- Can share the same EFS drive with multiple instances
- Use cases:
    - Content management, web serving, data sharing, wordpress 
    - Uses NFSv4.1 protocol
    - Uses SG to control access to EFS
    - Compatible with only Linux Based AMI!!!! (not windows)
    - Encrypt EFS at rest using KMS 

    - POSIX file syste, (~Linux) that has standard file API
    - File system autoscales automatically, pay per use, no capacity planning
- Performance and Storage Classes:
    - EFS Scale 
        - 1000s of concurrent NFS clients, 10 GB+ /s throughput
        - Grow to Petabyte-scale network file system, automatically
    - Performance mode (set at EFS creation time)
        - Gen Purpose (default) - latency-sensitive use cases (web server, CMS)
        - MAX IO - higher latency, throughput, highly parallel (big data, media processing)
    - Storage Tiers (lifecycle management feature - move file after N days)
        - Standard: for frequently accessed files
        - Infrequent access (EFS-IA): cost to retrieve files, lower price to store

