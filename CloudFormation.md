# CloudFormation

### Infrastructure as Code
- Currently, we have been doing manual work
- Touch to reproduce
    - In another region
    - In another AWS
    - Within same region if everything was deleted

- That code would be deployed and create/update/delete

### CloudFormation
- Declarative way of outlining your AWS infrastructure, for any resources
- For example with a template you say:
    - I want a SG
    - I want 2 EC2
    - I want 2 EIPS

### Benefits
- IaC
    - No resources are manually created, which is excellent for control
    - version controlled
    - reviewed through code

- Cost
    - Each resources within the stack is stagged with an identifier so you can easiliy see how much a stack costs you
    - You can estime the costs
    - Savings strategy (things can be down over night)
- Productivity
    - Ability to destroy & recreate an infra on the cloud on the fly
    - automated generation of daigram for your templates
    - Declarative programming (no need to figure out ordering and orchestration)


- Separation of concern: create many stacks for many apps and many layers eg:
    - VPC Stacks
    - Network stacks
    - APP stacks
    
- Dont reinvent the wheel 
    - Leverage existing templates ont he web
    - Leverage documentation

### What are parameters?

- Parameteres are a way to provide inputs to your AWS CF template
- Important to know abou tif
    - want to reuse your templates across company
    - some inputs cannot be determiend ahead of time

### What are resources?
- Resources are the core of your cfn template
- they represent the diff aws components
- resources are declared and can reference each other

- AWS figures out order of creation, updates and deletions for us
- over 224 types of res
- Identifiers are of the form:
    - `AWS::aws-product-name::data-type-name`

### FAQ for resources
- Can I create a dynamic amt of resources?
    - Nope, Can't perform code generation
- Is every AWS Service supported?
    - Almost. Only a select few niches are not there yet.
    - You can work around that using AWS lambda custom resources

### What are mappings?
- Fixed variables within your template
- Very handy to differnetiate between diff envs, regions, ami types..
- All the values are hardcoded within the template

- `Fn::FindInMap`
    - We use Fn::FindInMap to return a named value from a specific key
        - !FindInMap [ MapName, TopLevelKey, SecondLevelKey ]

### What are outputs?
- Optional but if we output them we can import them into other stacks
- You can also view the outputs int he AWS console or in the cli
- YOU CANNOT DELETE A STACK WHERE ITS OUTPUTS ARE BEING REFERENCED SOMEWHERE ELSE
- `Fn::ImportValue` 
    - !ImportValue NameExported

### What are conditions?
- Conditions are used to control the creation of resources or outputs based on a condition
- They can be whatever you want them to be
    - env
    - aws region
    - any param val
- Each condition can reference another condition, param value or mapping
- Example conditions:
    - Fn::And
    - Fn::Equals
    - Fn::If
    - Fn::Not
    - Fn::Or
```
Conditions:
  CreateProdResources !Equals [ !Ref EnvType, prod ]

...

Resources:
  MountPoint:
    Type: "AWS::EC2::VolumeAttachment"
    Condition: CreateProdResources
```

- Must Know Intrisic Fn
    - Fn::Ref
    - Fn::GetAtt
    - Fn::FindInMap
    - Fn::ImportValue
    - Fn::Join
    - Fn::Sub
    - Conditions Fn:

#### Important Questions
- Wait condition didnt receive the required number of signals from an amazon ec2 instance
    - Ensure that the AMI youre using has the AWS CFN helper scripts installed, if the AMI doesnt include the helper scripts, you can also download them to your instance
    - Verify that cfn-init & cfn-init command was successfully run on the instance. You can view logs such as cloud-init.log or cfn-init.log to help you debug the instance launch
    - Retrieve logs by logging into your instance but you must disable rollback on failure or AWS cfn will delete your instance after the stack fails to create
    - Verify that the instance has a connection to the internet. if the instance is in a VPC, the instance should be able to connect to the internet through a NAT device if its is in a private subnet or through an internet gateway if its in a public subnet (cant connect to service if it cant connect to the internet)

#### Nested Stacks
- Nested stacks are stacks as part of other stacks
- They allow you to isolate repeated patterns / common components in separate stacks and call them from other stacks
- Example
    - Load Balancer configuration that is re-used
    - Security Group that is reused

- Nested stacks are considered best-practice
- To update a nested stack, always update the parent (root stack)

- Termination Protection
    - Just like EC2 instances we can enable termination protection for stacks