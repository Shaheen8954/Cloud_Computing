1. EC2 + IAM Role
 Create an EC2 instance and attach an IAM role to it. Configure the role so that the EC2 instance can list and download objects from only one specific S3 bucket. Verify that access to another S3 bucket is denied.

2. Route 53 Private Hosted Zone
 Create two EC2 instances in the same VPC. Create a Route 53 Private Hosted Zone and configure a DNS record such as server1.test.internal pointing to the private IP of the first EC2 instance. From the second EC2 instance, resolve the hostname and connect to the first server using the DNS name instead of its IP.

3. S3 Versioning & Recovery
 Create an S3 bucket with versioning enabled. Upload a file, modify it, and upload it again using the same filename. Delete the file and recover the previous version. Verify that the original content is restored.

4. VPC Route Table Isolation
 Create one VPC with two private subnets and launch one EC2 instance in each subnet. Configure the route tables and Security Groups so that the servers cannot communicate with each other, while both servers remain able to communicate with required AWS services.

5. RDS Security Group Dependency
 Create two EC2 instances and an RDS PostgreSQL database. Configure the RDS Security Group so that port 5432 is accessible only from EC2-A and not from EC2-B. Test the database connection from both servers and verify the expected result.

6. S3 Bucket Policy – IP Restriction
 Create an S3 bucket and configure a bucket policy so that objects can be accessed only from a specific allowed IP address. Test access from the allowed network and from a different network and verify that the second request is denied.

7. EC2 Recovery Using AMI
 Create an EC2 instance and install a simple application on it. Create an AMI from the instance. Terminate the original instance and launch a new EC2 instance from the AMI. Verify that the application and required configuration are available on the new instance.

8. Private EC2 + Bastion + RDS
 Create a VPC with public and private subnets. Deploy:
* Bastion EC2 → Public subnet
* Application EC2 → Private subnet
* RDS PostgreSQL → Private subnet
Configure the Security Groups so that:
Bastion → Application EC2 → RDS
The Application EC2 must not have a public IP, and RDS must accept 5432 traffic only from the Application EC2.

9. Cross-Region EC2 Data Replication
 Create a Windows EC2 instance in Mumbai and another Windows EC2 instance in Hyderabad. Create a TEST directory on the Mumbai server and place test files inside it.
Configure an AWS-based solution so that the files are automatically synchronized to the Hyderabad server's TEST directory.

Hint: Think about using Amazon S3 as the intermediate storage layer rather than establishing direct server-to-server connectivity.

10. VPC Troubleshooting Scenario
 Create a VPC with public and private subnets and launch an EC2 instance in the private subnet. Configure the environment so the private EC2 should be able to access the internet.
Then intentionally introduce a networking issue by modifying one of the following:
* Route table
* NAT Gateway route
* Security Group
* Network ACL
Troubleshoot the connectivity issue from the EC2 instance and identify exactly which configuration is preventing internet connectivity.
