
## **What is PartiQL in DynamoDB?**

* **PartiQL** (pronounced “particle”) is a **SQL-compatible query language** for DynamoDB.
* Lets you use familiar SQL-like syntax to interact with DynamoDB tables, indexes, and items.
* Works for SELECT, INSERT, UPDATE, DELETE operations.
* Can be used via the AWS Console, CLI, SDK, and NoSQL Workbench.

**Key Advantages:**

* Easier for developers familiar with SQL (especially those coming from relational DBs).
* Enables ad-hoc querying, analytics, and scripting on DynamoDB data.
* Supports both single-item and batch operations.

---

## **Basic PartiQL Query Examples**

### **1. SELECT (Read Data)**

**Get all items from a table:**

```sql
SELECT * FROM Orders
```

**Get items with a filter:**

```sql
SELECT * FROM Orders WHERE CustomerID = 'C123'
```

**Query with multiple conditions:**

```sql
SELECT * FROM Orders WHERE CustomerID = 'C123' AND OrderDate > '2024-06-01'
```

**Projection (fetch only certain attributes):**

```sql
SELECT OrderID, TotalAmount FROM Orders WHERE CustomerID = 'C123'
```

### **2. INSERT (Write Data)**

**Insert a new item:**

```sql
INSERT INTO Orders VALUE {'OrderID': 'O100', 'CustomerID': 'C123', 'TotalAmount': 250, 'OrderDate': '2024-06-03'}
```

### **3. UPDATE (Modify Data)**

**Update an item’s attribute:**

```sql
UPDATE Orders SET TotalAmount = 275 WHERE OrderID = 'O100' AND CustomerID = 'C123'
```

**Increment a numeric value:**

```sql
UPDATE Orders SET TotalAmount = TotalAmount + 10 WHERE OrderID = 'O100' AND CustomerID = 'C123'
```

### **4. DELETE (Remove Data)**

**Delete an item:**

```sql
DELETE FROM Orders WHERE OrderID = 'O100' AND CustomerID = 'C123'
```

---

## **Using PartiQL on Indexes**

You can also query GSIs/LSIs using PartiQL:

```sql
SELECT * FROM Orders.GSI_ProductID WHERE ProductID = 'P555'
```

(Assuming `GSI_ProductID` is a GSI with `ProductID` as a partition key.)

---

## **Batch Operations with PartiQL**

You can send multiple INSERT/UPDATE/DELETE queries in a single API call, which is useful for bulk processing.

---

## **PartiQL in the AWS Console**

* Go to DynamoDB Table → “Explore Table Items” → “PartiQL editor.”
* Type and execute your SQL-like queries directly.

---

## **Reference from Slides**

* "DynamoDB – PartiQL: SQL-compatible query language for DynamoDB. Can use SELECT, INSERT, UPDATE, DELETE. Available in AWS Console, NoSQL Workbench, CLI, SDKs. Useful for both interactive and batch queries."

---

## **Summary Table**

| Operation | PartiQL Example                                       |
| --------- | ----------------------------------------------------- |
| SELECT    | `SELECT * FROM Users WHERE Age > 25`                  |
| INSERT    | `INSERT INTO Users VALUE {'UserID': 'u1', 'Age': 30}` |
| UPDATE    | `UPDATE Users SET Age = 31 WHERE UserID = 'u1'`       |
| DELETE    | `DELETE FROM Users WHERE UserID = 'u1'`               |

---
