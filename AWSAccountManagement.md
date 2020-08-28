### AWS Status 

#### Service Health Dashboard

- Shows all regions, all services health
- Shows historical information each day
- has an RSS Feed

#### Personal Health Dashboard
- Global service
- Show how AWS outages directly impact you
- Shows impact on your resource
- List issues and actions you can do to remediate them

### AWS Organizations

- Global Service
- Allows to manage multiple aws accts
- the main acct is the master acct - you cant change it
- other accts are member accounts
- members accts can only be part of one org
- consolidated billing across all accts - single payment method
- pricing benefits from aggregated usage (vol discount for ec2, s3)
- API is available to automate AWS account creation

#### Multi Account Strategies
- Create accounts per dept, per cost conter, per dev/test/prod, on reg restrictions (uscing SCP)
- for better resource isolation
- to have separate per-acct service limits, isolated account for logging

- Multi-Account vs One Account Multi VPC
- Use tagging standards for billing purposes
- Enable CloudTrail on all accounts, send logs to cnetral s3 account
- Send CloudWatch logs to central loggin acct
- Establish cross acct roles for admin purposes

- Organizational Units (OU)

#### Service Control Polices (SCP)
- Whitelist or blacklist IAM actions
- Applied at the OU or Acct level
- Does not apply to the Master Account
- SCP is applied to all the users and roles of the account, including root
- The SCP does not affect service-linked roles
    - Service-linked roles enable other AWS services to integrate with AWS organizations and can't be restricted by SCPs
- SCP must have an explicit Allow (does not allow anything by default)
- Use cases:
    - Restrict access to certain services (eg: cant use EMR)
    - Enforce PCI compliance by explicitly disabling services
 
#### AWS Service Catalog

- Users that are new to AWS have too many options, and may create stacks that are not compliant / in line w rest of org
- Some users want a quick self-service portal to launch a set of authorized produccts pre-defined by admins
- VMs, DBs, Storage options, etc...
- Create and manage catalogs of IT services that are approved on AWS
- the "products" are cloudformation templates
- VMs images, servers, software, dbs,r egions, ip address ranges
- CloudFormation helps ensure consistency, and standardization by Admins
- They are assigned to Portfolios (teams)
- Teams are presented a seflservice protal where they can launch the prodccs
- All the deployed products are centrally managed deployed services
- Helps w governance, compliance and consistency
- Can give user access to launching products without requiring deep AWS knowledge
- Integrations with self-service portal such as servicenow

#### AWS Billing Alarms
- Billing data metric is stored in CloudWatch us-east-1
- Billing data are for overall worldwide AWS costs
- Its for actual cost, not for project costs 

#### AWS Cost Explorer
- A graphical tool to view and analyze your costs and usage
- Review charges and cost associated wth your AWS account or org
- Forecast spending for the next 3 months
- Get recommendations for which EC2 Reserved Instances to purchase
- Access to default reports
- API to build cost management applications

#### AWS Budgets
- Create Budget and send alarms when costs exceeds the budget

- 3 Types of budget, usage, cost, reservation
- For RI
    - track utilization
    - Support EC2, Elasticache, RDS, Redshift

_ Up to 5 SNS nots per budget
- Can filter by: Service, Linked Acccount, tag, purchase opti, instace type, region, AZ, API Operation, etc...
- Same options as AWS Cost Explorer
- 2 budgets are free, then 0.02/day/budget

#### AWS Cost Allocation Tags
- track resources that relate to each other
- W cost allocation tags we can enable detailed costing reports
- just like tags, but they show up as columns in reports
- AWS generated cost allocation tags
    - automatically applied to the resource you create
    - Starts with prefix `aws:` 
    - They are not applied to resources created before the activation
- User tags
    - defined by the user
    - starts with prefix user:
- Cost allocation tags just appear in the billing console
- Takes up to 24 hrs to show up on the console