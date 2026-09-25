# Telnet or one-way connectivity with specific port
#### Security Group — One-Way TCP 8080 Connectivity

## Objective

Create two EC2 instances in the same VPC but different subnets.

The requirement is:

- **Server A must be able to connect to Server B on TCP port 8080.**
- **Server B must NOT be able to connect to Server A on TCP port 8080.**
- Use **Security Groups** to control the traffic.
- Use **Nginx on Server B** to provide a service on port `8080`.
- Use **Telnet and Curl** from Server A to test connectivity.

---

# 1. Architecture

```text
                    TCP 8080
        ┌───────────────────────────────┐
        │                               │
        ▼                               │
┌───────────────┐                ┌───────────────┐
│   Server A    │                │   Server B    │
│               │                │               │
│ Private IP:   │                │ Private IP:   │
│ 10.0.1.10     │                │ 10.0.2.20     │
│               │                │               │
│ SG-Server-A   │                │ SG-Server-B   │
└───────────────┘                └───────────────┘
                                        │
                                        │
                                   Nginx :8080
```

Expected traffic behavior:

```text
Server A ────────────────> Server B
          TCP 8080
             ALLOWED


Server B ───────X────────> Server A
          TCP 8080
             BLOCKED
```

---

# 2. Prerequisites

Before starting, make sure:

- Two Ubuntu EC2 instances are running.
- Both instances are in the **same VPC**.
- The instances are in different subnets.
- Both instances have private IP addresses.
- You have SSH access to both servers.
- Each server has its own Security Group.

Example:

| Server | Private IP | Security Group |
|---|---|---|
| Server A | `10.0.1.10` | `SG-Server-A` |
| Server B | `10.0.2.20` | `SG-Server-B` |

> Replace these example IP addresses with the actual private IP addresses of your EC2 instances.

---

# 3. Security Group Configuration

## 3.1 Server A Security Group

Security Group:

```text
SG-Server-A
```

Inbound rules should contain the SSH rule required for administration.

Example:

| Type | Protocol | Port | Source |
|---|---|---:|---|
| SSH | TCP | 22 | Your IP/32 |

Do **not** add:

```text
TCP 8080
Source: SG-Server-B
```

This is important because Server B must not be able to initiate a TCP connection to Server A on port `8080`.

---

## 3.2 Server B Security Group

Security Group:

```text
SG-Server-B
```

Add the following inbound rule:

| Type | Protocol | Port | Source |
|---|---|---:|---|
| Custom TCP | TCP | 8080 | `SG-Server-A` |

Also keep the SSH rule required to administer Server B.

Example:

| Type | Protocol | Port | Source |
|---|---|---:|---|
| SSH | TCP | 22 | Your IP/32 |
| Custom TCP | TCP | 8080 | `SG-Server-A` |

### Why use `SG-Server-A` as the source?

Using the Security Group as the source means that resources associated with `SG-Server-A` can access Server B on TCP port `8080`.

This is preferable to opening the port to the entire internet:

```text
0.0.0.0/0
```

Do **not** use `0.0.0.0/0` for this lab unless there is a specific reason to make the service publicly accessible.

---

# 4. Install Nginx on Server B

SSH into **Server B**.

Update the package repository:

```bash
sudo apt update
```

Install Nginx:

```bash
sudo apt install nginx -y
```

Check the Nginx service:

```bash
sudo systemctl status nginx
```

You should see that Nginx is running.

---

# 5. Configure Nginx to Listen on Port 8080

By default, Nginx listens on port `80`.

Our task requires Nginx to listen on:

```text
TCP 8080
```

Open the default Nginx configuration:

```bash
sudo nano /etc/nginx/sites-available/default
```

Find:

```nginx
listen 80 default_server;
listen [::]:80 default_server;
```

Change it to:

```nginx
listen 8080 default_server;
listen [::]:8080 default_server;
```

The relevant part should look similar to:

