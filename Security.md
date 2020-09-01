## Security

### AWS Shared Resp Model

#### AWS Resp - Security of the cloud
- Protecting infra (hard/software facilities and networking) that runs all of the AWS services
- Managed services liek s3, dynamodb, rds, etc

#### Customer Resp - Security in the cloud
- For EC2 instance, customer is responsible for management of the guest OS (including security patches)
- Firewall
- Network config
- IAM
- etc...


#### S3 Example
- AWS
    - Unlimited storage
    - Encrpytion
    - Separation of the data between diff cus
    - Ensure AWS employeees cant access your data
- Customer
    - Bucket Config
    - Bucket policy / Public settings
    - IAM users and roles
    - Enabling encryption

#### DDOS Attack

- DDOS Protection on AWS
    - AWS Shield Standard: protects against DDOS attack for your website and apps (free)
    - AWS Shield Advanced: 24/7 premium DDoS protection
    - AWS WAF: Filter specific requests based on rules
    - CloudFront and Route 53:
        - Avail protection using global edge network
        - Combined with AWS Shield, provides attack mitigation at the edge
    - Be Ready to scale - leverage AWS Auto Scaling
    - Separate static resources (s3/cloudfront) from dynamic ones (ec2/alb)


#### AWS Shield
- Optional DDoS mitigation services (3k per month per org)
- AWS Shield Standard
    - Free service that is activated for every aws account
    - Provides protection from attacks such as SYN/UDP floods, reflection attacks and other layter 3/layer 4 attacks
- AWS Shield Advanced
    - Protect against more sophisticated attack on cloudfront, route53, classic, application & network load balancer (select regions), elastic IP / EC2
    - 24/7 access to AWS DDoS response team (DRP)
    - Protect against higher fees during usage spieks due to DDoS

#### AWS WAF
- Protect web app from common web exploits
- Define customizeable web sec rules
    - control which traffic to allow or block to your web apps
    - rules can include: IP address, http headers, http body or URI strings
    - Protects from common attack - SQL injection and XSS
    - Protect against bots, bad user agents, etc
    - size constraints
    - Geo Match
- Deploy on CloudFront, app, load balancer or API Gateway
- Leverage existing marketplace of rules

#### AWS Inspector
- Only for EC2 Instances
- Analyze against known vulnerabilities
- Analyze against unintended network accessibility
- AWS Inspect agent must be installed on OS in EC2 instances
- Define template (rules package, duration, attributes, SNS topics)
- No custom rules (only use AWS managed rules)
- After the assesment, you'll get a list of rules

- For network assesments:
    - network reachability
- For host assesments:
    - common vuln n exposures
    - center for internet security benchmarks
    - security best practices
    - runtime behaviour analysis

#### Logging in AWS for sec and compliance

- Service Logs
    - CloudTrail trails - trace all api calls
    - Config Rules - For config & compliance over time
    - Cloudwatch Logs - for full data retention
    - VPC Flow Logs - IP Traffic within your vpc
    - ELB Access Logs - metadata of requests made to your load balancers
    - Cloudfront Logs - web distribution access logs
    - WAF Logs - Full logging of all requests analyzed by the service
    
- Logs can be analyzed using AWS Athena if they're stored in S3
- You should encrypt logs in s3, control access using IAM & bucket policies, MFA
- Move logs to glacier for cost savings or vault

#### GuardDuty
- intelligent threat discovery to protect AWS Account
- Uses Machine Leanring algorithms, anomaly detection, 3rd party data
- One click to enable (30 days trial), no need to instlal software

- input data includes:
    - CloudTrail logs - unusual api calls, unauthorized deploymnts
    - VPC Flow Logs - unusual internal traffic, unusual IP add
    - DNS Logs - compromised ec2 instances sending encoded data within DNS queries

- Notifies you in case of findings
- Integration with AWS Lambda

#### Trusted Advisor
- No need to install anything - high level AWS account assesment
- Analyze your AWS accounts and provide recommendation
    - cost opt
    - performance
    - security
    - fault tolerance
    - service limits
- Core checks and recommendations - all customers
- Can enable weekly email nots from the console
- full trusted advisor - avail for business and enterprise support plans

#### Encryption
- Encryption In Flight (SSL)
    - Data is encrypted before sending and decrypted after receiving
    - SSL certs help w encryption (HTTPS)
    - ensures no MITM (man in the middle attack) can happen

- Encryption At Rest
    - Server side
        - Data is encrypted after being received by the server
        - Data is decrypted before being sent
        - It is stored in an encrypted form thanks to a key (usually a data key)
        - The encryption/decryption keys must be managed somewhere and the server must have access to it
    - Client side
        - Data is encrypted by the client and never decrypted by the server
        - Data will be decrypted by a receiving client
        - The server should not be able to decrypt the data
        - Could leverage envelope encryption

#### KMS

- Anytime you hear encryption think KMS
- Easy way to control access to your data
- Fully integrated with IAm for auth
- Seamlessly integrated into multiple services

- Value in KMS is that CMK cannot be retrieved by the user
- Never store secrets in plaintext
- Encrypted sercrets can be stored in the code / env vars (eh)
- KMS can only help in encrpyting up to 4KB of data per call
- Access to a KMS
    - KMS policy allows user
    - IAM allows KMS usage
- Able to fully manage the keys & policies
    - Create
    - Rotation policies
    - Disable
    - enable

