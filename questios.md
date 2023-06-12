### How have I achieved cost effectiveness?
- I was able to scale down the number of virtual machines that I was using to eliminate unnesesary running costs.
- I was also able to scale up when needed, which allowed my app to accomodate more users, and hence higher revenues.
- Also, the fct that I was using AWS was very important because as it provides pay-as-you-go, meaning I only needed to pay for the resources I was using.

### How did I adapt to changes in user usage?
- When the amount of usage changed, I was able to either scale up or down based on the current demand. 
- This allowed me to constantly have an optomised number of virtual machines running at any time automatically. 
- The load balancer allowed for adaptatio to changes, as it was constantly splitting the load equally amongst each virtual machine.
- Overall, this allowed me to provide a consistent and reliable user expirience, with better cost optomisation and an optomised use of my resources.

### How did I eliminate a single point of failure?
- The load balancer distributed the load equally, meaning that one of the instances was less likely to have a larger load, and hence removed single points of failure.
- I was also able to enable health checks. This monitored the instances, and would automatically replace them if they were failing.
- Availability zones were used to eliminate a single point of failure in terms of the AWS hardware.
