# Project Requirements

Design a solution for a multi-tier web application to meet the following requirements:

1) The application will be launched in a VPC with CIDR block `10.0.0.0/16` with:
   - 2 Public subnets in two different AZs: `10.0.10.0/24` and `10.0.20.0/24`.
   - 2 Private Subnets in the same AZs: `10.0.100.0/24` and `10.0.200.0/24`.

2) Design the VPC security to ensure access control at Layer 4 at the subnet and compute levels.

3) Launch two EBS-backed EC2 instances, one in each of the two AZs above; these will serve as the web and application tiers.

4) Administrators and developers will need programmatic access to AWS services. Also, the application on EC2 instances will require access to AWS services.

5) The Domain name will be registered with AWS.

6) The application will have users across the globe. Ensure that the solution has a way of ensuring good performance for users in remote locations too.

7) Launch an RDS database in the above VPC. Ensure failover to another AZ in case of a failure of the primary RDS instance.

8) As the traffic increases, the solution must have a component that decouples the web/app tier from the database tier to avoid overwhelming the database.

9) Ensure that the data is encrypted as it is stored.

10) Ensure that the web/app tier is highly available across the two availability zones. The load should be distributed evenly across the web/app instances.

11) The solution must be connected to the corporate Datacenter with two connections, primary with low latency and a secure internet-based backup.