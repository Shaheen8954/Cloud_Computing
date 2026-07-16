# Amazon S3 Interview & Study Questions

---

# Amazon S3 Basics

- What is Amazon S3?
- Why is it called Simple Storage Service?
- Why is Amazon S3 an Object Storage Service?
- What types of files can be stored in Amazon S3?
- Is there any limit on the amount of data stored in S3?
- What are the main use cases of Amazon S3?
- Why do companies prefer Amazon S3 over traditional storage?
- What are the advantages of Amazon S3?
- Can S3 be used as a database?
- Can we install an operating system on Amazon S3?

---

# Object Storage

- What is Object Storage?
- What is the difference between Object Storage, File Storage, and Block Storage?
- Why does S3 use Object Storage instead of Block Storage?
- Where is Object Storage commonly used?

---

# Bucket

- What is a Bucket?
- Why do we need a Bucket?
- Can an object exist without a Bucket?
- Why must Bucket names be globally unique?
- Can two AWS accounts create the same Bucket name?
- Can we rename a Bucket after creation?
- Can we move a Bucket to another Region?
- Can we delete a Bucket that contains objects?
- How many Buckets can an AWS account create?
- Can a Bucket store unlimited objects?

---

# Objects

- What is an Object?
- What are the components of an Object?
- What is the maximum size of an object?
- How do you upload large files greater than 5 GB?
- Can metadata be modified after uploading?
- What is the difference between an Object and a File?

---

# Object Key

- What is an Object Key?
- Is the Object Key the same as the file name?
- Can two objects have the same Object Key?
- Does Amazon S3 actually create folders?
- What are Prefixes?

---

# Metadata

- What is Metadata?
- What are the types of Metadata?
- What is System Metadata?
- What is User-defined Metadata?
- Why is Metadata useful?

---

# Versioning

- What is Versioning?
- Why is Versioning important?
- What happens if Versioning is disabled?
- Can deleted objects be recovered using Versioning?
- What is a Delete Marker?
- Can Versioning be disabled after enabling it?
- Does Versioning increase storage cost?

---

# Storage Classes

- What is a Storage Class?
- Why do we need different Storage Classes?
- Which Storage Class is the default?
- Which Storage Class is the cheapest?
- Which Storage Class provides the fastest access?
- Which Storage Class is best for backups?
- Which Storage Class is best for active website files?
- What is Intelligent-Tiering?
- When should we use Standard-IA?
- When should we use One Zone-IA?
- What is Glacier?
- What is Glacier Flexible Retrieval?
- What is Glacier Deep Archive?
- Can an object be moved from one Storage Class to another?
- Which feature automatically moves objects between Storage Classes?

---

# Lifecycle Rules

- What are Lifecycle Rules?
- Why do companies use Lifecycle Rules?
- Can Lifecycle Rules delete objects automatically?
- Can Lifecycle Rules change the Storage Class?
- What is Lifecycle Transition?
- Can Lifecycle Rules work without Versioning?

---

# Replication

- What is Replication?
- Why is Replication used?
- What is Same Region Replication (SRR)?
- What is Cross Region Replication (CRR)?
- Does Replication copy existing objects?
- Is Versioning required for Replication?
- Can replicated objects have different permissions?
- What are the benefits of CRR?

---

# Encryption

- What is Encryption?
- Why do companies encrypt data?
- What is Default Encryption?
- What is SSE-S3?
- What is SSE-KMS?
- What is SSE-C?
- Which encryption method is most commonly used?
- What is Bucket Key?
- Why is Bucket Key used?

---

# Permissions

- How is security managed in Amazon S3?
- What is the Permissions Tab?
- What is Block Public Access?
- Why is Block Public Access enabled by default?
- What happens if Block Public Access is disabled?

---

# Bucket Policy

- What is a Bucket Policy?
- Where is a Bucket Policy attached?
- What language is used to write Bucket Policies?
- Can a Bucket Policy allow public access?
- Can Bucket Policies deny access?

---

# IAM

- What is IAM?
- What is an IAM Policy?
- What is the difference between IAM Policy and Bucket Policy?
- Which one has higher priority?
- Can IAM users access S3 without permission?

---

# ACL

- What is an ACL?
- Why are ACLs considered legacy?
- What is the difference between ACL and Bucket Policy?
- Should ACLs be used in modern AWS environments?

---

# Object Ownership

- What is Object Ownership?
- What is Bucket Owner Enforced?
- Why is Bucket Owner Enforced recommended?

