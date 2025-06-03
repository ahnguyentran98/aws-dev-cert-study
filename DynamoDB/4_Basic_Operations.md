# DynamoDB Basic Operations

A concise summary of DynamoDB’s core operations, with practical context and real-world examples.

---

## 1. Writing Data

- **PutItem**
  - Adds a new item or fully replaces an existing item with the same primary key.
  - Consumes Write Capacity Units (WCUs).
- **UpdateItem**
  - Modifies attributes of an existing item or creates a new item if it doesn’t exist.
  - Supports *atomic counters* (safe numeric increments).
- **Conditional Writes**
  - Executes only if specified conditions are met (e.g., attribute does not exist, value matches).
  - Useful for handling concurrent updates safely.
- **Examples:** Add a new user, update order status, increment page views.

---

## 2. Reading Data

### a. GetItem

- Retrieves a single item by its primary key (partition key or partition + sort key).
- Default: *Eventually consistent*; can request *strongly consistent* (doubles RCU cost).
- Use *ProjectionExpression* to return specific attributes.
- **Example:** Fetch a user profile by user ID.

### b. Query

- Returns items matching a specific partition key (required, "=" operator).
- Optional sort key conditions: "=", "<", "<=", ">", ">=", "BETWEEN", "BEGINS_WITH".
- *FilterExpression* can further filter results (applies after fetching, only on non-key attributes).
- Returns up to 1 MB per call (use pagination for more).
- Works on tables, Global Secondary Indexes (GSI), and Local Secondary Indexes (LSI).
- **Example:** Get all orders for a customer within a date range.

### c. Scan

- Reads every item in the table, with optional filtering (inefficient for large tables).
- Returns up to 1 MB per call; use pagination for more data.
- Consumes significant RCUs.
- *Parallel scan* (multiple workers) can speed up, but increases RCU usage.
- **Example:** Search users by attribute when keys are unknown.

---

## 3. Deleting Data

- **DeleteItem**
  - Removes a single item by its primary key.
  - Supports *conditional delete* (e.g., only delete if version matches).
- **DeleteTable**
  - Removes the entire table and all items (much faster than deleting items one-by-one).
- **Examples:** Remove a user account, delete expired sessions.

---

## 4. Batch Operations

- **BatchWriteItem**
  - Up to 25 PutItem or DeleteItem operations per request (max 16 MB total, 400 KB per item).
  - Does not support UpdateItem.
  - Some operations may fail ("UnprocessedItems"); retry with backoff.
- **BatchGetItem**
  - Retrieve up to 100 items per request (max 16 MB).
  - Fetches in parallel; failed reads returned as "UnprocessedKeys" for retry.
- **Purpose:** Reduce latency and API calls for bulk operations, but handle failures.
- **Examples:** Data migration, mass price updates, loading user profiles in bulk.

---

## 5. PartiQL

- **SQL-compatible query language** for DynamoDB.
- Use familiar SQL syntax: `SELECT`, `INSERT`, `UPDATE`, `DELETE`.
- Available via AWS Console, NoSQL Workbench, APIs, CLI, SDKs.
- Supports batch operations.
- **Example:** `SELECT * FROM Users WHERE Age > 25`

---

## Summary Table

| Operation          | Description / Usage                           | API / Tool      |
|--------------------|-----------------------------------------------|-----------------|
| PutItem            | Add/replace single item                       | API             |
| UpdateItem         | Update/add attribute(s), atomic counters      | API             |
| Conditional Writes | Write only if condition met                   | API             |
| GetItem            | Read by primary key                           | API             |
| Query              | Read by partition key & (optionally) sort key | API             |
| Scan               | Read all items (inefficient, for full search) | API             |
| DeleteItem         | Remove item by key                            | API             |
| BatchWriteItem     | Bulk put/delete (up to 25 per request)        | API             |
| BatchGetItem       | Bulk get (up to 100 per request)              | API             |
| PartiQL            | SQL-like operations (select/insert/update)    | SQL/CLI/Console |

---

## Real-World Example

**Scenario:** E-commerce app

- Add/Update product: `PutItem` / `UpdateItem`
- Show product details: `GetItem`
- List products for a category: `Query` (partition key = "Category")
- Search products by price: `Scan` (if not indexed)
- Delete product: `DeleteItem`
- Bulk update prices: `BatchWriteItem`
- SQL-style analytics for product sales: `PartiQL`

---

**References:**

- Slides: DynamoDB – Writing Data, Reading Data, Query, Scan, Deleting Data, Batch Operations, PartiQL