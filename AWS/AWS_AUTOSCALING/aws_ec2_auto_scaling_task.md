# AWS EC2 Auto Scaling with ALB, Target Group, CloudWatch Alarms, and Dynamic Scaling Policies

## 1. Objective

The objective of this task was to configure an AWS EC2 Auto Scaling
environment where:

-   An Application Load Balancer (ALB) distributes traffic to EC2
    instances.
-   A Target Group contains the EC2 instances used by the ALB.
-   An Auto Scaling Group (ASG) manages the EC2 instances.
-   The ASG maintains a minimum of 2 instances.
-   The ASG can scale out when CPU utilization becomes high.
-   The ASG can scale in when CPU utilization becomes low.
-   CloudWatch alarms trigger the scaling policies.
-   The new ASG instances automatically become targets of the Target
    Group.
-   The complete scale-out and scale-in behavior is tested.

------------------------------------------------------------------------

# 2. Final Architecture

``` text
                         Internet
                            |
                            v
                    +----------------+
                    |      ALB       |
                    |    test-alb    |
                    +--------+-------+
                             |
                             v
                    +----------------+
                    |  Target Group  |
                    |    test-trg    |
                    |    HTTP : 80   |
                    +--------+-------+
                             |
             +---------------+---------------+
             |               |               |
             v               v               v
        +---------+     +---------+     +---------+
        |  EC2    |     |  EC2    |     |  EC2    |
        | Healthy |     | Healthy |     | Healthy |
        +---------+     +---------+     +---------+
             \               |               /
              \              |              /
               +-------------+--------------+
                             |
                             v
                    +----------------+
                    | Auto Scaling   |
                    |     Group      |
                    |      asg       |
                    +----------------+
                       Min = 2
                       Desired = 2/3
                       Max = 4

                    CloudWatch
                       |
          +------------+------------+
          |                         |
          v                         v
    CPU >= 60%                 CPU < 30%
          |                         |
          v                         v
     Scale Out                  Scale In
       +1                          -1
```

------------------------------------------------------------------------

# 3. Resources Used

  Resource                    Name / Configuration
  --------------------------- ------------------------------------
  Application Load Balancer   `test-alb`
  Target Group                `test-trg`
  Target Group Protocol       HTTP
  Target Group Port           80
  Auto Scaling Group          `asg`
  Launch Template             `asg-tmp`
  Instance Type               `t3.micro`
  Minimum Capacity            2
  Desired Capacity            2
  Maximum Capacity            4
  Scale-Out Alarm             `asg-scale-out-cpu-60`
  Scale-In Alarm              `asg-scale-in-cpu-30`
  Scale-Out Threshold         CPU \> 60%
  Scale-In Threshold          CPU \< 30%
  Scale-Out Action            Add 1 instance
  Scale-In Action             Remove 1 instance
  CPU Evaluation Period       1 minute
  Instance Warmup             60 seconds for step scaling policy

------------------------------------------------------------------------

# 4. Prerequisites

Before starting, the following should already be available:

-   AWS account
-   VPC
-   Subnet(s)
-   Internet connectivity as required
-   Security Group
-   EC2 instances
-   SSH key pair if SSH is required
-   Nginx or another HTTP service listening on port 80
-   Application Load Balancer
-   Target Group
-   Launch Template

The EC2 instances used by the Target Group must be able to respond
successfully on HTTP port 80.

------------------------------------------------------------------------

# 5. Verify the Original Target Group

The existing Target Group was:

``` text
Target Group: test-trg
Protocol: HTTP
Port: 80
Target Type: Instance
Load Balancer: test-alb
```

Initially, the Target Group contained 4 instances.

Two of them were old/manual instances that had been registered before
Auto Scaling was configured.

The other two instances were managed by the Auto Scaling Group.

The old/manual instances were removed so that the Target Group contained
only the ASG-managed instances.

## Final Target Group Before Testing

The Target Group contained:

``` text
i-01e48454b85de1ac9
i-0adfb7923ab555d05
```

Both were:

``` text
Port: 80
Health status: Healthy
```

The Target Group showed:

``` text
Total targets: 2
Healthy: 2
Unhealthy: 0
```

This is the correct starting state for testing Auto Scaling.

------------------------------------------------------------------------

# 6. Create / Configure the Auto Scaling Group

Go to:

``` text
AWS Console
    -> EC2
    -> Auto Scaling Groups
```

Create or open the Auto Scaling Group.

