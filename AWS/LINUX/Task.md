## create a 1 user by any name in the second server then login the second server from the first server, 
after login create a one file in /home/ubuntu directory.

This task is about **passwordless SSH authentication** between two Ubuntu EC2 instances.

## Scenario

* **Server 1:** Source server (from where you will SSH)
* **Server 2:** Destination server (where you will create a user)
* New user: `devuser` (you can choose any name)

---

# Step 1: Login to Server 2

```bash
ssh -i key.pem ubuntu@<Server2-Public-IP>
```

---

# Step 2: Create a New User

```bash
sudo adduser devuser
```

Example output:

```
New password:
Retype new password:
Full Name:
Room Number:
...
```

Press **Enter** for optional fields.

Verify:

```bash
id devuser
```

Output:

```
uid=1001(devuser) gid=1001(devuser)
```

---

# Step 3: Give SSH Access to the User

Switch to the new user.

```bash
sudo su - devuser
```

Create the SSH directory.

```bash
mkdir ~/.ssh
chmod 700 ~/.ssh
```

---

# Step 4: Login to Server 1

```bash
ssh -i key.pem ubuntu@<Server1-Public-IP>
```

---

# Step 5: Generate an SSH Key (if you don't already have one)

```bash
ssh-keygen
```

Press Enter for all prompts.

This creates:

```
~/.ssh/id_rsa
~/.ssh/id_rsa.pub
```

Check:

```bash
ls ~/.ssh
```

---

# Step 6: Copy the Public Key to Server 2

Display the public key:

```bash
cat ~/.ssh/id_rsa.pub
```

Copy the entire output.

---

## On Server 2 (logged in as `devuser`)

Edit the authorized keys file:

```bash
nano ~/.ssh/authorized_keys
```

Paste the copied public key.

Save and exit.

Set permissions:

```bash
chmod 600 ~/.ssh/authorized_keys
```

---

# Step 7: Verify SSH Configuration

On Server 2:

```bash
sudo nano /etc/ssh/sshd_config
```

Ensure these settings are present:

```
PubkeyAuthentication yes
PasswordAuthentication yes
```

Restart SSH:

```bash
sudo systemctl restart ssh
```

---

# Step 8: Login from Server 1 to Server 2

From Server 1:

```bash
ssh devuser@<Private-IP-of-Server2>
```

If successful:

```
devuser@server2:~$
```

---

# Step 9: Create a File in `/home/ubuntu`

You are logged in as `devuser`, but the task requires creating a file in `/home/ubuntu`.

Use:

```bash
sudo touch /home/ubuntu/testfile.txt
```

Or add content:

```bash
echo "Created from Server1 using SSH" | sudo tee /home/ubuntu/testfile.txt
```

---

# Step 10: Verify

```bash
ls -l /home/ubuntu
```

Example:

```
-rw-r--r-- 1 root root 31 Jul 2  testfile.txt
```

Or view its contents:

```bash
cat /home/ubuntu/testfile.txt
```

Output:

```
Created from Server1 using SSH
```

---

## Command Summary

```bash
# On Server 2
sudo adduser devuser
sudo su - devuser
mkdir ~/.ssh
chmod 700 ~/.ssh
nano ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys

# On Server 1
ssh-keygen
cat ~/.ssh/id_rsa.pub

# SSH to Server 2
ssh devuser@<Server2-Private-IP>

# Create file in Ubuntu home
sudo touch /home/ubuntu/testfile.txt
```

### Note

If `sudo touch /home/ubuntu/testfile.txt` returns **Permission denied**, add the user to the sudo group on Server 2 before connecting:

```bash
sudo usermod -aG sudo devuser
```

Then log out and log back in so the new group membership takes effect.
