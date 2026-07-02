## Task:

hostname
hostnamectl
uname -a
cat /etc/os-release
uptime
whoami
id
date
timedatectl
who
w
last
useradd username
passwd username
usermod
userdel
groupadd
groupdel
groups username
chage -l username
top 
free -h
df -Th
lsblk




## Quick Comparison Table
Command	Purpose	Typical Use
### Command	              Purpose	                                                  Typical Use

```hostname	          Show temporary hostname	                                 Identify the current server
```hostnamectl        	Show or set permanent hostname                          	Rename a server
```uname -a          	Display kernel and architecture                         	Verify OS/kernel details
```cat /etc/os-release```	Show Linux distribution                           	Check OS version
```uptime	             Show uptime and load average                            	Monitor server health
```whoami```          	Show current user                                       	Verify login identity
```id```             	Show UID, GID, and groups                               	Check permissions
```date```            	Display or set system date/time                          	Verify timestamps
```timedatectl```    	Manage time and timezone	                                Configure NTP/timezone
```who````           	Show logged-in users                                     	Monitor active logins
```w```              	Show users and what they're doing	                       Monitor user activity
```last```            	Show login history                                      	Audit logins
```useradd```         	Create a user                                            	User administration
```passwd```          	Set or change password                                  	Enable or update user access
```usermod```        	Modify user properties                                  	Add to groups, lock accounts
```userdel```         	Delete a user                                           	Remove user accounts
```groupadd```       	Create a group                                          	Permission management
```groupdel```       	Delete a group                                          	Clean up unused groups
```groups```          	Show a user's groups                                     	Verify access
```chage -l```       	Display password aging                                  	Security audits
```top```            	Monitor processes in real time                           	Performance troubleshooting
```free -h```        	Show memory usage	                                        RAM analysis
```df -Th```          	Show filesystem usage                                   	Disk capacity monitoring
```lsblk```           	List block devices                                      	Verify disks and partitions

These commands form the foundation of day-to-day Linux system administration and are among the most frequently asked topics in Linux and AWS interviews.

