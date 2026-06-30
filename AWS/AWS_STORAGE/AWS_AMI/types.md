Types of AMIs (Amazon Machine Images)

There are three main types of AMIs.

1. Public AMI
Concept

These AMIs are available to everyone in AWS.

They are usually provided by AWS or the AWS community.

Who Creates Them?
AWS
Open-source communities
Individuals
Examples
Amazon Linux
Ubuntu
Debian
CentOS
Windows Server
When to Use
Learning AWS
Testing
Development
Starting a new project
Example

You want an Ubuntu server.

Instead of installing Ubuntu manually, select the Ubuntu Public AMI and launch your EC2 instance.

2. Private AMI
Concept

A Private AMI is created by you and is available only within your AWS account (or shared with specific accounts if you choose).

Who Creates It?

Your organization or you.

When to Use
Production environments
Company-standard server images
Internal applications
Example

Your company installs:

Ubuntu
Docker
Java
Nginx
Monitoring Agent

Create a Private AMI.

Now every developer launches servers with the same configuration.

3. AWS Marketplace AMI
Concept

These AMIs are provided by software vendors through the AWS Marketplace.

They come with software already installed.

Examples
WordPress
Jenkins
MongoDB
SQL Server
Red Hat Enterprise Linux
Palo Alto Firewall
When to Use

When you need commercial or preconfigured software without installing it yourself.

Example

Need a Jenkins server?

Instead of:

Launch Ubuntu
Install Java
Install Jenkins

Launch the Jenkins Marketplace AMI, and it's ready to use.

AMI Comparison
Type	Visibility	Created By	Best For
Public AMI	Everyone	AWS/Community	Learning, Testing
Private AMI	Your account	You/Organization	Production
Marketplace AMI	Marketplace customers	Software Vendors	Preconfigured software
