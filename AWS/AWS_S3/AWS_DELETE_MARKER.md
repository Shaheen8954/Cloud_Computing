# AWS S3 Delete Marker

## What is a Delete Marker?

A **Delete Marker** is a special placeholder object created when you delete an object in a **versioning-enabled S3 bucket** without specifying a version ID.

It is **not the actual object**. Instead, it tells Amazon S3 to treat the object as deleted while preserving all previous versions.

---

## Why Does S3 Create a Delete Marker?

Without Versioning:

```
photo.jpg
↓
Deleted Permanently
```

With Versioning:

```
photo.jpg

├── Delete Marker (Latest)
├── Version 3
├── Version 2
└── Version 1
```

The object appears deleted, but previous versions remain available.

---

## How It Works

Before deletion:

```
photo.jpg

Version 3 (Latest)
Version 2
Version 1
```

After deletion:

```
photo.jpg

Delete Marker
Version 3
Version 2
Version 1
```

The Delete Marker becomes the latest version.

---

## What Happens During a GET Request?

When a client requests:

```text
GET photo.jpg
```

Amazon S3 checks the latest version.

If the latest version is a Delete Marker, S3 returns:

```text
404 Not Found
```

Even though previous versions still exist.

---

## Is a Delete Marker a Version?

Yes.

A Delete Marker has:

- Version ID
- Creation timestamp
- Owner information

However, it contains **no object data**.

---

## Storage Cost

Delete Markers consume only a very small amount of metadata storage.

Example:

```text
movie.mp4 = 5 GB
Delete Marker = Metadata only
```

---

## Restoring an Object

Delete the Delete Marker.

Before:

```text
Delete Marker
Version 3
Version 2
Version 1
```

After:

```text
Version 3 (Latest)
Version 2
Version 1
```

The object becomes accessible again.

---

## Deleting a Specific Version

Deleting a specific Version ID permanently removes that version.

Example:

Before:

```text
Version 3
Version 2
Version 1
```

Delete Version 2:

```text
Version 3
Version 1
```

No Delete Marker is created.

---

## Lifecycle Rules

Lifecycle Rules can automatically:

- Delete expired Delete Markers
- Remove old object versions
- Transition previous versions to Glacier

---

## Replication

Delete Marker replication is configurable.

You can choose whether Delete Markers should also be replicated to the destination bucket.

---

## Advantages

- Protects against accidental deletion
- Preserves object history
- Enables easy recovery
- Works with Lifecycle and Replication

---

## Disadvantages

- Can confuse beginners
- Multiple Delete Markers may accumulate
- Requires cleanup through Lifecycle Rules or manual deletion

---

## Interview Questions

### What is a Delete Marker?

A Delete Marker is a placeholder created when an object is deleted in a versioning-enabled bucket without specifying a Version ID.

### Does it contain object data?

No. It stores only metadata.

### Can the object be restored?

Yes. Delete the Delete Marker to make the previous version current again.

### When is a Delete Marker created?

When deleting an object normally in a versioning-enabled bucket.

### Is a Delete Marker created when deleting a specific version?

No. Deleting a specific version permanently removes that version.

---

## Summary

| Feature | Description |
|---------|-------------|
| Purpose | Hide an object without deleting previous versions |
| Contains Data | ❌ No |
| Has Version ID | ✅ Yes |
| Created On | Normal delete in a versioning-enabled bucket |
| Recoverable | ✅ Yes |
| Storage Cost | Minimal metadata |

---

## Memory Trick

Think of a **Delete Marker** as a **"Closed" sign on a shop**.

The shop (object versions) is still there.

The sign simply tells customers it is unavailable.

Remove the sign, and the shop becomes accessible again.