The Auto Scaling Group used in this task was:

``` text
Name: asg
```

The launch template was:

``` text
asg-tmp
```

The instances were launched as:

``` text
Instance type: t3.micro
```

------------------------------------------------------------------------

# 7. Configure ASG Capacity

The ASG capacity was configured as:

``` text
Minimum capacity = 2
Desired capacity = 2
Maximum capacity = 4
```

### Meaning

### Minimum = 2

The ASG should not normally go below 2 instances.

``` text
Minimum = 2
```

Even if CPU utilization becomes very low, Auto Scaling should not reduce
the group below this minimum.

### Desired = 2

The initial desired number of instances is:

``` text
Desired = 2
```

Therefore the ASG starts with 2 instances.

### Maximum = 4

The ASG can scale out up to:

``` text
Maximum = 4
```

This is important because the scale-out policy needs permission to
increase the number of instances above 2.

------------------------------------------------------------------------

# 8. Verify ASG Instances

Go to:

``` text
EC2
    -> Auto Scaling Groups
    -> asg
    -> Instance management
```

The initial state was:

``` text
Instances = 2
```

Both instances showed:

``` text
Lifecycle = InService
Health status = Healthy
```

The launch template was:

``` text
asg-tmp
```

These two instances were also registered in the Target Group.

------------------------------------------------------------------------

# 9. Integrate ASG with the Target Group

Go to:

``` text
EC2
    -> Auto Scaling Groups
    -> asg
    -> Integrations
```

Under Load balancing, the Target Group was:

``` text
test-trg
```

This integration is important.

When the ASG launches a new EC2 instance, AWS automatically registers
that instance with the Target Group.

When the ASG terminates an instance, AWS removes it from the Target
Group.

Therefore, the Target Group should not be manually modified every time
the ASG scales.

------------------------------------------------------------------------

# 10. Verify Target Group Health

Go to:

``` text
EC2
    -> Target Groups
    -> test-trg
    -> Targets
```

The initial target count should be:

``` text
Total targets = 2
Healthy = 2
Unhealthy = 0
```

Both targets should be listening on:

``` text
HTTP : 80
```

The health check must pass before the instance is considered healthy by
the load balancer.

------------------------------------------------------------------------

# 11. Create the Scale-Out CloudWatch Alarm

The first alarm was created to detect high CPU utilization.

Go to:

``` text
CloudWatch
    -> Alarms
    -> Create alarm
```

Select:

``` text
Data source: Metrics
Metric type: Classic
```

Select the EC2 Auto Scaling metric.

The metric was:

``` text
Namespace: AWS/EC2
Metric: CPUUtilization
AutoScalingGroupName: asg
Statistic: Average
```

Configure:

``` text
Period = 1 minute
```

------------------------------------------------------------------------

# 12. Scale-Out Alarm Condition

Configure the threshold as:

``` text
Threshold type: Static
Condition: Greater
Threshold: 60
```

The required condition is:

``` text
CPUUtilization > 60%
```

Configure the alarm evaluation as:

``` text
Datapoints to alarm = 1
Datapoints evaluated = 1
```

Therefore:

``` text
1 out of 1 datapoints
```

If CPU utilization is greater than 60% for the evaluation period, the
alarm enters the ALARM state.

The alarm was named:

``` text
asg-scale-out-cpu-60
```

------------------------------------------------------------------------

# 13. Scale-Out Alarm Notification

The alarm notification section was left without an SNS notification
because the purpose of this task was Auto Scaling.

The important action was the Auto Scaling policy.

An SNS notification can be added separately if email or other
notifications are required.

------------------------------------------------------------------------

# 14. Create the Scale-Out Dynamic Scaling Policy

Go to:

``` text
EC2
    -> Auto Scaling Groups
    -> asg
    -> Automatic scaling
    -> Create dynamic scaling policy
```

Select:

``` text
Policy type: Step scaling
```

Policy name:

``` text
scale-out-policy-60
```

Select the CloudWatch alarm:

``` text
asg-scale-out-cpu-60
```

Configure the action:

``` text
Action: Add
Amount: 1
Unit: capacity units
```

Configure the step so that:

``` text
60 <= CPUUtilization < +infinity
```

Therefore:

``` text
CPU >= 60%
        |
        v
Add 1 instance
```

The instance warmup was configured as:

``` text
60 seconds
```

------------------------------------------------------------------------

# 15. Verify the Scale-Out Policy

