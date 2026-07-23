## AWS EC2 Lab: Attach Two Additional Private IP Addresses
Objective
### Attach two additional private IPv4 addresses to an existing EC2 instance.

### Prerequisites
•	Running EC2 instance
•	Permission to modify EC2 networking
•	Instance has an Elastic Network Interface (ENI)
### Steps
1.	Open AWS Console → EC2 → Instances.
2.	Select the target EC2 instance.
3.	Choose Actions → Networking → Manage IP addresses.
4.	Under IPv4 addresses, click 'Assign new IP address' twice.
5.	Verify two secondary private IP addresses are added.
6.	Click Save.
7.	Optionally verify on the instance with: ip addr show ens5 or hostname -I.
### Expected Result
Example:
Primary:   10.0.0.242
Secondary: 10.0.0.243
Secondary: 10.0.0.244
### Verification
Command	Purpose
ip addr show ens5	Show all IPs on the network interface
hostname -I	Display assigned IP addresses


### Routable = An IP address that internet routers can recognize and forward traffic to.


