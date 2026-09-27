1. Create an EC2 Linux instance and attach an additional 10 GB EBS volume. Format and mount the volume on /data. Create a test file inside /data and verify that the data persists after an EC2 reboot.
2. Create an S3 bucket and enable versioning. Upload a text file, modify the file and upload it again with the same name. Delete the file and then restore the previous version using S3 versioning.
3. Create an EC2 instance and assign an IAM Role to it. Configure the role so that the EC2 instance can read objects from only one specific S3 bucket and cannot access any other S3 bucket.

4. Create two EC2 instances in the same VPC but different subnets. Configure the Security Groups so that Server A can connect to Server B on TCP port 8080, while Server B should not be able to connect to Server A on port 8080.
Hint: Use Security Groups and a simple web service such as Python HTTP Server or Nginx.
5. Create an Application Load Balancer with two EC2 instances as targets. Host the same website on both servers. Configure the ALB health check and then stop the web service on one server. Verify that the ALB automatically stops sending traffic to the unhealthy server.
6. Create a private Ubuntu EC2 instance with no public IP. The instance should be able to download packages from the internet using a NAT Gateway. Then configure the required routing so that the private server can access the internet while remaining inaccessible directly from the internet.

7. Create two VPCs in different AWS regions. Establish private connectivity between the VPCs using VPC Peering. Create an EC2 instance in each VPC and configure the routing and Security Groups so that Server A can connect to Server B on TCP port 3000, and Server B can connect to Server A on TCP port 4000.
Hint: Do not use public IPs for the connectivity test.

8. Create an Auto Scaling Group with a minimum of 2 and maximum of 4 EC2 instances behind an ALB. Configure the ASG to scale out when average CPU utilization exceeds 60% and scale in when it falls below 30%.
Generate CPU load on the instances and verify that:
* A new instance is automatically launched when the threshold is breached.
* The new instance is automatically registered with the ALB.
* Traffic is distributed across the available healthy instances.
* Instances are automatically terminated when the load decreases.