After creation, the ASG should show a dynamic scaling policy similar to:

``` text
Policy name:
scale-out-policy-60

Policy type:
Step scaling

Condition:
CPUUtilization >= 60%

Action:
Add 1 capacity unit

Warmup:
60 seconds
```

The CloudWatch alarm should be connected to this policy.

------------------------------------------------------------------------

# 16. Create the Scale-In CloudWatch Alarm

A second CloudWatch alarm was created for low CPU utilization.

Alarm name:

``` text
asg-scale-in-cpu-30
```

Metric:

``` text
AWS/EC2
CPUUtilization
AutoScalingGroupName = asg
Statistic = Average
```

Period:

``` text
1 minute
```

Condition:

``` text
CPUUtilization < 30%
```

Configure:

``` text
Datapoints to alarm = 1
Datapoints evaluated = 1
```

Therefore:

``` text
1 out of 1 datapoints
```

------------------------------------------------------------------------

# 17. Create the Scale-In Dynamic Scaling Policy

Go to:

``` text
EC2
    -> Auto Scaling Groups
    -> asg
    -> Automatic scaling
    -> Create dynamic scaling policy
```

Select:

``` text
Policy type: Step scaling
```

Policy name:

``` text
scale-in-policy-30
```

Select the CloudWatch alarm:

``` text
asg-scale-in-cpu-30
```

Configure:

``` text
Action: Remove
Amount: 1
Unit: capacity units
```

The step was configured as:

``` text
- infinity < CPUUtilization <= 30
```

Therefore:

``` text
CPU < 30%
        |
        v
Remove 1 instance
```

The important protection is:

``` text
Minimum capacity = 2
```

Therefore the ASG cannot normally scale below 2 instances.

------------------------------------------------------------------------

# 18. Final Dynamic Scaling Configuration

The ASG now has two dynamic scaling policies.

## Scale Out

``` text
CloudWatch Alarm:
asg-scale-out-cpu-60

Condition:
CPU > 60%

Action:
Add 1 instance
```

## Scale In

``` text
CloudWatch Alarm:
asg-scale-in-cpu-30

Condition:
CPU < 30%

Action:
Remove 1 instance
```

------------------------------------------------------------------------

# 19. Final ASG Configuration

At this point the ASG should look like:

``` text
ASG Name: asg

Minimum capacity: 2
Desired capacity: 2
Maximum capacity: 4

Launch Template:
asg-tmp

Target Group:
test-trg

Scale-out:
CPU > 60% -> Add 1

Scale-in:
CPU < 30% -> Remove 1
```

------------------------------------------------------------------------

# 20. Test Scale-Out

To verify that the configuration actually works, CPU load was generated
on an ASG-managed EC2 instance.

Connect to one of the ASG instances.

Install the `stress` package:

``` bash
sudo apt update
sudo apt install stress -y
```

Then generate CPU load:

``` bash
stress --cpu 2 --timeout 300
```

Explanation:

``` text
--cpu 2
```

Runs 2 CPU stress workers.

``` text
--timeout 300
```

Runs the test for 300 seconds, which is 5 minutes.

The command displayed:

``` text
stress: info: dispatching hogs: 2 cpu, 0 io, 0 vm, 0 hdd
```

This confirmed that CPU load generation was running.

------------------------------------------------------------------------

# 21. Verify the Scale-Out Alarm

While the CPU load was running, go to:

``` text
CloudWatch
    -> Alarms
```

The scale-out alarm changed from:

``` text
OK
```

to:

``` text
In alarm
```

The scale-in alarm changed to:

``` text
OK
```

The final state during the CPU load test was:

``` text
asg-scale-out-cpu-60 = In alarm
asg-scale-in-cpu-30 = OK
```

This confirmed that the CPU utilization had crossed the scale-out
threshold.

------------------------------------------------------------------------

# 22. Verify ASG Scale-Out

Next, go to:

``` text
EC2
    -> Auto Scaling Groups
    -> asg
    -> Activity
```

The Auto Scaling activity showed that a new instance was launched.

The ASG changed from:

``` text
2 instances
```

to:

``` text
3 instances
```

The new instance was automatically registered with the Target Group.

------------------------------------------------------------------------

# 23. Verify the New Target

Go to:

``` text
EC2
    -> Target Groups
    -> test-trg
    -> Targets
```

The Target Group now showed:

``` text
Total targets = 3
```

All three targets became:

