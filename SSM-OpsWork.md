# SSM Overview

- Helps you manage ec2 and on-premise
- Get Operational insights about your infrastructure
- Easily detect problems
- Patch for enhanced compliance
- Works for both Linux and Windows
- Integrated with CloudWatch metrics / dashboards
- Integrated with AWS Config
- Free Service

#### Features

- Resource Groups
- Insights
    - Dashboard
    - Inventory: discover and audit the software installed
    - Compliance
- Parameter store
- Action
    - Automation (shutdown ec2, create AMIs)
    - Run Command
    - Session Manager
    - Patch Manager
    - Maintenance Windows
    - State Manager: define and maintaining configure for OS

#### How does it work?

- We need SSM agent in instances 
- Installed by default on Amazon Linux & Ubuntu AMI
- If an instance can't be controlled with SSM, it's probably an issue with the agent
- Make sure EC2 instances have a proper IAM role to allow SSM actions

#### AWS Tags

- Text K/V pairs
- Commonly used in EC2
- Free naming, commong tags are: name, env, team
- Used for:
    - Resource grouping
    - Automation
    - Cost allocation
- Better to have too many tags than too few

#### Resource Groups

- Create, view or manage logical group of resources thanks to tags
- Allows creation of logical groups of resources such as 
    - Apps
    - Diff layers of an app stack
    - prod vs dev envs
- Regional Service
- Works with ec2, s3, dynamodb, lambda.. etc

#### Documents
- JSON or YAML
- You define parameters
- You define actions
- Many documents already exists in AWS
- Applied to:
    - state manager
    - patch manager
    - automation
    - run command
    - parameter store

#### Run Command
- Execute a document (script) or just run a command
- Run command across multiple instances (using resource groups)
- Rate control / error control
- Integrated with IAM & CloudTrail
- No need for SSH
- Results in the console

### AWS Systems Manager to Patch
- Inventory: List Sofware on an instance
- Inventory + Run Command: Patch Software
- Patch Manager + Maintenance Windows: Patch OS
- Patch Manager: Gives you compliance
- State Manager: Ensure instances are in a consistent state (compliance)

### AWS Systems Manager Session Manager
- Session manager allows you to start a secure shell on your VM
- Does not use SSH access and bastion hosts
- Only EC2 for now (on promise coming)
- Log actions done through secure shells to s3 and cw logs
- IAM permissions: access SSM + write to s3 + write to CloudWatch
- CloudTrail can intercept startsession events
- AWS Secure shell versus SSH:
    - No need to 22
    - No need for bastion
    - All commands are logged to s3/cw

### What if I lost my SSH key

- Traditional (if EBS backed)
    - Stop instance, detach the root volume
    - Attach the root volume to another instances as a data vol
    - modify the ~/.ssh/authorized_keys file with your new key
    - Move the volume back to the stopped instance
    - Start the instance and you can SSH into it again
- New Method (if EBS backed)
    - Run the AWSSupport-ResetAccess automation document in SSM

- Instance Store backed EC2:
    - You can't stop the instance (otherwise data is lost) - AWS recommends termination
    - My tip: use SSM access and edit what we did earlier


### AWS Parameter Store
- Secure storage for configuration and secrets
- Optional Seamless encryption using KMS
- Serverless, scalable, durable, easy SDK, free
- Version tracking of configs/secrets
- Configuration management using path & IAM
- Notification with CloudWatch Events
- Integration with CloudFormation

#### AWS Parameter Store Hierarchy
- /my-dept/
    - my-app/
        - dev/
            - db-url
            - db-password
        - prod/
            - db-url
            - db-password
    - other-app/
- /other-dept/

### Opsworks overview
- Chef & Puppet help you perform server configuration automatically, or repetitive actions
- They work great with EC2 & on premise vm
- AWS Opsworks = Managed Chef & Puppet
- It's an alternative to AWS SSM
- No hands on here
- In exam: Chef & Puppet needed -> Opsworks

- Quick word on chef and puppet
    - Help with managing configuration as code
    - Helps in having consistent deployments
    - Works with Linux/Windows
    - Can automate: user accts, cront, ntp, packages, services..
    - They leverage "Recipes" or "Manifests"
    - Chef / Puppet have similarities with SSM / Beanstalk / Cloudformation but they're open-source tools that work cross-cloud