```nginx
server {
    listen 8080 default_server;
    listen [::]:8080 default_server;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html;

    server_name _;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Save the file.

---

# 6. Test the Nginx Configuration

Before restarting Nginx, check the configuration:

```bash
sudo nginx -t
```

Expected output:

```text
syntax is ok
test is successful
```

If the configuration test succeeds, restart Nginx:

```bash
sudo systemctl restart nginx
```

Check the service again:

```bash
sudo systemctl status nginx
```

---

# 7. Verify Nginx Is Listening on Port 8080

On Server B, run:

```bash
sudo ss -lntp | grep 8080
```

You should see something similar to:

```text
LISTEN 0 511 0.0.0.0:8080
```

This confirms that a process is listening on TCP port `8080`.

You can also check:

```bash
sudo ss -lntp
```

Look for:

```text
:8080
```

---

# 8. Test Nginx Locally on Server B

Before testing from Server A, verify that Nginx works locally.

On Server B:

```bash
curl http://localhost:8080
```

You should receive the Nginx HTML response.

You can also use:

```bash
curl http://127.0.0.1:8080
```

If this works, Nginx is correctly listening on port `8080`.

---

# 9. Install Telnet on Server A

Telnet is being used as a **TCP connectivity test**.

SSH into **Server A**.

Run:

```bash
sudo apt update
```

Install Telnet:

```bash
sudo apt install telnet -y
```

Verify that Telnet is available:

```bash
telnet
```

---

# 10. Test TCP 8080 From Server A

From Server A, connect to Server B's private IP:

```bash
telnet 10.0.2.20 8080
```

Replace `10.0.2.20` with the actual private IP address of Server B.

If the connection is successful, you should see:

```text
Trying 10.0.2.20...
Connected to 10.0.2.20.
Escape character is '^]'.
```

This confirms:

```text
Server A
    |
    | TCP 8080
    ↓
Server B
    |
    └── Nginx :8080
```

TCP connectivity is working.

---

# 11. Test Using Curl

Telnet verifies TCP connectivity.

Curl can verify that the HTTP application is responding.

From Server A:

```bash
curl http://10.0.2.20:8080
```

You should receive the Nginx HTML response.

Example:

```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
</html>
```

This confirms that:

1. Server A can reach Server B.
2. TCP port `8080` is reachable.
3. Nginx is listening on port `8080`.
4. HTTP communication is working.

---

# 12. Test the Reverse Direction

Now SSH into **Server B**.

Try to connect to Server A on TCP port `8080`:

```bash
telnet 10.0.1.10 8080
```

Replace `10.0.1.10` with Server A's actual private IP.

The connection should fail.

For example:

```text
Trying 10.0.1.10...
telnet: Unable to connect to remote host: Connection timed out
```

This is expected because Server A's Security Group does not allow Server B to initiate TCP connections to port `8080`.

---

# 13. Why Does the Reverse Connection Fail?

The Security Groups are configured like this:

```text
SG-Server-A

Inbound:
--------------------------------
SSH TCP 22
Source: Your IP

No TCP 8080 from SG-Server-B
```

And:

```text
SG-Server-B

Inbound:
--------------------------------
SSH TCP 22
Source: Your IP

TCP 8080
Source: SG-Server-A
```

Therefore:

```text
Server A → Server B :8080
              |
              ✓ ALLOWED
```

But:

```text
Server B → Server A :8080
              |
              ✗ BLOCKED
```

---

# 14. Important Concept — Security Groups Are Stateful

AWS Security Groups are **stateful**.

When Server A initiates a connection:

```text
Server A
   |
   | Request
   | TCP 8080
   ↓
Server B
   |
   | Response
   ↓
Server A
```

You don't need to create a separate inbound rule on Server A just to allow the response to an already-established connection.

The Security Group automatically allows the return traffic for an allowed connection.

---

# 15. Why We Don't Need VPC Peering

If both EC2 instances are inside the same VPC, communication between their subnets is handled by the VPC's **local route**.

For example:

```text
VPC CIDR:
10.0.0.0/16
```

Subnet A:

```text
10.0.1.0/24
```

Subnet B:

```text
10.0.2.0/24
```

The VPC route table contains a local route similar to:

```text
Destination       Target
10.0.0.0/16      local
```

Therefore:

```text
Server A
10.0.1.10
    |
    | VPC local routing
    ↓
Server B
10.0.2.20
```

No VPC peering is required.

---

# 16. Do We Need an Internet Gateway?

For communication between two EC2 instances using their **private IP addresses inside the same VPC**, an Internet Gateway is not required for the communication itself.

The traffic remains inside the VPC.

```text
Server A
    |
    | Private IP
    ↓