``` text
Healthy
```

The final scale-out result was:

``` text
Before:
2 healthy targets

CPU > 60%
     |
     v
CloudWatch Alarm
     |
     v
Scale-Out Policy
     |
     v
ASG launches 1 instance
     |
     v
After:
3 healthy targets
```

This proved that the scale-out configuration was working correctly.

------------------------------------------------------------------------

# 24. Test Scale-In

After the scale-out test, the CPU stress process was allowed to stop.

The command had a timeout of:

``` text
300 seconds
```

so it automatically stopped after approximately 5 minutes.

It could also be stopped manually with:

``` text
Ctrl + C
```

Once the CPU utilization dropped below 30%, the scale-in alarm could
trigger.

------------------------------------------------------------------------

# 25. Verify Scale-In

The expected flow was:

``` text
CPU < 30%
     |
     v
asg-scale-in-cpu-30
     |
     v
Scale-In Policy
     |
     v
Remove 1 instance
     |
     v
3 instances -> 2 instances
```

The ASG minimum capacity was 2, so the ASG retained at least two
instances.

The final expected state was:

``` text
Minimum capacity = 2
Desired capacity = 2
Instances = 2
```

------------------------------------------------------------------------

# 26. Important Difference: Alarm vs Scaling Action

A CloudWatch alarm entering the `ALARM` state does not by itself mean an
EC2 instance has already been launched or terminated.

The complete chain must be verified:

``` text
Metric
  |
  v
CloudWatch Alarm
  |
  v
Scaling Policy
  |
  v
Auto Scaling Activity
  |
  v
EC2 Instance Change
```

For scale-out:

``` text
CPU > 60%
  -> Alarm
  -> Scale-out policy
  -> Launch instance
  -> Instance becomes InService
  -> Target Group registers instance
  -> Health check passes
```

For scale-in:

``` text
CPU < 30%
  -> Alarm
  -> Scale-in policy
  -> Terminate instance
  -> Instance leaves ASG
  -> Target Group removes instance
```

------------------------------------------------------------------------

# 27. Why Old Instances Were Removed from the Target Group

Before Auto Scaling was configured, two older EC2 instances had already
been manually registered in the Target Group.

After configuring the ASG, those old instances should not remain as
permanent targets if the intention is for the ASG to control the
application fleet.

Otherwise the Target Group could contain:

``` text
Old manual instances
+
ASG-managed instances
```

This would make the load-balancing architecture harder to understand and
could cause traffic to be sent to instances that are not controlled by
the ASG.

Therefore, the old/manual targets were deregistered.

The final Target Group contained only ASG-managed instances.

------------------------------------------------------------------------

# 28. Why the Target Group Automatically Increased from 2 to 3

The ASG was integrated with:

``` text
test-trg
```

Therefore, when the ASG launched the third EC2 instance:

``` text
ASG
 |
 +--> launches EC2 instance
 |
 +--> registers instance with test-trg
```

The Target Group then performed its health check.

Once the instance became healthy:

``` text
test-trg
Total targets = 3
Healthy = 3
```

No manual registration was required.

------------------------------------------------------------------------

# 29. SSH Troubleshooting During the Test

During testing, direct SSH attempts to two public IP addresses returned:

``` text
kex_exchange_identification: read: Connection reset
Connection reset by x.x.x.x port 22
```

This type of error means the connection reached the remote endpoint but
was reset before SSH authentication completed.

For ASG instances, public IP addresses should not be assumed to remain
permanent because instances can be terminated and recreated.

When troubleshooting SSH, check:

1.  Current public IPv4 address of the ASG instance.
2.  Security Group inbound rule for TCP port 22.
3.  Network ACL rules.
4.  Route table and Internet Gateway.
5.  Whether the instance actually has a public IP.
6.  Whether the SSH service is running.
7.  Whether the launch template has the expected networking and security
    configuration.

A useful PowerShell test is:

``` powershell
Test-NetConnection <PUBLIC_IP> -Port 22
```

If SSM Session Manager is configured, it can also be used instead of
direct SSH.

------------------------------------------------------------------------

# 30. Final Validation Checklist

Use this checklist to verify the complete implementation.

## Auto Scaling Group

-   [x] ASG created
-   [x] ASG name = `asg`
-   [x] Launch template = `asg-tmp`
-   [x] Minimum capacity = 2
-   [x] Desired capacity = 2 initially
-   [x] Maximum capacity = 4
-   [x] Instances are healthy
-   [x] Instances are InService