- Able to audit key usage (cloudtrail)

- Three types of Customer Master Keys (CMK):
    - AWS Managed service default CMK: free
    - User Keys Created in KMS: $1 / month
    - User Keys imported (must be 256-bit symmetric key): $1 / month
- Pay for API call to KMS -> 0.03 / 10000

#### How does KMS work?
- API - Encrypt & Decrypt
- Encryption in AWS Services
    - Requires Migration (through snapshot/backup)
        - EBS Volumes
        - RDS DBs
        - Elasticache
        - EFS network file system
    
- In-place encryption
    - S3

#### Cloud HSM
- KMS -> AWS manages software for enc
- CloudHSM -> AWS provisions encryption hardware
- Dedicated Hardware (HMS = hardware security module)
- You manage your own encryption keys entirely (not AWS)
- the CloudHSM hardware is tamper resistant
- FIPS 140-2 Level 3 compliance
- CloudHSM clusters are spread across multi AZ (HA)
- Supports both symmetric and asymmetric encr (SSL/TLS Keys)
- No free tier available
- Must ues the CloudHSM client software

#### MFA + IAM 

- MFA adds a level of sec while accessing your AWS acct
- AWS MFA accepts both virtual and hardware MFA devices
- Virtual MFA devices (google auth / authy)
- MFA for root user can be configured from IAM dashboard
- MFA can also be configured from CLI
- You can setup MFA for ind users
- Cred Report
    - CSV report file on all the IAM users n creds
    - This shows who all have enabled MFA

#### IAM PassRole option
- When you create an ec2 instace you assign a role to it
- In oder to assign a role to an ec2 instance, you need `IAM:PassRole`

```
{
    "Version": "2012-10-17",
    "Statement: [
        {
            "Sid": "AdminRightsEc2Service",
            "Action": "ec2:*",
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Sid": "PassRole",
            "Action": [
                "iam:PassRole",
            ],
            "Effect": "Allow"
            "Resource": "arn:aws:iam::$acct-id:role/Sample-EC2-Role"
        }
    ]
}
```

#### AWS STS 
- Allows to grant limited and temporary access to AWS resources. 
- Token is valid for up to one hour (must be refreshed)
- Cross Acct access
    - Allows users from one AWS acct access resource in another acct
- Federation (AD)
    - Provides a non-AWS user with temp AWS access by linking users active directory creds
    - Uses SAML (security assertion markup language)
    - Allows SSO which enables users to log in to AWS console without assigning IAM creds
- Federation with third party providers (cognito)
    - Used mainly in web and mob apps
    - makes use of facebook/google/amazon etc to federate them

- Cross Account Access
    - Define an IAM role for another acct to access
    - Define which accts can access this IAM role
    - Use aws sts to retrieve creds and impersonate the IAM role you have access to (AssumeRole API)
    - Temporary creds can be valid between 15 mins to 1 hr


#### Identity Federation
- Federation let's users outside of AWS to assume temporary role for accessing AWS resources
- These users assume identity provided access role.
- Federation assumes a form of 3rd party
    - LDAP
    - Microsoft AD (~= SAML)
    - Single Sign On
    - Open ID
    - Cognito
- Using federation you dont need to create IAM users (user management outside of AWS)

- SAML Federation
    - For enterprises
    - TO integrate AD / ADFS w AWS (or any SAML 2.0)
    - Provides access to AWS console or CLI (through temporary creds)
    - No need to create an IAM users for each of your employees

- Custom Identity Broker App (non SAML 2.0)
    - For enterprises
    - Program an identity broker 
    - Must be used to determine the appropiate IAM policy

- AWS Cognito - Federated Identity Pools
    - For Public Applications
        - Goal
            - provide direct access to aws resources from client side
        - How
            - Login to federated identiy provi or remain anon
            - get temp aws creds back from federated identity pool
            - These creds come with a pre-defined IAM policy stating their permissions
        - Example:
            - provide access to wirte to s3 bucket using fb login

#### AWS Compliance frameworks
- AWS being compliant doesnt mean youre automatically compliant
- You need to get audited as well
- Remember Security of the cloud vs Security in the cloud

#### AWS Artifact
- (not really a service)
- On demand access to AWS compliance documentation and AWS agreements
- Artifact Reports - Allows you to dl aws sec and compli documents like AWS ISO certs, PCI...
- Artifact Agreements - Allows you to review, accept, and track the status of AWS agreements such as the business associate addendum (BAA)

- Can be used to support internal audit and complicance

### Sect Summary
- AWS Shield: Automatic DDoS protec + 24/7 support for advanced
- AWS WAF: Firewall to filter incoming requests based on rules
- AWS Inspector: For EC2 only, install agent and find vulnerabilities
- AWS GuardDuty: Find malicious behaviour with VPC, DNS & CloudTrai Logs
- AWS Trusted Advisor: Analyze AWS account and get recommendations
- AWS KMS: Encryption keys managed by AWS
- AWS CloudHSM: Hardware encryption - we manage keys
- AWS STS: generate security token
- Identity federation: SAML 2.0 or custom for enterprise, cognito for apps
- AWS Artifact: Get Acecss to compliance reports such as PCI, ISO, ETC...
- AWS Config: Track config changes and compliance against rules
- AWS CloudTrail: track API calls made by users within your acct
