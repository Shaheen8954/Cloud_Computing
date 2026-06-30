When Do We Use a Snapshot? (5 Why's)
1. Why use a Snapshot?

To create a backup of an EBS volume.

Example:

Your database contains customer records.

Before upgrading the database, take a Snapshot.

If something goes wrong, restore the data.

2. Why not create another EBS volume manually?

Because a new EBS volume is empty.

A Snapshot preserves:

Files
Operating system
Databases
Logs
Applications
3. Why is this useful?

Suppose someone accidentally deletes important files.

Instead of rebuilding everything, restore the Snapshot.

4. Why do companies take Snapshots regularly?

For:

Daily backups
Disaster recovery
Compliance requirements
Migration
Rollback before updates

Many organizations automate snapshots using backup policies.

5. Why not use an AMI instead?

Because you may not want a new server—you only want your data back.

Example:

Current server:

EC2
↓

Database corrupted

Restore Snapshot:

Snapshot

↓

New EBS Volume

↓

Attach to Existing EC2

↓

Database restored

No need to create another EC2 instance.

Real-World Scenario

Imagine you have a production web server.

Situation 1: Need 20 more web servers

Use an AMI.

Configured EC2

↓

Create AMI

↓

Launch 20 EC2 Instances

Reason: You need identical servers.

Situation 2: Accidentally deleted important data

Use a Snapshot.

EBS Volume

↓

Snapshot

↓

Restore

↓

Recover Data

Reason: You need to recover storage, not create a new server.

Situation 3: Before upgrading software

Take a Snapshot.

If the upgrade fails, restore the previous state.

Situation 4: Auto Scaling adds servers during high traffic

Use an AMI.

Every new EC2 launched by the Auto Scaling Group is created from the same AMI.

Situation 5: Migrating a configured server to another account or region

Create an AMI (and copy it if needed) so you can launch the same configured server in the new environment.
