# 🌐 Amazon S3 CORS (Cross-Origin Resource Sharing)

## What is CORS?

CORS (Cross-Origin Resource Sharing) is a browser security mechanism that allows or blocks a website from accessing resources stored in another origin.

It acts as a permission slip that tells the browser whether a website can use resources from an S3 bucket.

> **Important:** CORS does **not** grant access to S3. It only controls browser behavior.

---

# What is an Origin?

An origin consists of:

- Protocol (HTTP/HTTPS)
- Domain
- Port

Examples:

- `https://mywebsite.com`
- `https://app.mywebsite.com`

These are different origins.

---

# Why Do We Need CORS?

Browsers enforce the **Same-Origin Policy**, which blocks web pages from accessing resources on different origins unless the server explicitly allows it.

CORS provides that explicit permission.

---

# Real-Life Analogy

- 🏠 House → S3 Bucket
- 👮 Security Guard → Browser
- 👤 Visitor → Website
- 📄 Permission Letter → CORS Configuration

Without the permission letter, the guard refuses entry.

---

# Browser Flow

```text
Website
    │
    ▼
Browser requests S3 object
    │
    ▼
S3 returns CORS headers
    │
    ▼
Browser checks permission
    │
    ├── Allowed → Resource available to JavaScript
    └── Not Allowed → Browser blocks access
```

---

# CORS vs Bucket Policy

| Bucket Policy | CORS |
|--------------|------|
| Controls who can access S3 | Controls whether browsers can use the response |
| Enforced by AWS | Enforced by browsers |
| Grants permissions | Does not grant permissions |

---

# Sample CORS Configuration

```json
[
  {
    "AllowedOrigins": [
      "https://mywebsite.com"
    ],
    "AllowedMethods": [
      "GET",
      "PUT"
    ],
    "AllowedHeaders": [
      "*"
    ],
    "ExposeHeaders": [
      "ETag"
    ],
    "MaxAgeSeconds": 3000
  }
]
```

---

# CORS Configuration Fields

## AllowedOrigins

Specifies which origins can access the bucket.

Example:

```json
"https://mywebsite.com"
```

Avoid using `"*"` in production.

---

## AllowedMethods

Defines the allowed HTTP methods.

Examples:

- GET
- PUT
- POST
- DELETE
- HEAD
- OPTIONS

---

## AllowedHeaders

Specifies which request headers the browser may send.

Common value:

```json
"*"
```

---

## ExposeHeaders

Allows JavaScript to access selected response headers.

Example:

- `ETag`

---

## MaxAgeSeconds

Defines how long browsers cache the CORS permissions.

---

# Preflight Request

Before certain requests (such as `PUT` or `DELETE`), browsers first send an `OPTIONS` request.

If the server allows it, the browser sends the actual request.

---

# Configure CORS in AWS Console

1. Open Amazon S3.
2. Select your bucket.
3. Open the **Permissions** tab.
4. Scroll to **Cross-origin Resource Sharing (CORS)**.
5. Click **Edit**.
6. Paste the JSON configuration.
7. Save the changes.

---

# Common Use Cases

- Static website hosting
- React applications
- Angular applications
- Vue applications
- Browser-based uploads
- Mobile web applications

---

# Common Mistakes

- Using `*` for `AllowedOrigins` in production.
- Forgetting to allow the `OPTIONS` method.
- Configuring the wrong origin.
- Assuming CORS grants permissions.

---

# Interview Questions

### What is CORS?

A browser security mechanism that controls whether cross-origin requests are allowed.

### Does CORS grant permissions?

No. IAM policies, Bucket Policies, and ACLs control permissions.

### Why do we need CORS?

To allow browser applications to access resources hosted on another origin securely.

### Does AWS CLI require CORS?

No. CORS is enforced only by browsers.

---

# Key Takeaways

- CORS = Browser security feature.
- Bucket Policy = AWS permission system.
- CORS is required only for browser-based cross-origin requests.
- AWS CLI and SDKs are not affected by CORS.
- Public buckets can still produce CORS errors if CORS is not configured.
