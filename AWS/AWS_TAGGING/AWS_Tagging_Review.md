## TAGGING REVIEW
### First, let's safely inspect your current servers

Before making any changes, run this in AWS CloudShell:

```
aws ec2 describe-instances \
  --region eu-west-1 \
  --query 'Reservations[].Instances[].{InstanceId:InstanceId,Name:Tags[?Key==`Name`]|[0].Value,State:State.Name,Type:InstanceType,PrivateIP:PrivateIpAddress,PublicIP:PublicIpAddress}' \
  --output table
```

This is read-only. It will not change anything.

Then run:

```
aws ec2 describe-instances \
  --region eu-west-1 \
  --query 'Reservations[].Instances[].{InstanceId:InstanceId,Tags:Tags}' \
  --output json
```

This will show us the actual tags on every EC2 instance.
