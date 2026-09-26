## Create the shell script

In CloudShell:
```
vim ec2_inventory.sh
```
Paste this:
```
#!/bin/bash

REGION="eu-west-1"
OUTPUT_FILE="ec2_inventory.csv"

echo "InstanceName,InstanceID,PrivateIP,PublicIP,State,InstanceType,Tags" > "$OUTPUT_FILE"

aws ec2 describe-instances \
    --region "$REGION" \
    --query 'Reservations[].Instances[]' \
    --output json |
jq -r '
    .[] |
    [
        (
            [.Tags[]? | select(.Key=="Name") | .Value] | first // "N/A"
        ),
        .InstanceId,
        (.PrivateIpAddress // "N/A"),
        (.PublicIpAddress // "N/A"),
        .State.Name,
        .InstanceType,
        (
            [.Tags[]? | "\(.Key)=\(.Value)"] |
            join("; ")
        )
    ] |
    @csv
' >> "$OUTPUT_FILE"

echo "EC2 inventory generated successfully."
echo "File: $OUTPUT_FILE"
```


### Make the script executable

After saving the file:
```
chmod +x ec2_inventory.sh
```
Then run:
```
./ec2_inventory.sh
```
You should see:
```
EC2 inventory generated successfully.
File: ec2_inventory.csv
15. Check the generated file
```
Run:
```
ls -lh ec2_inventory.csv
```
Then:
```
cat ec2_inventory.csv
```
You should get something similar to:
```
InstanceName,InstanceID,PrivateIP,PublicIP,State,InstanceType,Tags
pub-server-vpc2,i-0daf0a679b48ce2af,10.10.1.136,34.244.212.210,running,t3.micro,"Name=pub-server-vpc2; Environment=Dev"
pub-server-vpc-1,i-06ba97349de03586c,10.0.1.83,34.247.71.96,running,t3.micro,"Name=pub-server-vpc-1; Environment=Dev"
pub-server-vpc3,i-012f12f529de07727,20.10.1.246,54.229.34.103,running,t3.micro,"Name=pub-server-vpc3; Environment=Dev"
```
The actual tags will depend on what's configured on your instances.



### How do we get this into Excel?

You have two options.

Option 1 — Recommended: Download the CSV

CloudShell supports downloading files.

First confirm:
```
ls -lh ec2_inventory.csv
```
Then use the CloudShell Actions → Download file option and enter:
```
ec2_inventory.csv
```
Download it to your computer.

Then open it with:

Microsoft Excel

Excel will automatically put the values into columns.
