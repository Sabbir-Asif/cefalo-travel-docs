# Why UUIDs Are Needed in Modern Applications (A Technical View)

When designing scalable, distributed applications, choosing a **unique identifier (Primary Key)** for your database is a key consideration.  
Most legacy systems rely on **integer IDs with sequences or `SERIAL`**, which work well in monolithic, single-database contexts —  
but quickly become problematic in distributed, multi-database, or sharded architectures.

## Why We Need UUIDs in a Distributed System — A Technical View

### The Sequence Model’s Shortcomings

Using sequences (like `SERIAL`) assumes:

- There’s a **central sequence generator**, typically a single Postgres instance.
- IDs are **ordered and incremental**, reflecting insertion order.

This approach has drawbacks:

- **Scalability:** If you have **multiple nodes or shards**, you'll need a way to synchronize sequences to avoid collisions — adding latency and complexity.
- **Availability:** The sequence becomes a **central bottleneck and single point of failure.**
- **Conflict:** Merges from different databases will produce overlapping IDs.


### Why UUIDs Provide a Solution

UUIDs (Universally Unique IDs) resolve these issues by:

- **Decentralized Generation:** Each node can generate IDs independently — no need for a central service.
- **Statistically Unique:** The pool of IDs (2^122 possibilities) makes collisions nearly impossible.
- **Non-Sequential:** IDs aren’t tied to insertion order, reducing the risk of "hot-spotting" in indexes.


## Types of UUIDs — v1 vs v4 Explained

UUIDs come in different versions. The most commonly used ones are:


### UUID v1 — "Time-Based"

- **How:** Combines **current timestamp** and **MAC address**
- **Pros:**  
  - Naturally **ordered by time**
  - Allows you to approximate **creation order**
- **Cons:**  
  - Encodes the **MAC address**, which can be a **privacy/security risk**
  - Less suitable for distributed workloads if MAC address must be kept private
- **General use:**  
  - Legacy, backward-compatible, or when **inserting in order matters**


### UUID v4 — "Random"

- **How:** Purely **generated from a cryptographically strong random number generator**
- **Pros:**  
  - Purely **decentralized**, no MAC or timestamp
  - Uniqueness depends on large pool (2^122 possibilities), extremely tiny collision probability
  - Better for distributed systems
- **Cons:**  
  - Unordered; IDs appear completely random
- **General use:**  
  - The preferred way for generating IDs in modern, distributed applications
  - Ideal for scaling, sharding, multi-database setups


## Why We Need Extensions in Postgres to Handle UUIDs

Postgres has a native `uuid` datatype, which efficiently stores and indexes 128-bit IDs.  
But generating a UUID directly in Postgres requires a **function**, and this functionality is not available by default.

So we need **extensions** to:

- Provide a way to generate UUIDs directly in the database
- Integrate nicely with `uuid` column definitions
- Provide a fast, cryptographically strong, and well-distributed IDs


## The Main Postgres Extensions for Generating UUIDs

Here are two popular ones:

### 1. uuid-ossp

```sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
````

Provides:

* `uuid_generate_v1()` — Time + MAC
* `uuid_generate_v4()` — Purely random (using OS randomness)

**Pros:**
Provides both v1 and v4.

**Cons:**

* Requires `libossp`.
* Less convenient and not cryptographically strong for v4.

---

### 2. pgcrypto (Recommended)

```sql
CREATE EXTENSION IF NOT EXISTS "pgcrypto";
```

Provides:

* `gen_random_uuid()` — Purely cryptographically strong, v4-style

**Pros:**

* Faster, more lightweight
* Better randomness (using OS-native sources)

---

## Why pgcrypto’s `gen_random_uuid()` Is Better

|              | uuid-ossp                      | pgcrypto                      |
| ------------ | ------------------------------ | ----------------------------- |
| Type         | v1 or v4                       | v4                            |
| Dependences  | External lib                   | Built into Postgres           |
| Security     | Medium                         | Strong, OS-native             |
| Ease of use  | Requires separate `uuid-ossp`  | `gen_random_uuid()`           |
| Distribution | May need central coordination  | Purely decentralized          |
| Use cases    | Legacy, backward compatibility | Modern, scalable, distributed |


## Final Thoughts

Using **pgcrypto’s `gen_random_uuid()`** lets you:

* **Design scalable and distributed applications without bottlenecks**
* **Reduce operational complexity**
* **Improve resiliency and independence of your database nodes**


## Final Example — Purely Database-side Primary Key Generation:

```sql
-- Enable pgcrypto
CREATE EXTENSION IF NOT EXISTS "pgcrypto";

-- Declare your table:
CREATE TABLE orders (
  order_id UUID PRIMARY KEY DEFAULT gen_random_uuid(), 
  customer_id UUID NOT NULL, 
  amount NUMERIC(10, 2) NOT NULL
);
```

Now you have:

* A **self-generating primary key**
* **Completely decentralized IDs**
* A solution well-suited for multi-database, sharded, or distributed applications

---

## Summary — Why pgcrypto’s `gen_random_uuid()` is Best

Using `pgcrypto`’s `gen_random_uuid()` lets you:

* Push UUID generation into the database layer.
* Provide unique IDs without needing a central service.
* Handle large scale and distributed workloads gracefully.