---

# CORS

- What is CORS?
- Why is CORS required?
- Give a real-world example of CORS.

---

# Object Lock

- What is Object Lock?
- Why is Object Lock used?
- What is Governance Mode?
- What is Compliance Mode?
- Can an object be deleted before the retention period ends?

---

# Static Website Hosting

- What is Static Website Hosting?
- Which types of websites can be hosted on S3?
- Can dynamic websites be hosted on S3?
- Which files are required for Static Website Hosting?
- What is an Index Document?
- What is an Error Document?

---

# Event Notifications

- What are Event Notifications?
- Which AWS services can S3 trigger?
- Give a real-world example of Event Notifications.

---

# Logging

- What is Server Access Logging?
- Why is Logging important?
- What is the difference between Server Access Logging and CloudTrail?

---

# Inventory

- What is S3 Inventory?
- Why is Inventory useful?

---

# Metrics & Analytics

- What are S3 Metrics?
- What is Storage Class Analysis?
- How do Metrics help administrators?

---

# Requester Pays

- What is Requester Pays?
- Who pays when Requester Pays is enabled?
- Give a use case for Requester Pays.

---

# Performance

- How does Amazon S3 achieve high durability?
- What does 99.999999999% durability mean?
- What is the difference between Durability and Availability?
- Does S3 automatically scale?
- Is there a limit on the number of requests per second?

---

# Cost Optimization

- How can companies reduce S3 storage costs?
- Which Storage Class should be selected for archived data?
- How does Intelligent-Tiering save money?
- How do Lifecycle Rules reduce costs?

---

# Real-World Scenario Questions

- If your manager accidentally deletes an important file, how would you recover it?
- A company wants to store 10 years of audit records at the lowest cost. Which Storage Class would you recommend?
- Your company wants a backup in another AWS Region. Which feature will you use?
- A website stores images in S3 but users cannot load them due to browser restrictions. Which feature should be configured?
- A company wants all uploaded files to be encrypted automatically. Which feature will you enable?
- A company wants files to become public only after approval. How would you configure S3?
- A company wants old files to move automatically to cheaper storage. Which feature would you use?
- How would you securely host a static website using Amazon S3?
- If multiple departments use the same bucket, how would you manage permissions?
- How would you monitor who accessed confidential files?

---

# Most Important Questions (Very High Probability)

1. Why is Amazon S3 called Object Storage?
2. Why do objects always need a bucket?
3. Why are bucket names globally unique?
4. What is the difference between Object Key and File Name?
5. What is Metadata?
6. What is Versioning, and why is it important?
7. Explain all S3 Storage Classes with use cases.
8. What is the difference between Standard-IA and Glacier?
9. What is the difference between SRR and CRR?
10. What is the difference between IAM Policy and Bucket Policy?
11. Why is Block Public Access enabled by default?
12. What is the difference between SSE-S3 and SSE-KMS?
13. What is Lifecycle, and why is it used?
14. What is CORS?
15. What is the difference between Durability and Availability?
16. Can S3 host a website? If yes, what type?
17. What is Object Lock, and where is it used?
18. What is the difference between Server Access Logging and CloudTrail?
19. How would you reduce the cost of storing old data in S3?
20. Explain a complete real-world architecture where multiple S3 features work together.

---

# Exam Preparation Checklist

## Amazon S3 Basics
- [ ] Amazon S3 Fundamentals
- [ ] Object Storage Concepts
- [ ] Buckets
- [ ] Objects
- [ ] Object Keys
- [ ] Metadata

## Data Protection
- [ ] Versioning
- [ ] Lifecycle Rules
- [ ] Replication
- [ ] Object Lock

## Storage Management
- [ ] Storage Classes
- [ ] Cost Optimization
- [ ] Performance
- [ ] Inventory

## Security
- [ ] Encryption
- [ ] IAM
- [ ] Bucket Policies
- [ ] ACLs
- [ ] Object Ownership
- [ ] Block Public Access
- [ ] CORS

## Monitoring
- [ ] Logging
- [ ] Metrics & Analytics
- [ ] Event Notifications

## Advanced Features
- [ ] Static Website Hosting
- [ ] Requester Pays

## Practice
- [ ] Real-world Scenarios
- [ ] Top 20 Interview Questions
- [ ] Hands-on AWS Console Practice
- [ ] CLI Commands
- [ ] Architecture Design
