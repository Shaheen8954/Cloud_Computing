## create an s3 bucket with any secific name create 3-4 folder prefix upload any sample data in these folders now create one iam user that iam user should  only access only one folder he can only view, upload the data 
 he should not be able to delete any folder and rest of the folders 
upload data via only win scp, and aws cli



This task is about **implementing folder-level access control in Amazon S3** using **IAM Policies**. Although S3 doesn't have real folders (only prefixes), you can restrict access to a specific prefix.

---

# Task Overview

## Objective

Create:

* 1 S3 Bucket
* 4 Folder Prefixes
* Upload sample files
* Create 1 IAM User
* IAM User should:

  * ✅ Access only one folder
  * ✅ View files
  * ✅ Upload files
  * ❌ Cannot delete files
  * ❌ Cannot access other folders
* Upload data using:

  * AWS CLI
  * WinSCP

---

# Architecture

```
S3 Bucket
│
├── finance/
│      report1.pdf
│      report2.xlsx
│
├── hr/
│      emp.csv
│
├── dev/
│      app.zip
│
└── backup/
       db.sql

IAM User
      │
      ▼
Can Access Only

finance/

Cannot See

hr/
dev/
backup/
```

---

# Step 1 Create S3 Bucket

Example Bucket Name

```
shaheen-company-storage-001
```

Region

```
Mumbai (ap-south-1)
```

---

# Step 2 Create Folder Prefixes

Inside bucket create

```
finance/
hr/
dev/
backup/
```

---

# Step 3 Upload Sample Files

Example

```
finance/
    salary.xlsx
    invoice.pdf

hr/
    employees.csv

dev/
    app.zip

backup/
    database.sql
```

You can simply upload any text files.

---

# Step 4 Create IAM User

IAM

→ Users

→ Create User

Example

```
finance-user
```

Permission

Choose

```
Attach policies directly

(No policy)
```

We'll create a custom policy.

Enable

```
Programmatic Access
```

Download

```
Access Key
Secret Key
```

---

# Step 5 Create IAM Policy

Go to

IAM

Policies

Create Policy

JSON

Replace bucket name.

```json
{
  "Version": "2012-10-17",
  "Statement": [

    {
      "Sid": "AllowBucketListFinanceOnly",
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket"
      ],
      "Resource": "arn:aws:s3:::shaheen-company-storage-001",
      "Condition": {
        "StringLike": {
          "s3:prefix": [
            "finance/*",
            "finance"
          ]
        }
      }
    },

    {
      "Sid": "AllowFinanceFolder",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": [
        "arn:aws:s3:::shaheen-company-storage-001/finance/*"
      ]
    },

    {
      "Sid": "DenyDelete",
      "Effect": "Deny",
      "Action": [
        "s3:DeleteObject",
        "s3:DeleteObjectVersion"
      ],
      "Resource": [
        "arn:aws:s3:::shaheen-company-storage-001/*"
      ]
    }
  ]
}
```

Save Policy

Example

```
FinanceFolderAccess
```

Attach it to

```
finance-user
```

---

# Step 6 Test in AWS Console

Login as

```
finance-user
```

You should see

```
finance/
```

Open

```
finance/
```

Can

```
Download

Upload

View
```

Cannot

```
Delete

Open hr/

Open backup/

Open dev/
```

---

# Step 7 Configure AWS CLI

Install AWS CLI

Check

```bash
aws --version
```

Configure

```bash
aws configure
```

Enter

```
Access Key
Secret Key
Region

ap-south-1

Output

json
```

---

# Step 8 Upload Using AWS CLI

Upload

```bash
aws s3 cp sample.txt s3://shaheen-company-storage-001/finance/
```

---

Download

```bash
aws s3 cp s3://shaheen-company-storage-001/finance/sample.txt .
```

---

List

```bash
aws s3 ls s3://shaheen-company-storage-001/finance/
```

---

Try another folder

```bash
aws s3 ls s3://shaheen-company-storage-001/hr/
```

Output

```
Access Denied
```

---

Try Delete

```bash
aws s3 rm s3://shaheen-company-storage-001/finance/sample.txt
```

Output

```
Access Denied
```

---

# Step 9 Configure WinSCP

Install WinSCP.

Choose:

```
New Session

Protocol

Amazon S3
```

Enter:

```
Access Key
Secret Key
Region

ap-south-1
```

Click:

```
Login
```

---

# Step 10 Upload via WinSCP

Navigate to:

```
finance/
```

Drag and drop a file into the folder.

Expected:

* ✅ Upload succeeds.
* ❌ Delete option returns **Access Denied**.
* ❌ Trying to access `hr/`, `dev/`, or `backup/` returns **Access Denied**.

---

# Testing Matrix

| Test Case                 | Expected Result |
| ------------------------- | --------------- |
| List `finance/`           | ✅ Allowed       |
| Upload to `finance/`      | ✅ Allowed       |
| Download from `finance/`  | ✅ Allowed       |
| Delete file in `finance/` | ❌ Access Denied |
| List `hr/`                | ❌ Access Denied |
| Upload to `hr/`           | ❌ Access Denied |
| Delete from `hr/`         | ❌ Access Denied |
| Access `backup/`          | ❌ Access Denied |
| Upload using AWS CLI      | ✅ Allowed       |
| Upload using WinSCP       | ✅ Allowed       |

---

# AWS Services Used

| Service    | Purpose                           |
| ---------- | --------------------------------- |
| Amazon S3  | Object storage                    |
| IAM        | Identity and Access Management    |
| IAM Policy | Restrict folder-level permissions |
| AWS CLI    | Command-line uploads/downloads    |
| WinSCP     | GUI-based S3 file transfer        |

---

## Important Note

S3 **folders are virtual prefixes**, not actual directories. The restriction works by granting permissions only on the `finance/` prefix (for example, `finance/*`). The user cannot browse or access objects under other prefixes because the IAM policy only allows listing and object operations within the `finance` prefix and explicitly denies object deletion.