VPC Local Route
    |
    ↓
Server B
```

---

# 17. Do We Need a NAT Gateway?

No.

A NAT Gateway is used when a **private subnet resource needs outbound internet access**, for example:

```text
Private EC2
    |
    ↓
NAT Gateway
    |
    ↓
Internet Gateway
    |
    ↓
Internet
```

It is not required for:

```text
Server A → Server B
```

when both servers are in the same VPC and communicate using private IP addresses.

---

# 18. Troubleshooting

If Server A cannot connect to Server B on port `8080`, check the following.

## Check 1 — Verify Server B's IP

On Server B:

```bash
hostname -I
```

Or:

```bash
ip addr
```

Make sure you're using the correct **private IP**.

---

## Check 2 — Check Nginx

On Server B:

```bash
sudo systemctl status nginx
```

If it isn't running:

```bash
sudo systemctl restart nginx
```

---

## Check 3 — Check Nginx Configuration

```bash
sudo nginx -t
```

---

## Check 4 — Check Port 8080

On Server B:

```bash
sudo ss -lntp | grep 8080
```

You should see Nginx listening on port `8080`.

---

## Check 5 — Check Server B Security Group

Make sure Server B has:

```text
Custom TCP
Port: 8080
Source: SG-Server-A
```

---

## Check 6 — Check Server A Security Group

Make sure you have **not** accidentally configured:

```text
Inbound:
TCP 8080
Source: SG-Server-B
```

because this would allow Server B to initiate TCP 8080 connections to Server A if the network path and destination service also permit it.

---

## Check 7 — Check Ubuntu Firewall

On Server B:

```bash
sudo ufw status
```

If UFW is enabled, make sure port `8080` is allowed:

```bash
sudo ufw allow 8080/tcp
```

Then:

```bash
sudo ufw status
```

> Only change the host firewall if it is actually enabled and blocking the traffic.

---

# 19. Final Security Group Configuration

## SG-Server-A

```text
Inbound Rules
------------------------------------------
SSH
Protocol: TCP
Port: 22
Source: Your IP/32

No TCP 8080 from Server B
```

## SG-Server-B

```text
Inbound Rules
------------------------------------------
SSH
Protocol: TCP
Port: 22
Source: Your IP/32

Custom TCP
Protocol: TCP
Port: 8080
Source: SG-Server-A
```

---

# 20. Final Testing Checklist

| Test | Expected Result |
|---|---|
| Nginx running on Server B | Pass |
| Nginx listening on 8080 | Pass |
| `curl localhost:8080` on Server B | Pass |
| Telnet Server A → Server B:8080 | **Success** |
| Curl Server A → Server B:8080 | **Success** |
| Telnet Server B → Server A:8080 | **Fail** |
| Server A inbound TCP 8080 from Server B | **Not configured** |
| Server B inbound TCP 8080 from Server A | **Configured** |

---

# 21. Final Architecture

```text
                         SAME VPC
                10.0.0.0/16
                      │
          ┌───────────┴───────────┐
          │                       │
          ▼                       ▼
   Subnet A                 Subnet B
   10.0.1.0/24              10.0.2.0/24
          │                       │
          │                       │
   ┌─────────────┐         ┌─────────────┐
   │  Server A   │         │  Server B   │
   │             │         │             │
   │ 10.0.1.10   │         │ 10.0.2.20   │
   │             │         │             │
   │   SG-A      │         │    SG-B     │
   └──────┬──────┘         └──────▲──────┘
          │                       │
          │                       │
          │    TCP 8080           │
          └───────────────────────┘
                    ALLOWED
                 A → B only


   B → A : TCP 8080
          X
       BLOCKED
```

# 22. Conclusion

The task is completed when:

```text
Server A → Server B :8080
       ✓ WORKS

Server B → Server A :8080
       ✗ DOES NOT WORK
```

The key configuration is:

```text
SG-Server-B
TCP 8080
Source: SG-Server-A
```

and **no corresponding TCP 8080 inbound rule from `SG-Server-B` exists on `SG-Server-A`**.

This demonstrates how AWS Security Groups can be used to control **directional initiation of TCP connections** between EC2 instances.
