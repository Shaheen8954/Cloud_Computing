# ACROSS REGION VPC PERRING
REQUIRED:
( 2VPC - EC2 - VPC PEERING - ACCEPT REQUEST - RTB - SG - INTERNET GATEWAY - RTB - TEST)

## STEP-1:
### Create vpc A ( mumbai region)
 CIDR: 10.0.0.0/16
SUBNET: 10.0.1.0/24
EC2: 10.0.1.10
### VPC-B ( Singapure)
CIDR: 172.16.0.0/16
SUBNET: 172.16.1.0/24
EC2: 172.16.1.10

## STEP 2
## VPC PEERING
Click on VPC PEERING CONNECTION 
Requester: vpc A (Mumbai)
Accepter:  vpc-B (Singapure)
Accept the peering request it should be active 
If pending:
Go to vpc-B console click on action & accept request

## STEP-3
###Update Route Table for vpc-A
Create a route table for vpc B
Click on add route
Destination: vpc-B
CIDR: 172.16.0.0/16
Target: Peering connection 
Pcx-doa------
click on save

## STEP - 4

### UPDATE ROUTE TABLE FOR VPC B
Create a rtb for vpc A
add routes:
Destination: vpc a
CIDR 10.0.0.0/16
Target: perring connection
pcx- 098-----
vpc subnet association

## STEP 5
### UPDATE SECURITY GROUPS 
EC2-1-SG:
Add Inbound Rules:
Type:  All ICMP-IPV4
Source: vpc-B CIDR (172.16.0.0/16)
Description: Allow all traffic from mumbai vpc for vpc peering   
( This means all ping request comming from singapre )

EC2-2-SG:
Add Inbount Rules:
Type: All ICMP-IPV4
Source: vpc-A CIDR (10.0.0.0/16)
Description: Allow all traffic from mumbai vpc for vpc peering   


## STEP 6
Internet gateway
Route table 

## STEP 7
TESTING
