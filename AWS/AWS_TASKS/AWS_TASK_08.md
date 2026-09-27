1. Create a Private Server and host a website on the server. Put the server behind the ALB and internet connective should be pass through load balancer only.
 
2. Create a one Public and one Private server in VPC1. Create One Public and one Private server in VPC2. Create a peering connection between both the VPC so that private servers of both VPC can communicate with each other.
 
3. Created Servers under VPC1>>> Connect the Private server through public server
 
4. Create open VPN in a VPC but you need to connect to private machines of other VPC using this openvpn client.
5. create two VPC in different regions with your name and in those VPC create two private subnet 1 public subnet launch a machine in any of the subnet and create private connectivity. (you should be able to reach the other machine on private IP).
6. Create an IAM user with having s3 only access also show cross region s3 replication.
 
