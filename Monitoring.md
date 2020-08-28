## CloudWatch Metrics

- provides metrics for every service in AWS
- Metric is a variable to monitor (cpuutil, networkin)
- Metrics belong to namespaces
- Dimension is an attribute of a metric (instance id, env, etc)
- up to 10 dimensions per metric
- metrics have timestamps
- Can create dashboard of metrics

#### AWS CLoudWatch EC2 detailed monitoring
- EC2 instances metrics have metrics every 5 mins
- with detailed monitoring (for a cost) you get data , every 1 minute
- use detailed monitoring if you want to prompt scale your ASG!

- AWS Free tier allows us to have 10 detailed monitoring metrics

- EC2 Memory usage is by default not pushed must be puhsed from inside your instance as a custom metric

#### CloudWatch Custom Metrics
- Possi to define and send your own custom metrics to CloudWatch
- Ability to used dimensions (attributes) to segment metrics
    - instance.id
    - environment.name
- Metric resolution
    - Standard 1 min
    - High res - up to 1 second (StorageResolution API Parameter) - Higher cost

- Use API call PutMetricData
- Use exponential back off in case of throttle errors

#### Cloudwatch dashboards
- Great way to setup dashb for quick access to key metrics
- Dashboards are global (you can see your dashboards from any region)
- You can include graphs from diff regions 
- You can change time zone and time range
- Setup automatic refresh (10s, 1m, 2m, 5m, 15m)

- Pricing
    - 3 dashboards (up to 50 metrics) for free
    - 3$/dashboard/month afterwards

#### CloudWatch Logs
- Apps can send logs to CW using the SDK
- CW can collect logs from 
    - Elastic Beanstalk collect logs from app
    - ECS collection from contain
    - Lambda collection from fn logs
    - VPC Flow Logs VPC specific logs
    - API Gateway
    - CloudTrail based on filter
    - CW log agents (ec2)
    - Route53 Log DNS queries

- Cloudwatch Logs can go to
    - export to s3 for archival
    - ElasticSearch cluster for fruther analtics

- Logs storage architecture
    - Log groups - arb tname usually representing an application
    - Log stream instances within application / log files / containers
- Define log expiration policies (never expire, 30 dys, etc)
- Use the AWS CLI we can tail cw logs
- To send logs to CW, make sure IAM permissions are correct
- Security, encryption of logs using KMS at the group level

#### Cloudwatch logs metric filter & insights
- CW logs can use filter expressions
    - eg find a specific ip insde of a log
    - metric filters can be used to trigger alarms

- CW logs Insights
    - can be used to query logs an add queires to CW dashboards


#### AWS CW Alarms
- Alarms are used to trigger notifications for any metric
- Alarms can go to Auto Scaling, EC2 Actions, SNS notifications
- Various opts (sampling, max, %, etc..)
- alarm states
    - OK
    - insufficient data
    - ALARM
- Period
    - length of time in seconds to evaluate the metric
    - high resolution custom metrics: can only choose 10 sec or 30 sec

CloudWatch Alarm Targets
- STop, terminate, reboot, or recover an EC2 instances
- Trigger autoscaling action
- send notification to SNS (from which you can do pretty much anything)

- Alarms can be created based on cw logs metrics filters
- cw doesnt test or validate the actions that is assigned
- to test alarms and notifications set the alarm state to Alarm using CLI

 #### CloudWatch Events
- Source + rule -> Target
- Schedule: cron-jobs
- Event Pattern: event rules to react to a service doing something
    - codepipeline state changes
- triggers to lambda fns, sqs/sns/kinesis msgs
- CW event creats a small json document to give information about the change

### CloudTrail

- Provides governance, compliance and audit for AWS (will track every API call 😱)
- Enabled by dfault
- Get a history of events / API calls made within your AWS account by:
    - Console
    - SDK
    - CLI
    - AWS Services
- Can put logs from CloudTrail into CW logs
- If a resource is deleted in AWS, look into CloudTrali First!!!!!
- 90 days of activity
- Default UI only shows `create`, `modify`, `delete` events
- CloudTrail trail
    - get a detalied list of all the events you choose
    - ability to store these events in s3
    - can be region specific or global
- SSE-S3 encrpytion when placed in s3
- Youd protect your s3 bucket

### AWS Config
- Helps w auditing and compliance of your AWS resources
- Helps record configs and change over time
- Help record compliance over time
- Possibility of storing AWS config data into s3 (can be queried by Athena)
- Questions that ca ben solved by AWS config:
    - Unrestricted SSH access to my SG?
    - Do my buckets have any public access
    - How has my ALB config changed over time
- You can receive alerts (SNS notifications) for any changes
- AWS Config is a per-region services
- can be aggregated accross regions and accounts

- Config rules
    - AWS maanged config rules (over 150)
    - can make custom config rules (must be defined in AWS lambda)
        - Evaluate if each EBS disk of type GP2
        - Evluate if each EC2 instance is t2.micro
    - Rules can be evaluated trigger:
        - for each config change
        - and/or at regular time intervals
    - Pricing
        - NO FREE TIER!
        - $2 per active rule per region per month (less after 10 rules)

### CloudWatch vs CloudTrail vs Config
- CloudWatch
    - performance monitoring & dashboards
    - Events & Alerting
    - Log aggregation & Analysis
- CloudTrail
    - Record API calls made within your acct by everyone
    - can define trails for spec resources
    - global services
- Config
    - record config changes
    - evaluate resources against compliance rules
    - get timeline of changes and compliance

