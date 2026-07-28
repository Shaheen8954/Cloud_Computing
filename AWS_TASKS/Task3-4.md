# Deploy a Python Flask Application with MySQL on AWS EC2

## Project Overview

## Architecture

## Prerequisites


- Backup Script
- Cron Job
- systemd Service
- Environment Variable


# Step 1 - Launch EC2

## Launch Ubuntu EC2

AMI:
Ubuntu Server 24.04 LTS

Instance Type:
t3.micro

Security Group

| Port | Purpose |
|------|---------|
|22|SSH|
|5000|Flask|

---

## Connect to EC2

```bash
ssh -i flask-key.pem ubuntu@<PUBLIC-IP>
```

---

## Update Ubuntu

```bash
sudo apt update
sudo apt upgrade -y
```

Explanation

- Updates package repository
- Installs latest security patches

Expected Output

```
Reading package lists...
Done
```


# Step 2 - Install Python Runtime

## Install Python

```bash
sudo apt install python3 python3-pip python3-venv -y
```

Why?

- Installs Python
- Installs pip
- Installs virtual environment

Verify

```bash
python3 --version
pip3 --version
```


# Step 3 - Install MySQL

```bash
sudo apt install mysql-server -y
```

Enable Service

```bash
sudo systemctl enable mysql
```

Start Service

```bash
sudo systemctl start mysql
```

Check Status

```bash
sudo systemctl status mysql
```

Secure MySQL

```bash
sudo mysql_secure_installation
```

Prompt

Remove anonymous users?

Answer

```
y
```

Reason

Removes anonymous MySQL users.

Prompt

Disallow root login remotely?

Answer

```
y
```

Reason

Prevents remote root login.

Prompt

Remove test database?

Answer

```
y
```

Prompt

Reload privilege tables?

Answer

```
y
```


# Step 4 - Configure Database

Login

```bash
sudo mysql
```

Create Database

```sql
CREATE DATABASE userdb;
```

Verify

```sql
SHOW DATABASES;
```

Create User

```sql
CREATE USER 'appuser'@'localhost'
IDENTIFIED BY '<YOUR_DATABASE_PASSWORD>';
```

Grant Permissions

```sql
GRANT ALL PRIVILEGES
ON userdb.*
TO 'appuser'@'localhost';
```

Reload

```sql
FLUSH PRIVILEGES;
```

Use Database

```sql
USE userdb;
```

Create Table

```sql
CREATE TABLE users(
id INT AUTO_INCREMENT PRIMARY KEY,
username VARCHAR(100),
email VARCHAR(150),
age INT,
gender VARCHAR(20)
);
```

Verify

```sql
SHOW TABLES;

DESCRIBE users;
```


# Step 5 - Flask Project

Create Directory

```bash
mkdir ~/flaskapp
cd ~/flaskapp
```

Create Virtual Environment

```bash
python3 -m venv venv
```

Activate

```bash
source venv/bin/activate
```

Upgrade pip

```bash
pip install --upgrade pip
```

Install Packages

```bash
pip install flask mysql-connector-python
```

Verify

```bash
pip list
```


Then it will include

Complete app.py
Complete index.html
Complete users.html

(with syntax highlighting)

Then


Run Flask

```bash
python app.py
```

Expected Output

```
Running on http://0.0.0.0:5000
```

Browser

```
http://<PUBLIC-IP>:5000
```

Verify MySQL

```bash
sudo mysql
```

```sql
USE userdb;

SELECT * FROM users;
```


## Step 9 – Verify Data in MySQL

After submitting the form from the Flask application, log in to MySQL and verify that the record has been stored successfully.

Login to MySQL:

```bash
sudo mysql
```

Select the database:

```sql
USE userdb;
```

Verify the stored records:

```sql
SELECT * FROM users;
```

Expected Output:

```text
+----+----------+-------------------+------+--------+
| id | username | email             | age  | gender |
+----+----------+-------------------+------+--------+
|  1 | Shaheen  | shaheen@test.com  | 24   | Female |
+----+----------+-------------------+------+--------+
```

### Verification Screenshot

The screenshot below confirms that:

- ✅ The Flask application successfully connected to MySQL.
- ✅ Data entered from the web form was inserted into the `users` table.
- ✅ The record can be retrieved directly from MySQL.

![MySQL Data Verification](images/mysql-data-verification.png)



# MySQL Backup and Restore on Ubuntu (EC2)

# Objective

This guide explains how to:
- Create a backup directory
- Write a MySQL backup script
- Execute the backup manually
- Verify the backup
- Restore the backup into another database
- Verify that the restore was successful

This guide is written for beginners and can be followed step by step.

---

# Prerequisites

- Ubuntu EC2 instance
- MySQL Server installed
- Database: `userdb`
- MySQL User: `appuser`
- Backup directory: `/backup`

---

# Step 1 – Create a Backup Directory

```bash
sudo mkdir /backup
```

Verify:

```bash
ls /
```

You should see:

```text
backup
```

---

# Step 2 – Create the Backup Script

Open the file:

```bash
sudo nano /backup/mysql_backup.sh
```

Paste:

```bash
#!/bin/bash

DATE=$(date +%F-%H-%M-%S)

mysqldump \
--no-tablespaces \
-u appuser \
-p'Password@123' \
userdb > /backup/userdb-$DATE.sql
```

Save the file.

---

# Step 3 – Make the Script Executable

```bash
sudo chmod +x /backup/mysql_backup.sh
```

Verify:

```bash
ls -l /backup/mysql_backup.sh
```

Expected:

```text
-rwxr-xr-x
```

---

# Step 4 – Run the Backup

Run:

```bash
sudo bash /backup/mysql_backup.sh
```

> Note: Running the script with `bash` worked reliably in this environment.

You may see:

```text
mysqldump: [Warning] Using a password on the command line interface can be insecure.
```

This is only a warning.

---

# Step 5 – Verify the Backup File

```bash
ls -lh /backup
```

Example:

```text
-rw-r--r-- userdb-2026-07-27-13-14-33.sql
```

Check the contents:

```bash
head -20 /backup/userdb-2026-07-27-13-14-33.sql
```

You should see SQL statements such as:

```sql
-- MySQL dump
CREATE TABLE `users`
```

---

# Step 6 – Restore the Backup

## Create a Restore Database

```bash
sudo mysql
```

```sql
CREATE DATABASE restoredb;
EXIT;
```

> If you receive:
>
> `ERROR 1007 (HY000): Can't create database 'restoredb'; database exists`
>
> the database already exists and you can continue.

Restore:

```bash
sudo mysql restoredb < /backup/userdb-2026-07-27-13-14-33.sql
```

If the command returns without errors, the restore completed successfully.

---

# Step 7 – Verify the Restore

Login:

```bash
sudo mysql
```

Run:

```sql
USE restoredb;
SHOW TABLES;
SELECT * FROM users;
```

Expected:

- The `users` table exists.
- The records match those in the original `userdb` database.

---

# Verification Screenshot

The screenshot below shows a successful verification where:

- `restoredb` was selected.
- `SHOW TABLES;` displayed the `users` table.
- `SELECT * FROM users;` returned the restored data.

![Backup Verification](image49.png)

---

# Interview Tip

**Question:** How do you verify that your backup is valid?

**Answer:**

"I don't assume a backup is valid just because the SQL file was created. I restore it into a separate database, verify that all tables are recreated, and confirm that the restored data matches the original database."

---

# Best Practice

Avoid storing database passwords directly in shell scripts in production. Instead, use:
- MySQL option files (`~/.my.cnf`)
- Environment variables
- Secret management solutions such as AWS Secrets Manager
