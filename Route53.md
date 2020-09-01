### Route 53 Section

- Managed DNS (domain name system)
- DNS is a collection of rules and records which helps clients understand how to reach a server through its domain name.
- In AWS, the most common records are:
    - A: hostname to IPv4
    - AAAA: hostname to IPv6
    - CNAME: hostname to hostname
    - Alias: hostname to AWS resource

- Route53 can use:
    - public domain names you own (or buy)
    - private domain names that can be resolved by your instances in your VPCs

- Route53 has advanced features such as:
    - Load Balancing (through DNS)
    - Health checks (limited)
    - Routing policy: simple, failover, geolocation, latency, weighted, multi value

#### DNS Records TTL (time to live)
- Cache DNS queries
    - High TTL 24hr
        - less traffic
        - possibly outdated
    - Low TTL 60s
        - more traffic
        - records are outdated for less time


#### CNAME vs Alias

- AWS resources (lb, cf) expose an AWS hostname:
    - lblb.us-east-1.elb.amazonaws.com and you want myapp.mydomain.com
- CNAME
    - Points a hostname to any other hostname (app.mydomain.com -> blabla.anything.com)
    - Only for non root domain
- Alias
    - Points a hostname to an AWS resource (app.mydomain.com -> blabl.amazonaws.com)
    - Works for root domain and non root domain
    - Free of charge
    - Native health check

#### Routing Policy
- Simple
    - basic
    - Use when you need to redirect to a single resource
    - You cant attach heatlth checks to simple routing policy
    - If multiple values are returned, a random one is chosen by the client

- Weighted
    - Control the % of the requests that go to specific endpoint
    - Helpful to test 1% of traffic on new app version for example
    - Helpful to split traffic between two regions
    - Can be associated w health checks

- Latency
    - Redirect to the server that has the least latency close to us
    - super helpful when latency of users is a priority
    - Evaluated in terms of user to designated AWS region
    - Germany may be directed to the US (if thats the lowest latency)

- Health Checks
    - have x health checks failed -> unhealthy (default 3)
    - after x health checks passed -> healthy (default 3)
    - Default health check interval: 30s (can set to 10s - higher cost)
    - about 15 health checkers will check the endpoint health
    - one request every 2 seconds on average
    - Can have HTTP, TCP and HTTPS health checks (no ssl verif)
    - possibility to integrating the health check w CW
    - Health checks can be linked to Route53 DNS queries

- Failover
    - Primary and Secondary

- Geo Location Routing Policy
    - Diff from latency based
    - this is routing based on user location
    - Here we specify: traffic from the UK should go to this spec ip
    - Should create a default policy (in case theres no match)

- Multi Value
    - Use when routing traffic to mult resources
    - Want to associate a route53 health chceks w records
    - up to 8 health records are returned for each Multi Value query
    - Multi Value is not a substitute for having an ELB

#### R53 is a registar
- A domain name register is an org that manages the reserv of internet domain names
- Famous names
    - Godaddy
    - Google Domains
    - etc...
- and Also... Route53!

- Domain Registrar != DNS
