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