## Target Group

-   [x] Target Group = `test-trg`
-   [x] Protocol = HTTP
-   [x] Port = 80
-   [x] Old/manual targets removed
-   [x] ASG instances registered
-   [x] Health checks passing
-   [x] New ASG instances automatically registered

## Load Balancer

-   [x] ALB = `test-alb`
-   [x] ALB forwards traffic to `test-trg`
-   [x] Target Group associated with ALB

## CloudWatch

-   [x] Scale-out alarm created
-   [x] Scale-out threshold = CPU \> 60%
-   [x] Scale-in alarm created
-   [x] Scale-in threshold = CPU \< 30%
-   [x] Period = 1 minute
-   [x] Evaluation = 1 out of 1 datapoints

## Scaling Policies

-   [x] Scale-out policy created
-   [x] Scale-out action = Add 1
-   [x] Scale-in policy created
-   [x] Scale-in action = Remove 1
-   [x] Instance warmup configured

## Testing

-   [x] CPU load generated
-   [x] Scale-out alarm entered ALARM
-   [x] ASG launched a third instance
-   [x] Third instance became healthy
-   [x] Target Group increased to 3 healthy targets
-   [x] CPU load stopped
-   [x] Scale-in behavior tested
-   [x] ASG returned toward its minimum/desired capacity

------------------------------------------------------------------------

# 31. Final Result

The Auto Scaling implementation successfully demonstrated both
directions of dynamic scaling.

### Scale-Out Test

``` text
Initial:
ASG = 2 instances

CPU load generated
        |
        v
CPU > 60%
        |
        v
CloudWatch Alarm = ALARM
        |
        v
Scale-Out Policy
        |
        v
Add 1 instance
        |
        v
ASG = 3 instances
        |
        v
Target Group = 3 healthy targets
```

### Scale-In Test

``` text
CPU load stops
        |
        v
CPU < 30%
        |
        v
CloudWatch Scale-In Alarm
        |
        v
Scale-In Policy
        |
        v
Remove 1 instance
        |
        v
ASG returns toward 2 instances
```

The final architecture provides automatic capacity management based on
CPU utilization while keeping the EC2 instances behind the Application
Load Balancer and Target Group.

------------------------------------------------------------------------

# 32. Key Concepts Learned

## Auto Scaling Group

An ASG automatically maintains the required number of EC2 instances and
adjusts capacity based on demand.

## Minimum Capacity

The minimum number of instances the ASG should maintain.

``` text
Minimum = 2
```

## Desired Capacity

The number of instances the ASG currently attempts to maintain.

## Maximum Capacity

The maximum number of instances the ASG is allowed to launch.

``` text
Maximum = 4
```

## Target Group

A Target Group contains the backend instances that receive traffic from
the ALB.

## CloudWatch Alarm

A CloudWatch alarm monitors a metric and changes state when a configured
threshold is reached.

## Scaling Policy

A scaling policy tells the ASG what action to take when a scaling
condition occurs.

``` text
High CPU -> Add instance
Low CPU  -> Remove instance
```

## Step Scaling

Step scaling changes capacity by a specified number of instances based
on CloudWatch alarm thresholds.

In this task:

``` text
CPU >= 60% -> +1
CPU <= 30% -> -1
```

## Instance Warmup

Warmup gives a newly launched EC2 instance time to initialize before
additional scaling decisions are made.

------------------------------------------------------------------------

# 33. Final Architecture Summary

``` text
                         Internet
                            |
                            v
                     +-------------+
                     |    ALB      |
                     |  test-alb   |
                     +------+------+
                            |
                            v
                     +-------------+
                     | Target Group|
                     |   test-trg  |
                     |   HTTP :80  |
                     +------+------+
                            |
               +------------+------------+
               |            |            |
               v            v            v
             EC2          EC2          EC2
           Healthy      Healthy      Healthy
               \            |            /
                \           |           /
                 +----------+----------+
                            |
                            v
                    +---------------+
                    |      ASG      |
                    |      asg      |
                    +---------------+
                     Min = 2
                     Desired = 2
                     Max = 4
                            |
             +--------------+--------------+
             |                             |
             v                             v
       CPU > 60%                       CPU < 30%
             |                             |
             v                             v
      Scale-out policy              Scale-in policy
             |                             |
             v                             v
          +1 EC2                         -1 EC2
```

**Task status: COMPLETED.**
