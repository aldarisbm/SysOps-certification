# Dev problems on AWS

- Manage infra
- DEploy code
- config db, lb 
- scaling concers

- most web apps have the same archi
- all the devs want is for their code to run!

# AWS Elastic Beanstalk overview

- Developer centric view of deploying an app on AWS
- Uses all components weve seen before
- All in one view
- FREE!!!

- Managed Service
    - Instance config / OS handled by beanstalk
    - deployment strategy is config but perform by Elastic Beanstalk
- Just the app code is the resp of deve

- Three architecture mods
    - Single instance deploy good for dev
    - LB + ASG great for prod or pre-prod web apps
    - ASG only great for nonwebapps

- elastbean 3 compons
    - app
    - app ver
    - env name
-  You deploy app versions to envs and can promote app vers to the next env
- Rollback feat to prev app version
- Full control over lifecycle of envs

- Support for many platf
    - GO
    - Java SE
    - Java with tomact
    - .NEt on windows server
    - Node.JS
    - PHP
    - Python
    - Ruby
    - Packer Builder

    - Single container docker
    - Multi conta docker (ECS)
    - Preconfig docker

    - If not supported you can write your custom plat

### Deployment Modes for Elastic Beanstalk

- Singles instance (GREAT FOR DEV)
    - One app, one az, one everything
- High Availability (GREAT FOR PROD)
    - ASG
    - Multi AZ

- All at once (deploy all in one go) - fastest, but instances arent avail to server traffic for a bit (downtime)
- Rolling - update few instances at a time (bucket), and then move onto the next bucket once the first bucket is healthy
- Rolling with additional batches - like rolling, but spins up new instances to move the batch
- Immutable - spins up new instances in a new ASG, deploys version to these instances and then swaps all the instances when everything is healthy

- Blue/Green Deployment - Totally new environment - not within the same environment 


### Beanstalk for SysOps
- BS can put app logs into CW logs
- You manage the app, AWS will manage the underlying infra
- Know the diff deployment modes for your app
- Route 53 alias or cname on top of beanstalk
- Youre not responsible for patching the runtimes (nodejs, php, etc)

- EC2 has a base AMI (can be confgd)
- EC2 gest the new code of the app
- EC2 resolves the app dependencies (can take a while)
- Apps get swapped on the EC2 Instance

- Resolving dependencies can take a long time
- We can Golden AMI to fix this problem

- If your app has a lot of app or OS dependencies
- Golden AMI= standardize company-specfic AMI with
    - Package OS dep
    - Pacakge App Dep
    - Package company-wide software
    
- By using a golden AMI to deploy to bs - our app wont need to resolve deps or a long time to configure

### Troubleshooting BS
- Review environment events
- Pull logs to view recent log entries
- Roll back to a previous, working version of the app

- When accessing external resources, make sure the SGs are correctly configured
- In case of command timeouts, you can increase the deployment timeout