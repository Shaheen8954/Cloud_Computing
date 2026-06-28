
Hub-and-Spoke Practice Lab


Objective
Create a network where one central VPC (Hub) connects to multiple Spoke VPCs.

Topology
                    Internet
                        |
                  Bastion Host
                        |
                  Hub VPC (10.0.0.0/16)
                 Transit Gateway
                 /              \
                /                \
      Spoke VPC-1           Spoke VPC-2
      10.1.0.0/16          10.2.0.0/16
Your Tasks
Task 1
Create 3 VPCs:

Hub VPC → 10.0.0.0/16

Spoke-1 → 10.1.0.0/16

Spoke-2 → 10.2.0.0/16

Task 2
Each VPC should contain:

1 Public subnet

1 Private subnet

Task 3
Launch one Ubuntu EC2 instance in each private subnet:

hub-server

app-server-1

app-server-2

Task 4
Create one AWS Transit Gateway.

Task 5
Attach all three VPCs to the Transit Gateway.

Task 6
Update route tables so that:

Hub can reach both spokes.

Spoke-1 can reach Hub.

Spoke-2 can reach Hub.

Spoke-1 can also reach Spoke-2 through the Hub/Transit Gateway.

Task 7
Create one Bastion Host in the Hub VPC public subnet.

SSH flow:

Laptop
   ↓
Bastion
   ↓
Hub Server
   ↓
Spoke-1 Server
   ↓
Spoke-2 Server
Task 8
Verify connectivity.

Test:

ping <Hub Server>

ping <Spoke-1 Server>

ping <Spoke-2 Server>
Also test:

ssh ubuntu@Hub

ssh ubuntu@Spoke1

ssh ubuntu@Spoke2
Task 9
Document the following:

VPC CIDRs

Subnets

Transit Gateway attachments

Route tables

Security Groups

Connectivity tests

Ping results

Bonus Challenge
Create a fourth VPC:

Spoke-3
10.3.0.0/16
Without changing the existing VPCs, connect it to the Transit Gateway and verify that it can communicate with the other spokes. This demonstrates how the hub-and-spoke architecture scales by adding new spokes without creating multiple VPC peering connections.

This lab closely resembles how many organizations build centralized AWS networks using a Transit Gateway as the hub. It also gives you practice with VPCs, subnets, route tables, security groups, EC2 instances, and connectivity troubleshooting—all valuable skills for DevOps and cloud engineering interviews.

Deep research
 

